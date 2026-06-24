# 運用

## 環境

このプロジェクトは個人学習用であり、本番リリースは想定しない。

初期段階ではローカル環境のみを利用する。

- ローカル: 開発者のPCでRuby + Sinatraアプリを起動する。
- 外部公開: ngrokまたはCloudflare Tunnelでローカルアプリを一時的に公開する。
- 本番環境: 用意しない。

## 環境変数

想定する環境変数:

```text
LINE_CHANNEL_SECRET=
LINE_CHANNEL_TOKEN=
```

ローカル開発では `.env` を使って管理する想定。ただし、`.env` は機密情報を含むためGit管理しない。

共有用には `.env.example` を用意する。

## LINE Developers設定

LINE DevelopersでMessaging APIチャネルを作成し、LINE公式アカウントと連携する。

設定する主な項目:

- チャネルシークレットを取得する。
- チャネルアクセストークンを発行する。
- Webhookの利用を有効にする。
- Webhook URLに、ngrokまたはCloudflare Tunnelで公開したURLを設定する。
- 応答メッセージをbot側で制御するため、必要に応じてLINE公式アカウント側の自動応答設定を確認する。

Webhook URLの例:

```text
https://example.ngrok-free.app/callback
```

## ローカル開発

想定手順:

1. Ruby環境を用意する。
2. 必要なgemをインストールする。
3. `.env` にLINEのチャネルシークレットとチャネルアクセストークンを設定する。
4. Sinatraアプリをローカルで起動する。
5. ngrokまたはCloudflare Tunnelでローカルポートを公開する。
6. 公開URLをLINE DevelopersのWebhook URLに設定する。
7. LINEアプリからbotにメッセージを送り、返信を確認する。

想定ポート:

```text
http://localhost:4567
```

## デプロイ

本番デプロイは行わない。

無料学習を前提とし、ローカル環境と一時的なWebhook公開で検証する。

## 監視とログ

初期段階では、Sinatraアプリの標準出力ログで確認する。

確認対象:

- Webhookを受信できているか。
- 署名検証に失敗していないか。
- メッセージ種別を正しく判定できているか。
- LINE Messaging APIへの返信リクエストが成功しているか。

## 障害対応

想定される問題:

- Webhook URLにLINEからアクセスできない。
- ngrokまたはCloudflare TunnelのURL変更後、LINE Developers側のWebhook URLを更新していない。
- チャネルシークレットまたはチャネルアクセストークンが間違っている。
- 署名検証に失敗している。
- 画像返信に利用する画像URLが外部からアクセスできない。
- 無料メッセージ通数の扱いを誤り、push messageなどを多用している。

## メンテナンス

学習用のため、定期運用は最小限とする。

- LINE Developersの設定を変更した場合は、このドキュメントを更新する。
- 実装手順や起動コマンドが固まったら、`README.md` と `AGENTS.md` に反映する。
- 有料機能を使いそうになった場合は、事前に無料枠や料金を確認する。
