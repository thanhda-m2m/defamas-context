---
tags:
  - ppes
  - vi
  - admin
  - menu
---

# Quản trị index (VI)

## Notes

- [[Thông báo nội bộ (VI)]]
- [[Phân quyền master (VI)]]
- [[Quản lý nhân sự (VI)]]
- [[Master cơ sở và vị trí (VI)]]
- [[Master nhóm thiết bị (VI)]]
- [[Master hạng mục thiết bị (VI)]]
- [[Master maker (VI)]]
- [[Master mẫu email (VI)]]
- [[Trang cấu hình (VI)]]
- [[Từ vựng chuyên ngành JA-VI]]

## Mermaid

```mermaid
flowchart TD
    Auth["a_auths"] --> Staff["bk_staff"]
    Factory["a_area/a_factory/a_line/a_floor"] --> Equip["a_equips"]
    Group["a_eqgroup/a_eqitem/a_eqgroup_detail"] --> Equip
    Maker["a_maker"] --> Equip
    Maker --> Stock["a_stocks"]
    Mail["a_mailtmpl"] --> Flow["rent + maintenance mails"]
    Config["bkmasters"] --> Pages["shared app behavior"]
```
