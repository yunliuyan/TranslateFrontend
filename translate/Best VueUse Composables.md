VueUse 拥有大量出色的可组合项。

但由于数量太多，将它们全部浏览一遍可能会让人不知所措。

这就是为什么整理了这个列表，以展示一些值得额外关注的最佳 Vue 可组合项。我们将介绍每个可组合项的用途以及它的用途。我还为每个内容提供了现场演示，以展示它们的实际效果。

以下是我们将在本文中介绍的 VueUse 可组合项：

1. `onClickOutside`
2. `useFocusTrap`
3. `useHead`
4. `useStorage`
5. `useVModel`
6. `useImage`
7. `useDark`

# 1、onClickOutside

![img](./img/0_8vk4szIbaATlCwk6.bin)

检测点击非常简单。但是检测何时在元素*外部*发生点击呢？这有点棘手。当然，除非您使用`onClickOutside`VueUse 中的可组合项。

```vue
<script setup>
import { ref } from 'vue'
import { onClickOutside } from '@vueuse/core'
const container = ref(null)
onClickOutside(container, () => alert('Good. Better to click outside.'))
</script>
<template>
  <div>
    <p>Hey there, here's some text.</p>
    <div class="container" ref="container">
      <p>Please don't click in here.</p>
    </div>
  </div>
</template>
```

 创建一个要跟踪的为`container`的`ref`元素： 

```vue
const container = ref(null);
```

 然后将其转换为模板引用，并在元素上添加`ref`属性： 

```vue
<div class="container" ref="container">
  <p>Please don't click in here.</p>
</div>
```

 现在有了容器，将它与处理程序一起传递给可组合项`onClickOutside`： 

```vue
onClickOutside(
  container,
  () => alert('Good. Better to click outside.')
)
```

 该可组合项对于管理窗口或下拉菜单很有用。当用户单击下拉菜单外部时，您可以将其关闭。 

