# Phase 2 Step 6: MVP機能実装

## 📋 概要

このステップでは、MVP（Minimum Viable Product）として最小限の機能を実装します。

---

## 🎯 実施内容

1. ダッシュボードページの実装
2. 基本的なUIパターンの実装
3. サンプルデータの表示

---

## 📝 実装手順

### 6-1. ダッシュボードページの実装

**ファイルパス:** `src/pages/Dashboard.tsx`

```tsx
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Users, CheckCircle, Clock, TrendingUp } from 'lucide-react';

export const Dashboard = () => {
  return (
    <div className="space-y-6">
      {/* ウェルカムメッセージ */}
      <div>
        <h1 className="text-3xl font-bold text-foreground">
          ダッシュボード
        </h1>
        <p className="text-muted-foreground mt-2">
          プロジェクトの概要と進捗状況
        </p>
      </div>

      {/* 統計カードグリッド */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
        {/* 総ユーザー数 */}
        <Card>
          <CardContent className="pt-6">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm text-muted-foreground">総ユーザー</p>
                <p className="text-3xl font-bold">1,234</p>
              </div>
              <Users className="h-10 w-10 text-muted-foreground" />
            </div>
          </CardContent>
        </Card>

        {/* 完了タスク */}
        <Card>
          <CardContent className="pt-6">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm text-muted-foreground">完了タスク</p>
                <p className="text-3xl font-bold text-green-600 dark:text-green-400">89</p>
              </div>
              <CheckCircle className="h-10 w-10 text-green-600 dark:text-green-400" />
            </div>
          </CardContent>
        </Card>

        {/* 進行中タスク */}
        <Card>
          <CardContent className="pt-6">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm text-muted-foreground">進行中</p>
                <p className="text-3xl font-bold text-blue-600 dark:text-blue-400">45</p>
              </div>
              <Clock className="h-10 w-10 text-blue-600 dark:text-blue-400" />
            </div>
          </CardContent>
        </Card>

        {/* 進捗率 */}
        <Card>
          <CardContent className="pt-6">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-sm text-muted-foreground">進捗率</p>
                <p className="text-3xl font-bold text-purple-600 dark:text-purple-400">78%</p>
              </div>
              <TrendingUp className="h-10 w-10 text-purple-600 dark:text-purple-400" />
            </div>
          </CardContent>
        </Card>
      </div>

      {/* プロジェクトリスト */}
      <Card>
        <CardHeader>
          <CardTitle>最近のプロジェクト</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="space-y-4">
            {[1, 2, 3].map((i) => (
              <div 
                key={i} 
                className="p-3 rounded-lg hover:bg-muted/50 transition-colors cursor-pointer"
              >
                <h3 className="font-semibold">プロジェクト {i}</h3>
                <p className="text-sm text-muted-foreground">説明テキスト...</p>
              </div>
            ))}
          </div>
        </CardContent>
      </Card>
    </div>
  );
};
```

---

### 6-2. App.tsx での統合

**ファイルパス:** `src/App.tsx`

```tsx
import { ThemeProvider } from "@/contexts/ThemeContext";
import { MainLayout } from "@/components/Layout/MainLayout";
import { Dashboard } from "@/pages/Dashboard";
import "./globals.css";

const App = () => (
  <ThemeProvider defaultTheme="system" storageKey="code-app-ui-theme">
    <MainLayout>
      <Dashboard />
    </MainLayout>
  </ThemeProvider>
);

export default App;
```

---

### 6-3. 基本的なUIパターン

#### リストアイテム

```tsx
// コンポーネント例
export const ListItem = ({ title, description }: { title: string; description: string }) => (
  <div className="p-4 rounded-lg border border-border hover:bg-muted/50 transition-colors cursor-pointer">
    <h3 className="font-semibold text-foreground">{title}</h3>
    <p className="text-sm text-muted-foreground mt-1">{description}</p>
  </div>
);
```

#### ステータスバッジ

```tsx
// ステータス表示用バッジ
export const StatusBadge = ({ status }: { status: 'success' | 'warning' | 'error' }) => {
  const styles = {
    success: 'bg-green-50 dark:bg-green-950/20 text-green-600 dark:text-green-400',
    warning: 'bg-yellow-50 dark:bg-yellow-950/20 text-yellow-600 dark:text-yellow-400',
    error: 'bg-red-50 dark:bg-red-950/20 text-red-600 dark:text-red-400',
  };

  return (
    <span className={`px-2 py-1 rounded text-xs font-medium ${styles[status]}`}>
      {status}
    </span>
  );
};
```

---

## ⚠️ 注意事項

- ✅ MVP機能はシンプルに保つ
- ✅ サンプルデータを使用して動作確認
- ✅ すべてのコンポーネントでダークモード対応
- ⚠️ PowerProvider.tsx は変更しない
- ⚠️ Phase 5 でデータ統合を行うまでは静的データを使用

---

## ✅ 完了条件

- [ ] Dashboard ページが実装されている
- [ ] 統計カードが表示されている
- [ ] リストアイテムが表示されている
- [ ] ライト・ダークモード両方で正しく表示される
- [ ] レスポンシブデザインが機能している
- [ ] `npm run dev` で正しく表示される

---

## 🧪 動作確認

### 確認ポイント

1. **デスクトップ表示**
   - 統計カードが4列で表示される
   - すべての要素が適切に配置されている

2. **タブレット表示**
   - 統計カードが2列で表示される
   - レイアウトが崩れていない

3. **モバイル表示**
   - 統計カードが1列で表示される
   - スクロールが正しく機能する

4. **ダークモード**
   - すべての色が適切に表示される
   - コントラストが十分ある

---

## 📖 関連ドキュメント

- [Lucide Icons](https://lucide.dev/) - アイコン一覧
- [shadcn/ui Card](https://ui.shadcn.com/docs/components/card)

---

## 🔄 次のステップ

MVP機能の実装が完了したら、最後のエラーチェックと統合に進みます。

👉 **[PHASE2_07_ERROR_CHECK_INTEGRATION.md](./PHASE2_07_ERROR_CHECK_INTEGRATION.md)** に進む
