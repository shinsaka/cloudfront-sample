amazon-connect-webcall
======================


web通話のtokenリクエストを確認するためのサンプル


## 設定

- Amazon Connectのウィジェットを設定
  - https://docs.aws.amazon.com/ja_jp/connect/latest/adminguide/add-chat-to-website.html
  - ドメインは次のWebサイトデプロイ語に指定しますので、ここではダミーのドメインを指定しておきます
- セキュリティキーを使用するよう設定し、セキュリティキーを `sam-app/samconfig.toml` のパラメーター `SecurityKey` へ指定します
- ウィジェットのスクリプトコードからウィジェットIDをコピーし、 `sam-app/samconfig.toml` のパラメーター `WidgetId` へ指定します

## Webサイトデプロイ

S3+CloudFrontでホスティングできるよう `web-host` 内に準備してあります。
デプロイ前に `web-host/samconfig.toml` の `OriginBucket=webcall-web-page` 部分を編集します。
本デプロイを実行すると新規S3バケットを作成します。 `webcall-web-page` 部分がS3バケット名になりますので、固有になるよう編集します。

その上で、下記のようにコマンドを実行し、デプロイします。(CloudFrontデプロイのため、5～10分程度待ちます)

```
cd web-host
sam build
sam deploy
```

デプロイ完了時、Outputsに表示される下記情報をコピーしておきます。

- CloudFrontDistributionDomainName
  - WebページURLになります
- WebContentsS3BucketName
  - Webページコンテンツを配置するS3バケットです

再度Amazon Connectのウィジェット設定ページへアクセスし、デプロイされたCloudFrontURLを許可ドメインとして指定します。
`https://xxxxxxxxxxxxx.cloudfront.net` のようになります


## アプリケーションデプロイ

API Gateway+Lambda(python) 構成のサンプルアプリケーションをデプロイします。

- pipenv+AWS SAMの環境を前提としています
- 初回は `sam deploy --guided` でデプロイ設定を行います

```
cd sam-app
sam build
sam deploy
```

デプロイ完了時、OutputsにAPI GatewayeエンドポイントURLが表示されますのでコピーします。

## ウィジェットを配置したWEBページを準備

ウィジェットを配置したWEBページを準備します。
サンプル `web/index.html` を参考にしてください。
`authenicate` 部分のfetch URLをアプリケーションデプロイ時のAPI GatewayのエンドポイントURLに書き換えます。
パスは `token` ですので、下記のようになります。

`https://xxxx.execute-api.ap-northeast-1.amazonaws.com/v1/token`

編集した `index.html` をS3バケットへアップロードします

```
aws s3 sync web/ s3://<Webサイトデプロイで指定したバケット名>/
```

## 動作確認

- ウィジェットを配置したWEBページをブラウザで開き、チャットウィジェットを開くとWeb通話が開始します。
- エージェント側で通話を受け、通話動作を確認します。
