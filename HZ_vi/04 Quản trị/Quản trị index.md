---
tags:
  - ppes
  - vi
  - admin
  - menu
---

# Quản trị index

## Notes

- [[Thông báo nội bộ]]
- [[Phân quyền master]]
- [[Quản lý nhân sự]]
- [[Master cơ sở và vị trí]]
- [[Master nhóm thiết bị]]
- [[Master hạng mục thiết bị]]
- [[Master maker]]
- [[Master mẫu email]]
- [[Trang cấu hình]]
- [[Từ vựng chuyên ngành JA-VI]]

## Kiến trúc xuyên suốt

- [[Kiến trúc Import-Export]] — pattern import/export chung được dùng bởi tất cả trang master ở trên

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
