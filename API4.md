# API4

コンテンツリスト
- [変更点の抄訳](#変更点の抄訳)
- [開発者向け情報](#開発者向け情報)

## 変更点の抄訳
参考 <https://github.com/pmmp/PocketMine-MP/blob/master/changelogs/4.0.md>

全訳は[API4changelogs](/API4changelogs)より
### BETA1
2021.9.7リリース
API4は重要なAPIの変更,新しいワールド形式のサポート,パフォーマンスの向上やネットワークの刷新を含めたコア全体の重要な変更に焦点を当てています。

#### 内容
- [Core](#General)

##### 一般 General
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
- `/enchat` コマンドは数学IDをサポートしなくなりました。現在では名前が要求されます。
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
    - `chunk-ticking.light-updates`: 明るさ計算は基本的なバニラの機能を動作させるのに必要なため、チャンクチックを無効化することなくこの機能を停止することは意味がありません。もし、明るさ計算をしたくなければチャンクチックと共に無効化するようにしてください。
    - `player.anti-cheat.allow-movement-cheats`

## 開発者向け情報
### DevTools API4対応版の配布場所
poggit上でダウンロード可 <https://poggit.pmmp.io/ci/pmmp/DevTools/PocketMine-DevTools> の Build History:master により最新版がダウンロード可能