# Phase 5: モックデータからリアルデータへの移行

## 📋 このドキュメントについて

このドキュメントでは、カスタムフックとReactコンポーネントを実装し、モックデータを完全に削除してDataverseの実データに接続する方法について説明します。

---

> **📌 SDK APIバージョン**  
> このドキュメントは **Power Apps SDK v0.3.x** を使用しています。
>
> - 成功判定: `result.success` ✅ (旧: `result.isSuccess` ❌)
> - データ取得: `result.data` ✅ (旧: `result.value` ❌)

---

## Step 4: データ統合の実装

### 4-1. カスタムフックの作成

**ファイルパス:** `src/hooks/useSystemUsers.ts`

```typescript
import { useState, useEffect, useCallback } from 'react';
import { SystemUsersService } from '../generated/services/SystemUsersService';
import type { SystemUsers } from '../generated/models/SystemUsersModel';

export const useSystemUsers = () => {
  const [users, setUsers] = useState<SystemUsers[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const loadUsers = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      
      // ✅ Power Apps SDK経由でデータ取得
      const result = await SystemUsersService.getAll({
        select: ['systemuserid', 'fullname', 'internalemailaddress', 'isdisabled'],
        filter: 'isdisabled eq false',
        orderBy: 'fullname asc',
        top: 100
      });
      
      // ✅ IOperationResult で型安全なエラーハンドリング
      if (result.success && result.data) {
        setUsers(result.data);
      } else {
        throw new Error(result.error?.message || 'ユーザー一覧の取得に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      console.error('Error fetching SystemUsers:', err);
      setError(errorMessage);
    } finally {
      setLoading(false);
    }
  }, []);

  // コンポーネントマウント時に自動でデータ取得
  useEffect(() => {
    loadUsers();
  }, [loadUsers]);

  return { 
    users, 
    loading, 
    error, 
    refetch: loadUsers
  };
};
```

> **💡 SDK初期化について**  
> Power Apps SDKの初期化は`PowerProvider`が自動的に行うため、カスタムフック内で初期化チェックは不要です。  
> `PowerProvider`で`initialize()`が完了してからアプリが表示されるため、すべてのフックやコンポーネントでSDKを安全に使用できます。

---

### 4-2. コンポーネントでの使用

**ファイルパス:** `src/components/UserList.tsx`

```typescript
import { useSystemUsers } from '../hooks/useSystemUsers';

export function UserList() {
  const { users, loading, error, refetch } = useSystemUsers();

  // ローディング状態
  if (loading) {
    return (
      <div className="flex items-center justify-center p-8">
        <div className="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
        <span className="ml-3">データ読み込み中...</span>
      </div>
    );
  }

  // エラー状態
  if (error) {
    return (
      <div className="bg-red-50 border border-red-200 rounded-lg p-4">
        <h3 className="text-red-800 font-semibold mb-2">エラーが発生しました</h3>
        <p className="text-red-600">{error}</p>
        <button
          onClick={refetch}
          className="mt-4 px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700"
        >
          再試行
        </button>
      </div>
    );
  }

  // データ表示
  return (
    <div>
      <div className="flex justify-between items-center mb-4">
        <h2 className="text-2xl font-bold">ユーザー一覧</h2>
        <button
          onClick={refetch}
          className="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
        >
          更新
        </button>
      </div>

      <div className="grid gap-4">
        {users.map((user) => (
          <div
            key={user.systemuserid}
            className="border rounded-lg p-4 hover:shadow-md transition-shadow"
          >
            <h3 className="font-semibold text-lg">{user.fullname}</h3>
            <p className="text-gray-600">{user.internalemailaddress}</p>
          </div>
        ))}
      </div>

      {users.length === 0 && (
        <div className="text-center py-8 text-gray-500">
          データが見つかりません
        </div>
      )}
    </div>
  );
}
```

---

### 4-3. App.tsxでの統合

**ファイルパス:** `src/App.tsx`

```typescript
import { UserList } from './components/UserList';

export function App() {
  return (
    <div className="min-h-screen bg-gray-50">
      <header className="bg-white shadow">
        <div className="max-w-7xl mx-auto py-6 px-4">
          <h1 className="text-3xl font-bold text-gray-900">
            My Power Apps Code App
          </h1>
        </div>
      </header>
      
      <main className="max-w-7xl mx-auto py-6 px-4">
        <UserList />
      </main>
    </div>
  );
}
```

