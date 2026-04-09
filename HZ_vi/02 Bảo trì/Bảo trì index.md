---
tags:
  - ppes
  - vi
  - maintenance
  - menu
---

# Bảo trì index

## Notes

- [[Đặt lịch bảo trì]]
- [[Công việc bảo trì]]
- [[Danh sách kết quả bảo trì (VI)]]
- [[Lịch bảo trì]]
- [[Từ vựng chuyên ngành JA-VI]]

## Luồng chính

```mermaid
flowchart LR
    Yk["mtinfo_yoyaku.php"] --> Sch["sch.php"]
    Mt["mtinfo.php"] --> Sch
    Mt --> Res["mtres_list.php"]
```
