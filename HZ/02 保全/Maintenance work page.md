---
aliases:
  - /{tenant}/mtinfo.php
  - /{tenant}/mtinfo.php?
tags:
  - en
  - ppes
  - maintenance
  - work
  - obsidian
---

# Maintenance work page

Related notes:

- [[PPES page map]]
- [[Equipment page]]
- [[Maintenance reservation page]]
- [[Maintenance results list]]
- [[Schedule calendar]]

## Summary

`/{tenant}/mtinfo.php` is the broader maintenance controller used for maintenance definitions, schedule rows, and result-side synchronization.

- In list mode it can show reservation-style or result-style maintenance rows.
- In edit mode it loads one maintenance record and can create or update schedule and result data.
- For periodic work it regenerates schedule rows.
- For non-periodic work it synchronizes one `a_mtsch` row and one `a_mtres` row.

## Core idea

This page sits in the middle of the maintenance pipeline.

- upstream parent: `a_equips`
- owned definition: `a_mtinfo`
- schedule projection: `a_mtsch`
- result projection: `a_mtres`

## Controller flow

| Branch | Trigger | Main reads | Main writes | Side effects |
| --- | --- | --- | --- | --- |
| Upload | `upload=1` | import lookups and `a_equips` | `a_mtinfo`, `a_mtsch` | periodic import |
| Edit/look | `edit=1` | `a_mtinfo`, `a_equips`, optional `a_mtsch`, `a_mtres`, `a_equips_detail` | `a_mtinfo`, `a_mtsch`, `a_mtres` | files, confirm step, reminder mail |
| AJAX delete | `delete=1&mts_uid=...` in list mode | `a_mtsch`, `a_mtinfo` | `a_mtsch`, sometimes `a_mtinfo` | physical delete |
| List/default | no `edit` | joined maintenance/equipment/schedule/result tables | none | CSV download |

## Mermaid: maintenance controller flow

```mermaid
flowchart TD
    Req["/{tenant}/mtinfo.php"] --> Auth["openUser + auth 11/12/10"]
    Auth --> Mode{"edit?"}

    Mode -->|no| List["list rows from a_mtinfo/a_mtsch/a_mtres"]
    Mode -->|yes| Edit["load mtinfo form"]

    Edit --> Load["read a_mtinfo + a_equips"]
    Load --> Check{"periodic?"}
    Check -->|yes| Periodic["save a_mtinfo and regenerate a_mtsch"]
    Check -->|no| Oneoff["save a_mtinfo + a_mtsch + a_mtres"]

    Oneoff --> Files["copy mtinfo files into mtres slots"]
    Periodic --> Mail["optional reminder mail"]
    List --> Sch["schedule links"]
    List --> Results["result links"]
```

