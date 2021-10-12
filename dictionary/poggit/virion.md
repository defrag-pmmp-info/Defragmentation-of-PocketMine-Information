# Virion
VirionはPocketMineプラグイン専用のPHPライブラリです。
Poggitではプラグインのビルドに自動的にVirionライブラリを含めることができます。

## プラグインからVirionを使う
### Poggitでコンパイル
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