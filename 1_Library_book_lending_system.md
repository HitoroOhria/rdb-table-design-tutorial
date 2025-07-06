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
        int id PK
        string name "ユーザー名"
    }

    books {
        int id PK
        string title "タイトル"
        int stock "在庫, 0になることがある"
    }
    
    lendings {
        int id PK
        int user_id FK
        int book_id FK
        datetime return_deadline "貸出期限"
        datetime lended_at "貸出日時"
        datetime returned_at "返却日時, null"
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
        int id PK
        string name "ユーザー名"
    }

    books {
        int id PK
        string title "タイトル"
        int stock "在庫, 0になることがある"
    }
    
    lendings {
        int id PK
        int user_id FK
        int book_id FK
        datetime return_deadline "貸出期限"
        datetime lended_at "貸出日時"
    }
    
    returns {
        int id PK
        int user_id FK
        int book_id FK
        int lending_id FK
        datetime returned_at "返却日時, null"
    }
```