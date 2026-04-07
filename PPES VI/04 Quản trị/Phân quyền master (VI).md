---
aliases:
  - /{tenant}/auth.php (VI)
tags:
  - ppes
  - vi
  - admin
  - auth
---

# Phân quyền master (VI)

## Thuật ngữ chính

- `権限 (phân quyền)`
- `マスタ管理 (quản lý master)`

## Tóm tắt

`/{tenant}/auth.php` quản lý `a_auths`.

- upload/import là nhánh ghi dữ liệu thực sự quan trọng
- list/download đang hoạt động
- manual edit có dấu hiệu stale hoặc copy-paste lỗi

## Bảng chính

- `a_auths`
- `datamaster`
- `a_area`
- `a_factory`
- `buscomps`
- `bk_staff`
- `bkmasters`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
    buscomps {
        bigint bkid PK
        varchar bcname
    }
    bkmasters {
        smallint bkid PK "app-FK to buscomps"
        smallint term
        char zaiko_add
        tinyint hide_eqid
    }
    a_auths {
        smallint bkid PK "app-FK to buscomps"
        smallint at_id PK
        varchar at_name
    }
    bk_staff {
        smallint bkid PK "app-FK to buscomps"
        int stf_id PK
        varchar level "app-FK to a_auths"
        smallint area_id "app-FK to a_area"
        varchar fc_id "app-FK to a_factory"
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
    datamaster {
        varchar propid PK
        smallint itid PK
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ a_auths : "bkid"
    a_auths ||--o{ bk_staff : "at_id (level)"
    buscomps ||--o{ bk_staff : "bkid"
    a_area ||--o{ a_factory : "area_id"
    datamaster }o--o{ a_auths : "propid (app labels)"
```

## Mermaid

```mermaid
flowchart TD
    AuthPage["auth.php"] --> AuthTable["a_auths"]
    AuthTable --> SetAuth["setAuth() across pages"]
```

## Xem thêm

- [[Authorization master]]
- [[Quản lý nhân sự (VI)]]
