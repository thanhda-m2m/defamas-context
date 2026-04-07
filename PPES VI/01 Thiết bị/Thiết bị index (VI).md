---
tags:
  - ppes
  - vi
  - equip
  - menu
---

# Thiết bị index (VI)

## Notes

- [[Trang thiết bị (VI)]]
- [[Thêm thiết bị (VI)]]
- [[Quản lý tồn kho (VI)]]
- [[Từ vựng chuyên ngành JA-VI]]

## Luồng chính

```mermaid
flowchart LR
    Equip["equip.php"] --> Add["equip.php?edit=1&add=1"]
    Equip --> Stock["stock.php"]
    Equip --> Yk["mtinfo_yoyaku.php"]
    Equip --> Mt["mtinfo.php"]
```
