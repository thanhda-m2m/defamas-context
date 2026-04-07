---
aliases:
  - /{tenant}/factory.php
tags:
  - en
  - ppes
  - admin
  - factory
  - obsidian
---

# Factory and location master

Related notes:

- [[ç®¡ç† index]]
- [[Equipment page]]
- [[Stock management]]

## Summary

`/{tenant}/factory.php` is the source-of-truth controller for the location hierarchy.

- `a_area`
- `a_factory`
- `a_line`
- `a_floor`

It supports list/edit, guarded delete, and bulk upload/export.

## Mermaid: location hierarchy

```mermaid
flowchart TD
    Area["a_area"] --> Factory["a_factory"]
    Factory --> Line["a_line"]
    Line --> Floor["a_floor"]
    Factory --> Equip["a_equips"]
    Line --> Equip
    Floor --> Equip
    Factory --> Stock["a_stocks"]
```

## Tables involved

- `a_area`
- `a_factory`
- `a_line`
- `a_floor`
- `a_equips`
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
        int area_id "app-FK to a_area"
        int fc_id "app-FK to a_factory"
    }
    a_auths {
        int bkid PK
        int at_id PK
        varchar at_name
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
    a_line {
        int bkid PK
        int line_id PK
        int fc_id PK
        varchar line_name
    }
    a_floor {
        int bkid PK
        int fc_id PK
        int line_id PK
        int flr_id PK
        varchar flr_name PK
    }
    a_equips {
        int bkid PK
        int eq_id PK
        int eqg_id "app-FK to a_eqgroup"
        int fc_id "app-FK to a_factory"
        int line_id "app-FK to a_line"
        int flr_id "app-FK to a_floor"
        int mat_mk_id "app-FK to a_maker"
        int del_flg
    }
    datamaster {
        int propid PK
        int itid PK
        varchar itname
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "level"
    buscomps ||--o{ a_area : "bkid"
    a_area ||--o{ a_factory : "area_id"
    a_factory ||--o{ a_line : "fc_id"
    a_line ||--o{ a_floor : "line_id"
    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_floor ||--o{ a_equips : "flr_id"
    datamaster ||--o{ a_factory : "ui_labels"
```

Reasoning:

- `a_area -> a_factory -> a_line -> a_floor` is the location hierarchy enforced mostly by application logic.
- `a_equips` consumes these masters as placement dimensions even where formal constraints are loose.

## Side effects

- guarded JSON delete endpoints for factory, line, and floor rows
- Excel import/export
- transaction-based bulk upload
- delete is blocked when location rows are referenced by equipment or child rows

## Important behavior notes

- downstream pages like `equip.php` and `stock.php` rely on these masters for selectors and display names
- `Factorys()` only returns factories joined to `a_line`, so incomplete location setup can hide factories from consumers

## Code references

- `htdocs/base/factory.php:28-49`
- `htdocs/base/factory.php:51-177`
- `htdocs/base/factory.php:186-346`
- `htdocs/base/factory.php:429-754`
