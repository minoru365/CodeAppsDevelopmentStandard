# Phase 5: 実装例とサンプルコード

## 📋 このドキュメントについて

このドキュメントでは、Office 365 Users、Dataverseテーブル、SQL Serverとの統合における実際のコード例を提供します。

---

## 例1: Office 365 Users統合

### 接続セットアップ

```powershell
# Power Appsポータルで Office 365 Users 接続を作成
# URLから接続IDを取得

pac code add-data-source `
  -a "shared_office365users" `
  -c "a1b2c3d4-e5f6-7890-abcd-ef1234567890"

npm run build
```

### カスタムフック

```typescript
// src/hooks/useOffice365Users.ts
import { useState, useEffect, useCallback } from 'react';
import { usePowerPlatform } from '@microsoft/power-apps';
import { Office365UsersService } from '../generated/services/Office365UsersService';
import type { User } from '../generated/models/Office365UsersModel';

export const useOffice365Users = () => {
  const { isInitialized } = usePowerPlatform();
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const loadUsers = useCallback(async () => {
    if (!isInitialized) return;

    try {
      setLoading(true);
      setError(null);

      const result = await Office365UsersService.getAll({
        select: ['Id', 'DisplayName', 'Mail', 'JobTitle'],
        top: 100
      });

      if (result.isSuccess && result.value) {
        setUsers(result.value);
      } else {
        throw new Error(result.error?.message || 'ユーザー取得に失敗しました');
      }
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Unknown error');
    } finally {
      setLoading(false);
    }
  }, [isInitialized]);

  useEffect(() => {
    if (isInitialized) {
      loadUsers();
    }
  }, [isInitialized, loadUsers]);

  return { users, loading, error, refetch: loadUsers };
};
```

---

## 例2: Dataverseカスタムテーブル統合

### テーブル定義

```text
テーブル論理名: geek_project_task
主キー: geek_project_taskid

フィールド:
- geek_name (文字列): タスク名
- geek_description (複数行テキスト): 説明
- geek_status (Choice): ステータス（未着手=1, 進行中=2, 完了=3）
- geek_priority (Choice): 優先度（低=1, 中=2, 高=3）
- geek_assignedto (Lookup): 担当者 (SystemUsers)
- geek_duedate (日付): 期限
```

### 接続セットアップ

```powershell
# customization.xmlをワークスペースルートに配置

pac code add-data-source `
  -a "shared_commondataserviceforapps" `
  -c "a1b2c3d4-e5f6-7890-abcd-ef1234567890" `
  -t "geek_project_task"

# ⚠️ dataSourceNameを修正（既知の問題）
# src/generated/services/GeekProjectTasksService.ts
# dataSourceName = 'geek_project_tasks' に修正

npm run build
```

### カスタムフック

```typescript
// src/hooks/useTasks.ts
import { useState, useEffect, useCallback } from 'react';
import { usePowerPlatform } from '@microsoft/power-apps';
import { GeekProjectTasksService } from '../generated/services/GeekProjectTasksService';
import type { 
  GeekProjectTasks, 
  GeekProjectTasksCreateInput 
} from '../generated/models/GeekProjectTasksModel';