## Mermaid: maintenance table relationships

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

    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_eqgroup ||--o{ a_equips : "eqg_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_mtinfo ||--o{ a_mtsch : "mt_id"
    a_mtsch ||--o| a_mtres : "mts_uid"
```

## Tables involved

## List load

- `a_mtinfo`
- `a_equips`
- `a_eqgroup`
- `a_factory`
- `a_line`
- `a_mtsch`
- `a_mtres`
- `datamaster`
- `a_maker`

Auth/session context:

- `buscomps`
- `bk_staff`
- `bkmasters`
- `a_auths`

## Edit load

- `a_mtinfo`
- `a_equips`
- `a_mtsch`
- `a_mtres`
- `a_equips_detail`
- `a_eqgroup_detail`
- `a_eqitem`
- `a_mailtmpl`

## Save path

Always written:

- `a_mtinfo`

One-off path writes:

- `a_mtsch`
- `a_mtres`

Periodic path writes:

- `a_mtsch`

Delete paths remove:

- `a_mtsch`
- sometimes `a_mtinfo`

## Side effects

- periodic save can prune and regenerate future `a_mtsch` rows
- non-periodic save can mirror attachments into mtres storage
- periodic save can send reminder mail from `a_mailtmpl`
- list mode can export CSV
- auth gating can make the page read-only or inaccessible

## Key helper functions

| Function | Role |
| --- | --- |
| `makeList()` | result/reservation list builder |
| `makeEdit()` | form loader for one maintenance record |
| `saveData()` | save/delete orchestration |
| `save_teiki()` | recurring schedule generator |
| `normalize_sub_stfname()` | normalizes assistant staff field |
| `inputCheck()` | validation and time/date rules |
| `uploadTeikiFile()` | periodic import helper |

## Important behavior notes

- The page is both a maintenance definition editor and a schedule/result synchronizer.
- `a_mtres` is not only edited from `mtres.php`; `mtinfo.php` also creates and updates it for one-off work.
- `sch.php` consumes the schedule rows written here.

## Related page flow

- Enter from [[Equipment page]] by choosing maintenance on an equipment record.
- Feed [[Schedule calendar]] through `a_mtsch`.
- Feed [[Maintenance results list]] through completed `a_mtsch` plus `a_mtres`.

## Code references

- `htdocs/base/mtinfo.php:28-52`
- `htdocs/base/mtinfo.php:84-155`
- `htdocs/base/mtinfo.php:203-223`
- `htdocs/base/mtinfo.php:262-601`
- `htdocs/base/mtinfo.php:603-707`
- `htdocs/base/mtinfo.php:807-1134`
- `htdocs/base/mtinfo.php:1307-1426`

---

## Appendix: table glossary

> Full schema (columns, types, per-column purpose) for every table listed here is in [[Table Glossary]].

### Maintenance chain (owned by this page)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_mtinfo\|a_mtinfo]]** | Maintenance plan. The primary table owned by this page. One row per maintenance task per equipment. Stores task name, type (`mtinfo_kbn`), planned schedule window, and category. Both periodic regeneration and one-off creation paths write this table. Delete can also remove this row. |
| **[[Table Glossary#a_mtsch\|a_mtsch]]** | Maintenance schedule instance. For periodic work, `save_teiki()` prunes and regenerates future schedule rows. For one-off work, one row is created/updated. Delete targets individual schedule rows by `mts_uid`. |
| **[[Table Glossary#a_mtres\|a_mtres]]** | Maintenance result. For non-periodic work, this page creates and updates result rows. Attachments can be mirrored from `mtinfo` file slots into `mtres` storage slots. |

### Equipment context (read-only)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_equips\|a_equips]]** | Equipment master. Loaded to display target equipment info. `eq_id` is the parent FK for `a_mtinfo`. |
| **[[Table Glossary#a_equips_detail\|a_equips_detail]]** | Per-equipment item values. Read in edit mode to show equipment field context alongside maintenance data. |
| **[[Table Glossary#a_eqgroup\|a_eqgroup]]** | Equipment group master. Used for the group filter dropdown on the list page. |
| **[[Table Glossary#a_eqgroup_detail\|a_eqgroup_detail]]** | Group-to-item mapping. Read in edit mode for field label resolution. |
| **[[Table Glossary#a_eqitem\|a_eqitem]]** | Equipment item definition. Read for dynamic field label display. |

### Location / org hierarchy

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_factory\|a_factory]]** | Factory master. Used for the factory filter dropdown on the list page. |
| **[[Table Glossary#a_line\|a_line]]** | Line master. Used for the line filter. |

### Mail notification

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_mailtmpl\|a_mailtmpl]]** | Mail template master. Loaded when sending periodic maintenance reminder mail. |

### Helper lookups

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_maker\|a_maker]]** | Maker master. Read for equipment display context. |
| **[[Table Glossary#datamaster\|datamaster]]** | Generic dropdown value master. Used for label resolution (e.g., `mtinfo_kbn` code → display name). |

### Auth / session context

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#buscomps\|buscomps]]** | Tenant registry (`zaikodb`). Read during login to identify the tenant company and check feature flags. |
| **[[Table Glossary#bk_staff\|bk_staff]]** | Tenant staff accounts. Checked by `openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. |
| **[[Table Glossary#bkmasters\|bkmasters]]** | Tenant configuration. One row per `bkid`; stores company name, working-hour settings, display preferences, and other tenant-level config. |
| **[[Table Glossary#a_auths\|a_auths]]** | Feature permission groups. `setAuth()` with auth IDs 11, 12, and 10 determines maintenance list/edit/download permissions. |
