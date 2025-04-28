# user_addresses

| Column Name  | Data Type                   | Constraints                            | Description          |
|--------------|-----------------------------|----------------------------------------|----------------------|
| id           | SERIAL                      | PRIMARY KEY                            | 住所ID               |
| user_id      | INTEGER                     | NOT NULL REFERENCES users(id)          | ユーザID             |
| postal_code  | VARCHAR(20)                 | NOT NULL                               | 郵便番号             |
| address      | VARCHAR(255)                | NOT NULL                               | 住所                 |
| is_default   | BOOLEAN                     | NOT NULL DEFAULT FALSE                 | デフォルト住所フラグ |
| created_at   | TIMESTAMP WITH TIME ZONE    | NOT NULL DEFAULT now()                 | 登録日時             |
