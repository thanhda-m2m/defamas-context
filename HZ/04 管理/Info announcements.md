---
aliases:
  - /{tenant}/info.php
tags:
  - en
  - ppes
  - admin
  - info
  - obsidian
---

# Info announcements

Related notes:

- [[管理 index]]
- [[Schedule calendar]]

## Summary

`/{tenant}/info.php` is a simple CRUD page for tenant announcements.

- main business table: `bk_infos`
- list, edit, confirm, save, and soft-delete flow
- content is later surfaced as news/information in schedule-style pages

## Mermaid: flow

```mermaid
flowchart TD
    Req["info.php"] --> Auth["openUser + auth 1"]
    Auth --> Mode{"edit?"}
    Mode -->|no| List["read bk_infos"]
    Mode -->|yes| Edit["load bk_infos row"]
    Edit --> Save["upsert or soft-delete bk_infos"]
    List --> Sch["consumed by sch.php newsList"]
```

## Tables involved

- `bk_infos`
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
        varchar email
        int level "app-FK to a_auths"
        int bs_delete
        int area_id "app-FK to a_area"
        int fc_id "app-FK to a_factory"
    }
    a_auths {
        int bkid PK
        int at_id PK
        varchar at_name
        char at_1
        char at_2
        char at_3
    }
    bk_infos {
        int bkid PK
        int inf_id PK
        varchar inf_title
        text inf_conts
        int del_flg
    }
    datamaster {
        int propid PK
        int itid PK
        varchar itname
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    buscomps ||--o{ bk_infos : "bkid"
    a_auths ||--o{ bk_staff : "level"
    datamaster ||--o{ bk_infos : "ui_labels"
```

Reasoning:

- `bk_infos` is scoped by `bkid`, so it belongs to the tenant/company represented by `buscomps`.
- `bk_staff` and `a_auths` are not content owners of `bk_infos`, but they control who can read/edit it.
- `datamaster` is only a helper/master-data source at the UI/application layer.

## Side effects

- login/access redirects
- confirm/read-only UI states
- no file handling or mail sending in this controller

## Key behaviors

- list mode filters `bk_infos.del_flg = 0`
- delete path is soft-delete oriented
- this page is the editor for announcement data consumed by `sch.php` and `cale.php`

## Code references

- `htdocs/base/info.php:25-61`
- `htdocs/base/info.php:71-102`
- `htdocs/base/info.php:104-179`

---

## Appendix: table glossary

> Full schema (columns, types, per-column purpose) for every table listed here is in [[Table Glossary]].

### Announcement data (owned by this page)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#bk_infos\|bk_infos]]** | Tenant-scoped announcements. The primary table owned by this page. One row per announcement per tenant. Stores title (`inf_title`), content (`inf_conts`), and soft-delete flag (`del_flg`). Content created here is displayed in the news/info widget on [[Schedule calendar]]. |

### Helper lookups

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#datamaster\|datamaster]]** | Generic dropdown value master. Used for label resolution on the announcement form. |

### Auth / session context

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#buscomps\|buscomps]]** | Tenant registry (`zaikodb`). Read during login to identify the tenant company and check feature flags. |
| **[[Table Glossary#bk_staff\|bk_staff]]** | Tenant staff accounts. Checked by `openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. |
| **[[Table Glossary#bkmasters\|bkmasters]]** | Tenant configuration. One row per `bkid`; stores company name, working-hour settings, display preferences, and other tenant-level config. |
| **[[Table Glossary#a_auths\|a_auths]]** | Feature permission groups. `setAuth($db, $request, 1)` determines announcement list/edit permissions. |
