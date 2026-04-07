---
aliases:
  - /{tenant}/sch.php (VI)
tags:
  - ppes
  - vi
  - maintenance
  - calendar
---

# Lịch bảo trì (VI)

## Thuật ngữ chính

- `保全カレンダー (lịch bảo trì)`
- `日次計画 (kế hoạch ngày)`
- `月次計画 (kế hoạch tháng)`
- `年次計画 (kế hoạch năm)`

## Tóm tắt

`/{tenant}/sch.php` là trang calendar/projection, không phải source-of-truth editor.

- đọc lịch từ `a_mtinfo + a_mtsch + a_mtres + a_equips`
- hỗ trợ view day/month/year/long
- là hub điều hướng sang equip/mtinfo/mtres

## Bảng chính

- `a_equips`
- `a_mtinfo`
- `a_mtsch`
- `a_mtres`
- `a_factory`
- `a_line`
- `holidays`
- `bk_infos`
- `infos`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
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
    a_equips {
        smallint bkid PK
        int eq_id PK
        smallint eqg_id "app-FK to a_eqgroup"
        varchar fc_id "app-FK to a_factory"
        varchar line_id "app-FK to a_line"
        tinyint del_flg
    }
    a_mtinfo {
        smallint bkid PK
        int mt_id PK
        int eq_id "app-FK to a_equips"
        smallint mtinfo_kbn
    }
    a_mtsch {
        smallint bkid PK
        int mt_id PK "app-FK to a_mtinfo"
        date sdate PK
        bigint mts_uid
    }
    a_mtres {
        smallint bkid PK
        int mts_uid PK "app-FK to a_mtsch"
    }
    bk_infos {
        smallint bkid PK
        int inf_id PK
        tinyint del_flg
    }
    infos {
        int inf_id PK
    }
    holidays {
        date hday PK
    }
    a_factory ||--o{ a_line : "fc_id"
    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_equips ||--o{ a_mtinfo : "eq_id"
    a_mtinfo ||--o{ a_mtsch : "mt_id"
    a_mtsch ||--o| a_mtres : "mts_uid"
    bk_infos }o--o{ a_mtinfo : "bkid (app view)"
    infos }o--o{ a_mtinfo : "inf_id (app view)"
    holidays }o--o{ a_mtsch : "hday (calendar overlay)"
```

Ghi chú suy luận:

- `bk_infos`, `infos`, `holidays` không phải parent thực của maintenance data; chúng chỉ được ghép vào calendar ở mức application view.

## Side effects

- không ghi business table
- redirect login nếu fail auth
- render widget news/info/today list

## Mermaid

```mermaid
flowchart TD
    Data["a_mtinfo + a_mtsch + a_mtres"] --> Calendar["sch.php grid"]
    Calendar --> Equip["equip.php"]
    Calendar --> Mtinfo["mtinfo.php"]
    Calendar --> Mtres["mtres.php"]
```

## Xem thêm

- [[Schedule calendar]]
- [[Đặt lịch bảo trì (VI)]]
- [[Công việc bảo trì (VI)]]
