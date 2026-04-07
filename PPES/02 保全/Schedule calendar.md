---
aliases:
  - /{tenant}/sch.php
  - schedule page context
tags:
  - en
  - ppes
  - schedule
  - calendar
  - obsidian
---

# Schedule calendar

Related notes:

- [[PPES page map]]
- [[Equipment page]]
- [[Maintenance reservation page]]
- [[Maintenance work page]]
- [[Maintenance results list]]

## Summary

`/{tenant}/sch.php` is a read-heavy calendar and dashboard page over maintenance schedules.

- It does not own the maintenance data.
- It projects schedule rows into day, month, year, and long-range views.
- It also shows today lists, news, and info widgets.
- Its main job is to help users navigate to equipment and maintenance detail pages.

## Controller flow

| Branch | Trigger | Main reads | Main writes | Side effects |
| --- | --- | --- | --- | --- |
| Day view | default `dm` | maintenance schedule joins | none | calendar rendering |
| Month view | `dm=month` | maintenance schedule joins | none | monthly grid |
| Year view | `dm=year` | maintenance schedule joins | none | yearly summary |
| Long view | `dm=long` | maintenance schedule joins | none | multi-year summary |

## Mermaid: schedule projection flow

```mermaid
flowchart TD
    Req["/{tenant}/sch.php"] --> Auth["openUser + auth flags"]
    Auth --> Widgets["newsList + infoList + todayList"]
    Auth --> Main["makeList"]
    Main --> Filters["date range + factory/group filters"]
    Filters --> Query["read a_equips + a_mtinfo + a_mtsch + a_mtres + a_factory"]
    Query --> Grid["calendar cells"]
    Grid --> Equip["open equip.php"]
    Grid --> Mtinfo["open mtinfo.php"]
    Grid --> Mtres["open mtres.php"]
```

## Mermaid: schedule table relationships

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
    holidays {
        date hday PK
    }

    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_mtinfo ||--o{ a_mtsch : "mt_id"
    a_mtsch ||--o| a_mtres : "mts_uid"
```

## Tables involved

Main calendar reads:

- `a_equips`
- `a_mtinfo`
- `a_mtsch`
- `a_mtres`
- `a_factory`
- `a_line`
- `holidays`
- `datamaster`

Page widget reads:

- `bk_infos`
- `infos`

Auth/session context:

- `buscomps`
- `bk_staff`
- `bkmasters`
- `a_auths`
- `a_eqgroup`

## Side effects

- no business-table writes in this controller
- redirects to login on failed auth
- applies display-mode auth flags used by the template
- renders a navigation hub into equipment and maintenance detail pages

## Important behavior notes

- This page is a projection, not a source-of-truth editor.
- The rows it shows come from maintenance pages that write `a_mtinfo`, `a_mtsch`, and `a_mtres`.
- If schedule data looks wrong here, the root cause is often upstream in [[Maintenance reservation page]] or [[Maintenance work page]].

## Related page flow

- reads schedule rows generated from [[Maintenance reservation page]] and [[Maintenance work page]]
- links to [[Equipment page]] and maintenance detail pages
- complements [[Maintenance results list]] as the calendar-oriented view of the same data

## Code references

- `htdocs/base/sch.php:19-48`
- `htdocs/base/sch.php:64-165`
- `htdocs/base/sch.php:167-733`
