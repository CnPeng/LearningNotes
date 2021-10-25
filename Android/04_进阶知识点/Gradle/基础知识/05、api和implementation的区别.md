## 1、api 和 implementation 
假设有 libA , libA 中依赖了 libB，我们的项目（module）依赖了 libA。

* 如果 libA 在依赖 libB 时使用了 `api "libB"`, 那么我们的 module 也可以直接访问 `libB` 中的方法。
* 如果 libA 在依赖 libB 时使用了 `implemetation "libB"`, 那么我们的 module 无法访问 `libB` 中的方法。

也就是说：**使用 `api` 添加依赖时会形成依赖穿透。** 

## 2、如何选择

* 如果使用了依赖穿透，当 libB 中的内容发生变化时，Gradle 编译时会同时编译 libA  及 module。
* 如果没有使用依赖穿透，那么 libB 中的内容发生变化时，Gradle 编译时只会编译 libA

综上，使用依赖穿透时，会影响编译速度。为了提示编译速度，我们最好不用依赖穿透。

##3、疑问内容：
* 使用 api 的优点有哪些？
* 如果 libA 和 module 都依赖了同一个 libB 时，该怎么编写依赖呢？是在 libA 中使用 api 还是分别写 implementation?
* 使用 api 和 implemetation 时都项目的体积有什么影响？
