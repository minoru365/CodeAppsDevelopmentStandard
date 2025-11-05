# Phase 5: CRUD操作実装

## 📋 このドキュメントについて

このドキュメントでは、Create（作成）、Read（読取）、Update（更新）、Delete（削除）操作の実装方法について説明します。

---

> **📌 SDK APIバージョン**  
> このドキュメントは **Power Apps SDK v0.3.x** を使用しています。
>
> - 成功判定: `result.success` ✅ (旧: `result.success` ❌)
> - データ取得: `result.data` ✅ (旧: `result.data` ❌)

---

## CRUD操作の基本

### 基本パターン

```typescript
// すべての操作でIOperationResultを返す
const result = await ServiceClass.operation();

if (result.success) {
  // 成功時の処理
} else {
  // エラー処理
  console.error(result.error);
}
```

---

## Create（作成）

### 基本的な作成操作

```typescript
import { SystemUsersService } from '../generated/services/SystemUsersService';
import type { SystemUsersCreateInput } from '../generated/models/SystemUsersModel';

async function createUser() {
  const newUser: SystemUsersCreateInput = {
    fullname: '山田 太郎',
    internalemailaddress: 'yamada@example.com'
  };

  const result = await SystemUsersService.create(newUser);

  if (result.success && result.data) {
    console.log('作成成功。新しいID:', result.data);
    return result.data; // 新しいレコードのID
  } else {
    console.error('作成失敗:', result.error);
    throw new Error(result.error?.message || '作成に失敗しました');
  }
}
```

### カスタムフックでの作成

```typescript
export const useSystemUsers = () => {
  const { isInitialized } = usePowerPlatform();
  const [users, setUsers] = useState<SystemUsers[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const createUser = async (data: SystemUsersCreateInput) => {
    if (!isInitialized) {
      throw new Error('SDK not initialized');
    }

    try {
      setLoading(true);
      setError(null);

      const result = await SystemUsersService.create(data);

      if (result.success && result.data) {
        // 作成成功後、リストを再取得
        await loadUsers();
        return result.data;
      } else {
        throw new Error(result.error?.message || '作成に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      setError(errorMessage);
      throw err;
    } finally {
      setLoading(false);
    }
  };

  return { users, loading, error, createUser };
};
```

---

## Read（読取）

### 全件取得

```typescript
const result = await SystemUsersService.getAll({
  select: ['systemuserid', 'fullname', 'internalemailaddress'],
  filter: 'isdisabled eq false',
  orderBy: 'fullname asc',
  top: 100
});

if (result.success && result.data) {
  console.log('取得成功:', result.data);
}
```

### ID指定で1件取得

```typescript
const userId = 'a1b2c3d4-e5f6-7890-abcd-ef1234567890';

const result = await SystemUsersService.getById(userId, {
  select: ['systemuserid', 'fullname', 'internalemailaddress']
});

if (result.success && result.data) {
  console.log('ユーザー:', result.data);
}
```

### フィルタリング

```typescript
// 単一条件
const result1 = await SystemUsersService.getAll({
  filter: 'isdisabled eq false'
});

// 複数条件（AND）
const result2 = await SystemUsersService.getAll({
  filter: 'isdisabled eq false and domainname eq \'example.com\''
});

// 複数条件（OR）
const result3 = await SystemUsersService.getAll({
  filter: '(isdisabled eq false) or (fullname eq \'山田 太郎\')'
});
```

---

## Update（更新）

### 基本的な更新操作

```typescript
import type { SystemUsersUpdateInput } from '../generated/models/SystemUsersModel';

async function updateUser(userId: string) {
  const updates: SystemUsersUpdateInput = {
    fullname: '山田 太郎（更新）',
    internalemailaddress: 'yamada.updated@example.com'
  };

  const result = await SystemUsersService.update(userId, updates);

  if (result.success) {
    console.log('更新成功');
  } else {
    console.error('更新失敗:', result.error);
    throw new Error(result.error?.message || '更新に失敗しました');
  }
}
```

### カスタムフックでの更新

```typescript
export const useSystemUsers = () => {
  // ... 他のstate

  const updateUser = async (userId: string, data: SystemUsersUpdateInput) => {
    if (!isInitialized) {
      throw new Error('SDK not initialized');
    }

    try {
      setLoading(true);
      setError(null);

      const result = await SystemUsersService.update(userId, data);

      if (result.success) {
        // 更新成功後、リストを再取得
        await loadUsers();
      } else {
        throw new Error(result.error?.message || '更新に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      setError(errorMessage);
      throw err;
    } finally {
      setLoading(false);
    }
  };

  return { users, loading, error, updateUser };
};
```

---

## Delete（削除）

### 基本的な削除操作

```typescript
async function deleteUser(userId: string) {
  const result = await SystemUsersService.delete(userId);

  if (result.success) {
    console.log('削除成功');
  } else {
    console.error('削除失敗:', result.error);
    throw new Error(result.error?.message || '削除に失敗しました');
  }
}
```

