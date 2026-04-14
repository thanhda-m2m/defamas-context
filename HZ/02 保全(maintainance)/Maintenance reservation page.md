---
aliases:
  - /{tenant}/mtinfo_yoyaku.php
  - /{tenant}/mtinfo_yoyaku.php?
tags:
  - en
  - ppes
  - maintenance
  - reservation
  - obsidian
---

# Maintenance reservation page

Related notes:

- [[PPES page map]]
- [[Equipment page]]
- [[Maintenance work page]]
- [[Maintenance results list]]
- [[Schedule calendar]]

## Summary

`/{tenant}/mtinfo_yoyaku.php` is the reservation-oriented maintenance controller.

- It is effectively the reservation-mode sibling of `mtinfo.php`.
- It creates or edits the maintenance definition in `a_mtinfo`.
- For periodic work it expands recurring schedule rows in `a_mtsch`.
- For non-periodic work it can also create `a_mtsch` and `a_mtres` rows.
- It can send reminder mail for periodic maintenance.

## Why this page matters

This page is where maintenance intent becomes schedule data.

- `a_mtinfo` = maintenance header / definition
- `a_mtsch` = scheduled occurrences
- `a_mtres` = result row for one-off work paths

## Controller flow

| Branch | Trigger | Main reads | Main writes | Side effects |
| --- | --- | --- | --- | --- |
| Upload | `upload=1` | `a_equips` and lookup tables | `a_mtinfo`, `a_mtsch` | periodic import |
| Edit/look | `edit=1` | `a_mtinfo`, `a_equips`, optional `a_mtsch`, `a_mtres` | `a_mtinfo`, `a_mtsch`, `a_mtres` | file upload, confirm step |
| Delete row | `delete=1&mts_uid=...` | `a_mtsch`, `a_mtinfo` | `a_mtsch`, sometimes `a_mtinfo` | physical delete |
| List/default | no `edit` | `a_mtinfo`, `a_equips`, `a_eqgroup`, `a_factory`, optional `a_line`, optional `a_mtsch`, optional `a_mtres` | none | CSV download |

## Mermaid: reservation data flow

```mermaid
flowchart TD
    Req["/{tenant}/mtinfo_yoyaku.php"] --> Auth["openUser + auth 11/12/10"]
    Auth --> Mode{"edit?"}

    Mode -->|no| List["list reservations"]
    Mode -->|yes| Edit["load reservation form"]

    Edit --> Load["read a_mtinfo + a_equips"]
    Load --> Kind{"mtinfo_kbn == 1?"}
    Kind -->|periodic| Teiki["save a_mtinfo then generate a_mtsch rows"]
    Kind -->|one-off| Oneoff["save a_mtinfo + a_mtsch + a_mtres"]

    Teiki --> Mail{"reminder mail?"}
    Mail -->|yes| Send["read a_mailtmpl and send mail"]
    Oneoff --> Result["result row available to mtres flow"]
```

## Mermaid: reservation table relationships

> **Note**: The database has zero explicit FK constraints. All relationships below are enforced at the application level.

```mermaid
erDiagram
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
    a_eqgroup {
        smallint bkid PK
        smallint eqg_id PK
    }
    a_equips {
        smallint bkid PK
        int eq_id PK
        smallint eqg_id "app-FK to a_eqgroup"
        varchar fc_id "app-FK to a_factory"
        varchar line_id "app-FK to a_line"
        varchar flr_id "app-FK to a_floor"
        varchar mat_mk_id "app-FK to a_maker"
        smallint del_flg
    }
    a_mtinfo {
        smallint bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        smallint mtinfo_kbn
    }
    a_mtsch {
        smallint bkid PK
        int mt_id PK "app-FK to a_mtinfo"
        date sdate PK
        bigint mts_uid "UNI auto_increment"
    }
    a_mtres {
        smallint bkid PK
        int mts_uid PK "app-FK to a_mtsch"
    }
    a_mailtmpl {
        smallint bkid PK
        smallint mtid PK
    }

    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_mtinfo ||--o{ a_mtsch : "mt_id"
    a_mtsch ||--o| a_mtres : "mts_uid"
```

## Tables involved

## List and look/edit load

- `a_mtinfo`
- `a_equips`
- `a_eqgroup`
- `a_factory`
- `a_line`
- `a_mtsch`
- `a_mtres`
- `a_eqgroup_detail`
- `a_eqitem`
- `a_mailtmpl`
- `datamaster`
- `a_maker`

Auth/session context:

- `buscomps`
- `bk_staff`
- `bkmasters`
- `a_auths`

## Save path

Always written:

- `a_mtinfo`

Written for one-off work:

- `a_mtsch`
- `a_mtres`

Written for periodic work:

- `a_mtsch` via recurring schedule generation

## Import / Export

> For full architecture details, see [[Import-Export architecture]].

| Direction | Format | Template file | Output filename |
|-----------|--------|--------------|-----------------|
| **Export** | CSV | *(no template)* | `mtinfo-{ymdhi}.csv` |
| **Import** | Excel → CSV | *(no template — accepts any .xlsx/.csv)* | temp: `{bkid}-mtinfo.csv` |

