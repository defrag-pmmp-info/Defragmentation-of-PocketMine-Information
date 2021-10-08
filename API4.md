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


## 開発者向け情報
### DevTools API4対応版の配布場所
poggit上でダウンロード可 <https://poggit.pmmp.io/ci/pmmp/DevTools/PocketMine-DevTools> の Build History:master により最新版がダウンロード可能