> **💡 SDK初期化について**  
> `PowerProvider`がSDK初期化を管理するため、`App.tsx`で初期化チェックは不要です。  
> アプリ全体が`PowerProvider`でラップされ、初期化完了後に表示されます。

---

## 4-4. モックデータの削除

実データ接続が確認できたら、モックデータファイルを削除します。

```powershell
# モックデータファイルを削除
Remove-Item src/data/mockUsers.ts -Force

# または、モックデータフォルダごと削除
Remove-Item -Recurse -Force src/data/
```

---

## 💡 重要なパターン

### パターン1: PowerProviderによる自動初期化

```typescript
// PowerProvider が SDK の初期化を管理
// main.tsx または App.tsx のルートで使用
import PowerProvider from './components/PowerProvider';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <PowerProvider>
      <App />
    </PowerProvider>
  </React.StrictMode>
);
```

> **💡 重要**  
> `PowerProvider`がSDK初期化を行うため、カスタムフックやコンポーネント内で`usePowerPlatform().isInitialized`チェックは不要です。

### パターン2: IOperationResultでのエラーハンドリング

```typescript
const result = await SystemUsersService.getAll();

// ✅ 型安全なエラーハンドリング
if (result.success && result.data) {
  setData(result.data);
} else {
  console.error(result.error);
  setError(result.error?.message || 'エラーが発生しました');
}
```

### パターン3: ローディング状態の管理

```typescript
try {
  setLoading(true);  // ✅ 開始時にtrue
  const result = await fetchData();
  // ... 処理
} finally {
  setLoading(false);  // ✅ 必ず終了時にfalse
}
```

---

## ⚠️ よくある間違い

### 間違い1: PowerProviderの使用忘れ

```typescript
// ❌ 悪い例 - PowerProvider なし
ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// ✅ 良い例 - PowerProvider で SDK 初期化
import PowerProvider from './components/PowerProvider';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <PowerProvider>
      <App />
    </PowerProvider>
  </React.StrictMode>
);
```

### 間違い2: エラーハンドリングなし

```typescript
// ❌ 悪い例
const result = await SystemUsersService.getAll();
setUsers(result.data);  // result.dataがundefinedの可能性

// ✅ 良い例
const result = await SystemUsersService.getAll();
if (result.success && result.data) {
  setUsers(result.data);
} else {
  setError(result.error?.message || 'データ取得に失敗しました');
}
```

### 間違い3: ローディング状態の管理忘れ

```typescript
// ❌ 悪い例
const loadData = async () => {
  const result = await fetchData();
  setData(result.data);
  // loadingがtrueのまま
};

// ✅ 良い例
const loadData = async () => {
  try {
    setLoading(true);
    const result = await fetchData();
    if (result.success && result.data) {
      setData(result.data);
    }
  } finally {
    setLoading(false);  // ✅ 必ず終了
  }
};
```

---

## ✅ Step 4 完了チェックリスト

### PowerProvider設定

- [ ] `PowerProvider`コンポーネントが作成されている
- [ ] `main.tsx`または`App.tsx`のルートで`PowerProvider`を使用している
- [ ] `initialize()`が`useEffect`内で正しく呼び出されている

### カスタムフック

- [ ] カスタムフックが作成されている
- [ ] `IOperationResult`でエラーハンドリングしている
- [ ] ローディング状態を管理している
- [ ] エラー状態を管理している

### コンポーネント

- [ ] ローディング表示がある
- [ ] エラー表示がある
- [ ] データが正しく表示される
- [ ] 再取得機能がある

### モックデータ削除

- [ ] モックデータファイルを削除した
- [ ] モックデータのimportを削除した
- [ ] アプリが実データで動作する

---

## 🔗 次のステップ

データ統合が完了したら、次は **[CRUD操作実装](./PHASE5_05_CRUD_IMPLEMENTATION.md)** または **[実装例](./PHASE5_07_EXAMPLES.md)** を参照してください。

---

## 📖 関連ドキュメント

- [Dataverseトラブルシューティング](../DATAVERSE_TROUBLESHOOTING.md)
- [Phase 5 概要](./PHASE5_01_OVERVIEW.md)
- [サービスクラス生成](./PHASE5_03_SERVICE_GENERATION.md)
