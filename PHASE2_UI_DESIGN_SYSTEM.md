# Phase 2: UI / デザインシステム構築

## 📋 概要

このフェーズでは、**Power Apps Code Apps** に美しく統一されたUIとデザインシステムを構築します。

**shadcn/ui + TailwindCSS** をベースに、ライト・ダークモード対応、レスポンシブデザイン、アクセシビリティを備えた**モダンなデザインシステム**を実装します。

---

## 🎯 Phase 2 の目標

- ✅ アプリアイコンとロゴの設定
- ✅ shadcn/ui + TailwindCSS の導入
- ✅ カラーシステム・タイポグラフィ・スペーシングの定義
- ✅ 共通レイアウトコンポーネントの実装
- ✅ ライト・ダークモードの実装
- ✅ MVP機能の実装
- ✅ エラーチェックと最終統合

---

## 📚 ドキュメント構成

Phase 2 は **8つのステップ** に分かれています。順番に実装してください。

| # | ドキュメント | 内容 | 所要時間 |
|---|------------|------|---------|
| **Step 1** | [ロゴとアセット](./docs/phases/PHASE2_01_LOGO_ASSETS.md) | アプリアイコン・ロゴの作成と設定 | 15分 |
| **Step 2** | [shadcn/ui セットアップ](./docs/phases/PHASE2_02_SHADCN_SETUP.md) | shadcn/ui + TailwindCSS 初期化 | 20分 |
| **Step 3** | [デザインシステム](./docs/phases/PHASE2_03_DESIGN_SYSTEM.md) | カラー・タイポグラフィ・スペーシング定義 | 30分 |
| **Step 4** | [レイアウトコンポーネント](./docs/phases/PHASE2_04_LAYOUT_COMPONENTS.md) | Header・SideMenu・MainLayout 実装 | 40分 |
| **Step 5** | [ダークモード](./docs/phases/PHASE2_05_DARK_MODE.md) | ライト・ダークモード切り替え実装 | 30分 |
| **Step 6** | [MVP実装](./docs/phases/PHASE2_06_MVP_IMPLEMENTATION.md) | Dashboard ページと UI パターン実装 | 45分 |
| **Step 7** | [エラーチェック](./docs/phases/PHASE2_07_ERROR_CHECK_INTEGRATION.md) | TypeScript・ESLint チェック、統合確認 | 30分 |
| **Step 8** | [デザインガイドライン](./docs/phases/PHASE2_08_DESIGN_GUIDELINES.md) | ベストプラクティス・カスタマイズ手順 | 参照用 |

**合計所要時間: 約3時間30分**

---

## 🚀 クイックスタート

### 前提条件

- Phase 1 (プロジェクトセットアップ) が完了していること
- Node.js と npm がインストールされていること
- Power Platform CLI が認証済みであること

### 実行手順

**Step 1から順番に実行:**

```bash
# Step 1: ロゴ設定
# → PHASE2_01_LOGO_ASSETS.md を参照

# Step 2: shadcn/ui セットアップ
npx shadcn@latest init
npx shadcn@latest add button card input select table

# Step 3: デザインシステム
# → globals.css を編集（PHASE2_03_DESIGN_SYSTEM.md）

# Step 4: レイアウトコンポーネント
# → CommonHeader.tsx, SideMenu.tsx, MainLayout.tsx 作成

# Step 5: ダークモード
# → ThemeContext.tsx, ThemeProvider.tsx 作成

# Step 6: MVP実装
# → Dashboard.tsx 作成、App.tsx に統合

# Step 7: エラーチェック
npm run build
npm run lint

# Step 8: デザインガイドライン参照
# → PHASE2_08_DESIGN_GUIDELINES.md を確認
```

---

## 📖 学習パス

### 初めて Code Apps を開発する方

**推奨学習順序:**

1. **Step 1** - ロゴ設定を理解する
2. **Step 2** - shadcn/ui の基本を学ぶ
3. **Step 3** - デザインシステムの概念を理解する
4. **Step 4** - レイアウトコンポーネントの構造を学ぶ
5. **Step 5** - ダークモードの実装方法を理解する
6. **Step 6** - 実際のページ実装を学ぶ
7. **Step 7** - エラーチェックの重要性を理解する
8. **Step 8** - ベストプラクティスを参照する

