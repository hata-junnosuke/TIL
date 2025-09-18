⏺ React Hook FormとShadcn/uiのForm実装について説明します。

⏺ Read(next/src/app/sign_in/SignInForm.tsx)
  ⎿  Error reading file

⏺ Search(pattern: "Form.*\.\.\.form", path: "next", glob: "**/*.tsx")
  ⎿  Found 5 files (ctrl+r to expand)

⏺ Read(next/src/features/auth/components/forms/LoginForm.tsx)
  ⎿  Read 100 lines (ctrl+r to expand)

⏺ React Hook Form と Shadcn/ui のForm実装について

  1. {...form} の意味

  <Form {...form}>

  これはスプレッド構文を使って、formオブジェクトのすべてのプロパティをFormコンポーネントに渡しています。

  useFormフックが返すオブジェクトには以下が含まれます：
  - control - フォームの制御オブジェクト
  - handleSubmit - フォーム送信処理
  - formState - フォームの状態（errors、isSubmittingなど）
  - register - フィールド登録関数
  - その他のメソッド（reset、setValue、watchなど）

  2. form.handleSubmit(onSubmit) の仕組み

  <form onSubmit={form.handleSubmit(onSubmit)}>

  handleSubmitは高階関数で、以下の処理を行います：

  1. イベント処理: event.preventDefault()を自動実行
  2. バリデーション: zodスキーマに基づく検証
  3. エラーハンドリング: バリデーションエラーがあれば送信を中止
  4. データ変換: フォームデータを型安全な形式に変換
  5. コールバック実行: バリデーション成功時のみonSubmitを実行

  3. control プロパティの役割

  <FormField
    control={form.control}
    name="email"
    render={({ field }) => (...)
  />

  controlオブジェクトは：
  - React Hook Formの中核となる制御システム
  - フォームの状態管理を担当
  - 各フィールドとReact Hook Formを接続
  - 内部的に以下を管理：
    - フィールドの値
    - バリデーション状態
    - エラーメッセージ
    - タッチ状態（フィールドが操作されたか）

  4. 全体の流れ

  // 1. フォームの初期化
  const form = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),  // Zodスキーマでバリデーション
    defaultValues: { email: '', password: '' }
  });

  // 2. Formコンポーネントにformオブジェクトを展開
  <Form {...form}>

  // 3. FormFieldでcontrolを使ってフィールドを制御
  <FormField
    control={form.control}  // React Hook Formとの接続
    name="email"            // フォーム内での識別子
    render={({ field }) => (
      // fieldには{ value, onChange, onBlur, ref, name }が含まれる
      <Input {...field} />
    )}
  />

  5. {...field} の内容

  FormFieldのrenderプロップが受け取るfieldオブジェクト：
  - value - 現在の値
  - onChange - 値変更ハンドラー
  - onBlur - フォーカス外れハンドラー
  - ref - DOM要素への参照
  - name - フィールド名

  これらがInputコンポーネントに自動的に適用され、React Hook Formによる状態管理が実現されます。
