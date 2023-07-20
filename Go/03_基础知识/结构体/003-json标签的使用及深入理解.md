# 1. 003-json标签的使用及深入理解

[原文：golang struct json tag的使用及深入理解](https://blog.csdn.net/xz_studying/article/details/106012535)

## 1.1. sturct json tag的使用

### 1.1.1. tag格式说明

struct json tag 主要在 struct 与 json 数据转换的过程 (Marshal/Unmarshal) 中使用。

json 的 tag 格式如下：

```go
Key type  `json:"name,opt1,opt2,opts..."`
```

说明：

* 变量必须是可导出的（ Key 首字母必须大写），否则会被忽略处理。
* 没有 json tag 或者 tag 中 name 省略（但不能少了逗号—`,`），默认使用字段名。
* name 要注意命名的有效性。
* opt1、opt2 等项为可选项，必须使用有限的几个限定的 opt 的一个或组合，如 "`omitempty`"、"`string`"，使用非限定的 opt 会发生错误。

### 1.1.2. 具体使用格式说明

我们先介绍下源码文档中提供的几种使用方式：

因 Marshal 与 Unmarshal 是相反的过程，两者规则是一致的，以下介绍中仅说明了 Marshal 时的处理。

* 不指定tag

```go
// 不指定tag，默认使用变量名称。转换为 json 时，key 为 Filed。
Field int // “Filed”:0
```

* 直接忽略

```go
//转换时不处理。注意：必须为"-"，不能带有opts
Field int json:"-"
```

* 指定key名

```go
// 转换为json时，key为myName
Field int json:"myName" // “myName”:0
```
* "omitempty"零值忽略

```go
// 转换为json时，值为零值则忽略，否则 key 为 myName
Field int json:",omitempty"
```

* 指定key且零值忽略

```go
// 转换为 json 时，值为零值则忽略，否则 key 为 myName
Field int json:"myName,omitempty"
```

* 指定key为 `-`

```go
// 此项与忽略的区别在于多了个”,“。
Field int json:"-," // “-”:0
```

* `“string” opt`

以上提到的用法都是常见的，这个比较特殊。

`"string"` 仅适用于字符串、浮点、整数或布尔类型，表示的意思是：**将字段的值转换为字符串**；解析时，则是将字符串解析为指定的类型。主要用于与 javascript 通信时数据的转换。

注意：

仅且仅有 `"string"`，**没有 int、number 之类的 opt**。即：带 `"string"` opt的字段，**编码时仅能将字符串、浮点、整数或布尔类型转换为 string 类型**，反之则不然；**解码时可以将 string 转换为其他类型**，反之不然。因为 `"string"` 有限制。

```go
Int64String int64 json:",string" // “Int64String”:“0”
```

`“string”` opt 的使用可以在 Marshal/Unmarshal 时自动进行数据类型的转换，减少了手动数据转换的麻烦，但是一定要注意使用的范围，对不满足的类型使用，是会报错的。

猜下对 string 使用 `"string"` opt的结果会是如何呢？

```go
Int64String string json:",string"
```

我们在了解源码后解答。


## 1.2. 源码角度的设计处理过程

一切的使用方式肯定在设计时就已限定，我们现在看看源码中的处理过程。

在看实现的过程中，可以思考下使用的方式对不对，还有要注意的地方吗？

对某些地方非常好的实现思路，我们也可以借鉴下，对以后的编程学习大有裨益。

>此处为了简洁，具体调用过程略过不讲，直接查看核心代码部分，有兴趣的话，可以查看下完整过程。

### 1.2.1. typeFields

在 `typeFields` 中详细的对上面提到的各种用法的 tag 做了处理，处理后的数据存入 `fileds` ，最后再进行编码。

```go
// typeFields returns a list of fields that JSON should recognize for the given type.
// The algorithm is breadth-first search over the set of structs to include - the top struct
// and then any reachable anonymous structs.
func typeFields(t reflect.Type) structFields {
    // Anonymous fields to explore at the current level and the next.
    current := []field{}
    next := []field{{typ: t}}

    // Count of queued names for current level and the next.
    var count, nextCount map[reflect.Type]int

    // Types already visited at an earlier level.
    visited := map[reflect.Type]bool{}

    // Fields found.
    var fields []field

    // Buffer to run HTMLEscape on field names.
    var nameEscBuf bytes.Buffer

    for len(next) > 0 {
        current, next = next, current[:0]
        count, nextCount = nextCount, map[reflect.Type]int{}

        for _, f := range current {
            if visited[f.typ] {//已处理的过类型跳过
                continue
            }
            visited[f.typ] = true

            // Scan f.typ for fields to include.
            for i := 0; i < f.typ.NumField(); i++ {
                sf := f.typ.Field(i)
                isUnexported := sf.PkgPath != ""
                if sf.Anonymous {//内嵌类型的处理
                    t := sf.Type
                    if t.Kind() == reflect.Ptr {
                        t = t.Elem()
                    }
                    if isUnexported && t.Kind() != reflect.Struct {
                        // Ignore embedded fields of unexported non-struct types.
                        continue//非struct结构的不能导出的key直接跳过
                    }
                    // Do not ignore embedded fields of unexported struct types
                    // since they may have exported fields.
                } else if isUnexported {
                    // Ignore unexported non-embedded fields.
                    continue//不能导出的key直接跳过
                }
                tag := sf.Tag.Get("json")
                if tag == "-" {
                    continue//tag为"-"直接跳过
                }
                name, opts := parseTag(tag)
                if !isValidTag(name) {
                    name = ""//包含特殊字符的无效name
                }
                index := make([]int, len(f.index)+1)
                copy(index, f.index)
                index[len(f.index)] = i

                ft := sf.Type
                if ft.Name() == "" && ft.Kind() == reflect.Ptr {
                    // Follow pointer.
                    ft = ft.Elem()
                }

                // Only strings, floats, integers, and booleans can be quoted.
                quoted := false
                if opts.Contains("string") {//此处为"string" opt的特殊处理，支持的类型如下：
                    switch ft.Kind() {
                    case reflect.Bool,
                        reflect.Int, reflect.Int8, reflect.Int16, reflect.Int32, reflect.Int64,
                        reflect.Uint, reflect.Uint8, reflect.Uint16, reflect.Uint32, reflect.Uint64, reflect.Uintptr,
                        reflect.Float32, reflect.Float64,
                        reflect.String:
                        quoted = true
                    }
                }

                // Record found field and index sequence.
                if name != "" || !sf.Anonymous || ft.Kind() != reflect.Struct {
                    tagged := name != ""
                    if name == "" {
                        name = sf.Name//未指定或者指定name无效的使用原field的name
                    }
                    field := field{
                        name:      name,
                        tag:       tagged,
                        index:     index,
                        typ:       ft,
                        omitEmpty: opts.Contains("omitempty"),//omitempty确认
                        quoted:    quoted,//是否支持"string" opt
                    }
                    field.nameBytes = []byte(field.name)
                    field.equalFold = foldFunc(field.nameBytes)

                    // Build nameEscHTML and nameNonEsc ahead of time.
                    //两种格式的构建
                    nameEscBuf.Reset()
                    nameEscBuf.WriteString(`"`)
                    HTMLEscape(&nameEscBuf, field.nameBytes)
                    nameEscBuf.WriteString(`":`)
                    field.nameEscHTML = nameEscBuf.String()
                    field.nameNonEsc = `"` + field.name + `":`

                    fields = append(fields, field)//存入fields
                    if count[f.typ] > 1 {
                        // If there were multiple instances, add a second,
                        // so that the annihilation code will see a duplicate.
                        // It only cares about the distinction between 1 or 2,
                        // so don't bother generating any more copies.
                        fields = append(fields, fields[len(fields)-1])
                    }
                    continue
                }

                // Record new anonymous struct to explore in next round.
                nextCount[ft]++
                if nextCount[ft] == 1 {
                    next = append(next, field{name: ft.Name(), index: index, typ: ft})
                }
            }
        }
    }

    ...

    for i := range fields {
        f := &fields[i]
        f.encoder = typeEncoder(typeByIndex(t, f.index))//设置fields的encoder
    }
    nameIndex := make(map[string]int, len(fields))
    for i, field := range fields {
        nameIndex[field.name] = i
    }
    return structFields{fields, nameIndex}
}
```

### 1.2.2. encode


```go
func newStructEncoder(t reflect.Type) encoderFunc {
    se := structEncoder{fields: cachedTypeFields(t)}
    return se.encode
}

