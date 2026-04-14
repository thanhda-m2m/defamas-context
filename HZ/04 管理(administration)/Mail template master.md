---
aliases:
  - /{tenant}/mailtmpl.php
tags:
  - en
  - ppes
  - admin
  - mail
  - obsidian
---

# Mail template master

Related notes:

- [[管理 index]]
- [[Maintenance reservation page]]
- [[Maintenance work page]]

## Summary

`/{tenant}/mailtmpl.php` edits rows in `a_mailtmpl`.

- it does not send mail directly
- it controls the subject/body templates later used by maintenance and rent flows
- it is mostly a simple list/edit/save page

## Mermaid: mail template consumers

```mermaid
flowchart TD
    MailTmpl["a_mailtmpl"] --> Rent["rent.php / api_rent_update / rent_reminder"]
    MailTmpl --> Mt["mtinfo.php / mtinfo_yoyaku.php / mtres.php / reminder"]
```

## Tables involved

- `a_mailtmpl`
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
    a_mailtmpl {
        int bkid PK
        int mtid PK
        varchar subject
        text body
        int mtype
    }
    a_mtinfo {
        int bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        int mtinfo_kbn
        int del_flg
    }
    a_mtres {
        int bkid PK
        int mts_uid PK "app-FK to a_mtsch"
        int del_flg
    }
    a_rent {
        int bkid PK
        int eq_id PK "app-FK to a_equips"
        date start_date PK
    }
    datamaster {
        int propid PK
        int itid PK
        varchar itname
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "level"
    buscomps ||--o{ a_mailtmpl : "bkid"
    a_mailtmpl }o--o{ a_mtinfo : "mtid"
    a_mailtmpl }o--o{ a_mtres : "mtid"
    a_mailtmpl }o--o{ a_rent : "mtid"
    datamaster ||--o{ a_mailtmpl : "ui_labels"
```

Reasoning:

- `a_mailtmpl` does not own maintenance or rent rows, but those flows load templates by `mtid` at the application layer.
- The maintenance/rent links are therefore inferred usage relationships, not strict relational dependencies.

## Side effects

- login/access redirects
- no mail sending from this controller itself
- downstream effect is significant because changed templates alter user-facing mail content

## Important behavior notes

- maintenance flows use template IDs like `9` and `10`
- rent flows use other template IDs such as the return reminder
- available tags are defined in code rather than in a DB table

## Code references

- `htdocs/base/mailtmpl.php:26-68`
- `htdocs/base/mailtmpl.php:72-176`
- `lib/ppes.php:248-343`

---

## Appendix: table glossary

> Full schema (columns, types, per-column purpose) for every table listed here is in [[Table Glossary]].

### Mail template (owned by this page)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_mailtmpl\|a_mailtmpl]]** | Mail template master. The primary table owned by this page. One row per template per tenant. Stores subject line (`subject`), body text (`body`) with replacement tags, and template type (`mtype`). Keyed by `mtid` — specific IDs are consumed by maintenance and rental mail flows. |

### Downstream consumers (read-only context)

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#a_mtinfo\|a_mtinfo]]** | Maintenance plan. Not read directly by this page, but maintenance flows load templates by `mtid` when sending reminder mail. |
| **[[Table Glossary#a_mtres\|a_mtres]]** | Maintenance result. Not read directly by this page, but result flows load templates when sending completion notifications. |
| **[[Table Glossary#a_rent\|a_rent]]** | Rental records. Not read directly by this page, but rental flows load templates for return reminders. |

### Helper lookups

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#datamaster\|datamaster]]** | Generic dropdown value master. Used for label resolution on the template edit form. |

### Auth / session context

| Table | Purpose |
| --- | --- |
| **[[Table Glossary#buscomps\|buscomps]]** | Tenant registry (`zaikodb`). Read during login to identify the tenant company and check feature flags. |
| **[[Table Glossary#bk_staff\|bk_staff]]** | Tenant staff accounts. Checked by `openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. |
| **[[Table Glossary#bkmasters\|bkmasters]]** | Tenant configuration. One row per `bkid`; stores company name, `mng_mail` (manager email for mail dispatch), and other tenant-level config. |
| **[[Table Glossary#a_auths\|a_auths]]** | Feature permission groups. `setAuth($db, $request, $authId)` reads this to determine what the current user can view or edit on the page. |
