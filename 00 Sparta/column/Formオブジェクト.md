# Formオブジェクトとは
qiitaの記事によると<br>
>Formオブジェクトとは？
1つのフォーム送信で複数のモデルを操作したい場合や、テーブルに情報を保存しない情報に対するバリデーションを行いたい場合使うデザインパターンのこと。

今回の課題に当てはめて考えると<br>
`全ての投稿を検索対象とすること（フィードに対する検索ではない）`が要件になっていて、<br>
**複数のモデル（post,user.comment）を検索フォームの送信を用いて操作（検索）したい**のでFormオブジェクトを使うことが適切であると考えられます。

それでは次は実際のコードを考察してみます。

app/forms/search_posts.rb
```
class SearchPostsForm
  # ActiveModelのModelモジュールを導入しています。これで、ActiveRecordの機能が使えるようになります。
  include ActiveModel::Model
  # attributeメソッドが使えるようになります
  include ActiveModel::Attributes

  # post_content、comment_content、nameをstring型の属性として定義
  attribute :body, :string
  attribute :comment_body, :string
  attribute :username, :string

  def search
    scope = Post.distinct # ここでダブりのコメントがあれば2件となるのを防ぐ。
    scope = splited_bodies.map { |splited_body| scope.body_contain(splited_body) }.inject { |result, scp| result.or(scp) } if body.present?
    # コメント検索欄が埋まっていれば、Postの中で.00_containに当たるものを取ってくる
    scope = scope.comment_body_contain(comment_body) if comment_body.present?
    scope = scope.username_contain(username) if username.present?
    scope
  end

  private

  def splited_bodies
    # 前後の空白をなくして、間の空白で区切って配列を作っている
    body.strip.split(/[[:blank:]]+/)
  end
end

```
- #### `include ActiveModel::Model`
これにより、SearchPostsFormがあたかもmodelと同じような機能を使えるようになります。いわば、model(仮)

- #### ` include ActiveModel::Attributes`
これにより、ここだけのカラムを作成できる。いわば、カラム（仮）<br>
なお、設定方法は
```
  attribute :body, :string      # bodyカラム（仮）を作成、以下同じ
  attribute :comment_body, :string
  attribute :username, :string
```
あとはモデルと同じようにメソッドを書くこともできます。

#### このほかに使うと適切な場面としては
- **サインアップ処理**: ユーザの作成と他ユーザのフォローを同時に行うなど、複数のActiveRecordモデルを作成する複数のタグを作成: 複数の子レコードを作成するとき。
- **accepts_nested_attributes_forを使うような場面だけど使いたくないとき**
- **ブログの検索フォーム**: Elasticsearchへのリクエストなど、ActiveRecordモデルを使用しない場合

>https://qiita.com/kaito121855/items/333f13f61bc167feff92 <br>
>https://applis.io/posts/rails-design-pattern-form-objects
