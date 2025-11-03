# Phase 2 Step 8: デザインガイドライン・ベストプラクティス

## 📋 概要

このドキュメントは、Code Apps開発における**デザインの一貫性**、**ベストプラクティス**、**カスタマイズ手順**を定義します。

すべての開発者がこのガイドラインに従うことで、統一された美しいUIを実現します。

---

## 🎨 デザイン原則

### 1. 一貫性 (Consistency)

**カラーシステムの統一:**
- セマンティックカラーを必ず使用
- ライト・ダークモードの両方で動作確認
- Power Platform ブランドカラーに準拠

**タイポグラフィの統一:**
- 見出しレベルを適切に使用 (h1 → h2 → h3)
- 本文は `text-base` または `text-sm`
- ボタンやラベルは `text-sm` または `text-xs`

**スペーシングの統一:**
- `space-y-*` や `gap-*` を一貫して使用
- カード間: `space-y-4` または `gap-6`
- フォーム要素間: `space-y-3`

### 2. シンプリシティ (Simplicity)

**過度な装飾を避ける:**
- 必要最小限のUI要素のみを配置
- ホワイトスペースを効果的に活用
- 情報の階層を明確にする

**直感的な操作性:**
- 主要なアクションは目立つボタン (primary)
- 破壊的な操作は赤色 (destructive)
- キャンセルやサブアクションは outline または ghost

### 3. アクセシビリティ (Accessibility)

**必須実装項目:**
- `aria-label` を適切に設定
- キーボードナビゲーションをサポート
- コントラスト比をWCAG 2.1 AA準拠にする
- ダークモードでも読みやすい配色

**カラーのみに頼らない:**
- ステータス表示にはアイコンやテキストも併記
- エラーメッセージは赤だけでなく、文言も明確に

### 4. レスポンシブデザイン (Responsive Design)

**モバイルファースト:**
- 基本スタイルはモバイル向けに設計
- `md:` (768px)、`lg:` (1024px) でデスクトップ対応
- グリッドレイアウトは `grid-cols-1 md:grid-cols-2 lg:grid-cols-3`

**ブレークポイント:**
- モバイル: `< 768px`
- タブレット: `768px - 1024px`
- デスクトップ: `> 1024px`

---

## 🎨 カラーシステム

### セマンティックカラー一覧

