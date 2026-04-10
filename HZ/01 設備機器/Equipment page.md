---
aliases:
  - /{tenant}/equip.php
  - equipment page context
tags:
  - en
  - ppes
  - equip
  - database
  - obsidian
---

# Equipment page

Related notes:

- [[PPES page map]]
- [[Edit-add equipment]]
- [[Stock management]]
- [[Maintenance reservation page]]
- [[Maintenance work page]]
- [[Schedule calendar]]

## Summary

`/{tenant}/equip.php` is the controller that owns the equipment master.

- List mode searches and exports equipment.
- Edit mode loads the dynamic equipment form based on equipment group.
- Save writes both the equipment header and its per-item detail rows.
- Stock-list file uploads from this page can also populate stock tables.
- Delete is guarded by maintenance, stock, and rental dependencies.

## What this page owns

- Canonical equipment header: `a_equips`
- Canonical equipment detail values: `a_equips_detail`
- Equipment change history: `a_eqhist`
- Attached stock-list import bridge into `a_stocks` and `a_eqstocks`

## Controller flow

| Branch | Trigger | Main reads | Main writes | Side effects |
| --- | --- | --- | --- | --- |
| Progress API | `api=check_dl` | progress file only | none | returns download progress |
| Admin lock | `lock` | `bkmasters` | `bkmasters` | toggles `ck_rule` |
| Admin floor repair | `set_floor` | `a_equips`, `a_floor` | `a_equips` | backfills `flr_id` |
| Bulk import | `upload=1` | `a_factory`, `a_line`, `a_eqgroup`, `a_eqgroup_detail`, `a_eqitem`, `a_equips` | `a_equips`, `a_equips_detail` | imports spreadsheet |
| Edit/add/copy/look | `edit=1` | lookup tables, `a_equips`, `a_equips_detail`, `a_eqhist` | `a_equips`, `a_equips_detail`, `a_eqhist` | file upload, validation, confirm step |
| Delete | `delete=1&eq_id=...` | dependency tables | `a_eqhist`, `a_equips`, `a_equips_detail` | removes physical files |
| List/default | no `edit` | `a_equips`, `a_eqgroup`, `a_factory`, `a_line` and lookups | none | CSV/XLSX download, pagination |

## Mermaid: controller data flow

```mermaid
flowchart TD
    Req["/{tenant}/equip.php request"] --> Auth["openUser + setMultiAuth"]
    Auth --> Mode{"mode"}

    Mode --> List["list / download"]
    Mode --> Edit["edit/add/copy/look"]
    Mode --> Upload["bulk upload"]
    Mode --> Delete["delete"]

    List --> LQ["read a_equips + a_eqgroup + a_factory + a_line"]

    Edit --> Form["load group fields from a_eqgroup_detail + a_eqitem"]
    Form --> Existing["optional load a_equips + a_equips_detail + a_eqhist"]
    Existing --> Save{"send?"}
    Save -->|yes| EqSave["upsert a_equips"]
    EqSave --> DetailSave["upsert a_equips_detail"]
    DetailSave --> HistSave["insert a_eqhist"]
    DetailSave --> StockFile{"stock file field?"}
    StockFile -->|yes| StockImport["upsert a_stocks + a_eqstocks"]

    Upload --> UploadSave["spreadsheet import into a_equips + a_equips_detail"]

    Delete --> Check["check a_mtinfo/a_mtsch/a_eqstocks/a_rent"]
    Check --> DeleteRows["delete a_eqhist + a_equips + a_equips_detail"]
```

## Mermaid: table relationships (PK/FK from live DB)

