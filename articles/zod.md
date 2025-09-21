## はじめに

フォームのバリデーションを実装するとき、「TypeScriptの型定義とバリデーションルールを二重管理するのが面倒」「エラーメッセージの管理が煩雑」といった悩みはありませんか？

ZodはTypeScript-firstなスキーマバリデーションライブラリで、React Hook Formと組み合わせることで、型安全かつ強力なフォームバリデーションを実現できます。

本記事では、ZodとReact Hook Formを組み合わせた基本的な使い方を解説します。

## 何ができる

### Zod × React Hook Formの主要機能

この組み合わせで、以下のことが実現できます。

### 1. スキーマから型を自動生成

```typescript
import { z } from 'zod';

// スキーマ定義
const userSchema = z.object({
  name: z.string().min(1, '名前は必須です'),
  email: z.string().email('有効なメールアドレスを入力してください'),
  age: z.number().min(18, '18歳以上である必要があります')
});

// 型を自動推論
type User = z.infer<typeof userSchema>;
// { name: string; email: string; age: number; }
```

### 2. 日本語エラーメッセージの簡単設定

```typescript
const schema = z.object({
  password: z.string()
    .min(8, 'パスワードは8文字以上で入力してください')
    .regex(/[A-Z]/, '大文字を1文字以上含めてください')
    .regex(/[0-9]/, '数字を1文字以上含めてください')
});
```

### 3. リアルタイムバリデーション

入力と同時にバリデーションが実行され、即座にエラーメッセージが表示されます。

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

### 基本設定

プロジェクトで使用するための初期設定は特に必要ありません。コンポーネント内で直接使用できます。

## 使用方法

### 基本的な使い方

#### 1. シンプルなログインフォーム

```tsx
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

// スキーマ定義
const loginSchema = z.object({
  email: z.string()
    .min(1, 'メールアドレスは必須です')
    .email('有効なメールアドレスを入力してください'),
  password: z.string()
    .min(6, 'パスワードは6文字以上で入力してください')
});

// 型推論
type LoginForm = z.infer<typeof loginSchema>;

export const LoginForm = () => {
  const {
    register,
    handleSubmit,
    formState: { errors }
  } = useForm<LoginForm>({
    resolver: zodResolver(loginSchema)
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

      <button type="submit">ログイン</button>
    </form>
  );
};
```

#### 2. より複雑なユーザー登録フォーム

```tsx
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

// スキーマ定義
const userRegistrationSchema = z.object({
  name: z.string()
    .min(1, '名前は必須です')
    .max(50, '名前は50文字以内で入力してください'),
  
  email: z.string()
    .min(1, 'メールアドレスは必須です')
    .email('有効なメールアドレスを入力してください'),
  
  age: z.string()
    .transform((val) => Number(val))
    .pipe(z.number().min(18, '18歳以上である必要があります')),
  
  password: z.string()
    .min(8, 'パスワードは8文字以上で入力してください')
    .regex(/[A-Z]/, '大文字を1文字以上含めてください')
    .regex(/[a-z]/, '小文字を1文字以上含めてください')
    .regex(/[0-9]/, '数字を1文字以上含めてください'),
  
  confirmPassword: z.string()
    .min(1, '確認用パスワードは必須です'),
  
  terms: z.boolean()
    .refine((val) => val === true, {
      message: '利用規約に同意する必要があります'
    })
}).refine((data) => data.password === data.confirmPassword, {
  message: 'パスワードが一致しません',
  path: ['confirmPassword']
});

type UserRegistrationForm = z.infer<typeof userRegistrationSchema>;

export const UserRegistrationForm = () => {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm<UserRegistrationForm>({
    resolver: zodResolver(userRegistrationSchema)
  });

  const onSubmit = async (data: UserRegistrationForm) => {
    try {
      // APIコール
      await fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(data)
      });
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label htmlFor="name">名前</label>
        <input
          id="name"
          {...register('name')}
        />
        {errors.name && (
          <p className="error">{errors.name.message}</p>
        )}
      </div>

      <div>
        <label htmlFor="email">メールアドレス</label>
        <input
          id="email"
          type="email"
          {...register('email')}
        />
        {errors.email && (
          <p className="error">{errors.email.message}</p>
        )}
      </div>

      <div>
        <label htmlFor="age">年齢</label>
        <input
          id="age"
          type="number"
          {...register('age')}
        />
        {errors.age && (
          <p className="error">{errors.age.message}</p>
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

      <div>
        <label>
          <input
            type="checkbox"
            {...register('terms')}
          />
          利用規約に同意する
        </label>
        {errors.terms && (
          <p className="error">{errors.terms.message}</p>
        )}
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? '送信中...' : '登録する'}
      </button>
    </form>
  );
};
```

