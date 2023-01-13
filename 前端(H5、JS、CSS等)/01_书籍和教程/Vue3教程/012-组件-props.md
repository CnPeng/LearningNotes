# 1. 012-组件的props

> 2022-11-21 周一 
> Props 详细内容可参考 [《深入组件-Props》](https://cn.vuejs.org/guide/components/props.html)

`props` 是一种特别的 attributes（属性），组件需要显式声明它所接受的 props，这样 Vue 才能知道外部传入的哪些是 props，哪些是透传 attribute (关于透传 attribute，我们会在[专门的章节](https://cn.vuejs.org/guide/components/attrs.html)中讨论)。

**在父组件中向子组件传递数据时，有多种方式，props 只是其中一种。**

>CnPeng-Props 使用总结。
>
> Props 用于父组件向子组件传递数据，使用方式如下：
>
> * 在子组件的 `export default` 中通过 `props` 数组或对象声明自定义属性。如：`props: ['title']`
> * 子组件内部使用该自定义属性。如：`<h4>{{ title }}</h4>`
> * 父组件使用子组件并通过子组件的自定义属性传值。如：
>     * 传入静态值：`<BlogPost title="My journey with Vue" />`
>     * 传入动态值：`<BlogPost :title="titleFromSrv"/>`


## 1.1. Props 声明

如果我们正在构建一个博客，则可能需要一个表示博客文章的组件。我们希望所有的博客文章分享相同的视觉布局，但有不同的内容。要实现这样的效果自然必须向组件中传递数据，例如每篇文章标题和内容，这就会使用到 `props` 。

### 1.1.1. 使用字符串数组声明 props

```vue
<!-- BlogPost.vue -->
<script>
export default {
  // 自定义属性
  props: ['title'],
  created() {
    // props 会暴露到 `this` 上
    console.log(this.foo)
  }
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

当一个值被传递给 `prop` 时，它将成为该组件实例上的一个属性。该属性的值可以像其他组件属性一样，在模板和组件的 `this` 上下文中访问。

一个组件可以有任意多的 `props` ，默认情况下，所有 prop 都接受任意类型的值。

当一个 prop 被注册后，在使用组件时，我们可以按如下方式为 prop 传递值：

```template
<!--此处的 title 为 BlogPost 中通过 props 自定的属性名称。这里直接为 title 赋值为静态字符串-->
<BlogPost title="My journey with Vue" />

<!--此处的 :title 为动态值绑定。titleFromSrv 为 data 节点中的属性，其值取决于服务端返回的数据-->
<BlogPost :title="titleFromSrv"/>
```

在实际应用中，我们可能在父组件中会有如下的一个博客文章数组：

```js
export default {
  // ...
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue' },
        { id: 2, title: 'Blogging with Vue' },
        { id: 3, title: 'Why Vue is so fun' }
      ]
    }
  }
}
```

这种情况下，我们可以使用 `v-for` 来渲染它们：

```template
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
 />
```

上述示例中，我们使用 `v-bind` 来传递动态 prop 值的。当事先不知道要渲染的确切内容时，这中方式特别有用。

### 1.1.2. 使用对象声明 props

除了使用字符串数组来声明 `prop` 外，还可以使用对象的形式：

```js
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

对于以对象形式声明中的每个属性， **key 是 prop 的名称，而值则是该 prop 预期类型的构造函数**。比如，如果要求一个 prop 的值是 `number` 类型，则可使用 `Number` 构造函数作为其声明的值。

