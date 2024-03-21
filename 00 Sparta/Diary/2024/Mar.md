# 2024年3月
## 1日
### 学習内容
- テストでfactoryを使うとき、stateを使うと個別の設定ができる。
- factoryでbelongs_to関連を作るとき
  ```
    'user_id' => User::factory(), で作る
  　　　　　// 以下にすると余計にユーザが作られる。
    'user_id' => User::factory()->create()->id, 
  ``` 
- 指定する場合は以下のような書き方ができる
  ```
  $taro = User::factory()->create(['name' => 'taro']);
  $posts = Post::factory(2)->for($taro)->create();
  ```
- 複数のユーザが複数のポストを持つとき
  ```
  $users = User::factory(3)->create()->each(function ($user) {
    Post::factory(random_int(2, 3))->for($user)->create();
  });
  ```
- DB には登録せずにモデルのインスタンスだけを返す ->make() というメソッドを利用して次のように書くことができます。
  ```
  // 例
  $post = Post::factor()->make();
  // Post は DB に登録されていないが、裏で User は DB に登録される等。
  ```
- ポイントとしては、->actingAs($user) の箇所です。この記述で、ログインした事にしてくれます。
- 

### コメント

## 2日
### 学習内容
- assertDatabaseCount のようにそれ専用のメソッドが用意されている時は、それ専用のメソッドを使った方が、テストに失敗した際により分かり易い（or デバッグし易い）情報が出力されたりもしますので、なるべくそれ専用のメソッドを使うと良いです。
- $post->fresh()->title のように fresh() が挟まっていますが、これは、DB から最新のデータを引っ張ってくる為のメソッドです。もしこれが無いと、Post::factory()->create() で生成した際のデータのままになってしまいます。そうするとテストの意味がありません。
- チェックする項目が多い時は、fresh() は、能率的ではありません。fresh() ではなく代わりに refresh() を使って下記のようにするとDB へのアクセスは一度で済みます。refresh() を使うと、自分自身（ここでは $post）が最新の DB 内容に置き換わります。
- 
### コメント

## 3日
### 学習内容
- Laravel
  - (36)PHPUnit には、そんな時の為にデータだけを別メソッドに用意し、そこで用意したデータを利用してテストを実行するという機能があります。それが、dataprovider（データプロバイダ）と呼ばれる機能です。
    - TestWith→1行目では、TestWith(["0"]) と指定しており、ここで指定した "0" が、テストメソッドのパラメータの $status に渡りテストが1回実行されます。そして2回目は、2行目に指定した TestWith(["1"]) の "1" が $status に渡り、またテストが実行されます。
      ```
      use PHPUnit\Framework\Attributes\TestWith; // ← 名前空間のインポートも忘れずに
  
      #[TestWith(["0"])]
      #[TestWith(["1"])]
      public function test_自分のブログは更新できますよ(string $status) // ← ここも確認
      {
          $validData = $this->validData([
              'status' => $status,
          ]);
  
          // 以下略
      }
      ```
  - (37)検索のテスト→ここは実装する時に再確認
  - (39)markTestIncomplete()→先にテストをしたい項目を列挙して、漏れがないようにして「未完成」ということを宣言することでテストの書き忘れを防げるかも。。
    ```
        public function test_他人様のブログの編集画面は開けない()
      {
          $this->markTestIncomplete();
  
          $this->markTestIncomplete('急用が入ったので一旦停止'); // コメント付き
      }
    ```
- Next.js
  - JSの構文復習
### コメント

## 4日
### 学習内容
- Laravel
  - (41)バリデーションテスト
    ```
    public function test_ブログ登録時の入力チェック()
    {
        $url = route('posts.store');

        $this->login();

        // 何も入力しないで送信した際は、リダイレクトされるのをまずは確認
        $this->post($url, [])->assertRedirect('/');

        // タイトルの入力チェック
        $this->post($url, ['title' => ''])->assertInvalid('title');
        $this->post($url, ['title' => ['aa' => 'bb']])->assertInvalid('title');
        $this->post($url, ['title' => str_repeat('a', 256)])->assertInvalid('title');

        // status の入力チェック
        $this->post($url, ['status' => ''])->assertInvalid('status');
        $this->post($url, ['status' => 'aa'])->assertInvalid('status');
        $this->post($url, ['status' => '3'])->assertInvalid('status');
    }
    ```
  - (42)ファイルのアップロードテスト→仮のストレージを活用してテストデータが蓄積されないように工夫
  - (43)CSVのテスト→業務で使うようになったら確認するといい。
