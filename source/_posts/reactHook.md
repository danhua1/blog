---
title: react Hook
---

## Hook概览
### Effect Hook
在react 组件中执行数据请求，dom修改，事件订阅等操作，统称为副作用。

### Hook使用规则
hook 本质就是一个JavaScript函数，但是在react中使用有两个额外的规则
1. 不要在循环，条件判断，子函数中使用hook。拿最主要的useState来说，如果在条件判断中使用useState，那么后续更新会找不到对应更新的数据。会导致数据的错乱。更新是按照顺序来的。
2. 只可以在react函数组件中使用hook。还有一个就是在自定义的hook中使用。

## 使用 state Hook
### 使用多个state
正常我们会把每个的state单独赋值，单独管理。这样如果state多的话，会声明多个state。state同时也是支持声明对象的。这样可以把多个state合并成一个对象或者数组进行更新。
useState和setState更新数据的方式是不一样的。前者是直接替换。而或者是合并进行更新。


