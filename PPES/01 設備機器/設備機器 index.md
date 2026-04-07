---
aliases:
  - equipment menu index
tags:
  - en
  - ppes
  - equip
  - menu
  - obsidian
---

# è¨­å‚™æ©Ÿå™¨ index

Menu group for `設備機器`.

## Notes

- [[Equipment page]]
- [[Edit-add equipment]]
- [[Stock management]]

## Flow

```mermaid
flowchart LR
    Equip["equip.php"] --> EquipAdd["equip.php?edit=1&add=1"]
    Equip --> Stock["stock.php"]
    Equip --> Yoyaku["mtinfo_yoyaku.php"]
    Equip --> Mtinfo["mtinfo.php"]
```
