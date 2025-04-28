# users

| Column Name   | Data Type                   | Constraints                | Description            |
|---------------|-----------------------------|----------------------------|------------------------|
| id            | SERIAL                      | PRIMARY KEY                | ユーザID               |
| email         | VARCHAR(255)                | NOT NULL, UNIQUE           | メールアドレス         |
| password_hash | VARCHAR(255)                | NOT NULL                   | ハッシュ化パスワード    |
| name          | VARCHAR(100)                | NOT NULL                   | 氏名                   |
| created_at    | TIMESTAMP WITH TIME ZONE    | NOT NULL DEFAULT now()     | アカウント作成日時     |
