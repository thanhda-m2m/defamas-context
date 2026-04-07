---
aliases:
  - /{tenant}/eqitem.php
tags:
  - en
  - ppes
  - admin
  - eqitem
  - obsidian
---

# Equipment item master

Related notes:

- [[ç®¡ç† index]]
- [[Equipment group master]]
- [[Equipment page]]

## Summary

`/{tenant}/eqitem.php` manages individual equipment item definitions in `a_eqitem`.

- it lists and edits field definitions
- it can toggle `required_flg` and `record_flg` in `a_eqgroup_detail` via API calls
- deleting an item also cleans dependent equipment detail values

## Mermaid: item usage flow

```mermaid
flowchart TD
    Item["a_eqitem"] --> GroupDetail["a_eqgroup_detail"]
    GroupDetail --> EquipForm["equip.php form fields"]
    EquipForm --> EquipVals["a_equips_detail + a_equips.details"]
    Delete["eqitem delete"] --> Cleanup["remove old equipment detail values"]
```

## Tables involved

- `a_eqitem`
- `a_eqgroup_detail`
- `a_equips_detail`
- `a_equips`
- `a_eqgroup`
- `a_line`
- `a_maker`
- `datamaster`
- `buscomps`
- `bk_staff`
- `bkmasters`
- `a_auths`

## Mermaid: inferred entity relationships

> **Note**: The database has zero explicit FK constraints. All relationships below are enforced at the application level.

```mermaid
erDiagram
    buscomps {
        int bkid PK
        varchar bcname
        varchar email
    }
    bkmasters {
        int bkid PK
        int term
        varchar compname
    }
    bk_staff {
        int bkid PK
        int stf_id PK
        varchar login
        int level "app-FK to a_auths"
        int bs_delete
    }
    a_auths {
        int bkid PK
        int at_id PK
        varchar at_name
    }
    a_eqitem {
        int bkid PK
        varchar eqitem_name PK
        smallint eqitem_id
        varchar eqitem_type
        varchar select_item
    }
    a_eqgroup {
        int bkid PK
        int eqg_id PK
        varchar eqg_name
    }
    a_eqgroup_detail {
        int bkid PK
        int eqg_id PK "app-FK to a_eqgroup"
        int eqitem_id PK "app-FK to a_eqitem"
        int required_flg
        int record_flg
    }
    a_equips {
        int bkid PK
        int eq_id PK
        int eqg_id "app-FK to a_eqgroup"
        int fc_id "app-FK to a_factory"
        int line_id "app-FK to a_line"
        int del_flg
    }
    a_equips_detail {
        int bkid PK
        int eq_id PK "app-FK to a_equips"
        int eqitem_id PK "app-FK to a_eqitem"
    }
    a_maker {
        int bkid PK
        int mk_id PK
        varchar mk_name
    }
    a_line {
        int bkid PK
        int line_id PK
        int fc_id PK
        varchar line_name
    }
    datamaster {
        int propid PK
        int itid PK
        varchar itname
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "level"
    buscomps ||--o{ a_eqitem : "bkid"
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    a_eqitem ||--o{ a_equips_detail : "eqitem_id"
    a_equips ||--o{ a_equips_detail : "eq_id"
    a_maker }o--o{ a_eqitem : "select_item"
    a_line ||--o{ a_equips : "line_id"
    datamaster ||--o{ a_eqitem : "ui_labels"
```

Reasoning:

- `a_eqitem` defines fields, `a_eqgroup_detail` decides where they appear, and `a_equips_detail` stores the actual values.
- `a_maker` is not a direct child table, but some item types use maker master values at the application layer.

## Side effects

- API updates for `required_flg` / `record_flg`
- deleting an item cleans equipment detail JSON and detail rows
- no file-system side effects in this controller

## Important behavior notes

- item membership in groups is not primarily managed here; that is mostly driven by [[Equipment group master]]
- equipment forms downstream are built from `a_eqitem + a_eqgroup_detail`

## Code references

- `htdocs/base/eqitem.php:27-47`
- `htdocs/base/eqitem.php:49-95`
- `htdocs/base/eqitem.php:104-176`
- `htdocs/base/eqitem.php:224-365`
