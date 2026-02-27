# Modrinth MODの依存関係追加マニュアル

このドキュメントでは、Modrinthで公開されているMODをNeoForgeプロジェクトの`runtimeOnly`依存関係として追加する手順を解説します。

## 1. プロジェクト環境の確認

まず、プロジェクトが対象としているMinecraftとNeoForgeのバージョンを確認します。これらは通常 `gradle.properties` に定義されています。

例:
- `minecraft_version=1.21.1`
- `neo_version=21.1.219`

## 2. ModrinthでのMOD情報の取得

対象とするMODのページ（例: [A.V.A - Alliance of Valiant Arms Guns](https://modrinth.com/mod/a.v.a-alliance-of-valiant-arms-guns)）から以下の情報を取得します。

1.  **MODスラグ (Slug)**: URLの `/mod/` の後にある文字列。
    - 例: `a.v.a-alliance-of-valiant-arms-guns`
2.  **バージョンID (Version ID)**: 自身の環境（Minecraft 1.21.1, NeoForge）に対応した最新のバージョン。
    - 例: `2.9.1`

## 3. build.gradle の編集

### 3.1 Modrinth Mavenリポジトリの追加

ModrinthのMODをダウンロードするために、`repositories` ブロックにModrinthのMavenリポジトリを追加します。

```gradle
repositories {
    mavenLocal()
    // Modrinth Mavenを追加
    maven {
        name = "Modrinth"
        url = "https://api.modrinth.com/maven"
    }
}
```

### 3.2 依存関係の追加

`dependencies` ブロックに、取得したスラグとバージョンIDを使用して依存関係を追加します。Modrinthの依存関係は `maven.modrinth:<Slug>:<VersionID>` の形式になります。

```gradle
dependencies {
    // runtimeOnlyとして追加（実行時のみ必要で、コンパイルには含めない場合）
    runtimeOnly "maven.modrinth:a.v.a-alliance-of-valiant-arms-guns:2.9.1"
}
```

## 4. 依存関係の構成（Configuration）の使い分け

プロジェクトのニーズに応じて、以下のいずれかの構成を使用して依存関係を追加します。

| 構成 | 用途 |
| :--- | :--- |
| `implementation` | コンパイル時と実行時の両方で必要。標準的な設定。 |
| `compileOnly` | コンパイル時のみ必要。実行時には別のMODが提供する場合などに使用。 |
| `runtimeOnly` | 実行時のみ必要。アドオンやツールMODなど、コード内で参照しない場合に使用。 |
| `api` | 他のMODが自分のMODをライブラリとして使用する場合に、その依存関係も公開する。 |

### 設定例

```gradle
dependencies {
    // 実行時のみ必要なMOD (アドオンや動作確認用)
    runtimeOnly "maven.modrinth:a.v.a-alliance-of-valiant-arms-guns:2.9.1"

    // コンパイル時にAPIとして参照し、実行時にも必要なライブラリ
    implementation "software.bernie.geckolib:geckolib-neoforge-1.21.1:4.5.2"

    // 開発中のデバッグや便利ツール (JEIなど)
    // APIはコンパイル時のみ、本体は実行時のみといった使い分けが一般的
    compileOnly "mezz.jei:jei-1.21.1-neoforge-api:19.21.0.244"
    runtimeOnly "mezz.jei:jei-1.21.1-neoforge:19.21.0.244"
}
```

## 5. 設定の反映と検証

ターミナルで以下のコマンドを実行し、依存関係が正しく解決されているか確認します。

```bash
./gradlew dependencies --configuration runtimeClasspath | grep "maven.modrinth"
```

正しく設定されていれば、指定したMODの名前とバージョンが表示されます。

---
**注意:**
MODによっては、動作のために他のライブラリMOD（GeckoLib, Architectury等）が必要な場合があります。その場合は、同様の手順でそれらのMODも依存関係に追加してください。
