# 多言語対応
Poggitでプラグインをリリースする場合やサーバーを多言語対応する場合には`pocketmine\lang\Language`を用いることで比較的楽に対応することができます。また、これによりメッセージ送信部がスマートにできるかもしれません。

<https://github.com/pmmp/PocketMine-MP/blob/master/src/lang/Language.php>より内容を抜粋。

## Languageの利用
例としてプラグインのディレクトリ構造が以下のようになっているとします。

```
ExamplePlugin
├ resources
│ └ lang
│ 　 ├ jpn.ini
│ 　 └ eng.ini
├ src
│ └ ...
└ plugin.yml
```
この場合、メインクラスにおいて
```php
$path = "{$this->getFile()}/resources/lang";
$language = new Language("jpn",$path);
```
とすれば、`$language`にはjpn.iniがロードされた状態になります。

例えば、`jpn.ini`の中身が以下のようであったとします。
```ini
language.name=日本語
test.msg1=Languageの使い方
test.msg2={%0}が{%1}に{%2}を渡しました。
```
このとき、上の`$language`では
```php
$language->translateString("test.msg1") /* -> "Languageの使い方" */;
$language->translateString("test.msg2", ["Steve", "Alex", "スコップ"]) /* -> "SteveがAlexにスコップを渡しました。"*/
```
のようになります。
これを利用して多言語対応を行うことができます。

なお、`Language`インスタンスの生成はファイルIOを伴うため、実用時には以下のような構成にすると良いでしょう。

```php:Loader.php
<?php

namespace exampleplugin;

use pocketmine\lang\Language;
use pocketmine\plugin\PluginBase;
use Webmozart\PathUtil\Path;

class Loader extends PluginBase{

    /** @var array<string, Language>*/
    private array $language = [];

    public function onLoad(): void
    {
        $path = Path::join($this->getFile(), "resources", "lang");
        foreach(Language::getLanguageList($path) as $lang => $name){
            $this->getLogger()->info("Language: $name is loaded");
            $this->language[$lang] = new Language($lang, $path);
        }
    }

    public function getLanguage(string $lang=Language::FALLBACK_LANGUAGE): Language{
        return $this->language[$lang];
    }
}
```