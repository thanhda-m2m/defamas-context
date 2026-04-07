---
aliases:
  - admin menu index
tags:
  - en
  - ppes
  - admin
  - menu
  - obsidian
---

# 管理 index

Menu group for `管理` and `master_tab.inc`. These are **per-tenant** admin pages accessible after logging in as a tenant admin.

> **Note:** There is also a separate super-admin panel at `/padmin/` that manages tenant provisioning and the `zaikodb` database. That layer is documented in [[Multi-tenancy architecture]] and is not part of this menu group.

## Notes

- [[Info announcements]]
- [[Authorization master]]
- [[Staff management]]
- [[Factory and location master]]
- [[Equipment group master]]
- [[Equipment item master]]
- [[Maker master]]
- [[Mail template master]]
- [[Configuration page]]

## Master relationships

```mermaid
flowchart TD
    Auth["a_auths"] --> Staff["bk_staff"]
    Factory["a_area / a_factory / a_line / a_floor"] --> Equip["a_equips"]
    EqGroup["a_eqgroup / a_eqgroup_detail / a_eqitem"] --> Equip
    Maker["a_maker"] --> Equip
    Maker --> Stock["a_stocks"]
    Mail["mail_master"] --> Mt["mtinfo/mtres mail flows"]
    Mail --> Rent["rent mail flows"]
    Info["bk_infos"] --> Sch["sch.php"]
    Config["bkmasters"] --> Sch
    Config --> Head["shared head/template behavior"]
```
