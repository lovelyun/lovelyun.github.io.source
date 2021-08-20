---
title: 尤雨溪带你实现一个mini vue
date: 2021-08-20 16:25:23
tags: js
categories: js
---

![mini_vue_1.png](/img/mini_vue_1.png)

## 前言
本篇文章主要是解析尤雨溪在[codepen](https://codepen.io/yyx990803/details/JjELqRo)上实现的一段mini-vue(solution)代码。

前面的文章，我们学习了怎么把数据变成响应式，怎么用虚拟dom局部刷新页面，现在我们在这两者的基础上，看怎么实现一个mini vue。

## 代码解析
先把所有的代码折叠起来看一下：

![mini_vue_2.png](/img/mini_vue_2.png)

可以看到，在响应式、虚拟dom的实现上，和前文一模一样，只是在这两个功能上，增加了1个createApp函数，那么接下来我们主要看看createApp函数干了什么。

首先我们初始化了一个叫Component的常量，这个常量内部有data和render函数，data返回了`{ count: 0 }`，render返回一个虚拟dom，渲染这个虚拟dom就会向页面中添加一个div，div中显示count的值，并绑定了点击事件，点击div就让count自增1。（虚拟dom渲染不清楚的话，可以看看上文）

```javascript
const Component = {
  data() {
    return {
      count: 0
    }
  },
  render() {
    return h(
      'div',
      {
        onClick: () => {
          this.count++
        }
      },
      String(this.count)
    )
  }
}
```

然后我们来看看createApp函数。

```javascript
function createApp(Component, container) {
  // implement this
  const state = reactive(Component.data())
  let isMount = true
  let prevTree
  watchEffect(() => {
    const tree = Component.render.call(state)
    if (isMount) {
      mount(tree, container)
      isMount = false
    } else {
      patch(prevTree, tree)
    }
    prevTree = tree
  })
}

// calling this should actually mount the component.
createApp(Component, document.getElementBy('app'))
```

进入函数后，首先将`Component.data()`即`{ count: 0 }`变成响应式后赋值给state，然后申明两个变量isMount和prevTree，然后调用watchEffect：

```javascript
function watchEffect(effect) {
  activeEffect = effect
  effect()
  activeEffect = null
}
```

那么就执行了effect函数，申明并初始化tree常量，`Component.render.call(state)`的意思是执行Component中的render函数，并把render函数内的this指向state。

初次createApp时，isMount是true，所以接下来就直接渲染tree，并将isMount变成false，后面count数据变化时，触发effect函数再次走到`if (isMount)`这个判断时，isMount值就都是false，从而`patch(prevTree, tree)`，用新的tree对前一个tree打补丁，即局部刷新页面。

每次渲染或局部刷新完页面，就把当前的tree赋值给prevTree。

现在一个mini vue就实现了，点击页面上的0，0就会变成1，每次点击页面上的数字，数字就自增1。
（响应式和虚拟节点相关代码这里就不贴了，有需要去前文看，或者直接去codepen）

## 总结
简单来说，之前实现的数据响应式，在使用时，只是在数据变化时把数据打印出来：

```javascript
watchEffect(() => {
  console.log(state.count)
})
```

现在只是把watchEffect的传参effect的功能改一下，从简单的打印，改成渲染或局部更新页面，并且运用虚拟dom提高页面性能。从而实现数据变化时，页面自动局部刷新，也就是数据驱动视图。