`onClickOutside`[查看此处](https://vueuse.org/core/onclickoutside/#onclickoutside)[的文档](https://vueuse.org/core/onclickoutside/#onclickoutside)。

[转到此处查看实际演示。](https://stackblitz.com/edit/vue3-script-setup-with-vite-18scsl?file=src%2FApp.vue)

# 2.useFocusTrap

![img](./img/0_g72jYGnMgtPUHJRf.bin)

为了获得可访问的应用程序，正确管理焦点非常重要。

没有什么比不小心按到模式*后面*并且无法将焦点重新回到模式更糟糕的了。这正是焦点陷阱的用武之地。

他们将键盘焦点锁定到特定的 DOM 元素。您的键盘焦点不会循环遍历整个页面，然后循环浏览器本身，而是仅循环遍历该 DOM 元素。

这是使用VueUse中`useFocusTrap`的示例：

```vue
<script setup>
import { ref } from 'vue'
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
const container = ref(null)
useFocusTrap(container, { immediate: true })
</script>
<template>
  <div>
    <button tab-index="-1">Can't click me</button>
    <div class="container" ref="container">
      <button tab-index="-1">Inside the trap</button>
      <button tab-index="-1">Can't break out</button>
      <button tab-index="-1">Stuck here forever</button>
    </div>
    <button tab-index="-1">Can't click me</button>
  </div>
</template>
```

`immediate`设置为`true`时，页面加载后焦点将立即放置在元素内。然后就不可能在`container`容器之外进行`tab`操作。

一旦到达第三个按钮，`tab`再次点击即可返回第一个按钮。

就像使用一样`onClickOutside`，我们首先为以下设置模板引用`container`：

```vue
const container = ref(null)
<div class="container" ref="container">
  <button tab-index="-1">Inside the trap</button>
  <button tab-index="-1">Can't break out</button>
  <button tab-index="-1">Stuck here forever</button>
</div>
```

然后我们将此模板引用传递给可`useFocusTrap`组合项：

```
useFocusTrap(container, { immediate: true });
```

该`immediate`选项将自动将焦点设置到容器内的第一个可聚焦元素。

- 必须安装`focus-trap`，因为这封装了
- 焦点陷阱可以嵌套，自动处理暂停和取消暂停

[查看此处的文档](https://vueuse.org/integrations/usefocustrap/#usefocustrap)`useFocusTrap`[。](https://vueuse.org/integrations/usefocustrap/#usefocustrap)

[转到此处查看实际演示。](https://stackblitz.com/edit/vue3-script-setup-with-vite-eocc6w?file=src%2FApp.vue)

# 3.useHead

![img](./img/0_w8YCQ7B7aSQR82xC.bin)

VueUse 为我们提供了一种简单的方法来更新应用程序的头部部分 - 页面标题、脚本和其他可能包含在其中的内容。

可组合项`useHead`要求我们首先设置一个插件：

```vue
import { createApp } from 'vue'
import { createHead } from '@vueuse/head'
import App from './App.vue'
const app = createApp(App)
const head = createHead()
app.use(head)
app.mount('#app')
```

一旦我们使用该插件，我们就可以更新我们想要改变的头部部分。在此示例中，我们将在按下按钮时注入一些自定义样式：

```vue
<script setup>
import { ref } from 'vue'
import { useHead } from '@vueuse/head'
const styles = ref('')
useHead({
  // Inject a style tag into the head
  style: [{ children: styles }],
})
const injectStyles = () => {
  styles.value = 'button { background: red }'
}
</script>
<template>
  <div>
    <button @click="injectStyles">Inject new styles</button>
  </div>
</template>
```

首先，我们创建一个引用来表示我们将注入的样式。我们暂时将其至空：

```vue
const styles = ref('');
```

其次，我们将设置`useHead`将样式注入到页面中：

```
useHead({
  // Inject a style tag into the head
  style: [{ children: styles }],
})
```

然后我们添加注入这些样式的方法：

```
const injectStyles = () => {
  styles.value = 'button { background: red }'
}
```

我们在这里所做的只是更新我们的样式参考的值。但反应性很好，每次这个值改变时，`useHead`都会自动更新注入的样式。

当然，我们不仅限于注入样式。我们可以将其中任何一个添加到我们的``：

- 标题
- 元标签
- 链接标签
- [基本标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base)
- 风格标签
- 脚本标签
- html属性
- 身体属性

[查看此处的文档](https://github.com/vueuse/head#api)`useHead`[。](https://github.com/vueuse/head#api)

[请在此处查看实际演示。](https://stackblitz.com/edit/vue3-script-setup-with-vite-szhedp?file=src%2FApp.vue)

# 4.useStorage

可组合项`useStorage`真的很酷，因为它会自动将您的引用同步到本地存储。

这是一个基本示例：

```vue
<script setup>
import { useStorage } from '@vueuse/core'
const input = useStorage('unique-key', 'Hello, world!')
</script>
<template>
  <div>
    <input v-model="input" />
  </div>
</template>
```

当您首次加载应用程序时，输入将显示“Hello, world!”。但之后它将显示您输入的最后一个内容，因为它已保存到本地存储中。

此可组合项还适用于会话存储：

```
const input = useStorage('unique-key', 'Hello, world!', sessionStorage)
```

你实际上可以提供任何你想要的存储系统，只要它实现`StorageLike`  [接口](https://github.com/vueuse/vueuse/blob/ef6cf56cfde1e41cead37ccda48cde68f89ec931/packages/core/ssr-handlers.ts#L9-L13)：

```
export interface StorageLike {
  getItem(key: string): string | null
  setItem(key: string, value: string): void
  removeItem(key: string): void
}
```

[查看此处的文档](https://vueuse.org/core/usestorage/)`useStorage`[。](https://vueuse.org/core/usestorage/)

[请在此处查看实际演示。](https://stackblitz.com/edit/vue3-script-setup-with-vite-wzemb6?file=src%2FApp.vue)

# 5.useVModel

该`v-model`指令是一些很好的语法糖，可以使双向数据绑定更容易。

但`useVModel`可组合性更进一步，摆脱了一堆没人真正想编写的样板代码：

```vue
<script setup>
import { useVModel } from '@vueuse/core'
const props = defineProps({
  count: Number,
})
const emit = defineEmits(['update:count'])
const count = useVModel(props, 'count', emit)
</script>
<template>
  <div>
    <button @click="count = count - 1">-</button>
    <button @click="count = 0">Reset to 0</button>
    <button @click="count = count + 1">+</button>
  </div>
</template>
```

在此示例中，我们首先定义要附加到`v-model`的 prop ：

```
const props = defineProps({
  count: Number,
})
```

然后，我们声明一个使用`update:`<propName>的`v-model`命名约定的事件`emit`：

```
const emit = defineEmits(['update:count'])
```

现在我们可以使用可`useVModel`组合项将 props 和事件emit绑定到 ref：

```
const count = useVModel(props, 'count', emit)
```

`count`每当 props 改变时，这个ref 就会改变。但每当该组件发生更改时，它都会发出事件`update:count`以通过指令触发更新`v-model`。

我们可以像这样使用这个`Input`组件：

```vue
<script setup>
import { ref } from 'vue'
import Input from './components/Input.vue'
const count = ref(50)
</script>
<template>
  <div>
    <Input v-model:count="count" />
    {{ count }}
  </div>
</template>
```

这里的`count`ref通过`v-model`绑定同步到`Input`组件内部的`count`ref。

[查看此处的文档](https://vueuse.org/core/usevmodel/#usevmodel)`useVModel`[。](https://vueuse.org/core/usevmodel/#usevmodel)

[请在此处查看实际演示。](https://stackblitz.com/edit/vue3-script-setup-with-vite-ut5ap8?file=src%2FApp.vue)

# 6.useImage

![img](./img/0_LPF_Y1zPF5Lw8YG6.bin)

随着时间的推移，网络应用程序中的图像变得越来越精美。我们拥有带有`srcset`的响应式图像、渐进式加载库以及仅在图像滚动到视口中时才加载图像的库。

但您知道我们还可以访问图像本身`loading`并对其`error`进行说明吗？

我之前通过监听[每个 HTML 元素发出的](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes)`onload`和`onerror`事件来完成此操作，但 VueUse 为我们提供了一种更简单的可组合方法：`useImage`

```vue
<script setup>
import { useImage } from '@vueuse/core'
// Change this to a non-existent URL to see the error state
const url = 'https://source.unsplash.com/random/400x300'
const { isLoading, error } = useImage(
  {
    src: url,
  },
  {
    // Just to show the loading effect more clearly
    delay: 2000,
  }
)
</script>
<template>
  <div>
    <div v-if="isLoading" class="loading gradient"></div>
    <div v-else-if="error">Couldn't load the image :(</div>
    <img v-else :src="url" />
  </div>
</template>
```

首先，我们使用图像 URL 设置可组合项：

```
const { isLoading, error } = useImage({ src: url })
```

我们将获取它返回的`isLoading`和`error`refs，以便我们可以跟踪状态。此可组合项`useAsyncState`在内部使用，因此它返回的值与该可组合项相同。

一旦我们这样做了，`useImage`加载我们的图像并将事件处理程序附加到它。

我们需要做的就是通过使用相同的 URL 在模板中使用该图像。由于浏览器将重用任何缓存的图像，因此它将重用通过以下方式加载的图像`useImage`：

```vue
<template>
  <div>
    <div v-if="isLoading" class="loading gradient"></div>
    <div v-else-if="error">Couldn't load the image :(</div>
    <img v-else :src="url" />
  </div>
</template>
```

在这里，我们设置了一个基本的加载和错误状态处理程序。加载图像时，我们会显示一个带有动画渐变的占位符。如果出现错误，我们会显示错误消息。否则我们可以渲染图像。

不过，我想重申这一点：因为图像已经由 加载`useImage`，一旦将该`img`标签添加到 DOM，它就会渲染图像。

这个可组合项还有其他一些很棒的功能！如果您想使其成为响应式图像，它支持`srcset`和`sizes`属性，这些属性会传递到`img`幕后的元素。

如果您希望将所有内容保留在模板中，还有一个无渲染组件。它的工作方式与可组合项相同：

```vue
<template>
	<UseImage src="https://source.unsplash.com/random/401x301">
    <template #loading>
		<div class="loading gradient"></div>
	</template>
    <template #error>
		Oops!
	</template>
  </UseImage>
</template>
```

[查看此处的文档](https://vueuse.org/core/useimage/#useimage)`useImage`[。](https://vueuse.org/core/useimage/#useimage)

[请在此处查看实际演示。](https://stackblitz.com/edit/vue3-script-setup-with-vite-d1jsec?file=src%2FApp.vue)

# 7. 深色模式`useDark`

![img](./img/0_fb9cyOk_uF7_W3o_.bin)

如今，每个网站和应用程序似乎都有深色模式。

困难的部分是样式的改变。但一旦你掌握了这一点，来回切换就非常简单了。

如果您使用的是 Tailwind，则只需将该`dark`类添加到`html`元素即可为整个页面启用它：

```html
<html class="dark"><!-- ... --></html>
```

但是，在深色模式和浅色模式之间切换时需要考虑一些事项。首先，我们要考虑用户的系统设置。其次，我们要记住他们是否推翻了这个选择。

VueUse 的可组合项`useDark`为我们将所有这些东西包装在一起。默认情况下，它会查看系统设置，但任何更改都会保留，`localStorage`以便记住设置：

```vue
<script setup>
import { useDark, useToggle } from '@vueuse/core'
const isDark = useDark()
const toggleDark = useToggle(isDark)
</script>
<template>
  <div class="container">
    Changes with dark/light mode.
    <button @click="toggleDark()">
        Enable {{ isDark ? 'Light' : 'Dark' }} Mode
    </button>
  </div>
</template>
```

我们还将包括这些深色模式样式：

```css
.dark .container {
  background: slategrey;
  color: white;
  border-color: black;
}
.dark button {
  background: lightgrey;
  color: black;
}
.dark body {
  background: darkgrey;
}
```

如果您不使用 Tailwind，则可以通过传入选项对象来完全自定义黑暗模式的应用方式。默认的 Tailwind 如下所示：

```vue
const isDark = useDark({
  selector: 'html',
  attribute: 'class',
  valueDark: 'dark',
  valueLight: '',
})
```

您还可以提供一个`onChanged`处理程序，以便您可以编写所需的任何 Javascript。这两种方法允许您使其与您已有的任何样式系统一起使用。

[查看此处的文档](https://vueuse.org/core/usedark/#usedark)`useDark`[。](https://vueuse.org/core/usedark/#usedark)

[请在此处查看实际演示。](https://stackblitz.com/edit/vue3-script-setup-with-vite-gs3hay?file=src%2FApp.vue)

# 总结

VueUse 拥有一个巨大的奇妙可组合项库，我们在这里只介绍了其中的一小部分。我强烈建议您花一些时间浏览文档并查看所有可用的内容。这是一个很棒的资源，可以让您免于大量的样板代码和不断地重新发明轮子。虽然 VueUse 是一个很棒的库，但很多时候您会想要创建自己的可组合项。你知不知道怎么？[我们在 Vue Mastery 的“编码更好的可组合性”](https://www.vuemastery.com/courses/coding-better-composables/what-is-a-composable)课程中引导您完成它。