export const useTasks = () => {
  const { isInitialized } = usePowerPlatform();
  const [tasks, setTasks] = useState<GeekProjectTasks[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const loadTasks = useCallback(async () => {
    if (!isInitialized) return;

    try {
      setLoading(true);
      setError(null);

      const result = await GeekProjectTasksService.getAll({
        select: [
          'geek_project_taskid',
          'geek_name',
          'geek_description',
          'geek_status',
          'geek_priority',
          'geek_duedate'
        ],
        expand: [
          {
            navigationProperty: 'geek_AssignedTo',
            select: ['systemuserid', 'fullname']
          }
        ],
        filter: 'statecode eq 0', // アクティブのみ
        orderBy: 'geek_duedate asc'
      });

      if (result.isSuccess && result.value) {
        setTasks(result.value);
      } else {
        throw new Error(result.error?.message || 'タスク取得に失敗しました');
      }
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Unknown error');
    } finally {
      setLoading(false);
    }
  }, [isInitialized]);

  const createTask = async (data: GeekProjectTasksCreateInput) => {
    if (!isInitialized) throw new Error('SDK not initialized');

    try {
      setLoading(true);
      const result = await GeekProjectTasksService.create(data);

      if (result.isSuccess) {
        await loadTasks();
        return result.value;
      } else {
        throw new Error(result.error?.message || 'タスク作成に失敗しました');
      }
    } catch (err) {
      throw err;
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    if (isInitialized) {
      loadTasks();
    }
  }, [isInitialized, loadTasks]);

  return { tasks, loading, error, refetch: loadTasks, createTask };
};
```

### コンポーネント

```typescript
// src/components/TaskList.tsx
import { useTasks } from '../hooks/useTasks';

export function TaskList() {
  const { tasks, loading, error, refetch } = useTasks();

  if (loading) return <div>読み込み中...</div>;
  if (error) return <div>エラー: {error}</div>;

  return (
    <div className="space-y-4">
      <div className="flex justify-between items-center">
        <h2 className="text-2xl font-bold">タスク一覧</h2>
        <button onClick={refetch} className="btn-primary">
          更新
        </button>
      </div>

      <div className="grid gap-4">
        {tasks.map((task) => (
          <TaskCard key={task.geek_project_taskid} task={task} />
        ))}
      </div>
    </div>
  );
}

function TaskCard({ task }: { task: GeekProjectTasks }) {
  const getStatusLabel = (status: number) => {
    const labels = { 1: '未着手', 2: '進行中', 3: '完了' };
    return labels[status as keyof typeof labels] || '不明';
  };

  const getPriorityColor = (priority: number) => {
    const colors = {
      1: 'bg-green-100 text-green-800',
      2: 'bg-yellow-100 text-yellow-800',
      3: 'bg-red-100 text-red-800'
    };
    return colors[priority as keyof typeof colors] || 'bg-gray-100';
  };

  return (
    <div className="border rounded-lg p-4 hover:shadow-md transition-shadow">
      <div className="flex justify-between items-start mb-2">
        <h3 className="font-semibold text-lg">{task.geek_name}</h3>
        <span className={`px-2 py-1 rounded text-xs ${getPriorityColor(task.geek_priority)}`}>
          優先度: {task.geek_priority}
        </span>
      </div>
      
      <p className="text-gray-600 mb-2">{task.geek_description}</p>
      
      <div className="flex gap-4 text-sm text-gray-500">
        <span>ステータス: {getStatusLabel(task.geek_status)}</span>
        <span>期限: {new Date(task.geek_duedate).toLocaleDateString('ja-JP')}</span>
        {task.geek_AssignedTo && (
          <span>担当者: {task.geek_AssignedTo.fullname}</span>
        )}
      </div>
    </div>
  );
}
```

---

## 例3: SQL Server統合

### 接続セットアップ

```powershell
# Power AppsポータルでSQL Server接続を作成
# 接続IDとデータセット名を取得

pac code add-data-source `
  -a "shared_sql" `
  -c "a1b2c3d4-e5f6-7890-abcd-ef1234567890" `
  -t "[dbo].[Products]" `
  -d "myserver.database.windows.net,mydb"

npm run build
```

### カスタムフック

```typescript
// src/hooks/useProducts.ts
import { useState, useEffect, useCallback } from 'react';
import { usePowerPlatform } from '@microsoft/power-apps';
import { ProductsService } from '../generated/services/ProductsService';
import type { Products } from '../generated/models/ProductsModel';

export const useProducts = () => {
  const { isInitialized } = usePowerPlatform();
  const [products, setProducts] = useState<Products[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const loadProducts = useCallback(async () => {
    if (!isInitialized) return;

    try {
      setLoading(true);
      setError(null);

      const result = await ProductsService.getAll({
        select: ['ProductId', 'ProductName', 'Price', 'Stock'],
        filter: 'Stock gt 0', // 在庫があるもののみ
        orderBy: 'ProductName asc'
      });

      if (result.isSuccess && result.value) {
        setProducts(result.value);
      } else {
        throw new Error(result.error?.message || '製品取得に失敗しました');
      }
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Unknown error');
    } finally {
      setLoading(false);
    }
  }, [isInitialized]);

  useEffect(() => {
    if (isInitialized) {
      loadProducts();
    }
  }, [isInitialized, loadProducts]);

  return { products, loading, error, refetch: loadProducts };
};
```

---

## 実装パターン比較

| コネクター | 接続タイプ | スキーマ取得 | 特徴 |
|-----------|----------|------------|-----|
| **Office 365 Users** | connectionReferences | 不要 | 読み取り専用、認証済みユーザー情報取得 |
| **Dataverse** | databaseReferences | customization.xml | フルCRUD、リレーション、Choice値対応 |
| **SQL Server** | connectionReferences | 不要 | フルCRUD、SQL固有機能（ストアドプロシージャ等） |

---

## ✅ 実装例チェックリスト

### 接続設定

- [ ] 適切なコネクタータイプを使用している
- [ ] 接続IDを正しく取得している
- [ ] スキーマファイルを配置している（Dataverseの場合）

### カスタムフック

- [ ] SDK初期化チェックを実装している
- [ ] エラーハンドリングを実装している
- [ ] ローディング状態を管理している
- [ ] 適切な型定義を使用している

### コンポーネント

- [ ] プレゼンテーションとロジックを分離している
- [ ] 適切なUIフィードバックを提供している
- [ ] アクセシビリティを考慮している

---

## 📖 関連ドキュメント

- [Phase 5 概要](./PHASE5_01_OVERVIEW.md)
- [接続セットアップ](./PHASE5_02_CONNECTION_SETUP.md)
- [サービスクラス生成](./PHASE5_03_SERVICE_GENERATION.md)
- [CRUD操作実装](./PHASE5_05_CRUD_IMPLEMENTATION.md)
- [Dataverseトラブルシューティング](../DATAVERSE_TROUBLESHOOTING.md)
