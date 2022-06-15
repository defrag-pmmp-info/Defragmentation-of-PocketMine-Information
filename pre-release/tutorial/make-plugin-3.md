# プラグインを作ろう
ようやく本題です。その前に、DevToolsを導入しましょう。

<details>
<summary>DevToolsの導入(クリックして開く)</summary>

## DevToolsの導入
DevToolsを導入するとフォルダの状態のプラグインを起動する、プラグインの型枠を用意するなどの機能が使えるようになります。

### ダウンロード
[Githubのこのページ](https://github.com/pmmp/DevTools/releases/latest)のDevTools.pharをクリックすれば、ダウンロードされます。
### インストール
PMMPのあるフォルダの、Pluginsフォルダの中にダウンロードしたDevTools.pharをそのまま入れてください。
</details>  


## プラグインの型枠を作る
まず、プラグインをの型枠を作ります。DevToolsの機能にそういうのがあるので使います。PMMPを起動して、
```
genplugin プラグインの名前 作者の名前
```
と入力してEnterを押すと、プラグインの型枠ができます。名前の所は適当にやっといてください。

そうすると、PMMPのPluginsフォルダの中にさっき指定したプラグインの名前のフォルダができているはず。そしてそのフォルダの中のフォルダの中に、`Main.php`が入っています。こいつがプラグインの本体です。

## いざいじる
まず、`Main.php`をエディタで開きましょう。