- Next
  - useState
  - useRef
- Tailwind
### コメント

## 5日
### 学習内容
- Laravel
  - 単体テスト→GETとかPOSTなど行わずそのクラスが機能しているのかを確かめるテスト。
  - 例外のテスト→例外が発生することをテストするより、例外発生により起きる現象（メッセージが表示されるなど）をテストする方がいい。
  - 単体テストと機能テストの違い
- Next
  - useReducerはstate更新の仕組みまで定義できる。stateの管理は基本useStateでアプリの規模が大きくなったらuseReducerを使うといい。
  - useContextはグローバルで管理したい値を定義する。これによりpropsのバケツリレーをせずに済む。
  - useEffect
    - クリーンアップ関数の使い所
      ```
      useEffect(() => {
        console.log("mount");
    
        return () => {
          console.log("mount cleanup");
        };
      }, []);
      ```
  - useLayoutEffectの使い所→画面のチラつき、他のuseEfffectより先に実行したい場合？
  - Reduxは別で学習。理解できなかった。
### コメント

## 6日
### 学習内容
- Laravel
  - モック
- Next
  - レンダリング
  - useEffectはwindowが使えて、クッキーなど操作できる。
### コメント

## 7日
### 学習内容
- Laravel
  - 日付のテスト→Carbonを使うことで任意の日付をセットしてテストできる。
  - メールのテスト
- Next
  - useEffectでのクリーンアップ関数→アンマウント時に実行してメモリリークを抑える。
    ```
        useEffect(() => {
        const fetchRooms = async () => {
          const roomCollectionRef = collection(db, 'rooms');
          const q = query(roomCollectionRef,
            where('userId', '==', '7h9fpqRJUZdltBIo8psUuRK0TUo1'),
            orderBy('createdAt'));
          const unsubscribe = onSnapshot(q, (snapshot) => {
            const newRooms: Room[] = snapshot.docs.map((doc) => ({
              id: doc.id,
              name: doc.data().name,
              createdAt: doc.data().createdAt,
            }));
            setRooms(newRooms);
          });
          // クリーンアップ関数
          return () => {
            unsubscribe();
          }
        }
        fetchRooms();
      }
      , []);
    ```
- firebaseでデプロイ 
### コメント

## 8日
### 学習内容
- Next
  - useMemo→親コンポーネントの再レンダリングの際に子コンポーネントの不要なレンダリングを防ぐ。パフォーマンスの向上が狙える。
  - useCallback→useMemoの関数バージョン。
  - テストツール→Jest
  - TypeScript
- GitHub　flow
- 
### コメント

## 9日
### 学習内容

### コメント

## 10日
### 学習内容

### コメント

## 11日
### 学習内容
- Todoアプリ作成（ログイン）
### コメント

## 12日
### 学習内容
- Todo機能
### コメント

## 13日
### 学習内容
- デプロイ
### コメント

## 14日
### 学習内容
- googleでログイン
### コメント

## 15日
### 学習内容
- Laravel+Nextの復習
- paiza(PHP)
### コメント

## 16日
### 学習内容
- Tailwind
- Todoアプリの動画
### コメント

## 17日
### 学習内容
- じゃけぇさんのUdemy
### コメント

## 18日
### 学習内容
- Redux
  - storeでstateを保持。
  - reducerで更新。
  - useDispatchで値を送る。
  - useSelectorでUI上で値を取得して表示する。
  - Redux Toolkit
    - configureStore
    - createSlice
### コメント

## 19日
### 学習内容
- paiza
- レンダリング（Next)
- パフォーマンス
  - React.memoは頻繁にpropsが更新されるなら使うべきではない。
  - useCallbackはReact.memoの関数バージョン。カスタムフックを作成したら使うといいらしい。→ちょっと曖昧。
- Redux復習
- zustand
### コメント

## 20日
### 学習内容
- reduxをTodoアプリでお試し
### コメント

## 21日
### 学習内容
- zustandでTodo実装
### コメント

## 22日
### 学習内容

### コメント

## 23日
### 学習内容

### コメント

## 24日
### 学習内容

### コメント

## 25日
### 学習内容

### コメント

## 26日
### 学習内容

### コメント

## 27日
### 学習内容

### コメント

## 28日
### 学習内容

### コメント

## 29日
### 学習内容

### コメント

## 30日
### 学習内容

### コメント

## 31日
### 学習内容

### コメント
