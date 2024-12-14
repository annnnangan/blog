---
title: 在package.json裡面加入custom command來快速執行指令
published: 2024-12-14
description: ''
image: ''
tags: [package.json]
category: 'package.json'
draft: false 
lang: 'zh-hk'
---
## 背景

在做side project的時候，經常需要把seed data 刪除，然後重新建立，藉此來重置database的data。之前的做法是，manually在terminal打下面這3個command，非常麻煩，而且經常不記得。所以在思考到底有沒有快一點、方便一點的方法呢。

```bash
yarn knex migrate:rollback //撤掉所有的database table
yarn knex migrate:latest //重新建立所有的database table
yarn knex seed:run //重新加入seed data
```

## 在package.json裡面加custom commands

後來我發現原來我們可以透過在package.json裡面的scripts那個部分加custom commands就可以直接在terminal打一句的command，就可以把一次執行所有的command。平常我們可能在Next.js 的project的時候可以透過`yarn dev` 或者 `npm run dev` 來打開server也是一樣的原理。

所以我在scripts那邊多加了一行，`"data": "yarn knex migrate:rollback && yarn knex migrate:latest && yarn knex seed:run”`。

![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/add-custom-commands-in-packagejson%2Fimage.png?alt=media&token=2df110ab-14f3-40c2-8b77-a091d001e7a6)

然後當我在terminal裡面打 `yarn data` 就可以立馬執行上面這3個command，非常方便、省時間。

![image.png](https://firebasestorage.googleapis.com/v0/b/testing-c9537.appspot.com/o/add-custom-commands-in-packagejson%2Fimage%201.png?alt=media&token=c5922971-a852-4f85-b244-231a49b6a7bf)