### デザインシステムに慣れている方

**ショートカット:**

- Step 1-2: 必須実行
- Step 3: `globals.css` のカスタマイズのみ
- Step 4-6: テンプレートをコピー&カスタマイズ
- Step 7: 必須実行
- Step 8: 必要に応じて参照

### UI/UX デザイナーの方

**フォーカスエリア:**

- **Step 3**: デザインシステムの定義
- **Step 5**: ダークモード対応
- **Step 8**: デザインガイドライン・ベストプラクティス

---

## 🎨 実装内容の概要

### 1. アプリアイコンとロゴ (Step 1)

- Power Platform アプリアイコン設定
- ロゴファイルの配置
- `pac code update` でアイコン反映

### 2. shadcn/ui セットアップ (Step 2)

- TailwindCSS 初期化
- shadcn/ui コンポーネントインストール
- `components/ui/` フォルダ構成

### 3. デザインシステム (Step 3)

- `globals.css` にカラーシステム定義
- セマンティックカラー (primary, success, warning, error)
- タイポグラフィシステム (h1-h3, 本文)
- スペーシングシステム (gap, space-y)

### 4. レイアウトコンポーネント (Step 4)

- **CommonHeader**: 64px 固定ヘッダー、ロゴ・ユーザーアバター表示
- **SideMenu**: 256px サイドバー、折りたたみ可能
- **MainLayout**: ヘッダー+サイドバー+メインコンテンツ統合

### 5. ダークモード (Step 5)

- **ThemeContext**: テーマ管理コンテキスト
- **ThemeProvider**: ローカルストレージ連携
- **ThemeToggle**: ライト・ダーク・システム切り替えドロップダウン

### 6. MVP実装 (Step 6)

- **Dashboard ページ**: 統計カード4枚、プロジェクトリスト表示
- **UI パターン**: StatusBadge, ListItem コンポーネント
- **App.tsx 統合**: ThemeProvider + MainLayout

### 7. エラーチェック (Step 7)

- TypeScript 型チェック (`npm run build`)
- ESLint コード品質チェック (`npm run lint`)
- ローカル実行確認 (`npm run dev`)
- PowerProvider.tsx 変更チェック

### 8. デザインガイドライン (Step 8)

- デザイン原則 (一貫性、シンプリシティ、アクセシビリティ、レスポンシブ)
- カラーシステム使用ガイドライン
- タイポグラフィ・スペーシングガイドライン
- コンポーネント使用ガイドライン
- カスタマイズ手順

---

## ✅ Phase 2 完了チェックリスト

すべての項目をチェックしてください:

### セットアップ

- [ ] アプリアイコンが設定されている
- [ ] ロゴファイルが `public/logo/` に配置されている
- [ ] shadcn/ui が初期化されている
- [ ] 基本UIコンポーネント (button, card, input, select, table) がインストールされている

### デザインシステム

- [ ] `globals.css` にカラーシステムが定義されている
- [ ] ライト・ダークモード両方の変数が定義されている
- [ ] セマンティックカラーが適切に使用されている
- [ ] タイポグラフィシステムが定義されている

### レイアウト

- [ ] CommonHeader が実装されている (64px 固定)
- [ ] SideMenu が実装されている (256px、折りたたみ可能)
- [ ] MainLayout が実装されている (ヘッダー+サイドバー+メイン)
- [ ] ThemeProvider が統合されている

### ダークモード

- [ ] ThemeContext が作成されている
- [ ] ThemeProvider が App.tsx でラップされている
- [ ] ThemeToggle がヘッダーに配置されている
- [ ] ライト・ダーク・システムの3つの切り替えが動作する
- [ ] テーマ設定がローカルストレージに保存される

### MVP機能

- [ ] Dashboard ページが実装されている
- [ ] 統計カードが4枚表示されている
- [ ] プロジェクトリストが表示されている
- [ ] StatusBadge コンポーネントが実装されている
- [ ] ListItem コンポーネントが実装されている

### 動作確認

