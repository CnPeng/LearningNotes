# 1. 1-js中判断字符串为空的方式

原文：[js 判断字符串为空的方法](https://blog.csdn.net/weixin_41646716/article/details/89375896)

## 1.1. 参考一：

```js
function isEmpty(obj){
    if(typeof obj == "undefined" || obj == null || obj == ""){
        return true;
    }else{
        return false;
    }
}
```

## 1.2. 参考二：

```js
 if (variable1 !== null || variable1 !== undefined || variable1 !== '') { 
  var variable2 = variable1; 
  }
```

上面的意思是说如果variable1不是一个空对象，或者未定义，或者不等于空字符串，那么声明一个variable2变量，将variable1赋给variable2。也就是说如果variable1存在那么就将variable1的值赋给variable2，若不存在则为空字符串。如下面的简写代码。

简写代码：

代码如下:

```js
  var variable2 = variable1 || '';
```

**以下是不正确的方法：**

代码如下:

```js
  var exp = null; 
  if (exp == null) 
  { 
  alert("is null"); 
  }
```
exp 为 undefined 时，也会得到与 null 相同的结果，虽然 null 和 undefined 不一样。注意：要同时判断 null 和 undefined 时可使用本法。
代码如下:

```js
  var exp = null; 
  if (!exp) 
  { 
  alert("is null"); 
  }
```

如果 exp 为 undefined，或数字零，或 false，也会得到与 null 相同的结果，虽然 null 和二者不一样。注意：要同时判断 null、undefined、数字零、false 时可使用本法

代码如下:

```js
  var exp = null; 
  if (typeof exp == "null") 
  { 
  alert("is null"); 
  }
```

为了向下兼容，exp 为 null 时，typeof null 总返回 object，所以不能这样判断。
代码如下:

```js
  var exp = null; 
  if (isNull(exp)) 
  { 
  alert("is null"); 
  }
```

判断字符串是否为空

s 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ fnrtv]。 很多情况下，都是用length来直接判断字符串是否为空，如下：
代码如下:

```js
  var strings = ''; 
  if (string.length == 0) 
  { 
  alert('不能为空'); 
  }
```

但如果用户输入的是空格，制表符，换页符呢?这样的话，也是不为空的，但是这样的数据就不是我们想要的吧。
其实可以用正则表达式来把这些“空”的符号去掉来判断的
代码如下:

```js
  var strings = ' '; 
  if (strings.replace(/(^s*)|(s*$)/g, "").length ==0) 
  { 
  alert('不能为空'); 
  }
```

s 小写的s是，匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ fnrtv]。
判断为空怎么简写，就为大家介绍到这里，希望上面的方法能对大家有所帮助。

另： `str.replace(/(^\s*)|(\s*$)/g, "")) != ""`

## 1.3. 参考三：

`\s` 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 `[ \f\n\r\t\v]`。 很多情况下，都是用length来直接判断字符串是否为空，如下：

```js
var strings = '';
if (string.length == 0)
{
    alert('不能为空');
}
```

但如果用户输入的是空格，制表符，换页符呢？这样的话，也是不为空的，但是这样的数据就不是我们想要的吧。

其实可以用正则表达式来把这些“空”的符号去掉来判断的

```js
var strings = ' ';
if (strings.replace(/(^\s*)|(\s*$)/g, "").length ==0)
{
    alert('不能为空');
}
```

`\s` 小写的s是，匹配任何空白字符，包括空格、制表符、换页符等等。等价于` [ \f\n\r\t\v]`。

 

## 1.4. 参考四：
 

判断字符串是否为空

```js
var strings = '';
 
if (string.length == 0)
 
{
 
alert('不能为空');
 
}
```

判断字符串是否为“空”字符即用户输入了空格

```js
var strings = ' ';
 
if (strings.replace(/(^s*)|(s*$)/g, "").length ==0)
 
{
 
alert('不能为空');
 
}
```

判断输入字符串是否为空或者全部都是空格

```js
function isNull( str ){
 
if ( str == "" ) return true;
 
var regu = "^[ ]+$";
 
var re = new RegExp(regu);
 
return re.test(str);
 
}
```

如果有null时上面代码就无法正常判断了，下面代码是判断为null的情况

```js
var exp = null;
 
if (exp == null)
 
{
 
alert("is null");
}
```

exp 为 undefined 时，也会得到与 null 相同的结果，虽然 null 和 undefined 不一样。

