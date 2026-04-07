---
aliases:
  - /{tenant}/mailtmpl.php (VI)
tags:
  - ppes
  - vi
  - admin
  - mail
---

# Master mẫu email (VI)

## Thuật ngữ chính

- `メールテンプレート (mẫu email)`
- `置換タグ (thẻ thay thế)`

## Tóm tắt

`/{tenant}/mailtmpl.php` quản lý `a_mailtmpl`.

- controller này không gửi mail trực tiếp
- nhưng thay đổi ở đây ảnh hưởng tới maintenance flow và rent flow

## Bảng chính

- `a_mailtmpl`
- `datamaster`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    a_mailtmpl {
        smallint bkid PK
        smallint mtid PK
        varchar subject
        text body
    }
    datamaster {
        varchar propid PK
        smallint itid PK
    }
    a_mtinfo {
        smallint bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        smallint mtinfo_kbn
    }
    a_mtres {
        smallint bkid PK
        int mts_uid PK "app-FK to a_mtsch"
    }
    a_rent {
        smallint bkid PK
        int eq_id PK "app-FK to a_equips"
        datetime start_date PK
    }
    a_mailtmpl }o--o{ a_mtinfo : "mtid (app usage)"
    a_mailtmpl }o--o{ a_mtres : "mtid (app usage)"
    a_mailtmpl }o--o{ a_rent : "mtid (app usage)"
    datamaster }o--o{ a_mailtmpl : "propid (app labels)"
```

Ghi chú suy luận:

- các quan hệ với `a_mtinfo`, `a_mtres`, `a_rent` là quan hệ usage ở tầng application, vì flow mail load template theo `mtid`.

## Mermaid

```mermaid
flowchart TD
    Tmpl["a_mailtmpl"] --> Mt["maintenance mails"]
    Tmpl --> Rent["rent mails"]
```

## Xem thêm

- [[Mail template master]]
- [[Đặt lịch bảo trì (VI)]]
- [[Công việc bảo trì (VI)]]
