# サーバー構築
ここではサーバー構築について記述します
API4.0.0(ベータ版)での構築は[API4.0.0での構築](./build-in-API4.html)を参照してください

このページは [pmmp.readthedocs.io](https://pmmp.readthedocs.io/en/rtfd/installation/requirements.html)を参考にしています。

## https://get.pmmp.io の利用(Linux/MacOS 限定)
PocketMine-MPをインストールしたいディレクトリを作成して、そのディレクトリを端末で開きます。

`curl`または`wget`を用いてPocketMine-MPをインストールできます。
```bash
curl -sL https://get.pmmp.io | bash -s -
```
または
```bash
wget -q -O - https://get.pmmp.io | bash -s -
```
するとこのような表示が出るはずです。
```
[*] Found PocketMine-MP Final_1.5dev (build 1254) using API 1.12.0
[*] This development build was released on Sat Jun 20 09:45:04 CEST 2015
[*] Installing/updating PocketMine-MP on directory ./
[1/3] Cleaning...
[2/3] Downloading PocketMine-MP Final_1.5dev-1254 phar... done!
[3/3] Obtaining PHP: detecting if build is available...
[3/3] MacOS 64-bit PHP build available, downloading PHP_5.6.10_x86-64_MacOS.tar.gz... checking... regenerating php.ini... done
[*] Everything done! Run ./start.sh to start PocketMine-MP
```
!!! info
    うまく動作しなかったら手動インストールを試してみてください

## 手動インストール
### 必要なファイルの準備
PocketMine-MPを最低限起動するためには
- **PocketMine-MP.phar** サーバーの本体
- **PHPバイナリ** サーバーを動作させるプログラム
- **スタート用スクリプト** サーバーを起動するためのファイル
    - start.cmd / start.ps1 (Windows)
    - start.sh (Linux/Mac)

以上の3つが必要です。