### よく使うバリデーション

#### 条件付きバリデーション

```typescript
const schema = z.object({
  accountType: z.enum(['personal', 'business']),
  companyName: z.string().optional(),
}).refine(
  (data) => {
    if (data.accountType === 'business') {
      return !!data.companyName && data.companyName.length > 0;
    }
    return true;
  },
  {
    message: '法人アカウントの場合、会社名は必須です',
    path: ['companyName']
  }
);
```

#### 配列のバリデーション

```typescript
const schema = z.object({
  tags: z.array(z.string())
    .min(1, 'タグを1つ以上選択してください')
    .max(5, 'タグは5つまでです'),
  
  members: z.array(
    z.object({
      name: z.string().min(1, '名前は必須です'),
      email: z.string().email('有効なメールアドレスを入力してください')
    })
  ).min(1, 'メンバーを1人以上追加してください')
});
```

#### カスタムバリデーション

```typescript
// 日本の郵便番号
const postalCodeSchema = z.string()
  .regex(/^\d{3}-?\d{4}$/, '有効な郵便番号を入力してください（例：123-4567）');

// 電話番号
const phoneSchema = z.string()
  .regex(/^0\d{9,10}$/, '有効な電話番号を入力してください（ハイフンなし）');

// カタカナのみ
const katakanaSchema = z.string()
  .regex(/^[ァ-ヶー]+$/, 'カタカナで入力してください');
```

### エラーハンドリングのパターン

#### フォーカス時のエラー制御

```tsx
const {
  register,
  handleSubmit,
  formState: { errors, touchedFields }
} = useForm<FormData>({
  resolver: zodResolver(schema),
  mode: 'onBlur' // フォーカスが外れた時にバリデーション
});

// エラー表示をタッチされたフィールドのみに限定
{touchedFields.email && errors.email && (
  <p className="error">{errors.email.message}</p>
)}
```

#### グローバルエラーの表示

```tsx
const [globalError, setGlobalError] = useState('');

const onSubmit = async (data: FormData) => {
  try {
    setGlobalError('');
    await submitForm(data);
  } catch (error) {
    setGlobalError('送信に失敗しました。もう一度お試しください。');
  }
};

return (
  <form onSubmit={handleSubmit(onSubmit)}>
    {globalError && (
      <div className="alert alert-error">{globalError}</div>
    )}
    {/* フォームフィールド */}
  </form>
);
```

## まとめ

**ZodとReact Hook Formで、型安全なフォームを簡単に実装できます！**

- スキーマから型が自動生成されるため、型の二重管理が不要
- 日本語のエラーメッセージを簡単に設定できる
- 複雑なバリデーションルールも直感的に記述できる

**注意点**

- `transform`を使う場合は、HTMLのinput要素の型に注意（numberでもstringで受け取る）
- エラーメッセージは具体的でユーザーフレンドリーに
- パフォーマンスを考慮して、適切な`mode`を選択しましょう

ZodとReact Hook Formを活用して、メンテナンスしやすいフォームを実装しましょう！

## 参考リンク

- [Zod 公式ドキュメント](https://zod.dev/)
- [React Hook Form 公式ドキュメント](https://react-hook-form.com/)
- [@hookform/resolvers](https://github.com/react-hook-form/resolvers)