- [ ] `npm run build` が成功する
- [ ] `npm run lint` でエラーが0件
- [ ] TypeScript 型エラーが0件
- [ ] ローカルで正しく動作する (`npm run dev`)
- [ ] レスポンシブデザインが機能する (モバイル・タブレット・デスクトップ)
- [ ] ダークモード切り替えが動作する
- [ ] PowerProvider.tsx が変更されていない

---

## 🔗 関連ドキュメント

### Phase 2 詳細ドキュメント

- [Step 1: ロゴとアセット](./docs/phases/PHASE2_01_LOGO_ASSETS.md)
- [Step 2: shadcn/ui セットアップ](./docs/phases/PHASE2_02_SHADCN_SETUP.md)
- [Step 3: デザインシステム](./docs/phases/PHASE2_03_DESIGN_SYSTEM.md)
- [Step 4: レイアウトコンポーネント](./docs/phases/PHASE2_04_LAYOUT_COMPONENTS.md)
- [Step 5: ダークモード](./docs/phases/PHASE2_05_DARK_MODE.md)
- [Step 6: MVP実装](./docs/phases/PHASE2_06_MVP_IMPLEMENTATION.md)
- [Step 7: エラーチェック](./docs/phases/PHASE2_07_ERROR_CHECK_INTEGRATION.md)
- [Step 8: デザインガイドライン](./docs/phases/PHASE2_08_DESIGN_GUIDELINES.md)

### その他のPhase

- [Phase 1: プロジェクトセットアップ](./PHASE1_PROJECT_SETUP.md)
- [Phase 3: ローカル実行とテスト](./PHASE3_LOCAL_TESTING.md)
- [Phase 4: デプロイ](./PHASE4_DEPLOYMENT.md)
- [Phase 5: データ統合 (Dataverse)](./PHASE5_DATA_INTEGRATION.md)

### 補足資料

- [ロゴ実装マスターガイド](./docs/LOGO_MASTER_GUIDE.md)
- [Dataverse トラブルシューティング](./docs/DATAVERSE_TROUBLESHOOTING.md)

### 外部リソース

- [shadcn/ui 公式ドキュメント](https://ui.shadcn.com/)
- [TailwindCSS 公式ドキュメント](https://tailwindcss.com/)
- [Lucide Icons](https://lucide.dev/)

---

## 🔄 次のステップ

Phase 2 が完了したら、次は **Phase 3: ローカル実行とテスト** に進みます。

👉 **[Phase 3 リファレンス](./PHASE3_LOCAL_TESTING.md)** に進む

---

## 💡 よくある質問 (FAQ)

### Q1: shadcn/ui と Fluent UI の違いは？

**A**: shadcn/ui は TailwindCSS ベースの軽量UIライブラリで、Code Apps に最適です。Fluent UI は Microsoft 公式ですが、Code Apps には shadcn/ui が推奨されます。

### Q2: ダークモードは必須ですか？

**A**: 推奨です。モダンなアプリケーションでは標準機能となっており、ユーザビリティ向上に貢献します。

### Q3: レスポンシブデザインはどこまで対応すべき？

**A**: 最低限、デスクトップ (>1024px) とモバイル (<768px) に対応してください。タブレット (768-1024px) も推奨です。

### Q4: PowerProvider.tsx を変更してはいけない理由は？

**A**: PowerProvider.tsx は Power Apps SDK の初期化を行うため、変更するとアプリが動作しなくなる可能性があります。

### Q5: Phase 2 をスキップできますか？

**A**: スキップ非推奨です。Phase 2 は統一されたUIとデザインシステムを確立するため、後のフェーズでの開発効率に大きく影響します。

---

## 📝 バックアップ情報

**オリジナルファイル:**
このファイルは Phase 2 文書分割の一環で作成されました。

オリジナルファイル (4950行) は以下にバックアップされています:
- `PHASE2_UI_DESIGN_SYSTEM.md.backup`

分割により、各ステップは独立したファイルとして管理され、AI処理可能なサイズ(<2000行)に最適化されました。

---

🎉 **Phase 2 のすべてのドキュメントが整いました！**

順番に実装して、美しいUIとデザインシステムを構築しましょう！
