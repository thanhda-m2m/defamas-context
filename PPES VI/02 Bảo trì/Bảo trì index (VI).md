---
tags:
  - ppes
  - vi
  - maintenance
  - menu
---

# Bảo trì index (VI)

## Notes

- [[Đặt lịch bảo trì (VI)]]
- [[Công việc bảo trì (VI)]]
- [[Danh sách kết quả bảo trì (VI)]]
- [[Lịch bảo trì (VI)]]
- [[Từ vựng chuyên ngành JA-VI]]

## Luồng chính

```mermaid
flowchart LR
    Yk["mtinfo_yoyaku.php"] --> Sch["sch.php"]
    Mt["mtinfo.php"] --> Sch
    Mt --> Res["mtres_list.php"]
```
