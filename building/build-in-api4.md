# API4でのサーバー構築
## 警告
これは安定版リリースでは**ありません**。予期しないバグが存在する可能性があります。

## https://get.pmmp.io の利用(Linux/MacOS 限定)
`curl`または`wget`を用いてPocketMine-MPをインストールできます。
```bash
curl -sL https://get.pmmp.io | bash -s - -v beta
```
または
```bash
wget -q -O - https://get.pmmp.io | bash -s - -v beta
```
を実行してください。

## 手動インストール
### 必要なファイルの準備
- **PocketMine-MP.phar**
- **PHPバイナリ(API3・API4共用)** バイナリが更新されたため、Jenkinsからダウンロードするものが使用できるようになりました。
- **start.cmd/start.ps1/start.sh** API3のものが利用可能

#### PocketMine-MP.pharのダウンロード
API4のものは現段階ではPre-releaseでありGitHubのreleasesからダウンロードできます。
<https://github.com/pmmp/PocketMine-MP/releases>


## ダウンロード後
これ以降は[API3の手動インストール](/building/README.html#_2)と同様の手順で進められます。

