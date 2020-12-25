# 3 数组中的 map 和 flatMap 的区别


## 3.1 源码中的定义

`flatMap` 有降维拆解的作用

```swift
  /// Returns an array containing the concatenated results of calling the
    /// given transformation with each element of this sequence.
    ///
    /// Use this method to receive a single-level collection when your
    /// transformation produces a sequence or collection for each element.
    ///
    /// In this example, note the difference in the result of using `map` and
    /// `flatMap` with a transformation that returns an array.
    ///
    ///     let numbers = [1, 2, 3, 4]
    ///
    ///     let mapped = numbers.map { Array(repeating: $0, count: $0) }
    ///     // [[1], [2, 2], [3, 3, 3], [4, 4, 4, 4]]
    ///
    ///     let flatMapped = numbers.flatMap { Array(repeating: $0, count: $0) }
    ///     // [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
    ///
    /// In fact, `s.flatMap(transform)`  is equivalent to
    /// `Array(s.map(transform).joined())`.
    ///
    /// - Parameter transform: A closure that accepts an element of this
    ///   sequence as its argument and returns a sequence or collection.
    /// - Returns: The resulting flattened array.
    ///
    /// - Complexity: O(*m* + *n*), where *n* is the length of this sequence
    ///   and *m* is the length of the result.
    @inlinable public func flatMap<SegmentOfResult>(_ transform: (Element) throws -> SegmentOfResult) rethrows -> [SegmentOfResult.Element] where SegmentOfResult : Sequence
```

## 3.2 其他介绍文章

原文[《swift中map和flatMap的使用》](https://www.jianshu.com/p/e2dd04fafa70)

### 3.2.1 map的使用

```swift
let numbers = [1,2,3,4]
let result = numbers.map { $0 + 2 }
//输出：3，4，5，6
print(result)    
```

map 方法接受的是一个闭包参数，然后它会遍历整个 numbers 数组，并对数组中每一个元素执行闭包中定义的操作。相当于对数组中的所有元素做了一个映射。

这个例子就是对 numbers 数组的所有元素进行了加2。

再看一个例子：

```swift
let array1 = ["a","b","c","d"]
let array2 = array1.map { "NO.\($0)" }
print(array2)    //输出：NO.a，NO.b，NO.c，NO.d
```

### 3.2.2 flatMap 的使用

map 可以理解为对一个集合类型的所有元素做了一个映射操作，那么flatMap呢？

让我们来看一下下面的一个例子：

```swift
result = numbers.flatMap{ $0 + 2 }      //输出：3,4,5,6
```

我们对同样的数组使用了 flatMap 进行处理，得到了相同的结果。那么这两个到底有什么区别呢？

```swift
let array1 = [[1,2,3],[4,5,6]]
var arr = array1.map{ $0.map{ $0 + 1 } }
//输出：[[2,3,4],[5,6,7]]

var arr2 = array1.flatMap{ $0.map{ $0 + 1 } }
//输出：[2,3,4,5,6,7]
```

看到这两个数组的输出就看出差别来了，对于二维数组，map 和 flatMap 的结果就不同了。

flatMap 会遍历数组的元素，并对这些元素执行闭包中定义的操作。 但唯一和 map 不同的是，它对最终的结果进行了所谓的 “降维” 操作。 本来原始数组是一个二维的， 但经过 flatMap 之后，它变成一维的了。

flatMap 的第二个作用：

```swift
let optionalArray: [String?] = ["AA","BB",nil,"CC"]
//输出：[Optional("AA"), Optional("BB"), nil, Optional("CC")]
print(optionalArray)   


var flatMapArray = optionalArray.flatMap{ $0 }
//输出：["AA", "BB", "CC"]
print(flatMapArray)
```

在 flatMap 的返回结果中， 成功的将原数组中的 nil 值过滤掉了。 再仔细观察，你会发现更多。 使用 flatMap 调用之后， 数组中的所有元素都被解包了。也就是说原始数组的类型是 `[String?]`, 而 flatMap 调用后变成了 `[String]`。 这也是 flatMap 和 map 的一个重大区别。
