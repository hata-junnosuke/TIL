## はじめに

フォームのバリデーションを実装するとき、「TypeScriptの型定義とバリデーションルールを二重管理するのが面倒」「エラーメッセージの管理が煩雑」といった悩みはありませんか？

ZodはTypeScript-firstなスキーマバリデーションライブラリで、React Hook Formと組み合わせることで、型安全かつ強力なフォームバリデーションを実現できます。

本記事では、ZodとReact Hook Formを組み合わせた基本的な使い方を解説します。

  <img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2517030/2be4e38c-e2d3-40b7-981c-ab80eb07240b.png" width="200">

## 何ができる

### Zod × React Hook Formの主要機能

この組み合わせで、以下のことが実現できます。

### 1. 日本語エラーメッセージの簡単設定

```typescript
const schema = z.object({
  email: z.string()
    .min(1, 'メールアドレスは必須です')
    .email('有効なメールアドレスを入力してください'),
  password: z.string()
    .min(6, 'パスワードは6文字以上で入力してください')
});
```

### 2. リアルタイムバリデーション

入力と同時にバリデーションが実行され、即座にエラーメッセージが表示されます。

### 3. TypeScriptとの完全な統合

スキーマから型を自動生成し、型安全なフォーム開発が可能です。

## 導入

### インストール

必要なパッケージをインストール：

```bash
npm install react-hook-form zod @hookform/resolvers
```

または

```bash
yarn add react-hook-form zod @hookform/resolvers
```

## 使用方法

### 基本的な使い方

#### シンプルなログインフォーム

まずは、最もシンプルな例から始めましょう。

```tsx
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

// 1. Zodでスキーマを定義
const loginSchema = z.object({
  email: z.string()
    .min(1, 'メールアドレスは必須です')
    .email('有効なメールアドレスを入力してください'),
  password: z.string()
    .min(6, 'パスワードは6文字以上で入力してください'),
  confirmPassword: z.string()
    .min(1, 'パスワード（確認）は必須です')
})
// refineで複数フィールドにまたがるバリデーション
.refine((data) => data.password === data.confirmPassword, {
  message: 'パスワードが一致しません',
  path: ['confirmPassword'] // エラーを表示するフィールドを指定
});

// 2. z.inferで型を自動生成（型の二重管理が不要！）
type LoginForm = z.infer<typeof loginSchema>;
// 自動生成される型:
// {
//   email: string;
//   password: string;
//   confirmPassword: string;
// }

export const LoginForm = () => {
  // 3. React Hook FormにZodのスキーマを連携（zodResolverが橋渡し役）
  const {
    register,
    handleSubmit,
    formState: { errors }
  } = useForm<LoginForm>({
    resolver: zodResolver(loginSchema) // zodResolverでZodスキーマを渡す
  });

  const onSubmit = (data: LoginForm) => {
    console.log(data);
    // APIコールなど
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label htmlFor="email">メールアドレス</label>
        <input
          id="email"
          type="email"
          {...register('email')}
        />
        {/* エラーメッセージの表示 */}
        {errors.email && (
          <p className="error">{errors.email.message}</p>
        )}
      </div>

      <div>
        <label htmlFor="password">パスワード</label>
        <input
          id="password"
          type="password"
          {...register('password')}
        />
        {errors.password && (
          <p className="error">{errors.password.message}</p>
        )}
      </div>

      <div>
        <label htmlFor="confirmPassword">パスワード（確認）</label>
        <input
          id="confirmPassword"
          type="password"
          {...register('confirmPassword')}
        />
        {errors.confirmPassword && (
          <p className="error">{errors.confirmPassword.message}</p>
        )}
      </div>

      <button type="submit">ログイン</button>
    </form>
  );
};
```

これだけで、以下の機能を持つフォームが完成です！
- 日本語のエラーメッセージ表示
- 型安全（z.inferによる自動型生成）
- パスワード一致確認（refineによる複雑なバリデーション）

### 【参考までに】よく使うバリデーションパターン

```typescript
// 文字列の各種バリデーション
const stringSchema = z.object({
  // 必須項目
  required: z.string().min(1, '必須項目です'),
  
  // 文字数制限
  username: z.string()
    .min(3, '3文字以上で入力してください')
    .max(20, '20文字以内で入力してください'),
  
  // メールアドレス
  email: z.string().email('有効なメールアドレスを入力してください'),
  
  // URL
  website: z.string().url('有効なURLを入力してください'),

// 数値のバリデーション
const numberSchema = z.object({
  age: z.number()
    .min(0, '0以上の値を入力してください')
    .max(120, '120以下の値を入力してください'),

// その他
const otherSchema = z.object({
  // チェックボックス
  terms: z.boolean().refine(val => val === true, {
    message: '利用規約に同意してください'
  }),
  
  // セレクトボックス
  role: z.enum(['admin', 'user', 'guest'], {
    errorMap: () => ({ message: '選択してください' })
  })
```

