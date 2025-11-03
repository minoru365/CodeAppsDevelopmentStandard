# Phase 2 Step 3: デザインシステム統合

## 📋 概要

このステップでは、カラーシステム、タイポグラフィ、スペーシングを統一して設定し、一貫したデザインシステムを構築します。

---

## 🎯 実施内容

1. globals.css の編集（CSS変数設定）
2. セマンティックカラーの定義
3. タイポグラフィシステムの設定
4. スペーシングシステムの統一

---

## 📝 実装手順

### 3-1. globals.css を編集

**ファイルパス:** `src/globals.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  /* 基本カラー */
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --card: 0 0% 100%;
  --card-foreground: 222.2 84% 4.9%;
  --popover: 0 0% 100%;
  --popover-foreground: 222.2 84% 4.9%;
  --primary: 222.2 47.4% 11.2%;
  --primary-foreground: 210 40% 98%;
  --secondary: 210 40% 96.1%;
  --secondary-foreground: 222.2 47.4% 11.2%;
  --muted: 210 40% 96.1%;
  --muted-foreground: 215.4 16.3% 46.9%;
  --accent: 210 40% 96.1%;
  --accent-foreground: 222.2 47.4% 11.2%;
  --destructive: 0 84.2% 60.2%;
  --destructive-foreground: 210 40% 98%;
  --border: 214.3 31.8% 91.4%;
  --input: 214.3 31.8% 91.4%;
  --ring: 222.2 47.4% 11.2%;
  --radius: 0.5rem;
}

.dark {
  --background: 222.2 84% 4.9%;
  --foreground: 210 40% 98%;
  --card: 222.2 84% 4.9%;
  --card-foreground: 210 40% 98%;
  --popover: 222.2 84% 4.9%;
  --popover-foreground: 210 40% 98%;
  --primary: 210 40% 98%;
  --primary-foreground: 222.2 47.4% 11.2%;
  --secondary: 217.2 32.6% 17.5%;
  --secondary-foreground: 210 40% 98%;
  --muted: 217.2 32.6% 17.5%;
  --muted-foreground: 215 20.2% 65.1%;
  --accent: 217.2 32.6% 17.5%;
  --accent-foreground: 210 40% 98%;
  --destructive: 0 62.8% 30.6%;
  --destructive-foreground: 210 40% 98%;
  --border: 217.2 32.6% 17.5%;
  --input: 217.2 32.6% 17.5%;
  --ring: 212.7 26.8% 83.9%;
}
```

---

### 3-2. セマンティックカラーの定義

#### 用途別のカラー設定

| 用途 | カラー | Tailwind クラス |
|------|--------|-----------------|
| 成功・完了 | Green | `text-green-600` `bg-green-50` `dark:bg-green-950/20` |
| 進行中 | Blue | `text-blue-600` `bg-blue-50` `dark:bg-blue-950/20` |
| 警告 | Yellow | `text-yellow-600` `bg-yellow-50` `dark:bg-yellow-950/20` |
| エラー・遅延 | Red | `text-red-600` `bg-red-50` `dark:bg-red-950/20` |
| 進捗率 | Purple | `text-purple-600` `bg-purple-50` `dark:bg-purple-950/20` |
| 情報 | Muted | `text-muted-foreground` `bg-muted` |

#### ダークモード対応の例

```tsx
// ライト・ダーク両対応のバッジ
<div className="bg-green-50 dark:bg-green-950/20">
  <span className="text-green-600 dark:text-green-400">完了</span>
</div>

// 警告メッセージ
<div className="bg-yellow-50 dark:bg-yellow-950/20 border border-yellow-200 dark:border-yellow-900">
  <span className="text-yellow-800 dark:text-yellow-200">注意が必要です</span>
</div>
```

#### Power Platform 公式カラー

| カラー変数 | HEX | 用途 |
|-----------|-----|------|
| `--power-blue` | #4072B3 | プライマリーカラー、メインアクション |
| `--power-blue-light` | #6088C6 | ホバー状態、セカンダリー要素 |
| `--power-blue-lighter` | #AEC4E5 | 背景、淡いアクセント |
| `--power-red` | #EB8686 | エラー、警告、削除アクション |
| `--power-gray` | #C0C0C0 | 無効状態、ボーダー |

**使用例:**

```tsx
// TailwindCSSクラスで使用
<button className="bg-primary text-primary-foreground">
  送信
</button>

// CSS変数として直接使用
<div style={{ backgroundColor: 'var(--power-blue)' }}>
  Power Apps
</div>
```

---

### 3-3. タイポグラフィシステム

#### 見出しスタイル

```tsx
// ページタイトル (h1)
<h1 className="text-3xl font-bold text-foreground">
  ページタイトル
</h1>

// セクションタイトル (h2)
<h2 className="text-2xl font-semibold text-foreground">
  セクションタイトル
</h2>

// カードタイトル (h3)
<h3 className="text-lg font-semibold text-foreground">
  カードタイトル
</h3>

// サイドメニューセクションヘッダー
<h3 className="text-xs font-semibold text-muted-foreground uppercase tracking-wider px-2">
  セクション名
</h3>
```

