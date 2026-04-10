---
aliases:
  - /{tenant}/config.php
tags:
  - en
  - ppes
  - admin
  - config
  - obsidian
---
# Configuration page

Related notes:

- [[管理 index]]
- [[Schedule calendar]]

## Summary

`/{tenant}/config.php` edits shared tenant configuration in `bkmasters`.

- it is mostly a single-record edit/view page
- it exposes configuration values consumed by schedule, inventory, and shared template behavior
- it has no list page in practice; it acts like a configuration form

## Mermaid: config consumers

```mermaid
flowchart TD
    Config["bkmasters"] --> Sch["sch.php term"]
    Config --> Tana["tana.php date range"]
    Config --> Head["shared head.inc behavior like hide_eqid"]
    Config --> User["merged into aspUser user context"]
```

## Tables involved

- `bkmasters`
- `buscomps`
- `bk_staff`
- `a_auths`
- `datamaster`

## Inferred entity relationships

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
        int zaiko_add
        int hide_eqid
        int del_disable
        varchar dl_char
        date tana_date1
        date tana_date2
        varchar compname
        varchar mng_mail
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
    a_mtinfo {
        int bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        int mtinfo_kbn
        int del_flg
    }
    a_stocks {
        int bkid PK
        int hin_id PK
        int fc_id PK "app-FK to a_factory"
        int stock_kbn
        int stk_num
        int stk_num_warn
    }
    datamaster {
        int propid PK
        int itid PK
        varchar itname
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "level"
    datamaster ||--o{ bkmasters : "option_labels"
    bkmasters }o--o{ a_mtinfo : "schedule_defaults"
    bkmasters }o--o{ a_stocks : "inventory_behavior"
```

Reasoning:

- `bkmasters` is a shared tenant configuration row merged into user/application context.
- Its relationships to maintenance and stock are application-level behavior links, not direct foreign keys.

## Side effects

- login/access redirects
- updates shared tenant behavior seen by other pages
- no file handling, no mail sending, no background jobs in this controller

## Important behavior notes

- `aspUser::open()` merges `bkmasters` into the in-memory user context, so many pages can consume these values indirectly
- confirmed consumers include schedule term defaults, inventory date windows, and shared CSS/display flags

## Code references

- `htdocs/base/config.php:25-64`
- `htdocs/base/config.php:85-150`
- `htdocs/base/sch.php:38-44`

---

## Appendix: table glossary

> Full schema (columns, types, per-column purpose) for every table listed here is in [[Table Glossary]].

### Tenant configuration (owned by this page)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#bkmasters\|bkmasters]]** | Tenant configuration. The primary table owned by this page. One row per `bkid`. Stores company name (`compname`), schedule term default (`term`), inventory date range (`tana_date1`/`tana_date2`), display preferences (`hide_eqid`, `del_disable`, `zaiko_add`), download character encoding (`dl_char`), and manager email (`mng_mail`). Merged into `aspUser` context at request time, so changes here affect behaviour across all pages. |

### Helper lookups

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#datamaster\|datamaster]]** | Generic dropdown value master. Used on the configuration form for option labels (e.g., encoding choices, display toggle labels). |

### Auth / session context

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#buscomps\|buscomps]]** | Tenant registry (`zaikodb`). Read during login to identify the tenant company and check feature flags. |
| **[[Table Glossary#bk_staff\|bk_staff]]** | Tenant staff accounts. Checked by `openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. |
| **[[Table Glossary#a_auths\|a_auths]]** | Feature permission groups. `setAuth($db, $request, $authId)` reads this to determine what the current user can view or edit on the page. |
