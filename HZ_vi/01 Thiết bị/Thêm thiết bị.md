---
aliases:
  - /{tenant}/equip.php?edit=1&add=1 (VI)
tags:
  - ppes
  - vi
  - equip
  - add
---

# Thêm thiết bị

## Thuật ngữ chính

- `追加登録 (đăng ký thêm mới)`
- `設備名称 (tên thiết bị)`
- `機番 (mã máy)`

## Tóm tắt

Đây là nhánh add form của `equip.php`.

- load master data và field động theo `a_eqgroup_detail + a_eqitem`
- khi save sẽ ghi `a_equips + a_equips_detail + a_eqhist`
- nếu có file tồn kho thì còn ghi `a_stocks + a_eqstocks`

## Bảng chính

- load form: `a_eqgroup`, `a_factory`, `a_area`, `a_line`, `a_floor`, `a_maker`, `a_eqgroup_detail`, `a_eqitem`, `datamaster`
- save: `a_equips`, `a_equips_detail`, `a_eqhist`
- optional stock import: `a_stocks`, `a_eqstocks`

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
    a_floor {
        smallint bkid PK
        varchar fc_id PK "app-FK to a_factory"
        varchar line_id PK "app-FK to a_line"
        varchar flr_id PK
        varchar flr_name PK
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
    a_eqitem {
        smallint bkid PK
        varchar eqitem_name PK
        smallint eqitem_id
        varchar eqitem_type
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
    a_eqhist {
        smallint bkid PK
        int eq_id PK "app-FK to a_equips"
        int eq_time PK
    }
    a_stocks {
        smallint bkid PK
        varchar hin_id PK
        varchar fc_id PK "app-FK to a_factory"
        double stk_num
        double stk_num_warn
    }
    a_eqstocks {
        smallint bkid PK
        varchar hin_id PK "app-FK to a_stocks"
        int eq_id PK "app-FK to a_equips"
        varchar fc_id PK "app-FK to a_factory"
    }
    a_area ||--o{ a_factory : "area_id"
    a_factory ||--o{ a_line : "fc_id"
    a_line ||--o{ a_floor : "line_id"
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_equips ||--o{ a_equips_detail : "eq_id"
    a_eqitem ||--o{ a_equips_detail : "eqitem_id"
    a_equips ||--o{ a_eqhist : "eq_id"
    a_stocks ||--o{ a_eqstocks : "hin_id"
    a_equips ||--o{ a_eqstocks : "eq_id"
```

## Mermaid

```mermaid
flowchart TD
    Add["edit=1&add=1"] --> Form["load dynamic fields"]
    Form --> Save["save a_equips"]
    Save --> Detail["save a_equips_detail"]
    Detail --> Hist["insert a_eqhist"]
    Detail --> Stock{"file tồn kho?"}
    Stock -->|yes| Import["a_stocks + a_eqstocks"]
```

## Xem thêm

- [[Edit-add equipment]]
- [[Trang thiết bị]]
