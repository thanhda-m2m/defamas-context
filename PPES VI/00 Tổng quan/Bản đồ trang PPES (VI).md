---
aliases:
  - PPES map VI
  - Vietnamese PPES map
tags:
  - ppes
  - vietnamese
  - overview
  - obsidian
---

# Bản đồ trang PPES (VI)

Note tổng quan tiếng Việt cho wiki PPES.

## Chỉ mục theo menu

- [[Thiết bị index (VI)]]
- [[Bảo trì index (VI)]]
- [[Tồn kho index (VI)]]
- [[Quản trị index (VI)]]
- [[Từ vựng chuyên ngành JA-VI]]

## Sơ đồ menu

```mermaid
flowchart LR
    Equip["設備機器 / Thiết bị"]
    Hozen["保全 / Bảo trì"]
    Stock["在庫一覧 / Tồn kho"]
    Admin["管理 / Quản trị"]
```

## Sơ đồ dữ liệu lõi

```mermaid
flowchart TD
    AE["a_equips"] --> AED["a_equips_detail"]
    AE --> AEH["a_eqhist"]
    AE --> AMI["a_mtinfo"]
    AMI --> AMS["a_mtsch"]
    AMS --> AMR["a_mtres"]
    AE --> AES["a_eqstocks"]
    AST["a_stocks"] --> AES
```

## Cách đọc bộ note VI

- Dùng bản VI để nắm ý nghĩa nghiệp vụ nhanh.
- Dùng note tiếng Anh gốc khi cần chi tiết controller, code reference, hoặc rủi ro kỹ thuật sâu hơn.
- Thuật ngữ chuyên ngành giữ dạng `JA-VI` theo [[Từ vựng chuyên ngành JA-VI]].

## Link sang bản chi tiết gốc

- [[PPES page map]]