> **Note**: The database has zero explicit FK constraints. All relationships below are enforced at the application level.

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
    }
    a_line {
        smallint bkid PK
        varchar line_id PK
        varchar fc_id PK "app-FK to a_factory"
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
        smallint eqitem_id "unique logical id"
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
    }
    a_eqstocks {
        smallint bkid PK
        varchar hin_id PK "app-FK to a_stocks"
        int eq_id PK "app-FK to a_equips"
        varchar fc_id PK "app-FK to a_factory"
    }
    a_stocks {
        smallint bkid PK
        varchar hin_id PK
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
    }

    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_floor }o--o{ a_equips : "flr_id"
    a_equips ||--o{ a_equips_detail : "eq_id"
    a_eqitem ||--o{ a_equips_detail : "eqitem_id"
    a_eqgroup ||--o{ a_eqgroup_detail : "eqg_id"
    a_eqitem ||--o{ a_eqgroup_detail : "eqitem_id"
    a_equips ||--o{ a_eqhist : "eq_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_equips ||--o{ a_eqstocks : "eq_id"
    a_stocks ||--o{ a_eqstocks : "hin_id+fc_id"
    a_equips ||--o{ a_rent : "eq_id"
    a_maker }o--o{ a_equips : "mat_mk_id app-level"
```

## Tables involved

## Initial list load

Main list query and filter support:

- `a_equips`
- `a_eqgroup`
- `a_factory`
- `a_line`
- `a_area`
- `a_floor`
- `a_eqgroup_detail`
- `a_eqitem`
- `a_maker`
- `datamaster`

Auth/session context:

- `buscomps`
- `bk_staff`
- `bkmasters`
- `a_auths`

## Edit/add load

Always involved on the form:

- `a_eqgroup`
- `a_factory`
- `a_area`
- `a_line`
- `a_maker`
- `a_eqgroup_detail`
- `a_eqitem`
- `a_floor`
- `datamaster`

Only when editing an existing `eq_id`:

- `a_equips`
- `a_equips_detail`
- `a_eqhist`
- `bk_staff`

## Save path

Written directly:

- `a_equips`
- `a_equips_detail`
- `a_eqhist`

Written when stock-list files are attached:

- `a_stocks`
- `a_eqstocks`

## Delete checks

Delete is blocked if related rows exist in:

- `a_mtinfo`
- `a_mtsch`
- `a_eqstocks`
- `a_rent`

Delete also removes:

- `a_eqhist`
- `a_equips`
- `a_equips_detail`

## Import / Export

> For full architecture details, see [[Import-Export architecture]].

| Direction | Format | Template file | Output filename |
|-----------|--------|--------------|-----------------|
| **Export (Excel)** | Excel (.xlsx) | `equip.xlsx` | `equip-{ymdhi}.xlsx` |
| **Export (CSV)** | CSV | *(no template)* | `equip-{ymdhi}.csv` |
| **Import** | Excel → CSV | *(no template — accepts any .xlsx/.csv)* | temp: `{bkid}-equip.csv` |

- This page supports **dual export**: `download=1` → Excel using `htdocs/base/equip.xlsx` as a template; `download=2` → CSV streamed directly.
- Excel export includes async progress tracking via a `donefile` (polled by `api=check_dl`).
- `dlKeys()` is **dynamic** — columns are built at runtime from `detailKeys()` based on the equipment group configuration, unlike other pages where `dlKeys()` is static.
- Import via `uploadEqFile()` uses a **two-pass** approach: first pass validates all rows, second pass writes to DB.
- `sys/equip.php` variants only support CSV export (no Excel template).

## Side effects

- physical equipment files are created, copied, and deleted under the equipment data directory
- stock-list spreadsheets are converted to CSV and imported
- list mode can stream Excel/CSV downloads
- admin utility can backfill `a_equips.flr_id`
- access control can switch the page into redirect or read-only behavior

## Key helper functions

| Function | Role |
| --- | --- |
| `makeList()` | search, join, pagination, and download logic |
| `makeEdit()` | loads header and detail rows into request state |
| `saveData()` | canonical save for one equipment record |
| `getHist()` | reconstructs field-level history from `a_eqhist` |
| `checkStockFile()` | validates stock-list uploads before save |
| `uploadStkFile()` | imports stock-list files into stock tables |
| `uploadEqFile()` | bulk spreadsheet import for equipment |
| `set_floor()` | admin repair for missing floor IDs |

## Data ownership notes

- `a_equips` stores both real columns and denormalized data such as `eq_vals`, `details`, and `eqv*` helper columns.
- `a_equips_detail` is the flexible per-item storage keyed by `eqitem_id`.
- This page is the main owner of the equipment domain; other pages mostly read from it or depend on it.

## Notable relationships

- [[Edit-add equipment]] is the narrow add-form context for this same controller.
- [[Stock management]] consumes the `a_stocks` and `a_eqstocks` rows that can be created here.
- [[Maintenance reservation page]] and [[Maintenance work page]] use `a_equips.eq_id` as the parent maintenance target.
- [[Schedule calendar]] is a read-only projection over maintenance rows anchored back to equipment.

## Code references

- `htdocs/base/equip.php:38-47`
- `htdocs/base/equip.php:59-92`
- `htdocs/base/equip.php:94-264`
- `htdocs/base/equip.php:266-339`
- `htdocs/base/equip.php:379-648`
- `htdocs/base/equip.php:716-846`
- `htdocs/base/equip.php:962-1203`
- `htdocs/base/equip.php:1266-1751`

---

## Appendix: table glossary

> Full schema (columns, types, per-column purpose) for every table listed here is in [[Table Glossary]].

### Equipment domain (owned by this page)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_equips\|a_equips]]** | Equipment master. The primary table owned by this page. One row per physical equipment per tenant. Stores the name (`eq_name`), factory/line/floor placement (`fc_id`, `line_id`, `flr_id`), equipment group (`eqg_id`), maker (`mat_mk_id`), machine numbers, and serialised custom field values (`eq_vals`). Soft-deleted via `del_flg`. |
| **[[Table Glossary#a_equips_detail\|a_equips_detail]]** | Per-equipment item values. Stores one row per dynamic field per equipment (`eq_id` + `eqitem_id` → `eqd_val`). Written on save alongside the equipment header. |
| **[[Table Glossary#a_eqhist\|a_eqhist]]** | Equipment change history. One row per save event per equipment. Records a snapshot of field values at each modification for audit/history display. |

### Dynamic field definitions

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_eqgroup\|a_eqgroup]]** | Equipment group master. Used for the group filter dropdown on the list page and to determine which dynamic fields appear on the edit form. |
| **[[Table Glossary#a_eqgroup_detail\|a_eqgroup_detail]]** | Group-to-item mapping. Junction table that defines which items belong to each group, with `required_flg` and `record_flg` flags. `dlKeys()` and the edit form read this to build dynamic columns. |
| **[[Table Glossary#a_eqitem\|a_eqitem]]** | Equipment item definition. Stores field name, type, and dropdown options. Consumed to render the dynamic equipment form and to build export columns. |

### Location / org hierarchy

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_area\|a_area]]** | Area master. Joined when loading factories for the filter/dropdown. |
| **[[Table Glossary#a_factory\|a_factory]]** | Factory master. Used for the factory filter dropdown and as a placement dimension for equipment. |
| **[[Table Glossary#a_line\|a_line]]** | Line master. Sub-location within a factory. Used for the line filter dropdown and equipment placement. |
| **[[Table Glossary#a_floor\|a_floor]]** | Floor / room master. Finest-grained location level. Used for equipment placement and the floor dropdown on the edit form. |

### Stock linkage

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_stocks\|a_stocks]]** | Stock item master. Written when stock-list files are uploaded from the equipment edit form. |
| **[[Table Glossary#a_eqstocks\|a_eqstocks]]** | Equipment-to-stock link. Written alongside `a_stocks` during stock-list file uploads. Also checked as a delete blocker. |

### Delete blockers (read-only)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_mtinfo\|a_mtinfo]]** | Maintenance plan. Checked before equipment delete — if maintenance records reference this equipment, delete is blocked. |
| **[[Table Glossary#a_mtsch\|a_mtsch]]** | Maintenance schedule instance. Joined with `a_mtinfo` during delete blocking checks. |
| **[[Table Glossary#a_rent\|a_rent]]** | Rental records. Checked before equipment delete — if rental records reference this equipment, delete is blocked. |

### Helper lookups

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_maker\|a_maker]]** | Maker / supplier master. Used for the maker dropdown on the edit form. Equipment rows store `mat_mk_id` referencing this table. |
| **[[Table Glossary#datamaster\|datamaster]]** | Generic dropdown value master. Used for column header and label resolution across the page. |

### Auth / session context

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#buscomps\|buscomps]]** | Tenant registry (`zaikodb`). Read during login to identify the tenant company and check feature flags. |
| **[[Table Glossary#bk_staff\|bk_staff]]** | Tenant staff accounts. Checked by `openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. Also used to display last-editor names. |
| **[[Table Glossary#bkmasters\|bkmasters]]** | Tenant configuration. One row per `bkid`; stores company name, working-hour settings, display preferences (`hide_eqid`, `del_disable`), and other tenant-level config. |
| **[[Table Glossary#a_auths\|a_auths]]** | Feature permission groups. `setMultiAuth()` reads this to determine list/edit/import/export permissions for the current user. |
