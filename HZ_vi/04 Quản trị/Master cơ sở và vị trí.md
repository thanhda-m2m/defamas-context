---
aliases:
  - /{tenant}/factory.php
tags:
  - ppes
  - vi
  - admin
  - factory
---

# Master cơ sở và vị trí

## Thuật ngữ chính

- `拠点 (cơ sở)`
- `エリア (khu vực)`
- `設置場所・建屋 (line / khu vực lắp đặt)`
- `階・部屋 (tầng / phòng)`

## Tóm tắt

`/{tenant}/factory.php` là source-of-truth cho location hierarchy.

- `a_area`
- `a_factory`
- `a_line`
- `a_floor`

Downstream lớn nhất là `equip.php` và `stock.php`.

## Bảng chính

- `a_area`
- `a_factory`
- `a_line`
- `a_floor`
- `a_equips`

## Mermaid ER

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều ở tầng application.

```mermaid
erDiagram
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
    a_floor {
        smallint bkid PK
        varchar fc_id PK "app-FK to a_factory"
        varchar line_id PK "app-FK to a_line"
        varchar flr_id PK
        varchar flr_name PK
    }
    a_equips {
        smallint bkid PK
        int eq_id PK
        varchar fc_id "app-FK to a_factory"
        varchar line_id "app-FK to a_line"
        varchar flr_id "app-FK to a_floor"
        tinyint del_flg
    }
    a_area ||--o{ a_factory : "area_id"
    a_factory ||--o{ a_line : "fc_id"
    a_line ||--o{ a_floor : "line_id"
    a_factory ||--o{ a_equips : "fc_id"
    a_line ||--o{ a_equips : "line_id"
    a_floor ||--o{ a_equips : "flr_id"
```

## Mermaid

```mermaid
flowchart TD
    Area["a_area"] --> Factory["a_factory"]
    Factory --> Line["a_line"]
    Line --> Floor["a_floor"]
    Factory --> Equip["a_equips"]
    Factory --> Stock["a_stocks"]
```

## Xem thêm

- [[Factory and location master]]
- [[Trang thiết bị]]
- [[Quản lý tồn kho]]
