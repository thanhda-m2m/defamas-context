---
aliases:
  - /{tenant}/mtres_list.php (VI)
tags:
  - ppes
  - vi
  - maintenance
  - results
---

# Danh sách kết quả bảo trì (VI)

## Thuật ngữ chính

- `実績一覧 (danh sách kết quả)`
- `保全履歴 (lịch sử bảo trì)`

## Tóm tắt

`/{tenant}/mtres_list.php` là trang reporting trên các maintenance row đã hoàn tất.

- chủ yếu read-only
- join `a_mtinfo + a_equips + a_mtsch + a_mtres`
- export CSV
- link sang equip và mtres detail

## Bảng chính

- `a_mtinfo`
- `a_equips`
- `a_mtsch`
- `a_mtres`
- `a_factory`
- `a_area`
- `a_line`
- `a_eqgroup`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    a_area {
        smallint bkid PK
        smallint area_id PK
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
    a_eqgroup {
        smallint bkid PK
        smallint eqg_id PK
        varchar eqg_name
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
    a_area ||--o{ a_factory : "area_id"
    a_factory ||--o{ a_line : "fc_id"
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_mtinfo ||--o{ a_mtsch : "mt_id"
    a_mtsch ||--o| a_mtres : "mts_uid"
```

## Điểm cần chú ý

- có một nhánh delete bất thường đụng vào `a_equips`

## Mermaid

```mermaid
flowchart TD
    Query["joined result set"] --> Equip["equip.php"]
    Query --> Mtres["mtres.php"]
    Query --> Csv["CSV export"]
```

## Xem thêm

- [[Maintenance results list]]
- [[Công việc bảo trì (VI)]]
