# sessions

## sessions

| Key Pattern             | Data Structure | Fields                                                | TTL (秒) | Description                           |
|-------------------------|----------------|-------------------------------------------------------|----------|---------------------------------------|
| `session:{session_id}`  | HASH           | user_id, creation_time, last_access_time, attributes* | 3600     | 個別セッション情報を格納              |



## Fields Json項目定義

| Field Name        | Type      | Required | Description                          |
|-------------------|-----------|----------|--------------------------------------|
| user_id           | string    | yes      | セッション所有者のユーザID          |
| creation_time     | integer   | yes      | UNIX タイムスタンプ（秒）           |
| last_access_time  | integer   | yes      | 最終アクセス時のUNIXタイムスタンプ  |
| attributes        | object    | no       | その他任意属性（JSON Schema参照）   |

## JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Session",
  "type": "object",
  "properties": {
    "user_id": {
      "type": "string",
      "description": "セッション所有者のユーザID"
    },
    "creation_time": {
      "type": "integer",
      "description": "UNIXタイムスタンプ（秒）"
    },
    "last_access_time": {
      "type": "integer",
      "description": "最終アクセスのUNIXタイムスタンプ"
    },
    "attributes": {
      "type": "object",
      "description": "任意属性",
      "properties": {
        "ip_address": {
          "type": "string",
          "format": "ipv4"
        },
        "user_agent": {
          "type": "string"
        },
        "cart_id": {
          "type": "string"
        }
      },
      "required": ["ip_address", "user_agent"]
    }
  },
  "required": ["user_id", "creation_time", "last_access_time"]
}
