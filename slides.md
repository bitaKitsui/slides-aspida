---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---


# Aspida
## -型安全にREST APIを-

---

# Aspidaとは?

<img src="/aspida.logo.png">

- TypeScriptフレンドリーなHTTPクライアントラッパー
- axiosとかfetchを使ってAPIとやり取りをするアレ
- 開発者が日本人!

<style>
img {
  width: 700px;
  margin-bottom: 20px;
}
</style>

---

# Aspidaの読み方

「アスピーダ」と読むらしい
<div class="container">
  <Tweet id="1488884328432308226" class="tweet" />
  <img src="/frourio.logo.png">
</div>

<style>
.container {
  display: flex;
  justify-content: space-between;
}

.tweet {
  width: 400px;
}

img {
  margin-top: 10px;
  width: 400px;
  height: 200px;
}
</style>

---

# そもそも何が問題？

<div grid="~ cols-2 gap-4">
<div>

<ul>
  <li>axiosの例</li>
  <li>GET: `/articles/:articleId/?user={userId}`</li>
</ul>

<p v-click="1">IDにタイトルを代入している</p>
<p v-click="2">article"s"のタイポ</p>
<p v-click="3">`{ params: { user: userId } }` が正解</p>
</div>

<div class="code">

```ts {all|6|9|10}
type Article = {
  id: number
  content: string
}

const articleId = 'sample title'
const userId = 2
const { data } = await axios.get<Article>(
  `/article/${articleId}`,
  { params: { userId } }
)
```

</div>
</div>

<h3 v-click="7">この辺りの型定義がデフォルトだとがっちりできない！</h3>

<style>
p {
  text-decoration: underline;
}

h3 {
  text-align: center;
  margin-top: 60px;
}

.code {
  width: 500px;
}
</style>

---
layout: center
---

<h1>
  <twemoji-cat-with-tears-of-joy />
</h1>
<div class="absolute top-12 left-30 text-3xl">タイポ気にするの面倒</div>
<div class="absolute top-17 right-12 text-3xl">なにかミスをしていないか</div>
<div class="absolute bottom-12 right-30 text-3xl">何か良い方法はないか。。</div>
<div class="absolute bottom-30 left-20 text-3xl">オレオレ関数も作るの面倒。。。</div>

---

# ここが便利: 型定義を自動生成
`api/articles/_articleId@number.ts`
- URLは`/articles/:articleId`
- アンダースコアから始まるパス変数「articleId」の型を「@number」と指定
- この型はデフォルトでは`number | string`


```ts
type Article = {
  id: number
  content: string 
}

export type Methods = {
  get: {
    query: {
      user: number
    }
  }
  resBody: Article
}
```

---

# ここが便利: 型補完
- 自動生成された型定義をもとにサクッと使用することが可能
- 使う側でタイポや型のミスなどを防げる

<video controls="controls">
  <source src="/code-example.mov">
</video>

<style>
video {
  margin-top: 40px;
}
</style>

---

# エコシステムも充実

<ul>
  <li><span>openapi2aspida</span>: Open APIやSwagger定義から自動型生成</li>
  <li><span>aspida-mock</span>: APIサーバーをモックできる</li>
  <li><span>frourio</span>: TypeScriptのフルスタックフレームワーク</li>
  <li><span>@aspida/swr</span>: SWR&#40;React Hooks&#41;ラッパー</li>
  <li><span>@aspida/swrv</span>: SWRV&#40;Vue Composition API&#41;ラッパー</li>
  <li><span>eslint-plugin-aspida</span>: Aspidaの定義にルール付け</li>
  <li><span>pathpida</span>: Next, Nuxt, Sapperで使うルーティングを型安全にする</li>
</ul>

<style>
span {
  color: #6998AB;
}
</style>

---

# openapi2aspida
Open APIやSwagger定義から自動型生成

```
$ npx openapi2aspida -i https://petstore.swagger.io/v2/swagger.json # or ../local-swagger.yaml
# api/$api.ts was built successfully.
```

あとはAspidaで使うだけ
```ts
import axiosClient from '@aspida/axios'
import api from "./api/$api"
import type { Pet } from './api/@types'

(async () => {
  const client = api(axiosClient())
  const petId = 100
  await client.pet._petId(petId).$get()
})()
```

---

# aspida-mock

<div grid="~ cols-2 gap-4">

<div>
  <ul>
    <li>Aspidaの型定義を利用してAPIをモックしてくれる</li>
    <li>サーバー不要、ブラウザのみで動作</li>
  </ul>
</div>

<div>

```ts
type Article = {
  id: number
  content: string 
}

export type Methods = {
  get: {
    query: {
      user: number
    }
  }
  resBody: Article
}

export default mockMethods<Methods>({
  get: ({ query }) => {
    status: 200,
    resBody: {
      id: query.user,
      content: 'mock content'
    }
  }
})
```

</div>

</div>



---

# まとめ
- ファイルシステムベースで直感的に型定義ができるのが便利！
- ユースケースはまだまだなので、「あれしたい、これしたい」に必要な情報へのアクセスは難しい
- 今後に期待なライブラリ！
