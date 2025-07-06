# 1. 図書館の蔵書貸出システム

## 要件

- 利用者（会員）が本を借りたり返却したりする
- 本は複数のコピー（冊）がある
- 貸出履歴を記録し、延滞日数も計算できるようにしたい

## 設計ポイント

- 会員、書籍、蔵書（コピー）、貸出履歴のテーブル構成
- 主キー・外部キーの設定
- 延滞計算のために貸出日・返却日をどう扱うか

## 設計

シンプルに実装。

```mermaid
erDiagram
    users }|--o{ lendings : "N:N"
    lendings }o--|{ books : "N:N"
    
    users {
        INTEGER id PK
        string name "ユーザー名"
    }

    books {
        INTEGER id PK
        VARCHAR(3000) title UK "タイトル"
        INTEGER stock "在庫, 0になることがある"
    }
    
    lendings {
        INTEGER id PK
        INTEGER user_id FK
        INTEGER book_id FK
        DATETIME return_deadline "貸出期限"
        DATETIME lended_at "貸出日時"
        DATETIME returned_at "返却日時, null"
    }
```

イベントソーシングを取り入れ、イベントごとに独立して記録する案。

```mermaid
erDiagram
    users }|--o{ lendings : "N:N"
    users }|--o{ returns : "N:N"
    lendings }o--|{ books : "N:N"
    returns }o--|{ books : "N:N"
    
    users {
        INTEGER id PK
        string name "ユーザー名"
    }

    books {
        INTEGER id PK
        VARCHAR(3000) title UK "タイトル"
        INTEGER stock "在庫, 0になることがある"
    }
    
    lendings {
        INTEGER id PK
        INTEGER user_id FK
        INTEGER book_id FK
        DATETIME return_deadline "貸出期限"
        DATETIME lended_at "貸出日時"
    }
    
    returns {
        INTEGER id PK
        INTEGER user_id FK
        INTEGER book_id FK
        INTEGER lending_id FK
        DATETIME returned_at "返却日時, null"
    }
```