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

## PowerShellスクリプトの利用(Windows 限定)
PocketMine-MPをインストールしたいディレクトリを作成して、そのディレクトリをPowerShellで開きます。
```PowerShell
wget https://gist.githubusercontent.com/famima65536/d1493bd3e4713f09aed2c70e428a5064/raw/0935f747077fe3454b329801a93fe30bb1b55919/install.ps1 -OutFile temp-install.ps1; ./temp-install.ps1; Remove-Item temp-install.ps1
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

以上の3つが必要です。[リンク集](/link.html)を見てダウンロードしてください。

### ディレクトリ配置
バイナリはzip形式のため展開し、以下のようにファイルを配置します。
```
|-- bin/
|-- PocketMine-MP.phar
|-- start.cmd(またはstart.ps1)
```

### サーバーの起動
start.cmdまたはstart.ps1を実行することでサーバーが起動されます。