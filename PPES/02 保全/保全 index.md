---
aliases:
  - maintenance menu index
tags:
  - en
  - ppes
  - maintenance
  - menu
  - obsidian
---

## 保全 index

Menu group for `保全`.

## Notes

- [[Maintenance reservation page]]
- [[Maintenance work page]]
- [[Maintenance results list]]
- [[Schedule calendar]]

## Flow

```mermaid
flowchart LR
    Yoyaku["mtinfo_yoyaku.php"] --> Sch["sch.php"]
    Mtinfo["mtinfo.php"] --> Sch
    Mtinfo --> MtresList["mtres_list.php"]
```
