# API4 Changelogs全訳(41%完了)
## 訳に関して
<https://github.com/pmmp/PocketMine-MP/blob/a3f8546ac442d6bbb44dfedc19eb6d3e639a50b6/changelogs/4.0.md> 版より翻訳

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
```yml
permissions:
    pmmp:
        default: op
children:
    pmmp.something:
        default: op
            pmmp.somethingElse:
                default: op
```

_After_:
```yml
permissions:
    pmmp.something:
        default: op
    pmmp.somethingElse:
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

##### Block
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
    - `Block->getCollisionBoxes()` はファイナルになりました。 子クラスは`recalculateCollisionBoxes()`をオーバーライドするべきです。
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

#### Command
- 以下のクラスは削除されました。
    - `RemoteConsoleCommandSender`
- 以下のAPIメソッドはシグネチャが変わりました。
    - `Command->setPermission()` nullableではありますが、引数が必須になりました
    - `CommandSender->setScreenLineHeight()` nullableではありますが、引数が必須になりました
- 名前に空白を含むコマンドはサポートされなくなりました

#### Entity
##### 一般
- `Entity`は`Location`を継承しなくなります。 代わりに`Entity->getLocation()`や`Entity->getPosition()`を使ってください。
- 以下のフィールドは削除されました。
    - `Entity->chunk`: エンティティはどのチャンクにいるのかを自身では記憶しなくなりました (かわりに`World`がそれを管理します)。
    - `Entity->height`: `EntitySizeInfo`に移動しました。 代わりに`Entity->size`を使ってください。
    - `Entity->width`: `EntitySizeInfo`に移動しました。 代わりに`Entity->size`を使ってください。
    - `Entity->eyeHeight`: `EntitySizeInfo`に移動しました。 代わりに `Entity->size`を使ってください。
- 以下のAPIメソッドが追加されました。
    - `Entity->getFallDistance()`
    - `Entity->setFallDistance()`
    - `ItemEntity->getDespawnDelay()`
    - `ItemEntity->setDespawnDelay()`
    - `Living->calculateFallDamage()`: これは`protected`です。 サブクラスにより、これをオーバーライドすることでダメージロジックをカスタマイズできます。
    - `Human->getHungerManager()`
    - `Human->getXpManager()`
- 以下のAPIメソッドはシグネチャが変わりました。
    - `Entity->entityBaseTick()`は`protected`になりました。
    - `Entity->move()`は`protected`になりました。
    - `Entity->setPosition()`は`protected`になりました。 (代わりに `Entity->teleport()`を使ってください).
    - `Entity->setPositionAndRotation()`は`protected` (代わりに`Entity->teleport()`を使ってください).
    - `Living->knockBack()`は`float, float, float`を受け入れるようになりました。 (最初の２つのパラメーターは削除されました).
    - `Living->getEffects()`は`Effect[]`の代わりに`EffectManager`を返すようになりました。
- 以下のクラスは追加されました。
    - `effect\EffectManager`: `Living`から展開されたエフェクト管理の機能群を保持します。
    - `HungerManager`: `Human`から展開された空腹管理の機能群を保持します。
    - `ExperienceManager`: `Human`から展開された経験値管理の機能群を保持します。
- 以下のAPIメソッドは移動、またはリネームされました。
    - `Entity->fall()` -> `Entity->onHitGround()` (また、アクセス権が`public`から`protected`になりました。)
    - `Living->removeAllEffects()` -> `EffectManager->clear()`
    - `Living->removeEffect()` -> `EffectManager->remove()`
    - `Living->addEffect()` -> `EffectManager->add()`
    - `Living->getEffect()` -> `EffectManager->get()`
    - `Living->hasEffect()` -> `EffectManager->has()`
    - `Living->hasEffects()` -> `EffectManager->hasEffects()`
    - `Living->getEffects()` -> `EffectManager->all()`
    - `Human->getFood()` -> `HungerManager->getFood()`
    - `Human->setFood()` -> `HungerManager->setFood()`
    - `Human->getMaxFood()` -> `HungerManager->getMaxFood()`
    - `Human->addFood()` -> `HungerManager->addFood()`
    - `Human->isHungry()` -> `HungerManager->isHungry()`
    - `Human->getSaturation()` -> `HungerManager->getSaturation()`
    - `Human->setSaturation()` -> `HungerManager->setSaturation()`
    - `Human->addSaturation()` -> `HungerManager->addSaturation()`
    - `Human->getExhaustion()` -> `HungerManager->getExhaustion()`
    - `Human->setExhaustion()` -> `HungerManager->setExhaustion()`
    - `Human->exhaust()` -> `HungerManager->exhaust()`
    - `Human->getXpLevel()` -> `ExperienceManager->getXpLevel()`
    - `Human->setXpLevel()` -> `ExperienceManager->setXpLevel()`
    - `Human->addXpLevels()` -> `ExperienceManager->addXpLevels()`
    - `Human->subtractXpLevels()` -> `ExperienceManager->subtractXpLevels()`
    - `Human->getXpProgress()` -> `ExperienceManager->getXpProgress()`
    - `Human->setXpProgress()` -> `ExperienceManager->setXpProgress()`
    - `Human->getRemainderXp()` -> `ExperienceManager->getRemainderXp()`
    - `Human->getCurrentTotalXp()` -> `ExperienceManager->getCurrentTotalXp()`
    - `Human->setCurrentTotalXp()` -> `ExperienceManager->setCurrentTotalXp()`
    - `Human->addXp()` -> `ExperienceManager->addXp()`
    - `Human->subtractXp()` -> `ExperienceManager->subtractXp()`
    - `Human->getLifetimeTotalXp()` -> `ExperienceManager->getLifetimeTotalXp()`
    - `Human->setLifetimeTotalXp()` -> `ExperienceManager->setLifetimeTotalXp()`
    - `Human->canPickupXp()` -> `ExperienceManager->canPickupXp()`
    - `Human->onPickupXp()` -> `ExperienceManager->onPickupXp()`
    - `Human->resetXpCooldown()` -> `ExperienceManager->resetXpCooldown()`
- 以下のAPIメソッドは削除されました。
    - `Human->getRawUniqueId()`は、代わりに`Human->getUniqueId()->toBinary()`を使うようになりました。
- 以下のクラスは削除されました。
    - `Creature`
    - `Damageable`
    - `Monster`
    - `NPC`
    - `Rideable`
    - `Vehicle`
- `Skin`無効なデータを受け取ると例外を投げるようになりました。

##### Effect
- 全ての`Effect`に関連したクラスは`pocketmine\entity\effect`の名前空間に移動しました。
- `Effect`クラスに入っていたエフェクト機能はいくつかのクラスに分けられました。以下のクラスが新しく追加されました。
    - `AbsorptionEffect`
    - `HealthBoostEffect`
    - `HungerEffect`
    - `InstantDamageEffect`
    - `InstantEffect`
    - `InstantHealthEffect`
    - `InvisibilityEffect`
    - `LevitationEffect`
    - `PoisonEffect`
    - `RegenerationEffect`
    - `SaturationEffect`
    - `SlownessEffect`
    - `SpeedEffect`
    - `WitherEffect`
- `VanillaEffects`クラスが追加されました。これは全てのバニラエフェクトタイプを静的メソッドで表現し、`Effect::getEffect()`よりもきれいに書けるようになります。
    - 例: `Effect::getEffect(Effect::NIGHT_VISION)`は`VanillaEffects::NIGHT_VISION()`に置き換えることができます。
- エフェクトの強さ(amplifiers)を負の値にすることは現在では明確に禁止されます。エフェクトの生み出す効果が定義されていないためです。
- MCPEのエフェクトIDとPocketMine-MP内部のIDの境目がより明確になりました。
    - IDハンドリングは`pocketmine\data\bedrock\EffectIdMap`へ移動しました。
    - すべてのエフェクトID定数は`Effect`から削除されました。何らかの理由でレガシーエフェクトIDが必要なら`pocketmine\data\bedrock\EffectIds`を利用してください。
- 以下のAPIメソッドは移動しました。
    - `Effect->getId()` -> `EffectIdMap->toId()`
    - `Effect::registerEffect()` -> `EffectIdMap->register()`
    - `Effect::getEffect()` -> `EffectIdMap->fromId()`
    - `Effect::getEffectByName()` -> `VanillaEffects::fromString()`
- 以下のAPIメソッドが追加されました。
    - `Effect->getRuntimeId()`: これは**動的ID**でありエフェクトタイプの比較に用いることができます。**これは決して保持されないため、configやNBTでは利用しないでください！**

##### 実行時エンティティNBTの削除
- エンティティは実行時にNBTを存在させ続けなくなりました。
    - `Entity->namedtag`は削除されました。
    - `Entity->saveNBT()`はその場で前のNBTに変更を加えるのではなく、現在では新規生成された`CompoundTag`を返すようになりました。
    - `Entity->initEntity()`は現在では`CompoundTag`パラメータを受け付けます。

##### エンティティ生成
- `Entity::createEntity()`は削除されました。もはや実行時に新たなエンティティを生成するためには必要なくなりました。代わりに、単に`new YourEntity`のようにしてください。
- `Entity`の子クラスのコンストラクタは普通のクラスと同様にどのようなシグネチャでも持つことができます。
- NBTからのエンティティの読み込みは現在では`EntityFactory`にハンドルされるようになりました。`Entity::createEntity()`の動作とはかなり違った動作をします。 `YourEntity::class`を一式のMinecraft save IDに登録する代わりに、 あるNBTと`World`が与えられたときにエンティティを構築するコールバックを与える必要があります。
    - 生成コールバックは`EntityFactory::register()`を用いて登録されます。
    - 生成コールバックはシグネチャ`function(World, CompoundTag) : Entity`を持つ必要があります。
    - これにより`Entity`の子クラスはそれぞれにふさわしいコンストラクタ引数を持てるようになりました。
    - また、特定のデータが常に与えられるように要求することも可能になります。(例えば、`FallingBlock`をどの種類のブロックかを指定することなく生成するというのは理解できません。)
    - 例:
        - `ItemEntity`は現在では`Item`をコンストラクタで要求するようになりました。そのため、生成コールバックは`Item`をコンストラクタに渡されたNBTからデコードするものです。
        - `Painting`は現在では`PaintingMotive`をコンストラクタで要求するようになりました。そのため、生成コールバックはどの`PaintingMotive`を与えるべきかを受け取ったNBTに応じて決定するものです。
        - さらなる例は`EntityFactory`をご覧ください。
- `EntityFactory::register()`(以前の`Entity::registerEntity()`)はエラー発生時に`false`のかわりに例外を投げるようになりました。
- 以下のAPIメソッドが移動しました。
    - `Entity::registerEntity()` -> `EntityFactory::register()`
- 以下のクラスはコンストラクタが変更されました。
    - すべての発射物の子クラスは現在では`?Entity $thrower`パラメータを必要とします。
    - `Arrow->__construct()`は現在では`bool $critical` パラメータを必要とします。
    - `ExperienceOrb->__construct()`は現在では`int $xpValue`パラメータを必要とします。
    - `FallingBlock->__construct()`は現在では`Block $block`パラメータを必要とします。
    - `ItemEntity->__construct()`は現在では`Item $item`パラメータを必要とします。
    - `Painting->__construct()`は現在では`PaintingMotive $motive`を必要とします。
    - `SplashPotion->__construct()`は現在では`int $potionId`を必要とします。
- 以下のAPIメソッドは削除されました。
    - `Entity::createBaseNBT()`: `new YourEntity`と適切なAPIメソッドが利用されるべきです。
    - `Entity->getSaveId()`
    - `Entity::getKnownEntityTypes()`
    - `Entity::createEntity()`: `new YourEntity`をかわりに利用してください。 

##### やりかけ エンティティのネットワークメタデータの削除
- 定数に関するすべてのネットワークメタデータは`Entity`クラスから削除され、プロトコル層へ移動されました。これはネットワークメタデータをAPIから全体的に削除することを意図していますが、完了はしていません。
    - 定数`Entity::DATA_FLAG_*`は`pocketmine\network\mcpe\protocol\types\entity\EntityMetadataFlags`に移動されました。
    - 定数`Entity::DATA_TYPE_*`は`pocketmine\network\mcpe\protocol\types\entity\EntityMetadataTypes`に移動されました。
    - 定数`Entity::DATA_*`は`pocketmine\network\mcpe\protocol\types\entity\EntityMetadataProperties`に移動されました。
- `DataPropertyManager`は 名前空間`pocketmine\network\mcpe\protocol\types\entity`に移動され、したがってAPIの一部ではないものとして扱われます。
- 導入された内部の`Entity`は`syncNetworkData()`をフックします。この関数はエンティティのプロパティをエンティティはエンティティのネットワークデータセット同期することが期待されます。
- 内部のエンティティのプロパティを保存するためのネットワークメタデータセットの内部利用は削除されました。エンティティは現在では正規のクラスプロパティを使用し、ネットワークデータセットを同期することが期待されます。
- `Entity->propertyManager`は`Entity->networkProperties`にリネームされました。
- `Entity->getDataPropertyManager()`は`Entity->getNetworkProperties()`にリネームされました。

#### Event
##### 内部イベントシステムにおける`Listener`依存の排除
- 内部イベント処理システムは`Listener`オブジェクトに依存しなくなりました。ハンドラーになるための標準要求さえ満たせば任意のクロージャーが利用できます。
    - この変更により、イベントハンドラーの呼び出し性能が15%程改善されます。プラグインが行っていることは何も含まれません。
    - 以下のクラスが削除されました。
        - `pocketmine\plugin\EventExecutor`
        - `pocketmine\plugin\MethodEventExecutor`
    - `RegisteredListener->__construct()`は先頭のパラメータとして`Listener, EventExecutor`の代わりに`Closure`を必要とします。
    - `RegisteredListener->getListener()`は削除されました。

##### デフォルトのキャンセルハンドリングの振る舞いの変化
- ハンドラー関数は**デフォルトではキャンセルされたイベントを受け取らなくなります**。 これは**静かに後方互換性を損なうもの**であり、すなわち直接エラーを吐くことはなくともバグを引き起こす可能性があります。
- `@ignoreCancelled`は無視されるようになりました。
- `@handleCancelled`が追加されました。これを用いるとキャンセルされたイベントを _受け取る_ ことができるようになります。(`@ignoreCancelled`の反対です)


##### `PlayerPreLoginEvent`の変更
- `Player`オブジェクトはログインのこのフェーズでは存在しなくなりました。代わりに`PlayerInfo`オブジェクトが接続情報とともに与えられます。
- Ban、サーバーの満員、ホワイトリストの確認は`PlayerPreLoginEvent`に一元化されます。もはやこの手の切断をハンドルするために`PlayerKickEvent`に介入する必要はなくなるとともに、介入することはできなくなりました。
    - 複数のキック理由を使用すると、他にプレイヤーを切断する理由があって内一つが除外されたときにもプレイヤーを確実に切断することができます。例えばあるプレイヤーがBanされていてサーバーが満員であったとすると、Banフラグを除外してもサーバーが満員であることからプレイヤーはそれでも切断されます。
    - プラグインはカスタムのキック理由をセットできます。どの理由も絶対的な優先度を持ちます。
    - もし複数のフラグがセットされていた場合、キックメッセージには最も優先度が高いものが表示されます。優先度(このスナップショットにおいて)は下記の順に続きます。
        - カスタム (最優先)
        - 満員
        - ホワイトリスト
        - Ban
- `PlayerPreLoginEvent::KICK_REASON_PRIORITY`定数は高いほうが先にくるキック理由の優先度のリストを保持します。
- 以下の定数が追加されました
    - `PlayerPreLoginEvent::KICK_REASON_PLUGIN`
    - `PlayerPreLoginEvent::KICK_REASON_SERVER_FULL`
    - `PlayerPreLoginEvent::KICK_REASON_SERVER_WHITELISTED`
    - `PlayerPreLoginEvent::KICK_REASON_BANNED`
    - `PlayerPreLoginEvent::KICK_REASON_PRIORITY`
- 以下のAPIメソッドが追加されました。
    - `PlayerPreLoginEvent->clearAllKickReasons()`
    - `PlayerPreLoginEvent->clearKickReason()`
    - `PlayerPreLoginEvent->getFinalKickMessage()`: 現在の理由リストでプレイヤーに表示されるメッセージ
    - `PlayerPreLoginEvent->getIp()`
    - `PlayerPreLoginEvent->getKickReasons()`: キック理由を示すフラグの配列, 参加できる時には空
    - `PlayerPreLoginEvent->getPlayerInfo()`
    - `PlayerPreLoginEvent->getPort()`
    - `PlayerPreLoginEvent->isAllowed()`
    - `PlayerPreLoginEvent->isAuthRequired()`: XBL認証が強制されているか
    - `PlayerPreLoginEvent->isKickReasonSet()`
    - `PlayerPreLoginEvent->setAuthRequired()`
    - `PlayerPreLoginEvent->setKickReason()`
- 以下のAPIメソッドは変更されました。
    - `PlayerPreLoginEvent->getKickMessage()`は現在ではシグネチャ`getKickMessage(int $flag) : ?string`を持ちます。
- 以下のAPIメソッドは削除されました。
    - `PlayerPreLoginEvent->setKickMessage()`
    - `PlayerPreLoginEvent->getPlayer()`
- 以下のAPIメソッドは移動/リネームされました。
    - `InventoryPickupItemEvent->getItem()` -> `InventoryPickupItemEvent->getItemEntity()`

##### その他の変更
- イベントの間にプレイヤーが切断されてもサーバーはクラッシュしなくなりました。(それでも他の副作用を引き起こす可能性があります)
- `PlayerKickEvent`は最初のログインシークエンスが完了する以前に発生した切断では着火されなくなりました。(例 リソースパックのダウンロード完了時)
- キャンセル可能なイベントはインターフェースの要件を満たすために必要なキャンセル可能なコンポーネントを得るための`CancellableTrait`を実装する必要があります。`Event`はこれらのメソッドをスタブを持たなくなりました。
- `PlayerInteractEvent`はプレイヤーがアイテムをアクティベートしたときに着火されなくなりました。これは古くからの問題であった`PlayerInteractEvent`が一回触った時にも繰り返し発火されてしまう問題を修正します。以下の定数は削除されました。
    - `PlayerInteractEvent::LEFT_CLICK_AIR`
    - `PlayerInteractEvent::RIGHT_CLICK_AIR`
    - `PlayerInteractEvent::PHYSICAL`
- 以下のイベントが追加されます。
    - `PlayerEntityInteractEvent`: プレイヤーがエンティティを右クリック(スマホでは長押し)した時のイベント
    - `PlayerItemUseEvent`: プレイヤーが持っているアイテムをアクティベートした時のイベント, 例えば雪玉を投げるなど。
    - `BlockTeleportEvent`: ブロックがテレポートした時のイベント、例えばエンドラの卵が攻撃されたときなど。
    - `PlayerDisplayNameChangeEvent`
    - `EntityItemPickupEvent`: プレイヤーやエンティティが落ちているアイテム(か矢)を拾った時のイベント。 `InventoryPickupItemEvent`と`InventoryPickupArrowEvent`の置き換え
        - 置き換え前とは違って、アイテムが入るインベントリを変更することができます。
        - もし入るインベントリを`null`にすると、アイテムは破棄されます。これはインベントリが満杯の状態でクリエイティブのプレイヤーが拾うときに見られる動作です。
    - `EntityTrampleFarmlandEvent`: 農地上でmobやプレイヤーがジャンプをして土に変化させるときのイベント。
    - `StructureGrowEvent`: 木や竹、その他の複数ブロックの苗木構造物が成長するときに呼ばれます。
- 以下のイベントは削除されました。
    - `EntityArmorChangeEvent`
    - `EntityInventoryChangeEvent`
    - `EntityLevelChangeEvent` - `EntityTeleportEvent`にてワールドの確認をすることで代用してください。
    - `InventoryPickupItemEvent` - `EntityItemPickupEvent`を代わりに使用してください。
    - `InventoryPickupArrowEvent` - `EntityItemPickupEvent`を代わり使用してください。
    - `NetworkInterfaceCrashEvent`
    - `PlayerCheatEvent`
    - `PlayerIllegalMoveEvent`
- 以下のAPIメソッドが追加されました。
    - `EntityDeathEvent->getXpDropAmount()`
    - `EntityDeathEvent->setXpDropAmount()`
    - `PlayerDeathEvent->getXpDropAmount()`
    - `PlayerDeathEvent->setXpDropAmount()`
- 以下のメソッドは削除されました。
    - `PlayerPreLoginEvent->getPlayer()`
    - `Cancellable->setCancelled()`: これにより`Cancellable`実装はそのイベント特有のキャンセル機構を実装できるようになりました。例えば`PlayerPreLoginEvent`における複雑な機構などです。
- 以下のAPIメソッドは移動されました。
    - `Event->isCancelled()` -> `CancellableTrait->isCancelled()`: これはクラスが`Cancellable`を実装していなかった場合に`BadMethodCallException`を投げるスタブでした。これは現在ではキャンセル不可能なイベントでは単に使えなくなっています。
    - `Event->setCancelled()`は`cancel()`と`uncancel()`に分割され、`CancellableTrait`に移動しました。
    - `HandlerList::unregisterAll()` -> `HandlerListManager->unregisterAll()`
    - `HandlerList::getHandlerListFor()` -> `HandlerListManager->getListFor()`
    - `HandlerList::getHandlerLists()` -> `HandlerListManager->getAll()`
- 以下のクラスは移動しました。
    - `pocketmine\plugin\RegisteredListener` -> `pocketmine\event\RegisteredListener`

#### Inventory
- すべてのクラフトとレシピに関するクラスは名前空間`pocketmine\crafting`に移動しました。
- 以下のクラスが追加されました。
    - `CallbackInventoryChangeListener`
    - `CreativeInventory`: 以前まで`pocketmine\item\Item`に埋め込まれていた機能群を含みます。詳細はアイテムの変更をご覧ください
    - `InventoryChangeListener`: 一つのインベントリのイベントを監視できます(ただし介入はできません)。
    - `transaction\CreateItemAction`
    - `transaction\DestroyItemAction`
- 以下のクラスはリネーム/移動されました。
    - `ContainerInventory` -> `pocketmine\block\inventory\BlockInventory`
- 以下のクラスは名前空間`pocketmine\block\inventory` に移動されました。
    - `AnvilInventory`
    - `ChestInventory`
    - `DoubleChestInventory`
    - `EnchantInventory`
    - `EnderChestInventory`
    - `FurnaceInventory`
- 以下のクラスは削除されました。
    - `CustomInventory`
    - `InventoryEventProcessor`
    - `Recipe`
    - `transaction\CreativeInventoryAction`
- 以下のAPIメソッドが追加されました。
    - `Inventory->addChangeListeners()`
    - `Inventory->getChangeListeners()`
    - `Inventory->removeChangeListeners()`
    - `Inventory->swap()`: 二つのスロットのコンテンツを入れ替えます。
- 以下のAPIメソッドは削除されました。
    - `BaseInventory->getDefaultSize()`
    - `BaseInventory->setSize()`
    - `Inventory->close()`
    - `Inventory->dropContents()`
    - `Inventory->getName()`
    - `Inventory->getTitle()`
    - `Inventory->onSlotChange()`
    - `Inventory->open()`
    - `Inventory->sendContents()`
    - `Inventory->sendSlot()`
    - `InventoryAction->onExecuteFail()`
    - `InventoryAction->onExecuteSuccess()`
    - `PlayerInventory->sendCreativeContents()`
- 以下のAPIメソッドはシグネチャが変更されました。
    - `Inventory->clear()`は`bool`の代わりに現在では`void`を返します。
    - `Inventory->setItem()`は`bool`の代わりに現在では`void`を返します。
    - `InventoryAction->execute()`は`bool`の代わりに現在では`void`を返します。
    - `BaseInventory->construct()`は初期化用のアイテムリストを受け取らなくなりました。
- `PlayerInventory->setItemInHand()`は現在ではそのプレイヤーが見える人にアイテム更新を送信します。

#### Item
##### General
- 新たに`VanillaItems`クラスが追加されました。すべての既知のアイテムタイプを生成する静的メソッドを含んでいます。これは定数が使われている`ItemFactory::get()`の代わりとして使われることが好ましいです。
- `StringToItemParser`が追加されました。あらゆる文字列をIDに関係なくアイテムへとマッピングすることができるようになりました。これらのマッピングは`/give`と`/clear`で使われていて、プラグインでのカスタムエイリアスを念頭に置いて作られています。
    - つまり、見苦しいハックなしに`/give`のカスタムエイリアスが追加できるようになったということです。
- `LegacyStringToItemParser`が追加されました。これは少しだけ動的な(しかし推奨はされない)`ItemFactory::fromString()`の置き換えです。
- `Item->count`はアクセス権がpublicから変更されました。
- 書き込める本(本と羽ペン,記入済みの本)のヒエラルキーが変更されました。`WritableBook`と`WrittenBook`は現在では`WritableBookBase`を継承します。
- 以下のAPIはシグネチャが変更されました。
    - `WritableBookBase->setPages()`は現在では`CompoundTag[]`の代わりに`WritableBookPage[]`を受け付けます。
    - `ItemFactory::get()`は`string`を`tags`パラメータとして受け付けなくなりました。
    - `ItemFactory::fromString()`は`$multiple`パラメータを受け取らなくなり、`Item|Item[]`ではなく`Item`のみを返すようになりました。
- 以下のメソッドはメソッドチェーンが利用できるようになりました。(原文: The following methods are now fluent)
    - `WritableBookBase->setPages()`
    - `Item->addEnchantment()`
    - `Item->removeEnchantment()`
    - `Item->removeEnchantments()`
    - `Armor->setCustomColor()`
    - `WrittenBook->setTitle()`
    - `WrittenBook->setAuthor()`
    - `WrittenBook->setGeneration()`
- 以下のAPIメソッドは削除されました。
    - `Item->getNamedTagEntry()`
    - `Item->removeNamedTagEntry()`
    - `Item->setDamage()`: 現在では`Durable`を実装しているアイテム以外ではダメージは不変です。
    - `Item->setNamedTagEntry()`
    - `Item::get()`: これはずいぶん前に`ItemFactory::get()`にとってかわられました。
    - `Item::fromString()`: これはずいぶん前に`ItemFactory::fromString()`にとってかわられました。
    - `Item->setCompoundTag()`
    - `Item->getCompoundTag()`
    - `Item->hasCompoundTag()`
    - `Potion::getPotionEffectsById()`
    - `ProjectileItem->getProjectileEntityType()`
- 以下の定数は削除されました。
    - `Potion::ALL` - `PotionType::getAll()`を代わりに使ってください。
    - `Potion::WATER`
    - `Potion::MUNDANE`
    - `Potion::LONG_MUNDANE`
    - `Potion::THICK`
    - `Potion::AWKWARD`
    - `Potion::NIGHT_VISION`
    - `Potion::LONG_NIGHT_VISION`
    - `Potion::INVISIBILITY`
    - `Potion::LONG_INVISIBILITY`
    - `Potion::LEAPING`
    - `Potion::LONG_LEAPING`
    - `Potion::STRONG_LEAPING`
    - `Potion::FIRE_RESISTANCE`
    - `Potion::LONG_FIRE_RESISTANCE`
    - `Potion::SWIFTNESS`
    - `Potion::LONG_SWIFTNESS`
    - `Potion::STRONG_SWIFTNESS`
    - `Potion::SLOWNESS`
    - `Potion::LONG_SLOWNESS`
    - `Potion::WATER_BREATHING`
    - `Potion::LONG_WATER_BREATHING`
    - `Potion::HEALING`
    - `Potion::STRONG_HEALING`
    - `Potion::HARMING`
    - `Potion::STRONG_HARMING`
    - `Potion::POISON`
    - `Potion::LONG_POISON`
    - `Potion::STRONG_POISON`
    - `Potion::REGENERATION`
    - `Potion::LONG_REGENERATION`
    - `Potion::STRONG_REGENERATION`
    - `Potion::STRENGTH`
    - `Potion::LONG_STRENGTH`
    - `Potion::STRONG_STRENGTH`
    - `Potion::WEAKNESS`
    - `Potion::LONG_WEAKNESS`
    - `Potion::WITHER`
- 以下のメソッドはリネームされました。
    - `Item->getDamage()` -> `Item->getMeta()`
- 以下のメソッドは `pocketmine\inventory\CreativeInventory`へと移動されました:
    - `Item::addCreativeItem()` -> `CreativeInventory::add()`
    - `Item::clearCreativeItems()` -> `CreativeInventory::clear()`
    - `Item::getCreativeItemIndex()` -> `CreativeInventory::getItemIndex()`
    - `Item::getCreativeItems()` -> `CreativeInventory::getAll()`
    - `Item::initCreativeItems()` -> `CreativeInventory::init()`
    - `Item::isCreativeItem()` -> `CreativeInventory::contains()`
    - `Item::removeCreativeItem()` -> `CreativeInventory::remove()`
- 以下のクラスが追加されました。
    - `ArmorTypeInfo`
    - `Fertilizer`
    - `LiquidBucket`
    - `MilkBucket`
    - `PotionType`: バニラのポーションタイプについての情報を保持する列挙クラス
    - `WritableBookBase`
    - `WritableBookPage`
- 以下のAPIメソッドが追加されました。
    - `Armor->getArmorSlot()`
    - `Item->canStackWith()`: 二つのアイテムが数量やサイズ上限は考慮せずに、同じアイテムスロットで保持できるかを返します。
    - `Potion->getType()`: 適用されるエッふぇくとなどの情報をもつ`PotionType`列挙オブジェクトを返します。
    - `ProjectileItem->createEntity()`: 投げられるプロジェクタイルエンティティの新規インスタンスを返します。
- 以下のクラスは削除されました。
    - `ChainBoots`
    - `ChainChestplate`
    - `ChainHelmet`
    - `ChainLeggings`
    - `DiamondBoots`
    - `DiamondChestplate`
    - `DiamondHelmet`
    - `DiamondLeggings`
    - `GoldBoots`
    - `GoldChestplate`
    - `GoldHelmet`
    - `GoldLeggings`
    - `IronBoots`
    - `IronChesplate`
    - `IronHelmet`
    - `IronLeggings`
    - `LeatherBoots`
    - `LeatherCap`
    - `LeatherPants`
    - `LeatherTunic`

##### NBT handling
- シリアライズされたNBTバイト配列キャッシュはアイテムスタックに保持されなくなりました。これらのキャッシュはネットワークレイヤーシリアライズに用いられる早すぎる最適化であり、それ自体がネットワークNBT形式に依存していました。
- 内部NBTの利用は周縁化されます。 もはや即座に変更点をNBTへ書き込む必要はありません。以下のフックが追加されます:
    - `Item->serializeCompoundTag()`
    - `Item->deserializeCompoundTag()`
- 実行時NBTをアイテムから完全に取り除くことが計画されていますが、現段階では未解決の後方互換性の問題があります。

##### Enchantment
- `VanillaEnchantments`クラスが追加されました。これはバニラのすべてのエンチャントを静的メソッドとして公開していて、 `Enchantment::get()`の意地の悪さを置き換えます。
    - 例: `Enchantment::get(Enchantment::PROTECTION)`は`VanillaEnchantments::PROTECTION()`で置き換えられました。
    - これらのメソッドは`Enchantment`の代わりに適切なタイプの情報を静的解析に渡し、コーディングがしやすくなりました。
- MCPEのエンチャントIDとPocketMine-MP内部のIDの境目がより明確になりました。
    - IDハンドリングは`pocketmine\data\bedrock\EnchantmentIdMap`へ移動しました。
    - すべてのエンチャントID定数は`Enchantment`から削除されました。何らかの理由でレガシーエンチャントIDが必要なら`pocketmine\data\bedrock\EnchantmentIds`を利用してください。
- `Enchantment::RARITY_*`定数は`Rarity`に移動され、`RARITY_`プレフィックスは削除されました。
- `Enchantment::SLOT_*`定数は`ItemFlags`に移動され、`SLOT_`プレフィックスは削除されました。
- 以下のAPIメソッドは移動されました。
    - `Enchantment::registerEnchantment()` -> `EnchantmentIdMap->register()`
    - `Enchantment::getEnchantment()` -> `EnchantmentIdMap->fromId()`
    - `Enchantment->getId()` -> `EnchantmentIdMap->toId()`
    - `Enchantment::getEnchantmentByName()` -> `VanillaEnchantments::fromString()`
- 以下のAPIメソッドが追加されました。
    - `Enchantment->getRuntimeId()`: これは**動的ID**でありエンチャントタイプの比較に用いることができます。**これは決して保持されないため、configやNBTでは利用しないでください！**
