# create-next-app+Supabase setup
この項目は動画で初期設定を行うだけ
## Supabase （https://supabase.com/）

### Supabaseとは
Supabaseは、オープンソースのFirebase代替として注目を集めているBaaSです。
PostgreSQLデータベースをベースに、リアルタイム更新、認証、ストレージ、サーバーレス関数など、フルスタック開発に必要な機能が提供されています。
> 【Supabase入門】認証・DB・リアルタイムリスナーを使ってチャットアプリを作ろう
> https://zenn.dev/chot/articles/ddd2844ad3ae61

# Root segments + file conventions
## 構造の確認
appからツリー構造になっている。
subtreeの一番上がRoot segmentと呼ばれる。
![スクリーン ショット 2023-04-29 に 21 49 52 午後](https://user-images.githubusercontent.com/81806676/235303324-d2f73435-18c7-4a0e-9a9c-45810770aff0.png)
page.tsxをラップする形でSuspenseとErrorBoundaryが存在することでloading.tsxやerror.tsxが機能する。
またlayout.tsxも同様でpage.tsxに影響を与えている。
![スクリーン ショット 2023-04-29 に 21 49 40 午後](https://user-images.githubusercontent.com/81806676/235303333-950b0c3f-6e5c-4583-a517-7497134bf802.png)

## appフォルダーの準備
1. pageフォルダーを削除してappフォルダーを作成する
2. appフォルダーの中にpage.tsxを作成
    ```
    export default function Page() {
      return (
        <main>
          <div className="m-10 text-center">Hello World🚀</div>
        </main>
      )
    }
    ```

3. layout.tsxを作成→全体のスタイルを書き込む
    ```
    import '../styles/globals.css'

    export default function RootLayout({
      // ここでのchildrenは、app/page.tsxのreturnの中身が入る
      children,
    }: {
      children: React.ReactNode
    }) {
      return (
        <html>
          <body>
            {children}
          </body>
        </html>
      )
    }
    ```

4. head.tsxの作成→ヘッダーの内容を書き込む

## components作成
1. componentsフォルダー作成
2. navbar作成
  Linkの作成
  ```
  import Link from 'next/link'

export default function NavBar() {
  return (
    <header className="bg-gray-800 p-4">
      <nav className="space-x-4">
        <Link
          href="/"
          className="rounded bg-gray-700 px-3 py-2 text-white hover:bg-gray-500"
        >
          Home
        </Link>
        <Link
          href="/blogs"
          className="rounded bg-gray-700 px-3 py-2 text-white hover:bg-gray-500"
        >
          Nested Layout with Blogs
        </Link>
        <Link
          href="/streaming-sr"
          className="rounded bg-gray-700 px-3 py-2 text-white hover:bg-gray-500"
        >
          Streaming SR
        </Link>
        <Link
          href="/auth"
          className="rounded bg-gray-700 px-3 py-2 text-white hover:bg-gray-500"
        >
          Auth with CRUD
        </Link>
      </nav>
    </header>
  )
}

  ```
3. layout.tsxでnav-barをimportする。

# Data fetch in server component
fetchの方法
- SSG→force-cacheで静的（デフォルト）
- SSR→no-storeで動的
- ISR→時間指定
![スクリーン ショット 2023-04-29 に 23 09 05 午後](https://user-images.githubusercontent.com/81806676/235307226-5b59861a-ad94-46ea-84e3-a33bd896745d.png)


# Server component and client component
## LoadingとError
Loading、Errorを入れると自動的にNextが読み込んでくれる。
![スクリーン ショット 2023-04-30 に 08 12 58 午前](https://user-images.githubusercontent.com/81806676/235327749-a0c4a21a-0701-4daa-8ae9-2ae3124415b7.png)
### Error
Errorはサーバーのエラーをクライアントで表示するため`use client`を記述する。これによりerror.tsxはクライアントコンポーネントになる。
```
//クライアントコンポーネント
'use client'

export default function Error({ error }: { error: Error }) {
  return (
    <div>
      <p className="mt-6 text-center text-red-500">
        Data fetching in server failed
      </p>
    </div>
  )
}

```

## サーバーとクライアントコンポーネントの特徴と使い分け
- 左がサーバーの右がクライアントの特徴
![スクリーン ショット 2023-04-30 に 08 27 45 午前](https://user-images.githubusercontent.com/81806676/235328136-9ec6cc59-e870-457e-9663-4f50376f25e7.png)
### サーバーを使用すべき場合
- fetch（データを取得）する場合
- シークレットキーを使う場合
- npmのパッケージはサーバーで使う→読み込みを早くできる
### クライアントを使用すべき場合
- イベントリスナーを使う場合
- useEffectを使う場合
- ブラウザで使うAPIがある場合
![スクリーン ショット 2023-04-30 に 08 30 41 午前](https://user-images.githubusercontent.com/81806676/235328197-c5bb784d-efe5-4a83-9147-9058038386c9.png)

## 混在する場合の注意
クライアントの中ではサーバーを使えないが、サーバーの中でクライアントを使うことはできる！

### サーバーの読み込みを別枠で実行（streaming HTML）
NotesListが読み込むのに時間がかかっている
Suspenseで囲むことでレンダリングを待たずしてそのほかを表示できる
```
import NotesList from "./components/notes-list";
import TimerCounter from "./components/timer-counter";
import { Suspense } from "react";
import Spinner from "./components/spinner";

export default function Page() {
  return (
    <main>
      <div className="m-10 text-center">Hello World🚀</div>
      {/* Suspenseで囲むことでレンダリングを待たずしてそのほかを表示できる */}
      <Suspense fallback={<Spinner color="border-green-500" />}>
        {/* ベータ版のため指摘がvscode上で表示されるので以下のコメントで回避する */}
        {/* @ts-ignore */}
        <NotesList />
      </Suspense>
      {/* クライアントコンポーネント */}
      <TimerCounter />
    </main>
  )
}
```

## router.refresh
useStateの値を保持したまま、fetchしてサーバーから情報を持ってくることができる
useRouterはクライアントでしか使えないので注意

# Revalidation frequency + Segment level cache options
## セグメントレベルでレンダリング方法を選択する
- revalidateを設定してあげるとセグメント全体のレンダリングを選択できる
- revalidateを複数指定すると最小のものが適応される
  - ファイルが異なっていてもツリー構造の中で最小のものが適応されることに注意
![スクリーン ショット 2023-04-30 に 09 01 56 午前](https://user-images.githubusercontent.com/81806676/235328965-6ae12c40-2607-4078-95be-cb88e1b9cdd0.png)
![スクリーン ショット 2023-04-30 に 09 06 14 午前](https://user-images.githubusercontent.com/81806676/235329059-0b8bb121-6177-464d-8b4f-e6607e302fca.png)

# Nested layout
階層上にlayoutを重ねて表示させることができる。
解説はこれくらいでいいでしょう

# Dynamic segment + generateStaticParams
- [blogId]を用いてダイナミックルーティングを作る
- 詳細ページを静的に事前にレンダリングしたい場合はgenerateStaticParamsを使う


# Streaming HTML
レンダリングの時間が異なるコンポーネントを扱うときに使う
読み込みに時間がかかるコンポーネントをSuspenseで囲むことでそれ以外のコンポーネントを先に表示して、その後囲んだコンポーネントを表示させることができる
（処理がかかるコンポーネントが全体の足を引っ張らなくて良くなる）
```
// ダイナミックレンダリングを有効化する→SSR
export const revalidate = 0

import { Suspense } from 'react'
import BlogList from '../components/blog-list'
import NewsList from '../components/news-list'
import Spinner from '../components/spinner'

export default function StreamingServerRenderingPage() {
  return (
    <section className="flex">
      <aside className="w-1/4">
        <section className="fixed m-1 h-full w-1/4 border border-blue-500 bg-gray-200 p-1">
          <Suspense fallback={<Spinner color="border-green-500" />}>
            {/*@ts-ignore*/}
            <BlogList />
          </Suspense>
        </section>
      </aside>
      <main>
        <section className="fixed w-3/4">
          <Suspense fallback={<Spinner />}>
            {/* @ts-ignore*/}
            <NewsList />
          </Suspense>
        </section>
      </main>
    </section>
  )
}
```

# Supabase auth in server component
以下つくる
![スクリーン ショット 2023-04-30 に 10 39 54 午前](https://user-images.githubusercontent.com/81806676/235331453-d71d610c-3766-485b-a364-4b89a2b2136b.png)
クライアントとサーバーのトークンをどうやって同期させるのか
![スクリーン ショット 2023-04-30 に 10 40 52 午前](https://user-images.githubusercontent.com/81806676/235331473-e9aa0e77-fc3f-44e2-8fd8-2bdca436dbc7.png)

memo
内容は深掘りしたいがsupabaseがちょっとニッチな感じがするから余裕があればまとめる

# CRUD + middleware
これつくる→動画見てやってみよう
![スクリーン ショット 2023-04-30 に 11 29 04 午前](https://user-images.githubusercontent.com/81806676/235332681-390aaf7c-9638-4c3a-a415-6977de5b0ec8.png)




