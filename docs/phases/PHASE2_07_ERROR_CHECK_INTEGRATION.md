# Phase 2 Step 7: エラーチェックと統合

## 📋 概要

このステップでは、すべての実装が正しく動作することを確認し、エラーをチェックします。

---

## 🎯 実施内容

1. TypeScript 型チェック
2. ESLint チェック
3. ビルド確認
4. ローカル実行確認

---

## 📝 実装手順

### 7-1. TypeScript 型チェック

```bash
# TypeScript 型エラーチェック
npm run build
```

#### 確認ポイント

- ✅ `Type error` が0件
- ✅ すべてのインポートが正しい
- ✅ コンポーネントの型定義が正しい

#### よくあるエラーと対処法

**エラー例1: モジュールが見つからない**
```text
Cannot find module '@/components/ui/button'
```

**対処法:**
```bash
# コンポーネントが追加されているか確認
ls src/components/ui/

# 追加されていない場合は shadcn/ui でインストール
npx shadcn@latest add button
```

**エラー例2: 型定義が見つからない**
```text
Property 'children' does not exist on type 'IntrinsicAttributes'
```

**対処法:**
```tsx
// インターフェースに children を追加
interface Props {
  children: React.ReactNode;
}
```

---

### 7-2. ESLint チェック

```bash
# ESLint でコード品質チェック
npm run lint
```

#### 確認ポイント

- ✅ ESLint エラーが0件
- ✅ ESLint 警告が最小限
- ✅ コーディング規約に準拠

#### よくある警告と対処法

**警告例1: 未使用変数**
```text
'React' is defined but never used
```

**対処法:**
```tsx
// React を使用しない場合は削除
- import React from 'react';
+ // React 削除（JSX変換で自動的にインポートされる）
```

**警告例2: any型の使用**
```text
Unexpected any. Specify a different type
```

**対処法:**
```tsx
// 適切な型を指定
- const data: any = {};
+ const data: Record<string, string> = {};
```

---

### 7-3. ビルド確認

```bash
# プロダクションビルド
npm run build

# ビルド成果物の確認
ls dist/
```

#### 確認ポイント

- ✅ ビルドが成功する
- ✅ `dist/` ディレクトリが作成される
- ✅ ビルドサイズが適切（目安: <1MB）

---

### 7-4. ローカル実行確認

```bash
# 開発サーバー起動
npm run dev
```

#### 確認ポイント

**✅ 基本動作**
- [ ] アプリが正しく起動する
- [ ] ヘッダーが表示される
- [ ] サイドメニューが表示される
- [ ] メインコンテンツが表示される

**✅ インタラクション**
- [ ] サイドメニューの開閉が動作する
- [ ] テーマ切り替えが動作する
- [ ] リンクのホバー効果が動作する
- [ ] ボタンのクリックが動作する

**✅ レスポンシブデザイン**
- [ ] デスクトップ表示が正しい（>1024px）
- [ ] タブレット表示が正しい（768px-1024px）
- [ ] モバイル表示が正しい（<768px）

**✅ ダークモード**
- [ ] ライトモードが正しく表示される
- [ ] ダークモードが正しく表示される
- [ ] テーマ設定が永続化される

---

## ⚠️ 重要な確認事項

### PowerProvider.tsx の確認

```bash
# PowerProvider.tsx が変更されていないことを確認
git diff src/PowerProvider.tsx
```

- ⚠️ **PowerProvider.tsx は絶対に変更しない**
- ⚠️ Power Apps SDK の初期化コードは保持する
- ✅ 変更がある場合は元に戻す

### globals.css のインポート確認

**main.tsx または App.tsx で確認:**

```tsx
// ✅ 正しい
import './globals.css';

// ❌ 間違い（インポートなし）
// globals.css がインポートされていない
```

---

## ✅ Phase 2 完了チェックリスト

### セットアップ

- [ ] shadcn/ui が初期化されている
- [ ] TailwindCSS が設定されている
- [ ] 基本UIコンポーネントが追加されている

### レイアウト

- [ ] CommonHeader が実装されている
- [ ] SideMenu が実装されている
- [ ] MainLayout が実装されている
- [ ] ThemeProvider が統合されている

### デザインシステム

- [ ] globals.css にカラーシステムが設定されている
- [ ] ライト・ダークモード両方の変数が定義されている
- [ ] セマンティックカラーが適切に使用されている

### MVP機能

- [ ] Dashboard ページが実装されている
- [ ] 統計カードが表示されている
- [ ] サンプルデータが表示されている

### 動作確認

- [ ] TypeScript エラーが0件
- [ ] ESLint エラーが0件
- [ ] ビルドが成功する
- [ ] ローカルで正しく動作する
- [ ] レスポンシブデザインが機能する
- [ ] ダークモードが機能する

---

## 🔧 トラブルシューティング

### 問題1: スタイルが適用されない

**症状:**
- コンポーネントが表示されるが、スタイルが適用されない

**原因:**
- `globals.css` がインポートされていない
- TailwindCSS のビルドが失敗している

**対処法:**
```bash
# globals.css のインポートを確認
# main.tsx または App.tsx に以下を追加
import './globals.css';

# TailwindCSS の再ビルド
npm run dev
```

### 問題2: コンポーネントが見つからない

**症状:**
```text
Module not found: Can't resolve '@/components/ui/button'
```

**原因:**
- shadcn/ui コンポーネントが追加されていない

**対処法:**
```bash
# コンポーネントを追加
npx shadcn@latest add button
```

### 問題3: ダークモードが動作しない

**症状:**
- テーマ切り替えボタンをクリックしても変わらない

**原因:**
- ThemeProvider がラップされていない
- globals.css のダークモード変数が未定義

**対処法:**
```tsx
// App.tsx で ThemeProvider を確認
import { ThemeProvider } from "@/contexts/ThemeContext";

const App = () => (
  <ThemeProvider defaultTheme="system" storageKey="code-app-ui-theme">
    {/* アプリコンテンツ */}
  </ThemeProvider>
);
```

---

## 📊 パフォーマンスチェック

### ビルドサイズ確認

```bash
npm run build
```

**目安:**
- 合計サイズ: <1MB
- JavaScript: <500KB
- CSS: <50KB

**サイズが大きい場合:**
- 未使用のコンポーネントを削除
- 動的インポートを検討
- ライブラリの最適化を検討

---

## 📖 関連ドキュメント

- [TypeScript 型システム](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html)
- [ESLint ルール](https://eslint.org/docs/latest/rules/)
- [Vite ビルド設定](https://vitejs.dev/guide/build.html)

---

## 🎉 Phase 2 完了！

すべてのチェックが完了したら、Phase 2 は完了です！

次は **Phase 3: ローカル実行とテスト** に進みます。

👉 **Phase 3 リファレンス** - PHASE3_LOCAL_TESTING.md

---

## 📝 最終確認コマンド

すべてを一度に確認する場合:

```bash
# すべてのチェックを実行
npm run build && npm run lint && npm run dev
```

これでエラーがなければ、Phase 2 は完璧に完了しています！🎉
