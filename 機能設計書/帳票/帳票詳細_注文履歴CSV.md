# 帳票詳細_RP-002 注文履歴CSV

## 1. 帳票ID

`RP-002`

## 2. 帳票名

注文履歴CSV

## 3. 帳票概要

ログイン中のユーザーが指定した期間内の過去注文データをCSV形式でエクスポートする帳票。注文ID、注文日、商品情報、数量、単価、小計、合計金額、注文ステータスなどを含む。

## 4. 出力フォーマット

- 形式：CSV  
- 文字コード：UTF-8  
- 区切り文字：`,`（カンマ）  
- 改行文字：`\n`  
- ヘッダー行あり  

### 4.1 出力レイアウト

CSV以外にも以下のレイアウトを想定可能：  
- **テーブルHTML**：Web上でプレビュー表示用  
- **PDF**：帳票として印刷／メール添付用  
- **Excel（XLSX）**：集計やフィルタリングを行いたい場合  
- **JSON**：他システム連携用の生データフォーマット  

## 5. 帳票項目定義

| 項目名           | データ型        | 出力順 | 説明                                          | DBテーブル名     | DBカラム名         | 備考                       |
|-----------------|---------------|-------|---------------------------------------------|-----------------|--------------------|---------------------------|
| orderId         | string        | 1     | 注文ID（UUID）                                 | orders          | order_id           |                             |
| orderDate       | datetime      | 2     | 注文日時                                       | orders          | order_date         | ISO 8601形式             |
| productId       | string        | 3     | 商品ID（UUID）                                 | order_items     | product_id         |                             |
| productName     | string        | 4     | 商品名                                        | products        | name               | 最大100文字                |
| quantity        | integer       | 5     | 注文数量                                       | order_items     | quantity           | 1以上                      |
| unitPrice       | integer       | 6     | 単価（円）                                     | order_items     | unit_price         | 0以上                      |
| lineTotal       | integer       | 7     | 小計（quantity × unitPrice）（円）             | —               | —                  | 計算値                      |
| orderStatus     | string        | 8     | 注文ステータス（例：Pending／Shipped／Cancelled） | orders          | status             | 列挙型                     |
| shippingAddress | string        | 9     | 配送先住所                                     | shipments       | address            | 改行は`|`に置換             |
| paymentMethod   | string        | 10    | 支払方法（例：CreditCard／BankTransfer）        | payments        | method             | 列挙型                     |
| totalAmount     | integer       | 11    | 合計金額（円）                                 | orders          | total_amount       | 0以上                      |

## 6. 入力パラメータ定義

| 項目名     | データ型   | 必須 | 説明                      | 備考                   |
|-----------|----------|-----|-------------------------|-----------------------|
| startDate | date     | O   | 開始日（yyyy-MM-dd）       | 過去 1 年以内推奨      |
| endDate   | date     | O   | 終了日（yyyy-MM-dd）       | startDate ≤ endDate   |

## 7. バリデーション

| 対象             | ルール                                         | 発生タイミング     | メッセージID        |
|-----------------|-----------------------------------------------|------------------|--------------------|
| startDate       | 必須、日付形式（yyyy-MM-dd）                   | リクエスト受信時   | MSG_VAL_ORH_01     |
| endDate         | 必須、日付形式、startDate ≤ endDate            | リクエスト受信時   | MSG_VAL_ORH_02     |
| quantity        | 整数、≥1                                      | データ集計後      | MSG_VAL_ORH_03     |
| unitPrice, totalAmount, lineTotal | 整数、≥0                         | データ集計後      | MSG_VAL_ORH_04     |
| orderStatus     | 列挙型内の値                                 | データ集計後      | MSG_VAL_ORH_05     |

## 8. 帳票生成フロー

1. ユーザーがマイページの「注文履歴CSV出力」画面で期間（startDate, endDate）を入力  
2. 「出力」ボタン押下 → フロントで入力チェック  
3. バックエンドへリクエスト送信  
   ```
   GET /api/orders/csv
   ?startDate=YYYY-MM-DD&endDate=YYYY-MM-DD
   Authorization: Bearer <アクセストークン>
   ```  
4. サーバーで該当注文データをDBから取得 → 集計・CSV／指定レイアウトで帳票生成  
5. ファイルをレスポンスとして返却  
   - CSV: `Content-Type: text/csv; charset=UTF-8`  
   - PDF: `Content-Type: application/pdf`  
   - XLSX: `Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`  
   - JSON: `Content-Type: application/json`  
   ※ `Content-Disposition: attachment; filename="order_history_YYYYMMDD.ext"`  

## 9. 処理イベント

| イベント            | フロント処理                                                | サーバー処理                                                             | 成功時操作                                              | エラー時操作                                           |
|--------------------|-----------------------------------------------------------|------------------------------------------------------------------------|-------------------------------------------------------|------------------------------------------------------|
| 帳票出力ボタンクリック | 入力チェック → ボタン無効化 → ローディング表示                     | パラメータバリデーション → DBクエリ → 帳票生成                         | ダウンロード開始 → ボタン有効化 → ローディング解除       | エラーメッセージ表示 → ボタン有効化 → ローディング解除 |
| レスポンス受信       | ローディング中                                                | —                                                                      | ファイル保存ダイアログ表示                               | —                                                    |