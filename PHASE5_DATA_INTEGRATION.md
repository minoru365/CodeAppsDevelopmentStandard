# Phase 5: 機能拡張・データソース統合

## 📋 概要

このPhaseでは、Power Apps Code AppsにDataverseやその他のコネクターを統合し、実際のビジネスデータと連携する機能を実装します。

**Phase 5は以下のサブドキュメントに分割されています：**

---

## 📚 ドキュメント構成

### 🎯 基本編

| ドキュメント | 内容 | 推奨タイミング |
|------------|------|--------------|
| **[Phase 5 概要とアーキテクチャ](./docs/phases/PHASE5_01_OVERVIEW.md)** | データソース統合の全体像、4ステップの詳細、アーキテクチャパターン | Phase 5開始時 |
| **[接続セットアップ](./docs/phases/PHASE5_02_CONNECTION_SETUP.md)** | 接続ID取得方法、Dataverseスキーマ取得、Power Appsポータルでの設定 | Step 1-2実施時 |
| **[サービスクラス生成](./docs/phases/PHASE5_03_SERVICE_GENERATION.md)** | `pac code add-data-source`コマンド、生成ファイルの確認、dataSourceName修正 | Step 3実施時 |

### 🔧 実装編

| ドキュメント | 内容 | 推奨タイミング |
|------------|------|--------------|
| **[モックデータからリアルデータへの移行](./docs/phases/PHASE5_04_MOCK_TO_REAL.md)** | SDK初期化確認パターン、データ取得の実装、エラーハンドリング | Step 4実施時 |
| **[CRUD操作実装](./docs/phases/PHASE5_05_CRUD_IMPLEMENTATION.md)** | Create, Read, Update, Delete操作、カスタムフック作成、IOperationResult活用 | CRUD機能実装時 |
| **[UI統合](./docs/phases/PHASE5_06_UI_INTEGRATION.md)** | Reactコンポーネント統合、フォーム実装、ローディング/エラー状態管理 | UI実装時 |

### 📖 実践編

| ドキュメント | 内容 | 推奨タイミング |
|------------|------|--------------|
| **[実装例とサンプルコード](./docs/phases/PHASE5_07_EXAMPLES.md)** | Office 365 Users、Dataverse、SQL Server統合の具体例 | 実装の参考に |

---

## 🚀 クイックスタート

### **実施するStep（概要）:**

1. **接続作成** - Power Appsポータルでコネクター接続を手動作成
2. **接続ID取得** - ブラウザURLから接続IDをコピー
3. **サービスクラス生成** - `pac code add-data-source` でTypeScript型定義を自動生成
4. **SDK初期化確認** - `usePowerPlatform().isInitialized` でPower Apps SDK初期化を確認
5. **カスタムフック作成** - ビジネスロジックをカプセル化（CRUD操作）
6. **UI統合** - Reactコンポーネントでデータを表示・操作
7. **エラーハンドリング** - `IOperationResult.isSuccess` でエラー処理

### **最小構成のコード例:**

```typescript
// カスタムフック例
export const useTasks = () => {
  const { isInitialized } = usePowerPlatform();
  const [tasks, setTasks] = useState([]);

  useEffect(() => {
    if (!isInitialized) return;
    
    TasksService.getAll().then(result => {
      if (result.isSuccess) setTasks(result.value);
    });
  }, [isInitialized]);

  return { tasks };
};
```

---

## ⚠️ 重要な注意事項

### **1. SDK初期化確認は必須**

すべてのサービス呼び出しの前に `isInitialized` チェックが必要です：

```typescript
const { isInitialized } = usePowerPlatform();

if (!isInitialized) return; // または早期リターン
```

### **2. Dataverseの場合はスキーマファイルが必須**

- ワークスペースルートに `customization.xml` を配置
- 詳細: [接続セットアップ](./docs/phases/PHASE5_02_CONNECTION_SETUP.md)

### **3. dataSourceNameの手動修正が必要**

PAC CLIの既知の問題により、生成後に手動修正が必要です：

```typescript
// src/generated/services/YourService.ts
dataSourceName = 'correct_table_name'; // 複数形に修正
```

詳細: [サービスクラス生成](./docs/phases/PHASE5_03_SERVICE_GENERATION.md)

### **4. トラブルシューティング**

エラーが発生した場合は以下を参照：

- **[Dataverseトラブルシューティング](./docs/DATAVERSE_TROUBLESHOOTING.md)** - 404エラー、接続エラー、データ取得エラーの解決方法

---

## 📖 関連ドキュメント

- **[Dataverseトラブルシューティング](./docs/DATAVERSE_TROUBLESHOOTING.md)** - エラー解決の完全ガイド
- **[Lookup フィールド実装ガイド](./docs/LOOKUP_FIELD_GUIDE.md)** - Dataverse Lookupフィールドの扱い方
- **[Phase 4: ローカル実行とテスト](./PHASE4_DEPLOYMENT.md)** - ローカル開発環境

---

## 🎯 学習パス

### **初めての方:**

1. [Phase 5 概要](./docs/phases/PHASE5_01_OVERVIEW.md) でアーキテクチャを理解
2. [接続セットアップ](./docs/phases/PHASE5_02_CONNECTION_SETUP.md) で接続を作成
3. [実装例](./docs/phases/PHASE5_07_EXAMPLES.md) でサンプルコードを確認

### **実装中の方:**

- 問題が発生したら → [Dataverseトラブルシューティング](./docs/DATAVERSE_TROUBLESHOOTING.md)
- CRUD操作を実装したい → [CRUD操作実装](./docs/phases/PHASE5_05_CRUD_IMPLEMENTATION.md)
- UIを統合したい → [UI統合](./docs/phases/PHASE5_06_UI_INTEGRATION.md)

### **より深く学びたい方:**

- すべてのサブドキュメントを順番に読む
- [実装例](./docs/phases/PHASE5_07_EXAMPLES.md) で実際のコードパターンを学ぶ

---

## 📝 バックアップ情報

元の統合ドキュメント（9022行）は `PHASE5_DATA_INTEGRATION.md.backup` として保存されています。