## zodResolverの役割

### resolverとは

`zodResolver`は、ZodのスキーマとReact Hook Formを繋ぐ橋渡し役です。これにより、Zodで定義したバリデーションルールをReact Hook Formで使用できるようになります。

### zodResolverの仕組み

```typescript
import { zodResolver } from '@hookform/resolvers/zod';

// zodResolverの基本的な使い方
const {
  register,
  handleSubmit,
  formState: { errors }
} = useForm({
  resolver: zodResolver(schema) // ここでZodスキーマを渡す
});

// zodResolverがやっていること（イメージ）
// 1. フォームの値を受け取る
// 2. Zodスキーマでバリデーション
// 3. エラーをReact Hook Formの形式に変換
// 4. errorsオブジェクトとして返す
```

## z.inferで型を自動生成

### inferの詳細

`z.infer`は、Zodスキーマから自動的にTypeScriptの型を生成する強力な機能です。

```typescript
import { z } from 'zod';

// スキーマ定義
const userSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
  age: z.number().min(0).max(120),
  isActive: z.boolean(),
  tags: z.array(z.string()),
  metadata: z.object({
    createdAt: z.date(),
    updatedAt: z.date()
  })
});

// z.inferで型を自動生成
type User = z.infer<typeof userSchema>;
/*
自動生成される型:
{
  id: number;
  name: string;
  email: string;
  age: number;
  isActive: boolean;
  tags: string[];
  metadata: {
    createdAt: Date;
    updatedAt: Date;
  }
}
*/
```

### inferの便利な使い方

```typescript
// ネストしたスキーマから部分的に型を取得
const addressSchema = z.object({
  street: z.string(),
  city: z.string(),
  zipCode: z.string()
});

const userSchema = z.object({
  name: z.string(),
  email: z.string(),
  address: addressSchema // 別のスキーマを使用
});

// それぞれの型を取得
type User = z.infer<typeof userSchema>;
type Address = z.infer<typeof addressSchema>;

// 部分的なスキーマから型を生成
const nameEmailSchema = userSchema.pick({ name: true, email: true });
type NameEmail = z.infer<typeof nameEmailSchema>;
// 結果: { name: string; email: string; }
```

## refineで複雑なバリデーションを実装

### refineの詳細

`refine`は、Zodの標準バリデーションだけでは実現できない複雑な条件や、複数フィールドにまたがるバリデーションを実装するための機能です。

### refineの実践例

```typescript
const registrationSchema = z.object({
  email: z.string().email('有効なメールアドレスを入力してください'),
  password: z.string()
    .min(8, 'パスワードは8文字以上で入力してください'),
  confirmPassword: z.string(),
  accountType: z.enum(['personal', 'business']),
  companyName: z.string().optional(),
  startDate: z.string(),
  endDate: z.string()
})
// パスワードの一致確認
.refine((data) => data.password === data.confirmPassword, {
  message: 'パスワードが一致しません',
  path: ['confirmPassword']
})
// 条件付き必須項目
.refine((data) => {
  if (data.accountType === 'business') {
    return !!data.companyName && data.companyName.length > 0;
  }
  return true;
}, {
  message: '法人アカウントの場合、会社名は必須です',
  path: ['companyName']
})
// 日付の前後関係
.refine((data) => {
  const start = new Date(data.startDate);
  const end = new Date(data.endDate);
  return start <= end;
}, {
  message: '終了日は開始日より後にしてください',
  path: ['endDate']
});

// inferで型を生成
type RegistrationForm = z.infer<typeof registrationSchema>;
```

## まとめ

**ZodとReact Hook Formで、型安全なフォームを簡単に実装できます！**

- 日本語のエラーメッセージを簡単に設定できる
- `zodResolver`でZodとReact Hook Formをシームレスに連携
- `z.infer`でスキーマから型を自動生成し、型の二重管理が不要
- `refine`で複雑なバリデーションルールも直感的に実装

**注意点**

- `resolver`の設定を忘れるとバリデーションが動作しません
- `refine`
  - カスタムしやすいがパフォーマンスに注意（重い処理は避ける）
  - どのフィールドにエラーを表示するか指定したい場合は`path`指定を忘れずに

ZodとReact Hook Formを活用して、メンテナンスしやすいフォームを実装しましょう！

## 参考リンク

- [Zod 公式ドキュメント](https://zod.dev/)
- [React Hook Form 公式ドキュメント](https://react-hook-form.com/)
    - [Reresolver: Resolver](https://react-hook-form.com/docs/useform#resolver)
- [@hookform/resolvers](https://github.com/react-hook-form/resolvers)
