## 1. 外部インタフェースID

`IF-101-S3`

## 2. 外部インタフェース名

日次売上レポートファイル配置IF

## 3. 外部インタフェース概要

前日の売上集計結果（CSV/JSON）をAmazon S3に配置し、後続バッチやダッシュボード更新で利用する外部インタフェース。

---

## 4. 外部インタフェース処理説明

### 4.1 前提条件
- **接続先相手システム名**：Amazon S3  
- **接続先プラットフォーム**：AWS S3  
- **送・受信の識別**：送信  
- **データ量**：可変（平均 10 MB/日）  
- **全体レコード長**：可変  
- **保存期間**：90日  
- **保存場所**：`s3://<bucket>/daily_reports/`  
- AWS認証情報（IAMロールまたはアクセスキー）が設定済み  

### 4.2 本処理
1. 日次集計処理でローカルに以下のファイルを生成  
   - `YYYY-MM-DD.csv`  
   - `YYYY-MM-DD.json`  
2. AWS CLI／SDKで S3 にアップロード  
   ```bash
   aws s3 cp ./YYYY-MM-DD.csv  s3://<bucket>/daily_reports/
   aws s3 cp ./YYYY-MM-DD.json s3://<bucket>/daily_reports/
   ```

### 4.3 終了処理
- AWS CLI／SDK の戻りコードが `0` であることを確認  
- S3 バケット内に両ファイルが配置されていることを確認  

### 4.4 例外処理
- アップロード失敗時は60秒間隔で最大3回リトライ  
- 3回とも失敗した場合、SNS/S lack 通知でアラート発行  
```