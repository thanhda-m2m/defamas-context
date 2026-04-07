---
aliases:
  - /{tenant}/equip.php (VI)
tags:
  - ppes
  - vi
  - equip
---

# Trang thiết bị (VI)

## Thuật ngữ chính

- `設備機器 (thiết bị / máy móc)`
- `設備グループ (nhóm thiết bị)`
- `設備項目 (hạng mục thiết bị)`

## Tóm tắt

`/{tenant}/equip.php` là controller trung tâm của domain thiết bị.

- sở hữu `a_equips`, `a_equips_detail`, `a_eqhist`
- list/search/export thiết bị
- edit/add/copy/look thiết bị
- có thể import file tồn kho liên kết sang `a_stocks` và `a_eqstocks`
- delete bị chặn nếu đang bị dùng bởi bảo trì, tồn kho, hoặc mượn trả

## Bảng chính

- đọc: `a_equips`, `a_eqgroup`, `a_factory`, `a_line`, `a_floor`, `a_eqitem`, `a_eqgroup_detail`, `a_maker`, `datamaster`
- ghi: `a_equips`, `a_equips_detail`, `a_eqhist`
- phụ thuộc khi delete: `a_mtinfo`, `a_mtsch`, `a_eqstocks`, `a_rent`

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
    a_floor {
        smallint bkid PK
        varchar fc_id PK "app-FK to a_factory"
        varchar line_id PK "app-FK to a_line"
        varchar flr_id PK
        varchar flr_name PK
    }
    a_equips {
        smallint bkid PK
        int eq_id PK
        smallint eqg_id "app-FK to a_eqgroup"
        varchar fc_id "app-FK to a_factory"
        varchar line_id "app-FK to a_line"
        varchar flr_id "app-FK to a_floor"
        varchar mat_mk_id "app-FK to a_maker"
        tinyint del_flg
    }
    a_equips_detail {
        smallint bkid PK
        int eq_id PK "app-FK to a_equips"
        int eqitem_id PK "app-FK to a_eqitem"
        varchar eqd_val
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
    a_eqhist {
        smallint bkid PK
        int eq_id PK "app-FK to a_equips"
        int eq_time PK
    }
    a_mtinfo {
        smallint bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        smallint mtinfo_kbn
    }
    a_eqstocks {
        smallint bkid PK
        varchar hin_id PK
        int eq_id PK "app-FK to a_equips"
        varchar fc_id PK "app-FK to a_factory"
    }
    a_rent {
        smallint bkid PK
        int eq_id PK "app-FK to a_equips"
        datetime start_date PK
    }
    a_maker {
        smallint bkid PK
        varchar mk_id PK
        varchar mk_name
    }
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_floor ||--o{ a_equips : "flr_id"
    a_equips ||--o{ a_equips_detail : "eq_id"
    a_eqitem ||--o{ a_equips_detail : "eqitem_id"
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    a_equips ||--o{ a_eqhist : "eq_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_equips ||--o{ a_eqstocks : "eq_id"
    a_equips ||--o{ a_rent : "eq_id"
    a_maker }o--o{ a_equips : "mat_mk_id"
```

Ghi chú suy luận:

- một số link như `a_maker -> a_equips` là quan hệ ở mức application/UI, không nhất thiết là foreign key cứng.

## Side effects

- upload/copy/delete file vật lý của thiết bị
- import spreadsheet tồn kho
- download Excel/CSV
- utility sửa `flr_id`

## Mermaid

```mermaid
flowchart TD
    Equip["equip.php"] --> Header["a_equips"]
    Header --> Detail["a_equips_detail"]
    Header --> Hist["a_eqhist"]
    Header --> Mt["a_mtinfo"]
    Header --> EqStock["a_eqstocks"]
```

## Xem thêm

- [[Equipment page]]
- [[Thêm thiết bị (VI)]]
- [[Quản lý tồn kho (VI)]]
