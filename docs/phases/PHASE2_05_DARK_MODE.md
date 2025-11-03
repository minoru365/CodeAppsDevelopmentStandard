# Phase 2 Step 5: ダークモード・ライトモード対応

## 📋 概要

このステップでは、ThemeContext、ThemeProvider、ThemeToggleコンポーネントを実装してテーマ切り替え機能を追加します。

---

## 🎯 実施内容

1. ThemeContext の作成
2. ThemeToggle コンポーネントの実装
3. App.tsx での統合
4. ダークモード対応のベストプラクティス

---

## 📝 実装手順

### 5-1. ThemeContext の作成

**ファイルパス:** `src/contexts/ThemeContext.tsx`

```tsx
import React, { createContext, useContext, useEffect, useState } from 'react';

type Theme = 'dark' | 'light' | 'system';

type ThemeProviderProps = {
  children: React.ReactNode;
  defaultTheme?: Theme;
  storageKey?: string;
};

type ThemeProviderState = {
  theme: Theme;
  setTheme: (theme: Theme) => void;
};

const initialState: ThemeProviderState = {
  theme: 'system',
  setTheme: () => null,
};

const ThemeProviderContext = createContext<ThemeProviderState>(initialState);

export function ThemeProvider({
  children,
  defaultTheme = 'system',
  storageKey = 'code-app-ui-theme',
  ...props
}: ThemeProviderProps) {
  const [theme, setTheme] = useState<Theme>(
    () => (localStorage.getItem(storageKey) as Theme) || defaultTheme
  );

  useEffect(() => {
    const root = window.document.documentElement;
    root.classList.remove('light', 'dark');

    if (theme === 'system') {
      const systemTheme = window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark'
        : 'light';
      root.classList.add(systemTheme);
      return;
    }

    root.classList.add(theme);
  }, [theme]);

  const value = {
    theme,
    setTheme: (theme: Theme) => {
      localStorage.setItem(storageKey, theme);
      setTheme(theme);
    },
  };

  return (
    <ThemeProviderContext.Provider {...props} value={value}>
      {children}
    </ThemeProviderContext.Provider>
  );
}

export const useTheme = () => {
  const context = useContext(ThemeProviderContext);

  if (context === undefined)
    throw new Error('useTheme must be used within a ThemeProvider');

  return context;
};
```

#### 機能説明

- **localStorage連携**: テーマ設定を永続化
- **システムテーマ検出**: `prefers-color-scheme` メディアクエリ対応
- **動的クラス切り替え**: `documentElement` に `dark` / `light` クラスを追加

---

### 5-2. ThemeToggle コンポーネント

**ファイルパス:** `src/components/ThemeToggle.tsx`

```tsx
import { Moon, Sun } from "lucide-react";
import { Button } from "@/components/ui/button";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { useTheme } from "@/contexts/ThemeContext";

export function ThemeToggle() {
  const { setTheme } = useTheme();

  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button variant="ghost" size="icon">
          <Sun className="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
          <Moon className="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
          <span className="sr-only">テーマ切り替え</span>
        </Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent align="end">
        <DropdownMenuItem onClick={() => setTheme("light")}>
          ライト
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("dark")}>
          ダーク
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("system")}>
          システム設定
        </DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  );
}
```

#### 必要なコンポーネントの追加

```bash
npx shadcn@latest add dropdown-menu
```

---

### 5-3. App.tsx での統合

**ファイルパス:** `src/App.tsx`

```tsx
import { ThemeProvider } from "@/contexts/ThemeContext";
import { Toaster } from "@/components/ui/toaster";
import { MainLayout } from "@/components/Layout/MainLayout";
import "./globals.css";

const App = () => (
  <ThemeProvider defaultTheme="system" storageKey="code-app-ui-theme">
    <MainLayout>
      {/* アプリのコンテンツ */}
      <div className="space-y-6">
        <h1 className="text-3xl font-bold">ダッシュボード</h1>
        <p className="text-muted-foreground">
          Code Apps へようこそ
        </p>
      </div>
    </MainLayout>
    <Toaster />
  </ThemeProvider>
);

export default App;
```

