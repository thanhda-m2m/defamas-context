---
aliases:
  - /ppes/info.php (VI)
tags:
  - ppes
  - vietnamese
  - admin
  - info
---

# Thông báo nội bộ (VI)

## Thuật ngữ chính

- `基本情報 (thông tin cơ bản)`
- `お知らせ / 情報 (thông báo / thông tin)`

## Tóm tắt

`/ppes/info.php` là CRUD cho thông báo tenant trong `bk_infos`.

- list / edit / confirm / save / soft-delete
- dữ liệu này được hiển thị lại ở trang lịch như news/info

## Bảng chính

- `bk_infos`
- `datamaster`
- `buscomps`
- `bk_staff`
- `bkmasters`
- `a_auths`

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
    bk_infos {
        smallint bkid PK "app-FK to buscomps"
        int inf_id PK
        tinyint del_flg
    }
    datamaster {
        varchar propid PK
        smallint itid PK
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "at_id (level)"
    buscomps ||--o{ bk_infos : "bkid"
    datamaster }o--o{ bk_infos : "propid (app labels)"
```

## Mermaid

```mermaid
flowchart TD
    Info["info.php"] --> News["bk_infos"]
    News --> Sch["sch.php newsList"]
```

## Xem thêm

- [[Info announcements]]
