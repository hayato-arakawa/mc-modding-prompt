# NeoForge 1.21.1 サーバーセットアップマニュアル

このマニュアルでは、Minecraft 1.21.1 用の NeoForge サーバーを Linux または macOS 環境でセットアップする手順を説明します。

## 前提条件
- **Java 21 以上** がインストールされていること（1.21.1 には Java 21 が必須です）。
- ターミナル操作が可能な環境であること。

## セットアップ手順

### 1. サーバーディレクトリの作成
サーバーファイルを配置する専用のディレクトリを作成し、移動します。

```bash
mkdir neoforge-server
cd neoforge-server
```

### 2. NeoForge インストーラーのダウンロード
NeoForge の公式リポジトリから、1.21.1 に対応した最新のインストーラー（バージョン 21.1.169）をダウンロードします。

```bash
curl -O https://maven.neoforged.net/releases/net/neoforged/neoforge/21.1.169/neoforge-21.1.169-installer.jar
```

### 3. サーバーファイルのインストール
ダウンロードしたインストーラーを `--install-server` フラグを付けて実行します。これにより、必要なライブラリと Minecraft 本体がダウンロードされます。

```bash
java -jar neoforge-21.1.169-installer.jar --install-server
```

### 4. EULA（使用許諾契約）への同意
Minecraft サーバーを起動するには、Mojang の EULA に同意する必要があります。`eula.txt` ファイルを作成し、内容を `eula=true` に設定します。

```bash
echo "eula=true" > eula.txt
```

### 5. 実行スクリプトの準備
インストーラーによって生成された `run.sh` に実行権限を付与します。

```bash
chmod +x run.sh
```

## サーバーの起動

以下のコマンドでサーバーを起動できます。

```bash
./run.sh
```

## カスタマイズ方法

### メモリ割り当ての変更
サーバーに割り当てるメモリ（RAM）を増やしたい場合は、`user_jvm_args.txt` を編集します。
例：4GB のメモリを割り当てる場合、以下の内容をファイルに追記します。

```text
-Xmx4G
-Xms4G
```

### サーバー設定の変更
サーバーのポート番号やゲームモードなどは、初回起動後に生成される `server.properties` を編集することで変更可能です。

## Modのインストール方法

### 1. Modファイルの入手
[CurseForge](https://www.curseforge.com/minecraft/search?class=mc-mods) や [Modrinth](https://modrinth.com/mods) などのサイトから、1.21.1 かつ NeoForge 対応の Mod をダウンロードします。

### 2. Modの配置
ダウンロードした `.jar` ファイルを、サーバーディレクトリ内の `mods` フォルダに配置します。

※ `mods` フォルダは、サーバーを一度起動すると自動的に生成されます。もちろん、手動でフォルダを作成して Mod を配置してから初回起動を行っても問題ありません。

```bash
mkdir -p mods
mv downloaded-mod.jar mods/
```

### 3. 再起動
Mod を配置後、`./run.sh` を実行してサーバーを再起動します。依存関係（前提 Mod）が必要な場合は、それらも同様に配置してください。

## 開発・トラブルシューティング設定

### ライセンス認証エラーへの対処
開発環境でのテスト時などに「ライセンス認証（ログイン）に失敗しました」というエラーが出る場合は、以下の設定を `server.properties` で変更します。

1. `server.properties` を開きます。
2. 以下の項目を変更します：
   - `online-mode=false` : プレイヤーのライセンス認証をスキップします。
   - `enforce-secure-profile=false` : 開発用クライアントなどで接続する際に必要になる場合があります。

**セキュリティ上の警告:**
`online-mode=false` は、誰でも任意の名前で接続できるようになる設定です。外部公開するサーバーでは、必ず `true` に戻すか、別の保護手段（ホワイトリスト等）を併用してください。

---
*最終更新日: 2026年2月27日*
