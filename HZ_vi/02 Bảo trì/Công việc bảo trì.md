---
aliases:
  - /{tenant}/mtinfo.php
tags:
  - ppes
  - vi
  - maintenance
---

# Công việc bảo trì

## Thuật ngữ chính

- `保全情報 (thông tin bảo trì)`
- `実績 (kết quả thực hiện)`
- `履歴 (lịch sử)`

## Tóm tắt

`/{tenant}/mtinfo.php` là controller bảo trì tổng quát.

- quản lý header `a_mtinfo`
- sync lịch `a_mtsch`
- sync kết quả `a_mtres` cho one-off work
- là nguồn dữ liệu chính cho schedule và result list

## Bảng chính

- đọc: `a_mtinfo`, `a_equips`, `a_mtsch`, `a_mtres`, `a_eqitem`, `a_eqgroup_detail`, `a_mailtmpl`
- ghi: `a_mtinfo`, `a_mtsch`, `a_mtres`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    a_equips {
        smallint bkid PK
        int eq_id PK
        smallint eqg_id "app-FK to a_eqgroup"
        varchar fc_id "app-FK to a_factory"
        varchar line_id "app-FK to a_line"
        tinyint del_flg
    }
    a_mtinfo {
        smallint bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        smallint mtinfo_kbn
    }
    a_mtsch {
        smallint bkid PK
        int mt_id PK "app-FK to a_mtinfo"
        date sdate PK
        bigint mts_uid
    }
    a_mtres {
        smallint bkid PK
        int mts_uid PK "app-FK to a_mtsch"
    }
    a_eqitem {
        smallint bkid PK
        varchar eqitem_name PK
        smallint eqitem_id
        varchar eqitem_type
    }
    a_eqgroup_detail {
        smallint bkid PK
        smallint eqg_id PK "app-FK to a_eqgroup"
        smallint eqitem_id PK "app-FK to a_eqitem"
        tinyint required_flg
        tinyint record_flg
    }
    a_mailtmpl {
        smallint bkid PK
        smallint mtid PK
        varchar subject
        text body
    }
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_mtinfo ||--o{ a_mtsch : "mt_id"
    a_mtsch ||--o| a_mtres : "mts_uid"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    a_mailtmpl }o--o{ a_mtinfo : "mtid (app usage)"
    a_mailtmpl }o--o{ a_mtres : "mtid (app usage)"
```

## Side effects

- prune/regenerate lịch định kỳ
- copy file từ mtinfo sang mtres trong một số nhánh
- gửi reminder mail
- CSV export/import

## Mermaid

```mermaid
flowchart TD
    Equip["a_equips"] --> Mtinfo["a_mtinfo"]
    Mtinfo --> Mtsch["a_mtsch"]
    Mtsch --> Mtres["a_mtres"]
```

## Xem thêm

- [[Maintenance work page]]
- [[Danh sách kết quả bảo trì]]
- [[Lịch bảo trì]]
