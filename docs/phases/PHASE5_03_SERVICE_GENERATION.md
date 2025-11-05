# Phase 5: サービスクラス生成

## 📋 このドキュメントについて

このドキュメントでは、`pac code add-data-source` コマンドを使用してPower Apps SDKベースのTypeScriptサービスクラスを自動生成する方法について説明します。

---

## Step 3: サービスクラスの生成

> **📘 Dataverseユーザーへの注意**  
> Dataverseを使用する場合、Phase 5の**Step 1-A**で既にサービスクラス生成が完了しています。
> このStep 3は、**Office 365 UsersやSQL Serverなど、Step 1-Bを使用した場合のみ**必要です。

---

### 3-1. データソース追加コマンド実行

**基本コマンド:**

```powershell
pac code add-data-source `
  --connector "<API ID>" `
  --connection-id "<Step 1で取得した接続ID>"
```

---

### 3-2. コネクター別のコマンド例

#### Dataverse（参考）

**推奨方法（接続ID不要）:**

Step 1-Aで既に実行済みの場合はスキップしてください。

```powershell
# 全テーブル生成
pac code add-data-source -a dataverse

# 特定のテーブルのみ
pac code add-data-source -a dataverse -t systemusers
pac code add-data-source -a dataverse -t geek_project_task
```

**従来の方法（非推奨）:**

```powershell
# 全テーブル生成
pac code add-data-source `
  --connector "shared_commondataserviceforapps" `
  --connection-id "a1b2c3d4-e5f6-7890-abcd-ef1234567890"

# 特定のテーブルのみ
pac code add-data-source `
  --connector "shared_commondataserviceforapps" `
  --connection-id "a1b2c3d4-e5f6-7890-abcd-ef1234567890" `
  --table "systemusers"
```

#### Office 365 Users

```powershell
pac code add-data-source `
  --connector "shared_office365users" `
  --connection-id "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
```

#### SQL Server

```powershell
pac code add-data-source `
  --connector "shared_sql" `
  --connection-id "a1b2c3d4-e5f6-7890-abcd-ef1234567890" `
  --table "[dbo].[TableName]" `
  --dataset "server.database.windows.net,databasename"
```

---

### 3-3. コマンドパラメータ説明

| パラメータ | 説明 | 必須 | 例 |
|-----------|------|------|-----|
| `--connector` (`-a`) | コネクターのAPI ID | ✅ | `shared_commondataserviceforapps` |
| `--connection-id` (`-c`) | 接続ID (GUID) | ✅ | `a1b2c3d4-e5f6-7890-abcd-ef1234567890` |
| `--table` (`-t`) | テーブル名（論理名） | ⚪ | `systemusers`, `geek_project_task` |
| `--dataset` (`-d`) | データセット名（SQL Serverのみ） | ⚪ | `server.database.windows.net,db` |

---

### 3-4. 生成されるファイル

コマンド実行後、以下のファイルが自動生成されます:

```text
src/
└── generated/
    ├── index.ts                          # エクスポートファイル
    ├── models/
    │   ├── CommonModels.ts               # 共通型定義
    │   ├── SystemUsersModel.ts           # SystemUsers型定義
    │   └── GeekProjectTasksModel.ts      # カスタムテーブル型定義
    └── services/
        ├── SystemUsersService.ts         # SystemUsersサービスクラス
        └── GeekProjectTasksService.ts    # カスタムテーブルサービスクラス
```

---

### 3-5. 生成されたコードの確認

#### Model例 (SystemUsersModel.ts)

```typescript
export interface SystemUsers {
  systemuserid: string;              // 主キー
  fullname: string;                  // フルネーム
  internalemailaddress: string;      // メールアドレス
  isdisabled: boolean;               // 無効フラグ
  domainname: string;                // ドメイン名
  // ... 他のフィールド
}

export interface SystemUsersCreateInput {
  fullname: string;
  internalemailaddress?: string;
  // ... 作成時の入力型
}

export interface SystemUsersUpdateInput {
  fullname?: string;
  internalemailaddress?: string;
  // ... 更新時の入力型
}
```

#### Service例 (SystemUsersService.ts)

```typescript
import { getClient } from '@microsoft/power-apps/data';
import type { IOperationResult } from '@microsoft/power-apps/data';
import type { SystemUsers, SystemUsersCreateInput, SystemUsersUpdateInput } from '../models/SystemUsersModel';

export class SystemUsersService {
  /**
   * 全レコード取得
   */
  static async getAll(options?: {
    select?: string[];
    filter?: string;
    orderBy?: string;
    top?: number;
    expand?: Array<{ navigationProperty: string; select?: string[] }>;
  }): Promise<IOperationResult<SystemUsers[]>> {
    const client = getClient();
    return await client.retrieveMultipleRecords('systemuser', options);
  }

  /**
   * ID指定で1件取得
   */
  static async getById(id: string, options?: {
    select?: string[];
    expand?: Array<{ navigationProperty: string; select?: string[] }>;
  }): Promise<IOperationResult<SystemUsers>> {
    const client = getClient();
    return await client.retrieveRecord('systemuser', id, options);
  }

  /**
   * 新規作成
   */
  static async create(data: SystemUsersCreateInput): Promise<IOperationResult<string>> {
    const client = getClient();
    return await client.createRecord('systemuser', data);
  }

