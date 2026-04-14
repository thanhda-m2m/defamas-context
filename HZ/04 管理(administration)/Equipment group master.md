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

## Import / Export

> For full architecture details, see [[Import-Export architecture]].

| Direction | Format | Template file | Output filename |
|-----------|--------|--------------|-----------------|
| **Export** | Excel (.xlsx) | `M_EQGROUP.xlsx` | `eqgroup-{ymdhi}.xlsx` |
| **Import** | Excel → CSV | *(no template — accepts any .xlsx/.csv)* | temp: `{bkid}-eqgroup.csv` |

- Export loads `htdocs/base/M_EQGROUP.xlsx` (or `htdocs/sys/M_EQGROUP.xlsx` for the sys module) as a template, populates it with data, and streams it to the browser.
- Import accepts an Excel file, converts it to CSV via `Excel::convToCsv()`, then parses rows with `fgetcsv()` and upserts into `a_eqgroup`, `a_eqitem`, and `a_eqgroup_detail`.
- Upload can advance `bk_idmaster` serial for new `a_eqitem` records.

## Side effects

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

---

## Appendix: table glossary

> Full schema (columns, types, per-column purpose) for every table listed here is in [[Table Glossary]].

### Equipment group model (owned by this page)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_eqgroup\|a_eqgroup]]** | Equipment group master. One row per group per tenant. Defines a named grouping of equipment (e.g. "Injection Molding", "CNC Mill"). Keyed by `eqg_id`. List, edit, import/export all target this table. |
| **[[Table Glossary#a_eqgroup_detail\|a_eqgroup_detail]]** | Group-to-item mapping. Junction table linking `eqg_id` to `eqitem_id`. Controls which dynamic fields appear on the equipment form, plus per-field `required_flg` and `record_flg` (history tracking). Rebuilt during import. |
| **[[Table Glossary#a_eqitem\|a_eqitem]]** | Equipment item definition. One row per field definition. Stores field name, type (`eqitem_type`), and dropdown options (`select_item`). Import on this page can create new `a_eqitem` rows and advance the serial in `bk_idmaster`. |

### ID allocation

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#bk_idmaster\|bk_idmaster]]** | Application-level serial allocator. Stores the next available `eqitem_id` value per tenant. Incremented when import creates new equipment item definitions. |

### Location / org hierarchy

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_area\|a_area]]** | Area master. Used in the page UI for area filter dropdown. |
| **[[Table Glossary#a_factory\|a_factory]]** | Factory master. Used in the page UI for factory filter dropdown. |

### UI label support

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#datamaster\|datamaster]]** | Generic dropdown value master. Stores named item lists keyed by `propid` with labels in four languages. Used for column header and label resolution. |

### Auth / session context

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#buscomps\|buscomps]]** | Tenant registry (`zaikodb`). Read during login to identify the tenant company and check feature flags. |
| **[[Table Glossary#bk_staff\|bk_staff]]** | Tenant staff accounts. Checked by `openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. |
| **[[Table Glossary#bkmasters\|bkmasters]]** | Tenant configuration. One row per `bkid`; stores company name, working-hour settings, display preferences, and other tenant-level config used across all pages. |
| **[[Table Glossary#a_auths\|a_auths]]** | Feature permission groups. `setAuth($db, $request, $authId)` reads this to determine what the current user can view or edit on the page. |
