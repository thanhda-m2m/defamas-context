---
aliases:
  - table glossary
  - schema reference
tags:
  - en
  - ppes
  - schema
  - reference
---


# Table Glossary

Global schema reference for all database tables used across the PPES system.
Each section links back to the page note(s) that use the table.

> **Note**: The database has zero explicit FK constraints. All relationships are enforced at the application level. Every tenant table includes a `bkid` (smallint) partition key for multi-tenancy row isolation.

> **Base PHP route access matrix**: Regenerated from `htdocs/base/**/*.php` on 2026-04-14. The route columns were replaced by `Read by base PHP` and `Written by base PHP` because the old wide matrix excluded APIs and several helper routes. `Read` includes direct SQL plus route-called helper reads from [[lib/aspUser.php]], [[lib/ads.php]], [[lib/ppes.php]], and [[lib/tbt.php]]. `Written` includes inserts, updates, upserts, deletes, and `setFile()` writes to `a_files`. Common login/auth bootstrap reads are intentionally excluded unless the page itself is a login/password/account-management use case; otherwise every protected page would falsely look like a staff/auth screen. Blank cells mean no direct business access from `htdocs/base` was found. Text symlink placeholders such as [[htdocs/base/index.php]] and [[htdocs/base/mtinfo_yoyaku.php]] are represented by their target routes ([[htdocs/base/sch.php]] and [[htdocs/base/mtinfo.php]]).

> **Review note**: [[htdocs/base/config.php]] still contains an unused `makeList()` query against `props`, and [[htdocs/base/forgot_pw.php]]/auth helpers reference `emps`; neither table has a section in this glossary or a `CREATE TABLE` entry in `docker/base_db.dump`, so they are not matrixed here.

---

## Table index

