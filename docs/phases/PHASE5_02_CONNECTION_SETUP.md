# Phase 5: 接続セットアップ

## 📋 このドキュメントについて

このドキュメントでは、Power Appsポータルでの接続作成、接続IDの取得、Dataverseスキーマの取得方法について説明します。

---

## Step 1: 接続IDの取得

このStepでは、データソースへの接続方法を説明します。

**接続方法は、コネクタータイプによって異なります:**

| コネクター | 推奨方法 | 接続ID | 手順 |
|-----------|---------|-------|------|
| **Dataverse** | 簡潔なコマンド | ❌ 不要 | → **Step 1-A** |
| Office 365 Users | 接続ID指定 | ✅ 必要 | → **Step 1-B** |
| SQL Server | 接続ID指定 | ✅ 必要 | → **Step 1-B** |
| その他 | 接続ID指定 | ✅ 必要 | → **Step 1-B** |

---

### Step 1-A: Dataverse接続（接続ID不要）⭐推奨

Dataverseテーブルに接続する場合、**最も簡潔な方法**を使用できます。

**手順:**

1. **環境に認証（未実施の場合）**

   ```powershell
   pac auth create --environment "https://org123456.crm.dynamics.com"
   ```

2. **customization.xml を配置**
   - Step 2で取得した `customization.xml` をワークスペースルートに配置

3. **サービスクラス生成**

   ```powershell
   # 全テーブル生成
   pac code add-data-source -a dataverse

   # または特定のテーブルのみ
   pac code add-data-source -a dataverse -t systemusers
   pac code add-data-source -a dataverse -t geek_project_task
   ```

**コマンド説明:**

- `-a dataverse`: `--connector shared_commondataserviceforapps` の短縮形
- `-t {テーブル論理名}`: 特定のテーブルのみを生成（省略時は全テーブル）
- `--connection-id` は不要（環境認証が自動使用される）

**動作の仕組み:**

- `pac auth create` で設定した環境認証が自動的に使用されます
- Power Apps環境への接続は自動的に確立されます
- 追加の接続設定は不要です

**メリット:**

- ✅ 接続ID取得の手間が不要
- ✅ コマンドが短くシンプル
- ✅ 環境認証が自動適用
- ✅ 接続管理が簡単

**従来の方法（非推奨）:**

接続IDを指定する方法も使用できますが、手間が増えるため推奨されません：

```powershell
# 非推奨：接続IDを指定する方法
pac code add-data-source `
  --connector "shared_commondataserviceforapps" `
  --connection-id "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
```

> **💡 ヒント**: Dataverse開発では、`-a dataverse` を使った簡潔な方法を使用してください。

---

### Step 1-B: Office 365 Users / SQL Server / その他（接続ID必要）

Office 365 UsersやSQL Serverなどのコネクターでは、**接続IDが必要**です。

#### 1-B-1. Power Appsポータルで接続を作成

1. **Power Apps Maker Portalにアクセス**

   ```text
   https://make.powerapps.com
   ```

2. **環境を選択**
   - 画面右上で適切な環境を選択

3. **接続ページに移動**
   - 左側メニュー: **データ** → **接続**

4. **新しい接続を作成**
   - 「**+ 新しい接続**」ボタンをクリック
   - 検索ボックスで接続したいコネクターを検索
     - **Dataverse**: 「Microsoft Dataverse」
     - **Office 365 Users**: 「Office 365 Users」
     - **SQL Server**: 「SQL Server」
   - コネクターを選択
   - 「**作成**」ボタンをクリック

5. **認証完了を確認**
   - サインインが求められた場合は認証を完了
   - 接続が作成されると「接続」ページに表示されます

---

#### 1-B-2. URLから接続IDを取得 ⭐重要

接続IDは**ブラウザのURLから直接取得**します。

**手順:**

1. 作成した接続をクリックして詳細ページを開く
2. ブラウザのアドレスバーのURLを確認
3. URLの末尾にあるGUID形式の文字列が接続ID

**URLの例:**

