cloudfront-sample
======================

S3バケットのWebコンテンツをCloudfrontで公開するサンプルテンプレート


## デプロイ

S3+CloudFrontでホスティングできるよう `web-host` 内に準備してあります。

1. `web-host/samconfig.toml` の `OriginBucket=<bucket>` 部分を編集します。
    - `<bucket>` 部分へS3バケット名を指定します
    - S3バケットもデプロイリソースに含まれます
    - また、Cloudfrontログ出力用の `<bucket>-log` バケットもデプロイされます
2.  下記のようにコマンドを実行し、デプロイします。(CloudFrontデプロイのため、5～10分程度待ちます)

```
cd web-host
sam build
sam deploy --profile <awsprofile>
```

デプロイ完了時、Outputsに表示される下記情報をコピーしておきます。

- CloudFrontDistributionDomainName
  - WebページURLになります
- WebContentsS3BucketName
  - Webページコンテンツを配置するS3バケットです


## WEBページをアップロード

Webコンテンツファイルをアップロードします。
サンプルとして `web-contents/*` を参考にしてください。

AWS CLIの場合は下記のように実行します

```
aws --profile <awsprofile> s3 sync web-contents/ s3://<Webサイトデプロイで指定したバケット名>/
```


## 動作確認

ウィジェットを配置したWEBページをブラウザでアクセスし、確認します