---

### 5-4. ダークモード対応のベストプラクティス

#### セマンティックカラーの使用

```tsx
// ✅ 推奨: セマンティックカラーを使用
<div className="bg-card text-card-foreground">
  コンテンツ
</div>

// ❌ 非推奨: ハードコードされたカラー
<div className="bg-white text-black">
  コンテンツ
</div>
```

#### ダークモード対応のコンポーネント例

```tsx
// 成功バッジ（ライト・ダーク対応）
<div className="bg-green-50 dark:bg-green-950/20 border border-green-200 dark:border-green-900 rounded-md p-2">
  <span className="text-green-600 dark:text-green-400 font-medium">完了</span>
</div>

// 警告カード（ライト・ダーク対応）
<Card className="border-yellow-200 dark:border-yellow-900">
  <CardContent className="bg-yellow-50 dark:bg-yellow-950/20 p-4">
    <p className="text-yellow-800 dark:text-yellow-200">警告メッセージ</p>
  </CardContent>
</Card>

// プログレスバー（ライト・ダーク対応）
<div className="w-full h-2 bg-muted rounded-full overflow-hidden">
  <div 
    className="h-full bg-purple-600 dark:bg-purple-400 transition-all" 
    style={{ width: '60%' }} 
  />
</div>

// エラー表示（ライト・ダーク対応）
<div className="bg-red-50 dark:bg-red-950/20 border border-red-200 dark:border-red-900 rounded-md p-4">
  <p className="text-red-600 dark:text-red-400">エラーが発生しました</p>
</div>
```

#### 画像とアイコンのダークモード対応

```tsx
// ロゴのダークモード対応
<img 
  src="/assets/logo.svg" 
  alt="Logo"
  className="h-8 dark:invert" 
/>

// アイコンの色調整
<Home className="h-5 w-5 text-muted-foreground hover:text-foreground transition-colors" />
```

---

## ⚠️ 注意事項

- ✅ すべての新規コンポーネントで `dark:` プレフィックスを使用
- ✅ セマンティックカラー変数（`bg-card`, `text-foreground` など）を優先
- ✅ カスタムカラーを使う場合は必ずダークモード対応を実装
- ⚠️ `bg-white` や `text-black` などの絶対値は避ける

---

## ✅ 完了条件

- [ ] `src/contexts/ThemeContext.tsx` が作成されている
- [ ] `src/components/ThemeToggle.tsx` が作成されている
- [ ] App.tsx に ThemeProvider が統合されている
- [ ] CommonHeader に ThemeToggle が配置されている
- [ ] テーマ切り替えが正しく動作する
- [ ] テーマ設定が localStorage に保存される
- [ ] システムテーマ設定が正しく検出される

---

## 🧪 動作確認

### テストシナリオ

1. **ライトモード→ダークモード切り替え**
   - ThemeToggle をクリック
   - 「ダーク」を選択
   - 全体の背景・テキストが切り替わることを確認

2. **設定の永続化**
   - テーマを変更
   - ページをリロード
   - 設定が保持されていることを確認

3. **システムテーマ連動**
   - 「システム設定」を選択
   - OS のダークモード設定を変更
   - アプリのテーマが連動して変わることを確認

### 確認用コード

```tsx
// テーマの動作確認用コンポーネント
export const ThemeTest = () => {
  const { theme } = useTheme();
  
  return (
    <div className="space-y-4 p-6">
      <p>現在のテーマ: {theme}</p>
      
      <div className="grid grid-cols-2 gap-4">
        <Card className="bg-green-50 dark:bg-green-950/20">
          <CardContent className="p-4">
            <span className="text-green-600 dark:text-green-400">完了</span>
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
};
```

---

## 📖 関連ドキュメント

- [TailwindCSS Dark Mode](https://tailwindcss.com/docs/dark-mode)
- [prefers-color-scheme MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)

---

## 🔄 次のステップ

ダークモード対応が完了したら、次はMVP機能の実装に進みます。

👉 **[PHASE2_06_MVP_IMPLEMENTATION.md](./PHASE2_06_MVP_IMPLEMENTATION.md)** に進む
