---
aliases:
  - bảng từ điển
  - schema reference vi
tags:
  - ppes
  - vi
  - schema
  - reference
---

# Bảng từ điển

Tài liệu tham chiếu schema toàn hệ thống cho tất cả các bảng database được sử dụng trong PPES.
Mỗi mục liên kết ngược về trang tài liệu chi tiết đã sử dụng bảng đó.

> **Ghi chú**: Database không có FK constraint. Tất cả quan hệ đều được thực thi ở tầng application. Mọi bảng của tenant đều có cột `bkid` (smallint) dùng để phân vùng dữ liệu đa tenant.

> **Ma trận truy cập route tenant**: Các cột route chỉ thể hiện truy cập bảng trực tiếp của các route HTML trong menu dạng `/{tenantCode}/...`. `read/write` bao gồm helper upsert như `dbUpdate()` và ghi metadata file qua `setFile()`. Ô trống nghĩa là không tìm thấy truy cập bảng trực tiếp cho route tenant đó. Các route sys, padmin, API endpoint, helper tải file, màn hình login/forgot-password, và file backup như `*.php.0` không được tính.

---

## Mục lục bảng

| STT | Bảng                  | Nhóm                  | Vai trò tóm tắt                                 |
| --- | --------------------- | --------------------- | ----------------------------------------------- |
| 1   | [[#a_equips]]         | Chuỗi bảo trì cốt lõi | Master thiết bị                                 |
| 2   | [[#a_mtinfo]]         | Chuỗi bảo trì cốt lõi | Kế hoạch bảo trì                                |
| 3   | [[#a_mtsch]]          | Chuỗi bảo trì cốt lõi | Instance lịch bảo trì                           |
| 4   | [[#a_mtres]]          | Chuỗi bảo trì cốt lõi | Kết quả bảo trì                                 |
| 5   | [[#a_factory]]        | Địa điểm / tổ chức    | Master nhà máy                                  |
| 6   | [[#a_line]]           | Địa điểm / tổ chức    | Master dây chuyền                               |
| 7   | [[#holidays]]         | Hỗ trợ calendar       | Danh sách ngày lễ                               |
| 8   | [[#datamaster]]       | Hỗ trợ calendar       | Master giá trị dropdown                         |
| 9   | [[#bk_infos]]         | Widget trang          | Thông báo theo tenant                           |
| 10  | [[#infos]]            | Widget trang          | Thông báo toàn hệ thống                         |
| 11  | [[#buscomps]]         | Auth / session        | Registry tenant (zaikodb)                       |
| 12  | [[#bk_staff]]         | Auth / session        | Tài khoản nhân viên tenant                      |
| 13  | [[#bkmasters]]        | Auth / session        | Cấu hình tenant                                 |
| 14  | [[#a_auths]]          | Auth / session        | Nhóm quyền tính năng                            |
| 15  | [[#a_eqgroup]]        | Auth / session        | Master nhóm thiết bị                            |
| 16  | [[#a_area]]           | Địa điểm / tổ chức    | Master khu vực                                  |
| 17  | [[#a_ckgroup]]        | Kiểm tra / inspection | Master nhóm kiểm tra                            |
| 18  | [[#a_ckgroup_detail]] | Kiểm tra / inspection | Bảng liên kết nhóm kiểm tra ↔ hạng mục kiểm tra |
| 19  | [[#a_ckitem]]         | Kiểm tra / inspection | Master hạng mục kiểm tra                        |
| 20  | [[#a_eqgroup_detail]] | Thiết bị              | Bảng liên kết nhóm thiết bị ↔ trường tùy chỉnh  |
| 21  | [[#a_eqhist]]         | Thiết bị              | Lịch sử thay đổi thiết bị                       |
| 22  | [[#a_eqitem]]         | Thiết bị              | Master trường tùy chỉnh thiết bị                |
| 23  | [[#a_eqpoint]]        | Thiết bị              | Master điểm kiểm tra thiết bị                   |
| 24  | [[#a_eqstocks]]       | Thiết bị              | Bảng liên kết thiết bị ↔ tồn kho                |
| 25  | [[#a_equips_detail]]  | Thiết bị              | Giá trị trường tùy chỉnh thiết bị               |
| 26  | [[#a_floor]]          | Địa điểm / tổ chức    | Master tầng                                     |
| 27  | [[#a_files]]          | Quản lý file          | Đăng ký file đính kèm tổng quát                 |
| 28  | [[#a_mailtmpl]]       | Email / thông báo     | Master mẫu email                                |
| 29  | [[#mail_master]]      | Email / thông báo     | Master người nhận email                         |
| 30  | [[#a_maker]]          | Nhà sản xuất          | Master nhà sản xuất / nhà cung cấp              |
| 31  | [[#a_mtbf]]           | Độ tin cậy            | Chỉ số MTBF / MTTR                              |
| 32  | [[#a_rent]]           | Cho thuê              | Bản ghi cho thuê / mượn thiết bị                |
| 33  | [[#a_stocks]]         | Tồn kho / kiểm kê     | Master tồn kho / phụ tùng                       |
| 34  | [[#a_tana]]           | Tồn kho / kiểm kê     | Bản ghi kiểm kê vật lý (棚卸)                     |
| 35  | [[#ads_master]]       | Master dữ liệu        | Master giá trị dropdown theo tenant             |
| 36  | [[#bk_idmaster]]      | Master dữ liệu        | Bộ cấp ID tuần tự                               |
| 37  | [[#calendars]]        | Calendar / lịch       | Sự kiện lịch theo tenant                        |
| 38  | [[#myview]]           | Calendar / lịch       | Lịch bảo trì được đánh dấu cá nhân              |
| 39  | [[#busareas]]         | Auth / quản trị       | Phân công công ty ↔ khu vực                     |
| 40  | [[#bustypengdays]]    | Auth / quản trị       | Ngày không khả dụng theo loại kinh doanh        |
| 41  | [[#loginhist]]        | Auth / quản trị       | Nhật ký đăng nhập / đăng xuất                   |
| 42  | [[#staff]]            | Auth / quản trị       | Tài khoản super-admin (zaikodb)                 |
| 43  | [[#p_item]]           | CAPEX / mua sắm       | Master hạng mục mua sắm                         |
| 44  | [[#p_mente]]          | CAPEX / mua sắm       | Bản ghi bảo trì dự án                           |
| 45  | [[#p_proj]]           | CAPEX / mua sắm       | Master dự án                                    |
| 46  | [[#p_purchase]]       | CAPEX / mua sắm       | Đơn đặt hàng                                    |
| 47  | [[#p_puritem]]        | CAPEX / mua sắm       | Dòng chi tiết đơn đặt hàng                      |
| 48  | [[#p_ringi]]          | CAPEX / mua sắm       | Yêu cầu phê duyệt (稟議)                          |
| 49  | [[#p_rinitem]]        | CAPEX / mua sắm       | Dòng chi tiết yêu cầu phê duyệt                 |
| 50  | [[#p_sisan]]          | CAPEX / mua sắm       | Sổ tài sản cố định                              |
| 51  | [[#p_sisancode]]      | CAPEX / mua sắm       | Master mã / thẻ tài sản                         |
| 52  | [[#syain_master]]     | Nhân viên             | Master công nhân / nhân viên vận hành           |

---

## a_equips

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Master thiết bị. Mỗi row là một thiết bị vật lý thuộc một tenant. Lưu tên, vị trí (nhà máy/dây chuyền), nhóm thiết bị, thông số máy, và tối đa 16 trường tùy chỉnh. Xóa mềm qua `del_flg` — các row bị xóa được ẩn trên tất cả màn hình nhưng vẫn được giữ trong database.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Tất cả query đều filter `WHERE bkid = $this->_bkid`. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eq_id` | int(11) NOT NULL | ID thiết bị — số nguyên gán khi tạo. Composite PK với `bkid`. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eq_name` | varchar(100) | Tên hiển thị của thiết bị trên tất cả màn hình. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eq_kbn` | smallint(6) | Mã phân loại loại thiết bị. Tra nhãn qua `datamaster` (`propid='eq_kbn'`). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eq_stat` | smallint(6) | Mã trạng thái vận hành thiết bị. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `disporder` | smallint(6) | Thứ tự hiển thị trong danh sách. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `create_date` | datetime | Datetime tạo row. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eqg_id` | smallint(6) | ID nhóm thiết bị (app-FK → `a_eqgroup.eqg_id`). Dùng cho bộ lọc nhóm trên `sch.php`. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eq_vals` | text | Giá trị trường tùy chỉnh (dạng JSON/tuần tự hóa). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eq_words` | text | Blob từ khóa tìm kiếm full-text. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `eqv11`–`eqv16` | varchar(32) ×6 | Sáu trường chuỗi tùy chỉnh bổ sung. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `del_flg` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa và ẩn khỏi tất cả trang. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `fc_num` | varchar(16) | Mã máy / thẻ quản lý nội bộ nhà máy. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `mat_mcno` | varchar(50) | Số model máy của nhà sản xuất. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `mat_nensiki` | varchar(8) | Năm sản xuất (ví dụ `2019`). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `mat_ton` | float | Công suất / trọng tải. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `mat_mk_id` | varchar(8) | ID nhà sản xuất (app-FK → `a_maker.mk_id`). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `mvdata` | text | Dữ liệu lịch sử di chuyển / chuyển giao thiết bị. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `upload` | tinyint(1) | `1` nếu thiết bị có file đính kèm. | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `details` | blob | Blob mô tả chi tiết dạng rich-text (HTML). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |
| `flr_id` | varchar(16) | ID tầng (app-FK → `a_floor.flr_id`). | read/write | read | read | read | read | read/write |  |  |  | read |  | read/write |  |  |  |  | read/write |

---

## a_mtinfo

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Kế hoạch bảo trì. Mỗi row là một nhiệm vụ bảo trì gắn với một thiết bị. Lưu tên nhiệm vụ, loại, cửa sổ kế hoạch, chi phí dự kiến, và tối đa 19 file đính kèm. Cột `sc_kbn` phân biệt BT định kỳ với quy trình báo giá/phát hành phiếu.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_id` | int(11) NOT NULL | ID kế hoạch BT (business PK). Composite PK với `bkid`. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) NOT NULL | ID thiết bị (app-FK → `a_equips.eq_id`). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `res_date` | datetime | Ngày đăng ký kế hoạch. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_name` | varchar(64) | Tên hiển thị nhiệm vụ BT. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `purpose` | varchar(128) | Mục đích / mục tiêu của nhiệm vụ. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu (qua mọi tenant). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `ree_date` | datetime | Ngày đăng ký lại / sửa đổi. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `eq_file1`–`eq_file19` | varchar(128) ×19 | Đường dẫn file đính kèm (19 slot). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_detail` | varchar(512) | Mô tả chi tiết công việc BT. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `failure_place` | varchar(512) | Vị trí hỏng hóc / điểm kiểm tra. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `sc_start_date` | date | Ngày bắt đầu cửa sổ kế hoạch (cấp plan). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `sc_end_date` | date | Ngày kết thúc cửa sổ kế hoạch (cấp plan). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtinfo_kbn` | smallint(6) | Mã phân loại loại BT. Tra nhãn qua `datamaster` (`propid='mtinfo_kbn'`). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `import_flg` | smallint(6) | Cờ đánh dấu row nhập từ nguồn ngoài. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `sc_kbn` | smallint(6) | Danh mục lịch. `0` = BT định kỳ; giá trị khác = quy trình báo giá/phát hành phiếu. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_cost` | int(11) | Chi phí dự kiến (cấp plan). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `del_flg` | tinyint(1) | Cờ xóa mềm. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `sp_start_date` / `sp_end_date` | datetime | Khoảng thời gian đặc biệt bắt đầu/kết thúc. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `failure_date` | date | Ngày xảy ra sự cố. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `estimate_kbn` | varchar(64) | Mã loại báo giá. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `fas_date` / `fae_date` | datetime | Ngày bắt đầu/kết thúc hoạt động FA. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `fas2_date` / `fae2_date` | datetime | Ngày FA thứ cấp bắt đầu/kết thúc. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `fail_date` | datetime | Datetime xảy ra sự cố. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_stat` | char(1) | Mã trạng thái kế hoạch (`'0'` = mặc định). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `main_stfname` | varchar(16) | Tên nhân viên chính (denormalized, snapshot). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `sub_stfname` | varchar(128) | Tên các nhân viên phụ (snapshot cách nhau bởi dấu phẩy). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `matsuo` | text | Blob dữ liệu bổ sung. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_fixtime` | smallint(6) | Thời gian cố định được phân bổ. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `upload` | tinyint(1) | `1` nếu kế hoạch có file đính kèm. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `ins_kbn` / `ins_term` / `ins_cat` | smallint / varchar | Mã loại / kỳ hạn / danh mục kiểm định. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `ins_data` | text | Blob dữ liệu kiểm định. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `stat1`–`stat5` | smallint(6) ×5 | Mã cờ trạng thái tùy chỉnh 1–5. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `stats1` / `stats2` | varchar(32) | Trường chuỗi trạng thái tùy chỉnh. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtdate1` | date | Ngày yêu cầu báo giá cấp plan (sao chép từ `mtr_mtdate1` khi tạo plan). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `dpdate1` | date | Ngày phát hành phiếu cấp plan (sao chép từ `mtr_dpdate1` khi tạo plan). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mmvdata` | blob | Blob dữ liệu di chuyển. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |

---

## a_mtsch

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Instance lịch bảo trì. Mỗi row là một lần thực hiện cụ thể của một kế hoạch (`mt_id` + `sdate`). Đây là **nguồn sự thật duy nhất cho tất cả icon trên calendar** của `sch.php`. Lưu cửa sổ thực hiện (`s_date`/`e_date`), cờ hoàn thành (`mtr_done`), và bốn cột ngày điều khiển icon Path B.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mt_id` | int(11) NOT NULL | ID kế hoạch BT (app-FK → `a_mtinfo.mt_id`). Phần của composite PK. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `sdate` | date NOT NULL | Ngày lịch (phần của composite PK `bkid, mt_id, sdate`). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `s_date` | datetime | Ngày bắt đầu cửa sổ thực hiện (độ chính xác datetime). Dùng để khớp khoảng ngày Path A. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `e_date` | datetime | Ngày kết thúc cửa sổ. Dùng kiểm tra quá hạn: `e_date < hôm nay` → nền vàng. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mts_uid` | bigint(20) unsigned AUTO_INCREMENT | ID instance lịch duy nhất toàn cầu. Được `a_mtres.mts_uid` tham chiếu như FK. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_done` | tinyint(1) DEFAULT 0 | **Cờ hoàn thành / đã đăng ký kết quả. Điều khiển icon Path A:** có giá trị → ●; không có + chưa quá hạn → ○; không có + quá hạn → ○ nền vàng. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `del_flg` | tinyint(1) | Cờ xóa mềm. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_stat` | char(1) | Mã trạng thái lịch (`'0'` = mặc định). | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `upload` | tinyint(1) | `1` nếu instance lịch có file đính kèm. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_mtdate1` | date | **Ngày dự kiến yêu cầu báo giá. Điều khiển △ (Path B ưu tiên 4).** Bị loại trừ khi `mtr_mtdate2` có giá trị. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_mtdate2` | date | **Ngày báo giá thực tế. Điều khiển ▲ (Path B ưu tiên 3).** Đặt giá trị này sẽ loại trừ `mtr_mtdate1` khỏi map icon PHP. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_dpdate1` | date | **Ngày dự kiến phát hành phiếu. Điều khiển □ (Path B ưu tiên 2).** Bị loại trừ khi `mtr_dpdate2` có giá trị. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_dpdate2` | date | **Ngày phát hành phiếu thực tế. Điều khiển ■ (Path B ưu tiên 1).** Đặt giá trị này sẽ loại trừ `mtr_dpdate1` khỏi map icon PHP. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `sendnum` | smallint(6) | Bộ đếm số lần gửi / thông báo. | read |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |

---

## a_mtres

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Kết quả bảo trì. Mỗi row là kết quả của một instance lịch đã thực hiện (khóa bởi `mts_uid`). Lưu chi tiết công việc, tên nhân viên, ngày thực hiện thực tế, chi phí, và tối đa 15 file đính kèm. Trên `sch.php` bảng này được join qua LEFT JOIN chỉ để lấy dữ liệu hiển thị — **không điều khiển bất kỳ icon nào** trên calendar.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mts_uid` | int(11) NOT NULL | ID instance lịch (app-FK → `a_mtsch.mts_uid`). Composite PK với `bkid`. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row kết quả duy nhất toàn cầu. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_detail` | varchar(512) | Mô tả công việc đã thực hiện. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_failure` | varchar(512) | Mô tả hỏng hóc / lỗi. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_gensyo` | varchar(512) | Mô tả triệu chứng (現象). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_genin` | varchar(512) | Mô tả nguyên nhân gốc rễ (原因). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_file1`–`mtr_file15` | varchar(128) ×15 | Đường dẫn file đính kèm (15 slot). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtre_date` / `mtrs_date` | datetime | Datetime kết thúc / bắt đầu công việc (thực tế). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtrs2_date` / `mtre2_date` | datetime | Khoảng thời gian làm việc thứ cấp bắt đầu/kết thúc. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `fins_date` / `fine_date` | datetime | Ngày kiểm tra cuối bắt đầu/kết thúc. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_cost` | int(11) | Chi phí thực tế. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_cost2` | int(11) | Trường chi phí thứ cấp (ví dụ chi phí linh kiện). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `del_flg` | tinyint(1) | Cờ xóa mềm. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_import_flg` | char(1) | Cờ nhập từ nguồn ngoài. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_mt_name` | varchar(64) | Tên nhiệm vụ denormalized (snapshot tại thời điểm tạo kết quả). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_purpose` | varchar(128) | Mục đích denormalized (snapshot). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `o_s_date` / `o_e_date` | datetime | Snapshot cửa sổ lịch gốc (sao chép từ `a_mtsch` tại thời điểm tạo kết quả). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_stat` | char(1) | Mã trạng thái kết quả. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_main_stfname` | varchar(16) | Tên nhân viên chính denormalized. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_sub_stfname` | varchar(128) | Tên nhân viên phụ denormalized. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_wktime` | int(11) | Thời gian làm việc thực tế (phút). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_matsuo` | blob | Blob dữ liệu bổ sung. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_fixtime` | smallint(6) | Thời gian cố định được phân bổ. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `upload` | tinyint(1) | `1` nếu kết quả có file đính kèm. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_mitei` | tinyint(1) | Cờ kết quả tạm thời / chưa xác nhận. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_stat1`–`mtr_stat5` | smallint(6) ×5 | Mã cờ trạng thái tùy chỉnh 1–5. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_stats1` / `mtr_stats2` / `mtr_stats3` | varchar | Trường chuỗi trạng thái tùy chỉnh. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_downtime` | int(11) | Thời gian ngừng hoạt động thiết bị do BT này gây ra (phút). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `mtr_estimate_kbn` | varchar(64) | Mã loại báo giá denormalized. |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |
| `p_mts_uid` | int(11) | UID lịch cha (dành cho kết quả liên kết / con). |  |  | read | read/write | read/write | read |  |  |  |  |  |  |  |  |  |  |  |

---

## a_factory

*Được dùng tại: [[Lịch bảo trì#Phân cấp địa điểm / tổ chức|Lịch bảo trì (VI)]], [[Phân quyền master#Phân cấp địa điểm / tổ chức|Phân quyền master]]*

**Vai trò:** Master nhà máy. Nhóm địa điểm cấp cao nhất. Mỗi thiết bị có `fc_id` là app-level FK trỏ vào bảng này. Dùng để populate dropdown lọc nhà máy trên `sch.php`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `fc_id` | varchar(16) NOT NULL | Mã nhà máy (business PK). Composite PK với `bkid`. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `fc_name` | varchar(64) | Tên hiển thị nhà máy. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `area_id` | smallint(6) | ID khu vực (app-FK → `a_area.area_id`). | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `fc_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row nhà máy duy nhất toàn cầu. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |
| `fc_del` | tinyint(1) | Cờ xóa mềm. | read | read | read | read | read | read |  |  | read | read/write |  |  |  | read |  |  | read |

---

## a_line

*Được dùng tại: [[Lịch bảo trì#Phân cấp địa điểm / tổ chức|Lịch bảo trì (VI)]]*

**Vai trò:** Master dây chuyền. Địa điểm con trong một nhà máy, khóa bởi `bkid + line_id + fc_id`. Dùng cho dropdown lọc dây chuyền và hiển thị như nhãn cột trong lưới calendar.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `line_id` | varchar(8) NOT NULL | Mã dây chuyền. Phần của composite PK. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `fc_id` | varchar(16) NOT NULL | Mã nhà máy (app-FK → `a_factory.fc_id`). Phần của composite PK. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `line_name` | varchar(32) | Tên hiển thị dây chuyền. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `line_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row dây chuyền duy nhất toàn cầu. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `old_names` | text | Danh sách tên cũ (lịch sử đổi tên). | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |
| `line_del` | tinyint(1) | Cờ xóa mềm. | read | read | read | read | read | read |  |  |  | read/write |  | read |  |  |  |  | read |

---

## holidays

*Được dùng tại: [[Lịch bảo trì#Hỗ trợ hiển thị calendar|Lịch bảo trì (VI)]]*

**Vai trò:** Danh sách ngày lễ công cộng. `sch.php` đọc bảng này để tô màu hồng cho cột header ngày lễ trong lưới calendar. Chia sẻ chung cho mọi tenant (không có `bkid`).

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `hday` | date NOT NULL | Ngày lễ (PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `hname` | varchar(32) | Tên ngày lễ (tiếng Nhật). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## datamaster

*Được dùng tại: [[Lịch bảo trì#Hỗ trợ hiển thị calendar|Lịch bảo trì (VI)]], [[Phân quyền master#Hỗ trợ hiển thị|Phân quyền master]]*

**Vai trò:** Master giá trị dropdown tổng quát. Lưu danh sách tên item theo `propid` (ví dụ `mtinfo_kbn`, `eq_kbn`) với nhãn bốn ngôn ngữ. Đọc qua `ppes_master()` để chuyển đổi mã số nguyên → tên hiển thị theo ngôn ngữ của người dùng. Chia sẻ chung cho mọi tenant (không có `bkid`).

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `propid` | varchar(16) NOT NULL | Định danh danh sách / thuộc tính (ví dụ `'mtinfo_kbn'`). Phần của composite PK. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |
| `itid` | smallint(6) NOT NULL | Mã item trong danh sách. Phần của composite PK. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |
| `itname` | varchar(32) | Tên hiển thị tiếng Nhật. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |
| `itname_en` | varchar(32) | Tên hiển thị tiếng Anh. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |
| `itname_cn` | varchar(32) | Tên hiển thị tiếng Trung. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |
| `itname_vn` | varchar(32) | Tên hiển thị tiếng Việt. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read | read |

---

## bk_infos

*Được dùng tại: [[Lịch bảo trì#Dữ liệu widget trang|Lịch bảo trì (VI)]]*

**Vai trò:** Thông báo theo tenant. Row thuộc về một `bkid` cụ thể. Hiển thị trong widget **【お知らせ】** trên trang lịch / TOP. Hỗ trợ tiêu đề/nội dung bốn ngôn ngữ và có thể giới hạn hiển thị theo nhà máy.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `inf_id`. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_id` | int(11) NOT NULL | ID thông báo. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_title` | varchar(255) | Tiêu đề (tiếng Nhật). |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_title_en` / `inf_title_cn` / `inf_title_vn` | varchar(255) ×3 | Tiêu đề tiếng Anh / Trung / Việt. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_conts` | text | Nội dung (tiếng Nhật). |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_conts_en` / `inf_conts_cn` / `inf_conts_vn` | text ×3 | Nội dung tiếng Anh / Trung / Việt. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_date` | date | Ngày đăng hiển thị trong widget. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_hidden` | tinyint(1) | `1` = bản nháp / ẩn khỏi người dùng. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `fc_ids` | varchar(128) | Danh sách `fc_id` cách nhau bằng dấu phẩy. Khi có giá trị, thông báo chỉ hiển thị cho các nhà máy đó. Rỗng = hiển thị cho tất cả. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `inf_file1`–`inf_file5` | varchar(64) ×5 | Đường dẫn file đính kèm. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên tạo thông báo. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |
| `del_flg` | tinyint(1) | Cờ xóa mềm. |  |  | read |  |  |  | read/write |  |  |  |  |  |  |  |  |  |  |

---

## infos

*Được dùng tại: [[Lịch bảo trì#Dữ liệu widget trang|Lịch bảo trì (VI)]]*

**Vai trò:** Thông báo toàn hệ thống, đăng từ `/padmin/`. Hiển thị trong widget info cùng với các entry của `bk_infos`. Không có `bkid` — hiển thị cho tất cả tenant. Chỉ super-admin mới quản lý được.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `inf_id` | int(11) NOT NULL | ID thông báo hệ thống (PK). |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `inf_title` | varchar(128) | Tiêu đề thông báo. |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `inf_conts` | text | Nội dung thông báo. |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `inf_date` | date | Ngày đăng. |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `inf_hidden` | tinyint(1) | `1` = ẩn trên các trang tenant. |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `is_web` | tinyint(1) | `1` = cũng hiển thị trên website công khai. |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `web_hidden` | tinyint(1) | `1` = ẩn trên website. |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  | read |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## buscomps

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Auth / session context|Phân quyền master]]*

**Vai trò:** Registry tenant. Nằm trong `zaikodb` (database super-admin). Mỗi row là một công ty tenant. Được đọc khi đăng nhập để xác định tenant, xác minh credentials, kiểm tra feature flag (`use_api`, `spe_*`, `use_*`), và resolve `bkid`. Được quản lý từ `/padmin/`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | bigint(20) unsigned AUTO_INCREMENT | Tenant ID — duy nhất toàn cầu, tự tăng. Đây là `bkid` được dùng trong tất cả bảng của tenant. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `bcid` | int(11) | ID sequence công ty. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `email` | varchar(64) | Email liên hệ chính. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `passwd` | varchar(16) | Mật khẩu admin đăng nhập (plain text). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `bcname` | varchar(64) | Tên công ty hiển thị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `bckana` | varchar(64) | Tên công ty theo kana. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `phone` / `fax` | varchar(16) | Điện thoại / fax liên hệ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `pc` | varchar(8) | Mã bưu chính. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `pref` | smallint(6) | Mã tỉnh/thành. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `addr` | varchar(96) | Địa chỉ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `bcstat` | char(1) | Trạng thái công ty (`'0'` = đang hoạt động). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `bikou` | text | Ghi chú nội bộ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `mng_name` / `mng_sect` | varchar | Tên / bộ phận quản lý. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `loginid` | varchar(16) | ID đăng nhập admin. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `subdom` | varchar(8) | Subdomain / mã đường dẫn URL tenant (ví dụ `ahihi` cho `/{ahihi}/sch.php`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `useplan` | char(1) | Mã gói đăng ký. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `startdate` / `enddate` | date | Ngày bắt đầu / kết thúc hợp đồng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `use_api` | tinyint(1) | Feature flag cho phép truy cập API. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `api_pw` | varchar(32) | Mật khẩu API. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `api_ips` | varchar(128) | Danh sách IP whitelist API (cách nhau bằng dấu phẩy). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `use_map` | tinyint(1) | Feature flag bản đồ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `use_shift` | tinyint(1) | Feature flag quản lý ca làm việc. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `use_ura` | tinyint(1) | Feature flag tính năng back-end (裏). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `use_uacj` / `use_ver2` | tinyint(1) | Feature toggle bổ sung. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `spe_17_1` / `spe_19_1` / `spe_14_1` | tinyint(1) | Feature flag đặc biệt theo tenant. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `regtime` | int(11) | Unix timestamp đăng ký. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `cmail` / `cstype` | varchar | Email CS liên hệ / loại hỗ trợ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `dis_syukei` / `dis_seikyu` | tinyint(1) | Vô hiệu hóa tính năng tổng hợp / thanh toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |
| `yk_count` / `uk_count` / `car_count` / `ag_count` / `drv_count` | int / smallint | Giới hạn số lượng / license theo tính năng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read |  |

---

## bk_staff

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Auth / session context|Phân quyền master]]*

**Vai trò:** Tài khoản nhân viên theo tenant. Mỗi row là một nhân viên của một tenant. Được `aspUser->openUser()` kiểm tra để xác thực session cookie và resolve `bkid` + `stf_id`. Cột `lang` xác định ngôn ngữ hiển thị cho người dùng đó.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `stf_id`. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `stf_id` | int(11) NOT NULL | ID nhân viên. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `stf_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row nhân viên duy nhất toàn cầu. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `login` | varchar(64) | Tên đăng nhập. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `passwd` | varchar(16) | Mật khẩu đăng nhập (plain text). | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `email` | varchar(64) | Địa chỉ email nhân viên. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `name` | varchar(64) | Tên hiển thị trong UI. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `level` | varchar(4) | Mã cấp quyền (dùng kết hợp với `a_auths`). | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `lang` | char(1) | Ngôn ngữ hiển thị: `'0'`=Nhật, `'1'`=Anh, `'2'`=Trung, `'3'`=Việt. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `fc_id` | varchar(16) | Nhà máy chính được phân công. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `fc_ids` | varchar(128) | Danh sách truy cập đa nhà máy (các `fc_id` cách nhau bằng dấu phẩy). | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `area_id` | smallint(6) | Phân công khu vực. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `sec_name` | varchar(32) | Tên phòng ban / bộ phận. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `phone` | varchar(32) | Số điện thoại. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `stf_order` | smallint(6) | Thứ tự hiển thị. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `sigfile` | varchar(32) | Đường dẫn file chữ ký. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `is_tbt` | tinyint(1) | Cờ super-admin TBT. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `lastlogin` | int(11) | Unix timestamp lần đăng nhập cuối. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |
| `bs_delete` | tinyint(1) | Cờ xóa mềm. | read |  |  | read | read | read |  |  | read/write |  |  |  |  |  |  |  | read |

---

## bkmasters

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Auth / session context|Phân quyền master]]*

**Vai trò:** Cấu hình tenant. Mỗi row ứng với một `bkid`. Lưu tên công ty, cài đặt giờ làm việc, tùy chọn hiển thị, cấu hình thanh toán, và các cài đặt cấp tenant được dùng trên tất cả trang. Được đọc sớm trong vòng đời request để áp dụng hành vi đặc thù của tenant.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant ID (PK). | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `compname` | varchar(64) | Tên công ty hiển thị trong header UI. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `license` | varchar(64) | Định danh giấy phép. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `addr1` / `addr2` | varchar(128) | Dòng địa chỉ công ty 1 và 2. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `pc` | varchar(8) | Mã bưu chính. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `tel` / `fax` | varchar(16) | Điện thoại / fax công ty. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `pref` | varchar(2) | Mã tỉnh/thành. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `ceo` | varchar(32) | Tên giám đốc (dùng in tài liệu). | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `mng1`–`mng3` | varchar(32) ×3 | Các slot tên quản lý 1–3. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `mng_mail` | varchar(64) | Email quản lý (dùng gửi thông báo alert). | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `wk_start` / `wk_end` | smallint(6) | Giờ bắt đầu / kết thúc ngày làm việc (số nguyên 0–23). | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `wktime` | smallint(6) | Thời gian làm việc tiêu chuẩn mỗi ngày (phút). | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `use_alert` | tinyint(1) | `1` = bật thông báo email alert. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `def_term` | smallint(6) | Độ dài kỳ hạn lịch mặc định (tháng). | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `rests` | varchar(64) | Chuỗi cấu hình thời gian nghỉ. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `sk_kuri` | tinyint(1) | Cài đặt chuyển tiếp lịch. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `hide_eqid` | tinyint(1) | `1` = ẩn cột ID thiết bị trong danh sách. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `del_disable` | tinyint(1) | `1` = ngăn xóa record bảo trì. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `dl_char` | char(1) | Mã hóa ký tự khi tải CSV (`'0'`=UTF-8, `'1'`=Shift-JIS). | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `tana_date1` / `tana_date2` | varchar(8) | Cài đặt ngày chu kỳ kiểm kê. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | write |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |

---

## a_auths

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Danh mục quyền|Phân quyền master]]*

**Vai trò:** Nhóm quyền tính năng. Mỗi row là một nhóm quyền của một tenant. `aspUser->setAuth($db, $request, $authId)` đọc bảng này để kiểm tra xem nhóm của người dùng hiện tại có quyền xem hoặc chỉnh sửa một tính năng cụ thể trên trang hiện tại không. Mỗi cờ `at_N` ánh xạ đến một tính năng cụ thể.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `at_id`. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_id` | smallint(6) NOT NULL | ID nhóm quyền. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_name` | varchar(16) | Tên hiển thị nhóm (ví dụ `管理者`, `一般`). |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_all` | tinyint(1) | `1` = cấp tất cả quyền (override các cờ riêng lẻ). |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_notall` | tinyint(1) | `1` = chỉ được truy cập record của chính mình. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_params` | varchar(128) | Chuỗi tham số bổ sung cho override quyền chi tiết. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_1` | char(1) | Cờ quyền tính năng 1. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_2` | char(1) | Cờ quyền tính năng 2. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_3` | char(1) | Cờ quyền tính năng 3. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_10` | char(1) | Cờ quyền tính năng 10. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_11` | char(1) | Cờ quyền tính năng 11. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_12` | char(1) | Cờ quyền tính năng 12 (trang lịch). |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_13` | char(1) | Cờ quyền tính năng 13. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_14` | char(1) | Cờ quyền tính năng 14. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `at_15` | char(1) | Cờ quyền tính năng 15. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `authid` / `authpass` | varchar(16) | Credentials xác thực ngoài (nếu có). |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  | read/write | read |  |  |  |  |  |  |  |  |

---

## a_eqgroup

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]]*

**Vai trò:** Master nhóm thiết bị. Nhóm các thiết bị để phân loại. Được dùng cho bộ lọc `eqg_id` trên form tìm kiếm của `sch.php`. Các row thiết bị tham chiếu bảng này qua `a_equips.eqg_id`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `eqg_id`. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `eqg_id` | smallint(6) NOT NULL | ID nhóm thiết bị (PK). | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `eqg_name` | varchar(32) | Tên hiển thị nhóm. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `eqg_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row nhóm duy nhất toàn cầu. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `nfc_id` | varchar(16) | ID thẻ NFC liên kết với nhóm này (dành cho tính năng quét di động). | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `eqg_data` | blob | Blob dữ liệu cấu hình nhóm bổ sung. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read |  |  | read | read |  |  |  |  |  | read/write | read |  |  |  |  |  |

## a_area

*Được dùng tại: [[Phân quyền master#Phân cấp địa điểm / tổ chức|Phân quyền master]]*

**Vai trò:** Master khu vực. Nhóm địa lý cấp cao nhất, nằm trên nhà máy trong phân cấp địa điểm. Mỗi row nhà máy có `area_id` là app-level FK trỏ vào bảng này. Tài khoản nhân viên cũng có thể mang `area_id` để phân quyền truy cập. Được dùng trên `auth.php` như một lookup hỗ trợ cho UI nhà máy/nhân viên — không phải parent cấu trúc trực tiếp của `a_auths`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `area_id`. | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |
| `area_id` | smallint(6) NOT NULL | ID khu vực (business PK). | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |
| `area_name` | varchar(32) | Tên hiển thị khu vực. | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |
| `area_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row khu vực duy nhất toàn cầu. | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read |  |  |  |  |  |  | read | read | read/write | read |  | read |  |  |  |  |

---

## a_ckgroup

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master nhóm kiểm tra. Nhóm nhiều hạng mục kiểm tra (`a_ckitem`) thành một nhóm có tên, có thể gán cho thiết bị trong quy trình kiểm định. Mỗi nhóm thuộc phạm vi tenant và được sắp xếp theo `disporder`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `ckg_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ckg_id` | smallint(6) NOT NULL | ID nhóm kiểm tra (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ckg_name` | varchar(32) | Tên hiển thị nhóm kiểm tra. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị trong danh sách. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_ckgroup_detail

*Được dùng tại: (nhiều trang)*

**Vai trò:** Bảng liên kết nhóm kiểm tra ↔ hạng mục kiểm tra. Liên kết các hạng mục kiểm tra riêng lẻ với nhóm kiểm tra cha. Cờ `required_flg` đánh dấu các hạng mục bắt buộc phải điền khi kiểm định.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ckg_id` | smallint(6) NOT NULL | ID nhóm kiểm tra (app-FK → `a_ckgroup.ckg_id`). Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ck_id` | smallint(6) NOT NULL | ID hạng mục kiểm tra (app-FK → `a_ckitem.ck_id`). Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `required_flg` | tinyint(1) | Cờ bắt buộc. `1` = hạng mục phải được điền khi kiểm định. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_ckitem

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master hạng mục kiểm tra. Định nghĩa các hạng mục kiểm tra / kiểm định riêng lẻ với tên, loại (đo lường số, lựa chọn, v.v.), giá trị min/max/cơ sở chấp nhận cho kiểm tra số, và các tùy chọn có thể chọn cho hạng mục dạng dropdown.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `ck_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ck_id` | smallint(6) NOT NULL | ID hạng mục kiểm tra (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ck_name` | varchar(32) | Tên hiển thị hạng mục kiểm tra. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ck_type` | varchar(2) | Mã loại hạng mục (số, lựa chọn, v.v.). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `max_size` | float | Giá trị tối đa chấp nhận (cho kiểm tra số). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `min_size` | float | Giá trị tối thiểu chấp nhận (cho kiểm tra số). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `base_size` | float | Giá trị cơ sở / tiêu chuẩn (cho kiểm tra số). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `select_item` | text | Danh sách tùy chọn có thể chọn (cho hạng mục dạng dropdown). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị trong danh sách. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_eqgroup_detail

*Được dùng tại: (nhiều trang)*

**Vai trò:** Bảng liên kết nhóm thiết bị ↔ trường tùy chỉnh thiết bị. Kiểm soát trường tùy chỉnh động nào (`a_eqitem`) hiển thị trên form chỉnh sửa thiết bị của một nhóm thiết bị cụ thể. `required_flg` đánh dấu trường bắt buộc; `record_flg` kiểm soát hiển thị trong chế độ xem lịch sử / bản ghi.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqg_id` | smallint(6) NOT NULL | ID nhóm thiết bị (app-FK → `a_eqgroup.eqg_id`). Phần của composite PK. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqitem_id` | smallint(6) NOT NULL | ID trường tùy chỉnh (app-FK → `a_eqitem.eqitem_id`). Phần của composite PK. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `required_flg` | tinyint(1) | Cờ bắt buộc. `1` = trường phải được điền. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `record_flg` | tinyint(1) | Cờ hiển thị bản ghi. `1` = hiển thị trong chế độ xem lịch sử. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `ecgd_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị trong danh sách. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |

---

## a_eqhist

*Được dùng tại: (nhiều trang)*

**Vai trò:** Lịch sử / nhật ký thay đổi thiết bị. Ghi nhận các mục văn bản có dấu thời gian cho mỗi thiết bị, khóa bởi `eq_id` + `eq_time` (unix timestamp). Dùng để theo dõi lịch sử chuyển giao, thay đổi trạng thái, và ghi chú của vận hành viên.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) NOT NULL | ID thiết bị (app-FK → `a_equips.eq_id`). Phần của composite PK. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_time` | int(11) NOT NULL | Unix timestamp sự kiện. Phần của composite PK. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_conts` | text | Nội dung mô tả sự kiện / thay đổi. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_eqitem

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master định nghĩa trường tùy chỉnh thiết bị. Định nghĩa các trường động trên form chỉnh sửa thiết bị. Mỗi row xác định tên trường, loại input, regex xác thực, tùy chọn có thể chọn, ràng buộc min/max, và màu hiển thị. Hỗ trợ nhãn song ngữ (JP + EN). Liên kết đến nhóm thiết bị qua `a_eqgroup_detail`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `eqitem_name`. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqitem_name` | varchar(32) NOT NULL | Tên trường tùy chỉnh (business PK). | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqitem_id` | smallint(6) | ID trường tùy chỉnh (dùng liên kết với `a_eqgroup_detail`). | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqitem_type` | varchar(32) | Mã loại input (text, select, number, v.v.). | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `select_item` | text | Danh sách tùy chọn có thể chọn (tiếng Nhật). | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqitem_regexp` | varchar(128) | Biểu thức chính quy xác thực giá trị. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqitem_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị trong danh sách. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `max_size` | int(11) | Giá trị tối đa cho ràng buộc. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `min_size` | int(11) | Giá trị tối thiểu cho ràng buộc. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `eqitem_name_en` | varchar(32) | Tên trường tiếng Anh. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `select_item_en` | text | Danh sách tùy chọn có thể chọn (tiếng Anh). | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |
| `item_color` | varchar(16) | Mã màu hiển thị trường. | read | read |  | read | read |  |  |  |  |  | read/write | read/write |  |  |  |  |  |

---

## a_eqpoint

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master điểm kiểm tra thiết bị. Định nghĩa các điểm đo lường / kiểm tra được đặt tên trên một thiết bị.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `po_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `po_id` | varchar(32) NOT NULL | Mã điểm kiểm tra (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) | ID thiết bị (app-FK → `a_equips.eq_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `po_name` | varchar(64) | Tên hiển thị điểm kiểm tra. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `is_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fld1` | varchar(64) | Trường dự trữ 1. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fld2` | varchar(32) | Trường dự trữ 2. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fld3` | varchar(16) | Trường dự trữ 3. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eqp_order` | smallint(6) | Thứ tự hiển thị điểm kiểm tra. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_eqstocks

*Được dùng tại: (nhiều trang)*

**Vai trò:** Bảng liên kết thiết bị ↔ tồn kho. Liên kết hạng mục tồn kho với thiết bị tại một nhà máy cụ thể. Cột `tana` lưu thông tin kệ / vị trí lưu trữ.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `hin_id` | varchar(64) NOT NULL | Mã hạng mục tồn kho (app-FK → `a_stocks.hin_id`). Phần của composite PK. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) NOT NULL | ID thiết bị (app-FK → `a_equips.eq_id`). Phần của composite PK. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) NOT NULL | Mã nhà máy (app-FK → `a_factory.fc_id`). Phần của composite PK. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `tana` | varchar(32) | Vị trí kệ / nơi lưu trữ. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_equips_detail

*Được dùng tại: (nhiều trang)*

**Vai trò:** Giá trị trường tùy chỉnh thiết bị. Lưu giá trị thực tế của mỗi trường động cho một thiết bị cụ thể.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `eq_id` | int(11) NOT NULL | ID thiết bị (app-FK → `a_equips.eq_id`). Phần của composite PK. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `eqitem_id` | int(11) NOT NULL | ID trường tùy chỉnh (app-FK → `a_eqitem.eqitem_id`). Phần của composite PK. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `eqd_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `eqd_val` | varchar(300) | Giá trị thực tế của trường tùy chỉnh. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `create_date` | datetime | Datetime tạo row. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read/write |  |  | read | read |  |  |  |  |  |  | read/write |  |  |  |  | read/write |

---

## a_files

*Được dùng tại: (nhiều trang)*

**Vai trò:** Đăng ký file đính kèm tổng quát. Thiết kế đa hình — `f_type` xác định loại thực thể cha, `f_id` là ID record cha, `f_num` là số slot.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `f_type` | varchar(16) NOT NULL | Loại thực thể cha (ví dụ thiết bị, bảo trì, v.v.). Phần của composite PK. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `f_id` | int(11) NOT NULL | ID record cha. Phần của composite PK. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `f_num` | varchar(8) NOT NULL | Số slot file. Phần của composite PK. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `f_name` | varchar(128) NOT NULL | Tên file gốc. Phần của composite PK. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `f_size` | int(11) | Kích thước file (bytes). | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `f_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read/write |  |  | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_floor

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master tầng. Cấp thứ ba trong phân cấp địa điểm: Khu vực → Nhà máy → Dây chuyền → Tầng. Các row thiết bị có thể tham chiếu qua `flr_id`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) NOT NULL | Mã nhà máy (app-FK → `a_factory.fc_id`). Phần của composite PK. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `line_id` | varchar(8) NOT NULL | Mã dây chuyền (app-FK → `a_line.line_id`). Phần của composite PK. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `flr_id` | varchar(16) NOT NULL | Mã tầng (business PK). Phần của composite PK. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `flr_name` | varchar(32) | Tên hiển thị tầng. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |
| `flr_order` | int(11) | Thứ tự hiển thị tầng. | read |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |  |

---

## a_mailtmpl

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master mẫu email. Lưu các mẫu email được sử dụng trong quy trình thông báo bảo trì và cho thuê. Mỗi mẫu có tiêu đề, chủ đề, nội dung với token thay thế, và mã loại.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `mtid`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `mtid` | smallint(6) NOT NULL | ID mẫu email (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `title` | varchar(32) | Tiêu đề nội bộ của mẫu. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `subject` | varchar(64) | Chủ đề email. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `body` | text | Nội dung email với token thay thế. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `mtype` | char(1) | Mã loại mẫu. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |  |  |

---

## a_maker

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master nhà sản xuất / nhà cung cấp. Lưu thông tin liên hệ nhà sản xuất thiết bị và nhà cung cấp bao gồm liên hệ trực tiếp và liên hệ qua đại lý. Xóa mềm qua `mk_del`. Được thiết bị tham chiếu qua `a_equips.mat_mk_id`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `mk_id`. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_id` | varchar(16) NOT NULL | Mã nhà sản xuất (business PK). Collation `utf8mb4_bin`. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_name` | varchar(64) | Tên hiển thị nhà sản xuất. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_phone` | varchar(32) | Số điện thoại nhà sản xuất. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_fax` | varchar(32) | Số fax nhà sản xuất. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_tanto` | varchar(32) | Tên người phụ trách liên hệ trực tiếp. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_tanto_phone` | varchar(32) | Điện thoại người phụ trách trực tiếp. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_ag_phone` | varchar(32) | Số điện thoại đại lý. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_ag_fax` | varchar(32) | Số fax đại lý. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_ag_tanto_phone` | varchar(32) | Điện thoại người phụ trách đại lý. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_ag_tanto` | varchar(32) | Tên người phụ trách đại lý. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_ag_name` | varchar(32) | Tên đại lý. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_kana` | varchar(64) | Tên kana nhà sản xuất (phiên âm). | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |
| `mk_mail` | varchar(64) | Địa chỉ email nhà sản xuất. | read | read |  | read | read |  |  | read/write |  |  |  | read | read/write |  |  |  |  |

---

## a_mtbf

*Được dùng tại: (nhiều trang)*

**Vai trò:** Chỉ số độ tin cậy MTBF / MTTR. Thống kê Mean Time Between Failures và Mean Time To Repair được tính trước cho mỗi thiết bị theo từng kỳ tài chính.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) NOT NULL | ID thiết bị (app-FK → `a_equips.eq_id`). Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ft_id` | smallint(6) NOT NULL | ID kỳ tài chính. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ttltime` | int(11) | Tổng thời gian trong kỳ (phút). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `downtime` | int(11) | Tổng thời gian ngừng hoạt động (phút). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `livetime` | int(11) | Tổng thời gian hoạt động (phút). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fails` | int(11) | Số lần hỏng hóc trong kỳ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mtbf` | int(11) | Giá trị MTBF tính trước (phút). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mttr` | int(11) | Giá trị MTTR tính trước (phút). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `startdate` | datetime | Ngày bắt đầu kỳ tính toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `enddate` | datetime | Ngày kết thúc kỳ tính toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_rent

*Được dùng tại: (nhiều trang)*

**Vai trò:** Bản ghi cho thuê / mượn thiết bị. Theo dõi việc cho mượn thiết bị với khoảng thời gian đặt chỗ, thông tin người mượn, quy trình phê duyệt, và cài đặt nhắc nhở trả.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) NOT NULL | ID thiết bị (app-FK → `a_equips.eq_id`). Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `start_date` | datetime NOT NULL | Ngày bắt đầu cho mượn. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `end_date` | datetime | Ngày kết thúc cho mượn. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_id` | int(11) | ID bản ghi cho thuê. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_stat` | char(1) | Mã trạng thái cho thuê. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_stf` | smallint(6) | ID nhân viên tạo bản ghi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `auth_stf` | smallint(6) | ID nhân viên phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `auth_date` | datetime | Ngày phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rt_auth_stat` | smallint(6) | Mã trạng thái phê duyệt cho thuê. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_stf` | smallint(6) | ID nhân viên mượn thiết bị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_mail` | varchar(64) | Email người mượn. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_phone` | varchar(32) | Điện thoại người mượn. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_sec` | varchar(32) | Phòng ban người mượn. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_purpose` | varchar(32) | Mục đích mượn thiết bị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_memo` | varchar(255) | Ghi chú cho thuê. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_ngres` | char(1) | Mã lý do từ chối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rent_remday` | smallint(6) | Số ngày trước khi nhắc nhở trả. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `is_rent` | tinyint(1) | Cờ đánh dấu đang cho mượn. `1` = đang mượn. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_stocks

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master tồn kho / phụ tùng. Mỗi row là một hạng mục tồn kho tại một nhà máy. Theo dõi số lượng hiện tại, mức tồn kho an toàn, ngưỡng cảnh báo, phân loại tồn kho, vị trí kệ, và thiết bị liên quan.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `hin_id` | varchar(100) NOT NULL | Mã hạng mục tồn kho (business PK). Phần của composite PK. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `hin_name` | varchar(256) | Tên hiển thị hạng mục tồn kho. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_num` | float | Số lượng hiện tại. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_num_safe` | float | Mức tồn kho an toàn. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_num_warn` | float | Ngưỡng cảnh báo tồn kho. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stock_kbn` | char(1) | Mã phân loại tồn kho. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) NOT NULL | Mã nhà máy (app-FK → `a_factory.fc_id`). Phần của composite PK. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_date` | date | Ngày cập nhật tồn kho. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mak_name` | varchar(32) | Tên nhà sản xuất (denormalized). | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_file1` | varchar(64) | Đường dẫn file đính kèm. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_start` | int(11) | Số lượng tồn kho đầu kỳ. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_time` | datetime | Datetime cập nhật tồn kho gần nhất. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_tank` | int(11) | Dung tích thùng / bể chứa. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_tana` | varchar(32) | Vị trí kệ / nơi lưu trữ. | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_eq_id` | int(11) | ID thiết bị liên quan (app-FK → `a_equips.eq_id`). | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stk_eq_names` | blob | Blob tên thiết bị liên quan (denormalized). | read/write | read/write |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## a_tana

*Được dùng tại: (nhiều trang)*

**Vai trò:** Bản ghi kiểm kê vật lý (棚卸). Mỗi row là một lần quét thiết bị theo năm tài chính (`nendo`). Ghi nhận xác minh vật lý sự tồn tại của thiết bị. Hỗ trợ RFID qua cột `epc`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `s_code` | varchar(32) NOT NULL | Mã thiết bị quét được. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `read_date` | date | Ngày đọc / quét. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `s_name` | varchar(32) | Tên thiết bị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `s_area` | varchar(32) | Khu vực đặt thiết bị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `tana_kumi` | varchar(32) | Nhóm / tổ kiểm kê. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `s_sect` | varchar(32) | Bộ phận. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `s_stf` | varchar(16) | Mã nhân viên phụ trách. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `s_cmt` | varchar(64) | Ghi chú kiểm kê. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `create_stf` | smallint(6) | ID nhân viên tạo bản ghi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `read_flg` | tinyint(1) | Cờ đã đọc / quét. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `mat_mcno_ok` | tinyint(1) | Cờ xác nhận số model máy khớp. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `kumi_ok` | tinyint(1) | Cờ xác nhận nhóm / tổ khớp. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `tgt_flg` | tinyint(1) | Cờ đánh dấu là đối tượng kiểm kê. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `m_eqv12` | varchar(32) | Giá trị trường tùy chỉnh thiết bị 12 (snapshot). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `m_mat_nensiki` | varchar(8) | Năm sản xuất (snapshot). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `m_eq_id` | int(11) | ID thiết bị (app-FK → `a_equips.eq_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `nendo` | varchar(6) NOT NULL | Năm tài chính. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `upload_time` | int(11) | Unix timestamp lần upload. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `epc` | varchar(32) | Mã EPC của thẻ RFID. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `a_stf` | varchar(16) | Mã nhân viên khu vực. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `stf_ok` | char(1) | Cờ xác nhận nhân viên. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `m_adm` | varchar(16) | Mã quản trị viên (snapshot). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `tck` | tinyint(1) | Cờ kiểm tra xác nhận. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `ck_stf` | smallint(6) | ID nhân viên xác nhận. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `tn_sid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |
| `s_area_flg` | tinyint(1) | Cờ xác nhận khu vực khớp. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | read/write |

---

## ads_master

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master giá trị dropdown theo tenant. Khác với `datamaster` (dùng chung toàn hệ thống), `ads_master` lưu danh sách dropdown riêng cho từng tenant. Các hạng mục có thể ẩn qua `ishidden`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `propid` | varchar(16) NOT NULL | Định danh danh sách / thuộc tính. Phần của composite PK. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `itid` | smallint(6) NOT NULL | Mã item trong danh sách. Phần của composite PK. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `itname` | varchar(32) | Tên hiển thị item. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `disporder` | smallint(6) | Thứ tự hiển thị. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `ishidden` | tinyint(1) | Cờ ẩn. `1` = ẩn khỏi dropdown. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `stf_id` | smallint(6) NOT NULL | ID nhân viên sửa đổi lần cuối. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `create_date` | datetime | Datetime tạo row. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. | read | read | read | read | read | read |  |  | read | read | read | read |  | read/write | read |  | read |

---

## bk_idmaster

*Được dùng tại: (nhiều trang)*

**Vai trò:** Bộ cấp ID tuần tự cấp ứng dụng. Lưu giá trị ID khả dụng tiếp theo cho các loại thực thể khác nhau theo từng tenant. `p_name` xác định loại thực thể, `p_val` chứa giá trị tiếp theo.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | int(11) NOT NULL | Khóa phân vùng tenant. Composite PK với `p_name`. |  |  |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |
| `p_name` | varchar(16) NOT NULL | Tên loại thực thể (ví dụ `'eq_id'`, `'mt_id'`). |  |  |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |
| `p_val` | int(11) | Giá trị ID khả dụng tiếp theo. |  |  |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  | read/write |  |  |  |  |  |  |

---

## busareas

*Được dùng tại: (nhiều trang)*

**Vai trò:** Phân công công ty ↔ khu vực. Bảng MyISAM legacy liên kết công ty tenant với mã khu vực. Không có primary key.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bcid` | int(11) | ID công ty (app-FK → `buscomps.bcid`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `areaid` | smallint(6) | Mã khu vực được phân công. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## bustypengdays

*Được dùng tại: (nhiều trang)*

**Vai trò:** Ngày không khả dụng (NG) theo loại kinh doanh. Bảng MyISAM legacy ghi nhận ngày bị chặn / không khả dụng theo loại kinh doanh. Dùng trong bối cảnh lập lịch / hoạch định năng lực.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bcid` | int(11) NOT NULL | ID công ty (app-FK → `buscomps.bcid`). Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ngdate` | date NOT NULL | Ngày không khả dụng. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `btype` | smallint(6) NOT NULL | Mã loại kinh doanh. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `daisu` | smallint(6) | Số lượng (thiết bị / đơn vị) bị ảnh hưởng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## calendars

*Được dùng tại: (nhiều trang)*

**Vai trò:** Sự kiện lịch / ghi chú theo tenant. Lưu nhận xét theo từng ngày cho mỗi tenant. Dùng kết hợp với `holidays` (toàn hệ thống) để chú thích lịch.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | int(11) NOT NULL | Khóa phân vùng tenant. Composite PK với `date`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `date` | date NOT NULL | Ngày sự kiện (PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `cmt` | varchar(128) | Nhận xét / ghi chú cho ngày. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## loginhist

*Được dùng tại: (nhiều trang)*

**Vai trò:** Nhật ký kiểm toán đăng nhập / đăng xuất. Ghi nhận mọi sự kiện xác thực với thời gian, IP remote, ID nhân viên, và cờ API.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `histid` | bigint(20) unsigned AUTO_INCREMENT | ID bản ghi lịch sử (PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `bkid` | smallint(6) | Khóa phân vùng tenant. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên đăng nhập. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `logout` | char(1) | Cờ đăng xuất. `'0'` = đăng nhập, `'1'` = đăng xuất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `acstime` | datetime | Thời gian sự kiện xác thực. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rip` | varchar(32) | Địa chỉ IP remote. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `is_api` | tinyint(1) | Cờ API. `1` = đăng nhập qua API. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## mail_master

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master người nhận email. Lưu địa chỉ email cho người nhận thông báo theo tenant, liên kết với mã nhân viên (`sya_id` → `syain_master`).

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `email`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `email` | varchar(64) NOT NULL | Địa chỉ email (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên liên kết (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_name` | varchar(16) | Tên nhân viên (denormalized). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_stf` | smallint(6) | ID nhân viên tạo bản ghi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ishidden` | tinyint(1) | Cờ ẩn. `1` = ẩn khỏi danh sách. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## myview

*Được dùng tại: (nhiều trang)*

**Vai trò:** Lịch bảo trì được đánh dấu cá nhân. Cho phép nhân viên đánh dấu các mục lịch bảo trì cụ thể để truy cập nhanh trong chế độ xem cá nhân.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `mts_uid`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mts_uid` | int(11) NOT NULL | ID instance lịch (app-FK → `a_mtsch.mts_uid`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên đánh dấu. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `name` | varchar(32) | Tên hiển thị / nhãn bookmark. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_item

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master hạng mục mua sắm (module CAPEX). Định nghĩa các hạng mục có thể mua sắm với đơn giá, tổng giá, số phiếu, danh mục kế toán, số phê duyệt liên kết. Là một phần của quy trình mua sắm theo dự án.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `item_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_name` | varchar(64) | Tên hạng mục mua sắm. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_tanka` | bigint(20) | Đơn giá. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_price` | bigint(20) | Tổng giá. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `den_ban` | varchar(32) | Số phiếu / chứng từ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `keiri_kbn` | varchar(4) | Mã danh mục kế toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_kind` | varchar(4) | Mã loại hạng mục. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_num` | varchar(16) | Số đơn hàng liên kết (app-FK → `p_purchase.p_num`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_ban` | varchar(32) | Số phê duyệt liên kết (app-FK → `p_ringi.rin_ban`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_date` | date | Ngày mua sắm. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mk_id` | varchar(16) | Mã nhà sản xuất (app-FK → `a_maker.mk_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên phụ trách (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_sid` | varchar(16) | Mã nhân viên sửa đổi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_sid` | varchar(16) | Mã nhân viên tạo. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `in_num` | smallint(6) | Số lượng nhập. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `out_num` | smallint(6) | Số lượng xuất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `item_id` | int(11) NOT NULL | ID hạng mục (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `item_name` | varchar(64) | Tên hạng mục (nhãn phụ). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | varchar(16) | ID nhân viên phụ trách. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `item_bikou` | varchar(255) | Ghi chú hạng mục. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `item_unit` | varchar(2) | Đơn vị tính. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `partkbn` | varchar(4) | Mã phân loại linh kiện. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rem_num` | smallint(6) | Số lượng còn lại. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `acd` | smallint(6) | Mã tài khoản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pi_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mk_id1` | varchar(16) | Mã nhà sản xuất phụ 1. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mk_id2` | varchar(16) | Mã nhà sản xuất phụ 2. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `cost1` | bigint(20) | Chi phí phụ 1. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `cost2` | bigint(20) | Chi phí phụ 2. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `siyousaki` | varchar(64) | Nơi sử dụng / đích đến. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_ids` | varchar(255) | Danh sách ID tài sản liên kết (cách nhau bằng dấu phẩy). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_mente

*Được dùng tại: (nhiều trang)*

**Vai trò:** Bản ghi bảo trì dự án (module CAPEX). Theo dõi các sự kiện bảo trì / sửa chữa liên kết với dự án và tài sản.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `pm_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pm_id` | int(11) NOT NULL | ID bản ghi bảo trì (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_id` | int(11) | ID đơn hàng liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pp_id` | int(11) | ID dự án phụ liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pm_date` | date | Ngày bảo trì. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pm_price` | int(11) | Chi phí bảo trì. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mk_id` | varchar(16) | Mã nhà sản xuất (app-FK → `a_maker.mk_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pm_naiyou` | text | Nội dung công việc bảo trì. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pm_bikou` | text | Ghi chú bảo trì. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên phụ trách (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_sid` | varchar(16) | Mã nhân viên sửa đổi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_sid` | varchar(16) | Mã nhân viên tạo. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_id` | int(11) | ID tài sản liên kết (app-FK → `p_sisan.si_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `m_file1` | varchar(64) | Đường dẫn file đính kèm 1. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `m_file2` | varchar(64) | Đường dẫn file đính kèm 2. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pm_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pm_mdate` | date | Ngày bảo trì phụ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `r_si_bans` | varchar(32) | Số tài sản liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `r_rin_bans` | varchar(32) | Số phê duyệt liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `r_p_nums` | varchar(32) | Số đơn hàng liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_proj

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master dự án (module CAPEX). Thực thể cấp cao nhất cho dự án chi phí vốn. Lưu tên dự án, ngân sách, nhà máy, và số lượng tổng hợp phê duyệt, mua sắm, và tài sản liên kết. Xóa mềm qua `is_del`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `prj_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `prj_id` | int(11) NOT NULL | ID dự án (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `prj_name` | varchar(64) | Tên dự án. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `prj_price` | bigint(20) | Ngân sách dự án. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên phụ trách (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_sid` | varchar(16) | Mã nhân viên sửa đổi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_sid` | varchar(16) | Mã nhân viên tạo. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `prj_date` | date | Ngày dự án. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `is_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `dm1`–`dm5` | varchar(32) ×5 | Trường dữ liệu tùy chỉnh 1–5. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ringi` | text | Dữ liệu phê duyệt liên kết (blob). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `purchase` | text | Dữ liệu mua sắm liên kết (blob). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_num` | smallint(6) | Số lượng phê duyệt liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_fin` | smallint(6) | Số phê duyệt đã hoàn thành. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_qty` | smallint(6) | Tổng số phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_qty` | smallint(6) | Tổng số đơn hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_fin` | smallint(6) | Số đơn hàng đã hoàn thành. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sisan` | text | Dữ liệu tài sản liên kết (blob). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sisan_txt` | varchar(255) | Tóm tắt tài sản (denormalized). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `purchase_txt` | varchar(512) | Tóm tắt mua sắm (denormalized). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ringi_txt` | varchar(255) | Tóm tắt phê duyệt (denormalized). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pb_qty` | smallint(6) | Số lượng phiếu thanh toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `k_qty` | smallint(6) | Số lượng kiểm tra. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_qty` | smallint(6) | Số lượng tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `r_file` | tinyint(1) | Cờ có file phê duyệt đính kèm. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_file` | tinyint(1) | Cờ có file mua sắm đính kèm. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `nouhin_fin` | smallint(6) | Số giao hàng đã hoàn thành. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rmk_txt` | varchar(255) | Tóm tắt ghi chú (denormalized). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `siban_txt` | varchar(512) | Tóm tắt số tài sản (denormalized). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_price` | bigint(20) | Tổng giá mua sắm thực tế. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_purchase

*Được dùng tại: (nhiều trang)*

**Vai trò:** Đơn đặt hàng (module CAPEX). Đại diện cho một đơn đặt hàng liên kết với dự án. Lưu số đơn hàng, ngày, mô tả, tổng giá, nhà sản xuất, nhà máy, thông tin giao hàng, và tối đa năm file đính kèm. Xóa mềm qua `p_del`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `p_num`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_id` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_num` | varchar(16) NOT NULL | Số đơn hàng (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `prj_id` | int(11) | ID dự án liên kết (app-FK → `p_proj.prj_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_date` | date | Ngày đặt hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_name` | varchar(64) | Tên / mô tả đơn hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_price` | bigint(20) | Tổng giá đơn hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mk_id` | varchar(16) | Mã nhà sản xuất (app-FK → `a_maker.mk_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên phụ trách (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_sid` | varchar(16) | Mã nhân viên sửa đổi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_sid` | varchar(16) | Mã nhân viên tạo. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `d_date` | date | Ngày giao hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `k_date` | date | Ngày kiểm tra. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `dai_id` | int(11) | ID đại lý. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sis_id` | varchar(16) | Mã tài sản liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sis_num` | varchar(16) | Số tài sản liên kết. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_ban` | varchar(16) | Số phê duyệt liên kết (app-FK → `p_ringi.rin_ban`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `paycon` | smallint(6) | Mã điều kiện thanh toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_file1`–`p_file5` | varchar(64) ×5 | Đường dẫn file đính kèm (5 slot). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_bikou` | text | Ghi chú đơn hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_reason` | text | Lý do mua sắm. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `in_date` | date | Ngày nhập kho. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `in_acd` | smallint(6) | Mã tài khoản nhập. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `bunai_rin` | char(1) | Cờ phê duyệt nội bộ phòng ban. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `s_date` | date | Ngày xuất hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `s_name` | varchar(64) | Tên hàng xuất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `s_bikou` | text | Ghi chú xuất hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `s_lot` | varchar(32) | Số lot xuất hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pk_date` | date | Ngày đóng gói. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ck_flg` | tinyint(1) | Cờ đã kiểm tra. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) | ID thiết bị liên kết (app-FK → `a_equips.eq_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `nouhin_flg` | tinyint(1) | Cờ đã giao hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_puritem

*Được dùng tại: (nhiều trang)*

**Vai trò:** Dòng chi tiết đơn đặt hàng (module CAPEX). Các dòng chi tiết của đơn đặt hàng, khóa theo số dòng. Mỗi dòng có mô tả, đơn giá, số lượng, tổng tiền, và đơn vị.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_num` | varchar(16) NOT NULL | Số đơn hàng (app-FK → `p_purchase.p_num`). Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `gyo_no` | smallint(6) NOT NULL | Số dòng. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_ttl` | bigint(20) | Tổng tiền dòng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_qty` | int(11) | Số lượng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_con` | varchar(64) | Mô tả nội dung dòng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_tan` | bigint(20) | Đơn giá. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_uni` | varchar(4) | Đơn vị tính. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_ringi

*Được dùng tại: (nhiều trang)*

**Vai trò:** Yêu cầu phê duyệt / 稟議 (module CAPEX). Yêu cầu phê duyệt chính thức cho chi phí vốn liên kết với dự án. Lưu số phê duyệt, số tiền, chi tiết kế toán, tối đa chín file đính kèm, và các chỉ số tài chính (hoàn vốn, NPV, IRR). Xóa mềm qua `r_del`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `rin_ban`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_id` | int(11) | ID phê duyệt (sequence). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_ban` | varchar(16) NOT NULL | Số phê duyệt (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_date` | date | Ngày nộp yêu cầu phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_kbn` | char(1) | Mã phân loại phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_title` | varchar(64) | Tiêu đề yêu cầu phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_price` | bigint(20) | Số tiền yêu cầu phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_sid` | varchar(16) | Mã nhân viên sửa đổi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_sid` | varchar(16) | Mã nhân viên tạo. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên phụ trách (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `prj_id` | int(11) | ID dự án liên kết (app-FK → `p_proj.prj_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_kei` | int(11) | Số tiền kế toán tích lũy. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `is_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_sagaku` | bigint(20) | Chênh lệch so với dự toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_est` | bigint(20) | Số tiền dự toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stdno` | smallint(6) | Số tiêu chuẩn. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `se_id` | smallint(6) | ID thiết lập. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `setres` | smallint(6) | Kết quả thiết lập. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `paycon` | smallint(6) | Mã điều kiện thanh toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `r_file1`–`r_file9` | varchar(128) ×9 | Đường dẫn file đính kèm (9 slot). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `se_date` | date | Ngày thiết lập. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_plan` | char(1) | Cờ kế hoạch phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `do_date` | date | Ngày thực hiện. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `plan_date` | date | Ngày kế hoạch. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_reason` | text | Lý do yêu cầu phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_bikou` | text | Ghi chú phê duyệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `yos_id` | varchar(16) | Mã dự toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rmk_id` | varchar(16) | Mã ghi chú. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `r_del` | tinyint(1) | Cờ xóa mềm bổ sung. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `plan_keizoku` | tinyint(1) | Cờ kế hoạch tiếp tục. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `kaisyu_y` | float | Số năm hoàn vốn. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `npv` | float | Giá trị hiện tại ròng (NPV). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `irr` | float | Tỷ suất hoàn vốn nội bộ (IRR). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_rinitem

*Được dùng tại: (nhiều trang)*

**Vai trò:** Dòng chi tiết yêu cầu phê duyệt (module CAPEX). Các dòng chi tiết của yêu cầu phê duyệt, khóa theo số dòng. Mỗi dòng có nhà sản xuất, số tiền thanh toán, dự toán, mã tài sản, bảo hành, chủ đề, và ghi chú đặc biệt.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_ban` | varchar(16) NOT NULL | Số phê duyệt (app-FK → `p_ringi.rin_ban`). Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `gyo_no` | smallint(6) NOT NULL | Số dòng. Phần của composite PK. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `mk_id` | varchar(16) | Mã nhà sản xuất (app-FK → `a_maker.mk_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_pay` | int(11) | Số tiền thanh toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_est` | int(11) | Số tiền dự toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_ass` | varchar(32) | Mã tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_war` | varchar(32) | Thông tin bảo hành. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_sbj` | varchar(32) | Chủ đề / hạng mục. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_tok` | varchar(64) | Ghi chú đặc biệt. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_sisan

*Được dùng tại: (nhiều trang)*

**Vai trò:** Sổ tài sản cố định (module CAPEX). Theo dõi từng tài sản cố định với số tài sản, tên, giá, danh mục kế toán, liên kết mua sắm/dự án, thông số kỹ thuật, thông tin thanh lý, và thời hạn sử dụng. Có thể liên kết đến master thiết bị qua `eq_id`. Xóa mềm qua `si_del`.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `si_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_id` | int(11) NOT NULL | ID tài sản (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_name` | varchar(64) | Tên tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_tanka` | int(11) | Đơn giá tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_price` | int(11) | Tổng giá tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `den_ban` | varchar(32) | Số phiếu / chứng từ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `keiri_kbn` | varchar(4) | Mã danh mục kế toán. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_kind` | varchar(4) | Mã loại tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_num` | varchar(16) | Số đơn hàng liên kết (app-FK → `p_purchase.p_num`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rin_ban` | varchar(32) | Số phê duyệt liên kết (app-FK → `p_ringi.rin_ban`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên phụ trách (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_sid` | varchar(16) | Mã nhân viên sửa đổi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_sid` | varchar(16) | Mã nhân viên tạo. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `prj_id` | int(11) | ID dự án liên kết (app-FK → `p_proj.prj_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_ban` | varchar(32) | Số tài sản (mã thẻ). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_stat` | smallint(6) | Mã trạng thái tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `dai_ban` | varchar(16) | Số đại lý. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `d_kind` | char(1) | Mã loại xử lý. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_date` | date | Ngày ghi nhận tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `nou_tan` | varchar(16) | Mã người phụ trách giao hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eqkbn` | smallint(6) | Mã phân loại thiết bị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `acd1` | smallint(6) | Mã tài khoản 1. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `acd2` | smallint(6) | Mã tài khoản 2. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `typeid` | varchar(64) | Mã model / type. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `serid` | varchar(64) | Số serial. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `spec` | text | Thông số kỹ thuật. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_bikou` | text | Ghi chú tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `smk_id` | varchar(16) | Mã nhà sản xuất phụ. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `nou_sya_id` | varchar(16) | Mã nhân viên giao hàng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ps_date` | date | Ngày bắt đầu thời hạn sử dụng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `pe_date` | date | Ngày kết thúc thời hạn sử dụng. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `disposal` | varchar(64) | Thông tin thanh lý. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `p_dai_ban` | varchar(16) | Số đại lý mua sắm. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `eq_id` | int(11) | ID thiết bị liên kết (app-FK → `a_equips.eq_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## p_sisancode

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master mã / thẻ tài sản (module CAPEX). Khóa bởi số tài sản, lưu dữ liệu thẻ tài sản vật lý: RFID, nhân viên phụ trách, ngày kiểm kê vật lý, phân công nhà máy.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `si_ban`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `si_ban` | varchar(32) NOT NULL | Số tài sản (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) | Mã nhân viên phụ trách (app-FK → `syain_master.sya_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_sid` | varchar(16) | Mã nhân viên sửa đổi. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_sid` | varchar(16) | Mã nhân viên tạo. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sic_id` | int(11) | ID mã tài sản (sequence). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `s_file1` | varchar(64) | Đường dẫn file đính kèm. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sic_date` | date | Ngày ghi nhận mã tài sản. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `actual` | smallint(6) | Mã trạng thái thực tế. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `rfid` | varchar(32) | Mã thẻ RFID. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `tana_date` | date | Ngày kiểm kê vật lý. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `s_fc_id` | varchar(16) | Mã nhà máy phân công (app-FK → `a_factory.fc_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## staff

*Được dùng tại: (nhiều trang)*

**Vai trò:** Tài khoản super-admin (zaikodb). Tách biệt với `bk_staff` của tenant — đây là tài khoản quản trị viên cấp hệ thống cho `/padmin/`. Không có `bkid` — không thuộc phạm vi tenant.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `login` | varchar(16) | Tên đăng nhập admin. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `passwd` | varchar(32) | Mật khẩu đăng nhập (plain text). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `email` | varchar(64) | Địa chỉ email admin. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `level` | smallint(6) | Mã cấp quyền. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `name` | varchar(64) | Tên hiển thị admin. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | bigint(20) unsigned AUTO_INCREMENT | ID admin (PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `is_del` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---

## syain_master

*Được dùng tại: (nhiều trang)*

**Vai trò:** Master công nhân / nhân viên vận hành (社員マスター). Lưu nhân viên tại hiện trường — có thể không có tài khoản đăng nhập hệ thống. Được module bảo trì, mua sắm, và thông báo email tham chiếu.

| Cột | Kiểu | Mục đích | [/{tenant}/equip.php](<01 Thiết bị/Trang thiết bị.md>) | [/{tenant}/stock.php](<03 Tồn kho/Quản lý tồn kho.md>) | [/{tenant}/sch.php#calendar](<02 Bảo trì/Lịch bảo trì.md>) | [/{tenant}/mtinfo_yoyaku.php](<02 Bảo trì/Đặt lịch bảo trì.md>) | [/{tenant}/mtinfo.php](<02 Bảo trì/Công việc bảo trì.md>) | [/{tenant}/mtres_list.php](<02 Bảo trì/Danh sách kết quả bảo trì.md>) | [/{tenant}/info.php](<04 Quản trị/Thông báo nội bộ.md>) | [/{tenant}/auth.php](<04 Quản trị/Phân quyền master.md>) | [/{tenant}/staff.php](<04 Quản trị/Quản lý nhân sự.md>) | [/{tenant}/factory.php](<04 Quản trị/Master cơ sở và vị trí.md>) | [/{tenant}/eqgroup.php](<04 Quản trị/Master nhóm thiết bị.md>) | [/{tenant}/eqitem.php](<04 Quản trị/Master hạng mục thiết bị.md>) | [/{tenant}/maker.php](<04 Quản trị/Master maker.md>) | /{tenant}/mt_master.php | [/{tenant}/mailtmpl.php](<04 Quản trị/Master mẫu email.md>) | [/{tenant}/config.php?look=1](<04 Quản trị/Trang cấu hình.md>) | /{tenant}/tana.php |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `sya_id`. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_id` | varchar(16) NOT NULL | Mã nhân viên (business PK). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_name` | varchar(16) | Tên hiển thị nhân viên. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `disporder` | smallint(6) | Thứ tự hiển thị. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `ishidden` | tinyint(1) | Cờ ẩn. `1` = ẩn khỏi dropdown. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `create_date` | datetime | Datetime tạo row. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `nodisp` | tinyint(1) | Cờ không hiển thị. `1` = ẩn trên tất cả màn hình. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| `sya_mail` | varchar(64) | Địa chỉ email nhân viên. |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

---
