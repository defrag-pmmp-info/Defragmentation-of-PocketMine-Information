# API4 Changelogs全訳(18%完了)
## 訳に関して
<https://github.com/pmmp/PocketMine-MP/commit/9e6d7405709560c0025e072324602983213276dd> 版より翻訳

翻訳漏れの無いように気を配りますが、書かれていない項目がある場合PRを送ってもらえると助かります。

## 4.0.0-BETA1
2021.9.7リリース
このメジャーバージョンAPI4は重要なAPIの変更,新しいワールド形式のサポート,パフォーマンスの向上やネットワークの刷新を含めたコア全体の重要な変更に焦点を当てています。

これはベータリリースであり、最終版でないことを注意してください。 現在からリリースまでに重大な変更はない予定ですがAPIはまだ変更される可能性があります。

さらに、このチェンジログはベストエフォートに基づいて提供されていて、すべての変更点が言及されていない可能性があることに注意してください。何か洩れがあった場合追加するためにプルリクエストをお願いします。(すなわち pmmp公式へ)

### 警告
これは安定版リリースでは**ありません**。PMMPはこのビルドを使用したことによる損害のいかなる責任を負いません。
**テスト**目的にのみ使用してください。

### コア
#### 一般 General
- "プラグイングレーリスト"機能が導入されます。ローディング時からプラグインをホワイトリスト化もしくはブラックリスト化することができます。
- Remote Console (RCON) は削除されました。[RconServer](https://github.com/pmmp/RconServer)プラグインが代わりに提供されます。
- スポーンプロテクションは削除されました。[BasicSpawnProtection](https://github.com/pmmp/BasicSpawnProtection)プラグインが代わりに提供されます。
- Ctrl+Cシグナルハンドリングは削除されました。[PcnltSignalHandler]プラグインが代わりに提供されます。
- プレイヤー移動アンチチートは削除されました。
- GS4 QueryはRakLibが無効化されても停止されなくなりました。
- `pocketmine_chunkutils` PHP拡張は使われなくなりました。
- 新しいPHP拡張がこのバージョンから必要になりました。
    - [chunkutils2](https://github.com/pmmp/ext-chunkutils2)
    - [morton](https://github.com/pmmp/ext-morton)
- プレイヤーリスポーン時の多くのバグが修正されました。
    - 生成されていない地形でリスポーンする際に岩盤下にスポーンするバグ
    - 非常に遅いマシン上で初回サーバー参加時に岩盤下にスポーンするバグ
    - カスタムスポーン地点がセットされている状態でリスポーンしたときにブロック内(または上空)にスポーンするバグ
    - プレイヤースポーン地点がワールドスポーンが変わった時に以前の場所になってしまうバグ
- 高い維持コストのわりに恩恵が少ないためプリプロセッサーはビルドから削除されました。現在、プリプロセッサー利用は非推奨です。

#### コマンド Commands
- `/reload` コマンドは削除されました。
- `/effect` コマンドは数字IDをサポートしなくなりました。現在では名前が要求されます。
- `/enchant` コマンドは数学IDをサポートしなくなりました。現在では名前が要求されます。
- `/clear` コマンドが追加されました。バニラのMinecraftのものと機能的に同等のものです。
- 以下のコマンドの出力は選択した言語にローカライズされました。
    - `/gc`
    - `/status`
    - `/op`
    - `/deop`

#### 設定 Configuration
- ワールドのプリセットは`pocketmine.yml`の`preset`キーとして利用できるようになりました: 以前は`generator`キー
- 新たに以下のオプションが`pocketmine.yml`に追加されます
    - `chunk-ticking.blocks-per-subchunk-per-tick`(デフォルト `3`):この値を増加させるとランダムブロックアップデートの確率が上がります。(例. 草の成長)
    - `network.enable-encryption`(デフォルト`true`): Minecraftネットワークパケットを暗号化するか否かを決定できます。
- 以下のオプションが`pocketmine.yml`から削除されました。
    - `chunk-ticking.light-updates`: 光処理は基本的なバニラの機能を動作させるのに必要なため、チャンクチックを無効化することなくこの機能を停止することは意味がありません。もし、明るさ計算をしたくなければチャンクチックと共に無効化するようにしてください。
    - `player.anti-cheat.allow-movement-cheats`

#### ワールドハンドリング
##### インターフェイス
- 最初のワールド生成時にスポーン地形チャンク生成の進度が表示されるようになりました。

##### 機能
- 1.12.xまでのMinecraft Bedrockのワールドがサポートされました。(1.13より上はまた別の形式変更によりまだ**サポートされていません**。対応には非常に労力を要します。)
- 非推奨のワールド形式の自動変換が実装されました。
- `leveldb`以外のすべての形式は非推奨となりました。以下のワールド形式は**ロード時に自動的に新しい形式に変換されます**:
  - `mcregion`
  - `anvil`
  - `pmanvil`
- 建築上限256はすべてのワールドでサポートされます。(自動変換により)
- 追加ブロックがサポートされます。(自動変換により)
- 光処理はディスク上に保存・ロードされなくなりました。
- かわりに必要に応じてすぐに算出されます。これは以前からあった多くのバグを修正します。

##### パフォーマンス
- `leveldb`は現在、基本のサポートされたワールド形式です。優れた設計により生来よりリージョンベース形式よりも高速です。
- 部分的なチャンク保存(変更されたチャンクの子要素のみを保存する)が実装されました。これによりチャンク保存時に書き込む必要のあるデータ量を徹底的に減らすことができます。また、同様に**ワールド保存にかかる時間も徹底的に減らします**。 これは`leveldb`ワールド形式のモジュール設計に可能になりました。リージョンベース形式ではこの強化はできません。
- 光処理はすべてのチャンクで利用可能であるとは限らなくなります。必要に応じてすぐに算出されます。
- `/op`, `/deop`, `/whitelist add` and `/whitelist remove` は理由なくプレイヤーデータを読み込まないようになりました。
- `Timings`はより正確なパフォーマンスメトリクスを収集するため`hrtime()`により提供される高精度なタイマーを使うようになります。
- Z階数曲線(モートンコード)がブロックとチャンク座標のハッシュのために使用されます。これはハッシュテーブルキーハッシュ衝突問題を解決することにより広い範囲でパフォーマンスを非常に改善します。影響がある範囲には爆発や明るさ計算などを含みます。
- [`libdeflate`](https://github.com/ebiggers/libdeflate)が(オプションで)外方向へのMinecraftパケット圧縮に使用できます。これは多くのケースで`zlib`の2倍以上高速で、パケットブロードキャストとネットワーク全体の性能を向上させます。
- クロージャーは現在内部イベントハンドラーの呼び出しのために使用されています。これにより3.xシステムよりも10-20%の性能向上ができます。3.xシステムはイベントコールのたびに動的にコーラブルを解決する必要がありました。

#### Logger刷新
- 多くのコンポーネントは自動的にプレフィックスをメッセージに追加する専用のロガーを持つようになりました。
- メインロガーはミリ秒のタイムスタンプを含むようになりました。


#### ネットワーク
このバージョンではネットワークシステムの重大な変更を組み込んでいます: コヒーレンシ、信頼性、モジュール性の向上をしました。

##### Minecraft Bedrock パケット暗号化
- これはハッカーがプレイヤーのログインを盗聴し利用することによる反射攻撃を防ぎます。
- 新たな設定`network.enable-encryption`が`pocketmine.yml`に追加されました。デフォルトでは`true`です。

##### パケット受信エラーハンドリングが整備されました
- 今は`BadPacketException`のみがパケットのデコード、ハンドリング時にキャッチされます。これにはすべてのデコードが適切なエラーチェックを行うことが**必須**とされます。
    - デコードで`BadPacketException`を投げるとプレイヤーは`Packet processing error`メッセージでキックされます。
    - 切断メッセージにはサーバーオーナーがプレイヤーから報告された問題を特定しやすくするためのランダムな16進数IDが含まれます。
- そのほかの例外を投げるとサーバーはクラッシュします。`Internal server error`は削除されました。
- 現在ではサーバーに対するクライアント方向へのパケットの送信は不当なものになりました。これを行うとクライアントは`Unexpected non-serverbound packet`メッセージでキックされます。

##### 新たなパケットハンドラーシステム
- パケットハンドラーはネットワークセッションから分離され、専用のパケットハンダラー構造に分かれました。
- ネットワークセッションは正確に1つのハンドラーをその時点で持つことができます。ハンドラーは可変ありいつでも置き換えられます。これによりパケットハンドリングのロジックを複数のステージへと分離することができます。
    - 不正なタイミングで不正なパケットを送信することを防ぐ(今では何事もなくドロップされます)
    - 一時的な状態固有のロジックの存在を許可する(例えばリソースパックのより厳格なダウンロードチェック)
- パケットハンドラーはほとんどいたるところで`Player`からなくなり、代わりにそのパケットハンドラー専用のユニットに存在しまるようになりました。
- 以前まで`Player`のパケットハンドラー内部に絡み合っていたほぼすべてのゲームロジックは新たなAPIメソッドへと展開されました。詳しくはPlayer APIの変更をご覧ください。

### API
#### 一般
- 以前まで`callable`を許容していた大半の個所は、現在では`/Closure`のみを許可します。これはクロージャーがより一貫性のある振る舞いをして性能がより高いためです。
- `void`と`?nullable` パラメータおよび返り値の型指定は多くの箇所に適用されました。
- `pocketmine\metadeta`にあるすべてのものとそれに関連する実装は削除されました。


#### `plugin.yml`の変更
##### パーミッションのネスト
パーミッションのネストは`plugin.yml`内ではサポートされなくなりました。 `plugin.yml`内で(デフォルトにより)パーミッションのグループ化には、非常に混乱しやすく一貫性のないふるまいがありました。
ネストによるパーミッションの宣言の代わりに、宣言はそれぞればらばらになさなければならなりません。

_Before_:
```
permissions:
   pmmp:
     default: op
	 children:
	     pmmp.something:
		     default: op
         pmmp.somethingElse
		     default: op
```

_After_:
```
permissions:
    pmmp.something:
        default: op
    pmmp.somethingElse
        default: op
```

##### `src-namespace-prefix`
新しい指示文`src-namespace-prefix`が導入されます。これにより以下のようなプラグイン構造にある無駄なサブディレクトリを取り除くことができます。
例えば、以前では`pmmp\TesterPlugin\Main`がメインクラスであるようなプラグインはこのような構造でした。
```
|-- plugin.yml
|-- src/
    |-- pmmp/
        |-- TesterPlugin/
            |-- Main.php
            |-- SomeOtherClass.php
            |-- SomeNamespace/
                |-- SomeNamespacedClass.php
```
しかし、`src-namespace-prefix:pmmp\TesterPlugin`を`plugin.yml`に付けくわえることで無駄なディレクトリを取り除き、このような構造にすることができます。
```
|-- plugin.yml
|-- src/
    |-- Main.php
    |-- SomeOtherClass.php
    |-- SomeNamespace/
        |-- SomeNamespacedClass.php
```

**ノート**: 昔の構造でもちゃんと動きます。これは必ず変えなければならない変更ではありません。

#### Block
- 新たに`VanillaBlocks`クラスが追加されました。このクラスは現在知られているどのブロックタイプでも生成できる静的メソッドを保有しています。 これは定数が使われている場合に`BlockFactory::get()`の代わりとしてしようされることが好ましいです。
- ブロックは`Position`を継承するかわりにポジションを保有します。`Block->getBlockPosition()`が追加されました。
- 256以上のIDをもつブロックがサポートされました。
- ブロックのステートとバリアントメタデータは分離されました。
    - バリアントはIDの拡張と考えられ、不変です。(解説:これは回転や開閉といった状態を含まないことを表す)
    - `Block->setDamage()`は削除されました。
    - すべてのブロックにはブロックプロパティ相応のゲッターとセッターが備わりました。例えば`facing` どの向きか,`lit/unlit` 着火/消火, `colour` 色などです。これらはメタデータの代わりに利用されるべきです。
- タイルエンティティは,タイルエンティティを必要とするブロックが`World->setBlock()`されたときに自動的に作成/削除されるようになります。
- いくつかのタイルエンティティのAPIは,タイルエンティティが非推奨となると同時に対応する`Block`クラスで公開されるようになりました。
- 名前空間`pocketmine\tile`は`pocketmine\block\tile`に移動されました。
- `Block->recalculateBoundingBox()`と`Block->recalculateCollisionBoxes()`は自身の位置基準ではなく`0,0,0`に基準のAABBsを返すようになります。
- ブロックの破壊情報は新しい動的な`BlockBreakInfo`ユニットへと展開されました。以下のメソッドが移動されます。
  - `Block->getBlastResistance()` -> `BlockBreakInfo->getBlastResistance()`
  - `Block->getBreakTime()` -> `BlockBreakInfo->getBreakTime()`
  - `Block->getHardness()` -> `BlockBreakInfo->getHardness()`
  - `Block->getToolHarvestLevel()` -> `BlockBreakInfo->getToolHarvestLevel()`
  - `Block->getToolType()` -> `BlockBreakInfo->getToolType()`
  - `Block->isBreakable()` -> `BlockBreakInfo->isBreakable()`
  - `Block->isCompatibleWithTool()` -> `BlockBreakInfo->isToolCompatible()`
- 以下のAPIメソッドが追加されました。
  - `Block->asItem()`: ブロックに対応するアイテムスタックを返す。
  - `Block->isSameState()`: ステート情報を含めてブロックが同じパラメータであるかを返す。
  - `Block->isSameType()`: ステート情報を含めずにブロックが同じパラメータであるかを返す。
  - `Block->isFullCube()`
- 以下のフックが追加されました。
  - `Block->onAttack()`: サバイバルモードのプレイヤーがブロックを左クリックしブロックの破壊をし始めた時に呼ばれる。
  - `Block->onEntityLand()`: エンティティが(高さによらず)落下後にブロック上に乗ったときに呼ばれる。
  - `Block->onPostPlace()`: ワールドにおかれた直後に呼ばれ、レール接続やチェストの連結をハンドルする。
- 以下のAPIメソッドがリネームされました。
  - `Block->getDamage()` -> `Block->getMeta()`
  - `Block->onActivate()` -> `Block->onInteract()`
  - `Block->onEntityCollide()` -> `Block->onEntityInside()`
- 以下のAPIメソッドはシグネチャが変わりました。
  - `Block->onInteract()` は現在ではシグネチャ `onInteract(Item $item, int $face, Vector3 $clickVector, ?Player $player = null) : bool` を持ちます。
  - `Block->getCollisionBoxes()` はファイナルになりました。 子クラスは`recalculateCollisionBoxes()`をオーバーライドするべきです.
- 以下のAPIメソッドは削除されました。
  - `Block->canPassThrough()`
  - `Block->setDamage()`
  - `Block::get()`: これはずいぶん前に `BlockFactory::get()`に置き換えられています。
  - `Block->getBoundingBox()`
- 以下のクラスはリネームされました。
  - `BlockIds` -> `BlockLegacyIds`
  - `CobblestoneWall` -> `Wall`
  - `NoteBlock` -> `Note`
  - `SignPost` -> `Sign`
  - `StandingBanner` -> `Banner`
- 以下のクラスは削除されました。
  - `Bricks`
  - `BurningFurnace`
  - `CobblestoneStairs`
  - `Dandelion`
  - `DoubleSlab`
  - `DoubleStoneSlab`
  - `EndStone`
  - `GlowingRedstoneOre`
  - `GoldOre`
  - `Gold`
  - `IronDoor`
  - `IronOre`
  - `IronTrapdoor`
  - `Iron`
  - `Lapis`
  - `NetherBrickFence`
  - `NetherBrickStairs`
  - `Obsidian`
  - `PurpurStairs`
  - `Purpur`
  - `QuartzStairs`
  - `Quartz`
  - `RedSandstoneStairs`
  - `RedSandstone`
  - `SandstoneStairs`
  - `Sandstone`
  - `StainedClay`
  - `StainedGlassPane`
  - `StainedGlass`
  - `StoneBrickStairs`
  - `StoneBricks`
  - `StoneSlab2`
  - `StoneSlab`
  - `Stone`
  - `WallBanner`
  - `WallSign`
  - `Wood2`
- `BlockToolType`の定数は`TYPE_`プレフィックスを削除する形でリネームされました。
