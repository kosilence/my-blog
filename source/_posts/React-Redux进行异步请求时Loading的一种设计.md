---
title: React Redux 进行异步请求时 Loading 的一种设计
date: 2018-01-30 11:28:57
desc: React Redux Async Request Loading Design
---

思考，在 `React & Redux` 应用中，当需要异步请求时，我们时常使用 `redux-api-middleware` 或者类似的中间件来帮助我们自动生成的 `FETCH_DATA_SUCCESS` or `FETCH_DATA_FAIL`，避免写很多繁琐的 `action creator`。

但从 UI 层面来看，我们经常性的需要 `Loading` 之类的组件进行过渡，即：请求开始时，Show Loading；请求返回成功或者失败时，Hide Loading。

这导致了我们仍然需要在 `reducer` 中分出一部分精力关注在 fetch 之后 `Loading` 的显示问题上，手动的处理每个请求完成之后的 UI 切换，这件事显然是不合理的。

<!--more-->

## 痛点

在使用 React && Redux 过程中，大量的模板文件会是一个很大的困扰，尤其是当需要发起异步请求的时候，如果你使用过 `redux-thunk`，你一定深有感触。

还好，我们可以寻找一些更简洁的 package 来完成相同的工作，例如 `redux-promis`、`redux-api-middleware`、`redux-axios-middleware` 等等，本文以 `redux-axios-middleware` 来实现异步请求处理。

对于异步请求，常见的 UI 处理就是添加一个 Loading 状态，请求结束时，隐藏 Loading 状态，上述过程在 `Redux` 中用 `action` 来描述：

1. 发起请求：`dispatch FETCH_DATA`
2. 显示加载：`dispatch SHOW_LOADING`
3. 接收请求：`dispatch FETCH_DATA_SUCCESS || FETCH_DATA_FAIL`
4. 隐藏加载：`dispatch HIDE_LOADING`

如果在 `reducer` 中处理这个 `loading` 值的变化，那我们的代码将会是这个样子的：

```js
export default (state = { ...initialState }, action) => {
  switch (action.type) {
    case "FETCH_DATA":
      return { ...state, loading: true };
    case "FETCH_DATA_SUCCESS":
      return { ...state, loading: false, data: action.data };
    case "FETCH_DATA_FAIL":
      return { ...state, loading: false };
    default:
      return state;
  }
};
```

而如果我们...

---

这篇文章写在一年之前，但是遗憾的是只写了个开头，现在再打开来看，完全忘记了当初的想法和思路，完蛋玩意。。。
立个 FLAG： 坚持更新！