- Export streams CSV directly via `printdownLoadHeader()` — no Excel template is used.
- Import via `uploadTeikiFile()` accepts an Excel file, converts to CSV, and creates/updates `a_mtinfo` + generates recurring `a_mtsch` schedule rows via `save_teiki()`.

## Side effects

- sends reminder mail using template `mtid=9`
- generates future schedule rows in `a_mtsch`
- can delete schedule rows and sometimes the parent `a_mtinfo`
- uploads and copies maintenance-related files
- list mode can export CSV

## Key helper functions

| Function | Role |
| --- | --- |
| `makeList()` | reservation/result list builder |
| `makeEdit()` | loads maintenance form state |
| `saveData()` | central save/delete orchestration |
| `save_teiki()` | periodic schedule row generation |
| `inputCheck()` | validation and date checks |
| `uploadTeikiFile()` | bulk periodic import |

## Important behavior notes

- This page is not only a reservation page; for non-periodic work it can also populate `a_mtres`.
- Periodic maintenance is expanded into concrete rows in `a_mtsch`, which is what the schedule and result pages later consume.
- Reminder mail only happens for periodic items under the right status conditions.

## Related page flow

- Starts from [[Equipment page]] when a user creates maintenance from an equipment record.
- Feeds [[Schedule calendar]] through `a_mtsch`.
- Overlaps heavily with [[Maintenance work page]], but this note focuses on the reservation-oriented entry path.

## Code references

- `htdocs/base/mtinfo_yoyaku.php:28-65`
- `htdocs/base/mtinfo_yoyaku.php:84-153`
- `htdocs/base/mtinfo_yoyaku.php:355-375`
- `htdocs/base/mtinfo_yoyaku.php:414-729`
- `htdocs/base/mtinfo_yoyaku.php:938-1266`
- `htdocs/base/mtinfo_yoyaku.php:1419-1529`

---

## Appendix: table glossary

> Full schema (columns, types, per-column purpose) for every table listed here is in [[Table Glossary]].

### Maintenance chain (owned by this page)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_mtinfo\|a_mtinfo]]** | Maintenance plan. The primary table owned by this page. One row per maintenance task per equipment. Stores task name (`mt_name`), type (`mtinfo_kbn` — periodic vs. one-off), planned schedule window (`sc_start_date`/`sc_end_date`), and category (`sc_kbn`). Both save paths (periodic and one-off) write this table. |
| **[[Table Glossary#a_mtsch\|a_mtsch]]** | Maintenance schedule instance. Generated from `a_mtinfo` — for periodic work, `save_teiki()` expands the plan into concrete date rows with `s_date`/`e_date` windows. For one-off work, one `a_mtsch` row is created. Delete can remove individual schedule rows. |
| **[[Table Glossary#a_mtres\|a_mtres]]** | Maintenance result. Written only for non-periodic (one-off) work paths, where this page creates a result row alongside the schedule entry. Keyed by `mts_uid`. |

### Equipment context (read-only)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_equips\|a_equips]]** | Equipment master. Loaded to display the target equipment's name, location, and group. `eq_id` is the parent foreign key for `a_mtinfo`. |
| **[[Table Glossary#a_eqgroup\|a_eqgroup]]** | Equipment group master. Used for the group filter dropdown on the list page. |
| **[[Table Glossary#a_eqgroup_detail\|a_eqgroup_detail]]** | Group-to-item mapping. Read to display dynamic field labels in maintenance context. |
| **[[Table Glossary#a_eqitem\|a_eqitem]]** | Equipment item definition. Read alongside `a_eqgroup_detail` for field label resolution. |

### Location / org hierarchy

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_factory\|a_factory]]** | Factory master. Used for the factory filter dropdown on the list page. |
| **[[Table Glossary#a_line\|a_line]]** | Line master. Used for the line filter, shown as context on the edit form. |

### Mail notification

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_mailtmpl\|a_mailtmpl]]** | Mail template master. Template `mtid=9` is loaded when sending periodic maintenance reminder mail. |

### Helper lookups

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_maker\|a_maker]]** | Maker master. Read for equipment display context in some views. |
| **[[Table Glossary#datamaster\|datamaster]]** | Generic dropdown value master. Used for label resolution (e.g., `mtinfo_kbn` code → display name). |

### Auth / session context

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#buscomps\|buscomps]]** | Tenant registry (`zaikodb`). Read during login to identify the tenant company and check feature flags. |
| **[[Table Glossary#bk_staff\|bk_staff]]** | Tenant staff accounts. Checked by `openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. |
| **[[Table Glossary#bkmasters\|bkmasters]]** | Tenant configuration. One row per `bkid`; stores company name, working-hour settings, display preferences, and other tenant-level config. |
| **[[Table Glossary#a_auths\|a_auths]]** | Feature permission groups. `setAuth()` with auth IDs 11, 12, and 10 determines reservation list/edit/download permissions. |
