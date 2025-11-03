# Dataverseトラブルシューティング完全ガイド

## 📋 概要

このドキュメントは、Power Apps Code AppsでDataverseを使用する際の**全てのエラーと解決方法**をまとめた完全ガイドです。

### 対象エラー

- ❌ **404 "Resource not found"** - 接続設定の問題
- ❌ **"Data source not found"** - データソース名の不一致
- ❌ **"Failed to fetch projects"** - データ取得エラー
- ❌ **"Access denied"** - 権限問題
- ❌ **"Table not found"** - テーブル名の問題

---

## 📚 目次

1. [接続設定の問題](#1-接続設定の問題)
   - 404エラー
   - Data source not foundエラー
2. [データ取得エラー](#2-データ取得エラー)
3. [権限問題](#3-権限問題)
4. [テーブル名の問題](#4-テーブル名の問題)
5. [SDK初期化の問題](#5-sdk初期化の問題)
6. [デバッグ手順](#デバッグ手順)

---

## 1. 接続設定の問題

### 🔴 404 "Resource not found" エラー

#### 症状

```javascript
Error: {"statusCode": 404, "message": "Resource not found"}
```

デプロイ後、ブラウザのコンソールに404エラーが表示される。

#### 原因

| 原因 | 確認方法 | 解決方法 |
|------|---------|---------|
| ❌ `connectionReferences`を使用している | `power.config.json`を確認 | `databaseReferences`に変更 |
| ❌ `instanceUrl`が間違っている | ブラウザでURLにアクセスして確認 | 正しいURL (`https://<org>.crm.dynamics.com/`) に修正 |
| ❌ `state: "Configured"`がない | `power.config.json`の`default.cds`設定を確認 | `"state": "Configured"` を追加 |
| ❌ `webApiVersion`が設定されていない | `power.config.json`を確認 | `"webApiVersion": "v9.0"` を追加 |
| ❌ `version`が設定されていない | `power.config.json`を確認 | `"version": "v9.0"` を追加 |

#### ❌ 誤った設定例

```json
{
  "connectionReferences": {
    "msdyn_Dataverse": {
      "api": "shared_commondataservice",
      "connection": "2b14ab593eca47baaee14dfcb233e284"
    }
  }
}
```

#### ✅ 正しい設定例

```json
{
  "databaseReferences": {
    "default.cds": {
      "state": "Configured",
      "instanceUrl": "https://org698324f8.crm.dynamics.com/",
      "webApiVersion": "v9.0",
      "dataSources": {
        "Tasks": {
          "entitySetName": "crf38_taskses",
          "logicalName": "crf38_tasks",
          "isHidden": false
        }
      },
      "version": "v9.0",
      "environmentVariableName": ""
    }
  }
}
```

#### 📋 設定値の取得方法

| 設定項目 | 値 | 取得方法 |
|---------|-----|---------|
| **`state`** | `"Configured"` | 固定値（必須） |
| **`instanceUrl`** | `https://org698324f8.crm.dynamics.com/` | Power Apps Maker Portal → 設定 → セッション情報 → 「Instance url」 |
| **`webApiVersion`** | `"v9.0"` | 固定値（通常は v9.0） |
| **`version`** | `"v9.0"` | 固定値（webApiVersionと同じ） |

#### 💡 なぜ`databaseReferences`が必要か

Power Apps Code Appsには2つの接続パターンがあります：

| パターン | 用途 | 設定 |
|---------|------|------|
| **コネクタ接続** | Office 365 Users, SQL Server等 | `connectionReferences` |
| **Dataverse直接接続** | Dataverseテーブル | `databaseReferences` |

Dataverseテーブルの場合、**データベースへの直接接続**が必要なため、`databaseReferences`を使用します。

---

### 🔴 "Data source not found" エラー

#### 症状

```javascript
Error: Retrieve multiple records operation failed. Data source name: Tasks
at xh.getDataSource (index-C5qbVCve.js:1071586)
```

#### 原因

生成されたサービスクラスの`dataSourceName`が`.power/appschemas/dataSourcesInfo.ts`のキーと一致していない。

これは**PAC CLIの既知の問題**です。

#### 問題の詳細

**自動生成されたコード（問題あり）:**

```typescript
// src/generated/services/Crf38_tasksesService.ts
export class Crf38_tasksesService {
  private static readonly dataSourceName = 'Tasks';  // ❌ 間違い
  private static readonly client = getClient(dataSourcesInfo);
  // ...
}
```

**dataSourcesInfoの内容:**

```typescript
// .power/appschemas/dataSourcesInfo.ts
export const dataSourcesInfo = {
  'crf38_taskses': {  // ← キー名は entitySetName
    "tableId": "",
    "version": "",
    "primaryKey": "crf38_tasksid",
    "dataSourceType": "Dataverse",
    "apis": {}
  }
};
```

#### 解決手順（4ステップ）

**Step 1: `.power/appschemas/dataSourcesInfo.ts` を開く**

エクスポートされているキー名を確認：

```typescript
export const dataSourcesInfo = {
  'crf38_taskses': {  // ← このキー名をコピー
    // ...
  }
};
```

**Step 2: `src/generated/services/<TableName>Service.ts` を開く**

`dataSourceName`を確認：

```typescript
export class Crf38_tasksesService {
  private static readonly dataSourceName = 'Tasks';  // ❌ 間違い
  // ...
}
```

**Step 3: `dataSourceName` を修正**

`dataSourcesInfo`のキー名に一致させる：

```typescript
export class Crf38_tasksesService {
  private static readonly dataSourceName = 'crf38_taskses';  // ✅ 修正
  // ...
}
```

**Step 4: 再ビルドと再デプロイ**

```powershell
npm run build
pac code push
```

#### 💡 なぜこの修正が必要か

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

### 🔴 実装チェックリスト

接続設定が正しいか確認してください：

#### 設定ファイル

- [ ] `power.config.json`に`databaseReferences`が設定されている（`connectionReferences`ではない）
- [ ] `databaseReferences.default.cds.state`が`"Configured"`
- [ ] `databaseReferences.default.cds.instanceUrl`が正しいDataverse URL
- [ ] `databaseReferences.default.cds.webApiVersion`が`"v9.0"`
- [ ] `databaseReferences.default.cds.version`が`"v9.0"`
- [ ] `databaseReferences.default.cds.dataSources`にテーブルが定義されている

#### 生成ファイル

- [ ] `.power/appschemas/dataSourcesInfo.ts`が生成されている
- [ ] `dataSourcesInfo`にテーブルのキーが存在する（例: `'crf38_taskses'`）
- [ ] `src/generated/services/<TableName>Service.ts`が生成されている
- [ ] `src/generated/models/<TableName>Model.ts`が生成されている

#### サービスクラスの修正

- [ ] サービスクラスの`dataSourceName`を確認
- [ ] `dataSourceName`が`dataSourcesInfo`のキーと**完全に一致**している
- [ ] `dataSourceName`が`power.config.json`の`dataSources`名ではなく、`entitySetName`と一致

#### ビルドとデプロイ

- [ ] `npm run build`が成功する（エラーなし）
- [ ] `pac code push`が成功する
- [ ] デプロイ後、ブラウザでアプリを開く
- [ ] ブラウザのコンソール（F12）にエラーがない
- [ ] Dataverseからデータが正しく表示される

---

## 2. データ取得エラー

### 🔴 "Failed to fetch projects" エラー

#### エラーの症状

```javascript
❌ Error fetching Dataverse projects: Error: Failed to fetch projects from Dataverse
```

#### 原因1: Dataverseテーブルが存在しない

**確認方法:**

1. <https://make.powerapps.com> にアクセス
2. 「テーブル」→「すべて」を選択
3. 以下のテーブルを検索：
   - `geek_projecrt` (プロジェクト)
   - `geek_project_task` (タスク)

**対処法:**
テーブルが存在しない場合は、Dataverseでテーブルを作成してください。

#### 原因2: テーブル名のスペルミス

現在のコードは以下のテーブル名を使用しています：

- `geek_projecrt` (最後の's'がない - これは意図的?)
- `geek_project_task`

**確認するログ:**

```javascript
📦 Dataverse getAll result: {
  success: false,  // ← false の場合はエラー
  error: "..." // ← エラーメッセージを確認
}
```

---

## 3. 権限問題

### 🔴 アクセス権限の問題

#### 症状

- テーブルは存在するがデータが取得できない
- `projectsResult.success === false`

#### 確認方法

Power Appsポータルでテーブルのセキュリティ設定を確認：

1. テーブルを選択
2. 「設定」→「権限」
3. 現在のユーザーに読み取り権限があるか確認

#### 対処法

必要な権限を付与する、またはテーブルのセキュリティロールを調整

---

## 4. テーブル名の問題

### 🔴 データソース接続の問題

#### 確認するログ

```javascript
🔍 Dataverse environment check: {
  dataSourcesInfo: "available",
  serviceType: "function",
  getAllMethod: "function"
}
```

#### 対処法

データソースを再生成：

```powershell
pac code add-data-source -a dataverse -t geek_projecrt
pac code add-data-source -a dataverse -t geek_project_task
```

---

## 5. SDK初期化の問題

### 🔴 Power Apps SDKの初期化タイミング

#### 確認するログ

```javascript
🔍 Dataverse fetchProjects called: {
  isPowerApps: true,
  powerAppsInitialized: true  // ← これがfalseの場合は初期化待ち
}
```

#### 対処法

アプリをリロードして、SDK初期化を待つ

#### 補足: テーブルにデータが存在しない場合

**これは正常です！**

テーブルが空の場合：

```javascript
📦 Dataverse getAll result: {
  success: true,
  hasData: true,
  dataLength: 0  // ← 0件は正常
}
```

この場合、アプリは空の状態で起動し、「新規プロジェクト」からデータを作成できます。

---

## デバッグ手順

### ステップ1: コンソールログを確認

Power Appsでアプリを開き、F12でコンソールを開いて以下を確認：

**1. SDK初期化:**

```text
✅ Power Platform SDK initialization complete
```

**2. 環境検出:**

```text
🔍 Dataverse fetchProjects called: { isPowerApps: true, powerAppsInitialized: true }
```

**3. API呼び出し:**

```text
📡 Calling Geek_projecrtsService.getAll()...
```

**4. 結果:**

```text
📦 Dataverse getAll result: { success: true/false, error: "..." }
```

### ステップ2: エラーメッセージを特定

コンソールで以下を確認：
```javascript
❌ Error details: {
  message: "...",  // ← 具体的なエラーメッセージ
  stack: "...",
  error: { ... }
}
```

### ステップ3: Power Appsポータルで確認

1. テーブルの存在確認
2. テーブルのスキーマ確認（列名が一致しているか）
3. データの存在確認
4. セキュリティ権限の確認

### ステップ4: データソースの再生成

問題が解決しない場合：

```powershell
# プロジェクトディレクトリで実行
pac code add-data-source -a dataverse -t geek_projecrt
pac code add-data-source -a dataverse -t geek_project_task

# ビルドして再デプロイ
npm run build
pac code push
```

## よくあるエラーパターン

### エラー1: "Table not found"

**原因:** テーブル名が間違っているか、テーブルが存在しない

**対処:** テーブル名を確認し、必要に応じて作成

### エラー2: "Access denied"

**原因:** 権限不足

**対処:** セキュリティロールを確認

### エラー3: "PowerDataRuntime is not initialized"

**原因:** SDK初期化前にAPIを呼び出している

**対処:** PowerProvider の初期化を待つ（既に修正済み）

### エラー4: "Failed to fetch projects"（success: false）

**原因:** API呼び出しそのものが失敗

**対処:**

1. ログで具体的なエラーを確認
2. テーブルとフィールドの存在を確認
3. データソース接続を再生成

---

## 次のステップ

1. **Power Appsでアプリを開く**
2. **F12でコンソールを開く**
3. **上記のログを確認して、どのステップで失敗しているか特定**
4. **エラーメッセージの詳細を確認**
5. **該当する対処法を実施**

---

## サンプルデータでのテスト

Dataverseに問題がある場合、開発環境（localhost）で動作確認：

```powershell
npm run dev
```

ブラウザで `http://localhost:5173` を開くと、サンプルデータでアプリが動作します。

これにより、コード自体の問題かDataverse接続の問題かを切り分けられます。
