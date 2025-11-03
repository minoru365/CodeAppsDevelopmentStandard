# Phase 2 Step 4: レイアウトコンポーネント実装

## 📋 概要

このステップでは、CommonHeader、SideMenu、MainLayoutの3つのレイアウトコンポーネントを実装します。

---

## 🎯 実施内容

1. 基本レイアウト構造の設計
2. CommonHeader コンポーネントの実装
3. SideMenu コンポーネントの実装
4. MainLayout 統合コンポーネントの実装

---

## 📝 実装手順

### 4-1. 基本レイアウト構造

#### 推奨レイアウト構成

```text
┌─────────────────────────────────────────────────────┐
│ CommonHeader (固定ヘッダー - 64px)                   │
├──────────┬──────────────────────────────────────────┤
│          │                                          │
│ SideMenu │ Main Content Area                        │
│ (256px)  │ (動的コンテンツ)                          │
│          │                                          │
│          │                                          │
└──────────┴──────────────────────────────────────────┘
```

#### 重要な寸法

| 要素 | 高さ/幅 | クラス |
|------|---------|--------|
| ヘッダー | 64px (h-16) | `h-16` |
| サイドメニュー | 256px | `w-64` |
| メインコンテンツの高さ | calc(100vh - 4rem) | `h-[calc(100vh-4rem)]` |
| メインコンテンツのトップ余白 | 64px (pt-16) | `pt-16` |

---

### 4-2. CommonHeader コンポーネント

**ファイルパス:** `src/components/Layout/CommonHeader.tsx`

```tsx
import { Button } from '@/components/ui/button';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import { Menu } from 'lucide-react';
import { ThemeToggle } from '@/components/ThemeToggle';

interface CommonHeaderProps {
  onMenuToggle: () => void;
  title?: string;
  subtitle?: string;
}

export const CommonHeader: React.FC<CommonHeaderProps> = ({
  onMenuToggle,
  title = "アプリ名",
  subtitle = "説明"
}) => {
  return (
    <header className="sticky top-0 z-50 w-full border-b border-border bg-card/95 backdrop-blur-md supports-[backdrop-filter]:bg-card/80 shadow-sm">
      <div className="flex h-16 items-center justify-between px-4 md:px-6">
        {/* 左側: メニューボタン + タイトル */}
        <div className="flex items-center space-x-4">
          <Button 
            variant="ghost" 
            size="icon"
            onClick={onMenuToggle}
          >
            <Menu className="h-5 w-5" />
          </Button>
          <div className="flex flex-col">
            <span className="font-semibold text-foreground">{title}</span>
            <span className="text-xs text-muted-foreground">{subtitle}</span>
          </div>
        </div>

        {/* 右側: テーマトグル + プロフィール */}
        <div className="flex items-center space-x-2">
          <ThemeToggle />
          <Avatar className="h-8 w-8">
            <AvatarImage src="/assets/avatar.png" />
            <AvatarFallback>U</AvatarFallback>
          </Avatar>
        </div>
      </div>
    </header>
  );
};
```

#### 特徴

- **固定位置**: `sticky top-0 z-50`
- **半透明背景**: `bg-card/95 backdrop-blur-md`
- **ガラスモーフィズム効果**: `backdrop-blur-md`
- **影**: `shadow-sm`
- **レスポンシブパディング**: `px-4 md:px-6`

---

### 4-3. SideMenu コンポーネント

**ファイルパス:** `src/components/Layout/SideMenu.tsx`

```tsx
import { Button } from '@/components/ui/button';
import { cn } from '@/lib/utils';
import { Home, Users, Settings, FileText } from 'lucide-react';

interface SideMenuProps {
  isOpen: boolean;
  onClose?: () => void;
}

export const SideMenu: React.FC<SideMenuProps> = ({ isOpen, onClose }) => {
  return (
    <aside className={cn(
      "fixed left-0 top-16 h-[calc(100vh-4rem)] bg-card border-r border-border transition-all duration-300 z-40",
      isOpen ? "w-64" : "w-0"
    )}>
      <div className={cn("h-full flex flex-col overflow-y-auto", !isOpen && "hidden")}>
        <div className="flex-1">
          <div className="p-4 space-y-6">
            {/* ホームセクション */}
            <div className="space-y-2">
              <h3 className="text-xs font-semibold text-muted-foreground uppercase tracking-wider px-2">
                ホーム
              </h3>
              <Button variant="ghost" className="w-full justify-start">
                <Home className="h-4 w-4 mr-2" />
                ダッシュボード
              </Button>
            </div>

            {/* プロジェクトセクション */}
            <div className="space-y-2">
              <h3 className="text-xs font-semibold text-muted-foreground uppercase tracking-wider px-2">
                プロジェクト
              </h3>
              <Button variant="ghost" className="w-full justify-start">
                <FileText className="h-4 w-4 mr-2" />
                タスク一覧
              </Button>
              <Button variant="ghost" className="w-full justify-start">
                <Users className="h-4 w-4 mr-2" />
                メンバー
              </Button>
            </div>

            {/* その他セクション */}
            <div className="space-y-2">
              <h3 className="text-xs font-semibold text-muted-foreground uppercase tracking-wider px-2">
                その他
              </h3>
              <Button variant="ghost" className="w-full justify-start">
                <Settings className="h-4 w-4 mr-2" />
                設定
              </Button>
            </div>
          </div>
        </div>

        {/* フッター */}
        <div className="border-t border-border p-4">
          <div className="text-xs text-muted-foreground">
            <div className="font-semibold">アプリ名</div>
            <div>v1.0.0</div>
          </div>
        </div>
      </div>
    </aside>
  );
};
```