func (se structEncoder) encode(e *encodeState, v reflect.Value, opts encOpts) {
    next := byte('{')
FieldLoop:
    for i := range se.fields.list {
        f := &se.fields.list[i]

        // Find the nested struct field by following f.index.
        fv := v
        for _, i := range f.index {
            if fv.Kind() == reflect.Ptr {
                if fv.IsNil() {
                    continue FieldLoop
                }
                fv = fv.Elem()
            }
            fv = fv.Field(i)
        }

        if f.omitEmpty && isEmptyValue(fv) {//"omitempty"的忽略处理，需要值为零值
            continue
        }
        e.WriteByte(next)
        next = ','
        if opts.escapeHTML {
            e.WriteString(f.nameEscHTML)
        } else {
            e.WriteString(f.nameNonEsc)
        }
        opts.quoted = f.quoted
        f.encoder(e, fv, opts)//根据具体类型的编码处理
    }
    if next == '{' {
        e.WriteString("{}")
    } else {
        e.WriteByte('}')
    }
}
```

以下以 int 类型 `intEncoder` 为例:

```go
func intEncoder(e *encodeState, v reflect.Value, opts encOpts) {
    b := strconv.AppendInt(e.scratch[:0], v.Int(), 10)
    if opts.quoted {//带有"string" opt添加引号
        e.WriteByte('"')
    }
    e.Write(b)
    if opts.quoted {
        e.WriteByte('"')
    }
}
```

对于数字类型，如果带有**“string”**则在写入正式值前后添加引号。

对于字符串类型，如果带有**“string”**，原 string 值再编码时会添加引号，再对结果添加引号，则格式异常，因此需要先对原值进行编码。

```go
func stringEncoder(e *encodeState, v reflect.Value, opts encOpts) {
    if v.Type() == numberType {
        numStr := v.String()
        // In Go1.5 the empty string encodes to "0", while this is not a valid number literal
        // we keep compatibility so check validity after this.
        if numStr == "" {
            numStr = "0" // Number's zero-val
        }
        if !isValidNumber(numStr) {
            e.error(fmt.Errorf("json: invalid number literal %q", numStr))
        }
        e.WriteString(numStr)
        return
    }
    if opts.quoted {
        sb, err := Marshal(v.String())//注意此处处理
        if err != nil {
            e.error(err)
        }
        e.string(string(sb), opts.escapeHTML)
    } else {
        e.string(v.String(), opts.escapeHTML)
    }
}

