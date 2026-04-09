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

---

## Mục lục bảng

| Bảng | Nhóm | Vai trò tóm tắt |
| --- | --- | --- |
| [[#a_equips]] | Chuỗi bảo trì cốt lõi | Master thiết bị |
| [[#a_mtinfo]] | Chuỗi bảo trì cốt lõi | Kế hoạch bảo trì |
| [[#a_mtsch]] | Chuỗi bảo trì cốt lõi | Instance lịch bảo trì |
| [[#a_mtres]] | Chuỗi bảo trì cốt lõi | Kết quả bảo trì |
| [[#a_factory]] | Địa điểm / tổ chức | Master nhà máy |
| [[#a_line]] | Địa điểm / tổ chức | Master dây chuyền |
| [[#holidays]] | Hỗ trợ calendar | Danh sách ngày lễ |
| [[#datamaster]] | Hỗ trợ calendar | Master giá trị dropdown |
| [[#bk_infos]] | Widget trang | Thông báo theo tenant |
| [[#infos]] | Widget trang | Thông báo toàn hệ thống |
| [[#buscomps]] | Auth / session | Registry tenant (zaikodb) |
| [[#bk_staff]] | Auth / session | Tài khoản nhân viên tenant |
| [[#bkmasters]] | Auth / session | Cấu hình tenant |
| [[#a_auths]] | Auth / session | Nhóm quyền tính năng |
| [[#a_eqgroup]] | Auth / session | Master nhóm thiết bị |
| [[#a_area]] | Địa điểm / tổ chức | Master khu vực |

---

## a_equips

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Master thiết bị. Mỗi row là một thiết bị vật lý thuộc một tenant. Lưu tên, vị trí (nhà máy/dây chuyền), nhóm thiết bị, thông số máy, và tối đa 16 trường tùy chỉnh. Xóa mềm qua `del_flg` — các row bị xóa được ẩn trên tất cả màn hình nhưng vẫn được giữ trong database.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Tất cả query đều filter `WHERE bkid = $this->_bkid`. |
| `eq_id` | int(11) NOT NULL | ID thiết bị — số nguyên gán khi tạo. Composite PK với `bkid`. |
| `eq_name` | varchar(100) | Tên hiển thị của thiết bị trên tất cả màn hình. |
| `eq_kbn` | smallint(6) | Mã phân loại loại thiết bị. Tra nhãn qua `datamaster` (`propid='eq_kbn'`). |
| `eq_stat` | smallint(6) | Mã trạng thái vận hành thiết bị. |
| `fc_id` | varchar(16) | Mã nhà máy (app-FK → `a_factory.fc_id`). |
| `line_id` | varchar(8) | Mã dây chuyền (app-FK → `a_line.line_id`). |
| `disporder` | smallint(6) | Thứ tự hiển thị trong danh sách. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` | datetime | Datetime sửa đổi gần nhất. |
| `create_date` | datetime | Datetime tạo row. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |
| `eqg_id` | smallint(6) | ID nhóm thiết bị (app-FK → `a_eqgroup.eqg_id`). Dùng cho bộ lọc nhóm trên `sch.php`. |
| `eq_vals` | text | Giá trị trường tùy chỉnh (dạng JSON/tuần tự hóa). |
| `eq_words` | text | Blob từ khóa tìm kiếm full-text. |
| `eqv11`–`eqv16` | varchar(32) ×6 | Sáu trường chuỗi tùy chỉnh bổ sung. |
| `del_flg` | tinyint(1) | Cờ xóa mềm. `1` = đã xóa và ẩn khỏi tất cả trang. |
| `fc_num` | varchar(16) | Mã máy / thẻ quản lý nội bộ nhà máy. |
| `mat_mcno` | varchar(50) | Số model máy của nhà sản xuất. |
| `mat_nensiki` | varchar(8) | Năm sản xuất (ví dụ `2019`). |
| `mat_ton` | float | Công suất / trọng tải. |
| `mat_mk_id` | varchar(8) | ID nhà sản xuất (app-FK → `a_maker.mk_id`). |
| `mvdata` | text | Dữ liệu lịch sử di chuyển / chuyển giao thiết bị. |
| `upload` | tinyint(1) | `1` nếu thiết bị có file đính kèm. |
| `details` | blob | Blob mô tả chi tiết dạng rich-text (HTML). |
| `flr_id` | varchar(16) | ID tầng (app-FK → `a_floor.flr_id`). |

---

## a_mtinfo

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Kế hoạch bảo trì. Mỗi row là một nhiệm vụ bảo trì gắn với một thiết bị. Lưu tên nhiệm vụ, loại, cửa sổ kế hoạch, chi phí dự kiến, và tối đa 19 file đính kèm. Cột `sc_kbn` phân biệt BT định kỳ với quy trình báo giá/phát hành phiếu.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. |
| `mt_id` | int(11) NOT NULL | ID kế hoạch BT (business PK). Composite PK với `bkid`. |
| `eq_id` | int(11) NOT NULL | ID thiết bị (app-FK → `a_equips.eq_id`). |
| `res_date` | datetime | Ngày đăng ký kế hoạch. |
| `mt_name` | varchar(64) | Tên hiển thị nhiệm vụ BT. |
| `purpose` | varchar(128) | Mục đích / mục tiêu của nhiệm vụ. |
| `mt_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row duy nhất toàn cầu (qua mọi tenant). |
| `disporder` | smallint(6) | Thứ tự hiển thị. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |
| `ree_date` | datetime | Ngày đăng ký lại / sửa đổi. |
| `eq_file1`–`eq_file19` | varchar(128) ×19 | Đường dẫn file đính kèm (19 slot). |
| `mt_detail` | varchar(512) | Mô tả chi tiết công việc BT. |
| `failure_place` | varchar(512) | Vị trí hỏng hóc / điểm kiểm tra. |
| `sc_start_date` | date | Ngày bắt đầu cửa sổ kế hoạch (cấp plan). |
| `sc_end_date` | date | Ngày kết thúc cửa sổ kế hoạch (cấp plan). |
| `mtinfo_kbn` | smallint(6) | Mã phân loại loại BT. Tra nhãn qua `datamaster` (`propid='mtinfo_kbn'`). |
| `import_flg` | smallint(6) | Cờ đánh dấu row nhập từ nguồn ngoài. |
| `sc_kbn` | smallint(6) | Danh mục lịch. `0` = BT định kỳ; giá trị khác = quy trình báo giá/phát hành phiếu. |
| `mt_cost` | int(11) | Chi phí dự kiến (cấp plan). |
| `del_flg` | tinyint(1) | Cờ xóa mềm. |
| `sp_start_date` / `sp_end_date` | datetime | Khoảng thời gian đặc biệt bắt đầu/kết thúc. |
| `failure_date` | date | Ngày xảy ra sự cố. |
| `estimate_kbn` | varchar(64) | Mã loại báo giá. |
| `fas_date` / `fae_date` | datetime | Ngày bắt đầu/kết thúc hoạt động FA. |
| `fas2_date` / `fae2_date` | datetime | Ngày FA thứ cấp bắt đầu/kết thúc. |
| `fail_date` | datetime | Datetime xảy ra sự cố. |
| `mt_stat` | char(1) | Mã trạng thái kế hoạch (`'0'` = mặc định). |
| `main_stfname` | varchar(16) | Tên nhân viên chính (denormalized, snapshot). |
| `sub_stfname` | varchar(128) | Tên các nhân viên phụ (snapshot cách nhau bởi dấu phẩy). |
| `matsuo` | text | Blob dữ liệu bổ sung. |
| `mt_fixtime` | smallint(6) | Thời gian cố định được phân bổ. |
| `upload` | tinyint(1) | `1` nếu kế hoạch có file đính kèm. |
| `ins_kbn` / `ins_term` / `ins_cat` | smallint / varchar | Mã loại / kỳ hạn / danh mục kiểm định. |
| `ins_data` | text | Blob dữ liệu kiểm định. |
| `stat1`–`stat5` | smallint(6) ×5 | Mã cờ trạng thái tùy chỉnh 1–5. |
| `stats1` / `stats2` | varchar(32) | Trường chuỗi trạng thái tùy chỉnh. |
| `mtdate1` | date | Ngày yêu cầu báo giá cấp plan (sao chép từ `mtr_mtdate1` khi tạo plan). |
| `dpdate1` | date | Ngày phát hành phiếu cấp plan (sao chép từ `mtr_dpdate1` khi tạo plan). |
| `mmvdata` | blob | Blob dữ liệu di chuyển. |

---

## a_mtsch

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Instance lịch bảo trì. Mỗi row là một lần thực hiện cụ thể của một kế hoạch (`mt_id` + `sdate`). Đây là **nguồn sự thật duy nhất cho tất cả icon trên calendar** của `sch.php`. Lưu cửa sổ thực hiện (`s_date`/`e_date`), cờ hoàn thành (`mtr_done`), và bốn cột ngày điều khiển icon Path B.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. |
| `mt_id` | int(11) NOT NULL | ID kế hoạch BT (app-FK → `a_mtinfo.mt_id`). Phần của composite PK. |
| `sdate` | date NOT NULL | Ngày lịch (phần của composite PK `bkid, mt_id, sdate`). |
| `s_date` | datetime | Ngày bắt đầu cửa sổ thực hiện (độ chính xác datetime). Dùng để khớp khoảng ngày Path A. |
| `e_date` | datetime | Ngày kết thúc cửa sổ. Dùng kiểm tra quá hạn: `e_date < hôm nay` → nền vàng. |
| `mts_uid` | bigint(20) unsigned AUTO_INCREMENT | ID instance lịch duy nhất toàn cầu. Được `a_mtres.mts_uid` tham chiếu như FK. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |
| `mtr_done` | tinyint(1) DEFAULT 0 | **Cờ hoàn thành / đã đăng ký kết quả. Điều khiển icon Path A:** có giá trị → ●; không có + chưa quá hạn → ○; không có + quá hạn → ○ nền vàng. |
| `del_flg` | tinyint(1) | Cờ xóa mềm. |
| `mtr_stat` | char(1) | Mã trạng thái lịch (`'0'` = mặc định). |
| `upload` | tinyint(1) | `1` nếu instance lịch có file đính kèm. |
| `mtr_mtdate1` | date | **Ngày dự kiến yêu cầu báo giá. Điều khiển △ (Path B ưu tiên 4).** Bị loại trừ khi `mtr_mtdate2` có giá trị. |
| `mtr_mtdate2` | date | **Ngày báo giá thực tế. Điều khiển ▲ (Path B ưu tiên 3).** Đặt giá trị này sẽ loại trừ `mtr_mtdate1` khỏi map icon PHP. |
| `mtr_dpdate1` | date | **Ngày dự kiến phát hành phiếu. Điều khiển □ (Path B ưu tiên 2).** Bị loại trừ khi `mtr_dpdate2` có giá trị. |
| `mtr_dpdate2` | date | **Ngày phát hành phiếu thực tế. Điều khiển ■ (Path B ưu tiên 1).** Đặt giá trị này sẽ loại trừ `mtr_dpdate1` khỏi map icon PHP. |
| `sendnum` | smallint(6) | Bộ đếm số lần gửi / thông báo. |

---

## a_mtres

*Được dùng tại: [[Lịch bảo trì#Chuỗi bảo trì cốt lõi|Lịch bảo trì (VI)]]*

**Vai trò:** Kết quả bảo trì. Mỗi row là kết quả của một instance lịch đã thực hiện (khóa bởi `mts_uid`). Lưu chi tiết công việc, tên nhân viên, ngày thực hiện thực tế, chi phí, và tối đa 15 file đính kèm. Trên `sch.php` bảng này được join qua LEFT JOIN chỉ để lấy dữ liệu hiển thị — **không điều khiển bất kỳ icon nào** trên calendar.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. |
| `mts_uid` | int(11) NOT NULL | ID instance lịch (app-FK → `a_mtsch.mts_uid`). Composite PK với `bkid`. |
| `mtr_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row kết quả duy nhất toàn cầu. |
| `mtr_detail` | varchar(512) | Mô tả công việc đã thực hiện. |
| `mtr_failure` | varchar(512) | Mô tả hỏng hóc / lỗi. |
| `mtr_gensyo` | varchar(512) | Mô tả triệu chứng (現象). |
| `mtr_genin` | varchar(512) | Mô tả nguyên nhân gốc rễ (原因). |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |
| `mtr_file1`–`mtr_file15` | varchar(128) ×15 | Đường dẫn file đính kèm (15 slot). |
| `mtre_date` / `mtrs_date` | datetime | Datetime kết thúc / bắt đầu công việc (thực tế). |
| `mtrs2_date` / `mtre2_date` | datetime | Khoảng thời gian làm việc thứ cấp bắt đầu/kết thúc. |
| `fins_date` / `fine_date` | datetime | Ngày kiểm tra cuối bắt đầu/kết thúc. |
| `mtr_cost` | int(11) | Chi phí thực tế. |
| `mtr_cost2` | int(11) | Trường chi phí thứ cấp (ví dụ chi phí linh kiện). |
| `del_flg` | tinyint(1) | Cờ xóa mềm. |
| `mtr_import_flg` | char(1) | Cờ nhập từ nguồn ngoài. |
| `mtr_mt_name` | varchar(64) | Tên nhiệm vụ denormalized (snapshot tại thời điểm tạo kết quả). |
| `mtr_purpose` | varchar(128) | Mục đích denormalized (snapshot). |
| `o_s_date` / `o_e_date` | datetime | Snapshot cửa sổ lịch gốc (sao chép từ `a_mtsch` tại thời điểm tạo kết quả). |
| `mtr_stat` | char(1) | Mã trạng thái kết quả. |
| `mtr_main_stfname` | varchar(16) | Tên nhân viên chính denormalized. |
| `mtr_sub_stfname` | varchar(128) | Tên nhân viên phụ denormalized. |
| `mtr_wktime` | int(11) | Thời gian làm việc thực tế (phút). |
| `mtr_matsuo` | blob | Blob dữ liệu bổ sung. |
| `mtr_fixtime` | smallint(6) | Thời gian cố định được phân bổ. |
| `upload` | tinyint(1) | `1` nếu kết quả có file đính kèm. |
| `mtr_mitei` | tinyint(1) | Cờ kết quả tạm thời / chưa xác nhận. |
| `mtr_stat1`–`mtr_stat5` | smallint(6) ×5 | Mã cờ trạng thái tùy chỉnh 1–5. |
| `mtr_stats1` / `mtr_stats2` / `mtr_stats3` | varchar | Trường chuỗi trạng thái tùy chỉnh. |
| `mtr_downtime` | int(11) | Thời gian ngừng hoạt động thiết bị do BT này gây ra (phút). |
| `mtr_estimate_kbn` | varchar(64) | Mã loại báo giá denormalized. |
| `p_mts_uid` | int(11) | UID lịch cha (dành cho kết quả liên kết / con). |

---

## a_factory

*Được dùng tại: [[Lịch bảo trì#Phân cấp địa điểm / tổ chức|Lịch bảo trì (VI)]], [[Phân quyền master#Phân cấp địa điểm / tổ chức|Phân quyền master]]*

**Vai trò:** Master nhà máy. Nhóm địa điểm cấp cao nhất. Mỗi thiết bị có `fc_id` là app-level FK trỏ vào bảng này. Dùng để populate dropdown lọc nhà máy trên `sch.php`.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. |
| `fc_id` | varchar(16) NOT NULL | Mã nhà máy (business PK). Composite PK với `bkid`. |
| `fc_name` | varchar(64) | Tên hiển thị nhà máy. |
| `area_id` | smallint(6) | ID khu vực (app-FK → `a_area.area_id`). |
| `fc_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row nhà máy duy nhất toàn cầu. |
| `disporder` | smallint(6) | Thứ tự hiển thị. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |
| `fc_del` | tinyint(1) | Cờ xóa mềm. |

---

## a_line

*Được dùng tại: [[Lịch bảo trì#Phân cấp địa điểm / tổ chức|Lịch bảo trì (VI)]]*

**Vai trò:** Master dây chuyền. Địa điểm con trong một nhà máy, khóa bởi `bkid + line_id + fc_id`. Dùng cho dropdown lọc dây chuyền và hiển thị như nhãn cột trong lưới calendar.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. |
| `line_id` | varchar(8) NOT NULL | Mã dây chuyền. Phần của composite PK. |
| `fc_id` | varchar(16) NOT NULL | Mã nhà máy (app-FK → `a_factory.fc_id`). Phần của composite PK. |
| `line_name` | varchar(32) | Tên hiển thị dây chuyền. |
| `disporder` | smallint(6) | Thứ tự hiển thị. |
| `line_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row dây chuyền duy nhất toàn cầu. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |
| `old_names` | text | Danh sách tên cũ (lịch sử đổi tên). |
| `line_del` | tinyint(1) | Cờ xóa mềm. |

---

## holidays

*Được dùng tại: [[Lịch bảo trì#Hỗ trợ hiển thị calendar|Lịch bảo trì (VI)]]*

**Vai trò:** Danh sách ngày lễ công cộng. `sch.php` đọc bảng này để tô màu hồng cho cột header ngày lễ trong lưới calendar. Chia sẻ chung cho mọi tenant (không có `bkid`).

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `hday` | date NOT NULL | Ngày lễ (PK). |
| `hname` | varchar(32) | Tên ngày lễ (tiếng Nhật). |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |

---

## datamaster

*Được dùng tại: [[Lịch bảo trì#Hỗ trợ hiển thị calendar|Lịch bảo trì (VI)]], [[Phân quyền master#Hỗ trợ hiển thị|Phân quyền master]]*

**Vai trò:** Master giá trị dropdown tổng quát. Lưu danh sách tên item theo `propid` (ví dụ `mtinfo_kbn`, `eq_kbn`) với nhãn bốn ngôn ngữ. Đọc qua `ppes_master()` để chuyển đổi mã số nguyên → tên hiển thị theo ngôn ngữ của người dùng. Chia sẻ chung cho mọi tenant (không có `bkid`).

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `propid` | varchar(16) NOT NULL | Định danh danh sách / thuộc tính (ví dụ `'mtinfo_kbn'`). Phần của composite PK. |
| `itid` | smallint(6) NOT NULL | Mã item trong danh sách. Phần của composite PK. |
| `itname` | varchar(32) | Tên hiển thị tiếng Nhật. |
| `itname_en` | varchar(32) | Tên hiển thị tiếng Anh. |
| `itname_cn` | varchar(32) | Tên hiển thị tiếng Trung. |
| `itname_vn` | varchar(32) | Tên hiển thị tiếng Việt. |
| `disporder` | smallint(6) | Thứ tự hiển thị. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |

---

## bk_infos

*Được dùng tại: [[Lịch bảo trì#Dữ liệu widget trang|Lịch bảo trì (VI)]]*

**Vai trò:** Thông báo theo tenant. Row thuộc về một `bkid` cụ thể. Hiển thị trong widget **【お知らせ】** trên trang lịch / TOP. Hỗ trợ tiêu đề/nội dung bốn ngôn ngữ và có thể giới hạn hiển thị theo nhà máy.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `inf_id`. |
| `inf_id` | int(11) NOT NULL | ID thông báo. |
| `inf_title` | varchar(255) | Tiêu đề (tiếng Nhật). |
| `inf_title_en` / `inf_title_cn` / `inf_title_vn` | varchar(255) ×3 | Tiêu đề tiếng Anh / Trung / Việt. |
| `inf_conts` | text | Nội dung (tiếng Nhật). |
| `inf_conts_en` / `inf_conts_cn` / `inf_conts_vn` | text ×3 | Nội dung tiếng Anh / Trung / Việt. |
| `inf_date` | date | Ngày đăng hiển thị trong widget. |
| `inf_hidden` | tinyint(1) | `1` = bản nháp / ẩn khỏi người dùng. |
| `fc_ids` | varchar(128) | Danh sách `fc_id` cách nhau bằng dấu phẩy. Khi có giá trị, thông báo chỉ hiển thị cho các nhà máy đó. Rỗng = hiển thị cho tất cả. |
| `inf_file1`–`inf_file5` | varchar(64) ×5 | Đường dẫn file đính kèm. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên tạo thông báo. |
| `del_flg` | tinyint(1) | Cờ xóa mềm. |

---

## infos

*Được dùng tại: [[Lịch bảo trì#Dữ liệu widget trang|Lịch bảo trì (VI)]]*

**Vai trò:** Thông báo toàn hệ thống, đăng từ `/padmin/`. Hiển thị trong widget info cùng với các entry của `bk_infos`. Không có `bkid` — hiển thị cho tất cả tenant. Chỉ super-admin mới quản lý được.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `inf_id` | int(11) NOT NULL | ID thông báo hệ thống (PK). |
| `inf_title` | varchar(128) | Tiêu đề thông báo. |
| `inf_conts` | text | Nội dung thông báo. |
| `inf_date` | date | Ngày đăng. |
| `inf_hidden` | tinyint(1) | `1` = ẩn trên các trang tenant. |
| `is_web` | tinyint(1) | `1` = cũng hiển thị trên website công khai. |
| `web_hidden` | tinyint(1) | `1` = ẩn trên website. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |

---

## buscomps

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Auth / session context|Phân quyền master]]*

**Vai trò:** Registry tenant. Nằm trong `zaikodb` (database super-admin). Mỗi row là một công ty tenant. Được đọc khi đăng nhập để xác định tenant, xác minh credentials, kiểm tra feature flag (`use_api`, `spe_*`, `use_*`), và resolve `bkid`. Được quản lý từ `/padmin/`.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | bigint(20) unsigned AUTO_INCREMENT | Tenant ID — duy nhất toàn cầu, tự tăng. Đây là `bkid` được dùng trong tất cả bảng của tenant. |
| `bcid` | int(11) | ID sequence công ty. |
| `email` | varchar(64) | Email liên hệ chính. |
| `passwd` | varchar(16) | Mật khẩu admin đăng nhập (plain text). |
| `bcname` | varchar(64) | Tên công ty hiển thị. |
| `bckana` | varchar(64) | Tên công ty theo kana. |
| `phone` / `fax` | varchar(16) | Điện thoại / fax liên hệ. |
| `pc` | varchar(8) | Mã bưu chính. |
| `pref` | smallint(6) | Mã tỉnh/thành. |
| `addr` | varchar(96) | Địa chỉ. |
| `bcstat` | char(1) | Trạng thái công ty (`'0'` = đang hoạt động). |
| `bikou` | text | Ghi chú nội bộ. |
| `mng_name` / `mng_sect` | varchar | Tên / bộ phận quản lý. |
| `loginid` | varchar(16) | ID đăng nhập admin. |
| `subdom` | varchar(8) | Subdomain / mã đường dẫn URL tenant (ví dụ `ahihi` cho `/{ahihi}/sch.php`). |
| `useplan` | char(1) | Mã gói đăng ký. |
| `startdate` / `enddate` | date | Ngày bắt đầu / kết thúc hợp đồng. |
| `use_api` | tinyint(1) | Feature flag cho phép truy cập API. |
| `api_pw` | varchar(32) | Mật khẩu API. |
| `api_ips` | varchar(128) | Danh sách IP whitelist API (cách nhau bằng dấu phẩy). |
| `use_map` | tinyint(1) | Feature flag bản đồ. |
| `use_shift` | tinyint(1) | Feature flag quản lý ca làm việc. |
| `use_ura` | tinyint(1) | Feature flag tính năng back-end (裏). |
| `use_uacj` / `use_ver2` | tinyint(1) | Feature toggle bổ sung. |
| `spe_17_1` / `spe_19_1` / `spe_14_1` | tinyint(1) | Feature flag đặc biệt theo tenant. |
| `regtime` | int(11) | Unix timestamp đăng ký. |
| `cmail` / `cstype` | varchar | Email CS liên hệ / loại hỗ trợ. |
| `dis_syukei` / `dis_seikyu` | tinyint(1) | Vô hiệu hóa tính năng tổng hợp / thanh toán. |
| `yk_count` / `uk_count` / `car_count` / `ag_count` / `drv_count` | int / smallint | Giới hạn số lượng / license theo tính năng. |

---

## bk_staff

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Auth / session context|Phân quyền master]]*

**Vai trò:** Tài khoản nhân viên theo tenant. Mỗi row là một nhân viên của một tenant. Được `aspUser->openUser()` kiểm tra để xác thực session cookie và resolve `bkid` + `stf_id`. Cột `lang` xác định ngôn ngữ hiển thị cho người dùng đó.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `stf_id`. |
| `stf_id` | int(11) NOT NULL | ID nhân viên. |
| `stf_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row nhân viên duy nhất toàn cầu. |
| `login` | varchar(64) | Tên đăng nhập. |
| `passwd` | varchar(16) | Mật khẩu đăng nhập (plain text). |
| `email` | varchar(64) | Địa chỉ email nhân viên. |
| `name` | varchar(64) | Tên hiển thị trong UI. |
| `level` | varchar(4) | Mã cấp quyền (dùng kết hợp với `a_auths`). |
| `lang` | char(1) | Ngôn ngữ hiển thị: `'0'`=Nhật, `'1'`=Anh, `'2'`=Trung, `'3'`=Việt. |
| `fc_id` | varchar(16) | Nhà máy chính được phân công. |
| `fc_ids` | varchar(128) | Danh sách truy cập đa nhà máy (các `fc_id` cách nhau bằng dấu phẩy). |
| `area_id` | smallint(6) | Phân công khu vực. |
| `sec_name` | varchar(32) | Tên phòng ban / bộ phận. |
| `phone` | varchar(32) | Số điện thoại. |
| `stf_order` | smallint(6) | Thứ tự hiển thị. |
| `sigfile` | varchar(32) | Đường dẫn file chữ ký. |
| `is_tbt` | tinyint(1) | Cờ super-admin TBT. |
| `lastlogin` | int(11) | Unix timestamp lần đăng nhập cuối. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `bs_delete` | tinyint(1) | Cờ xóa mềm. |

---

## bkmasters

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Auth / session context|Phân quyền master]]*

**Vai trò:** Cấu hình tenant. Mỗi row ứng với một `bkid`. Lưu tên công ty, cài đặt giờ làm việc, tùy chọn hiển thị, cấu hình thanh toán, và các cài đặt cấp tenant được dùng trên tất cả trang. Được đọc sớm trong vòng đời request để áp dụng hành vi đặc thù của tenant.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Tenant ID (PK). |
| `compname` | varchar(64) | Tên công ty hiển thị trong header UI. |
| `license` | varchar(64) | Định danh giấy phép. |
| `addr1` / `addr2` | varchar(128) | Dòng địa chỉ công ty 1 và 2. |
| `pc` | varchar(8) | Mã bưu chính. |
| `tel` / `fax` | varchar(16) | Điện thoại / fax công ty. |
| `pref` | varchar(2) | Mã tỉnh/thành. |
| `ceo` | varchar(32) | Tên giám đốc (dùng in tài liệu). |
| `mng1`–`mng3` | varchar(32) ×3 | Các slot tên quản lý 1–3. |
| `mng_mail` | varchar(64) | Email quản lý (dùng gửi thông báo alert). |
| `wk_start` / `wk_end` | smallint(6) | Giờ bắt đầu / kết thúc ngày làm việc (số nguyên 0–23). |
| `wktime` | smallint(6) | Thời gian làm việc tiêu chuẩn mỗi ngày (phút). |
| `use_alert` | tinyint(1) | `1` = bật thông báo email alert. |
| `def_term` | smallint(6) | Độ dài kỳ hạn lịch mặc định (tháng). |
| `rests` | varchar(64) | Chuỗi cấu hình thời gian nghỉ. |
| `sk_kuri` | tinyint(1) | Cài đặt chuyển tiếp lịch. |
| `hide_eqid` | tinyint(1) | `1` = ẩn cột ID thiết bị trong danh sách. |
| `del_disable` | tinyint(1) | `1` = ngăn xóa record bảo trì. |
| `dl_char` | char(1) | Mã hóa ký tự khi tải CSV (`'0'`=UTF-8, `'1'`=Shift-JIS). |
| `tana_date1` / `tana_date2` | varchar(8) | Cài đặt ngày chu kỳ kiểm kê. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |

---

## a_auths

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]], [[Phân quyền master#Danh mục quyền|Phân quyền master]]*

**Vai trò:** Nhóm quyền tính năng. Mỗi row là một nhóm quyền của một tenant. `aspUser->setAuth($db, $request, $authId)` đọc bảng này để kiểm tra xem nhóm của người dùng hiện tại có quyền xem hoặc chỉnh sửa một tính năng cụ thể trên trang hiện tại không. Mỗi cờ `at_N` ánh xạ đến một tính năng cụ thể.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `at_id`. |
| `at_id` | smallint(6) NOT NULL | ID nhóm quyền. |
| `at_name` | varchar(16) | Tên hiển thị nhóm (ví dụ `管理者`, `一般`). |
| `at_all` | tinyint(1) | `1` = cấp tất cả quyền (override các cờ riêng lẻ). |
| `at_notall` | tinyint(1) | `1` = chỉ được truy cập record của chính mình. |
| `at_params` | varchar(128) | Chuỗi tham số bổ sung cho override quyền chi tiết. |
| `at_1` | char(1) | Cờ quyền tính năng 1. |
| `at_2` | char(1) | Cờ quyền tính năng 2. |
| `at_3` | char(1) | Cờ quyền tính năng 3. |
| `at_10` | char(1) | Cờ quyền tính năng 10. |
| `at_11` | char(1) | Cờ quyền tính năng 11. |
| `at_12` | char(1) | Cờ quyền tính năng 12 (trang lịch). |
| `at_13` | char(1) | Cờ quyền tính năng 13. |
| `at_14` | char(1) | Cờ quyền tính năng 14. |
| `at_15` | char(1) | Cờ quyền tính năng 15. |
| `authid` / `authpass` | varchar(16) | Credentials xác thực ngoài (nếu có). |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |

---

## a_eqgroup

*Được dùng tại: [[Lịch bảo trì#Auth / session context|Lịch bảo trì (VI)]]*

**Vai trò:** Master nhóm thiết bị. Nhóm các thiết bị để phân loại. Được dùng cho bộ lọc `eqg_id` trên form tìm kiếm của `sch.php`. Các row thiết bị tham chiếu bảng này qua `a_equips.eqg_id`.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `eqg_id`. |
| `eqg_id` | smallint(6) NOT NULL | ID nhóm thiết bị (PK). |
| `eqg_name` | varchar(32) | Tên hiển thị nhóm. |
| `eqg_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row nhóm duy nhất toàn cầu. |
| `disporder` | smallint(6) | Thứ tự hiển thị. |
| `nfc_id` | varchar(16) | ID thẻ NFC liên kết với nhóm này (dành cho tính năng quét di động). |
| `eqg_data` | blob | Blob dữ liệu cấu hình nhóm bổ sung. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |

## a_area

*Được dùng tại: [[Phân quyền master#Phân cấp địa điểm / tổ chức|Phân quyền master]]*

**Vai trò:** Master khu vực. Nhóm địa lý cấp cao nhất, nằm trên nhà máy trong phân cấp địa điểm. Mỗi row nhà máy có `area_id` là app-level FK trỏ vào bảng này. Tài khoản nhân viên cũng có thể mang `area_id` để phân quyền truy cập. Được dùng trên `auth.php` như một lookup hỗ trợ cho UI nhà máy/nhân viên — không phải parent cấu trúc trực tiếp của `a_auths`.

| Cột | Kiểu | Mục đích |
| --- | --- | --- |
| `bkid` | smallint(6) NOT NULL | Khóa phân vùng tenant. Composite PK với `area_id`. |
| `area_id` | smallint(6) NOT NULL | ID khu vực (business PK). |
| `area_name` | varchar(32) | Tên hiển thị khu vực. |
| `area_uid` | bigint(20) unsigned AUTO_INCREMENT | ID row khu vực duy nhất toàn cầu. |
| `disporder` | smallint(6) | Thứ tự hiển thị. |
| `uptime` | int(11) | Unix timestamp lần cập nhật cuối. |
| `modify_date` / `create_date` | datetime | Timestamp kiểm toán. |
| `stf_id` | smallint(6) | ID nhân viên sửa đổi lần cuối. |

---
