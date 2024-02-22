# 2024年2月
## 1日
### 学習内容

### コメント

## 2日
### 学習内容

### コメント

## 3日
### 学習内容

### コメント

## 4日
### 学習内容

### コメント

## 5日
### 学習内容
- 自作アプリのドメイン取得
- ACMを使ってSSL証明書を取得
### コメント
- 久しぶりにやった。着実にやってよく理解しておこう！！
## 6日
### 学習内容
- バックエンドのデプロイ準備
  - VPC,RDS
  - Railsの設定
### コメント
- サブネットマスクなどぼんやりなので、数をこなして反復で覚えていきたい。
- セキュリティグループとサブネットグループがごっちゃになる。
　　- セキュリティグループ→アプリやDBのインバウンドとアウトバウンドを設定する。「誰を家に入れるか、どこに家から出すのか」
 - サブネットグループ→作成した複数のサブネットをまとめてグループの概念を形成し、グループの単位何らかの機能や役割を付与するものになります。今回は２つのプライベートサブネットをRDSと接続するために設定する。 
## 7日
### 学習内容
- backendのデプロイ
### コメント
- 沼っております。ヘルスチェックで301が返される。
## 8日
### 学習内容
- laravelの学習に力を入れるため、AWSを撤収（再開することも検討、ドメインは保持）
### コメント
- laravel頑張って、結果出して、稼ぐぞ！
## 9日
### 学習内容
- php2章まで
### コメント
- 素のphpでフォーム作成まで動画をみた。
- コード書くのはlaravelからでいいかな。
## 10日
### 学習内容

### コメント

## 11日
### 学習内容
- laravel学習開始
  - MVCの配置、作成方法
  - laravel tinkerはrails c
  - Bladeはerb
### コメント
- Railsとそんな変わらない。
## 12日
### 学習内容

### コメント

## 13日
### 学習内容
- laravel学習
  - ルーティング
    ```
    php artisan make:controller ContactFormController --resource→リソースでアクションを一括でコントローラに設定

    Route::prefix('contacts')
    ->middleware(['auth']) //認証してなければアクセスできない。
    ->controller( ContactFormController::class )
    ->name('contact.')
    ->group(function(){
     Route::get('/', 'index')->name('index'); //view側でリンクを貼るときに便利（らしい）。
    });
    
    ```
  - Bladeコンポーネント（$slot） 
### コメント

## 14日
### 学習内容
- ルーティング
  ```
  Route::prefix('contacts')->middleware(['auth'])
   ->controller( ContactFormController::class )
   ->name('contacts.')
   ->group(function(){
    Route::get('/', 'index')->name('index');
    Route::get('/create', 'create')->name('create');
    Route::post('/', 'store')->name('store');
    Route::get('/{id}', 'show')->name('show');
    Route::get('/{id}/edit', 'edit')->name('edit');
   });
  ```
  ```
  GET|HEAD  contacts ....................................... contacts.index › ContactFormController@index
  POST      contacts ....................................... contacts.store › ContactFormController@store
  GET|HEAD  contacts/create .............................. contacts.create › ContactFormController@create
  GET|HEAD  contacts/{id} .................................... contacts.show › ContactFormController@show
  ```
  
### コメント
- storeがRailsのcreate,createがnewにあたる。注意！
- フォームの遷移先やリンクの貼り方再確認しよう!
## 15日
### 学習内容
- CRUDのedit,update,destroy
- サービスでアルゴリズム作成
- バリデーション（Requestを作ってruleを設定）
- old関数でフォームの入力を保持させる
- seed作成（Seeder, Factory）
### コメント
バリデーションはビューで項目ごとに設定すると記述量が多くて大変だ。
## 16日
### 学習内容
- ページネーション(コントローラでpaginateして、viewでlinksを使えばできる、日本語化もvendorいじればできる)
- 検索（モデルでscope作ってコントローラで絞り込めばおっけ）
### コメント
laravelは簡単にページネーションや検索を設定できていいかも。
## 17日
### 学習内容
- Next
  - apiをフロントで呼び出す時にkeyがブラウザから取得されないようにapiフォルダでapiを叩くようのファイルを作成してあげる。
  - 環境変数でNEXT_PUBLICをつけるとフロントで使えるようになる。ただし、秘匿情報はつけないでおくべし。
  - stateの取得状況を確認するときは、コンソールではなくて、React developer toolのcomponentで確認するとみれる。
  - Swiper・・画像をスライドさせるような動作を導入するライブラリ。
  - SSRはaxiosで取得してreturnにデータを渡すことで一つのファイルで完結。
### コメント

## 18日
### 学習内容
- 非同期通信はasync awaitを使うが関数でくくる必要がある。
```
 useEffect(() => {
    const fetchMedia = async() => {
      try {
        const response = await axios.get(`api/searchMedia?searchQuery=${searchQuery}`);
        console.log(searchQuery);
      } catch (err){
        console.log(err);
      }
    }
  },[searchQuery])
```
- filterメソッド→条件で絞り込み
  ```
   const validResults = searchResults.filter((item) => {
    return item.media_type === 'movie' || item.media_type === 'tv';
   });
  ```
- レスポンス対応
　　 ```
   xsなら12,mdなら9
   <Grid item xs={12} md={9}>
  ```