注意：要同时判断 null 和 undefined 时可使用本法。 代码如下

```js
var exp = null;
 
if (!exp)
 
{
 
alert("is null");
 
}
```

如果 exp 为 undefined，或数字零，或 false，也会得到与 null 相同的结果，虽然 null 和二者不一样。注意：要同时判断 null、undefined、数字零、false 时可使用本法。代码如下

```js
var exp = null;
 
if (typeof exp == "null")
 
{
 
alert("is null");
 
}
```
 
为了向下兼容，exp 为 null 时，typeof null 总返回 object，所以不能这样判断

```js
<script type="text/javascript">
 
function testuser(){
 
var i= document.getElementByIdx_x("aa");
 
if (i.value=="null")
 
{
 
alert("请登录后再发表留言!")
 
return false;
 
}
 
else
 
{
 
alert(i.value)
 
return true;
 
}
 
}
 
</script>
```

## 1.5. 参考五：

### 1.5.1. 方法一： 使用trim()

```js
/* 使用String.trim()函数，来判断字符串是否全为空*/
 function kongge1(test) {
    let str = test.trim();
  if (str.length == 0) {
      console.log('字符串全是空格');
  } else {
      console.log('输入的字符串为:' + test);
  }
}
```

如果 trim() 不存在，可以在所有代码前执行下面代码

```js
/* 给String原型链对象添加方法trim */
if (!String.prototype.trim) {
  String.prototype.trim = function () {
    return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '');
  };
}
```

例如：

```js
/* 使用String.trim()函数，来判断字符串是否全为空*/  
  function kongge1(test) {  
      /* 给String原型链对象添加方法trim */  
      if (!String.prototype.trim) {  
         String.prototype.trim = function () {  
          return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '');  
      };  
   }  
 
let str = test.trim();
if (str.length == 0) {
    console.log('字符串全是空格');
} else {
    console.log('输入的字符串为:' + test);
}
}
```

### 1.5.2. 方法二： 使用正则表达式

```js
/* 使用正则表达式来判断字符串是否全为空 */
function kongge2(test) {
    if(test.match(/^\s+$/)){
      console.log("all space or \\n");            
    }
    if(test.match(/^[ ]+$/)){
      console.log("all space")
    }
    if(test.match(/^[ ]*$/)){
      console.log("all space or empty")
    }
    if(test.match(/^\s*$/)){
      console.log("all space or \\n or empty")
    } else {
        console.log('输入的字符串为:' + test);
    }
}
```

案例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>js判断字符串是否全为空(使用trim函数/正则表达式)</title>
</head>
<body>
姓名：<input type="text" name="userName" id='userName' onblur="check(value)" value="">
    <script type="text/javascript">
    /*
        typeof 检测给定变量的数据类型。
        两种写法： typeof(value);  typeof value;
        可能返回的字符串：
        "undefined" --- 如果这个值未定义。
        "boolean" --- 如果这个值是布尔值。 
        "string" --- 如果这个值是字符串。
        "number" --- 如果这个值是数值。
        "object" --- 如果这个值是对象或者null;
        "function" --- 如果这个值是函数。
     */
    let str = 'str';
    alert(typeof abc);//undefined;
    alert(typeof undefined);//undefined
    alert(typeof check(str));//undefined
    alert(typeof '');//string
    alert(typeof str);//string
    alert(typeof 98);//number
    alert(typeof {});//object
    alert(typeof null);//object
 
        function check(value) { 
            if ('string' == typeof value) {
                kongge1(value);
                kongge2(value);
            } else {
                console.log(typeof value);
                console.log('请输入字符串');
            }
        }
 
        /* 使用String.trim()函数，来判断字符串是否全为空*/
        function kongge1(test) {
            let str = test.trim();
            if (str.length == 0) {
                console.log('字符串全是空格');
            } else {
                console.log('输入的字符串为:' + test);
            }
        }
 
        /* 使用正则表达式来判断字符串是否全为空 */
        function kongge2(test) {
            if(test.match(/^\s+$/)){
              console.log("all space or \\n");            
            }
            if(test.match(/^[ ]+$/)){
              console.log("all space")
            }
            if(test.match(/^[ ]*$/)){
              console.log("all space or empty")
            }
            if(test.match(/^\s*$/)){
              console.log("all space or \\n or empty")
            } else {
                console.log('输入的字符串为:' + test);
            }
        }
    </script>
</body>
</html>

```