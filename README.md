# Kong API GatewayのGUI/Kongaを構築する

## はじめに

`Mac環境の記事ですが、Windows環境も同じ手順になります。環境依存の部分は読み替えてお試しください。`

### 目的

この記事を最後まで読むと、次のことができるようになります。

| No.  | 概要         | キーワード                  |
| :--- | :----------- | :-------------------------- |
| 1    | 仮装環境構築 | VirtualBox, Vagrant         |
| 2    | Kong構築     | Kong, PostgreSQL            |
| 3    | Konga構築    | npm, Sails, Git, nvm, Konga |

### 実行環境

| 環境           | Ver.    |
| :------------- | :------ |
| macOS Catalina | 10.15.3 |
| CentOS         | 7.0     |
| VirtualBox     | 6.0     |
| Vagrant        | 2.2.8   |
| Kong           | 2.0.4   |
| PostgreSQL     | 11.7    |
| npm            | 3.10.10 |
| Sails          | 1.2.4   |
| Git            | 1.8.3.1 |
| nvm            | 0.35.3  |
| Konga          | 0.14.7  |

### 関連する記事

- [Kong API Gatewayを構築する](https://qiita.com/nsuhara/items/ad1d8fa1faad7940b5c1)
- [Konga](https://pantsel.github.io/konga/)

## 全体の流れ

1. Kongaとは
2. Kongaをインストールする
3. Kongaを設定する
4. Kongaを起動する
5. Kongaを確認する

## 1. Kongaとは

### Kongaとは

KongのGUI環境を提供するオープンソース。アドミン管理などGUIにて操作する。

### オープンソース版のKong

GUI環境を持たないため、運用の際はCUIにて操作する。

### エンタープライズ版のKong

もとからGUIが提供される。プラグインで容易にサービスの追加が可能。

## 2. Kongaをインストールする

### 前提条件

1. Kong環境がセットアップ済みであること。未完了の場合は、[Kong API Gatewayを構築する](https://qiita.com/nsuhara/items/ad1d8fa1faad7940b5c1)を参考にセットアップください。以降は、Kong環境をそのまま流用してKongaを設定していきます。

### npmインストール

```command.sh
~$ sudo yum install epel-release
~$ sudo yum install npm
```

### Sailsインストール

```command.sh
~$ sudo npm install bower gulp sails -g
```

### Gitインストール

```command.sh
~$ sudo yum install git
```

### nvmインストール

```command.sh
~$ git clone git://github.com/creationix/nvm.git ~/.nvm
~$ source ~/.nvm/nvm.sh
~$ nvm install 9.0.0
~$ nvm use 9.0.0
```

### Kongaインストール

```command.sh
~$ git clone https://github.com/pantsel/konga.git
~$ cd konga
~$ npm install
```

## 3. Kongaを設定する

### Konga設定

```command.sh
~$ cd config
~$ cp -pr local_example.js local.js
~$ vi local.js

- // kong_admin_url : process.env.KONG_ADMIN_URL || 'http://127.0.0.1:8001',
+ kong_admin_url: process.env.KONG_ADMIN_URL || 'http://192.168.33.77:8001',
```

## 4. Kongaを起動する

### Konga起動

```command.sh
~$ cd ~/konga
~$ npm start
```

### ブラウザ起動

1. 接続は`1338`ポートとなる
2. ブラウザで`http://192.168.33.77:1338`に接続する

## 5. Kongaを確認する

### Users登録

1. 画面指示に従いユーザを登録する

### Connections登録

1. `Name`に任意の名前を入力する
2. `Kong Admin URL`に`Kong Admin API`を入力する
3. `CREATE CONNECTION`をクリックする

| 項目名         | 値                                             |
| :------------- | :--------------------------------------------- |
| Name           | kong                                           |
| Kong Admin URL | [http://localhost:8001](http://localhost:8001) |

<img width="750" alt="スクリーンショット 2020-05-10 14.02.45.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/326996/22a4274f-bd83-aba3-f851-3bf9c9e83746.png">

<img width="750" alt="スクリーンショット 2020-05-10 14.03.16.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/326996/452b3524-41c4-ac2b-1624-6b5591086bf4.png">

### Services登録

1. 事前にcurlで登録しておくと一覧に表示される

```command.sh
~$ curl -i -X POST \
--url http://localhost:8001/services/ \
--data 'name=service-yahoo-news-rss' \
--data 'url=https://news.yahoo.co.jp/pickup/rss.xml'
```

<img width="750" alt="スクリーンショット 2020-05-10 14.04.27.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/326996/9f2168ab-bc38-0dc1-dab4-d57bb8c7d1c2.png">

### Routes登録

1. 事前にcurlで登録しておくと一覧に表示される

```command.sh
~$ curl -i -X POST \
--url http://localhost:8001/services/service-yahoo-news-rss/routes \
--data 'hosts[]=route-yahoo-news-rss'
```

<img width="750" alt="スクリーンショット 2020-05-10 14.04.35.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/326996/ea7a41c7-5f8e-5f2b-91c6-26c887f25587.png">
