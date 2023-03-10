---
title: next
tags: 
  - next
  - 文档
categories: 
  - next
  - next学习
---

## next

### 渲染
   渲染分为静态生成和服务端渲染，两者的区别
   1. 静态渲染是再构建时就生成对应的html页面，并且页面的请求是可以被cdn缓存的，这样无需重复发送请求
   2. 服务端渲染，每次请求的时候都会重新请求页面，重新进行渲染。重新生成html。
   3. getServerSideProps和getStaticProps类似都是用于页面数据渲染提供额外的数据。但是不同的是getServerSideProps只有在请求页面的时候才会渲染执行，在构建的时候并不会执行。

   





