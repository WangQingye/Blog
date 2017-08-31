---
title: vue-router的几种路由方式
date: 2017-8-31
tag: [框架学习,vue,vue-router]
---

##  路由

路由是根据不同的url地址展示不同的内容或页面。

### 前端路由

把原本属于后端的路由工作交由前端来做。

后端路由是通过url地址返回不同的html文件

前端路由始终只有一个页面，也就是俗称的Single Page Application (SPA)。

优点：用户体验好，减少请求。

缺点：不利于SEO，不利于缓存，无法记录访问流程（前进，后退等功能）。

## vue-router的几种路由方式

1. ### 动态路由匹配

   |              模式               |          $rouer.params           |        匹配路径         |
   | :---------------------------: | :------------------------------: | :-----------------: |
   |        /user/:username        |        {username: 'evan'}        |     /user/evan      |
   | /user/:username/post/:post_id | {username: 'evan', post_id: 123} | /user/evan/post/123 |

简单举例，通过不同的商品ID来访问不同的商品信息：	

```javascript
mode: 'history' || 'hash' (区别是地址后是否有'#'号)
routes: [
  	{
      	path: '/goods/:goodId',
      	name: 'GoodList',
      	component: GoodList
  	}
]

/**
* 在组件中可以通过$route.params.goodId获得参数
*/
```

2. ### 嵌套路由

   在路由组件中再次添加路由

```javascript
mode: 'history' || 'hash' (区别是地址后是否有'#'号)
routes: [
  	{
      	path: '/goods/',
      	name: 'GoodList',
      	component: GoodList,
      	children:[
          	{
              	path: 'title',
              	name: 'title',
              	component: Title
          	}
      	]
  	}
]
```

```html
  /**
  * 在GoodList组件中需要加入router-view
  */
  <router-link to="/goods/title">展示标题</router-link>
```

3. ### 编程式路由

   直接通过代码跳转：

```javascript
this.$router.push({path: '/cart?goodsId=123'})
```

```html
<router-link v-bind:to="{ name:'cart', params: {cartId:123} }">
```

4. ### 值得一提

   vue-router中还提供了一种方式叫视图命名，即在同一个组件中可以通过name标识创建多个router-view，因为使用场景不多故略过介绍，仅此一提。