#### 本文スタイル

```tsx
// 通常テキスト
<p className="text-base text-foreground">通常の本文</p>

// 小さいテキスト（補足情報）
<p className="text-sm text-muted-foreground">補足情報</p>

// 極小テキスト（ラベル）
<p className="text-xs text-muted-foreground">ラベル</p>
```

#### タイポグラフィ対照表

| 要素 | サイズ | ウェイト | クラス |
|------|--------|---------|--------|
| h1 | 30px (text-3xl) | Bold (font-bold) | `text-3xl font-bold` |
| h2 | 24px (text-2xl) | Semibold (font-semibold) | `text-2xl font-semibold` |
| h3 | 18px (text-lg) | Semibold (font-semibold) | `text-lg font-semibold` |
| Body | 16px (text-base) | Normal (font-normal) | `text-base` |
| Small | 14px (text-sm) | Normal (font-normal) | `text-sm` |
| XSmall | 12px (text-xs) | Normal (font-normal) | `text-xs` |

---

### 3-4. スペーシングシステム

#### 一貫したスペーシング

- `space-y-2`: 要素間の小さい間隔（8px）
- `space-y-4`: 要素間の中間隔（16px）
- `space-y-6`: 要素間の大きい間隔（24px）
- `gap-4`: グリッドアイテム間の間隔（16px）
- `p-4`: パディング（16px）
- `p-6`: 大きめのパディング（24px）

#### スペーシング使用例

```tsx
// カードグリッド
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {/* カード */}
</div>

// 縦方向のリスト
<div className="space-y-4">
  <div>アイテム1</div>
  <div>アイテム2</div>
  <div>アイテム3</div>
</div>

// カード内のパディング
<Card className="p-6">
  <CardHeader>タイトル</CardHeader>
  <CardContent className="space-y-4">
    コンテンツ
  </CardContent>
</Card>
```

---

## ⚠️ 注意事項

- ✅ CSS変数は必ずライトモードとダークモードの両方を定義する
- ✅ セマンティックカラーは `dark:` プレフィックスで必ずダークモード対応する
- ✅ タイポグラフィは一貫性を保つために定義されたクラスを使用する
- ⚠️ カスタムカラーを追加する場合は必ずドキュメント化する

---

## ✅ 完了条件

- [ ] `src/globals.css` に CSS変数が設定されている
- [ ] ライトモード・ダークモードの両方の変数が定義されている
- [ ] セマンティックカラーが一貫して使用されている
- [ ] タイポグラフィシステムが適用されている
- [ ] スペーシングが統一されている
- [ ] `npm run dev` で確認してスタイルが正しく適用されている

---

## 🧪 動作確認

### テストコンポーネント

```tsx
// src/App.tsx（テスト用）
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';

function App() {
  return (
    <div className="p-8 space-y-6">
      {/* タイポグラフィテスト */}
      <div className="space-y-4">
        <h1 className="text-3xl font-bold text-foreground">H1: ページタイトル</h1>
        <h2 className="text-2xl font-semibold text-foreground">H2: セクションタイトル</h2>
        <h3 className="text-lg font-semibold text-foreground">H3: カードタイトル</h3>
        <p className="text-base text-foreground">Body: 通常のテキスト</p>
        <p className="text-sm text-muted-foreground">Small: 補足情報</p>
      </div>

      {/* セマンティックカラーテスト */}
      <div className="grid grid-cols-2 md:grid-cols-3 gap-4">
        <Card className="bg-green-50 dark:bg-green-950/20">
          <CardContent className="p-4">
            <span className="text-green-600 dark:text-green-400">完了</span>
          </CardContent>
        </Card>
        <Card className="bg-blue-50 dark:bg-blue-950/20">
          <CardContent className="p-4">
            <span className="text-blue-600 dark:text-blue-400">進行中</span>
          </CardContent>
        </Card>
        <Card className="bg-yellow-50 dark:bg-yellow-950/20">
          <CardContent className="p-4">
            <span className="text-yellow-600 dark:text-yellow-400">警告</span>
          </CardContent>
        </Card>
      </div>
    </div>
  );
}
```

---

## 📖 関連ドキュメント

- [TailwindCSS Color Reference](https://tailwindcss.com/docs/customizing-colors)
- [TailwindCSS Typography](https://tailwindcss.com/docs/font-size)
- [TailwindCSS Spacing](https://tailwindcss.com/docs/padding)

---

## 🔄 次のステップ

デザインシステムの統合が完了したら、次はレイアウトコンポーネントの実装に進みます。

👉 **[PHASE2_04_LAYOUT_COMPONENTS.md](./PHASE2_04_LAYOUT_COMPONENTS.md)** に進む
