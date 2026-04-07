---
aliases:
  - /{tenant}/maker.php (VI)
tags:
  - ppes
  - vi
  - admin
  - maker
---

# Master maker (VI)

## Thuật ngữ chính

- `メーカー (hãng / maker)`
- `仕入先 (nhà cung cấp)`

## Tóm tắt

`/{tenant}/maker.php` quản lý `a_maker`.

- được dùng lại trong `equip.php`
- được dùng lại trong `stock.php`
- hỗ trợ import/export/order

## Bảng chính

- `a_maker`
- `a_area`
- `a_factory`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    a_maker {
        smallint bkid PK
        varchar mk_id PK
        varchar mk_name
    }
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
    a_equips {
        smallint bkid PK
        int eq_id PK
        varchar mat_mk_id "app-FK to a_maker"
        tinyint del_flg
    }
    a_stocks {
        smallint bkid PK
        varchar hin_id PK
        varchar fc_id PK "app-FK to a_factory"
        double stk_num
    }
    a_area ||--o{ a_factory : "area_id"
    a_maker }o--o{ a_equips : "mat_mk_id"
    a_maker }o--o{ a_stocks : "mk_id (app usage)"
```

## Mermaid

```mermaid
flowchart TD
    Maker["a_maker"] --> Equip["equip.php"]
    Maker --> Stock["stock.php"]
```

## Xem thêm

- [[Maker master]]