  /**
   * 更新
   */
  static async update(id: string, data: SystemUsersUpdateInput): Promise<IOperationResult<void>> {
    const client = getClient();
    return await client.updateRecord('systemuser', id, data);
  }

  /**
   * 削除
   */
  static async delete(id: string): Promise<IOperationResult<void>> {
    const client = getClient();
    return await client.deleteRecord('systemuser', id);
  }
}
```

---

### 3-6. ビルドとLint確認

生成されたコードをビルドして確認します:

```powershell
# ビルド
npm run build

# Lint確認
npm run lint

# 両方を実行
npm run build; npm run lint
```

**成功時の出力:**

```text
> build
> vite build

✓ built in 2.34s

> lint
> eslint . --ext .ts,.tsx

✨ No errors found
```

エラーがなければ、サービスクラスの生成は完了です。

---

## ⚠️ 重要: dataSourceName の手動修正（Dataverseのみ）

Dataverseテーブルを使用する場合、**PAC CLIの既知の問題**により、生成されたサービスクラスの `dataSourceName` が正しくない場合があります。

### 問題の症状

```javascript
Error: Retrieve multiple records operation failed. Data source name: Tasks
at xh.getDataSource (index-C5qbVCve.js:1071586)
```

### 原因

生成されたサービスクラスの`dataSourceName`が`.power/appschemas/dataSourcesInfo.ts`のキーと一致していない。

### 解決手順（4ステップ）

#### Step 1: `.power/appschemas/dataSourcesInfo.ts` を開く

エクスポートされているキー名を確認：

```typescript
export const dataSourcesInfo = {
  'crf38_taskses': {  // ← このキー名をコピー
    "tableId": "",
    "version": "",
    "primaryKey": "crf38_tasksid",
    "dataSourceType": "Dataverse",
    "apis": {}
  }
};
```

#### Step 2: `src/generated/services/<TableName>Service.ts` を開く

`dataSourceName`を確認：

```typescript
export class Crf38_tasksesService {
  private static readonly dataSourceName = 'Tasks';  // ❌ 間違い
  // ...
}
```

#### Step 3: `dataSourceName` を修正

`dataSourcesInfo`のキー名に一致させる：

```typescript
export class Crf38_tasksesService {
  private static readonly dataSourceName = 'crf38_taskses';  // ✅ 修正
  // ...
}
```

#### Step 4: 再ビルドと再デプロイ

```powershell
npm run build
pac code push
```

### なぜこの修正が必要か

Power Apps SDKは実行時に以下のように動作します：

```typescript
// SDKの内部処理（概念図）
dataSourcesInfo[dataSourceName]  // キーでデータソース情報を取得

// dataSourceName が 'Tasks' の場合
dataSourcesInfo['Tasks']  // ❌ undefined → エラー

// dataSourceName が 'crf38_taskses' の場合
dataSourcesInfo['crf38_taskses']  // ✅ 正しく取得 → 成功
```

---

## ✅ Step 3 完了チェックリスト

### コマンド実行

- [ ] `pac code add-data-source` が成功している
- [ ] エラーメッセージが表示されていない
- [ ] `src/generated/` フォルダが作成されている

### 生成ファイル確認

- [ ] `src/generated/models/` にモデルファイルが生成されている
- [ ] `src/generated/services/` にサービスファイルが生成されている
- [ ] `src/generated/index.ts` が生成されている

### dataSourceName修正（Dataverseのみ）

- [ ] `.power/appschemas/dataSourcesInfo.ts` のキー名を確認した
- [ ] サービスクラスの `dataSourceName` がキー名と一致している
- [ ] 一致していない場合は手動で修正した

### ビルド確認

- [ ] `npm run build` が成功する
- [ ] `npm run lint` がエラーなし
- [ ] TypeScriptの型エラーがない

---

## 🔧 トラブルシューティング

### エラー1: "Connection not found"

**原因:** 接続IDが間違っているか、接続が存在しない

**解決:**

```powershell
# 接続一覧を確認
pac connector list

# 正しい接続IDを使用
pac code add-data-source -a "..." -c "{正しい接続ID}"
```

### エラー2: "customization.xml not found"

**原因:** ワークスペースルートに`customization.xml`が配置されていない

**解決:**

```powershell
# ファイルの存在確認
Get-Item .\customization.xml

# 存在しない場合は、Step 2を再実施
```

### エラー3: "Table not found in customization.xml"

**原因:** `--table` で指定したテーブルが`customization.xml`に含まれていない

**解決:**

1. `customization.xml`を開いてテーブル名を確認
2. 正しいテーブル名（論理名）を指定
3. または `--table` オプションを省略して全テーブルを生成

---

## 🔗 次のステップ

サービスクラスの生成が完了したら、次は **[モックデータからリアルデータへの移行](./PHASE5_04_MOCK_TO_REAL.md)** に進みます。

---

## 📖 関連ドキュメント

- [Dataverseトラブルシューティング完全ガイド](../DATAVERSE_TROUBLESHOOTING.md)
- [Phase 5 概要](./PHASE5_01_OVERVIEW.md)
- [接続セットアップ](./PHASE5_02_CONNECTION_SETUP.md)
