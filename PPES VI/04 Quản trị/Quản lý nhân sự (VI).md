---
aliases:
  - /{tenant}/staff.php (VI)
tags:
  - ppes
  - vi
  - admin
  - staff
---

# Quản lý nhân sự (VI)

## Thuật ngữ chính

- `ユーザ管理 (quản lý người dùng)`
- `担当者 (người phụ trách)`

## Tóm tắt

`/{tenant}/staff.php` quản lý `bk_staff`.

- dùng `a_auths` để gán level quyền
- dùng area/factory để scope người dùng
- hỗ trợ import
- có rule: luôn phải còn ít nhất một admin active

## Bảng chính

- `bk_staff`
- `bk_idmaster`
- `a_auths`
- `a_area`
- `a_factory`
- `a_line`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    buscomps {
        bigint bkid PK
        varchar bcname
    }
    bk_staff {
        smallint bkid PK "app-FK to buscomps"
        int stf_id PK
        varchar level "app-FK to a_auths"
        smallint area_id "app-FK to a_area"
        varchar fc_id "app-FK to a_factory"
    }
    a_auths {
        smallint bkid PK
        smallint at_id PK
        varchar at_name
    }
    bk_idmaster {
        int bkid PK
        varchar p_name PK
        int p_val
    }
    a_area {
        smallint bkid PK
        smallint area_id PK
    }
    a_factory {
        smallint bkid PK
        varchar fc_id PK
        smallint area_id "app-FK to a_area"
        varchar fc_name
    }
    a_line {
        smallint bkid PK
        varchar line_id PK
        varchar fc_id PK "app-FK to a_factory"
        varchar line_name
    }
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "at_id (level)"
    bk_idmaster ||--o{ bk_staff : "p_val (id alloc)"
    a_area ||--o{ a_factory : "area_id"
    a_factory ||--o{ a_line : "fc_id"
    a_area ||--o{ bk_staff : "area_id"
    a_factory ||--o{ bk_staff : "fc_id"
```

## Mermaid

```mermaid
flowchart TD
    Staff["bk_staff"] --> Level["a_auths"]
    Staff --> Org["a_area / a_factory"]
```

## Xem thêm

- [[Staff management]]
