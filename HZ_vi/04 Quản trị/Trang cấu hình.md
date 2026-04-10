---
aliases:
  - /{tenant}/config.php
tags:
  - ppes
  - vi
  - admin
  - config
---

# Trang cấu hình

## Thuật ngữ chính

- `基本設定 (cấu hình cơ bản)`
- `期間 (khoảng thời gian)`

## Tóm tắt

`/{tenant}/config.php` chỉnh một record cấu hình dùng chung trong `bkmasters`.

- ảnh hưởng tới term mặc định của lịch
- ảnh hưởng tới một số behavior giao diện chung
- không có list page thực sự, gần giống một form cấu hình

## Bảng chính

- `bkmasters`
- `buscomps`
- `bk_staff`
- `a_auths`
- `datamaster`

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
    datamaster {
        varchar propid PK
        smallint itid PK
    }
    a_mtinfo {
        smallint bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        smallint mtinfo_kbn
    }
    a_stocks {
        smallint bkid PK
        varchar hin_id PK
        varchar fc_id PK "app-FK to a_factory"
        double stk_num
        double stk_num_warn
    }
    buscomps ||--|| bkmasters : "bkid"
    buscomps ||--o{ bk_staff : "bkid"
    a_auths ||--o{ bk_staff : "at_id (level)"
    datamaster }o--o{ bkmasters : "propid (app labels)"
    bkmasters }o--o{ a_mtinfo : "bkid (app defaults)"
    bkmasters }o--o{ a_stocks : "bkid (app defaults)"
```

Ghi chú suy luận:

- `bkmasters` ảnh hưởng tới behavior của nhiều trang qua application context, không phải do foreign key trực tiếp.

## Mermaid

```mermaid
flowchart TD
    Config["bkmasters"] --> Sch["sch.php term"]
    Config --> Ui["shared UI behavior"]
    Config --> User["merged into aspUser context"]
```

## Xem thêm

- [[Configuration page]]
- [[Lịch bảo trì]]