func (e *encodeState) string(s string, escapeHTML bool) {
    e.WriteByte('"')//添加引号
    start := 0
    for i := 0; i < len(s); {
        if b := s[i]; b < utf8.RuneSelf {//字符串中存在特殊的字符时的转义处理
            if htmlSafeSet[b] || (!escapeHTML && safeSet[b]) {
                i++
                continue
            }
            if start < i {
                e.WriteString(s[start:i])
            }
            e.WriteByte('\\')
            switch b {
            case '\\', '"':
                e.WriteByte(b)
            case '\n':
                e.WriteByte('n')
            case '\r':
                e.WriteByte('r')
            case '\t':
                e.WriteByte('t')
            default:
                // This encodes bytes < 0x20 except for \t, \n and \r.
                // If escapeHTML is set, it also escapes <, >, and &
                // because they can lead to security holes when
                // user-controlled strings are rendered into JSON
                // and served to some browsers.
                e.WriteString(`u00`)
                e.WriteByte(hex[b>>4])
                e.WriteByte(hex[b&0xF])
            }
            i++
            start = i
            continue
        }
        c, size := utf8.DecodeRuneInString(s[i:])
        if c == utf8.RuneError && size == 1 {
            if start < i {
                e.WriteString(s[start:i])
            }
            e.WriteString(`\ufffd`)
            i += size
            start = i
            continue
        }
        // U+2028 is LINE SEPARATOR.
        // U+2029 is PARAGRAPH SEPARATOR.
        // They are both technically valid characters in JSON strings,
        // but don't work in JSONP, which has to be evaluated as JavaScript,
        // and can lead to security holes there. It is valid JSON to
        // escape them, so we do so unconditionally.
        // See http://timelessrepo.com/json-isnt-a-javascript-subset for discussion.
        if c == '\u2028' || c == '\u2029' {
            if start < i {
                e.WriteString(s[start:i])
            }
            e.WriteString(`\u202`)
            e.WriteByte(hex[c&0xF])
            i += size
            start = i
            continue
        }
        i += size
    }
    if start < len(s) {
        e.WriteString(s[start:])
    }
    e.WriteByte('"')
}
```

在了解完源码的处理过程后，我们对之前提到的问题做个解答。对 string 类型的字段添加 `"string"` opt，得到的是：

```go
Int64String string json:",string" // “Int64String”: "“1234"”
```

## 1.3. 总结

本文主要从源码的角度说明 struct json tag的为什么这么使用，以及使用时需要注意的地方。最后重复下重要的几点：

* 字段必须可导出，tag 才有意义
* 忽略必须使用 `json:"-"` ，不得带有 opts，否则 key 将会变成 `-`
* `"string"` opt仅适用于字符串、浮点、整数及布尔类型，意思是可以将这些类型的数据 Marshal 为 string 类型，或者将 string 类型的数据 Unmarshal 为这些类型。
请勿滥用，尤其是对已经是 string 类型的数据使用。