- try,catchの後、どちらでも使用するfinally
  ```
  try {
      const response = await axios.get(`api/searchMedia?searchQuery=${searchQuery}`);
      // console.log(response);
      const searchResults = response.data.results;
  
      const validResults = searchResults.filter((item) => {
        return item.media_type === 'movie' || item.media_type === 'tv';
      });
      // console.log(validResults);
      setResults(validResults);
  
    } catch (err){
      console.log(err);
    } finally {
      setLoading(false);
    }
  ```
- Laravelのマイグレーションファイルでのdependent_destroy
  ```
  $table->foreignId('user_id')->constrained('users')->onDelete('cascade');

  これは、Laravelのマイグレーションファイル内で外部キー制約を定義するための記述です。

    $table->foreignId('user_id')は、user_idという名前の外部キーを定義しています。これは、usersテーブルのidカラムを参照します。
    
    ->constrained('users')は、外部キーがusersテーブルのidカラムを参照することを示しています。これにより、外部キー制約が追加されます。
    
    ->onDelete('cascade')は、親テーブルのレコードが削除された場合に、それに関連する子テーブルのレコードも一緒に削除されることを示しています。つまり、usersテーブルの特定のユーザーが削除された場合、関連するすべてのuser_idを持つレコードが自動的に削除されます。
    
    このように、外部キー制約を使用することで、データの整合性を保ちつつ、データベース内の関連付けを行うことができます。
  ```
- Laravel関連づけ
  ```
      class Review extends Model
      {
          use HasFactory;
      
          public function user()
          {
              return $this->belongsTo(User::class);
          }
      }

  ```
  ```
  public function reviews()
    {
        return $this->hasMany(Review::class);
    }
  ```
- 
### コメント

## 19日
### 学習内容
- 投稿機能作成
### コメント

## 20日
### 学習内容
- `<Button color="error" onClick={() => handleDelete(review.id)}>削除</Button>`のようにアロー関数にするのは？
- ルートモデルバインディングっていうらしい。これでidで検索してから削除ではなく、直で削除できるみたい。
  ```
  public function destroy(Review $review)
    {
        $review->delete();

        return response() ->json(['message'=> '正常にレビューを削除しました。']);
    }
  ```
- 削除の関数
  ```
  const handleDelete = async (id) => {
    if(window.confirm('削除しますか？')){ // 確認ダイアログ
      try {
        await laravelAxios.delete(`/api/review/${id}`);
        const filteredReviews = reviews.filter((review) => review.id !== id); // 削除したレビュー以外を抽出
        setReviews(filteredReviews);
        updateAverageRating(filteredReviews);
      } catch (err) {
        console.log(err);
      }
    }
  }
  ```
- 
### コメント

## 21日
### 学習内容
- routerでqueryを取得してuseEffectで活用する
  ```
  const router = useRouter();
  const { revviewId } = router.query; // URLからreviewIdを取得

  useEffect(() => {}, [reviewId]) // reviewIdが変更されたら実行
  ```
- フォームで画面遷移を防ぐのと空欄の送信を禁じる
  ```
  const handleCommentAdd = async(e) => {
    // ページ遷移を防ぐ
    e.preventDefault();
    // 空白文字を削除
    const trimmedContent = content.trim();
    if(!trimmedContent) {
      return;
    }
  ``` 
- 登録できるようにModelにfillableを設定することを忘れがち。
### コメント

## 22日
### 学習内容
- モデル、マイグレーション、コントローラを同時に作成
  ```
      php artisan make:model Comment -mcr コマンドは、Laravel アプリケーションで Comment モデルを作成し、それに関連するマイグレーションファイル、コントローラー、およびリソースコントローラーのアクションを含むコントローラーを生成します。このコマンドのオプションは以下の通りです：
    
    - -m はマイグレーションファイルを生成します。
    - -c はコントローラーを生成します。
    - -r はリソースコントローラーのアクションを含むコントローラーを生成します。（コントローラ内のindexメソッドなどのメソッドが設定されていない。手動で設定する。）
  ```
- 組み合わせの重複を許可しないモデルの作成
  ```
  Schema::create('favorites', function (Blueprint $table) {
      $table->id();
      $table->string('media_type');
      $table->bigInteger('media_id');
      $table->foreignId('user_id')->constrained('users')->onDelete('cascade');
      $table->unique(['media_type', 'media_id', 'user_id']);// 重複を許さない
      $table->timestamps();
  });
  ```
- ２つのapiを同時に呼び出す方法
  ```
  useEffect(async()=> {
    const fetchReviews = async () => {
      try {
        const [reviewResponse, favoriteResponse] = await Promise.all([
          laravelAxios.get(`/api/reviews/tv/236000`),
          laravelAxios.get(`/api/favorites/status`),{
            // getの場合はparamsを使う
            params: {
              media_type: "tv",
              media_id: 236000
            }
          }
        ]) 
        const fetchReviews = reviewResponse.data;
        setReviews(fetchReviews);
        updateAverageRating(fetchReviews);
        console.log(favoriteResponse);
        setIsFavorite(favoriteResponse.data);
      } catch (err) {
        console.log(err);
      }
    }
  ``` 
- 
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
