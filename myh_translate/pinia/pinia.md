# Pinia vs Vuex - 为什么 Pinia 获胜

在 Vue.js 领域，我经常遇到使用 Vuex 和 Pinia 之间的争论以及为什么其中一个可能优于另一个。Pinia 被 Vue.js 官方网站推荐为 Vue.js 状态管理工具，这可能是它获得广泛欢迎的原因之一。在本文中，我将详细比较 Pinia 和 Vuex，概述它们的独特功能、预期用途、优点和缺点。

# Pinia

Pinia 是一个相对较新的状态管理库，允许您跨 Vue.js 组件管理响应式状态。也是目前推荐的全局状态管理工具。Pinia 是 Vue 核心团队成员 Eduardo San Martin Morote 的设计者。Pinia 使用新的反应机制构建了一个简单且类型正确的状态管理系统，并且是一个用于管理应用程序反应状态的优秀库。与 Vuex 相比，Pinia API 更容易学习，并且使您的代码更易于阅读。

### 结构

它是如何工作的？创建仓库就是这么简单！

```
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => {
    return { count: 0 }
  },
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

## 为什么您可能想要使用 Pinia（功能）

### 开发工具支持

Pinia 为 Vue 开发工具提供了出色的支持，使开发人员可以轻松调试和监控应用程序的状态和突变。

### 热模块更换

通过模块热替换，开发人员可以对其代码进行更改，更改将立即反映在正在运行的应用程序中，从而改进开发工作流程并减少开发时间。如果没有 Pinia，全局状态可以在 HMR 期间被销毁和重置，但 Pinia 会保留它

### 插件

Pinia 商店可以通过低级 API 进行全面扩展。通过[插件，](https://pinia.vuejs.org/core-concepts/plugins.html)您可以执行本地存储同步、ORM（对象关系映射）等任务，在定义存储时添加选项，以及当 Pinia 的默认行为不足以满足您的需求时您可以利用的更多任务。您可以开始使用一些[可用的插件](https://github.com/topics/pinia-plugin)，也可以根据自己的需要创建一个插件。

### 为 JS 用户提供适当的 TypeScript 支持或**自动完成**

Pinia 为 TypeScript 提供强大的支持，包括强大的 JavaScript 自动完成功能，这大大简化了开发过程。借助此功能，开发人员可以快速编写代码并避免常见错误，从而提高代码质量并缩短开发时间。

### 服务器端渲染支持

Pinia 提供对服务器端渲染 (SSR) 的支持，使开发人员能够在 SSR Vue.js 框架（例如 Nuxt）中使用 Pinia。

# Vuex

Vuex 是 Vue.js 应用程序的状态管理模式 + 库，充当应用程序中所有组件的集中存储，其规则保证状态只能以可预测的方式更改。

### 结构

这就是 Vuex 中商店的样子。

```
import { createStore } from 'vuex'

const counterstore = createStore({
  state() {
    return {
      todoListItems: []
    }
  },
getters: {
    countList (state) {
      return state.todoListItems.length()
    }
  actions: {
    addNewToList({ commit }, item) {
    {
      commit('createNewItem', item)
    }
  },
  mutations: {
    createNewItem(state, item) {
      state.todoListItems.push(item)
    }
  }
})
```

## 为什么你可能想使用 Vuex（功能）

### 模块

通过利用 Vuex 模块，您可以根据域函数将存储分成多个文件，这允许您从特定命名空间中的模块访问状态循环。随着应用程序大小的增加，这将成为避免使其变得难以使用的有用策略。

### HMR 支持

Vuex 还支持热重载功能，该功能利用 webpack 的热模块替换 API 在您继续开发代码时快速重新加载您的mutations、模块、actions 和 getter。

### 开发工具支持

您还可以利用 Vue 开发工具中的 Vuex 选项卡来方便地访问和调试我们的突变，以及查看状态，为我们提供了一种跟踪应用程序状态的简单方法。

## 是什么让 Pinia 与众不同、更好？

Pinia 与其他解决方案（包括 Vuex）相比具有多种优势；

### 直观的设计

借助 Pinia，通过其简单的 API，创建和组织商店变得简单轻松，类似于创建组件。与 Vuex 解决方案相比，这种方法减少了对样板代码的需求，并减少了需要学习的概念数量。您不必记住：“是它`commit`还是`dispatch`🤔”或使用魔术字符串来调用操作例如：`dispatch(’doTheThing’)`。这也为其他开发者提供了一种更简单、更好的方式来了解您的商店。

### 突变不再存在

无需考虑创建规则来更新您的状态。直接更新它们。这么大的缓解

### 更好的 TypeScript 支持

无需创建自定义的复杂包装器来支持 TypeScript，一切都是类型化的，并且 API 的设计方式是尽可能利用 TS 类型推断。

### 自动完成

JS 自动完成功能可以更好地编写代码。不再需要注入、导入函数、调用它们的神奇字符串。

### 更好的模块结构

不再有模块的嵌套结构。您仍然可以通过在另一个存储中导入和使用存储来隐式嵌套存储，但 Pinia 通过设计提供扁平结构，同时仍然支持存储之间的交叉组合方式。

### 轻量

Pinia 的重量仅为 1 KB，因此可以非常轻松地融入您的项目中。这可能会提高您的应用程序性能。****

## 我应该使用哪个？

看看这两个状态管理库，我们可以看到它们各有优势，但 Pinia 比 Vuex 具有明显的优势。Pinia 已被指定为推荐的状态管理库，使其成为构建 Vue.js 应用程序的明确选择。

如果您已经在 Vuex 上运营商店，请不要担心，它不会很快消失。但是，如果您正在考虑将 Vuex 商店迁移到 Pinia，那么您绝对应该阅读这篇[文章](https://vueschool.io/articles/vuejs-tutorials/how-to-migrate-from-vuex-to-pinia/)。

这绝对是一条有用的信息，我希望您喜欢阅读它。如果您想要更详细的解释，我建议您阅读 [Vuex 文档](https://vuex.vuejs.org/)和[Pinia 文档](https://pinia.vuejs.org/)，或者如果您想探索这两个库，那么绝对要查看[Vuex For Every](https://vueschool.io/courses/vuex-for-everyone)和[Pinia：](https://vueschool.io/courses/pinia-the-enjoyable-vue-store) 来自 Vue School 的令人愉快的 Vue Store 课程，以获得关于使用这些技术。