| No. | Table                 | Category              | Brief purpose                           |
| --- | --------------------- | --------------------- | --------------------------------------- |
| 1   | [[#a_equips]]         | Core maintenance      | Equipment master                        |
| 2   | [[#a_mtinfo]]         | Core maintenance      | Maintenance plan                        |
| 3   | [[#a_mtsch]]          | Core maintenance      | Maintenance schedule instance           |
| 4   | [[#a_mtres]]          | Core maintenance      | Maintenance result                      |
| 5   | [[#a_factory]]        | Location / org        | Factory master                          |
| 6   | [[#a_line]]           | Location / org        | Line master                             |
| 7   | [[#a_floor]]          | Location / org        | Floor master                            |
| 8   | [[#a_area]]           | Location / org        | Area master                             |
| 9   | [[#holidays]]         | Calendar support      | Public holiday dates                    |
| 10  | [[#datamaster]]       | Master data           | Generic dropdown value master (global)  |
| 11  | [[#ads_master]]       | Master data           | Tenant-scoped dropdown value master     |
| 12  | [[#bk_idmaster]]      | Master data           | Application-level serial ID allocator   |
| 13  | [[#bk_infos]]         | Page widget           | Tenant-scoped announcements             |
| 14  | [[#infos]]            | Page widget           | System-wide announcements               |
| 15  | [[#buscomps]]         | Auth / admin          | Tenant registry (zaikodb)               |
| 16  | [[#bk_staff]]         | Auth / admin          | Tenant staff accounts                   |
| 17  | [[#bkmasters]]        | Auth / admin          | Tenant configuration                    |
| 18  | [[#a_auths]]          | Auth / admin          | Feature permission groups               |
| 19  | [[#busareas]]         | Auth / admin          | Business company ↔ area assignment      |
| 20  | [[#bustypengdays]]    | Auth / admin          | Business type NG days                   |
| 21  | [[#loginhist]]        | Auth / admin          | Login / logout audit trail              |
| 22  | [[#staff]]            | Auth / admin          | Super-admin staff accounts (zaikodb)    |
| 23  | [[#a_eqgroup]]        | Equipment domain      | Equipment group master                  |
| 24  | [[#a_eqgroup_detail]] | Equipment domain      | Equipment group ↔ custom field junction |
| 25  | [[#a_eqhist]]         | Equipment domain      | Equipment history / change log          |
| 26  | [[#a_eqitem]]         | Equipment domain      | Equipment custom field definition       |
| 27  | [[#a_eqpoint]]        | Equipment domain      | Equipment inspection point master       |
| 28  | [[#a_eqstocks]]       | Equipment domain      | Equipment ↔ stock linkage               |
| 29  | [[#a_equips_detail]]  | Equipment domain      | Equipment custom field values           |
| 30  | [[#a_ckgroup]]        | Inspection / check    | Check group master                      |
| 31  | [[#a_ckgroup_detail]] | Inspection / check    | Check group ↔ check item junction       |
| 32  | [[#a_ckitem]]         | Inspection / check    | Check item master                       |
| 33  | [[#a_files]]          | File management       | Generic file attachment registry        |
| 34  | [[#a_mailtmpl]]       | Mail / notification   | Mail template master                    |
| 35  | [[#mail_master]]      | Mail / notification   | Mail recipient master                   |
| 36  | [[#a_maker]]          | Maker / vendor        | Maker / manufacturer master             |
| 37  | [[#a_mtbf]]           | Reliability           | MTBF / MTTR reliability metrics         |
| 38  | [[#a_rent]]           | Rental                | Equipment rental / loan record          |
| 39  | [[#a_stocks]]         | Stock / inventory     | Stock / spare parts inventory master    |
| 40  | [[#a_tana]]           | Stock / inventory     | Physical inventory / stocktaking record |
| 41  | [[#calendars]]        | Calendar / scheduling | Tenant-scoped calendar events           |
| 42  | [[#myview]]           | Calendar / scheduling | User-bookmarked schedule instances      |
| 43  | [[#p_proj]]           | CAPEX / procurement   | Project master                          |
| 44  | [[#p_ringi]]          | CAPEX / procurement   | Approval request / ringi                |
| 45  | [[#p_rinitem]]        | CAPEX / procurement   | Approval request line item              |
| 46  | [[#p_purchase]]       | CAPEX / procurement   | Purchase order                          |
| 47  | [[#p_puritem]]        | CAPEX / procurement   | Purchase order line item                |
| 48  | [[#p_item]]           | CAPEX / procurement   | Procurement item master                 |
| 49  | [[#p_mente]]          | CAPEX / procurement   | Project maintenance record              |
| 50  | [[#p_sisan]]          | CAPEX / procurement   | Fixed asset register                    |
| 51  | [[#p_sisancode]]      | CAPEX / procurement   | Asset code / tag master                 |
| 52  | [[#syain_master]]     | Worker                | Worker / operator master                |

---

## a_equips

**Purpose:** Equipment master. One row per physical piece of equipment owned by a tenant. Holds the name, factory/line location, equipment group, machine specifications, and up to 16 custom field slots. Soft-deleted via `del_flg` — deleted rows are hidden from all screens but retained in the database.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. All queries filter `WHERE bkid = $this->_bkid`. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_mtres_update.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance results list]]<br>[[htdocs/base/tana.php]] |
| `eq_id` | int(11) NOT NULL | Equipment ID — integer assigned at creation. Composite PK with `bkid`. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_mtres_update.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `eq_name` | varchar(100) | Equipment display name shown on all screens. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `eq_kbn` | smallint(6) | Equipment type classification code. Resolved to label via `datamaster` (`propid='eq_kbn'`). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `eq_stat` | smallint(6) | Equipment operational status code. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `disporder` | smallint(6) | Sort order for display lists. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_mtres_update.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance results list]]<br>[[htdocs/base/tana.php]] |
| `modify_date` | datetime | Datetime of last modification. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance results list]]<br>[[htdocs/base/tana.php]] |
| `create_date` | datetime | Datetime of row creation. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `stf_id` | smallint(6) | Staff ID of the last editor. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance results list]]<br>[[htdocs/base/tana.php]] |
| `eqg_id` | smallint(6) | Equipment group ID (app-FK → `a_eqgroup.eqg_id`). Used for the group filter on [[htdocs/base/sch.php]]. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `eq_vals` | text | Serialised custom field values (JSON-like). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `eq_words` | text | Full-text search keyword blob. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `eqv11`–`eqv16` | varchar(32) ×6 | Six additional free-form string custom fields. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `del_flg` | tinyint(1) | Soft-delete flag. `1` = deleted and hidden from all pages. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `fc_num` | varchar(16) | Factory-internal machine tag / management number. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `mat_mcno` | varchar(50) | Manufacturer model number. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_mtres_update.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `mat_nensiki` | varchar(8) | Year of manufacture (e.g. `2019`). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `mat_ton` | float | Capacity / tonnage. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `mat_mk_id` | varchar(8) | Maker ID (app-FK → `a_maker.mk_id`). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `mvdata` | text | Equipment transfer/movement history data. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `upload` | tinyint(1) | `1` if the equipment has attached uploaded files. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |
| `details` | blob | Rich-text detail / description (HTML blob). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance results list]]<br>[[htdocs/base/tana.php]] |
| `flr_id` | varchar(16) | Floor ID (app-FK → `a_floor.flr_id`). | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment page]]<br>[[Maintenance results list]] |

---

## a_mtinfo

**Purpose:** Maintenance plan. One row per maintenance task attached to an equipment. Stores the task name, type, planned schedule window, cost estimate, and up to 19 attached files. The `sc_kbn` column distinguishes periodic BT tasks from estimate/quotation workflows.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid`                             | smallint(6) NOT NULL               | Tenant partition key.                                                                  | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mt_id`                            | int(11) NOT NULL                   | Maintenance plan ID (business PK). Composite PK with `bkid`.                           | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `eq_id`                            | int(11) NOT NULL                   | Equipment ID (app-FK → `a_equips.eq_id`).                                              | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `res_date`                         | datetime                           | Registration date of the plan.                                                         | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]]                     | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mt_name`                          | varchar(64)                        | Maintenance task display name.                                                         | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]]                     | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `purpose`                          | varchar(128)                       | Purpose / objective of the task.                                                       | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mt_uid`                           | bigint(20) unsigned AUTO_INCREMENT | Globally unique row ID across all tenants.                                             | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `disporder`                        | smallint(6)                        | Sort order.                                                                            | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `uptime`                           | int(11)                            | Unix timestamp of last update.                                                         | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `modify_date` / `create_date`      | datetime                           | Audit timestamps.                                                                      | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `stf_id`                           | smallint(6)                        | Staff ID of last editor.                                                               | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]                           | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `ree_date`                         | datetime                           | Re-registration / revision date.                                                       | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `eq_file1`–`eq_file19`             | varchar(128) ×19                   | Attached file paths (19 slots).                                                        | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]                                        | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mt_detail`                        | varchar(512)                       | Detailed description of the maintenance work.                                          | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `failure_place`                    | varchar(512)                       | Location of failure or inspection point.                                               | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `sc_start_date`                    | date                               | Schedule window start date (plan-level).                                               | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `sc_end_date`                      | date                               | Schedule window end date (plan-level).                                                 | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mtinfo_kbn`                       | smallint(6)                        | Maintenance type code. Resolved to label via `datamaster` (`propid='mtinfo_kbn'`).     | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `import_flg`                       | smallint(6)                        | Flag marking rows imported from external data.                                         | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]                                        | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `sc_kbn`                           | smallint(6)                        | Schedule category. `0` = periodic BT; other values = estimate/quotation/slip workflow. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]]                                               | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mt_cost`                          | int(11)                            | Estimated cost (plan-level).                                                           | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `del_flg`                          | tinyint(1)                         | Soft-delete flag.                                                                      | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]                           | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `sp_start_date` / `sp_end_date`    | datetime                           | Special period start/end (e.g. extended work window).                                  | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `failure_date`                     | date                               | Date of failure event.                                                                 | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `estimate_kbn`                     | varchar(64)                        | Estimate type code.                                                                    | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `fas_date` / `fae_date`            | datetime                           | FA (field activity) start / end datetimes.                                             | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `fas2_date` / `fae2_date`          | datetime                           | Secondary FA start / end datetimes.                                                    | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `fail_date`                        | datetime                           | Failure occurrence datetime.                                                           | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mt_stat`                          | char(1)                            | Plan status code (`'0'` = default).                                                    | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `main_stfname`                     | varchar(16)                        | Denormalised main staff name (snapshot).                                               | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `sub_stfname`                      | varchar(128)                       | Denormalised sub staff names (comma-separated snapshot).                               | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]]                     | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `matsuo`                           | text                               | Supplemental data blob.                                                                | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mt_fixtime`                       | smallint(6)                        | Fixed time slot allocation.                                                            | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `upload`                           | tinyint(1)                         | `1` if plan has uploaded files.                                                        | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `ins_kbn` / `ins_term` / `ins_cat` | smallint / varchar                 | Inspection type / term / category codes.                                               | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `ins_data`                         | text                               | Inspection data blob.                                                                  | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `stat1`–`stat5`                    | smallint(6) ×5                     | Custom status flag codes 1–5.                                                          | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `stats1` / `stats2`                | varchar(32)                        | Custom status string fields.                                                           | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mtdate1`                          | date                               | Plan-level quote request date (copied from `mtr_mtdate1` at plan creation).            | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]]                                               | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `dpdate1`                          | date                               | Plan-level slip issue date (copied from `mtr_dpdate1` at plan creation).               | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]]                                               | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mmvdata`                          | blob                               | Movement data blob.                                                                    | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                                            | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |

---

## a_mtsch

**Purpose:** Maintenance schedule instance. One row per scheduled occurrence of a plan (`mt_id` + `sdate`). This is the **single source of truth for all calendar icons** on [[htdocs/base/sch.php]]. Owns the execution window (`s_date`/`e_date`), the completion flag (`mtr_done`), and the four date columns that drive Path B icons.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mt_id` | int(11) NOT NULL | Maintenance plan ID (app-FK → `a_mtinfo.mt_id`). Part of composite PK. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `sdate` | date NOT NULL | Schedule date (part of composite PK `bkid, mt_id, sdate`). | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `s_date` | datetime | Schedule window start (datetime precision). Used for Path A date-range matching. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `e_date` | datetime | Schedule window end. Used for overdue check: `e_date < today` → yellow background. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mts_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique schedule instance ID. Referenced as FK by `a_mtres.mts_uid`. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_done` | tinyint(1) DEFAULT 0 | **Completion / result-registered flag. Drives Path A icons:** truthy → ● (result done); falsy + not overdue → ○; falsy + overdue → ○ yellow. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `del_flg` | tinyint(1) | Soft-delete flag. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_stat` | char(1) | Schedule status code (`'0'` = default). | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `upload` | tinyint(1) | `1` if schedule instance has uploaded files. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_mtdate1` | date | **Planned quote request date. Drives △ (Path B priority 4).** Suppressed when `mtr_mtdate2` is set. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_mtdate2` | date | **Actual quote date. Drives ▲ (Path B priority 3).** Setting this suppresses `mtr_mtdate1` in the PHP icon map. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_dpdate1` | date | **Planned slip issue date. Drives □ (Path B priority 2).** Suppressed when `mtr_dpdate2` is set. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_dpdate2` | date | **Actual slip issue date. Drives ■ (Path B priority 1).** Setting this suppresses `mtr_dpdate1` in the PHP icon map. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `sendnum` | smallint(6) | Send / notification dispatch counter. | [[htdocs/base/api_mtres_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]] | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |

---

## a_mtres

**Purpose:** Maintenance result. One row per completed schedule instance (keyed by `mts_uid`). Stores work details, staff names, actual work dates, costs, and up to 15 attached files. On [[htdocs/base/sch.php]] this table is joined via LEFT JOIN for display data only — it does **not** control any calendar icon.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid`                                     | smallint(6) NOT NULL               | Tenant partition key.                                                        | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mts_uid`                                  | int(11) NOT NULL                   | Schedule instance ID (app-FK → `a_mtsch.mts_uid`). Composite PK with `bkid`. | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_uid`                                  | bigint(20) unsigned AUTO_INCREMENT | Globally unique result row ID.                                               | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_detail`                               | varchar(512)                       | Description of work performed.                                               | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_failure`                              | varchar(512)                       | Failure / defect description.                                                | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_gensyo`                               | varchar(512)                       | Symptom description (現象).                                                    | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_genin`                                | varchar(512)                       | Root cause description (原因).                                                 | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `uptime`                                   | int(11)                            | Unix timestamp of last update.                                               | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `modify_date` / `create_date`              | datetime                           | Audit timestamps.                                                            | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `stf_id`                                   | smallint(6)                        | Staff ID of last editor.                                                     | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_file1`–`mtr_file15`                   | varchar(128) ×15                   | Attached file paths (15 slots).                                              | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtre_date` / `mtrs_date`                  | datetime                           | Work end / start datetimes (actual).                                         | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtrs2_date` / `mtre2_date`                | datetime                           | Secondary work period start / end.                                           | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `fins_date` / `fine_date`                  | datetime                           | Final inspection start / end datetimes.                                      | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_cost`                                 | int(11)                            | Actual work cost.                                                            | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mtr_cost2`                                | int(11)                            | Secondary cost field (e.g. parts cost).                                      | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `del_flg`                                  | tinyint(1)                         | Soft-delete flag.                                                            | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/mtres.php]]                                           |
| `mtr_import_flg`                           | char(1)                            | Import flag.                                                                 | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mtr_mt_name`                              | varchar(64)                        | Denormalised task name (snapshot at result creation time).                   | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `mtr_purpose`                              | varchar(128)                       | Denormalised purpose (snapshot).                                             | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[htdocs/base/mtres.php]]                                           |
| `o_s_date` / `o_e_date`                    | datetime                           | Original schedule window snapshot (copied from `a_mtsch` at result time).    | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_stat`                                 | char(1)                            | Result status code.                                                          | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_main_stfname`                         | varchar(16)                        | Denormalised main staff name.                                                | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[htdocs/base/mtres.php]]                 |
| `mtr_sub_stfname`                          | varchar(128)                       | Denormalised sub staff names.                                                | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[Schedule calendar]] | [[htdocs/base/api_mtres_update.php]]<br>[[htdocs/base/mtres.php]]                 |
| `mtr_wktime`                               | int(11)                            | Actual work time in minutes.                                                 | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_matsuo`                               | blob                               | Supplemental data blob.                                                      | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[htdocs/base/api_mtres_update.php]]<br>[[htdocs/base/mtres.php]]                 |
| `mtr_fixtime`                              | smallint(6)                        | Fixed time allocation.                                                       | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `upload`                                   | tinyint(1)                         | `1` if result has uploaded files.                                            | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_mitei`                                | tinyint(1)                         | Tentative / unconfirmed result flag.                                         | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Schedule calendar]]                     | [[htdocs/base/mtres.php]]                                           |
| `mtr_stat1`–`mtr_stat5`                    | smallint(6) ×5                     | Custom status flag codes 1–5.                                                | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]              | [[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                           |
| `mtr_stats1` / `mtr_stats2` / `mtr_stats3` | varchar                            | Custom status string fields.                                                 | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_downtime`                             | int(11)                            | Equipment downtime caused by this maintenance (minutes).                     | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `mtr_estimate_kbn`                         | varchar(64)                        | Denormalised estimate type code.                                             | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |
| `p_mts_uid`                                | int(11)                            | Parent schedule UID (for linked / child results).                            | [[htdocs/base/api_mtres_update.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]                                  | [[htdocs/base/mtres.php]]                                           |

---

## a_factory

**Purpose:** Factory master. Top-level location grouping. Every equipment row has a `fc_id` that is an app-level FK into this table. Used to populate the factory filter dropdown on [[htdocs/base/sch.php]].

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. | [[htdocs/base/api_equip.php]]<br>[[Authorization master]]<br>[[Equipment group master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `fc_id` | varchar(16) NOT NULL | Factory code (business PK). Composite PK with `bkid`. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `fc_name` | varchar(64) | Factory display name. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `area_id` | smallint(6) | Area ID (app-FK → `a_area.area_id`). | [[htdocs/base/api_equip.php]]<br>[[Authorization master]]<br>[[Equipment group master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `fc_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique factory row ID. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `disporder` | smallint(6) | Sort order. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `fc_del` | tinyint(1) | Soft-delete flag. | [[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |

---

## a_line

**Purpose:** Line master. Sub-location within a factory. Keyed by `bkid + line_id + fc_id`. Used for the line filter dropdown and shown as a column label in the calendar grid.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `line_id` | varchar(8) NOT NULL | Line code. Part of composite PK. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `fc_id` | varchar(16) NOT NULL | Factory code (app-FK → `a_factory.fc_id`). Part of composite PK. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `line_name` | varchar(32) | Line display name. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `disporder` | smallint(6) | Sort order. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `line_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique line row ID. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `old_names` | text | Historical line name list (for rename audit trail). | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `line_del` | tinyint(1) | Soft-delete flag. | [[htdocs/base/api/get_line.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |

---

## holidays

**Purpose:** Public holiday date list. [[htdocs/base/sch.php]] reads this table to colour holiday column headers pink in the calendar grid. Shared across all tenants (no `bkid`).

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `hday` | date NOT NULL | Holiday date (PK). | [[htdocs/base/rent.php]]<br>[[Schedule calendar]] |  |
| `hname` | varchar(32) | Holiday name (Japanese). | [[htdocs/base/rent.php]]<br>[[Schedule calendar]] |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |

---

## datamaster

**Purpose:** Generic dropdown value master. Stores named item lists keyed by `propid` (e.g. `mtinfo_kbn`, `eq_kbn`) with labels in four languages. Read via `ppes_master()` to resolve integer codes → display names in Japanese, English, Chinese, or Vietnamese. Shared across all tenants (no `bkid`).

| Column      | Type                 | Purpose                                                                 | Read by base PHP                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | Written by base PHP         |
| ----------- | -------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| `propid`    | varchar(16) NOT NULL | Property / list identifier (e.g. `'mtinfo_kbn'`). Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[htdocs/base/dbcheck.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/section.php]] |
| `itid`      | smallint(6) NOT NULL | Item code within the list. Part of composite PK.                        | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[htdocs/base/dbcheck.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/section.php]] |
| `itname`    | varchar(32)          | Japanese display name.                                                  | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]]                                | [[htdocs/base/section.php]] |
| `itname_en` | varchar(32)          | English display name.                                                   | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]]                                | [[htdocs/base/section.php]] |
| `itname_cn` | varchar(32)          | Chinese display name.                                                   | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]]                                | [[htdocs/base/section.php]] |
| `itname_vn` | varchar(32)          | Vietnamese display name.                                                | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]]                                | [[htdocs/base/section.php]] |
| `disporder` | smallint(6)          | Sort order.                                                             | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]]                                | [[htdocs/base/section.php]] |
| `uptime`    | int(11)              | Unix timestamp of last update.                                          | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Authorization master]]<br>[[Configuration page]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Info announcements]]<br>[[Mail template master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]]                                | [[htdocs/base/section.php]] |

---

## bk_infos

**Purpose:** Tenant-scoped announcements. Rows belong to a specific `bkid`. Shown in the **【お知らせ】** info widget on the schedule / TOP page. Supports four-language titles/bodies and optional factory-level visibility restrictions.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `inf_id`. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `inf_id` | int(11) NOT NULL | Announcement ID. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `inf_title` | varchar(255) | Title (Japanese). | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `inf_title_en` / `inf_title_cn` / `inf_title_vn` | varchar(255) ×3 | Title in English / Chinese / Vietnamese. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `inf_conts` | text | Body content (Japanese). | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `inf_conts_en` / `inf_conts_cn` / `inf_conts_vn` | text ×3 | Body in English / Chinese / Vietnamese. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `inf_date` | date | Published date displayed in the widget. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `inf_hidden` | tinyint(1) | `1` = draft / hidden from users. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `fc_ids` | varchar(128) | Comma-separated `fc_id` list. When set, announcement is visible only to those factories. Empty = visible to all. | [[Info announcements]]<br>[[Schedule calendar]] |  |
| `inf_file1`–`inf_file5` | varchar(64) ×5 | Attached file paths. | [[Info announcements]]<br>[[Schedule calendar]] |  |
| `uptime` | int(11) | Unix timestamp of last update. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `stf_id` | smallint(6) | Staff ID of creator. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |
| `del_flg` | tinyint(1) | Soft-delete flag. | [[Info announcements]]<br>[[Schedule calendar]] | [[Info announcements]] |

---

## infos

**Purpose:** System-wide announcements published from `/padmin/`. Rendered in the info widget alongside `bk_infos` entries. No `bkid` — visible to all tenants. Managed exclusively by super-admins.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `inf_id` | int(11) NOT NULL | System announcement ID (PK). | [[Schedule calendar]] |  |
| `inf_title` | varchar(128) | Announcement title. | [[Schedule calendar]] |  |
| `inf_conts` | text | Announcement body. | [[Schedule calendar]] |  |
| `inf_date` | date | Published date. | [[Schedule calendar]] |  |
| `inf_hidden` | tinyint(1) | `1` = hidden from tenant pages. | [[Schedule calendar]] |  |
| `is_web` | tinyint(1) | `1` = also show on public website. | [[Schedule calendar]] |  |
| `web_hidden` | tinyint(1) | `1` = hide from website display. | [[Schedule calendar]] |  |
| `uptime` | int(11) | Unix timestamp of last update. | [[Schedule calendar]] |  |

---

## buscomps

**Purpose:** Tenant registry. Lives in `zaikodb` (the super-admin database). One row per tenant company. Read during login to identify the tenant, verify credentials, check feature flags (`use_api`, `spe_*`, `use_*`), and resolve `bkid`. Managed from `/padmin/`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | bigint(20) unsigned AUTO_INCREMENT | Tenant ID — globally unique, auto-incremented. This is the `bkid` used in all tenant tables. | [[Configuration page]]<br>[[htdocs/base/forgot_pw.php]] |  |
| `bcid` | int(11) | Business company sequence ID. | [[Configuration page]] |  |
| `email` | varchar(64) | Primary contact email. | [[Configuration page]]<br>[[htdocs/base/forgot_pw.php]] |  |
| `passwd` | varchar(16) | Admin login password (plain text). | [[Configuration page]]<br>[[htdocs/base/forgot_pw.php]] |  |
| `bcname` | varchar(64) | Company display name. | [[Configuration page]] |  |
| `bckana` | varchar(64) | Company name kana reading. | [[Configuration page]] |  |
| `phone` / `fax` | varchar(16) | Contact phone / fax. | [[Configuration page]] |  |
| `pc` | varchar(8) | Postal code. | [[Configuration page]] |  |
| `pref` | smallint(6) | Prefecture code. | [[Configuration page]] |  |
| `addr` | varchar(96) | Address. | [[Configuration page]] |  |
| `bcstat` | char(1) | Company status (`'0'` = active). | [[Configuration page]]<br>[[htdocs/base/forgot_pw.php]] |  |
| `bikou` | text | Internal notes. | [[Configuration page]] |  |
| `mng_name` / `mng_sect` | varchar | Manager name / section. | [[Configuration page]] |  |
| `loginid` | varchar(16) | Admin login ID. | [[Configuration page]]<br>[[htdocs/base/forgot_pw.php]] |  |
| `subdom` | varchar(8) | Subdomain / tenant URL path code (e.g. `ahihi` for `/{ahihi}/sch.php`). | [[Configuration page]] |  |
| `useplan` | char(1) | Subscription plan code. | [[Configuration page]] |  |
| `startdate` / `enddate` | date | Contract start / end dates. | [[Configuration page]] |  |
| `use_api` | tinyint(1) | API access feature flag. | [[Configuration page]] |  |
| `api_pw` | varchar(32) | API password. | [[Configuration page]] |  |
| `api_ips` | varchar(128) | API IP whitelist (comma-separated). | [[Configuration page]] |  |
| `use_map` | tinyint(1) | Map feature enabled flag. | [[Configuration page]] |  |
| `use_shift` | tinyint(1) | Shift management feature flag. | [[Configuration page]] |  |
| `use_ura` | tinyint(1) | Back-end (裏) feature flag. | [[Configuration page]] |  |
| `use_uacj` / `use_ver2` | tinyint(1) | Additional feature toggle flags. | [[Configuration page]] |  |
| `spe_17_1` / `spe_19_1` / `spe_14_1` | tinyint(1) | Tenant-specific special feature flags. | [[Configuration page]] |  |
| `regtime` | int(11) | Registration Unix timestamp. | [[Configuration page]] |  |
| `cmail` / `cstype` | varchar | CS contact email / support type. | [[Configuration page]] |  |
| `dis_syukei` / `dis_seikyu` | tinyint(1) | Disable aggregate / billing features. | [[Configuration page]] |  |
| `yk_count` / `uk_count` / `car_count` / `ag_count` / `drv_count` | int / smallint | Capacity / licence count limits per feature. | [[Configuration page]] |  |

---

## bk_staff

**Purpose:** Tenant staff accounts. One row per staff member per tenant. Checked by `aspUser->openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. The `lang` column determines the display language for that user.

| Column                        | Type                               | Purpose                                                                           | Read by base PHP                                                                                                                                                                                                                                                                                                                                      | Written by base PHP       |
| ----------------------------- | ---------------------------------- | --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| `bkid`                        | smallint(6) NOT NULL               | Tenant partition key. Composite PK with `stf_id`.                                 | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/auto_staff.php]]<br>[[Equipment page]]<br>[[htdocs/base/forgot_pw.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]] | [[Staff management]] |
| `stf_id`                      | int(11) NOT NULL                   | Staff ID.                                                                         | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/auto_staff.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                  | [[Staff management]] |
| `stf_uid`                     | bigint(20) unsigned AUTO_INCREMENT | Globally unique staff row ID.                                                     | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    |                           |
| `login`                       | varchar(64)                        | Login username.                                                                   | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/auto_staff.php]]<br>[[Equipment page]]<br>[[htdocs/base/forgot_pw.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]] | [[Staff management]] |
| `passwd`                      | varchar(16)                        | Login password (plain text).                                                      | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[htdocs/base/forgot_pw.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                   | [[Staff management]] |
| `email`                       | varchar(64)                        | Staff email address.                                                              | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[htdocs/base/forgot_pw.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                   | [[Staff management]] |
| `name`                        | varchar(64)                        | Display name shown in UI.                                                         | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/auto_staff.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                  | [[Staff management]] |
| `level`                       | varchar(4)                         | Permission level code (used alongside `a_auths`).                                 | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    | [[Staff management]] |
| `lang`                        | char(1)                            | Display language: `'0'`=Japanese, `'1'`=English, `'2'`=Chinese, `'3'`=Vietnamese. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[htdocs/base/forgot_pw.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                   | [[Staff management]] |
| `fc_id`                       | varchar(16)                        | Primary factory assignment.                                                       | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/auto_staff.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                  | [[Staff management]] |
| `fc_ids`                      | varchar(128)                       | Multi-factory access list (comma-separated `fc_id`).                              | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    |                           |
| `area_id`                     | smallint(6)                        | Area assignment.                                                                  | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    | [[Staff management]] |
| `sec_name`                    | varchar(32)                        | Section / department name.                                                        | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    | [[Staff management]] |
| `phone`                       | varchar(32)                        | Phone number.                                                                     | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    | [[Staff management]] |
| `stf_order`                   | smallint(6)                        | Display sort order.                                                               | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    |                           |
| `sigfile`                     | varchar(32)                        | Signature image file path.                                                        | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    |                           |
| `is_tbt`                      | tinyint(1)                         | TBT super-admin flag.                                                             | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    |                           |
| `lastlogin`                   | int(11)                            | Unix timestamp of last login.                                                     | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    |                           |
| `uptime`                      | int(11)                            | Unix timestamp of last update.                                                    | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    | [[Staff management]] |
| `modify_date` / `create_date` | datetime                           | Audit timestamps.                                                                 | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]]                                                                    | [[Staff management]] |
| `bs_delete`                   | tinyint(1)                         | Soft-delete flag.                                                                 | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/auto_staff.php]]<br>[[Equipment page]]<br>[[htdocs/base/forgot_pw.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Staff management]]<br>[[htdocs/base/tana.php]] | [[Staff management]] |

---

## bkmasters

**Purpose:** Tenant configuration. One row per `bkid`. Stores company name, working-hour settings, display preferences, billing configuration, and other tenant-level settings used across all pages. Read early in the request lifecycle to apply tenant-specific behaviour.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant ID (PK). | [[Configuration page]] | [[Configuration page]]<br>[[Equipment page]] |
| `compname` | varchar(64) | Company name displayed in the UI header. | [[Configuration page]] |  |
| `license` | varchar(64) | License identifier. | [[Configuration page]] |  |
| `addr1` / `addr2` | varchar(128) | Company address lines. | [[Configuration page]] |  |
| `pc` | varchar(8) | Postal code. | [[Configuration page]] |  |
| `tel` / `fax` | varchar(16) | Company phone / fax. | [[Configuration page]] |  |
| `pref` | varchar(2) | Prefecture code. | [[Configuration page]] |  |
| `ceo` | varchar(32) | CEO name (for printed documents). | [[Configuration page]] |  |
| `mng1`–`mng3` | varchar(32) ×3 | Manager name slots 1–3. | [[Configuration page]] |  |
| `mng_mail` | varchar(64) | Manager email address (used for alert notifications). | [[Configuration page]] |  |
| `wk_start` / `wk_end` | smallint(6) | Working day start / end hour (0–23 integer). | [[Configuration page]] |  |
| `wktime` | smallint(6) | Standard work time in minutes per day. | [[Configuration page]] |  |
| `use_alert` | tinyint(1) | `1` = alert email notifications enabled. | [[Configuration page]] |  |
| `def_term` | smallint(6) | Default schedule term length (months). | [[Configuration page]] |  |
| `rests` | varchar(64) | Rest time configuration string. | [[Configuration page]] |  |
| `sk_kuri` | tinyint(1) | Schedule carry-over setting. | [[Configuration page]] |  |
| `hide_eqid` | tinyint(1) | `1` = hide equipment ID column in lists. | [[Configuration page]] |  |
| `del_disable` | tinyint(1) | `1` = prevent deletion of maintenance records. | [[Configuration page]] |  |
| `dl_char` | char(1) | CSV download character encoding (`'0'`=UTF-8, `'1'`=Shift-JIS). | [[Configuration page]] |  |
| `tana_date1` / `tana_date2` | varchar(8) | Inventory cycle date settings. | [[Configuration page]] |  |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[Configuration page]] | [[Configuration page]]<br>[[Equipment page]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Configuration page]] | [[Configuration page]]<br>[[Equipment page]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Configuration page]] | [[Configuration page]]<br>[[Equipment page]] |

---

## a_auths

**Purpose:** Feature permission groups. One row per permission group per tenant. `aspUser->setAuth($db, $request, $authId)` reads this table to check whether the current user's group has permission to view or edit a specific feature on the current page. Each `at_N` flag maps to a specific feature (e.g. `at_12` = schedule page edit permission).

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `at_id`. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_id` | smallint(6) NOT NULL | Auth group ID. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_name` | varchar(16) | Group display name (e.g. `管理者`, `一般`). | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_all` | tinyint(1) | `1` = grant all permissions shortcut (overrides individual flags). | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_notall` | tinyint(1) | `1` = restrict to own records only. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_params` | varchar(128) | Additional parameter string for fine-grained overrides. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_1` | char(1) | Permission flag for feature 1. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_2` | char(1) | Permission flag for feature 2. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_3` | char(1) | Permission flag for feature 3. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_10` | char(1) | Permission flag for feature 10. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_11` | char(1) | Permission flag for feature 11. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_12` | char(1) | Permission flag for feature 12 (schedule page). | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_13` | char(1) | Permission flag for feature 13. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_14` | char(1) | Permission flag for feature 14. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `at_15` | char(1) | Permission flag for feature 15. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `authid` / `authpass` | varchar(16) | External authentication credentials (optional). | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Authorization master]]<br>[[Staff management]] | [[Authorization master]] |

---

## a_eqgroup

**Purpose:** Equipment group master. Groups of equipment named for categorisation. Used as the `eqg_id` filter on the [[htdocs/base/sch.php]] calendar search form. Equipment rows reference this via `a_equips.eqg_id`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `eqg_id`. | [[htdocs/base/api_equip.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `eqg_id` | smallint(6) NOT NULL | Equipment group ID (PK). | [[htdocs/base/api_equip.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `eqg_name` | varchar(32) | Group display name. | [[htdocs/base/api_equip.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `eqg_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique group row ID. | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `disporder` | smallint(6) | Sort order. | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `nfc_id` | varchar(16) | NFC tag ID associated with this group (for mobile scan features). | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `eqg_data` | blob | Additional group configuration data. | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[Schedule calendar]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Equipment group master]] |

## a_area

**Purpose:** Area master. Top-level geographic grouping, sitting above factory in the location hierarchy. Each factory row has an `area_id` that is an app-level FK into this table. Staff accounts may also carry an `area_id` for access scoping. Used on [[htdocs/base/auth.php]] as a helper lookup for factory/staff UI -- not a direct structural parent of `a_auths`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `area_id`. | [[Authorization master]]<br>[[Equipment group master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `area_id` | smallint(6) NOT NULL | Area ID (business PK). | [[Authorization master]]<br>[[Equipment group master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maker master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[htdocs/base/section.php]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[Factory and location master]] |
| `area_name` | varchar(32) | Area display name. | [[Authorization master]]<br>[[Equipment group master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Maker master]]<br>[[htdocs/base/section.php]]<br>[[Staff management]] | [[Factory and location master]] |
| `area_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique area row ID. | [[Factory and location master]] |  |
| `disporder` | smallint(6) | Sort order. | [[Factory and location master]] | [[Factory and location master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Factory and location master]] | [[Factory and location master]] |
| `modify_date` / `create_date` | datetime | Audit timestamps. | [[Factory and location master]] | [[Factory and location master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Factory and location master]] | [[Factory and location master]] |

---

## a_ckgroup

**Purpose:** Check group master. Groups multiple check items (`a_ckitem`) into a named group that can be assigned to equipment for inspection workflows. Each group is tenant-scoped and ordered by `disporder`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `ckg_id`. |  |  |
| `ckg_id` | smallint(6) NOT NULL | Check group ID (business PK). |  |  |
| `ckg_name` | varchar(32) | Check group display name. |  |  |
| `disporder` | smallint(6) | Sort order for display lists. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |

---

## a_ckgroup_detail

**Purpose:** Check group ↔ check item junction table. Links individual check items to their parent check group. The `required_flg` marks items that must be filled in during inspection.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. |  |  |
| `ckg_id` | smallint(6) NOT NULL | Check group ID (app-FK → `a_ckgroup.ckg_id`). Part of composite PK. |  |  |
| `ck_id` | smallint(6) NOT NULL | Check item ID (app-FK → `a_ckitem.ck_id`). Part of composite PK. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `required_flg` | tinyint(1) | `1` = this check item is mandatory during inspection. |  |  |

---

## a_ckitem

**Purpose:** Check item master. Defines individual inspection/check items with name, type (numeric measurement, selection, etc.), acceptable min/max/base values for numeric checks, and selectable options for dropdown-type items. Used in equipment inspection workflows.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `ck_id`. |  |  |
| `ck_id` | smallint(6) NOT NULL | Check item ID (business PK). |  |  |
| `ck_name` | varchar(32) | Check item display name. |  |  |
| `ck_type` | varchar(2) | Input type code (e.g. numeric measurement, selection). |  |  |
| `max_size` | float | Upper acceptable value for numeric checks. |  |  |
| `min_size` | float | Lower acceptable value for numeric checks. |  |  |
| `base_size` | float | Standard / baseline value for numeric checks. |  |  |
| `select_item` | text | Selectable option values for dropdown-type items (delimited list). |  |  |
| `disporder` | smallint(6) | Sort order for display lists. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |

---

## a_eqgroup_detail

**Purpose:** Equipment group ↔ equipment item junction table. Controls which dynamic custom fields (`a_eqitem`) appear on the equipment edit form for a given equipment group (`a_eqgroup`). The `required_flg` marks mandatory fields; `record_flg` controls visibility in record/history views.

| Column         | Type                               | Purpose                                                                  | Read by base PHP                                                                                                                                                                   | Written by base PHP                                       |
| -------------- | ---------------------------------- | ------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| `bkid`         | smallint(6) NOT NULL               | Tenant partition key. Part of composite PK.                              | [[htdocs/base/api_equip.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Equipment group master]]                               |
| `eqg_id`       | smallint(6) NOT NULL               | Equipment group ID (app-FK → `a_eqgroup.eqg_id`). Part of composite PK.  | [[htdocs/base/api_equip.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Equipment group master]]                               |
| `eqitem_id`    | smallint(6) NOT NULL               | Equipment item ID (app-FK → `a_eqitem.eqitem_id`). Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Equipment group master]]                               |
| `required_flg` | tinyint(1)                         | `1` = this custom field is mandatory on the equipment edit form.         | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Stock management]]                                  | [[Equipment group master]]<br>[[Equipment item master]] |
| `record_flg`   | tinyint(1)                         | `1` = show this field in record/history views.                           | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]                                                                                             | [[Equipment group master]]<br>[[Equipment item master]] |
| `ecgd_uid`     | bigint(20) unsigned AUTO_INCREMENT | Globally unique junction row ID.                                         | [[Equipment group master]]                                                                                                                                                        | [[Equipment group master]]                               |
| `disporder`    | smallint(6)                        | Sort order for field display on the form.                                | [[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Stock management]]                                  | [[Equipment group master]]                               |
| `uptime`       | int(11)                            | Unix timestamp of last update.                                           | [[Equipment group master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[Stock management]]                                                                | [[Equipment group master]]                               |
| `modify_date`  | datetime                           | Datetime of last modification.                                           | [[Equipment group master]]<br>[[Equipment page]]                                                                                                                           | [[Equipment group master]]                               |
| `create_date`  | datetime                           | Datetime of row creation.                                                | [[Equipment group master]]                                                                                                                                                        | [[Equipment group master]]                               |
| `stf_id`       | smallint(6)                        | Staff ID of last editor.                                                 | [[Equipment group master]]<br>[[Equipment page]]<br>[[Maintenance work page]]                                                                                             | [[Equipment group master]]                               |

---

## a_eqhist

**Purpose:** Equipment history / change log. Records timestamped text entries for each equipment item, keyed by `eq_id` + `eq_time` (unix timestamp). Used to track transfer history, status changes, and operator notes.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[Equipment page]] | [[Equipment page]] |
| `eq_id` | int(11) NOT NULL | Equipment ID (app-FK → `a_equips.eq_id`). Part of composite PK. | [[Equipment page]] | [[Equipment page]] |
| `eq_time` | int(11) NOT NULL | Unix timestamp of the history entry. Part of composite PK. | [[Equipment page]] | [[Equipment page]] |
| `eq_conts` | text | History entry text content (transfer notes, status changes, etc.). | [[Equipment page]] | [[Equipment page]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Equipment page]] | [[Equipment page]] |
| `modify_date` | datetime | Datetime of last modification. | [[Equipment page]] | [[Equipment page]] |
| `create_date` | datetime | Datetime of row creation. | [[Equipment page]] | [[Equipment page]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Equipment page]] | [[Equipment page]] |

---

## a_eqitem

**Purpose:** Equipment custom field definition master. Defines the dynamic fields that can appear on equipment edit forms. Each row defines a field name, input type (text, select, number, etc.), validation regex, selectable options, min/max constraints, and display colour. Supports bilingual labels (JP + EN). Linked to equipment groups via `a_eqgroup_detail`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid`           | smallint(6) NOT NULL               | Tenant partition key. Part of composite PK.                                          | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]] | [[Equipment group master]]<br>[[Equipment item master]] |
| `eqitem_name`    | varchar(32) NOT NULL               | Field name (Japanese). Part of composite PK.                                         | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]] | [[Equipment group master]]<br>[[Equipment item master]] |
| `eqitem_id`      | smallint(6)                        | Numeric field definition ID. Referenced by `a_eqgroup_detail` and `a_equips_detail`. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]] | [[Equipment group master]]<br>[[Equipment item master]] |
| `eqitem_type`    | varchar(32)                        | Input type (e.g. text, select, number).                                              | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]] | [[Equipment group master]]<br>[[Equipment item master]] |
| `select_item`    | text                               | Selectable option values for dropdown-type fields (delimited list, Japanese).        | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]] | [[Equipment group master]]<br>[[Equipment item master]] |
| `eqitem_regexp`  | varchar(128)                       | Validation regex pattern applied to user input.                                      | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment item master]]                  |
| `eqitem_uid`     | bigint(20) unsigned AUTO_INCREMENT | Globally unique field definition row ID.                                             | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment item master]]                  |
| `disporder`      | smallint(6)                        | Sort order for field display.                                                        | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]] | [[Equipment group master]]<br>[[Equipment item master]] |
| `uptime`         | int(11)                            | Unix timestamp of last update.                                                       | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment group master]]<br>[[Equipment item master]] |
| `modify_date`    | datetime                           | Datetime of last modification.                                                       | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment group master]]<br>[[Equipment item master]] |
| `create_date`    | datetime                           | Datetime of row creation.                                                            | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment group master]]<br>[[Equipment item master]] |
| `stf_id`         | smallint(6)                        | Staff ID of last editor.                                                             | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment group master]]<br>[[Equipment item master]] |
| `max_size`       | int(11)                            | Upper constraint value for numeric fields.                                           | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment group master]]<br>[[Equipment item master]] |
| `min_size`       | int(11)                            | Lower constraint value for numeric fields.                                           | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment group master]]<br>[[Equipment item master]] |
| `eqitem_name_en` | varchar(32)                        | Field name (English).                                                                | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment item master]]                  |
| `select_item_en` | text                               | Selectable option values (English).                                                  | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment item master]]                  |
| `item_color`     | varchar(16)                        | Display colour code for the field (e.g. for column highlighting).                    | [[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[Stock management]]                    | [[Equipment group master]]<br>[[Equipment item master]] |

---

## a_eqpoint

**Purpose:** Equipment inspection point master. Defines named measurement/inspection points on a piece of equipment. Each point has a name, up to three free-form metadata fields (`fld1`–`fld3`), and a sort order. Soft-deleted via `is_del`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `po_id`. |  |  |
| `po_id` | varchar(32) NOT NULL | Point ID (business PK). |  |  |
| `eq_id` | int(11) | Equipment ID (app-FK → `a_equips.eq_id`). |  |  |
| `po_name` | varchar(64) | Inspection point display name. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `is_del` | tinyint(1) | Soft-delete flag. `1` = deleted / hidden. |  |  |
| `fld1` | varchar(64) | Free-form metadata field 1. |  |  |
| `fld2` | varchar(32) | Free-form metadata field 2. |  |  |
| `fld3` | varchar(16) | Free-form metadata field 3. |  |  |
| `eqp_order` | smallint(6) | Sort order for point display within equipment. |  |  |

---

## a_eqstocks

**Purpose:** Equipment ↔ stock linkage junction table. Associates stock items (`a_stocks.hin_id`) with equipment (`a_equips.eq_id`) at a specific factory. The `tana` column stores shelf/location info. Indexed for both stock-centric and equipment-centric lookups.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `hin_id` | varchar(64) NOT NULL | Stock item code (app-FK → `a_stocks.hin_id`). Part of composite PK. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `eq_id` | int(11) NOT NULL | Equipment ID (app-FK → `a_equips.eq_id`). Part of composite PK. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `modify_date` | datetime | Datetime of last modification. | [[Equipment page]] | [[Equipment page]]<br>[[Stock management]] |
| `create_date` | datetime | Datetime of row creation. | [[Equipment page]] | [[Equipment page]]<br>[[Stock management]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Equipment page]] | [[Equipment page]]<br>[[Stock management]] |
| `fc_id` | varchar(16) NOT NULL | Factory code (app-FK → `a_factory.fc_id`). Part of composite PK. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `tana` | varchar(32) | Shelf / storage location label. | [[Equipment page]] | [[Equipment page]]<br>[[Stock management]] |

---

## a_equips_detail

**Purpose:** Equipment custom field values. Stores the actual value (`eqd_val`) of each dynamic field for a specific equipment item. Keyed by equipment ID + field definition ID (`eqitem_id` → `a_eqitem`). One row per field per equipment.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[htdocs/base/tana.php]] |
| `eq_id` | int(11) NOT NULL | Equipment ID (app-FK → `a_equips.eq_id`). Part of composite PK. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]] |
| `eqitem_id` | int(11) NOT NULL | Equipment item field ID (app-FK → `a_eqitem.eqitem_id`). Part of composite PK. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]] |
| `eqd_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique detail row ID. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]] |
| `eqd_val` | varchar(300) | Stored value for this custom field on this equipment. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[htdocs/base/tana.php]] |
| `disporder` | smallint(6) | Sort order. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[htdocs/base/tana.php]] |
| `modify_date` | datetime | Datetime of last modification. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[htdocs/base/tana.php]] |
| `create_date` | datetime | Datetime of row creation. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/api_rent_update.php]]<br>[[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/rent.php]]<br>[[htdocs/base/tana.php]] | [[Equipment item master]]<br>[[Equipment page]]<br>[[htdocs/base/tana.php]] |

---

## a_files

**Purpose:** Generic file attachment registry. Polymorphic design — `f_type` identifies the parent entity type (e.g. equipment, maintenance), `f_id` is the parent record ID, `f_num` is a slot number. Stores file metadata (name, size) rather than the binary content itself.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `f_type` | varchar(16) NOT NULL | Parent entity type identifier (e.g. `'eq'`, `'mt'`). Part of composite PK. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `f_id` | int(11) NOT NULL | Parent record ID. Part of composite PK. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `f_num` | varchar(8) NOT NULL | File slot number within the parent record. Part of composite PK. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `f_name` | varchar(128) NOT NULL | Original file name. Part of composite PK. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `f_size` | int(11) | File size in bytes. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |
| `f_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique file row ID. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |  |
| `disporder` | smallint(6) | Sort order. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |  |
| `uptime` | int(11) | Unix timestamp of last update. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |  |
| `modify_date` | datetime | Datetime of last modification. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |  |
| `create_date` | datetime | Datetime of row creation. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |  |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Equipment page]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]] |  |

---

## a_floor

**Purpose:** Floor master. Third level in the location hierarchy: Area → Factory → Line → Floor. Keyed by factory + line + floor ID. Equipment rows can reference this via `flr_id`. Used for fine-grained location tracking within a production line.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[htdocs/base/api/get_floor.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]] | [[Factory and location master]] |
| `fc_id` | varchar(16) NOT NULL | Factory code (app-FK → `a_factory.fc_id`). Part of composite PK. | [[htdocs/base/api/get_floor.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]] | [[Factory and location master]] |
| `line_id` | varchar(8) NOT NULL | Line code (app-FK → `a_line.line_id`). Part of composite PK. | [[htdocs/base/api/get_floor.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]] | [[Factory and location master]] |
| `flr_id` | varchar(16) NOT NULL | Floor ID (business PK). Part of composite PK. | [[htdocs/base/api/get_floor.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]] | [[Factory and location master]] |
| `flr_name` | varchar(32) | Floor display name. | [[htdocs/base/api/get_floor.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]] | [[Factory and location master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Factory and location master]] | [[Factory and location master]] |
| `modify_date` | datetime | Datetime of last modification. |  | [[Factory and location master]] |
| `create_date` | datetime | Datetime of row creation. |  | [[Factory and location master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. |  | [[Factory and location master]] |
| `flr_order` | int(11) | Sort order for floor display within a line. | [[htdocs/base/api/get_floor.php]]<br>[[htdocs/base/api_equip.php]]<br>[[Equipment page]]<br>[[Factory and location master]] | [[Factory and location master]] |

---

## a_mailtmpl

**Purpose:** Mail template master. Stores email templates used by maintenance and rental notification flows. Each template has a title (internal label), subject line, body text with placeholder tokens, and a type code (`mtype`). Edited from the mail template management page; consumed downstream when sending notifications.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `mtid`. | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `mtid` | smallint(6) NOT NULL | Mail template ID (business PK). | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `title` | varchar(32) | Internal label / title for the template. | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] |  |
| `subject` | varchar(64) | Email subject line (may contain placeholder tokens). | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `body` | text | Email body text (may contain placeholder tokens). | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `mtype` | char(1) | Template type code (e.g. maintenance notification, rental reminder). | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] |  |
| `disporder` | smallint(6) | Sort order. | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `create_date` | datetime | Datetime of row creation. | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |
| `modify_date` | datetime | Datetime of last modification. | [[htdocs/base/api_rent_update.php]]<br>[[Mail template master]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[htdocs/base/rent.php]] | [[Mail template master]] |

---

## a_maker

**Purpose:** Maker / manufacturer master. Stores equipment manufacturer and vendor contact information including direct and agent (代理店) contacts. Each maker has phone, fax, contact person details for both the maker itself and its local agent. Soft-deleted via `mk_del`. Referenced by equipment via `a_equips.mat_mk_id`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `mk_id`. | [[htdocs/base/api_equip.php]]<br>[[Authorization master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Authorization master]]<br>[[Maker master]] |
| `mk_id` | varchar(16) NOT NULL | Maker ID (business PK). Binary collation for case-sensitive matching. | [[htdocs/base/api_equip.php]]<br>[[Authorization master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Authorization master]]<br>[[Maker master]] |
| `mk_name` | varchar(64) | Maker display name. | [[htdocs/base/api_equip.php]]<br>[[Authorization master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Authorization master]]<br>[[Maker master]] |
| `mk_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique maker row ID. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `disporder` | smallint(6) | Sort order. | [[htdocs/base/api_equip.php]]<br>[[Authorization master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Authorization master]]<br>[[Maker master]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_equip.php]]<br>[[Authorization master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Authorization master]]<br>[[Maker master]] |
| `modify_date` | datetime | Datetime of last modification. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Authorization master]]<br>[[Maker master]] |
| `create_date` | datetime | Datetime of row creation. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Authorization master]]<br>[[Maker master]] |
| `mk_phone` | varchar(32) | Maker direct phone number. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_fax` | varchar(32) | Maker direct fax number. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_tanto` | varchar(32) | Maker contact person name (担当). | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_tanto_phone` | varchar(32) | Maker contact person phone number. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_ag_phone` | varchar(32) | Agent (代理店) phone number. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_ag_fax` | varchar(32) | Agent fax number. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_ag_tanto_phone` | varchar(32) | Agent contact person phone number. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_ag_tanto` | varchar(32) | Agent contact person name. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_ag_name` | varchar(32) | Agent company name. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_kana` | varchar(64) | Maker name kana reading (for search/sort). | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_del` | tinyint(1) | Soft-delete flag. `1` = hidden from selection lists. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |
| `mk_mail` | varchar(64) | Maker email address. | [[htdocs/base/api_equip.php]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Maker master]]<br>[[Maintenance work page]]<br>[[Stock management]] | [[Maker master]] |

---

## a_mtbf

**Purpose:** MTBF / MTTR reliability metrics. Stores pre-calculated Mean Time Between Failures (MTBF) and Mean Time To Repair (MTTR) statistics per equipment per fiscal term. Includes total time, downtime, live time, failure count, and the analysis period (`startdate`/`enddate`). Keyed by equipment ID + fiscal term ID.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. |  |  |
| `eq_id` | int(11) NOT NULL | Equipment ID (app-FK → `a_equips.eq_id`). Part of composite PK. |  |  |
| `ft_id` | smallint(6) NOT NULL | Fiscal term ID. Part of composite PK. |  |  |
| `ttltime` | int(11) | Total available time in the period (minutes). |  |  |
| `downtime` | int(11) | Total downtime in the period (minutes). |  |  |
| `livetime` | int(11) | Total uptime / live time in the period (minutes). |  |  |
| `fails` | int(11) | Number of failures in the period. |  |  |
| `mtbf` | int(11) | Mean Time Between Failures (calculated, minutes). |  |  |
| `mttr` | int(11) | Mean Time To Repair (calculated, minutes). |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `startdate` | datetime | Analysis period start date. |  |  |
| `enddate` | datetime | Analysis period end date. |  |  |

---

## a_rent

**Purpose:** Equipment rental / loan record. Tracks equipment lending with reservation period (`start_date`/`end_date`), borrower info (staff, email, phone, section, purpose), approval workflow (`auth_stf`, `auth_date`, `rt_auth_stat`), and return reminder settings. `rent_stat` tracks lifecycle status; `is_rent` flags currently-rented state.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `eq_id` | int(11) NOT NULL | Equipment ID (app-FK → `a_equips.eq_id`). Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `start_date` | datetime NOT NULL | Rental start date/time. Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `end_date` | datetime | Rental end / return date. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `modify_date` | datetime | Datetime of last modification. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `create_date` | datetime | Datetime of row creation. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `rent_id` | int(11) | Rental sequence ID. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `rent_stat` | char(1) | Rental lifecycle status code (`'0'` = default). | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `create_stf` | smallint(6) | Staff ID who created the rental request. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `auth_stf` | smallint(6) | Staff ID of the approver. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `auth_date` | datetime | Approval decision datetime. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rt_auth_stat` | smallint(6) | Approval status code (e.g. pending, approved, rejected). | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |
| `rent_stf` | smallint(6) | Staff ID of the borrower. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rent_mail` | varchar(64) | Borrower email address. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rent_phone` | varchar(32) | Borrower phone number. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rent_sec` | varchar(32) | Borrower section / department. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rent_purpose` | varchar(32) | Purpose of the rental. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rent_memo` | varchar(255) | Free-form memo / notes about the rental. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rent_ngres` | char(1) | NG (rejection) reason code. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `rent_remday` | smallint(6) | Number of days before return to send a reminder. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |  |
| `is_rent` | tinyint(1) | `1` = equipment is currently rented out. | [[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] | [[htdocs/base/api_rent_update.php]]<br>[[htdocs/base/rent.php]] |

---

## a_stocks

**Purpose:** Stock / spare parts inventory master. One row per stock item per factory. Tracks current quantity (`stk_num`), safety stock level, warning threshold, stock classification, shelf location (`stk_tana`), and associated equipment. `stk_eq_names` stores a blob of linked equipment names for display. Keyed by item code + factory.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `hin_id` | varchar(100) NOT NULL | Stock item code (business PK). Part of composite PK. | [[Equipment page]]<br>[[Stock management]] | [[Stock management]] |
| `hin_name` | varchar(256) | Stock item display name. | [[Equipment page]]<br>[[Stock management]] | [[Stock management]] |
| `stk_num` | float | Current stock quantity on hand. | [[Equipment page]]<br>[[Stock management]] | [[Stock management]] |
| `stk_num_safe` | float | Safety stock level (reorder trigger). | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `stk_num_warn` | float | Warning threshold level. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `stock_kbn` | char(1) | Stock classification code. | [[Equipment page]]<br>[[Stock management]] | [[Stock management]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `modify_date` | datetime | Datetime of last modification. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `create_date` | datetime | Datetime of row creation. | [[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `stk_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique stock row ID. | [[Stock management]] | [[Stock management]] |
| `fc_id` | varchar(16) NOT NULL | Factory code (app-FK → `a_factory.fc_id`). Part of composite PK. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `stk_date` | date | Last stock update date. | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). | [[Equipment page]]<br>[[Stock management]] | [[Equipment page]]<br>[[Stock management]] |
| `mak_name` | varchar(32) | Maker / manufacturer name (denormalised). | [[Stock management]] | [[Stock management]] |
| `stk_file1` | varchar(64) | Attached file path (e.g. photo, datasheet). | [[Stock management]] | [[Stock management]] |
| `stk_start` | int(11) | Starting stock count (for period-based tracking). | [[Stock management]] | [[Stock management]] |
| `stk_time` | datetime | Last stock movement datetime. | [[Stock management]] | [[Stock management]] |
| `stk_tank` | int(11) | Tank / container unit count. | [[Stock management]] | [[Stock management]] |
| `stk_tana` | varchar(32) | Shelf / storage location label. | [[Equipment page]]<br>[[Stock management]] | [[Stock management]] |
| `stk_eq_id` | int(11) | Primary associated equipment ID. | [[Stock management]] | [[Stock management]] |
| `stk_eq_names` | blob | Blob of linked equipment names for display. | [[Stock management]] | [[Stock management]] |

---

## a_tana

**Purpose:** Physical inventory / stocktaking (棚卸) record. One row per equipment scan per fiscal year (`nendo`). Records physical verification of equipment existence: scan code, read date, area, team (`tana_kumi`), staff, matching flags for model number and team verification. `m_eq_id` links to the matched equipment master. Supports RFID via `epc` column.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `s_code` | varchar(32) NOT NULL | Scan / equipment code read during stocktaking. Part of composite PK. | [[htdocs/base/tana.php]] |  |
| `read_date` | date | Date when the physical scan was performed. | [[htdocs/base/tana.php]] |  |
| `s_name` | varchar(32) | Scanned item name. | [[htdocs/base/tana.php]] |  |
| `s_area` | varchar(32) | Area where the item was found. | [[htdocs/base/tana.php]] |  |
| `tana_kumi` | varchar(32) | Stocktaking team / group name. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `s_sect` | varchar(32) | Section where the item was scanned. | [[htdocs/base/tana.php]] |  |
| `s_stf` | varchar(16) | Staff identifier who performed the scan. | [[htdocs/base/tana.php]] |  |
| `s_cmt` | varchar(64) | Comment / notes from the scanner. | [[htdocs/base/tana.php]] |  |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `modify_date` | datetime | Datetime of last modification. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `create_date` | datetime | Datetime of row creation. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `create_stf` | smallint(6) | Staff ID who created the record. | [[htdocs/base/tana.php]] |  |
| `stf_id` | smallint(6) | Staff ID of last editor. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `read_flg` | tinyint(1) | `1` = scan has been read / processed. | [[htdocs/base/tana.php]] |  |
| `mat_mcno_ok` | tinyint(1) | `1` = model number matches the equipment master. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `kumi_ok` | tinyint(1) | `1` = team assignment verified. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `tgt_flg` | tinyint(1) | `1` = item is a stocktaking target. | [[htdocs/base/tana.php]] |  |
| `m_eqv12` | varchar(32) | Matched equipment custom field value (snapshot). | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `m_mat_nensiki` | varchar(8) | Matched equipment year of manufacture (snapshot). | [[htdocs/base/tana.php]] |  |
| `m_eq_id` | int(11) | Matched equipment ID (app-FK → `a_equips.eq_id`). | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `nendo` | varchar(6) NOT NULL | Fiscal year code (e.g. `'2024'`). Part of composite PK. | [[htdocs/base/tana.php]] |  |
| `upload_time` | int(11) | Unix timestamp of data upload. | [[htdocs/base/tana.php]] |  |
| `epc` | varchar(32) | RFID EPC (Electronic Product Code) tag value. | [[htdocs/base/tana.php]] |  |
| `a_stf` | varchar(16) | Approver / auditor staff identifier. | [[htdocs/base/tana.php]] |  |
| `stf_ok` | char(1) | Staff verification status flag. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `m_adm` | varchar(16) | Matched administrator identifier. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `tck` | tinyint(1) | Check flag (e.g. double-check completed). | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `ck_stf` | smallint(6) | Staff ID who performed the check. | [[htdocs/base/tana.php]] | [[htdocs/base/tana.php]] |
| `tn_sid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique stocktaking row ID. | [[htdocs/base/tana.php]] |  |
| `s_area_flg` | tinyint(1) | `1` = area match verified. | [[htdocs/base/tana.php]] |  |

---

## ads_master

**Purpose:** Tenant-scoped dropdown value master. Unlike `datamaster` (which is global/shared), `ads_master` stores per-tenant dropdown lists keyed by `bkid` + `propid` + `itid`. Items can be hidden via `ishidden`. Used for tenant-customisable classification codes (e.g. work types, part categories).

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `propid` | varchar(16) NOT NULL | Property / list identifier (e.g. `'work_type'`). Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `itid` | smallint(6) NOT NULL | Item code within the list. Part of composite PK. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `itname` | varchar(32) | Item display name. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `disporder` | smallint(6) | Sort order. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `ishidden` | tinyint(1) | `1` = hidden from dropdown selections. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `uptime` | int(11) | Unix timestamp of last update. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `stf_id` | smallint(6) NOT NULL | Staff ID of last editor. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `create_date` | datetime | Datetime of row creation. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |
| `modify_date` | datetime | Datetime of last modification. | [[htdocs/base/api_equip.php]]<br>[[htdocs/base/api_rent.php]]<br>[[htdocs/base/api_rent_update.php]]<br>[[Equipment group master]]<br>[[Equipment item master]]<br>[[Equipment page]]<br>[[Factory and location master]]<br>[[Mail template master]]<br>[[htdocs/base/mt_master.php]]<br>[[Maintenance work page]]<br>[[htdocs/base/mtres.php]]<br>[[Maintenance results list]]<br>[[htdocs/base/rent.php]]<br>[[Schedule calendar]]<br>[[Staff management]]<br>[[Stock management]]<br>[[htdocs/base/tana.php]] | [[htdocs/base/mt_master.php]] |

---

## bk_idmaster

**Purpose:** Application-level serial ID allocator. Stores the next available ID value for various entity types per tenant. `p_name` identifies the entity type (e.g. `'stf_id'`, `'eqg_id'`, `'eq_id'`), and `p_val` holds the next value to assign. Incremented atomically when creating new records.

| Column   | Type                 | Purpose                                                              | Read by base PHP            | Written by base PHP         |
| -------- | -------------------- | -------------------------------------------------------------------- | --------------------------- | --------------------------- |
| `bkid`   | int(11) NOT NULL     | Tenant partition key. Composite PK with `p_name`.                    | [[Equipment group master]] | [[Equipment group master]] |
| `p_name` | varchar(16) NOT NULL | Entity type name (e.g. `'stf_id'`, `'eq_id'`). Part of composite PK. | [[Equipment group master]] |                             |
| `p_val`  | int(11)              | Next available ID value to assign.                                   | [[Equipment group master]] | [[Equipment group master]] |
| `uptime` | int(11)              | Unix timestamp of last update.                                       | [[Equipment group master]] | [[Equipment group master]] |

---

## busareas

**Purpose:** Business company ↔ area assignment. Legacy MyISAM table linking tenant companies (`bcid`) to area codes. Used in the `zaikodb` admin context for geographic grouping of tenants. No primary key defined.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bcid` | int(11) | Business company ID (app-FK → `buscomps.bcid`). |  |  |
| `areaid` | smallint(6) | Area code for geographic grouping. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |

---

## bustypengdays

**Purpose:** Business type NG (no-good) days. Legacy MyISAM table recording unavailable/blocked dates per business type. `daisu` stores a count (e.g. number of units affected). Used in scheduling/capacity planning contexts.

| Column   | Type                 | Purpose                                                               | Read by base PHP | Written by base PHP |
| -------- | -------------------- | --------------------------------------------------------------------- | ---------------- | ------------------- |
| `bcid`   | int(11) NOT NULL     | Business company ID (app-FK → `buscomps.bcid`). Part of composite PK. |                  |                     |
| `ngdate` | date NOT NULL        | Blocked / unavailable date. Part of composite PK.                     |                  |                     |
| `btype`  | smallint(6) NOT NULL | Business type code. Part of composite PK.                             |                  |                     |
| `daisu`  | smallint(6)          | Count of affected units on this date.                                 |                  |                     |
| `uptime` | int(11)              | Unix timestamp of last update.                                        |                  |                     |

---

## calendars

**Purpose:** Tenant-scoped calendar events / notes. Stores per-date comments for each tenant. Used alongside `holidays` (system-wide) to annotate the schedule calendar with tenant-specific notes. Keyed by tenant + date.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | int(11) NOT NULL | Tenant partition key. Composite PK with `date`. |  |  |
| `date` | date NOT NULL | Calendar date. Part of composite PK. |  |  |
| `cmt` | varchar(128) | Comment / note for this date. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |

---

## loginhist

**Purpose:** Login / logout audit trail. Records every authentication event with timestamp, remote IP (`rip`), staff ID, and whether the access was via API (`is_api`). `logout` distinguishes login vs. logout events. Used for security audit and the login history page.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `histid` | bigint(20) unsigned AUTO_INCREMENT | Auto-increment primary key. |  |  |
| `bkid` | smallint(6) | Tenant partition key. |  |  |
| `stf_id` | smallint(6) | Staff ID of the user who logged in/out. |  |  |
| `logout` | char(1) | `'0'` = login event; `'1'` = logout event. |  |  |
| `acstime` | datetime | Timestamp of the authentication event. |  |  |
| `rip` | varchar(32) | Remote IP address of the client. |  |  |
| `is_api` | tinyint(1) | `1` = access was via API; `0` = normal web login. |  |  |

---

## mail_master

**Purpose:** Mail recipient master. Stores email addresses for notification recipients per tenant, linked to a worker ID (`sya_id` → `syain_master`). Used when sending maintenance/rental notifications to build the recipient list. Items can be hidden via `ishidden`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `email`. |  |  |
| `email` | varchar(64) NOT NULL | Recipient email address (business PK). |  |  |
| `sya_id` | varchar(16) | Worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `sya_name` | varchar(16) | Worker name (denormalised for display). |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `create_stf` | smallint(6) | Staff ID who created the record. |  |  |
| `ishidden` | tinyint(1) | `1` = hidden from recipient selection lists. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |

---

## myview

**Purpose:** User-bookmarked schedule instances. Allows a staff member to "star" or bookmark specific maintenance schedule entries (`mts_uid` → `a_mtsch`) for quick access in their personal view. Each row links a schedule instance to the staff member who bookmarked it.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `mts_uid`. |  |  |
| `mts_uid` | int(11) NOT NULL | Schedule instance ID (app-FK → `a_mtsch.mts_uid`). Part of composite PK. |  |  |
| `stf_id` | smallint(6) | Staff ID who bookmarked this schedule entry. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `name` | varchar(32) | User-assigned label / name for the bookmark. |  |  |

---

## p_item

**Purpose:** Procurement item master (CAPEX module). Defines purchasable items with unit price, total price, slip number (`den_ban`), accounting category (`keiri_kbn`), kind, purchase order number, and linked approval number (`rin_ban`). Tracks in/out quantities, up to two maker references, and associated asset IDs. Part of the project-based procurement workflow.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `item_id`. |  |  |
| `p_name` | varchar(64) | Item name (procurement context). |  |  |
| `p_tanka` | bigint(20) | Unit price. |  |  |
| `p_price` | bigint(20) | Total price. |  |  |
| `den_ban` | varchar(32) | Slip / voucher number (伝票番号). |  |  |
| `keiri_kbn` | varchar(4) | Accounting category code (経理区分). |  |  |
| `p_kind` | varchar(4) | Item kind / classification code. |  |  |
| `p_num` | varchar(16) | Purchase order number reference. |  |  |
| `rin_ban` | varchar(32) | Approval / ringi number reference. |  |  |
| `p_date` | date | Procurement date. |  |  |
| `mk_id` | varchar(16) | Primary maker ID (app-FK → `a_maker.mk_id`). |  |  |
| `fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). |  |  |
| `sya_id` | varchar(16) | Responsible worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `modify_sid` | varchar(16) | Worker ID who last modified. |  |  |
| `create_sid` | varchar(16) | Worker ID who created the record. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `in_num` | smallint(6) | Incoming / received quantity. |  |  |
| `out_num` | smallint(6) | Outgoing / issued quantity. |  |  |
| `item_id` | int(11) NOT NULL | Item ID (business PK). Part of composite PK. |  |  |
| `item_name` | varchar(64) | Item name (item context). |  |  |
| `stf_id` | varchar(16) | Staff / worker identifier of last editor. |  |  |
| `item_bikou` | varchar(255) | Item remarks / notes. |  |  |
| `item_unit` | varchar(2) | Unit of measure (e.g. `'個'`, `'本'`). |  |  |
| `partkbn` | varchar(4) | Part classification code. |  |  |
| `rem_num` | smallint(6) | Remaining quantity. |  |  |
| `acd` | smallint(6) | Asset classification code. |  |  |
| `pi_del` | tinyint(1) | Soft-delete flag. `1` = deleted. |  |  |
| `mk_id1` | varchar(16) | Secondary maker ID reference 1. |  |  |
| `mk_id2` | varchar(16) | Secondary maker ID reference 2. |  |  |
| `cost1` | bigint(20) | Cost amount 1 (e.g. maker 1 quote). |  |  |
| `cost2` | bigint(20) | Cost amount 2 (e.g. maker 2 quote). |  |  |
| `siyousaki` | varchar(64) | Usage destination / installation location. |  |  |
| `si_ids` | varchar(255) | Comma-separated asset IDs linked to this item. |  |  |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). |  |  |

---

## p_mente

**Purpose:** Project maintenance record (CAPEX module). Tracks maintenance/repair events linked to projects (`p_id`/`pp_id`) and assets (`si_id`). Stores date, cost, maker, work content (`pm_naiyou`), remarks, and up to two attached files. Cross-references asset numbers, approval numbers, and purchase order numbers.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `pm_id`. |  |  |
| `pm_id` | int(11) NOT NULL | Maintenance record ID (business PK). |  |  |
| `p_id` | int(11) | Project ID (app-FK → `p_proj.prj_id`). |  |  |
| `pp_id` | int(11) | Sub-project / parent project ID. |  |  |
| `pm_date` | date | Maintenance work date. |  |  |
| `pm_price` | int(11) | Maintenance cost. |  |  |
| `mk_id` | varchar(16) | Maker ID (app-FK → `a_maker.mk_id`). |  |  |
| `pm_naiyou` | text | Work content / description (内容). |  |  |
| `pm_bikou` | text | Remarks / notes. |  |  |
| `sya_id` | varchar(16) | Responsible worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `modify_sid` | varchar(16) | Worker ID who last modified. |  |  |
| `create_sid` | varchar(16) | Worker ID who created the record. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `si_id` | int(11) | Asset ID (app-FK → `p_sisan.si_id`). |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `m_file1` | varchar(64) | Attached file path 1. |  |  |
| `m_file2` | varchar(64) | Attached file path 2. |  |  |
| `pm_del` | tinyint(1) | Soft-delete flag. `1` = deleted. |  |  |
| `pm_mdate` | date | Planned maintenance date. |  |  |
| `r_si_bans` | varchar(32) | Cross-referenced asset numbers (comma-separated). |  |  |
| `r_rin_bans` | varchar(32) | Cross-referenced approval numbers (comma-separated). |  |  |
| `r_p_nums` | varchar(32) | Cross-referenced purchase order numbers (comma-separated). |  |  |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). |  |  |

---

## p_proj

**Purpose:** Project master (CAPEX module). Top-level entity for capital expenditure projects. Stores project name, budget, factory, responsible worker, and aggregated counts for linked approvals (`rin_num`/`rin_fin`), purchases (`p_qty`/`p_fin`), and assets. Contains denormalised summary text blobs for approvals, purchases, and assets for display. Soft-deleted via `is_del`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `prj_id`. |  |  |
| `prj_id` | int(11) NOT NULL | Project ID (business PK). |  |  |
| `prj_name` | varchar(64) | Project display name. |  |  |
| `prj_price` | bigint(20) | Project budget amount. |  |  |
| `fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). |  |  |
| `sya_id` | varchar(16) | Responsible worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `modify_sid` | varchar(16) | Worker ID who last modified. |  |  |
| `create_sid` | varchar(16) | Worker ID who created the record. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `prj_date` | date | Project date (start or registration). |  |  |
| `is_del` | tinyint(1) | Soft-delete flag. `1` = deleted. |  |  |
| `dm1`–`dm5` | varchar(32) ×5 | Custom dimension fields 1–5. |  |  |
| `ringi` | text | Denormalised approval summary blob for display. |  |  |
| `purchase` | text | Denormalised purchase order summary blob for display. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `rin_num` | smallint(6) | Total number of linked approval requests. |  |  |
| `rin_fin` | smallint(6) | Number of completed / settled approvals. |  |  |
| `rin_qty` | smallint(6) | Approval quantity count. |  |  |
| `p_qty` | smallint(6) | Total number of linked purchase orders. |  |  |
| `p_fin` | smallint(6) | Number of completed / delivered purchases. |  |  |
| `sisan` | text | Denormalised asset summary blob for display. |  |  |
| `sisan_txt` | varchar(255) | Asset summary text (short form). |  |  |
| `purchase_txt` | varchar(512) | Purchase summary text. |  |  |
| `ringi_txt` | varchar(255) | Approval summary text. |  |  |
| `pb_qty` | smallint(6) | Number of linked procurement items. |  |  |
| `k_qty` | smallint(6) | Number of linked inspections / checks. |  |  |
| `si_qty` | smallint(6) | Number of linked fixed assets. |  |  |
| `r_file` | tinyint(1) | `1` = has attached ringi files. |  |  |
| `p_file` | tinyint(1) | `1` = has attached purchase files. |  |  |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). |  |  |
| `nouhin_fin` | smallint(6) | Number of deliveries completed. |  |  |
| `rmk_txt` | varchar(255) | Remarks summary text. |  |  |
| `siban_txt` | varchar(512) | Asset number summary text. |  |  |
| `p_price` | bigint(20) | Actual total expenditure amount. |  |  |

---

## p_purchase

**Purpose:** Purchase order (CAPEX module). Represents a purchase order linked to a project. Stores order number (`p_num`), date, description, total price, maker, factory, worker, delivery info, and up to five attached files. Tracks delivery status (`nouhin_flg`), inspection date, payment conditions, and linked approval number. Soft-deleted via `p_del`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `p_num`. |  |  |
| `p_id` | bigint(20) unsigned AUTO_INCREMENT | Globally unique purchase order row ID. |  |  |
| `p_num` | varchar(16) NOT NULL | Purchase order number (business PK). |  |  |
| `prj_id` | int(11) | Project ID (app-FK → `p_proj.prj_id`). |  |  |
| `p_date` | date | Order date. |  |  |
| `p_name` | varchar(64) | Order description / name. |  |  |
| `p_price` | bigint(20) | Total order price. |  |  |
| `mk_id` | varchar(16) | Maker / vendor ID (app-FK → `a_maker.mk_id`). |  |  |
| `fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). |  |  |
| `sya_id` | varchar(16) | Responsible worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `modify_sid` | varchar(16) | Worker ID who last modified. |  |  |
| `create_sid` | varchar(16) | Worker ID who created the record. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `d_date` | date | Delivery date. |  |  |
| `k_date` | date | Inspection / acceptance date. |  |  |
| `dai_id` | int(11) | Ledger / slip sequence ID. |  |  |
| `sis_id` | varchar(16) | Asset link ID. |  |  |
| `sis_num` | varchar(16) | Asset number. |  |  |
| `rin_ban` | varchar(16) | Linked approval / ringi number (app-FK → `p_ringi.rin_ban`). |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `paycon` | smallint(6) | Payment condition code. |  |  |
| `p_file1`–`p_file5` | varchar(64) ×5 | Attached file paths (5 slots). |  |  |
| `p_bikou` | text | Remarks / notes. |  |  |
| `p_reason` | text | Purchase reason / justification. |  |  |
| `in_date` | date | Goods receipt date. |  |  |
| `in_acd` | smallint(6) | Receipt asset classification code. |  |  |
| `bunai_rin` | char(1) | Internal approval flag. |  |  |
| `s_date` | date | Supplier / shipment date. |  |  |
| `s_name` | varchar(64) | Supplier name. |  |  |
| `s_bikou` | text | Supplier remarks. |  |  |
| `s_lot` | varchar(32) | Lot number. |  |  |
| `pk_date` | date | Packing / preparation date. |  |  |
| `ck_flg` | tinyint(1) | Check / verification flag. |  |  |
| `p_del` | tinyint(1) | Soft-delete flag. `1` = deleted. |  |  |
| `eq_id` | int(11) | Equipment ID (app-FK → `a_equips.eq_id`). |  |  |
| `nouhin_flg` | tinyint(1) | `1` = delivery completed. |  |  |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). |  |  |

---

## p_puritem

**Purpose:** Purchase order line item (CAPEX module). Detail rows for a purchase order (`p_num`), keyed by line number (`gyo_no`). Each line has a description (`p_con`), unit price (`p_tan`), quantity, total amount, and unit of measure.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. |  |  |
| `p_num` | varchar(16) NOT NULL | Purchase order number (app-FK → `p_purchase.p_num`). Part of composite PK. |  |  |
| `gyo_no` | smallint(6) NOT NULL | Line number within the purchase order. Part of composite PK. |  |  |
| `p_ttl` | bigint(20) | Line total amount. |  |  |
| `p_qty` | int(11) | Line quantity. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `p_con` | varchar(64) | Line item description / content. |  |  |
| `p_tan` | bigint(20) | Unit price. |  |  |
| `p_uni` | varchar(4) | Unit of measure. |  |  |

---

## p_ringi

**Purpose:** Approval request / ringi (稟議) (CAPEX module). Represents a formal approval request for capital expenditure linked to a project. Stores approval number (`rin_ban`), date, title, requested amount, accounting details, settlement info, up to nine attached files, plan/actual dates, reason, remarks, and financial metrics (payback period `kaisyu_y`, NPV, IRR). Soft-deleted via `r_del`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `rin_ban`. |  |  |
| `rin_id` | int(11) | Ringi sequence ID (globally unique). |  |  |
| `rin_ban` | varchar(16) NOT NULL | Approval / ringi number (business PK). |  |  |
| `rin_date` | date | Approval request date. |  |  |
| `rin_kbn` | char(1) | Approval type classification code. |  |  |
| `rin_title` | varchar(64) | Approval request title. |  |  |
| `rin_price` | bigint(20) | Requested amount. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). |  |  |
| `modify_sid` | varchar(16) | Worker ID who last modified. |  |  |
| `create_sid` | varchar(16) | Worker ID who created the record. |  |  |
| `sya_id` | varchar(16) | Responsible worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `prj_id` | int(11) | Project ID (app-FK → `p_proj.prj_id`). |  |  |
| `rin_kei` | int(11) | Accounting category / account code (経理). |  |  |
| `is_del` | tinyint(1) | Legacy soft-delete flag. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `rin_sagaku` | bigint(20) | Difference / variance amount (差額). |  |  |
| `rin_est` | bigint(20) | Estimated amount. |  |  |
| `stdno` | smallint(6) | Standard number / reference code. |  |  |
| `se_id` | smallint(6) | Settlement ID. |  |  |
| `setres` | smallint(6) | Settlement result code. |  |  |
| `paycon` | smallint(6) | Payment condition code. |  |  |
| `r_file1`–`r_file9` | varchar(128) ×9 | Attached file paths (9 slots). |  |  |
| `se_date` | date | Settlement date. |  |  |
| `rin_plan` | char(1) | Plan status flag. |  |  |
| `do_date` | date | Execution / action date. |  |  |
| `plan_date` | date | Planned completion date. |  |  |
| `rin_reason` | text | Approval reason / justification. |  |  |
| `rin_bikou` | text | Remarks / notes. |  |  |
| `yos_id` | varchar(16) | Budget code reference. |  |  |
| `rmk_id` | varchar(16) | Remark category ID. |  |  |
| `r_del` | tinyint(1) | Soft-delete flag. `1` = deleted. |  |  |
| `plan_keizoku` | tinyint(1) | `1` = plan continues into next period. |  |  |
| `kaisyu_y` | float | Payback period in years (回収年). |  |  |
| `npv` | float | Net Present Value. |  |  |
| `irr` | float | Internal Rate of Return. |  |  |

---

## p_rinitem

**Purpose:** Approval request line item (CAPEX module). Detail rows for a ringi (`rin_ban`), keyed by line number (`gyo_no`). Each line has a maker reference, payment amount, estimate amount, asset code, warranty info, subject, and special notes.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Part of composite PK. |  |  |
| `rin_ban` | varchar(16) NOT NULL | Approval / ringi number (app-FK → `p_ringi.rin_ban`). Part of composite PK. |  |  |
| `gyo_no` | smallint(6) NOT NULL | Line number within the ringi. Part of composite PK. |  |  |
| `mk_id` | varchar(16) | Maker / vendor ID (app-FK → `a_maker.mk_id`). |  |  |
| `p_pay` | int(11) | Payment amount for this line. |  |  |
| `p_est` | int(11) | Estimate amount for this line. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `p_ass` | varchar(32) | Asset code. |  |  |
| `p_war` | varchar(32) | Warranty information. |  |  |
| `p_sbj` | varchar(32) | Subject / item name. |  |  |
| `p_tok` | varchar(64) | Special notes / terms (特記). |  |  |

---

## p_sisan

**Purpose:** Fixed asset register (CAPEX module). Tracks individual fixed assets with asset number (`si_ban`), name, price, slip number, accounting category, purchase order reference, project link, status, delivery vendor, equipment classification, type ID, serial number, specs, disposal info, and useful life period. Can be linked to equipment master via `eq_id`. Soft-deleted via `si_del`.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `si_id`. |  |  |
| `si_id` | int(11) NOT NULL | Asset sequence ID (business PK). |  |  |
| `p_name` | varchar(64) | Asset name. |  |  |
| `p_tanka` | int(11) | Unit price. |  |  |
| `p_price` | int(11) | Total price / book value. |  |  |
| `den_ban` | varchar(32) | Slip / voucher number (伝票番号). |  |  |
| `keiri_kbn` | varchar(4) | Accounting category code (経理区分). |  |  |
| `p_kind` | varchar(4) | Asset kind / classification code. |  |  |
| `p_num` | varchar(16) | Purchase order number reference (app-FK → `p_purchase.p_num`). |  |  |
| `rin_ban` | varchar(32) | Approval / ringi number reference. |  |  |
| `fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). |  |  |
| `sya_id` | varchar(16) | Responsible worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `modify_sid` | varchar(16) | Worker ID who last modified. |  |  |
| `create_sid` | varchar(16) | Worker ID who created the record. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `prj_id` | int(11) | Project ID (app-FK → `p_proj.prj_id`). |  |  |
| `si_ban` | varchar(32) | Asset number / tag (資産番号). |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `si_stat` | smallint(6) | Asset status code (e.g. active, disposed). |  |  |
| `dai_ban` | varchar(16) | Ledger number. |  |  |
| `d_kind` | char(1) | Disposition kind code. |  |  |
| `si_date` | date | Asset registration / acquisition date. |  |  |
| `nou_tan` | varchar(16) | Delivery vendor contact / person. |  |  |
| `eqkbn` | smallint(6) | Equipment classification code. |  |  |
| `acd1` | smallint(6) | Asset classification code 1. |  |  |
| `acd2` | smallint(6) | Asset classification code 2. |  |  |
| `typeid` | varchar(64) | Type / model identifier. |  |  |
| `serid` | varchar(64) | Serial number. |  |  |
| `spec` | text | Specifications / technical details. |  |  |
| `si_bikou` | text | Remarks / notes. |  |  |
| `smk_id` | varchar(16) | Sub-maker / secondary vendor ID. |  |  |
| `nou_sya_id` | varchar(16) | Delivery worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `si_del` | tinyint(1) | Soft-delete flag. `1` = deleted. |  |  |
| `ps_date` | date | Useful life period start date. |  |  |
| `pe_date` | date | Useful life period end date. |  |  |
| `disposal` | varchar(64) | Disposal information (method, date). |  |  |
| `p_dai_ban` | varchar(16) | Parent ledger number. |  |  |
| `eq_id` | int(11) | Equipment ID (app-FK → `a_equips.eq_id`). Links asset to equipment master. |  |  |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). |  |  |

---

## p_sisancode

**Purpose:** Asset code / tag master (CAPEX module). Keyed by asset number (`si_ban`), stores physical asset tag data: RFID tag, responsible worker, attached file, physical inventory date (`tana_date`), factory assignment, and actual count. Used for asset stocktaking / physical verification.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `si_ban`. |  |  |
| `si_ban` | varchar(32) NOT NULL | Asset number / tag (business PK). |  |  |
| `sya_id` | varchar(16) | Responsible worker ID (app-FK → `syain_master.sya_id`). |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `modify_sid` | varchar(16) | Worker ID who last modified. |  |  |
| `create_sid` | varchar(16) | Worker ID who created the record. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `sic_id` | int(11) | Asset code sequence ID. |  |  |
| `s_file1` | varchar(64) | Attached file path (e.g. photo of asset tag). |  |  |
| `sic_date` | date | Asset code registration date. |  |  |
| `actual` | smallint(6) | Actual physical count during stocktaking. |  |  |
| `rfid` | varchar(32) | RFID tag value. |  |  |
| `tana_date` | date | Last physical inventory verification date. |  |  |
| `s_fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). |  |  |

---

## staff

**Purpose:** Super-admin staff accounts (zaikodb). Separate from tenant `bk_staff` — these are system-level administrator accounts used to log into `/padmin/`. Minimal schema: login, password, email, permission level, name. Soft-deleted via `is_del`. No `bkid` — not tenant-scoped.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `login` | varchar(16) | Admin login username. |  |  |
| `passwd` | varchar(32) | Admin login password. |  |  |
| `email` | varchar(64) | Admin email address. |  |  |
| `level` | smallint(6) | Permission level (higher = more access). |  |  |
| `name` | varchar(64) | Admin display name. |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `stf_id` | bigint(20) unsigned AUTO_INCREMENT | Auto-increment staff ID (unique key, no PK). |  |  |
| `is_del` | tinyint(1) | Soft-delete flag. `1` = deactivated. |  |  |

---

## syain_master

**Purpose:** Worker / operator master (社員マスター). Stores on-site workers who may not have system login accounts (unlike `bk_staff`). Each worker has a code (`sya_id`), name, factory/line assignment, email, and visibility flags. Referenced by maintenance, procurement, and mail notification modules to identify responsible workers.

| Column | Type | Purpose | Read by base PHP | Written by base PHP |
| --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `sya_id`. |  |  |
| `sya_id` | varchar(16) NOT NULL | Worker code (business PK). |  |  |
| `sya_name` | varchar(16) | Worker display name. |  |  |
| `disporder` | smallint(6) | Sort order for display lists. |  |  |
| `ishidden` | tinyint(1) | `1` = hidden from selection lists. |  |  |
| `fc_id` | varchar(16) | Factory assignment (app-FK → `a_factory.fc_id`). |  |  |
| `line_id` | varchar(8) | Line assignment (app-FK → `a_line.line_id`). |  |  |
| `uptime` | int(11) | Unix timestamp of last update. |  |  |
| `stf_id` | smallint(6) | Staff ID of last editor. |  |  |
| `modify_date` | datetime | Datetime of last modification. |  |  |
| `create_date` | datetime | Datetime of row creation. |  |  |
| `sya_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique worker row ID. |  |  |
| `nodisp` | tinyint(1) | `1` = do not display in UI lists (stronger than `ishidden`). |  |  |
| `sya_mail` | varchar(64) | Worker email address. |  |  |

---
