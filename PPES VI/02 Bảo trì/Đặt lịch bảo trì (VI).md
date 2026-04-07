---
aliases:
  - /{tenant}/mtinfo_yoyaku.php (VI)
tags:
  - ppes
  - vi
  - maintenance
  - reservation
---

# Đặt lịch bảo trì (VI)

## Thuật ngữ chính

- `保全予約 (đặt lịch bảo trì)`
- `定期 (định kỳ)`
- `突発 (đột xuất)`

## Tóm tắt

`/{tenant}/mtinfo_yoyaku.php` là nhánh reservation-oriented của maintenance.

- tạo/sửa `a_mtinfo`
- với `定期` sẽ generate nhiều row `a_mtsch`
- với non-periodic có thể ghi cả `a_mtsch` và `a_mtres`
- có thể gửi mail nhắc việc

## Bảng chính

- đọc: `a_mtinfo`, `a_equips`, `a_eqgroup`, `a_factory`, `a_line`, `a_mtsch`, `a_mtres`, `a_eqitem`, `a_eqgroup_detail`, `a_mailtmpl`
- ghi: `a_mtinfo`, `a_mtsch`, `a_mtres`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    a_eqgroup {
        smallint bkid PK
        smallint eqg_id PK
        varchar eqg_name
    }
    a_factory {
        smallint bkid PK
        varchar fc_id PK
        smallint area_id "app-FK to a_area"
        varchar fc_name
    }
    a_line {
        smallint bkid PK
        varchar line_id PK
        varchar fc_id PK "app-FK to a_factory"
        varchar line_name
    }
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
    a_mailtmpl {
        smallint bkid PK
        smallint mtid PK
        varchar subject
        text body
    }
    a_eqgroup_detail {
        smallint bkid PK
        smallint eqg_id PK "app-FK to a_eqgroup"
        smallint eqitem_id PK "app-FK to a_eqitem"
        tinyint required_flg
        tinyint record_flg
    }
    a_eqitem {
        smallint bkid PK
        varchar eqitem_name PK
        smallint eqitem_id
        varchar eqitem_type
    }
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_mtinfo ||--o{ a_mtsch : "mt_id"
    a_mtsch ||--o| a_mtres : "mts_uid"
    a_mailtmpl }o--o{ a_mtinfo : "mtid (app usage)"
    a_eqgroup_detail }o--o{ a_eqitem : "eqitem_id"
```

## Side effects

- generate lịch bảo trì tương lai
- gửi mail template `mtid=9`
- import periodic plan từ file

## Mermaid

```mermaid
flowchart TD
    Mtinfo["a_mtinfo"] --> Mtsch["a_mtsch"]
    Mtsch --> Mtres["a_mtres"]
```

## Xem thêm

- [[Maintenance reservation page]]
- [[Công việc bảo trì (VI)]]
- [[Lịch bảo trì (VI)]]
