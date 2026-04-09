---
aliases:
  - /{tenant}/eqgroup.php
tags:
  - en
  - ppes
  - admin
  - eqgroup
  - obsidian
---

# Equipment group master

Related notes:

- [[管理 index]]
- [[Equipment item master]]
- [[Equipment page]]

## Summary

`/{tenant}/eqgroup.php` is more than a group list page.

- it maintains `a_eqgroup`
- its upload flow also maintains `a_eqitem`
- and it owns the group-to-item mapping in `a_eqgroup_detail`

This page effectively shapes which fields appear on the equipment form.

## Mermaid: dynamic equipment model

```mermaid
flowchart TD
    Group["a_eqgroup"] --> GroupDetail["a_eqgroup_detail"]
    Item["a_eqitem"] --> GroupDetail
    GroupDetail --> EquipForm["equip.php dynamic form"]
    EquipForm --> EquipDetail["a_equips_detail"]
```

## Tables involved

- `a_eqgroup`
- `a_eqgroup_detail`
- `a_eqitem`
- `bk_idmaster`
- `a_area`
- `a_factory`
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
    a_eqitem {
        int bkid PK
        varchar eqitem_name PK
        smallint eqitem_id
        varchar eqitem_type
        varchar select_item
    }
    bk_idmaster {
        int bkid PK
        varchar p_name PK
        int p_val
    }
    a_equips {
        int bkid PK
        int eq_id PK
        int eqg_id "app-FK to a_eqgroup"
        int fc_id "app-FK to a_factory"
        int line_id "app-FK to a_line"
        int del_flg
    }
    a_area {
        int bkid PK
        int area_id PK
    }
    a_factory {
        int bkid PK
        int fc_id PK
        int area_id "app-FK to a_area"
        varchar fc_name
    }
    datamaster {
        int propid PK
        int itid PK
        varchar itname
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "level"
    buscomps ||--o{ a_eqgroup : "bkid"
    buscomps ||--o{ a_eqitem : "bkid"
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    bk_idmaster ||--o{ a_eqitem : "eqitem_id_alloc"
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_area ||--o{ a_factory : "area_id"
    datamaster ||--o{ a_eqitem : "ui_labels"
```

Reasoning:

- `a_eqgroup_detail` is the application-level junction between groups and items.
- `a_eqgroup` shapes the dynamic equipment form because `equip.php` resolves fields through `a_eqgroup_detail`.

## Side effects

- Excel import/export
- item/group mapping regeneration during upload
- upload can advance `bk_idmaster` for `a_eqitem`

## Important behavior notes

- `eqitem.php` edits item definitions, but group membership is effectively owned here through upload logic
- changes here directly affect `equip.php` field composition and required/history flags

## Code references

- `htdocs/base/eqgroup.php:28-44`
- `htdocs/base/eqgroup.php:46-86`
- `htdocs/base/eqgroup.php:95-339`
- `htdocs/base/eqgroup.php:352-655`
