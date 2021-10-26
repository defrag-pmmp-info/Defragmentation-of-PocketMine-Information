# API4でのサーバー構築
## 警告
これは安定版リリースでは**ありません**。予期しないバグが存在する可能性があります。

## 必要なファイルの準備
- **PocketMine-MP.phar**
- **PHPバイナリ(API4用)**
- **start.cmd/start.ps1/start.sh** API3のものが利用可能

### PocketMine-MP.pharのダウンロード
API4のものは現段階ではPre-releaseでありGitHubのreleasesからダウンロードできます。
https://github.com/pmmp/PocketMine-MP/releases

### PHPバイナリのダウンロード
API4ではPHP拡張の一部が変更されたため、それが導入されているPHPバイナリが必要になります。以下の手順でダウンロードしてください。

![masterブランチのLast run](/image/e987af6f-2a36-474c-a926-69272498c823.png)
マスターブランチのLast run以下をクリック

![published](/image/d9f2aaa6-3d32-4f49-921c-c0698ff28ea2.png)
publishedをクリック

![artifact](/image/0ecfc460-fe8d-41e9-b62f-9c21cff0298e.png)
対応するOSの︙をクリックし、ダウンロードを選択

## ダウンロード後
これ以降は[API3の手動インストール](/building/README.md#_2)と同様の手順で進められます。