### カスタムフックでの削除

```typescript
export const useSystemUsers = () => {
  // ... 他のstate

  const deleteUser = async (userId: string) => {
    if (!isInitialized) {
      throw new Error('SDK not initialized');
    }

    try {
      setLoading(true);
      setError(null);

      const result = await SystemUsersService.delete(userId);

      if (result.success) {
        // 削除成功後、リストを再取得
        await loadUsers();
      } else {
        throw new Error(result.error?.message || '削除に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      setError(errorMessage);
      throw err;
    } finally {
      setLoading(false);
    }
  };

  return { users, loading, error, deleteUser };
};
```

---

## 完全なカスタムフック例

```typescript
import { useState, useEffect, useCallback } from 'react';
import { usePowerPlatform } from '@microsoft/power-apps';
import { SystemUsersService } from '../generated/services/SystemUsersService';
import type { 
  SystemUsers, 
  SystemUsersCreateInput, 
  SystemUsersUpdateInput 
} from '../generated/models/SystemUsersModel';

export const useSystemUsers = () => {
  const { isInitialized } = usePowerPlatform();
  const [users, setUsers] = useState<SystemUsers[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  // 全件取得
  const loadUsers = useCallback(async () => {
    if (!isInitialized) return;

    try {
      setLoading(true);
      setError(null);

      const result = await SystemUsersService.getAll({
        select: ['systemuserid', 'fullname', 'internalemailaddress', 'isdisabled'],
        filter: 'isdisabled eq false',
        orderBy: 'fullname asc'
      });

      if (result.success && result.data) {
        setUsers(result.data);
      } else {
        throw new Error(result.error?.message || '取得に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      setError(errorMessage);
    } finally {
      setLoading(false);
    }
  }, [isInitialized]);

  // 作成
  const createUser = async (data: SystemUsersCreateInput) => {
    if (!isInitialized) throw new Error('SDK not initialized');

    setLoading(true);
    setError(null);

    try {
      const result = await SystemUsersService.create(data);

      if (result.success && result.data) {
        await loadUsers();
        return result.data;
      } else {
        throw new Error(result.error?.message || '作成に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      setError(errorMessage);
      throw err;
    } finally {
      setLoading(false);
    }
  };

  // 更新
  const updateUser = async (userId: string, data: SystemUsersUpdateInput) => {
    if (!isInitialized) throw new Error('SDK not initialized');

    setLoading(true);
    setError(null);

    try {
      const result = await SystemUsersService.update(userId, data);

      if (result.success) {
        await loadUsers();
      } else {
        throw new Error(result.error?.message || '更新に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      setError(errorMessage);
      throw err;
    } finally {
      setLoading(false);
    }
  };

  // 削除
  const deleteUser = async (userId: string) => {
    if (!isInitialized) throw new Error('SDK not initialized');

    setLoading(true);
    setError(null);

    try {
      const result = await SystemUsersService.delete(userId);

      if (result.success) {
        await loadUsers();
      } else {
        throw new Error(result.error?.message || '削除に失敗しました');
      }
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error';
      setError(errorMessage);
      throw err;
    } finally {
      setLoading(false);
    }
  };

  // 初期ロード
  useEffect(() => {
    if (isInitialized) {
      loadUsers();
    }
  }, [isInitialized, loadUsers]);

  return {
    users,
    loading,
    error,
    refetch: loadUsers,
    createUser,
    updateUser,
    deleteUser,
    isInitialized
  };
};
```

---

## ✅ CRUD実装チェックリスト

### Create（作成）

- [ ] `SystemUsersCreateInput`型を使用している
- [ ] `IOperationResult`でエラーハンドリングしている
- [ ] 作成成功後にリストを更新している
- [ ] エラー時に適切なメッセージを表示している

### Read（読取）

- [ ] `select`で必要なフィールドのみ取得している
- [ ] `filter`で適切な条件を指定している
- [ ] `orderBy`でソートしている
- [ ] ページングを考慮している（必要な場合）

### Update（更新）

- [ ] `SystemUsersUpdateInput`型を使用している
- [ ] 必須フィールドを含めている
- [ ] 更新成功後にリストを更新している
- [ ] 楽観的ロックを考慮している（必要な場合）

### Delete（削除）

- [ ] 削除確認ダイアログを表示している
- [ ] 削除成功後にリストから削除している
- [ ] カスケード削除の影響を理解している
- [ ] エラー時に適切なメッセージを表示している

---

## 📖 関連ドキュメント

- [モックデータからリアルデータへの移行](./PHASE5_04_MOCK_TO_REAL.md)
- [UI統合とコンポーネント](./PHASE5_06_UI_INTEGRATION.md)
- [実装例](./PHASE5_07_EXAMPLES.md)

