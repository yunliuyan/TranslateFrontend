# 发布3.3版本

今天我们很高兴地宣布Vue 3.3“Rurouni Kenshin”的发布!

这个版本的重点是开发人员体验的改进——特别是SFC

这篇文章概述了3.3中突出显示的特性。完整的变更列表，请查阅GitHub上的完整变更日志。

------

依赖项更新

当升级到3.3时，建议也更新以下依赖项:

- Volar / vue-tsc@^1.6.4
- vite@ ^ 4.3.5
- @vitejs / plugin-vue@ ^ 4.2.0
- vue-loader@^17.1.0(如果使用webpack或vue-cli)

[TOC]


## `<script setup>`+ TypeScript DX改进

### 宏中支持导入类型和复杂类型

以前，在defineProps和defineemit的类型参数位置中使用的类型仅限于局部类型，并且只支持类型字面量和接口。这是因为Vue需要能够分析props接口上的属性，以便生成相应的运行时选项。

这个限制现在在3.3中得到了解决。编译器现在可以解析导入的类型，并支持一组有限的复杂类型：

```vue
<script setup lang="ts">
import type { Props } from './foo'

// imported + intersection type
defineProps<Props & { extraProp?: string }>()
</script>
```

请注意，复杂类型支持是基于ast的，因此不是100%全面。不支持一些需要实际类型分析的复杂类型，例如条件类型。您可以对单个prop的类型使用条件类型，但不能对整个props对象使用条件类型。

