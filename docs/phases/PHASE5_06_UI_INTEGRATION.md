# Phase 5: UI統合とコンポーネント

## 📋 このドキュメントについて

このドキュメントでは、Reactコンポーネントを使用してDataverseデータをUIに統合する方法について説明します。

---

## 基本的なリストコンポーネント

```typescript
import { useSystemUsers } from '../hooks/useSystemUsers';

export function UserList() {
  const { users, loading, error, refetch } = useSystemUsers();

  if (loading) {
    return <LoadingSpinner message="データ読み込み中..." />;
  }

  if (error) {
    return <ErrorMessage error={error} onRetry={refetch} />;
  }

  return (
    <div className="space-y-4">
      <div className="flex justify-between items-center">
        <h2 className="text-2xl font-bold">ユーザー一覧</h2>
        <button onClick={refetch} className="btn-primary">
          更新
        </button>
      </div>

      <div className="grid gap-4">
        {users.map((user) => (
          <UserCard key={user.systemuserid} user={user} />
        ))}
      </div>

      {users.length === 0 && (
        <EmptyState message="データが見つかりません" />
      )}
    </div>
  );
}
```

---

## フォームコンポーネント

### 作成フォーム

```typescript
import { useState } from 'react';
import { useSystemUsers } from '../hooks/useSystemUsers';
import type { SystemUsersCreateInput } from '../generated/models/SystemUsersModel';

export function UserCreateForm({ onClose }: { onClose: () => void }) {
  const { createUser } = useSystemUsers();
  const [formData, setFormData] = useState<SystemUsersCreateInput>({
    fullname: '',
    internalemailaddress: ''
  });
  const [submitting, setSubmitting] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    try {
      setSubmitting(true);
      setError(null);
      await createUser(formData);
      onClose();
    } catch (err) {
      setError(err instanceof Error ? err.message : '作成に失敗しました');
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <div>
        <label className="block text-sm font-medium mb-1">
          氏名 <span className="text-red-500">*</span>
        </label>
        <input
          type="text"
          value={formData.fullname}
          onChange={(e) => setFormData({ ...formData, fullname: e.target.value })}
          className="input-field"
          required
        />
      </div>

      <div>
        <label className="block text-sm font-medium mb-1">
          メールアドレス
        </label>
        <input
          type="email"
          value={formData.internalemailaddress}
          onChange={(e) => setFormData({ ...formData, internalemailaddress: e.target.value })}
          className="input-field"
        />
      </div>

      {error && (
        <div className="bg-red-50 text-red-600 p-3 rounded">{error}</div>
      )}

      <div className="flex gap-2">
        <button
          type="submit"
          disabled={submitting}
          className="btn-primary"
        >
          {submitting ? '作成中...' : '作成'}
        </button>
        <button
          type="button"
          onClick={onClose}
          className="btn-secondary"
        >
          キャンセル
        </button>
      </div>
    </form>
  );
}
```

---

## 共通UIコンポーネント

### ローディングスピナー

```typescript
export function LoadingSpinner({ message }: { message?: string }) {
  return (
    <div className="flex items-center justify-center p-8">
      <div className="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
      {message && <span className="ml-3 text-gray-600">{message}</span>}
    </div>
  );
}
```

### エラーメッセージ

```typescript
export function ErrorMessage({ 
  error, 
  onRetry 
}: { 
  error: string; 
  onRetry?: () => void 
}) {
  return (
    <div className="bg-red-50 border border-red-200 rounded-lg p-4">
      <h3 className="text-red-800 font-semibold mb-2">エラーが発生しました</h3>
      <p className="text-red-600 mb-4">{error}</p>
      {onRetry && (
        <button onClick={onRetry} className="btn-primary">
          再試行
        </button>
      )}
    </div>
  );
}
```

### 空状態

```typescript
export function EmptyState({ message }: { message: string }) {
  return (
    <div className="text-center py-12">
      <svg 
        className="mx-auto h-12 w-12 text-gray-400" 
        fill="none" 
        viewBox="0 0 24 24" 
        stroke="currentColor"
      >
        <path 
          strokeLinecap="round" 
          strokeLinejoin="round" 
          strokeWidth={2} 
          d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4" 
        />
      </svg>
      <p className="mt-4 text-gray-500">{message}</p>
    </div>
  );
}
```

---

## モーダルダイアログ

```typescript
import { ReactNode } from 'react';

export function Modal({ 
  isOpen, 
  onClose, 
  title, 
  children 
}: {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: ReactNode;
}) {
  if (!isOpen) return null;

  return (
    <div className="fixed inset-0 z-50 overflow-y-auto">
      <div className="flex items-center justify-center min-h-screen px-4">
        <div 
          className="fixed inset-0 bg-black bg-opacity-50"
          onClick={onClose}
        />
        
        <div className="relative bg-white rounded-lg shadow-xl max-w-md w-full p-6">
          <div className="flex justify-between items-center mb-4">
            <h2 className="text-xl font-bold">{title}</h2>
            <button onClick={onClose} className="text-gray-400 hover:text-gray-600">
              <svg className="w-6 h-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>
          
          {children}
        </div>
      </div>
    </div>
  );
}

// 使用例
function UserListWithCreate() {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsModalOpen(true)}>
        新規作成
      </button>

      <Modal 
        isOpen={isModalOpen}
        onClose={() => setIsModalOpen(false)}
        title="ユーザー作成"
      >
        <UserCreateForm onClose={() => setIsModalOpen(false)} />
      </Modal>
    </div>
  );
}
```

---

## ✅ UI統合チェックリスト

### コンポーネント構造

- [ ] カスタムフックでロジックを分離している
- [ ] コンポーネントはプレゼンテーションに専念している
- [ ] 再利用可能なUIコンポーネントを作成している
- [ ] 適切なpropsを定義している

### 状態管理

- [ ] ローディング状態を表示している
- [ ] エラー状態を表示している
- [ ] 空状態を表示している
- [ ] SDK初期化待ちを表示している

### ユーザー体験

- [ ] 適切なフィードバックを提供している
- [ ] 操作の確認ダイアログを表示している
- [ ] アクセシビリティを考慮している
- [ ] レスポンシブデザインになっている

---

## 📖 関連ドキュメント

- [CRUD操作実装](./PHASE5_05_CRUD_IMPLEMENTATION.md)
- [実装例](./PHASE5_07_EXAMPLES.md)
- [Phase 5 概要](./PHASE5_01_OVERVIEW.md)
