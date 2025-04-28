# Sessionテーブル一覧

以下に、ECサイトのフロントおよび管理サイトで稼働するSessionテーブル一覧をまとめた表を示します。

## テーブル一覧

| No. | テーブル論理名              | テーブル物理名            | 説明                                                                                              |
| --- | --------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------- |
| 1   | セッション (キャッシュ)     | sessions                  | ElastiCache for Redis により高速キャッシュする稼働中セッション情報を保持します :contentReference[oaicite:6]{index=6} |
| 2   | 永続セッション              | persistent_sessions       | キャッシュ切れや障害時のためにセッション情報を永続化するテーブルです :contentReference[oaicite:7]{index=7}           |
| 3   | セッショントークン          | session_tokens            | 認証トークンやCSRFトークンなど複数トークンを管理します :contentReference[oaicite:8]{index=8}                       |
| 4   | セッション履歴              | session_history           | 過去のセッション変更ログや有効化／無効化履歴を保存し、監査用に利用します :contentReference[oaicite:9]{index=9}         |
| 5   | セッションロック            | session_locks             | 分散ロック制御のためのセッションロック情報を管理し、同時更新を防止します :contentReference[oaicite:10]{index=10}        |
| 6   | セッションメタ情報          | session_meta              | ユーザーエージェントやIPアドレスなどの補足情報を管理します :contentReference[oaicite:11]{index=11}                   |

