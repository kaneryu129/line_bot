# 設計

## 概要

RubyとSinatraを使って、個人学習用の小さなLINE Botを実装する。

ユーザーがLINEでbotにメッセージを送ると、LINE PlatformがWebhookイベントをアプリケーションに送信する。アプリケーションはイベント内容を判定し、LINE Messaging APIを使ってテキスト、画像、スタンプなどを返信する。

## アーキテクチャ

想定する技術スタックは以下の通り。

- 言語: Ruby
- Webフレームワーク: Sinatra
- LINE SDK: line-bot-sdk-ruby
- 環境変数管理: dotenv
- テスト: RSpec
- ローカル公開: ngrok または Cloudflare Tunnel

想定構成:

```text
LINEアプリ
  ↓
LINE Platform
  ↓ Webhook
Ruby + Sinatraアプリ
  ↓
LINE Messaging API
  ↓
LINEアプリへ返信
```

## LINE Webhookフロー

1. 開発者がLINEでbotにメッセージを送信する。
2. LINE PlatformがWebhook URLにイベントをPOSTする。
3. Sinatraアプリが `/callback` などのエンドポイントでWebhookイベントを受け取る。
4. リクエスト署名を検証する。
5. イベント種別とメッセージ内容を判定する。
6. reply tokenを使って応答メッセージを送信する。

ローカル開発では、`localhost` のアプリをLINE Platformから直接呼び出せないため、ngrokまたはCloudflare TunnelでWebhook URLを公開する。

## メッセージ設計

初期実装では、以下の返信を想定する。

| 入力メッセージ | 返信内容 |
| --- | --- |
| `こんにちは` | テキストメッセージを返信する |
| `スタンプ` | LINE公式スタンプを返信する |
| `画像` | 公開URL上の画像を返信する |
| その他 | おうむ返しで返信する |

学習目的のため、まずはreply messageを中心に実装する。push message、multicast、broadcastなどの任意タイミング送信は、無料枠や通数カウントへの影響を理解してから扱う。

## データモデル

初期段階ではデータベースを使用しない。

会話履歴、ユーザー情報、設定値などの永続化が必要になった場合のみ、SQLiteなど無料で扱える軽量な選択肢を検討する。

## 外部連携

- LINE Messaging API
- LINE Developers
- LINE公式アカウント
- ngrok または Cloudflare Tunnel
- 画像返信に利用する公開画像URL

## エラーハンドリング

- 署名検証に失敗した場合はリクエストを拒否する。
- 未対応のイベント種別は無理に処理せず、ログに出力する。
- メッセージ送信に失敗した場合は、エラー内容をログに出力する。
- 学習用のため、初期段階では高度なリトライやアラートは実装しない。

## セキュリティ

- LINEからのWebhookリクエストは署名検証を行う。
- チャネルシークレット、チャネルアクセストークンは環境変数で管理する。
- `.env` など機密情報を含むファイルはGit管理しない。
- 個人学習用のため、不要なユーザーデータは保存しない。

## テスト方針

- メッセージ内容ごとの分岐はRSpecでテストする。
- Webhookの疎通はLINE DevelopersのWebhook検証機能または実際のLINEアプリから手動確認する。
- 画像返信やスタンプ返信は、実機上のLINEアプリで表示確認する。

## 未決事項

- 画像返信に利用する画像URLの管理方法。
- ngrokとCloudflare Tunnelのどちらを採用するか。
- Sinatraアプリのファイル構成。
- RSpecでどこまでテストするか。