```text
https://make.powerapps.com/environments/12345678-abcd-1234-abcd-123456789012/connections/shared_commondataserviceforapps/a1b2c3d4-e5f6-7890-abcd-ef1234567890/details
                                                                                                              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                                                                                              これが接続ID
```

**接続IDのフォーマット:**

```text
a1b2c3d4-e5f6-7890-abcd-ef1234567890
```

**📋 接続IDをメモ帳にコピーしてください。Step 3で使用します。**

---

#### 1-B-3. (代替方法) PAC CLIでの確認

コマンドラインで接続を確認することもできます:

```powershell
# 接続一覧を表示
pac connector list

# 出力例:
# Connector Display Name: Microsoft Dataverse
# Connector Name: shared_commondataserviceforapps  
# Connection Id: a1b2c3d4-e5f6-7890-abcd-ef1234567890
```

> **💡 ヒント**: URLからの取得が最も確実な方法です。

---

#### 1-B-4. コネクター別の接続ID取得

**Office 365 Users:**

```text
URL: .../connections/shared_office365users/{接続ID}/details
API ID: shared_office365users
```

**SQL Server:**

```text
URL: .../connections/shared_sql/{接続ID}/details
API ID: shared_sql
```

---

## Step 2: Dataverseスキーマの取得

**ソリューションファイルから `customization.xml` を取得し、ワークスペースのルートフォルダに配置します。**

> **📘 詳細リファレンス**  
> Dataverseスキーマの取得方法の詳細は **[HOW_TO_GET_DATAVERSE_SCHEMA.md](../HOW_TO_GET_DATAVERSE_SCHEMA.md)** を参照してください。

---

### 2-1. ソリューションのエクスポート

1. **Power Apps Maker Portalにアクセス**

   ```text
   https://make.powerapps.com
   ```

2. **ソリューションページに移動**
   - 左側メニュー: **ソリューション**

3. **対象のソリューションを選択**
   - 使用するテーブルが含まれているソリューションを選択
   - カスタムテーブルを使用する場合は、そのテーブルを含むソリューションを選択

4. **ソリューションをエクスポート**
   - ソリューションを選択した状態で「**エクスポート**」をクリック
   - 「**アンマネージド**」を選択
   - 「**エクスポート**」ボタンをクリック

5. **ZIPファイルをダウンロード**
   - エクスポートが完了すると、ZIPファイルがダウンロードされます

---

### 2-2. customization.xml の抽出

1. **ZIPファイルを解凍**
   - ダウンロードしたZIPファイルを解凍

2. **customization.xml を見つける**

   ```text
   解凍フォルダ/
   └── Customizations/
       └── customization.xml  ← これをコピー
   ```

3. **ワークスペースのルートに配置** ⭐重要

   ```text
   YourCodeAppsProject/    ← ワークスペースルート
   ├── customization.xml   ← ここに配置
   ├── src/
   ├── public/
   ├── package.json
   └── vite.config.ts
   ```

**配置コマンド例 (PowerShell):**

```powershell
# ダウンロードフォルダからコピー
Copy-Item "C:\Users\YourName\Downloads\ExtractedSolution\Customizations\customization.xml" -Destination ".\customization.xml"

# 配置確認
Get-Item .\customization.xml
```

---

### 2-3. customization.xml の内容確認

ファイルには以下のような情報が含まれています:

```xml
<ImportExportXml>
  <Entities>
    <Entity Name="geek_project_task">
      <EntityInfo>
        <entity Name="geek_project_task">
          <LocalizedNames>
            <LocalizedName description="プロジェクトタスク" languagecode="1041" />
          </LocalizedNames>
          <attributes>
            <attribute PhysicalName="geek_name" LogicalName="geek_name" Type="nvarchar" />
            <attribute PhysicalName="geek_assignedto" LogicalName="geek_assignedto" Type="lookup" />
            <attribute PhysicalName="geek_status" LogicalName="geek_status" Type="picklist">
              <optionset Name="geek_status">
                <options>
                  <option value="1" label="未着手" />
                  <option value="2" label="進行中" />
                  <option value="3" label="完了" />
                </options>
              </optionset>
            </attribute>
          </attributes>
        </entity>
      </EntityInfo>
    </Entity>
  </Entities>
</ImportExportXml>
```