| 用途 | Tailwind クラス | ライトモード | ダークモード |
|------|----------------|-------------|-------------|
| **背景** | `bg-background` | 白 (#ffffff) | ダーク (#0a0a0a) |
| **前景** | `text-foreground` | 黒 (#0a0a0a) | 白 (#fafafa) |
| **カード** | `bg-card` | 白 (#ffffff) | ダーク (#0a0a0a) |
| **プライマリ** | `bg-primary` `text-primary` | ブルー (#0078d4) | ブルー (#4494ff) |
| **成功** | `text-success-600` `bg-success-100` | グリーン (#107c10) | グリーン (#4ade80) |
| **警告** | `text-warning-600` `bg-warning-100` | オレンジ (#ff8c00) | オレンジ (#fb923c) |
| **エラー** | `text-error-600` `bg-error-100` | レッド (#d13438) | レッド (#ef4444) |
| **ミュート** | `text-muted-foreground` | グレー (#737373) | グレー (#a1a1aa) |

### カラー使用ガイドライン

**✅ 正しい例:**
```tsx
// 成功メッセージ
<div className="bg-success-100 dark:bg-success-950 text-success-600 dark:text-success-400 p-4 rounded-lg">
  <CheckCircle className="h-5 w-5" />
  <span>保存に成功しました</span>
</div>

// エラーメッセージ
<div className="bg-error-100 dark:bg-error-950 text-error-600 dark:text-error-400 p-4 rounded-lg">
  <XCircle className="h-5 w-5" />
  <span>エラーが発生しました</span>
</div>
```

**❌ 間違った例:**
```tsx
// ハードコードされたカラーコード（避ける）
<div className="bg-[#ff0000] text-[#ffffff]">エラー</div>

// ダークモード対応なし（避ける）
<div className="bg-green-100 text-green-600">成功</div>
```

---

## 📝 タイポグラフィシステム

### 見出しスタイル

```tsx
// h1 - ページタイトル
<h1 className="text-3xl font-bold text-foreground mb-4">
  ページタイトル
</h1>

// h2 - セクションタイトル
<h2 className="text-2xl font-semibold text-foreground mb-3">
  セクションタイトル
</h2>

// h3 - サブセクションタイトル
<h3 className="text-xl font-semibold text-foreground mb-2">
  サブセクションタイトル
</h3>
```

### 本文・説明文スタイル

```tsx
// 通常の本文
<p className="text-base text-foreground">
  これは通常の本文テキストです。
</p>

// 説明文・補足テキスト
<p className="text-sm text-muted-foreground">
  これは説明文や補足情報です。
</p>

// 小さい補足テキスト
<p className="text-xs text-muted-foreground">
  最終更新: 2025年1月20日
</p>
```

### フォントサイズ一覧

| クラス | サイズ | 用途 |
|--------|--------|------|
| `text-xs` | 12px | 補足情報、キャプション |
| `text-sm` | 14px | ボタン、ラベル、説明文 |
| `text-base` | 16px | 通常の本文 |
| `text-lg` | 18px | 強調テキスト |
| `text-xl` | 20px | h3見出し |
| `text-2xl` | 24px | h2見出し |
| `text-3xl` | 30px | h1見出し、ページタイトル |

---

## 📐 スペーシングシステム

### 一貫したスペーシング

```tsx
// カード・コンテナ間のスペース
<div className="space-y-4">
  <Card>カード1</Card>
  <Card>カード2</Card>
  <Card>カード3</Card>
</div>

// フォーム要素間のスペース
<div className="space-y-3">
  <FormField label="名前">
    <Input />
  </FormField>
  <FormField label="メール">
    <Input />
  </FormField>
</div>

// グリッドレイアウトのギャップ
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <Card>アイテム1</Card>
  <Card>アイテム2</Card>
  <Card>アイテム3</Card>
</div>
```

### スペーシング値の目安

| クラス | サイズ | 用途 |
|--------|--------|------|
| `gap-2` / `space-y-2` | 8px | 小さな要素間 (アイコン・テキスト) |
| `gap-3` / `space-y-3` | 12px | フォーム要素間 |
| `gap-4` / `space-y-4` | 16px | カード間、セクション内 |
| `gap-6` / `space-y-6` | 24px | セクション間、グリッドアイテム間 |
| `gap-8` / `space-y-8` | 32px | 大きなセクション間 |

---

## 🧩 コンポーネント使用ガイドライン

### ボタン (Button)

**バリアント選択:**
```tsx
// プライマリアクション（保存、送信、作成など）
<Button variant="default">保存</Button>

// セカンダリアクション（キャンセル、戻るなど）
<Button variant="outline">キャンセル</Button>

// 破壊的アクション（削除、無効化など）
<Button variant="destructive">削除</Button>

// 控えめなアクション（リンクや補助的な操作）
<Button variant="ghost">詳細を見る</Button>
```

### カード (Card)

**カードの使い方:**
```tsx
// 基本的なカード
<Card>
  <h3 className="text-lg font-semibold mb-2">タイトル</h3>
  <p className="text-sm text-muted-foreground">説明文</p>
</Card>

// 統計カード（KPI表示）
<Card className="hover:shadow-lg transition-shadow">
  <div className="flex items-center justify-between">
    <div>
      <p className="text-sm text-muted-foreground">総売上</p>
      <h3 className="text-2xl font-bold">¥1,234,567</h3>
    </div>
    <DollarSign className="h-8 w-8 text-primary" />
  </div>
</Card>
```

### Badge (バッジ)

**ステータス表示に使用:**
```tsx
// 成功・完了
<Badge variant="success">完了</Badge>

// 警告・保留
<Badge variant="warning">保留中</Badge>

// エラー・失敗
<Badge variant="destructive">失敗</Badge>

// 通常・情報
<Badge variant="default">進行中</Badge>
```

### Input (インプット)

**フォーム入力:**
```tsx
<div className="space-y-2">
  <Label htmlFor="email">メールアドレス</Label>
  <Input
    id="email"
    type="email"
    placeholder="example@example.com"
    className="bg-background border-input"
  />
  <p className="text-xs text-muted-foreground">
    ログインに使用するメールアドレスを入力してください
  </p>
</div>
```

---

## 🎭 ダークモード対応

### 必須実装項目

**✅ すべてのコンポーネントでダークモード対応:**
```tsx
// 背景色
<div className="bg-background dark:bg-background">

// テキスト色
<p className="text-foreground dark:text-foreground">

// カード・ボーダー
<div className="bg-card dark:bg-card border-border dark:border-border">

// セマンティックカラー
<div className="bg-success-100 dark:bg-success-950 text-success-600 dark:text-success-400">
```

**✅ hover状態もダークモード対応:**
```tsx
<Button className="hover:bg-secondary dark:hover:bg-secondary/80">
  ボタン
</Button>
```

**✅ shadow（影）もダークモード調整:**
```tsx
<Card className="shadow-md dark:shadow-lg dark:shadow-white/5">
  カード
</Card>
```

---

## 📱 レスポンシブデザイン

### グリッドレイアウト

**基本パターン:**
```tsx
// 1列（モバイル） → 2列（タブレット） → 3列（デスクトップ）
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <Card>アイテム1</Card>
  <Card>アイテム2</Card>
  <Card>アイテム3</Card>
</div>

// 1列（モバイル） → 2列（タブレット） → 4列（デスクトップ）
<div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-4 gap-4">
  <StatsCard />
  <StatsCard />
  <StatsCard />
  <StatsCard />
</div>
```

### ヘッダーのレスポンシブ対応

**モバイル: ハンバーガーメニュー表示:**
```tsx
<header className="flex items-center justify-between px-4 h-16">
  {/* モバイル: ハンバーガーメニュー */}
  <Button variant="ghost" size="icon" className="md:hidden">
    <Menu className="h-5 w-5" />
  </Button>

  {/* デスクトップ: 常に表示 */}
  <h1 className="text-lg font-semibold hidden md:block">
    アプリケーション名
  </h1>
</header>
```

### サイドバーのレスポンシブ対応

**デスクトップ: 常時表示 / モバイル: オーバーレイ表示:**
```tsx
// デスクトップサイドバー
<aside className="hidden md:flex w-64 border-r">
  <Sidebar />
</aside>

// モバイルオーバーレイ
{isMobileMenuOpen && (
  <div className="fixed inset-0 z-50 md:hidden">
    <div className="absolute inset-0 bg-black/20 backdrop-blur-sm" onClick={closeMobileMenu} />
    <aside className="absolute left-0 top-0 h-full w-64 bg-card">
      <Sidebar onItemSelect={closeMobileMenu} />
    </aside>
  </div>
)}
```

---

## ⚡ パフォーマンス最適化

### 画像の最適化

**適切なサイズを使用:**
```tsx
// ❌ 大きすぎる画像
<img src="/hero-image-4k.jpg" width="300" />

// ✅ 適切なサイズ
<img src="/hero-image-600w.jpg" width="300" />
```

**lazy loading を使用:**
```tsx
<img src="/image.jpg" loading="lazy" alt="説明" />
```

### コンポーネントの最適化

**不要な再レンダリングを防ぐ:**
```tsx
// React.memo を使用
export const ExpensiveComponent = React.memo(({ data }) => {
  return <div>{data}</div>;
});

// useMemo でメモ化
const sortedData = useMemo(() => {
  return data.sort((a, b) => a.value - b.value);
}, [data]);
```

---

## 🔧 カスタマイズ手順

### プライマリカラーの変更

**1. `globals.css` を編集:**
```css
/* globals.css */
@layer base {
  :root {
    /* ライトモード */
    --primary: 221.2 83.2% 53.3%; /* ブルー → 変更したい色 */
  }

  .dark {
    /* ダークモード */
    --primary: 217.2 91.2% 59.8%; /* 明るいブルー → 変更したい色 */
  }
}
```

**2. HSL値の取得方法:**
- カラーピッカーで色を選択: https://hslpicker.com/
- 例: `hsl(200, 80%, 50%)` → `200 80% 50%` (カンマなし、hsl()なし)

### フォントファミリーの変更

**1. `globals.css` でフォントをインポート:**
```css
/* globals.css */
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700&display=swap');

@layer base {
  body {
    font-family: 'Noto Sans JP', sans-serif;
  }
}
```

**2. `tailwind.config.js` で設定:**
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        sans: ['Noto Sans JP', 'sans-serif'],
      },
    },
  },
}
```

### ボーダー半径の変更

**`globals.css` を編集:**
```css
/* globals.css */
@layer base {
  :root {
    --radius: 0.5rem; /* 8px → 変更したい値 */
  }
}

/* より角丸に: 0.75rem (12px) */
/* 直角に: 0rem (0px) */
/* 完全な円形に: 9999px */
```

---

## 📚 関連ドキュメント

- [Phase 2 Step 1: ロゴとアセット](./PHASE2_01_LOGO_ASSETS.md)
- [Phase 2 Step 2: shadcn/ui セットアップ](./PHASE2_02_SHADCN_SETUP.md)
- [Phase 2 Step 3: デザインシステム](./PHASE2_03_DESIGN_SYSTEM.md)
- [Phase 2 Step 4: レイアウトコンポーネント](./PHASE2_04_LAYOUT_COMPONENTS.md)
- [Phase 2 Step 5: ダークモード](./PHASE2_05_DARK_MODE.md)
- [Phase 2 Step 6: MVP実装](./PHASE2_06_MVP_IMPLEMENTATION.md)
- [Phase 2 Step 7: エラーチェック](./PHASE2_07_ERROR_CHECK_INTEGRATION.md)

---

## 🎉 ガイドライン完了！

このガイドラインに従うことで、**統一感のある美しいUI**を実現できます。

新しいコンポーネントを作成する際は、このドキュメントを参照してください。

👉 **Phase 3: ローカル実行とテスト** に進む - [PHASE3_LOCAL_TESTING.md](../../PHASE3_LOCAL_TESTING.md)

---

## 📋 デザインチェックリスト

新しいコンポーネント作成時にチェック:

**基本:**
- [ ] セマンティックカラーを使用している
- [ ] ダークモード対応している
- [ ] レスポンシブデザインになっている
- [ ] 適切なスペーシングを使用している

**アクセシビリティ:**
- [ ] `aria-label` が設定されている
- [ ] キーボードナビゲーションが動作する
- [ ] コントラスト比が適切（WCAG AA準拠）

**パフォーマンス:**
- [ ] 不要な再レンダリングを防いでいる
- [ ] 画像は最適化されている
- [ ] lazy loading を使用している

**コーディング:**
- [ ] TypeScript型エラーが0件
- [ ] ESLint警告が最小限
- [ ] コンポーネントが再利用可能

すべてチェックできたら、完璧です！🎉
