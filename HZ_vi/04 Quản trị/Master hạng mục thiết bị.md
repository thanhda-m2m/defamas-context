---
aliases:
  - /{tenant}/eqitem.php (VI)
tags:
  - ppes
  - vi
  - admin
  - eqitem
---

# Master hạng mục thiết bị

## Thuật ngữ chính

- `設備項目 (hạng mục thiết bị)`
- `required_flg (cờ bắt buộc)`
- `record_flg (cờ ghi lịch sử)`

## Tóm tắt

`/{tenant}/eqitem.php` quản lý definition của từng field thiết bị trong `a_eqitem`.

- có API để bật/tắt `required_flg` và `record_flg` trong `a_eqgroup_detail`
- delete item sẽ cleanup dữ liệu cũ trong `a_equips_detail` và `a_equips.details`

## Bảng chính

- `a_eqitem`
- `a_eqgroup_detail`
- `a_equips_detail`
- `a_equips`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    a_eqitem {
        smallint bkid PK
        varchar eqitem_name PK
        smallint eqitem_id
        varchar eqitem_type
    }
    a_eqgroup {
        smallint bkid PK
        smallint eqg_id PK
        varchar eqg_name
    }
    a_eqgroup_detail {
        smallint bkid PK
        smallint eqg_id PK "app-FK to a_eqgroup"
        smallint eqitem_id PK "app-FK to a_eqitem"
        tinyint required_flg
        tinyint record_flg
    }
    a_equips {
        smallint bkid PK
        int eq_id PK
        smallint eqg_id "app-FK to a_eqgroup"
        tinyint del_flg
    }
    a_equips_detail {
        smallint bkid PK
        int eq_id PK "app-FK to a_equips"
        int eqitem_id PK "app-FK to a_eqitem"
        varchar eqd_val
    }
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_equips_detail : "eqitem_id"
    a_equips ||--o{ a_equips_detail : "eq_id"
```

## Mermaid

```mermaid
flowchart TD
    Item["a_eqitem"] --> Group["a_eqgroup_detail"]
    Group --> EquipVals["a_equips_detail"]
```

## Xem thêm

- [[Equipment item master]]
- [[Master nhóm thiết bị]]
