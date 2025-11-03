# Phase 2 Step 1: アプリアイコン・ロゴ作成

## 📋 概要

このステップでは、Code Appsで使用するロゴとアイコンの準備と配置を行います。

---

## 🎯 実施内容

1. assetsディレクトリ作成
2. ロゴファイルの準備
3. Code Apps登録時のロゴ指定

---

## 📝 実装手順

### 1-1. assetsディレクトリ作成

```bash
# public/assets ディレクトリ作成
mkdir -p public/assets
```

**ディレクトリ構造:**
```
public/
└── assets/
    ├── logo.svg      # メインロゴ（推奨）
    ├── logo.png      # フォールバック用
    └── favicon.ico   # ファビコン
```

---

### 1-2. ロゴファイルの準備

#### 方法1: 既存ロゴを使用

```bash
# 既存のロゴファイルをコピー
copy "C:\path\to\logo.svg" "public\assets\logo.svg"
copy "C:\path\to\logo.png" "public\assets\logo.png"
```

#### 方法2: 無料アイコン生成ツールを使用

| ツール | URL | 特徴 |
|-------|-----|------|
| Canva | https://canva.com | テンプレート豊富、初心者向け |
| LogoMaker | https://logomaker.com | シンプル操作 |
| Flaticon | https://flaticon.com | アイコン素材豊富 |
| Figma | https://figma.com | プロ向け、SVG出力可能 |

#### 推奨アイコンサイズ

- **SVGロゴ**: `logo.svg` (スケーラブル、推奨)
- **PNGアイコン**: `128x128px`, `256x256px`
- **ファビコン**: `favicon.ico` (32x32px)

---

### 1-3. Code Apps登録時のロゴ指定

#### 初回登録時

```bash
pac code init --displayName "My Code App" --logo "./public/assets/logo.svg"
```

#### 既存アプリのロゴ更新

```bash
pac code update --logo "./public/assets/logo.svg"
```

---

## ⚠️ 注意事項

- ✅ SVG形式が推奨（スケーラブル）
- ✅ ファイルサイズは1MB以下
- ✅ 透過背景推奨
- ✅ 正方形比率（1:1）が最適

---

## ✅ 完了条件

- [ ] `public/assets/` ディレクトリが作成されている
- [ ] ロゴファイル（SVGまたはPNG）が配置されている
- [ ] ファビコンが配置されている（オプション）
- [ ] Code Apps登録時にロゴが指定されている

---

## 📖 関連ドキュメント

> **📘 詳細なロゴ実装ガイド**  
> ロゴのデザイン仕様、実装パターン、トラブルシューティングは [LOGO_MASTER_GUIDE.md](../LOGO_MASTER_GUIDE.md) を参照してください。

- **[LOGO_MASTER_GUIDE.md](../LOGO_MASTER_GUIDE.md)** - ロゴ実装の完全ガイド
- **[LOGO_DISPLAY_FIX.md](../LOGO_DISPLAY_FIX.md)** - 表示問題のトラブルシューティング

---

## 🔄 次のステップ

ロゴの準備が完了したら、次は shadcn/ui のセットアップに進みます。

👉 **[PHASE2_02_SHADCN_SETUP.md](./PHASE2_02_SHADCN_SETUP.md)** に進む
