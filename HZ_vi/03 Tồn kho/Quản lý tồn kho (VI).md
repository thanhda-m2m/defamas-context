---
aliases:
  - /{tenant}/stock.php (VI)
tags:
  - ppes
  - vi
  - stock
---

# Quản lý tồn kho (VI)

## Thuật ngữ chính

- `在庫一覧 (danh sách tồn kho)`
- `在庫区分 (loại tồn kho)`
- `品名 (tên vật tư)`

## Tóm tắt

`/{tenant}/stock.php` quản lý `a_stocks` và liên kết thiết bị qua `a_eqstocks`.

- list/search/export tồn kho
- edit tồn kho và chọn thiết bị liên quan
- API trả equipment list theo factory
- delete xóa row tồn kho và link thiết bị

## Bảng chính

- đọc: `a_stocks`, `a_eqstocks`, `a_equips`, `a_factory`, `a_area`, `a_line`, `a_eqgroup`, `a_maker`, `a_eqitem`, `a_eqgroup_detail`
- ghi: `a_stocks`, `a_eqstocks`

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
    a_stocks {
        smallint bkid PK
        varchar hin_id PK
        varchar fc_id PK "app-FK to a_factory"
        double stk_num
        double stk_num_warn
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
        varchar mat_mk_id "app-FK to a_maker"
        tinyint del_flg
    }
    a_eqstocks {
        smallint bkid PK
        varchar hin_id PK "app-FK to a_stocks"
        int eq_id PK "app-FK to a_equips"
        varchar fc_id PK "app-FK to a_factory"
    }
    a_maker {
        smallint bkid PK
        varchar mk_id PK
        varchar mk_name
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
    a_area ||--o{ a_factory : "area_id"
    a_factory ||--o{ a_line : "fc_id"
    a_factory ||--o{ a_stocks : "fc_id"
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_equips ||--o{ a_eqstocks : "eq_id"
    a_stocks ||--o{ a_eqstocks : "hin_id"
    a_maker }o--o{ a_equips : "mat_mk_id"
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
```

## Side effects

- JSON API
- CSV/XLSX export
- warning khi `stk_num < stk_num_warn`

## Mermaid

```mermaid
flowchart TD
    Stock["a_stocks"] --> EqStock["a_eqstocks"]
    EqStock --> Equip["a_equips"]
```

## Xem thêm

- [[Stock management]]
- [[Trang thiết bị]]
