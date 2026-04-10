---
aliases:
  - /{tenant}/eqgroup.php
tags:
  - ppes
  - vi
  - admin
  - eqgroup
---

# Master nhóm thiết bị

## Thuật ngữ chính

- `設備グループ (nhóm thiết bị)`
- `必須 (bắt buộc)`
- `履歴記録 (ghi lịch sử)`

## Tóm tắt

`/{tenant}/eqgroup.php` không chỉ quản lý `a_eqgroup`.

- upload còn sync `a_eqitem`
- và mapping `a_eqgroup_detail`
- vì vậy trang này quyết định field nào sẽ xuất hiện trên form thiết bị

## Bảng chính

- `a_eqgroup`
- `a_eqgroup_detail`
- `a_eqitem`
- `bk_idmaster`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
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
    a_eqitem {
        smallint bkid PK
        varchar eqitem_name PK
        smallint eqitem_id
        varchar eqitem_type
    }
    bk_idmaster {
        int bkid PK
        varchar p_name PK
        int p_val
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
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    bk_idmaster ||--o{ a_eqitem : "p_val (id alloc)"
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_eqitem ||--o{ a_equips_detail : "eqitem_id"
```

## Mermaid

```mermaid
flowchart TD
    Group["a_eqgroup"] --> Detail["a_eqgroup_detail"]
    Item["a_eqitem"] --> Detail
    Detail --> Equip["equip.php dynamic fields"]
```

## Xem thêm

- [[Equipment group master]]
- [[Master hạng mục thiết bị]]