この情報を基に、次のStepでTypeScript型定義が生成されます。

---

### 2-4. customization.xml に含まれる情報

| 情報 | 説明 | 使用目的 |
|------|------|---------|
| **テーブル名** | 論理名 (`geek_project_task`) | サービスクラス生成 |
| **フィールド定義** | データ型、必須/任意、最大長 | TypeScript型定義 |
| **Choice値** | オプションセットの値とラベル | Enum型定義 |
| **Lookupフィールド** | 参照先テーブル情報 | リレーション型定義 |

---

## Step 2 (代替): Power Apps開発者ツールでスキーマ取得

Dataverseテーブルのスキーマは、以下の方法でも取得できます。

### 方法1: Power Apps Make Portalから

1. Power Apps Maker Portal → **テーブル** → 対象テーブルを選択
2. **列**タブでフィールド一覧を確認
3. 各フィールドの「表示名」「論理名」「データ型」をメモ

### 方法2: Dataverse Web APIで取得

```powershell
# EntityDefinitions APIで取得
$env = "https://org123456.crm.dynamics.com"
$token = "..." # アクセストークン

Invoke-RestMethod -Uri "$env/api/data/v9.0/EntityDefinitions(LogicalName='geek_project_task')" `
  -Headers @{ Authorization = "Bearer $token" } `
  -Method Get
```

### 方法3: XrmToolBoxを使用

1. XrmToolBoxをインストール
2. 「Metadata Browser」プラグインを使用
3. テーブルとフィールドの詳細情報を確認

---

## SQL Server接続の場合の追加情報

SQL Serverに接続する場合は、**データセット名**も必要です。

### データセット名の取得方法

1. Power Apps Maker Portal → **データ** → **接続**
2. SQL Server接続を選択
3. 接続詳細ページで「データセット名」を確認

**データセット名の例:**

```text
server.database.windows.net,databasename
```

**コマンドでの使用例:**

```powershell
pac code add-data-source `
  -a "shared_sql" `
  -c "{接続ID}" `
  -t "[dbo].[TableName]" `
  -d "server.database.windows.net,databasename"
```

---

## ✅ Step 1 完了チェックリスト

### Dataverseの場合（Step 1-A）

- [ ] `pac auth create` で環境に認証済み
- [ ] customization.xmlをワークスペースルートに配置した
- [ ] `pac code add-data-source -a dataverse` コマンドを実行した
- [ ] サービスクラスが正常に生成された

### その他のコネクター（Step 1-B）

- [ ] Power Appsポータルで接続が作成されている
- [ ] ブラウザのURLから接続IDをコピーした
- [ ] 接続IDがGUID形式（ハイフン区切りの36文字）である
- [ ] 接続IDをメモ帳に保存した

### スキーマ取得（Dataverseのみ）

- [ ] ソリューションをエクスポートした
- [ ] customization.xmlを抽出した
- [ ] customization.xmlをワークスペースルートに配置した
- [ ] ファイルが正しく配置されているか確認した (`Get-Item .\customization.xml`)

---

## 🔗 次のステップ

**Dataverseの場合（Step 1-A使用）:**

- `pac code add-data-source -a dataverse` を実行すれば完了
- そのまま **[モックデータからリアルデータへの移行](./PHASE5_04_MOCK_TO_REAL.md)** に進めます

**その他のコネクター（Step 1-B使用）:**

- 接続IDとスキーマの取得が完了したら、次は **[サービスクラス生成](./PHASE5_03_SERVICE_GENERATION.md)** に進みます

---

## 📖 関連ドキュメント

- [Dataverseスキーマ取得方法の詳細](../HOW_TO_GET_DATAVERSE_SCHEMA.md)
- [Dataverseトラブルシューティング](../DATAVERSE_TROUBLESHOOTING.md)
- [Phase 5 概要](./PHASE5_01_OVERVIEW.md)
