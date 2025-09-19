> useTransition　を解説してくれますか？

⏺ useTransitionについて解説します。

  useTransitionとは

  React 18で導入された並行機能（Concurrent Features）の一つで、UIの応答性を保ちながら重い状態更新を処理するためのフックです。

  基本的な使い方

  const [isPending, startTransition] = useTransition();

  // isPending: トランジション中かどうかのboolean
  // startTransition: トランジションを開始する関数

  今回の実装での使用例

  // ForgotPasswordFormの例
  const [isPending, startTransition] = useTransition();

  const onSubmit = async (data: ForgotPasswordFormData) => {
    setMessage('');
    setError('');

    startTransition(async () => {  // 非同期処理をトランジションでラップ
      try {
        const response = await fetch(/* API呼び出し */);
        // ...処理
      } catch {
        setError('エラーが発生しました');
      }
    });
  };

  // ボタンでisPendingを使用
  <Button disabled={isPending || form.formState.isSubmitting}>
    {isPending ? '送信中...' : 'リセットメールを送信'}
  </Button>

  なぜuseTransitionを使うのか

  1. 優先度の管理

  // ❌ 従来の方法
  const [loading, setLoading] = useState(false);
  const handleSubmit = async () => {
    setLoading(true);  // UIがブロックされる可能性
    await heavyOperation();
    setLoading(false);
  };

  // ✅ useTransitionを使用
  const [isPending, startTransition] = useTransition();
  const handleSubmit = () => {
    startTransition(async () => {
      await heavyOperation();  // 低優先度として処理
    });
  };

  2. ユーザー入力の優先

  - トランジション中でも、ユーザーの入力やクリックなどは高優先度として即座に処理
  - フォーム送信処理中でも、キャンセルボタンや他の入力への反応が良い

  3. 自動的な状態管理

  // isPendingが自動的にtrue/falseになる
  // 手動でsetLoading(true/false)を管理する必要がない

  Server Actionsとの相性

  Next.js 13以降のServer Actionsと組み合わせると特に有効：

  // Server Action使用時
  const onSubmit = async (data: FormData) => {
    startTransition(async () => {
      const result = await serverAction(data);  // Server Action
      // isPendingが自動的に管理される
    });
  };

  useTransition vs useState

  | 観点         | useState                  | useTransition  |
  |------------|---------------------------|----------------|
  | 状態管理       | 手動でsetLoading(true/false) | 自動でisPending管理 |
  | UIブロック     | 可能性あり                     | ユーザー入力を優先      |
  | React 18対応 | 従来の方法                     | 並行レンダリング対応     |
  | 使用シーン      | シンプルな処理                   | 重い処理・非同期処理     |

  実装のポイント

  1. フォーム送信に最適
    - APIコール中もユーザー操作を阻害しない
    - ローディング状態の自動管理
  2. エラーハンドリング
  startTransition(async () => {
    try {
      // 成功処理
    } catch {
      // エラー処理も含めてトランジション内で
    }
  });
  3. 複数の無効化条件
  disabled={isPending || form.formState.isSubmitting}
  // トランジション中またはフォーム送信中は無効化

  まとめ

  useTransitionは、ユーザー体験を向上させながら非同期処理を扱うための強力なツールです。特にフォーム送信のような「重い処理だが、UIの応答性を保ちたい」場面で威力を発揮します。
