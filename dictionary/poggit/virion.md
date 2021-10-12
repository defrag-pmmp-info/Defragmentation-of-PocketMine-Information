# Virion
Poggit Virion(以下Virion)はPocketMineプラグイン専用のPHPライブラリです。
このドキュメントでは、Virionの開発と使用についてのワークフローについての情報を提供します。

## Virionの目的

ソフトウェア開発においてライブラリは素晴らしいものですが、複数のプラグインが同じライブラリを使用する場合、同じPHPランタイムで実行されるために間違ったバージョンのライブラリが使用される可能性があります。Virionフレームワークでは、クラスをシェーディング(名前空間の操作)することでこの問題を解決することを目指しています。

## 前置き

Virionはプラグインや他のVirionによって使用されます。
このドキュメントでは、`Virionユーザ`は、Virionを使用するプラグインまたはVirionを指します。

## ワークフロー

### Virionの開発
Virionはメインクラスを含む必要はありませんが、virion が宣言するすべてのシンボル (クラス・インターフェース・trait・名前空間関数・名前空間定数) は、**Antigen**と呼ばれるVirionの名前空間、またはAntigenのサブ名前空間の下になければなりません。
Antigenはビリオンに固有のものでなければなりません。
他のVirionやプラグインは、Antigenの名前空間またはその下位の名前空間の下でシンボルを宣言することはできません。