细节: [PR#8083](https://github.com/vuejs/core/pull/8083) 

### 通用组件

使用＜script setup＞的组件现在可以通过泛型属性接受泛型类型参数：

```vue
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
  selected: T
}>()
</script>
```

泛型的值与＜…＞之间的参数列表完全相同在TypeScript中。例如，可以使用多个参数、扩展约束、默认类型和引用导入的类型：

```vue
<script setup lang="ts" generic="T extends string | number, U extends Item">
import type { Item } from './types'
defineProps<{
  id: T
  list: U[]
}>()
</script>
```

此功能以前需要显式选择加入，但现在在最新版本的volar/vue tsc中默认启用。

- 讨论: [RFC#436](https://github.com/vuejs/rfcs/discussions/436) 
- 相关: [generic `defineComponent()` - PR#7963](https://github.com/vuejs/core/pull/7963) 

### 更符合设计的`defineEmits`

以前，defineEmits的type参数只支持调用签名语法：

```ts
// BEFORE
const emit = defineEmits<{
  (e: 'foo', id: number): void
  (e: 'bar', name: string, ...rest: any[]): void
}>()
```

该类型与emit的返回类型匹配，但是写起来有点冗长和笨拙。3.3引入了一种更符合人体工程学的方式来用类型声明emit:

ts

```ts
// AFTER
const emit = defineEmits<{
  foo: [id: number]
  bar: [name: string, ...rest: any[]]
}>()
```

在类型文字中，键是事件名称，值是指定附加参数的数组类型。虽然不是必需的，但您可以使用标记的元组元素来显式，就像上面的例子一样。

仍然支持调用签名语法。

### 带的类型插槽`defineSlots`

新的defineSlots宏可用于声明预期插槽及其各自的预期插槽props：

```ts
<script setup lang="ts">
defineSlots<{
  default?: (props: { msg: string }) => any
  item?: (props: { id: number }) => any
}>()
</script>
```

defineSlots()只接受类型参数，不接受运行时参数。类型参数应该是类型字面值，其中属性键是插槽名，值是插槽函数。该函数的第一个参数是slot期望接收的props，它的类型将用于模板中的slot的props。defineSlots的返回值与useSlots返回的slots对象相同。

目前的一些限制:

- 所需的槽位检查尚未在volar / vue-tsc中实现。
- 插槽函数返回类型目前被忽略，可以是`any`，但我们可能会在未来利用它来检查插槽内容。

对于defineComponent的使用，还有一个相应的插槽选项。这两个API都没有运行时含义，只是作为IDE和vue-tsc的类型提示。

- 细节: [PR#7982](https://github.com/vuejs/core/issues/7982) 

## 实验特性

### 响应式Props结构

响应式props解构作为响应式变换被破坏的一部分，被拆分为一个单独的特性。

该功能允许被破坏的props保持响应性，并提供了一种更符合人体工程学的方式来声明props默认值

```
<script setup>
import { watchEffect } from 'vue'

const { msg = 'hello' } = defineProps(['msg'])

watchEffect(() => {
  // accessing `msg` in watchers and computed getters
  // tracks it as a dependency, just like accessing `props.msg`
  console.log(`msg is: ${msg}`)
})
</script>

<template>{{ msg }}</template>
```

这个功能是实验性的，需要明确的选择加入。

- 细节: [RFC#502](https://github.com/vuejs/rfcs/discussions/502) 

### `defineModel`

以前，对于支持与v-model双向绑定的组件，它需要（1）声明一个prop，（2）在打算更新prop时发出相应的update:proName事件：

```vue
<!-- BEFORE -->
<script setup>
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
console.log(props.modelValue)

function onInput(e) {
  emit('update:modelValue', e.target.value)
}
</script>

<template>
  <input :value="modelValue" @input="onInput" />
</template>
```

3.3通过新的defineModel宏简化了使用。宏会自动注册一个道具，并返回一个可以直接转换的ref：

```vue
<!-- AFTER -->
<script setup>
const modelValue = defineModel()
console.log(modelValue.value)
</script>

<template>
  <input v-model="modelValue" />
</template>
```

这个功能是实验性的，需要明确的选择加入。

- 细节: [RFC#503](https://github.com/vuejs/rfcs/discussions/503) 

## 其他显著特性

### `defineOptions`

新的defineOptions宏允许在＜script setup＞中直接声明组件选项，而不需要单独的＜script＞块：

```vue
<script setup>
defineOptions({ inheritAttrs: false })
</script>
```

### 更好的Getter支持`toRef`和`toValue`

toRef已得到增强，支持将  values / getters / existing refs 规范化为refs：

```js
// equivalent to ref(1)
toRef(1)
// creates a readonly ref that calls the getter on .value access
toRef(() => props.foo)
// returns existing refs as-is
toRef(existingRef)
```

用getter调用toRef类似于computed，但当getter只执行属性访问而不进行额外的计算时，效率会更高。

新的toValue实用程序方法提供了相反的结果，将 values / getters / refs 规范化为值：

```js
toValue(1) //       --> 1
toValue(ref(1)) //  --> 1
toValue(() => 1) // --> 1
```

toValue可以在可组合文件中使用，而不是unref，这样您的可组合文件就可以接受getter作为响应数据源：

```js
// before: 分配不必要的中间refs
useFeature(computed(() => props.foo))
useFeature(toRef(props, 'foo'))

// after: 更高效简洁
useFeature(() => props.foo)
```

toRef和toValue之间的关系类似于ref和unref之间的关系，主要区别在于对getter函数的特殊处理。

- 细节: [PR#7997](https://github.com/vuejs/core/pull/7997) 

### JSX导入源支持

目前，Vue的类型自动注册全局JSX类型。这可能会导致与其他需要JSX类型推断的库（尤其是React）一起使用时发生冲突。

从3.3开始，Vue支持通过TypeScript的 [jsxImportSource](https://www.typescriptlang.org/tsconfig#jsxImportSource) 选项指定JSX命名空间。这允许用户根据他们的用例选择全局或按文件选择加入。

为了向后兼容性，3.3仍然全局注册JSX命名空间。**我们计划在3.4中删除默认的全局注册。**如果您将TSX与Vue一起使用，则应在升级到3.3后将显式的jsxImportSource添加到tsconfig.json中，以避免在3.4中出现中断。

## 维护及基础设施改善

此版本建立在许多维护基础设施改进的基础上，使我们能够更有信心地更快地移动:

- 通过将类型检查从累积构建中分离出来，从而使构建速度提高10倍`rollup-plugin-typescript2`来`rollup-plugin-esbuild`．
- 通过从Jest迁移到Vitest来加快测试速度。
- 通过移动`@microsoft / api-extractor`来`rollup-plugin-dts`，更快地生成类型
- 通过  [ecosystem-ci](https://github.com/vuejs/ecosystem-ci)i进行全面的回归测试——在发布之前捕获主要生态系统依赖项中的回归!

按照计划，我们的目标是在2023年开始制作更小、更频繁的功能版本。请继续关注!