对象形式的 props 声明不仅可以一定程度上作为组件的文档，而且如果其他开发者在使用你的组件时传递了错误的类型，也会在浏览器控制台中抛出警告。[点击查看有关 prop 校验的更多细节。](https://cn.vuejs.org/guide/components/props.html#prop-validation)

TypeScript 用户请参考：[为组件 Props 标注类型](https://cn.vuejs.org/guide/typescript/options-api.html#typing-component-props)

## 1.2. 传递 prop 的细节

### 1.2.1. Prop 名字格式

如果一个 prop 的名字很长，应使用 `camelCase` （驼峰） 形式，因为它们是合法的 JavaScript 标识符，可以直接在模板的表达式中使用，也可以避免在作为属性 key 名时必须加上引号。

```js
export default {
  props: {
    greetingMessage: String
  }
}
```

```template
<span>{{ greetingMessage }}</span>
```

虽然理论上也可以在向子组件传递 props 时使用 `camelCase` 形式 (**使用 DOM 模板时例外**)，但实际上为了和 HTML attribute 对齐，我们通常会将其写为 `kebab-case` 形式：

```template
<MyComponent greeting-message="hello" />
```

对于组件名我们推荐使用 `PascalCase` ，因为这提高了模板的可读性，能帮助我们区分 Vue 组件和原生 HTML 元素。然而对于传递 props 来说，使用 `camelCase` 并没有太多优势，因此我们推荐更贴近 HTML 的书写风格。

补充：命名格式说明

命名格式 | 示例 | 解析
---|---|---
`camelCase` | greetingMessage | 驼峰形式，开头单词首字母缩写，后面的单词首字母大写。
`PascalCase` | GreetingMessage | 首字母大写。
`kebab-case` | greeting-message | 中横线连接，全部单词小写。

### 1.2.2. 静态 vs. 动态 Prop

至此，你已经见过了很多像这样的静态值形式的 props：

```template
<BlogPost title="My journey with Vue" />
```

相应地，还有使用 `v-bind` 或缩写 `:` 来进行动态绑定的 props：

```template
<!-- 根据一个变量的值动态传入 -->
<BlogPost :title="post.title" />

<!-- 根据一个更复杂表达式的值动态传入 -->
<BlogPost :title="post.title + ' by ' + post.author.name" />
```

### 1.2.3. 传递不同的值类型

在上述的两个例子中，我们只传入了字符串值，但实际上任何类型的值都可以作为 props 的值被传递。

* Number

```template
<!-- 虽然 `42` 是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :likes="42" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :likes="post.likes" />
```

* Boolean

```template
<!-- 仅写上 prop 但不传值，会隐式转换为 `true` -->
<BlogPost is-published />

<!-- 虽然 `false` 是静态的值，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :is-published="false" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :is-published="post.isPublished" />
```

* Array

```template
<!-- 虽然这个数组是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :comment-ids="[234, 266, 273]" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :comment-ids="post.commentIds" />
```

* Object

```template
<!-- 虽然这个对象字面量是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
 />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :author="post.author" />
```

### 1.2.4. 使用一个对象绑定多个 prop

如果你想要将一个对象的所有属性都当作 props 传入，你可以使用没有参数的 `v-bind`，即只使用 `v-bind` 而非 `:prop-name`。例如，这里有一个 post 对象：

```js
export default {
  data() {
    return {
      post: {
        id: 1,
        title: 'My Journey with Vue'
      }
    }
  }
}
```

以及下面的模板：

```template
<BlogPost v-bind="post" />
```

而这实际上等价于：

```template
<BlogPost :id="post.id" :title="post.title" />
```

## 1.3. 单向数据流

### 1.3.1. 单向数据流概念

所有的 props 都遵循着**单向绑定**原则，**props 因父组件的更新而变化，自然地将新的状态向下流往子组件，而不会逆向传递**。这避免了子组件意外修改父组件的状态的情况，不然应用的数据流将很容易变得混乱而难以理解。

另外，**每次父组件更新后，所有的子组件中的 props 都会被更新到最新值**，这意味着**你不应该在子组件中去更改一个 prop**。若你这么做了，Vue 会在控制台上向你抛出警告：

```js
export default {
  props: ['foo'],
  created() {
    // ❌ 警告！prop 是只读的！
    this.foo = 'bar'
  }
}
```

### 1.3.2. 需要更改 prop 的场景

导致你想要更改一个 prop 的需求通常来源于以下两种场景：

* **prop 被用于传入初始值；而子组件想在之后将其作为一个局部数据属性**。在这种情况下，最好是新定义一个局部数据属性，从 props 上获取初始值即可：

```js
export default {
  props: ['initialCounter'],
  data() {
    return {
      // 计数器只是将 this.initialCounter 作为初始值
      // 像下面这样做就使 prop 和后续更新无关了
      counter: this.initialCounter
    }
  }
}
```

* **需要对传入的 prop 值做进一步的转换**。在这种情况中，最好是基于该 prop 值定义一个计算属性：

```js
export default {
  props: ['size'],
  computed: {
    // 该 prop 变更时计算属性也会自动更新
    normalizedSize() {
      return this.size.trim().toLowerCase()
    }
  }
}
```

### 1.3.3. 更改对象 / 数组类型的 props

当对象或数组作为 props 被传入时，虽然子组件无法更改 props 绑定，但仍然**可以更改对象或数组内部的值**。这是因为 JavaScript 的**对象和数组是按引用传递**，而对 Vue 来说，禁止这样的改动虽然可能，但有很大的性能损耗，比较得不偿失。

这种更改的主要**缺陷是它允许了子组件以某种不明显的方式影响父组件的状态，可能会使数据流在将来变得更难以理解**。在最佳实践中，**你应该尽可能避免这样的更改，除非父子组件在设计上本来就需要紧密耦合。在大多数场景下，子组件应该[抛出一个事件](https://cn.vuejs.org/guide/components/events.html)来通知父组件做出改变**。

## 1.4. Prop 校验

Vue 组件可以更细致地声明对传入的 props 的校验要求。

比如我们上面已经看到过的类型声明，如果传入的值不满足类型要求，Vue 会在浏览器控制台中抛出警告来提醒使用者。这在开发给其他开发者使用的组件时非常有用。

### 1.4.1. 校验示例

要声明对 props 的校验，你可以向 props 选项**提供一个带有 props 校验选项的对象**，例如：

```js
export default {
  props: {
    // 基础类型检查
    //（给出 `null` 和 `undefined` 值则会跳过任何类型检查）
    propA: Number,
    // 多种可能的类型
    propB: [String, Number],
    // 必传，且为 String 类型
    propC: {
      type: String,
      required: true
    },
    // Number 类型的默认值, 默认值为 100
    propD: {
      type: Number,
      default: 100
    },
    // 对象类型的默认值
    propE: {
      type: Object,
      // 对象或者数组应当用工厂函数返回。
      // 工厂函数会收到组件所接收的原始 props
      // 作为参数
      default(rawProps) {
        return { message: 'hello' }
      }
    },
     // 数组类型的默认值，默认为空数组。
    propArr:{
      type: Array,
      default(){
        return [ ]
      }
    },
    // 自定义类型校验函数
    propF: {
      validator(value) {
        // 传入的 value 必须是下列三者之一
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // 函数类型的默认值
    propG: {
      type: Function,
      // 不像对象或数组的默认，这不是一个工厂函数。这会是一个用来作为默认值的函数
      default() {
        return 'Default function'
      }
    }
  }
}
```

一些补充细节：

* 所有 prop 默认都是可选的，除非声明了 `required: true`。
* 除 `Boolean` 外的未传递的可选 prop 将会有一个默认值 `undefined` 。
* `Boolean` 类型的未传递 prop 将被转换为 `false` 。这可以通过为它设置 `default` 来更改——例如：设置为 `default: undefined` 将与非布尔类型的 prop 的行为保持一致。
* 如果声明了 `default` 值，那么在 prop 的值被解析为 `undefined` 时，无论 prop 是未被传递还是显式指明的 `undefined` ，都会改为 `default` 值。

当 prop 的校验失败后，Vue 会抛出一个控制台警告 (在开发模式下)。

>注意
>注意 prop 的**校验是在组件实例被创建之前**，所以**实例的属性 (比如 `data` 、 `computed` 等) 将在 `default` 或 `validator` 函数中不可用**。

### 1.4.2. 运行时类型检查

校验选项中的 `type` 可以是下列这些原生构造函数：

* String
* Number
* Boolean
* Array
* Object
* Date
* Function
* Symbol

另外， `type` 也可以是自定义的类或构造函数，Vue 将会通过 `instanceof` 来检查类型是否匹配。例如下面这个类：

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }
}
```

你可以将其作为一个 prop 的类型：

```js
export default {
  props: {
    author: Person
  }
}
```

Vue 会通过 `instanceof Person` 来校验 `author` prop 的值是否是 `Person` 类的一个实例。

## 1.5. Boolean 类型转换

为了更贴近原生 `boolean` attributes 的行为，声明为 `Boolean` 类型的 props 有特别的类型转换规则。以带有如下声明的 `<MyComponent>` 组件为例：

```js
export default {
  props: {
    disabled: Boolean
  }
}
```

该组件可以被这样使用：

```template
<!-- 等同于传入 :disabled="true" -->
<MyComponent disabled />

<!-- 等同于传入 :disabled="false" -->
<MyComponent />
```
当一个 prop 被声明为允许多种类型时，例如：

```js
export default {
  props: {
    disabled: [Boolean, Number]
  }
}
```

无论声明类型的顺序如何， `Boolean` 类型的特殊转换规则都会被应用。
