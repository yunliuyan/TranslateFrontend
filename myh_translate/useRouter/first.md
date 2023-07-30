# 如何使用 Vue Router：完整教程

现代单页 Web 应用程序 (SPA) 最强大的功能之一是路由。现代单页应用程序（例如 Vue 应用程序）可以在客户端从一个页面转换到另一个页面（无需请求服务器）。**Vue Router**是 Vue 应用程序中用于页面导航的官方库。Vue Router 使用简单，但功能强大。在本文中，我们将深入研究**Vue Router 4（与 Vue 3 一起使用）。** 我们将介绍您舒适使用 Vue Router 所需了解的所有内容。

我们将涵盖：

- Vue 路由器基础知识
- 动态路由
- 如何传递路由器参数
- 延迟加载

无论您是刚刚接触 Vue Router 的初学者，还是希望提高技能的经验丰富的 JavaScript/Vue 开发人员，本教程都能为您提供帮助。让我们开始吧。

*👉 我们还创建了**完全免费的**深入[Vue Router 视频课程](https://vueschool.io/courses/vue-router-4-for-everyone)。如果您通过观看视频学得更好，我们强烈建议您尝试一下。[单击此处观看 Vue Router 课程](https://vueschool.io/courses/vue-router-4-for-everyone)。*

## Vue 路由器基础知识

让我们创建一个新项目并开始编码。我们将使用[vue-cli](https://cli.vuejs.org/)创建一个新项目。如果您没有安装它，您可以通过运行来安装它`npm install -g @vue/cli`。

***# Create a new Vue project***

 **vue create myapp**

 **cd myapp**

 ***# start your app by running the following command***

 **npm run serve**

接下来我们可以通过运行以下命令将 Vue Router 添加到我们的项目中。

**vue add router**

它将`cli` 询问我们是否想要历史模式。在本教程中，我们将使用历史模式，因此我们将选择“是”

![image1 ](images\image1 .png)

让我们看一下为我们创建的文件夹结构

![image2](images\image2.png)

为我们创建了一个名为 的文件夹`views`以及两个文件`About.vue`和`Home.vue`。这些文件代表我们的页面。`router/index.js`还会生成一个名为 file 的 文件。该文件包含所有路由器配置。让我们打开该`router/index.js`文件。

**import { createRouter, createWebHistory } from 'vue-router'**

 **import Home from '../views/Home.vue'**

 **const routes = [** 

 **{    path: '/',    name: 'Home',    component: Home  },** 

 **{    path: '/about',    name: 'About',** 

   ***// route level code-splitting***  

  ***// this generates a separate chunk (about.[hash].js) for this route***   

 ***// which is lazy-loaded when the route is visited.***    

**component: () => import(*/\* webpackChunkName: "about" \*/* '../views/About.vue')** 

 **}**

**]**

 **const router = createRouter({** 

 **history: createWebHistory(process.env.BASE_URL),** 

 **routes**

 **})** 

**export default router**

我们正在从vue-router库中导入createrouter和createWebhistory，接下来，我们从 导入 Home 组件。在第 3 行，我们声明了一个名为“routes”的对象数组。该数组代表我们在应用程序中的路由。这些数组项称为路由对象。第一个路由对象有一个路径，这意味着这将是我们的基本 URL。组件属性表示当用户访问该路径时将呈现什么组件。我们将在此路径上渲染主页。最后，name 属性表示路线的名称。`createWebHistory``vue-router``views/Home.vue``/`

`/about`我们对于路径几乎遵循相同的逻辑。然而，我们不是直接导入组件，而是通过 Webpack 的代码分割功能导入它。稍后会详细介绍这一点。

现在让我们进入`App.vue` 文件。

![](images\image3.png)

观察`router-link`标签。这些标签只是奇特的锚链接。然而，与锚链接（`<a href="">`标签）不同，它`<router-link>`不会重新加载整个页面。请记住，Vue 是一个单页应用程序。该应用程序的数据已从服务器下载。当我们路由到另一个视图时，应用程序只是隐藏一些信息并显示请求的信息。`router-link`标签有一个`to`属性来指示要访问哪个页面。当导航链接被触发时，标签 `<router-view/>`会呈现正确的组件。

让我们将自己的路由添加到该应用程序中。

![](images\image4.png)

我们需要添加一个新的路由器链接到`App.vue`. 接下来我们在views目录中创建一个新组件。

![](images\image5.png)

最后，在我们的路由数组中，我们必须导入它并指定路由路径。

![](images\image6.png)

就这样，我们将自己的路由添加到了应用程序中。

![](images\image7.gif)



## 如何在 Vue Router 中使用路由器参数

首先，我们来看看什么是路由器参数（简称router params）以及它们为何如此重要。假设我们的应用程序中有一个页面，我们向用户显示所有可用博客文章的列表。

![](images\image8.png)

假设在我们的应用程序中这条路径是`/posts`。当用户单击这些文章中的任何一篇时，用户就会被带到该文章页面。对于每篇文章，路径都会不同（`/posts/article-1`、`/posts/article-2`等）。我们如何实现这一目标？我们可以为每个帖子添加一条新路线，但每次发布新帖子时我们都必须更改代码。因此，我们将使用动态路由。这就是路由器参数派上用场的地方。

在我们的应用程序中，我们可以指定一个通用路由，例如`/posts` and ，然后我们可以为同一路由指定可选路由参数，如下所示`/posts/:id`。选取`:id`任何动态字符串作为参数。这意味着我们现在可以路由到 `/posts/article-1`、`/posts/article-2`等路径，并且应用程序中的 Vue 路由器将知道我们要尝试前往哪条路径。

让我们看看它在我们的应用程序中的实际应用。

![](images\image9.png)

在我们的`router-link` 标签中，我们可以使用`:to`属性来传递带有名称和参数数据的对象。params 对象可以是任何类型的数据（即字符串、数字、对象）。在上面的代码片段中，我们传入一个带有`username`字符串值（mark2021）的对象。

## 如何从视图访问路由参数

当我们将 Vue 路由器添加到 Vue 应用程序时，我们可以访问`$route` 应用程序全局范围内的特殊对象。该对象保存了我们应用程序的所有路由器信息。我们可以 `$route`通过简单地调用来访问对象`this.$route`。参数信息也可在该`$route`对象中使用。我们可以通过该对象访问`params`Profile组件内部的对象`$route`。看看下面的代码片段。

![](images\image10.png)

![](images\image11.png)

## 动态路由

下面是一个快速参考表，可帮助您直观地了解动态路由的工作原理。

![](images\image12.png)

在上一节中，我们了解了如何使用 传递附加信息`<router-link>`。动态路由是一个类似的概念。在这种情况下，参数是其`URL`自身的一部分。

让我们看一下如何在应用程序中定义动态路由。让我们在 view 下创建一个新组件并将其命名为`Product`。

![](images\image13.png)

现在我们可以将此组件添加到我们的路线中。

![](images\image14.png)

`:id`请注意，我们在URL 中附加了一个动态参数 。现在，如果您`http://localhost:8080/product/12343`在我们的浏览器中访问，您可以看到该组件将有权访问通过 URL 传递的动态参数。

然后，我们可以使用它`Id`从生命周期挂钩中的 API 获取实际产品。

![](images\image15.png)

我们的 URL 中还可以有多个动态参数。动态段将映射到 的相应字段`$route.params`。

让我们看看它是如何工作的。我们将首先对我们的产品路线进行一些快速的改变

![](images\image16.png)

`:id`现在我们指定两个参数，而不是一个参数。现在让我们走上这条路`http://localhost:8080/product/12343/electronics`并打开 Vue 开发工具。正如您从下面的屏幕截图中看到的，我们现在可以访问参数，并且这些参数映射到指定的变量名称`id`和`category`.

![](images\image17.png)



## 延迟加载

我们来谈谈延迟加载。这是 Vue Router 提供的开箱即用的高级功能之一。让我们打开开发工具并观察`js` 选项卡。

![](images\image18.png)



您可以看到，当我们第一次加载应用程序时，它会加载一个`app.js`脚本。当您导航到`/profile`路线或`/product`路线时，您可以看到没有新的 JavaScript 被加载。这是因为这些组件的所有 JavaScript 代码都已经加载了`app.js`. 在较小的应用程序中，这不是问题。但是，如果我们有一个大型应用程序怎么办？在这种情况下，一次性加载所有 JS 代码会使页面加载时间明显延长。延迟加载就是为了避免这种情况。

当我们延迟加载时，我们只在需要时加载我们需要的部分。

![](images\image19.png)

看一下 的第 17 行`router/index.js`。您还记得之前的例子吗？这里的组件`About`是用箭头函数导入的。这是延迟加载的一个示例。 与 、和组件不同 `Home`，该 组件不随 一起加载。仅当用户访问该路线时该组件才会被加载。这就是我们实现延迟加载所要做的全部工作。这种延迟加载是通过 Webpack 的代码分割功能来完成的。在底层，Vue 使用 Webpack 作为捆绑器。[您可以在我们的Vue 3 大师班](https://vueschool.io/courses/the-vuejs-3-master-class)课程中深入了解更多有关高级 Vue 功能的信息，例如代码分割、Webpack 配置等。 `Profile``Product``About``app.js``About``/about`

我们可以更新代码以对每个路线使用延迟加载。

![](images\image20.png)

*💡 如果您希望成为一名深入了解 Vue 的开发者，那么请尝试一下我们的Vue [3 大师班。](https://vueschool.io/courses/the-vuejs-3-master-class)在本课程中，我们将深入研究 Vue 的一些高级功能，例如**高阶函数、代码分割、Webpack 配置、创建 vue 插件、路由保护**等等。*

## 程序化导航

在我们的应用程序中，有时我们希望以编程方式导航到另一个页面。在我们的应用程序中，我们可以访问路由器实例`$router`。我们可以通过调用来访问它`**this.$router**`。路由器实例有一个称为推送的功能。该功能可用于导航到不同的路线。以下是其工作原理的一些示例

![](images\image21.png)

## 从这往哪儿走

我希望本文能让您概述 Vue Router 的不同功能。我们讨论了 Vue Router 最重要和最常用的功能。请随意访问[vue 路由器文档，](https://router.vuejs.org/)了解有关各种 API 的更深入的详细信息。在 VueSchool，我们创建了一个[关于 Vue Router 的完全免费的课程](https://vueschool.io/courses/vue-router-4-for-everyone)。如需更深入的 Vue 学习材料，您还可以查看我们的[Vue 3 大师班](https://vueschool.io/courses/the-vuejs-3-master-class)课程。

我们还有针对 Vue.js 2[的 Vue Router 3 For Every 课程](https://vueschool.io/courses/vue-router-for-everyone)。

这就是今天的全部内容，祝各位快乐。

