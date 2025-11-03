# Phase 2 Step 2: shadcn/ui + TailwindCSS 統合セットアップ

## 📋 概要

このステップでは、shadcn/ui と TailwindCSS を統合し、モダンなUIコンポーネントライブラリをセットアップします。

---

## 🎯 実施内容

1. shadcn/ui初期化
2. 基本UIコンポーネントの追加
3. 設定ファイルの確認

---

## 📝 実装手順

### 2-1. shadcn/ui初期化

```bash
npx shadcn@latest init
```

#### 対話式プロンプト

```text
Would you like to use TypeScript? › Yes
Which style would you like to use? › Default
Which color would you like to use as base color? › Slate
Where is your global CSS file? › src/globals.css
Would you like to use CSS variables for colors? › Yes
Where is your tailwind.config.js located? › tailwind.config.js
Configure the import alias for components: › @/components
Configure the import alias for utils: › @/lib/utils
Are you using React Server Components? › No
```

#### 自動的に作成されるファイル

- `tailwind.config.js` - TailwindCSS設定
- `src/globals.css` - グローバルスタイル
- `components.json` - shadcn/ui設定
- `src/lib/utils.ts` - ユーティリティ関数

#### 自動的にインストールされるパッケージ

```json
{
  "dependencies": {
    "class-variance-authority": "^x.x.x",
    "clsx": "^x.x.x",
    "tailwind-merge": "^x.x.x"
  },
  "devDependencies": {
    "tailwindcss": "^x.x.x",
    "tailwindcss-animate": "^x.x.x",
    "autoprefixer": "^x.x.x",
    "postcss": "^x.x.x"
  }
}
```

---

### 2-2. 基本UIコンポーネントの追加

#### 必須コンポーネント

```bash
# Power Apps開発でよく使う基本コンポーネント
npx shadcn@latest add button card input select table
```

**追加されるコンポーネント:**
- `button` - ボタン
- `card` - カードコンテナ
- `input` - テキスト入力
- `select` - ドロップダウン
- `table` - テーブル

#### コンポーネントの格納場所

```text
src/
└── components/
    └── ui/
        ├── button.tsx
        ├── card.tsx
        ├── input.tsx
        ├── select.tsx
        └── table.tsx
```

#### 追加のコンポーネント（必要に応じて）

```bash
# フォーム関連
npx shadcn@latest add form label textarea checkbox radio-group

# ナビゲーション
npx shadcn@latest add tabs navigation-menu

# フィードバック
npx shadcn@latest add alert dialog toast

# データ表示
npx shadcn@latest add badge avatar progress
```

---

### 2-3. 設定ファイルの確認

#### components.json

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "src/globals.css",
    "baseColor": "slate",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

#### tsconfig.json パスエイリアス確認

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

---

## ⚠️ 注意事項

- ✅ Node.js 16.8以降が必要
- ✅ すでに TailwindCSS がインストールされている場合は上書き確認が表示される
- ✅ `@/` エイリアスは自動的に設定される
- ⚠️ PowerProvider.tsx は変更しない

---

## ✅ 完了条件

- [ ] `npx shadcn@latest init` が成功している
- [ ] 基本コンポーネント（button, card, input, select, table）が追加されている
- [ ] `src/components/ui/` ディレクトリにコンポーネントファイルが存在する
- [ ] `src/lib/utils.ts` が存在する
- [ ] `components.json` が作成されている
- [ ] `npm run dev` でエラーなく起動できる

---

## 🧪 動作確認

### 基本コンポーネントのテスト

```tsx
// src/App.tsx（テスト用）
import { Button } from '@/components/ui/button';
import { Card } from '@/components/ui/card';
import { Input } from '@/components/ui/input';

function App() {
  return (
    <div className="p-8 space-y-4">
      <Button>Click me</Button>
      <Card className="p-4">
        <h3 className="font-semibold">Card Title</h3>
        <p className="text-muted-foreground">Card content</p>
      </Card>
      <Input placeholder="Enter text..." />
    </div>
  );
}
```

### 確認ポイント

- ✅ ボタンがクリック可能
- ✅ カードに適切なスタイルが適用されている
- ✅ 入力フィールドが機能する
- ✅ ホバー効果が動作する

---

## 📖 関連ドキュメント

- [shadcn/ui 公式ドキュメント](https://ui.shadcn.com/)
- [TailwindCSS 公式ドキュメント](https://tailwindcss.com/)
- [Lucide Icons](https://lucide.dev/)

---

## 🔄 次のステップ

shadcn/ui のセットアップが完了したら、次はデザインシステムの統合に進みます。

👉 **[PHASE2_03_DESIGN_SYSTEM.md](./PHASE2_03_DESIGN_SYSTEM.md)** に進む
