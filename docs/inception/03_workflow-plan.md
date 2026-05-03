# ワークフロー計画

## 開発フェーズ

```
Phase 0: INCEPTION（現在）
  └── 要件定義・設計・ドキュメント整備

Phase 1: MVP（コアループ構築）
  ├── デイリーレコメンド（献立・服装）の基本プッシュ通知
  └── フリートーク + 相談モードのチャットUI

Phase 2: 習慣化強化
  ├── 会話の記憶・引用機能（F-10）
  ├── 感情検知による返答スタイル切り替え（F-11）
  ├── 余暇提案・おやつ提案・週間献立機能の追加（F-03〜F-05）
  └── モーニングメッセージ（F-06）

Phase 3: マネタイズ（有料プラン）
  └── 詳細は README の「将来展望」セクション参照（検討中）
```

---

## 技術タスク（Phase 1）

| タスク | AWSサービス | 優先度 |
|---|---|---|
| AIチャット・レコメンド生成 | Amazon Bedrock (Claude) | 最高 |
| プッシュ通知の配信 | Amazon SNS / Amazon Pinpoint | 高 |
| 定時レコメンド生成のトリガー | Amazon EventBridge Scheduler | 高 |
| チャットUI + フロントエンド | AWS Amplify (Next.js) | 高 |
| リアルタイムチャット API | API Gateway WebSocket + Lambda | 高 |
| ユーザー設定画面 | AWS Amplify (Next.js) | 中 |
| 会話履歴の保存・取得 | Amazon DynamoDB + Lambda | 中 |