#### 特徴

- **固定位置**: `fixed left-0 top-16`
- **アニメーション**: `transition-all duration-300`
- **開閉制御**: `w-64` / `w-0`
- **セクション分け**: ホーム、プロジェクト、その他
- **スクロール対応**: `overflow-y-auto`

---

### 4-4. MainLayout 統合コンポーネント

**ファイルパス:** `src/components/Layout/MainLayout.tsx`

```tsx
import { useState } from 'react';
import { CommonHeader } from './CommonHeader';
import { SideMenu } from './SideMenu';
import { cn } from '@/lib/utils';

interface MainLayoutProps {
  children: React.ReactNode;
}

export const MainLayout: React.FC<MainLayoutProps> = ({ children }) => {
  const [sidebarOpen, setSidebarOpen] = useState(true);

  return (
    <div className="min-h-screen bg-background">
      {/* ヘッダー */}
      <CommonHeader 
        onMenuToggle={() => setSidebarOpen(!sidebarOpen)}
        title="アプリ名"
        subtitle="サブタイトル"
      />

      {/* サイドメニュー */}
      <SideMenu 
        isOpen={sidebarOpen}
        onClose={() => setSidebarOpen(false)}
      />

      {/* メインコンテンツ */}
      <main className={cn(
        "transition-all duration-300 pt-16",
        sidebarOpen ? "ml-64" : "ml-0"
      )}>
        <div className="h-[calc(100vh-4rem)] overflow-y-auto">
          <div className="container mx-auto p-6">
            {children}
          </div>
        </div>
      </main>
    </div>
  );
};
```

#### 特徴

- **状態管理**: サイドバーの開閉状態を管理
- **スムーズな遷移**: `transition-all duration-300`
- **レスポンシブマージン**: サイドバー開閉に応じて `ml-64` / `ml-0`
- **スクロール対応**: メインコンテンツ内でスクロール可能

---

## ⚠️ 注意事項

- ✅ コンポーネントは `src/components/Layout/` に配置
- ✅ ThemeToggle コンポーネントは Step 5 で実装
- ✅ Avatar コンポーネントは shadcn/ui から追加が必要
- ⚠️ 高さ計算は `h-[calc(100vh-4rem)]` で固定ヘッダー分を引く

---

## ✅ 完了条件

- [ ] `src/components/Layout/CommonHeader.tsx` が作成されている
- [ ] `src/components/Layout/SideMenu.tsx` が作成されている
- [ ] `src/components/Layout/MainLayout.tsx` が作成されている
- [ ] サイドバーの開閉が正しく動作する
- [ ] ヘッダーが固定表示される
- [ ] レスポンシブデザインが機能する

---

## 🧪 動作確認

### Avatarコンポーネントの追加

```bash
npx shadcn@latest add avatar
```

### テスト用コード

```tsx
// src/App.tsx
import { MainLayout } from '@/components/Layout/MainLayout';
import './globals.css';

function App() {
  return (
    <MainLayout>
      <div className="space-y-6">
        <h1 className="text-3xl font-bold">ダッシュボード</h1>
        <p className="text-muted-foreground">レイアウトテスト</p>
      </div>
    </MainLayout>
  );
}

export default App;
```

### 確認ポイント

- ✅ ヘッダーが画面上部に固定されている
- ✅ メニューボタンでサイドバーが開閉する
- ✅ サイドバー開閉時にメインコンテンツの幅が調整される
- ✅ スクロールが正しく機能する
- ✅ ライト・ダークモード両方で表示が適切

---

## 📖 関連ドキュメント

- [Lucide Icons](https://lucide.dev/) - アイコン一覧
- [shadcn/ui Avatar](https://ui.shadcn.com/docs/components/avatar)

---

## 🔄 次のステップ

レイアウトコンポーネントの実装が完了したら、次はダークモード対応に進みます。

👉 **[PHASE2_05_DARK_MODE.md](./PHASE2_05_DARK_MODE.md)** に進む
