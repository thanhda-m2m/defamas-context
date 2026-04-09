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

---

## Table index

| Table | Category | Brief purpose |
| --- | --- | --- |
| [[#a_equips]] | Core maintenance | Equipment master |
| [[#a_mtinfo]] | Core maintenance | Maintenance plan |
| [[#a_mtsch]] | Core maintenance | Maintenance schedule instance |
| [[#a_mtres]] | Core maintenance | Maintenance result |
| [[#a_factory]] | Location / org | Factory master |
| [[#a_line]] | Location / org | Line master |
| [[#holidays]] | Calendar support | Public holiday dates |
| [[#datamaster]] | Calendar support | Generic dropdown value master |
| [[#bk_infos]] | Page widget | Tenant-scoped announcements |
| [[#infos]] | Page widget | System-wide announcements |
| [[#buscomps]] | Auth / session | Tenant registry (zaikodb) |
| [[#bk_staff]] | Auth / session | Tenant staff accounts |
| [[#bkmasters]] | Auth / session | Tenant configuration |
| [[#a_auths]] | Auth / session | Feature permission groups |
| [[#a_eqgroup]] | Auth / session | Equipment group master |
| [[#a_area]] | Location / org | Area master |

---

## a_equips

*Used in: [[Schedule calendar#Core maintenance chain|Schedule calendar]]*

**Purpose:** Equipment master. One row per physical piece of equipment owned by a tenant. Holds the name, factory/line location, equipment group, machine specifications, and up to 16 custom field slots. Soft-deleted via `del_flg` — deleted rows are hidden from all screens but retained in the database.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. All queries filter `WHERE bkid = $this->_bkid`. |
| `eq_id` | int(11) NOT NULL | Equipment ID — integer assigned at creation. Composite PK with `bkid`. |
| `eq_name` | varchar(100) | Equipment display name shown on all screens. |
| `eq_kbn` | smallint(6) | Equipment type classification code. Resolved to label via `datamaster` (`propid='eq_kbn'`). |
| `eq_stat` | smallint(6) | Equipment operational status code. |
| `fc_id` | varchar(16) | Factory code (app-FK → `a_factory.fc_id`). |
| `line_id` | varchar(8) | Line code (app-FK → `a_line.line_id`). |
| `disporder` | smallint(6) | Sort order for display lists. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` | datetime | Datetime of last modification. |
| `create_date` | datetime | Datetime of row creation. |
| `stf_id` | smallint(6) | Staff ID of the last editor. |
| `eqg_id` | smallint(6) | Equipment group ID (app-FK → `a_eqgroup.eqg_id`). Used for the group filter on `sch.php`. |
| `eq_vals` | text | Serialised custom field values (JSON-like). |
| `eq_words` | text | Full-text search keyword blob. |
| `eqv11`–`eqv16` | varchar(32) ×6 | Six additional free-form string custom fields. |
| `del_flg` | tinyint(1) | Soft-delete flag. `1` = deleted and hidden from all pages. |
| `fc_num` | varchar(16) | Factory-internal machine tag / management number. |
| `mat_mcno` | varchar(50) | Manufacturer model number. |
| `mat_nensiki` | varchar(8) | Year of manufacture (e.g. `2019`). |
| `mat_ton` | float | Capacity / tonnage. |
| `mat_mk_id` | varchar(8) | Maker ID (app-FK → `a_maker.mk_id`). |
| `mvdata` | text | Equipment transfer/movement history data. |
| `upload` | tinyint(1) | `1` if the equipment has attached uploaded files. |
| `details` | blob | Rich-text detail / description (HTML blob). |
| `flr_id` | varchar(16) | Floor ID (app-FK → `a_floor.flr_id`). |

---

## a_mtinfo

*Used in: [[Schedule calendar#Core maintenance chain|Schedule calendar]]*

**Purpose:** Maintenance plan. One row per maintenance task attached to an equipment. Stores the task name, type, planned schedule window, cost estimate, and up to 19 attached files. The `sc_kbn` column distinguishes periodic BT tasks from estimate/quotation workflows.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. |
| `mt_id` | int(11) NOT NULL | Maintenance plan ID (business PK). Composite PK with `bkid`. |
| `eq_id` | int(11) NOT NULL | Equipment ID (app-FK → `a_equips.eq_id`). |
| `res_date` | datetime | Registration date of the plan. |
| `mt_name` | varchar(64) | Maintenance task display name. |
| `purpose` | varchar(128) | Purpose / objective of the task. |
| `mt_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique row ID across all tenants. |
| `disporder` | smallint(6) | Sort order. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |
| `ree_date` | datetime | Re-registration / revision date. |
| `eq_file1`–`eq_file19` | varchar(128) ×19 | Attached file paths (19 slots). |
| `mt_detail` | varchar(512) | Detailed description of the maintenance work. |
| `failure_place` | varchar(512) | Location of failure or inspection point. |
| `sc_start_date` | date | Schedule window start date (plan-level). |
| `sc_end_date` | date | Schedule window end date (plan-level). |
| `mtinfo_kbn` | smallint(6) | Maintenance type code. Resolved to label via `datamaster` (`propid='mtinfo_kbn'`). |
| `import_flg` | smallint(6) | Flag marking rows imported from external data. |
| `sc_kbn` | smallint(6) | Schedule category. `0` = periodic BT; other values = estimate/quotation/slip workflow. |
| `mt_cost` | int(11) | Estimated cost (plan-level). |
| `del_flg` | tinyint(1) | Soft-delete flag. |
| `sp_start_date` / `sp_end_date` | datetime | Special period start/end (e.g. extended work window). |
| `failure_date` | date | Date of failure event. |
| `estimate_kbn` | varchar(64) | Estimate type code. |
| `fas_date` / `fae_date` | datetime | FA (field activity) start / end datetimes. |
| `fas2_date` / `fae2_date` | datetime | Secondary FA start / end datetimes. |
| `fail_date` | datetime | Failure occurrence datetime. |
| `mt_stat` | char(1) | Plan status code (`'0'` = default). |
| `main_stfname` | varchar(16) | Denormalised main staff name (snapshot). |
| `sub_stfname` | varchar(128) | Denormalised sub staff names (comma-separated snapshot). |
| `matsuo` | text | Supplemental data blob. |
| `mt_fixtime` | smallint(6) | Fixed time slot allocation. |
| `upload` | tinyint(1) | `1` if plan has uploaded files. |
| `ins_kbn` / `ins_term` / `ins_cat` | smallint / varchar | Inspection type / term / category codes. |
| `ins_data` | text | Inspection data blob. |
| `stat1`–`stat5` | smallint(6) ×5 | Custom status flag codes 1–5. |
| `stats1` / `stats2` | varchar(32) | Custom status string fields. |
| `mtdate1` | date | Plan-level quote request date (copied from `mtr_mtdate1` at plan creation). |
| `dpdate1` | date | Plan-level slip issue date (copied from `mtr_dpdate1` at plan creation). |
| `mmvdata` | blob | Movement data blob. |

---

## a_mtsch

*Used in: [[Schedule calendar#Core maintenance chain|Schedule calendar]]*

**Purpose:** Maintenance schedule instance. One row per scheduled occurrence of a plan (`mt_id` + `sdate`). This is the **single source of truth for all calendar icons** on `sch.php`. Owns the execution window (`s_date`/`e_date`), the completion flag (`mtr_done`), and the four date columns that drive Path B icons.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. |
| `mt_id` | int(11) NOT NULL | Maintenance plan ID (app-FK → `a_mtinfo.mt_id`). Part of composite PK. |
| `sdate` | date NOT NULL | Schedule date (part of composite PK `bkid, mt_id, sdate`). |
| `s_date` | datetime | Schedule window start (datetime precision). Used for Path A date-range matching. |
| `e_date` | datetime | Schedule window end. Used for overdue check: `e_date < today` → yellow background. |
| `mts_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique schedule instance ID. Referenced as FK by `a_mtres.mts_uid`. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |
| `mtr_done` | tinyint(1) DEFAULT 0 | **Completion / result-registered flag. Drives Path A icons:** truthy → ● (result done); falsy + not overdue → ○; falsy + overdue → ○ yellow. |
| `del_flg` | tinyint(1) | Soft-delete flag. |
| `mtr_stat` | char(1) | Schedule status code (`'0'` = default). |
| `upload` | tinyint(1) | `1` if schedule instance has uploaded files. |
| `mtr_mtdate1` | date | **Planned quote request date. Drives △ (Path B priority 4).** Suppressed when `mtr_mtdate2` is set. |
| `mtr_mtdate2` | date | **Actual quote date. Drives ▲ (Path B priority 3).** Setting this suppresses `mtr_mtdate1` in the PHP icon map. |
| `mtr_dpdate1` | date | **Planned slip issue date. Drives □ (Path B priority 2).** Suppressed when `mtr_dpdate2` is set. |
| `mtr_dpdate2` | date | **Actual slip issue date. Drives ■ (Path B priority 1).** Setting this suppresses `mtr_dpdate1` in the PHP icon map. |
| `sendnum` | smallint(6) | Send / notification dispatch counter. |

---

## a_mtres

*Used in: [[Schedule calendar#Core maintenance chain|Schedule calendar]]*

**Purpose:** Maintenance result. One row per completed schedule instance (keyed by `mts_uid`). Stores work details, staff names, actual work dates, costs, and up to 15 attached files. On `sch.php` this table is joined via LEFT JOIN for display data only — it does **not** control any calendar icon.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. |
| `mts_uid` | int(11) NOT NULL | Schedule instance ID (app-FK → `a_mtsch.mts_uid`). Composite PK with `bkid`. |
| `mtr_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique result row ID. |
| `mtr_detail` | varchar(512) | Description of work performed. |
| `mtr_failure` | varchar(512) | Failure / defect description. |
| `mtr_gensyo` | varchar(512) | Symptom description (現象). |
| `mtr_genin` | varchar(512) | Root cause description (原因). |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |
| `mtr_file1`–`mtr_file15` | varchar(128) ×15 | Attached file paths (15 slots). |
| `mtre_date` / `mtrs_date` | datetime | Work end / start datetimes (actual). |
| `mtrs2_date` / `mtre2_date` | datetime | Secondary work period start / end. |
| `fins_date` / `fine_date` | datetime | Final inspection start / end datetimes. |
| `mtr_cost` | int(11) | Actual work cost. |
| `mtr_cost2` | int(11) | Secondary cost field (e.g. parts cost). |
| `del_flg` | tinyint(1) | Soft-delete flag. |
| `mtr_import_flg` | char(1) | Import flag. |
| `mtr_mt_name` | varchar(64) | Denormalised task name (snapshot at result creation time). |
| `mtr_purpose` | varchar(128) | Denormalised purpose (snapshot). |
| `o_s_date` / `o_e_date` | datetime | Original schedule window snapshot (copied from `a_mtsch` at result time). |
| `mtr_stat` | char(1) | Result status code. |
| `mtr_main_stfname` | varchar(16) | Denormalised main staff name. |
| `mtr_sub_stfname` | varchar(128) | Denormalised sub staff names. |
| `mtr_wktime` | int(11) | Actual work time in minutes. |
| `mtr_matsuo` | blob | Supplemental data blob. |
| `mtr_fixtime` | smallint(6) | Fixed time allocation. |
| `upload` | tinyint(1) | `1` if result has uploaded files. |
| `mtr_mitei` | tinyint(1) | Tentative / unconfirmed result flag. |
| `mtr_stat1`–`mtr_stat5` | smallint(6) ×5 | Custom status flag codes 1–5. |
| `mtr_stats1` / `mtr_stats2` / `mtr_stats3` | varchar | Custom status string fields. |
| `mtr_downtime` | int(11) | Equipment downtime caused by this maintenance (minutes). |
| `mtr_estimate_kbn` | varchar(64) | Denormalised estimate type code. |
| `p_mts_uid` | int(11) | Parent schedule UID (for linked / child results). |

---

## a_factory

*Used in: [[Schedule calendar#Location / org hierarchy|Schedule calendar]], [[Authorization master#Location / org hierarchy|Authorization master]]*

**Purpose:** Factory master. Top-level location grouping. Every equipment row has a `fc_id` that is an app-level FK into this table. Used to populate the factory filter dropdown on `sch.php`.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. |
| `fc_id` | varchar(16) NOT NULL | Factory code (business PK). Composite PK with `bkid`. |
| `fc_name` | varchar(64) | Factory display name. |
| `area_id` | smallint(6) | Area ID (app-FK → `a_area.area_id`). |
| `fc_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique factory row ID. |
| `disporder` | smallint(6) | Sort order. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |
| `fc_del` | tinyint(1) | Soft-delete flag. |

---

## a_line

*Used in: [[Schedule calendar#Location / org hierarchy|Schedule calendar]]*

**Purpose:** Line master. Sub-location within a factory. Keyed by `bkid + line_id + fc_id`. Used for the line filter dropdown and shown as a column label in the calendar grid.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. |
| `line_id` | varchar(8) NOT NULL | Line code. Part of composite PK. |
| `fc_id` | varchar(16) NOT NULL | Factory code (app-FK → `a_factory.fc_id`). Part of composite PK. |
| `line_name` | varchar(32) | Line display name. |
| `disporder` | smallint(6) | Sort order. |
| `line_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique line row ID. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |
| `old_names` | text | Historical line name list (for rename audit trail). |
| `line_del` | tinyint(1) | Soft-delete flag. |

---

## holidays

*Used in: [[Schedule calendar#Calendar display support|Schedule calendar]]*

**Purpose:** Public holiday date list. `sch.php` reads this table to colour holiday column headers pink in the calendar grid. Shared across all tenants (no `bkid`).

| Column | Type | Purpose |
| --- | --- | --- |
| `hday` | date NOT NULL | Holiday date (PK). |
| `hname` | varchar(32) | Holiday name (Japanese). |
| `uptime` | int(11) | Unix timestamp of last update. |

---

## datamaster

*Used in: [[Schedule calendar#Calendar display support|Schedule calendar]], [[Authorization master#Calendar display support|Authorization master]]*

**Purpose:** Generic dropdown value master. Stores named item lists keyed by `propid` (e.g. `mtinfo_kbn`, `eq_kbn`) with labels in four languages. Read via `ppes_master()` to resolve integer codes → display names in Japanese, English, Chinese, or Vietnamese. Shared across all tenants (no `bkid`).

| Column | Type | Purpose |
| --- | --- | --- |
| `propid` | varchar(16) NOT NULL | Property / list identifier (e.g. `'mtinfo_kbn'`). Part of composite PK. |
| `itid` | smallint(6) NOT NULL | Item code within the list. Part of composite PK. |
| `itname` | varchar(32) | Japanese display name. |
| `itname_en` | varchar(32) | English display name. |
| `itname_cn` | varchar(32) | Chinese display name. |
| `itname_vn` | varchar(32) | Vietnamese display name. |
| `disporder` | smallint(6) | Sort order. |
| `uptime` | int(11) | Unix timestamp of last update. |

---

## bk_infos

*Used in: [[Schedule calendar#Page widget data|Schedule calendar]]*

**Purpose:** Tenant-scoped announcements. Rows belong to a specific `bkid`. Shown in the **【お知らせ】** info widget on the schedule / TOP page. Supports four-language titles/bodies and optional factory-level visibility restrictions.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `inf_id`. |
| `inf_id` | int(11) NOT NULL | Announcement ID. |
| `inf_title` | varchar(255) | Title (Japanese). |
| `inf_title_en` / `inf_title_cn` / `inf_title_vn` | varchar(255) ×3 | Title in English / Chinese / Vietnamese. |
| `inf_conts` | text | Body content (Japanese). |
| `inf_conts_en` / `inf_conts_cn` / `inf_conts_vn` | text ×3 | Body in English / Chinese / Vietnamese. |
| `inf_date` | date | Published date displayed in the widget. |
| `inf_hidden` | tinyint(1) | `1` = draft / hidden from users. |
| `fc_ids` | varchar(128) | Comma-separated `fc_id` list. When set, announcement is visible only to those factories. Empty = visible to all. |
| `inf_file1`–`inf_file5` | varchar(64) ×5 | Attached file paths. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of creator. |
| `del_flg` | tinyint(1) | Soft-delete flag. |

---

## infos

*Used in: [[Schedule calendar#Page widget data|Schedule calendar]]*

**Purpose:** System-wide announcements published from `/padmin/`. Rendered in the info widget alongside `bk_infos` entries. No `bkid` — visible to all tenants. Managed exclusively by super-admins.

| Column | Type | Purpose |
| --- | --- | --- |
| `inf_id` | int(11) NOT NULL | System announcement ID (PK). |
| `inf_title` | varchar(128) | Announcement title. |
| `inf_conts` | text | Announcement body. |
| `inf_date` | date | Published date. |
| `inf_hidden` | tinyint(1) | `1` = hidden from tenant pages. |
| `is_web` | tinyint(1) | `1` = also show on public website. |
| `web_hidden` | tinyint(1) | `1` = hide from website display. |
| `uptime` | int(11) | Unix timestamp of last update. |

---

## buscomps

*Used in: [[Schedule calendar#Auth / session context|Schedule calendar]], [[Authorization master#Auth / session context|Authorization master]]*

**Purpose:** Tenant registry. Lives in `zaikodb` (the super-admin database). One row per tenant company. Read during login to identify the tenant, verify credentials, check feature flags (`use_api`, `spe_*`, `use_*`), and resolve `bkid`. Managed from `/padmin/`.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | bigint(20) unsigned AUTO_INCREMENT | Tenant ID — globally unique, auto-incremented. This is the `bkid` used in all tenant tables. |
| `bcid` | int(11) | Business company sequence ID. |
| `email` | varchar(64) | Primary contact email. |
| `passwd` | varchar(16) | Admin login password (plain text). |
| `bcname` | varchar(64) | Company display name. |
| `bckana` | varchar(64) | Company name kana reading. |
| `phone` / `fax` | varchar(16) | Contact phone / fax. |
| `pc` | varchar(8) | Postal code. |
| `pref` | smallint(6) | Prefecture code. |
| `addr` | varchar(96) | Address. |
| `bcstat` | char(1) | Company status (`'0'` = active). |
| `bikou` | text | Internal notes. |
| `mng_name` / `mng_sect` | varchar | Manager name / section. |
| `loginid` | varchar(16) | Admin login ID. |
| `subdom` | varchar(8) | Subdomain / tenant URL path code (e.g. `ahihi` for `/{ahihi}/sch.php`). |
| `useplan` | char(1) | Subscription plan code. |
| `startdate` / `enddate` | date | Contract start / end dates. |
| `use_api` | tinyint(1) | API access feature flag. |
| `api_pw` | varchar(32) | API password. |
| `api_ips` | varchar(128) | API IP whitelist (comma-separated). |
| `use_map` | tinyint(1) | Map feature enabled flag. |
| `use_shift` | tinyint(1) | Shift management feature flag. |
| `use_ura` | tinyint(1) | Back-end (裏) feature flag. |
| `use_uacj` / `use_ver2` | tinyint(1) | Additional feature toggle flags. |
| `spe_17_1` / `spe_19_1` / `spe_14_1` | tinyint(1) | Tenant-specific special feature flags. |
| `regtime` | int(11) | Registration Unix timestamp. |
| `cmail` / `cstype` | varchar | CS contact email / support type. |
| `dis_syukei` / `dis_seikyu` | tinyint(1) | Disable aggregate / billing features. |
| `yk_count` / `uk_count` / `car_count` / `ag_count` / `drv_count` | int / smallint | Capacity / licence count limits per feature. |

---

## bk_staff

*Used in: [[Schedule calendar#Auth / session context|Schedule calendar]], [[Authorization master#Auth / session context|Authorization master]]*

**Purpose:** Tenant staff accounts. One row per staff member per tenant. Checked by `aspUser->openUser()` to authenticate the session cookie and resolve `bkid` + `stf_id`. The `lang` column determines the display language for that user.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `stf_id`. |
| `stf_id` | int(11) NOT NULL | Staff ID. |
| `stf_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique staff row ID. |
| `login` | varchar(64) | Login username. |
| `passwd` | varchar(16) | Login password (plain text). |
| `email` | varchar(64) | Staff email address. |
| `name` | varchar(64) | Display name shown in UI. |
| `level` | varchar(4) | Permission level code (used alongside `a_auths`). |
| `lang` | char(1) | Display language: `'0'`=Japanese, `'1'`=English, `'2'`=Chinese, `'3'`=Vietnamese. |
| `fc_id` | varchar(16) | Primary factory assignment. |
| `fc_ids` | varchar(128) | Multi-factory access list (comma-separated `fc_id`). |
| `area_id` | smallint(6) | Area assignment. |
| `sec_name` | varchar(32) | Section / department name. |
| `phone` | varchar(32) | Phone number. |
| `stf_order` | smallint(6) | Display sort order. |
| `sigfile` | varchar(32) | Signature image file path. |
| `is_tbt` | tinyint(1) | TBT super-admin flag. |
| `lastlogin` | int(11) | Unix timestamp of last login. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `bs_delete` | tinyint(1) | Soft-delete flag. |

---

## bkmasters

*Used in: [[Schedule calendar#Auth / session context|Schedule calendar]], [[Authorization master#Auth / session context|Authorization master]]*

**Purpose:** Tenant configuration. One row per `bkid`. Stores company name, working-hour settings, display preferences, billing configuration, and other tenant-level settings used across all pages. Read early in the request lifecycle to apply tenant-specific behaviour.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant ID (PK). |
| `compname` | varchar(64) | Company name displayed in the UI header. |
| `license` | varchar(64) | License identifier. |
| `addr1` / `addr2` | varchar(128) | Company address lines. |
| `pc` | varchar(8) | Postal code. |
| `tel` / `fax` | varchar(16) | Company phone / fax. |
| `pref` | varchar(2) | Prefecture code. |
| `ceo` | varchar(32) | CEO name (for printed documents). |
| `mng1`–`mng3` | varchar(32) ×3 | Manager name slots 1–3. |
| `mng_mail` | varchar(64) | Manager email address (used for alert notifications). |
| `wk_start` / `wk_end` | smallint(6) | Working day start / end hour (0–23 integer). |
| `wktime` | smallint(6) | Standard work time in minutes per day. |
| `use_alert` | tinyint(1) | `1` = alert email notifications enabled. |
| `def_term` | smallint(6) | Default schedule term length (months). |
| `rests` | varchar(64) | Rest time configuration string. |
| `sk_kuri` | tinyint(1) | Schedule carry-over setting. |
| `hide_eqid` | tinyint(1) | `1` = hide equipment ID column in lists. |
| `del_disable` | tinyint(1) | `1` = prevent deletion of maintenance records. |
| `dl_char` | char(1) | CSV download character encoding (`'0'`=UTF-8, `'1'`=Shift-JIS). |
| `tana_date1` / `tana_date2` | varchar(8) | Inventory cycle date settings. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |
| `uptime` | int(11) | Unix timestamp of last update. |

---

## a_auths

*Used in: [[Schedule calendar#Auth / session context|Schedule calendar]], [[Authorization master#Permission catalog|Authorization master]]*

**Purpose:** Feature permission groups. One row per permission group per tenant. `aspUser->setAuth($db, $request, $authId)` reads this table to check whether the current user's group has permission to view or edit a specific feature on the current page. Each `at_N` flag maps to a specific feature (e.g. `at_12` = schedule page edit permission).

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `at_id`. |
| `at_id` | smallint(6) NOT NULL | Auth group ID. |
| `at_name` | varchar(16) | Group display name (e.g. `管理者`, `一般`). |
| `at_all` | tinyint(1) | `1` = grant all permissions shortcut (overrides individual flags). |
| `at_notall` | tinyint(1) | `1` = restrict to own records only. |
| `at_params` | varchar(128) | Additional parameter string for fine-grained overrides. |
| `at_1` | char(1) | Permission flag for feature 1. |
| `at_2` | char(1) | Permission flag for feature 2. |
| `at_3` | char(1) | Permission flag for feature 3. |
| `at_10` | char(1) | Permission flag for feature 10. |
| `at_11` | char(1) | Permission flag for feature 11. |
| `at_12` | char(1) | Permission flag for feature 12 (schedule page). |
| `at_13` | char(1) | Permission flag for feature 13. |
| `at_14` | char(1) | Permission flag for feature 14. |
| `at_15` | char(1) | Permission flag for feature 15. |
| `authid` / `authpass` | varchar(16) | External authentication credentials (optional). |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |

---

## a_eqgroup

*Used in: [[Schedule calendar#Auth / session context|Schedule calendar]]*

**Purpose:** Equipment group master. Groups of equipment named for categorisation. Used as the `eqg_id` filter on the `sch.php` calendar search form. Equipment rows reference this via `a_equips.eqg_id`.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `eqg_id`. |
| `eqg_id` | smallint(6) NOT NULL | Equipment group ID (PK). |
| `eqg_name` | varchar(32) | Group display name. |
| `eqg_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique group row ID. |
| `disporder` | smallint(6) | Sort order. |
| `nfc_id` | varchar(16) | NFC tag ID associated with this group (for mobile scan features). |
| `eqg_data` | blob | Additional group configuration data. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |

## a_area

*Used in: [[Authorization master#Location / org hierarchy|Authorization master]]*

**Purpose:** Area master. Top-level geographic grouping, sitting above factory in the location hierarchy. Each factory row has an `area_id` that is an app-level FK into this table. Staff accounts may also carry an `area_id` for access scoping. Used on `auth.php` as a helper lookup for factory/staff UI -- not a direct structural parent of `a_auths`.

| Column | Type | Purpose |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant partition key. Composite PK with `area_id`. |
| `area_id` | smallint(6) NOT NULL | Area ID (business PK). |
| `area_name` | varchar(32) | Area display name. |
| `area_uid` | bigint(20) unsigned AUTO_INCREMENT | Globally unique area row ID. |
| `disporder` | smallint(6) | Sort order. |
| `uptime` | int(11) | Unix timestamp of last update. |
| `modify_date` / `create_date` | datetime | Audit timestamps. |
| `stf_id` | smallint(6) | Staff ID of last editor. |

---
