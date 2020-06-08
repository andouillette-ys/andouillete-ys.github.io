---
title: 2020年编写React组件(hooks)的五个常见错误
date: '2020-06-07'
---

原文链接: [Five common mistakes writing react components (with hooks) in 2020](https://www.lorenzweiss.de/common_mistakes_react_hooks/)

### `本文是关于我发现的编写React组件时最常见的错误，为什么它们是错误以及如何避免或修复它们。`

![](./fish.jpg)

# 作为框架的React

React出现在在Web开发领域已经有一段时间了，近年来它作为敏捷Web开发工具的地位稳步增强。 特别是在宣布并发布了新的[hook API/概念](https://reactjs.org/docs/hooks-state.html#hooks-and-function-components)之后，编写组件从未如此简单。

尽管React背后的团队和庞大的社区力量试图以令人印象深刻的方式培训和解释框架的概念，但我仍然看到使用该框架时遇到的一些陷阱和常见错误。 我记录了过去几年中我看到的所有与React相关的错误，尤其是React Hooks相关的。 在本文中，我想向您展示最常见的错误，并且还将尝试详细解释为什么我认为它们是错误，并提出了以更简洁的方式进行编码的建议。

## 免责声明

在我们开始之前，我必须说明下面列出的大多数事情都不是根本性的错误或者乍一看甚至看不出错误，而且大多数也不大可能影响应用的性能或外观。 除了从事该产品的开发人员外，也许没人会注意到这里有些问题，但是我仍然相信，高质量的代码可以带来更好的开发体验，从而带来更好的产品。

与任何软件框架或库一样，对一个问题总有数百万种不同的意见。 您在此处看到的所有内容都是基于我的个人观点，不应视为一般规则。 如果您对此有不同的看法，我洗耳恭听。🌟

# 1. 在不需要重新渲染的地方使用useState

React的核心概念之一是处理状态。 您可以通过状态控制整个数据流和渲染。 每次重新渲染树时，它很可能与状态变化有关。

通过`useState hook`，现在可以在函数组件中定义状态，这是一种在React中处理状态的非常整洁而简便的方法。 但正如我们在以下示例中看到的那样，它也可能被错误使用。

对于下一个示例，我们需要一些说明，假设我们有两个按钮，一个按钮是计数器，另一个按钮使用当前计数发送API请求或触发操作。 不过，当前计数永远不会在组件内展示。 仅当单击第二个按钮时才需要该计数来发送API请求。

### 这很危险 ❌

```jsx
function ClickButton(props) {
  const [count, setCount] = useState(0);

  const onClickCount = () => {
    setCount((c) => c + 1);
  };

  const onClickRequest = () => {
    apiCall(count);
  };

  return (
    <div>
      <button onClick={onClickCount}>Counter</button>
      <button onClick={onClickRequest}>Submit</button>
    </div>
  );
}
```

### 问题所在 ⚡

乍一看，您可能会问这到底有什么问题？ 不正是这样维护状态吗？是的您是对的，上面的代码可以正常地运行并且可能永远不会有问题，但是在React中，每次状态更改都会对该组件及其子组件进行重新渲染。但是在上面的示例中我们从未在组件渲染中使用该状态，所以每次设置计数器时都会导致不必要的渲染，这可能会影响性能或产生意外的副作用。

### 解决办法 ✅

如果要在组件内部使用一个变量，并且在不同的渲染中保持该变量的值，同时变量值的改变又不会强制组件重新渲染，则可以使用`useRef hook`。 它可以保持变量值，但不会强制重新渲染组件。
```jsx
function ClickButton(props) {
  const count = useRef(0);

  const onClickCount = () => {
    count.current++;
  };

  const onClickRequest = () => {
    apiCall(count.current);
  };

  return (
    <div>
      <button onClick={onClickCount}>Counter</button>
      <button onClick={onClickRequest}>Submit</button>
    </div>
  );
}
```