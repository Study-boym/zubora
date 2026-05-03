# アプリケーション設計

## システム構成図

```
[ユーザー（スマホ）]
       │
       ▼
[フロントエンド]
 AWS Amplify (Next.js PWA)
 ・チャット画面
 ・デイリーレコメンド表示
 ・ユーザー設定
       │
  ┌────┴─────────────┐
  │（REST）          │（WebSocket）
  ▼                  ▼
[Amazon API Gateway]  [API Gateway WebSocket API]
       │                       │
       ▼                       ▼
[AWS Lambda]           [AWS Lambda]
 ・レコメンド生成           ・チャット処理
 ・設定CRUD                ・感情検知
       │                       │
  ┌────┴───────────────────────┤
  ▼                            ▼
[Amazon Bedrock]        [Amazon DynamoDB]
 Claude Sonnet           ・会話履歴テーブル
 / Claude Haiku          ・ユーザー設定テーブル

[Amazon EventBridge Scheduler]
  │ 毎朝/毎晩 定時起動
  ▼
[AWS Lambda] → [Amazon Bedrock] → [Amazon SNS / Pinpoint]
 レコメンド生成              プッシュ通知配信

[Amazon Cognito]
 ユーザー認証・セッション管理
```

---

## 技術スタック

| レイヤー | AWSサービス / 技術 | 用途 |
|---|---|---|
| フロントエンド | AWS Amplify + Next.js | ホスティング・CI/CD込み |
| REST API | Amazon API Gateway + AWS Lambda | 設定管理・レコメンド取得 |
| リアルタイムチャット | API Gateway WebSocket + Lambda | 双方向チャット通信 |
| AI推論 | Amazon Bedrock (Claude) | チャット応答・レコメンド生成 |
| DB | Amazon DynamoDB | 会話履歴・ユーザー設定（NoSQL） |
| 認証 | Amazon Cognito | ユーザー管理・JWT発行 |
| プッシュ通知 | Amazon SNS + Amazon Pinpoint | デイリー通知配信 |
| スケジューラ | Amazon EventBridge Scheduler | 定時レコメンド生成トリガー |
| CDN / 配信 | Amazon CloudFront + S3 | 静的アセット配信 |
| 監視 | Amazon CloudWatch | ログ・メトリクス・アラート |

---

## Amazon Bedrock 活用方針

| 用途 | モデル | プロンプト設計 |
|---|---|---|
| バーマスターチャット | Claude Sonnet (claude-sonnet-4-6) | 「傾聴・共感を第一とするバーのマスター」ペルソナをシステムプロンプトで固定。DynamoDBから取得した会話履歴をコンテキストとして付与 |
| デイリーレコメンド生成 | Claude Haiku (claude-haiku-4-5) | ユーザーの好み・天気・季節をプロンプトに埋め込み、EventBridge Schedulerで定時生成。低コスト・高速なHaikuを採用 |
| 感情検知 | Claude Haiku (claude-haiku-4-5) | 直近の発言を分類（ポジティブ / ネガティブ / 中立）し、チャットモデルへの返答スタイル指示に反映 |