名前空間内に置くことができない他の名前付きグローバルリソースを登録する場合、その名前は、Virionのランタイム名前空間に依存するか、Virionユーザが提供する文字列に依存する必要があります。
例えば、VirionがAsyncTaskスレッドストアを使用する場合、`AsyncTask::saveToThreadStore()`に渡されるキーは**クラスの構文上の名前空間の参照 ([構文的参照](#構文的参照)) から生成されるか、Virionユーザから渡された値から生成されなければなりません**。
stream_wrapper_register()`も同様のケースで、プロトコル名Virionによってハードコードされてはなりません。

Virionフレームワークには、`PSR-0`のソースファイルとVirionのマニフェストファイル `virion.yml` が含まれています。
`virion.yml`には以下の属性が含まれます。

| 名前 | 説明 | 例 | 必須/任意 |
| :---: | :---: | :---: | :---: |
| `name` | 現在は使用されていませんが、Poggitのプロジェクト名と一致する必要があります。 | `libasynql` | 必須 |
| `version` | セマンティック・バージョニングを用いたVirionのバージョン | `0.1.0-beta` | 必須 |
| `antigen` | Antigen(名前空間) | `poggit\libasynql` | 必須 |
| `api` | このVirionが使用できる**唯一**のPocketMine APIのバージョンです。plugin.yml の`api`と同じシステムを使用します。 | `[1.4.0, 2.0.0, 3.0.0-ALPHA1]` | `api`か`php`のどちらか |
| `php` | このVirionが使用できる**唯一**の PHP バージョンです。plugin.ymlの`api` と同様のシステムを使用します。 | `[5.6, 7.0]` | `api`か`php`のどちらか |
| `authors` | 製作者名。文字列かリストが指定できます。 | `[SOFe, PEMapModder]` | 任意 |
| `description` | 説明文 | `Simple asynchronous SQL access` | 任意 |

異なるプラグインで使用されている同じVirionが相互に作用してはいけません。

### Virionユーザの開発
virion ユーザーは、virion によって宣言されたシンボルが正常にロードされることを前提に開発することができます。ただし、これらのシンボルへのすべての参照は、[構文的](#構文的参照)でなければなりません。

### Virionやプラグインのテスト
Virion用テスターの役割を果たすプラグインを作ることが推奨されます。

Poggitは[DEVirion](https://poggit.pmmp.io/p/DEVirion)を提供しています。詳しい使い方はPoggitのリリースページにあるプラグインの説明を参照してください。
このプラグインを使うと、Phar形式またはフォルダ形式のVirionまたはプラグインでサーバーを動かすことができます。

**デバッグ以外の理由でDEVirionを使用しないでください**。
DEVirionはvirionのクラスを影で支えているわけではないので、DEVirionを使った複数のプラグインがあっても互換性の問題が発生し、virionフレームワークの意味がなくなってしまいます。

### PoggitでVirionをビルドする
Virionプロジェクトは、プラグインプロジェクトと同様にリポジトリの`.poggit.yml`で宣言することができますが、プロジェクト属性が 2 つ追加されます。

```yaml
type: library
model: virion
```

例:

```yaml
projects:
  libasynql:
    path: path/to/libasynql
    type: library
    model: virion
```

### ローカルでVirionをビルドする
Virionをローカルで直接コンパイルする公式ツールは作成されていませんが、ビルドスクリプトは簡単に作成することができます。

基本的にVirionはプラグインと同じフォーマットですが、4つの違いがあります。
- `plugin.yml`が`virion.yml`になります。
- `resources` はサポートされていません (将来的に追加される可能性があります)。
- `virion.php`というファイルと、`virion_stub.php`というファイルがpharに含まれている必要があります。これらのファイルは、[](https://github.com/poggit/poggit/tree/beta/assets/php/)からダウンロードすることができます。
    - `virion_stub.php`はビルド用のCLIを提供します。
    - `virion.php`には、実際にVirionを注入するコードが含まれています。
- pharのスタブは`virion.php`を直接読み込む必要があります。以下は有効なスタブの例です。

```php
<?php require "phar://" . __FILE__ . "/virion_stub.php"; __HALT_COMPILER();
```

[参考](https://github.com/poggit/poggit/blob/d587ecf3891270bd37940bf5640fca1eb7e374ad/src/poggit/ci/builder/PoggitVirionBuilder.php?utf8=%E2%9C%93#L50-L52)


### PoggitでVirionユーザをビルドする

Virionユーザ(Virionを使用するプラグインやVirion本体)は、`.poggit.yml`で使用するVirionを宣言することができ、プラグインのビルド時にPoggitが自動的にVirionを注入します。Virionは、プラグインプロジェクトの`libs`属性でリストアップできます。利用する各Virionの宣言は下記の属性を含みます。

| 名前 | 説明 | 形式・例 | 必須/任意 |
| :---: | :---: | :---: | :---: |
| `format` | ライブラリのフォーマット | `virion` (現在この値のみ選択可能) | 任意 (デフォルト：`virion`) |
| `vendor` | Virionのインポート元 | `poggit-project` または `raw` | 任意 (デフォルト：`poggit-project`) |
| `src` (`vendor`属性で`poggit-project`を選択した場合) | Poggitプロジェクトのフルパス | `/projectName` (同じリポジトリのプロジェクト) OR `repoOwner/repoName/projectName` | 必須 |
| `src` (`vendor`属性で`raw`)を選択した場合 | ダウンロード先URL(HTTP/HTTPS)、またはプロジェクトからの相対パス | `https://example.com/virion.phar` または `libs/virion.phar` | 必須 |
| `version` (`vendor: poggit-project`) | [セマンティックバージョニング](https://getcomposer.org/doc/articles/versions.md)で指定 | `^1.0` | `vender`属性で`poggit-project`を指定した場合のみ必須 |
| `shade` | シェーディングストラテジ | `syntax` または `single` または `double` | 任意 (デフォルト：`syntax`) |
| `branch` (`vendor`属性で`poggit-project`を選択した場合) | ブランチの指定 | `ブランチ名` または `:default`(デフォルトブランチ) または `*`/`%`(全てのブランチ) | 任意(デフォルト：`:default`) |
| `epitope` | 追加の名前空間 | `.random`(ランダム) または `.sha`(ハッシュ) または `.none` または `任意の名前空間` | 任意 (デフォルト：`libs`) |

**注意**

セキュリティ上の理由から、Poggitでのビルドではローカルのvirion.phpを無視し、常に最新のvirion.phpを使用します。

### ローカルでVirionユーザをコンパイルする
ビルドされたVirionには、`virion_stub.php`および`virion.php`ファイルが含まれている必要があります。これらのファイルはVirionをVirionユーザに注入するためのCLIを提供します。

基本的な使い方：

```bash
php used_virion.phar virion_user.phar ${ANTIBODY_PREFIX}
```

`${ANTIBODY_PREFIX}`はビリオンユーザーのメインの名前空間（プラグインの場合はメインクラスの名前空間、ビリオンの場合はAntigenの名前空間）でなければなりません。バックスラッシュ等はエスケープする必要があります。

## CLI機能の拡張
Virionは、プロジェクトルートに`cli-map.json`というファイルを追加することで、カスタムコマンドを追加することができます。このファイルには、コマンド名と実行ファイルをマッピングしたオブジェクトを記述します。

`cli-map.json`が存在する場合、Poggitはsrcディレクトリのオートローダーのための`cli-autoload.php`を作成します。

コマンド名は`.phar`で終わらないようにしてください。

## 制限事項
- Virionユーザは、自分のAPIでVirionのクラスを公開してはいけません。もしそのようなオブジェクトをAPIで公開しなければならない場合は、Virionユーザが宣言したインターフェースでラップしなければなりません。

## 付録
### 構文的参照
基本的に、構文参照は以下のように定義されます。

> A `T_NAMESPACE` (`namespace`), `T_USE` (`use`) or `T_NS_SEPARATOR` (`\`) token, or `T_USE` + `T_FUNCTION` (`use function`) or `T_USE` + `T_CONST` (`use const`), followed by a consecutive series of `T_STRING` (a non-keyword set of characters, i.e. part of the namespace) and `T_NS_SEPARATOR` (`\`).

Virionの`クラス`・`関数`・`定数`の名前空間を参照する際には、以下のような形式で**のみ**行うことができます。

```php
namespace name\space;
use name\space\Clazz;
use function name\space\func;
use const name\space\CONSTANT;
\name\space\Clazz::method();
```
`poggit\libasynql`というAntigenのVirionの例：

```php
// syntactic reference
use poggit\libasynql\SqlResult as Result; 
$result = new Result;

// syntactic reference
$result = new \poggit\libasynql\SqlResult;

// syntactic reference
use poggit\libasynql\SqlResult as Result;
$class = new \ReflectionClass(Result::class);

// syntactic reference
$class = new \ReflectionClass(\poggit\libasynql\SqlResult::class);

// syntactic references, from the scope of a class method in poggit\libasynql\SqlResult
$class = new \ReflectionClass(self::class);
$class = new \ReflectionClass(static::class);
$class = new \ReflectionClass(get_class());
$class = new \ReflectionClass(__CLASS__);
$class = new \ReflectionClass(__NAMESPACE__ . "\\SqlResult"); // a boundary case barely acceptable

// non-syntactic reference
$class = new \ReflectionClass("poggit\\libasynql\\SqlResult");
$class = new \ReflectionClass('poggit\libasynql\SqlResult');
```

名前空間からの相対参照はシェーディングされないため、**Virionの名前空間への相対参照は使用しないでください**。例えば、Virion のAntigenが`namespace poggit\libasynql`である場合、Virionユーザでは`namespace poggit`で始まるコードを書いてはいけません。