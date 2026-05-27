# 01_PROJECT_BRIEF.md

## Metadata

| Field        | Value                                                                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Document ID  | DOC-01-PROJECT-BRIEF                                                                                                                          |
| Project Name | Multi-channel E-commerce Hub                                                                                                                  |
| Version      | 0.2                                                                                                                                           |
| Status       | Draft - Updated with Open Questions answers                                                                                                   |
| Owner        | TBD                                                                                                                                           |
| Last Updated | TBD                                                                                                                                           |
| Depends On   | 00_PROJECT_IDEA.md                                                                                                                            |
| Used By      | 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md, 10_UI_UX_DIRECTION.md, 11_TECHNICAL_ARCHITECTURE.md |

---

# 1. Project Overview

## 1.1 Project Summary

**Multi-channel E-commerce Hub** là một hệ thống quản lý bán hàng đa kênh, giúp người bán quản lý tập trung các hoạt động quan trọng như sản phẩm, đơn hàng, tồn kho, khách hàng và báo cáo từ nhiều kênh bán hàng khác nhau trong một nền tảng duy nhất.

Thay vì phải đăng nhập và xử lý thủ công trên từng kênh riêng lẻ như website riêng, sàn thương mại điện tử, mạng xã hội hoặc các kênh bán hàng khác, người dùng có thể theo dõi và vận hành toàn bộ hoạt động bán hàng từ một dashboard tập trung.

Mục tiêu chính của dự án là giảm thao tác thủ công, hạn chế sai lệch dữ liệu, tăng khả năng kiểm soát đơn hàng/tồn kho và giúp người bán ra quyết định nhanh hơn dựa trên dữ liệu tổng hợp.

---

# 2. Problem Statement

## 2.1 Current Problem

Nhiều người bán hiện nay không chỉ bán hàng trên một kênh duy nhất. Họ có thể bán đồng thời trên:

* Website riêng
* Shopee
* Lazada
* TikTok Shop
* Facebook
* Instagram
* Zalo
* Cửa hàng offline
* Các kênh bán hàng thủ công khác

Việc quản lý nhiều kênh riêng lẻ dẫn đến nhiều vấn đề:

| Problem ID | Vấn đề                            | Mô tả                                                                                        |
| ---------- | --------------------------------- | -------------------------------------------------------------------------------------------- |
| PROB-001   | Dữ liệu phân tán                  | Sản phẩm, đơn hàng, khách hàng và tồn kho nằm rải rác ở nhiều nền tảng khác nhau.            |
| PROB-002   | Dễ sót đơn                        | Người bán phải kiểm tra nhiều nơi nên dễ bỏ lỡ đơn hàng mới.                                 |
| PROB-003   | Sai lệch tồn kho                  | Một sản phẩm bán ở nhiều kênh nhưng tồn kho không được đồng bộ kịp thời.                     |
| PROB-004   | Khó kiểm soát trạng thái đơn hàng | Mỗi kênh có trạng thái xử lý riêng, gây khó theo dõi tổng thể.                               |
| PROB-005   | Tốn thời gian thao tác thủ công   | Người bán phải lặp lại thao tác nhập, sửa, kiểm tra dữ liệu ở nhiều nền tảng.                |
| PROB-006   | Khó xem báo cáo tổng hợp          | Doanh thu, đơn hàng, sản phẩm bán chạy bị chia nhỏ theo từng kênh.                           |
| PROB-007   | Khó phân quyền nhân sự            | Khi có nhiều nhân viên cùng xử lý đơn/kho/sản phẩm, việc kiểm soát quyền truy cập dễ bị rối. |

## 2.2 Why This Matters

Nếu không có hệ thống quản lý tập trung, người bán có thể gặp các rủi ro sau:

* Bán quá số lượng tồn kho thực tế.
* Giao hàng chậm do xử lý đơn không kịp.
* Sai giá, sai sản phẩm hoặc sai thông tin khách hàng.
* Không biết kênh nào đang hoạt động hiệu quả.
* Khó mở rộng khi số lượng đơn hàng và kênh bán tăng lên.
* Phụ thuộc quá nhiều vào thao tác thủ công của nhân viên.

---

# 3. Goals

## 3.1 Business Goals

| Goal ID  | Goal                                  | Mô tả                                                                             |
| -------- | ------------------------------------- | --------------------------------------------------------------------------------- |
| GOAL-001 | Quản lý tập trung nhiều kênh bán hàng | Gom dữ liệu quan trọng từ nhiều kênh về một nơi để dễ vận hành.                   |
| GOAL-002 | Giảm thao tác thủ công                | Hạn chế việc nhập, sửa, kiểm tra dữ liệu lặp lại trên nhiều nền tảng.             |
| GOAL-003 | Tăng độ chính xác tồn kho             | Giúp người bán kiểm soát số lượng tồn kho tốt hơn giữa các kênh.                  |
| GOAL-004 | Tăng tốc xử lý đơn hàng               | Giúp nhân viên theo dõi và cập nhật đơn hàng nhanh hơn.                           |
| GOAL-005 | Cải thiện khả năng ra quyết định      | Cung cấp báo cáo tổng quan về doanh thu, đơn hàng, tồn kho và hiệu suất kênh bán. |
| GOAL-006 | Hỗ trợ mở rộng vận hành               | Hệ thống đủ rõ ràng để sau này mở rộng thêm kênh bán, module hoặc phân quyền.     |

## 3.2 Product Goals

| Goal ID   | Goal                        | Mô tả                                                                     |
| --------- | --------------------------- | ------------------------------------------------------------------------- |
| PGOAL-001 | Dashboard tổng quan dễ hiểu | Người dùng xem nhanh tình hình kinh doanh, đơn hàng và tồn kho.           |
| PGOAL-002 | Quản lý sản phẩm tập trung  | Người dùng tạo, cập nhật và theo dõi sản phẩm ở một nơi.                  |
| PGOAL-003 | Quản lý đơn hàng đa kênh    | Người dùng xem và xử lý đơn hàng từ nhiều kênh trong một danh sách chung. |
| PGOAL-004 | Quản lý tồn kho             | Người dùng theo dõi số lượng tồn và biến động kho.                        |
| PGOAL-005 | Quản lý khách hàng          | Người dùng xem thông tin khách hàng và lịch sử mua hàng cơ bản.           |
| PGOAL-006 | Phân quyền theo vai trò     | Người dùng trong hệ thống chỉ được truy cập đúng phần được phép.          |

---

# 4. Non-goals

Các nội dung sau **không nằm trong phạm vi bắt buộc của giai đoạn đầu**, trừ khi được chốt lại ở tài liệu sau:

| Non-goal ID | Nội dung không làm ở giai đoạn đầu         | Lý do                                                                                              |
| ----------- | ------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| NGOAL-001   | Không xây dựng marketplace riêng           | Dự án tập trung vào quản lý bán hàng, không phải tạo sàn thương mại điện tử mới.                   |
| NGOAL-002   | Không tự xử lý vận chuyển end-to-end       | Có thể tích hợp đơn vị vận chuyển sau, nhưng không tự xây hệ thống logistics phức tạp ngay từ đầu. |
| NGOAL-003   | Không tự xây cổng thanh toán riêng         | Nếu cần thanh toán, ưu tiên tích hợp bên thứ ba thay vì tự xử lý payment gateway.                  |
| NGOAL-004   | Không làm AI recommendation phức tạp       | Gợi ý sản phẩm, dự báo nhu cầu, AI phân tích nâng cao để giai đoạn sau.                            |
| NGOAL-005   | Không đồng bộ tất cả nền tảng ngay lập tức | Giai đoạn đầu nên chọn một số kênh ưu tiên để tránh quá rộng.                                      |
| NGOAL-006   | Không xây mobile app native ngay từ đầu    | Ưu tiên web dashboard responsive trước, mobile app có thể là phase sau.                            |

---

# 5. Target Users

## 5.1 Primary Users

Dự án phục vụ chính cho **shop nhỏ và vừa**. Đây là nhóm người bán cần quản lý nhiều nghiệp vụ như sản phẩm, đơn hàng, tồn kho, khách hàng, báo cáo và nhân sự, nhưng không muốn dùng hệ thống ERP quá nặng.

| User Group ID        | Nhóm người dùng                 | Mô tả                                                                                                      | Nhu cầu chính                                                                             |
| -------------------- | ------------------------------- | ---------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| USER-SELLER-OWNER    | Chủ shop / Nhà bán hàng nhỏ-vừa | Người trực tiếp sở hữu hoặc điều hành cửa hàng. Với shop nhỏ, họ thường quản lý gần như toàn bộ nghiệp vụ. | Quản lý tổng thể sản phẩm, đơn hàng, tồn kho, khách hàng, báo cáo, nhân viên và kênh bán. |
| USER-SELLER-STAFF    | Nhân viên shop                  | Người hỗ trợ xử lý nghiệp vụ hằng ngày cho shop.                                                           | Xử lý đơn, cập nhật trạng thái, kiểm tra khách hàng, hỗ trợ vận hành.                     |
| USER-WAREHOUSE       | Nhân viên kho của shop          | Người phụ trách kiểm tra số lượng hàng thực tế và thao tác nhập/xuất kho.                                  | Theo dõi tồn kho thực, cập nhật nhập/xuất kho, kiểm tra hàng sắp hết.                     |
| USER-SHOPHUB-ADMIN   | Quản trị viên hệ thống ShopHub  | Người thuộc bên vận hành nền tảng ShopHub.                                                                 | Quản lý toàn hệ thống, gói dịch vụ, tenant/shop, cấu hình nền tảng, giám sát vận hành.    |
| USER-SHOPHUB-SUPPORT | Nhân viên hỗ trợ ShopHub        | Người hỗ trợ khách hàng sử dụng hệ thống.                                                                  | Tra cứu thông tin shop, hỗ trợ lỗi, kiểm tra trạng thái đồng bộ, hướng dẫn sử dụng.       |

## 5.2 Tenant / Shop Model

Hệ thống cần hỗ trợ **nhiều cửa hàng / nhiều tenant**.

| Concept              | Mô tả                                                                                                                                                   |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Shop / Tenant        | Mỗi shop là một không gian dữ liệu riêng. Dữ liệu sản phẩm, đơn hàng, tồn kho, khách hàng của shop này không được lẫn với shop khác.                    |
| Shop Owner           | Người quản lý cao nhất trong một shop. Có quyền quản lý toàn bộ dữ liệu thuộc shop đó.                                                                  |
| ShopHub System Admin | Người thuộc bên nền tảng ShopHub. Có quyền quản trị hệ thống ở cấp platform, không phải người bán hàng trực tiếp.                                       |
| Subscription Plan    | Gói dịch vụ mà shop đăng ký, ví dụ: Pro, Premium. Gói dịch vụ ảnh hưởng đến giới hạn tính năng, số lượng user, số lượng kênh bán hoặc khả năng báo cáo. |

## 5.3 Secondary Users

| User Group ID   | Nhóm người dùng                    | Mô tả                                                                         | Ghi chú                                             |
| --------------- | ---------------------------------- | ----------------------------------------------------------------------------- | --------------------------------------------------- |
| USER-CUSTOMER   | Khách hàng cuối                    | Người mua hàng từ các kênh bán như Lazada hoặc các kênh khác trong tương lai. | Không phải người dùng chính của dashboard quản trị. |
| USER-ACCOUNTANT | Kế toán / người theo dõi lợi nhuận | Người cần theo dõi doanh thu, chi phí, lợi nhuận, đối soát.                   | Có thể là role riêng trong phase sau.               |

---

# 6. Main Use Cases

| Use Case ID | Use Case                         | Actor chính                 | Mô tả ngắn                                                                                                                 |
| ----------- | -------------------------------- | --------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| UC-001      | Xem dashboard tổng quan          | Owner, Admin                | Người dùng xem nhanh doanh thu, số đơn hàng, tồn kho, sản phẩm bán chạy.                                                   |
| UC-002      | Quản lý sản phẩm                 | Owner, Staff                | Người dùng tạo, sửa, xem danh sách và trạng thái sản phẩm.                                                                 |
| UC-003      | Quản lý danh mục sản phẩm        | Owner, Admin                | Người dùng tổ chức sản phẩm theo danh mục.                                                                                 |
| UC-004      | Quản lý đơn hàng                 | Owner, Staff                | Người dùng xem đơn, lọc đơn, cập nhật trạng thái đơn hàng.                                                                 |
| UC-005      | Xử lý đơn hàng mới               | Staff                       | Nhân viên kiểm tra thông tin đơn, xác nhận đơn và chuyển sang bước xử lý tiếp theo.                                        |
| UC-006      | Hủy đơn hàng                     | Owner, Staff                | Người dùng hủy đơn trong điều kiện hợp lệ.                                                                                 |
| UC-007      | Theo dõi tồn kho                 | Owner, Warehouse Staff      | Người dùng xem số lượng tồn, sản phẩm sắp hết hàng.                                                                        |
| UC-008      | Cập nhật nhập/xuất kho           | Warehouse Staff             | Nhân viên kho cập nhật biến động số lượng sản phẩm.                                                                        |
| UC-009      | Quản lý khách hàng               | Owner, Staff                | Người dùng xem thông tin khách hàng và lịch sử đơn hàng cơ bản.                                                            |
| UC-010      | Quản lý người dùng nội bộ        | Admin, Owner                | Người dùng thêm/sửa/xóa tài khoản nhân viên và phân quyền.                                                                 |
| UC-011      | Xem báo cáo bán hàng             | Owner, Admin                | Người dùng xem báo cáo doanh thu, đơn hàng, hiệu suất kênh bán.                                                            |
| UC-012      | Cấu hình kênh bán hàng           | ShopHub Admin, Owner        | Người dùng thiết lập hoặc quản lý các kênh bán được kết nối, trong đó kênh ưu tiên ban đầu là Lazada.                      |
| UC-013      | Đồng bộ dữ liệu Lazada real-time | Owner, Staff, ShopHub Admin | Hệ thống đồng bộ đơn hàng, sản phẩm, tồn kho hoặc trạng thái từ Lazada theo cơ chế gần real-time/real-time tùy API hỗ trợ. |
| UC-014      | Quản lý gói dịch vụ              | ShopHub Admin               | Bên ShopHub quản lý các gói như Pro, Premium cho từng shop.                                                                |
| UC-015      | Tích hợp vận chuyển              | Owner, Staff                | Người dùng kết nối hoặc sử dụng đơn vị vận chuyển để xử lý giao hàng.                                                      |
| UC-016      | Theo dõi lợi nhuận               | Owner, Accountant           | Người dùng xem báo cáo lợi nhuận dựa trên doanh thu, giá vốn, phí sàn, phí vận chuyển và chi phí liên quan.                |

---

# 7. Scope

## 7.1 In Scope

Các phần nên nằm trong phạm vi chính của dự án:

| Scope ID  | Module                                  | Mô tả                                                                                               |
| --------- | --------------------------------------- | --------------------------------------------------------------------------------------------------- |
| SCOPE-001 | Authentication & Authorization          | Đăng nhập, đăng xuất, phân quyền theo vai trò.                                                      |
| SCOPE-002 | Dashboard                               | Hiển thị số liệu tổng quan về đơn hàng, doanh thu, tồn kho, sản phẩm.                               |
| SCOPE-003 | Product Management                      | Quản lý sản phẩm, SKU, giá, trạng thái, danh mục.                                                   |
| SCOPE-004 | Category Management                     | Quản lý danh mục sản phẩm.                                                                          |
| SCOPE-005 | Order Management                        | Quản lý danh sách đơn hàng, chi tiết đơn, trạng thái đơn.                                           |
| SCOPE-006 | Inventory Management                    | Theo dõi tồn kho, cảnh báo tồn thấp, lịch sử nhập/xuất kho cơ bản.                                  |
| SCOPE-007 | Customer Management                     | Quản lý thông tin khách hàng và lịch sử mua hàng cơ bản.                                            |
| SCOPE-008 | User & Role Management                  | Quản lý tài khoản nội bộ và quyền truy cập.                                                         |
| SCOPE-009 | Sales Channel Management                | Quản lý danh sách kênh bán hàng được kết nối, ưu tiên kênh đầu tiên là Lazada.                      |
| SCOPE-010 | Real-time Data Sync                     | Đồng bộ dữ liệu real-time/gần real-time với Lazada cho các nghiệp vụ được hỗ trợ.                   |
| SCOPE-011 | Shipping Integration                    | Tích hợp đơn vị vận chuyển để hỗ trợ xử lý giao hàng.                                               |
| SCOPE-012 | Subscription, Plan & Payment Management | Quản lý gói dịch vụ cho từng shop như Pro, Premium và tích hợp thanh toán cho các gói này.          |
| SCOPE-013 | Profit Reports                          | Báo cáo doanh thu, chi phí và lợi nhuận ở mức shop.                                                 |
| SCOPE-014 | Audit Log cơ bản                        | Ghi nhận một số thao tác quan trọng như đổi trạng thái đơn, cập nhật tồn kho, thay đổi gói dịch vụ. |

## 7.2 Out of Scope

| Scope ID | Nội dung ngoài phạm vi       | Ghi chú                                                                                  |
| -------- | ---------------------------- | ---------------------------------------------------------------------------------------- |
| OUT-001  | Mobile app native            | Chưa cần ở giai đoạn đầu.                                                                |
| OUT-002  | AI recommendation            | Đưa vào phase sau nếu cần.                                                               |
| OUT-003  | Advanced CRM                 | Chưa làm automation marketing, segmentation nâng cao.                                    |
| OUT-004  | Full accounting system       | Có báo cáo lợi nhuận, nhưng không thay thế phần mềm kế toán chuyên dụng.                 |
| OUT-005  | Complex warehouse management | Không làm WMS nâng cao như barcode flow, bin location, wave picking trong giai đoạn đầu. |
| OUT-006  | Full logistics management    | Có tích hợp vận chuyển, nhưng không tự xây toàn bộ hệ thống logistics phức tạp.          |
| OUT-007  | Excel Import                 | Chưa cần import Excel ở giai đoạn đầu.                                                   |

---

# 8. Business Value

| Value ID | Giá trị                          | Mô tả                                                             |
| -------- | -------------------------------- | ----------------------------------------------------------------- |
| VAL-001  | Tiết kiệm thời gian vận hành     | Giảm việc kiểm tra thủ công trên từng kênh bán hàng.              |
| VAL-002  | Giảm sai sót dữ liệu             | Hạn chế sai lệch đơn hàng, sản phẩm, tồn kho.                     |
| VAL-003  | Tăng khả năng kiểm soát          | Chủ shop có cái nhìn tổng quan hơn về toàn bộ hoạt động bán hàng. |
| VAL-004  | Dễ mở rộng kinh doanh            | Khi thêm kênh bán mới, hệ thống vẫn có một nơi quản lý tập trung. |
| VAL-005  | Cải thiện trải nghiệm khách hàng | Đơn hàng được xử lý nhanh và chính xác hơn.                       |
| VAL-006  | Hỗ trợ ra quyết định             | Báo cáo giúp người bán biết sản phẩm/kênh nào hiệu quả.           |

---

# 9. Success Metrics

Các chỉ số dưới đây dùng để đánh giá dự án có thành công hay không.

| Metric ID  | Chỉ số                          | Cách hiểu                                                       | Target ban đầu |
| ---------- | ------------------------------- | --------------------------------------------------------------- | -------------- |
| METRIC-001 | Thời gian xử lý đơn hàng        | Thời gian từ lúc đơn mới xuất hiện đến khi được xác nhận/xử lý. | TBD            |
| METRIC-002 | Tỷ lệ đơn bị sót                | Số đơn không được xử lý đúng hạn.                               | TBD            |
| METRIC-003 | Tỷ lệ sai lệch tồn kho          | Chênh lệch giữa tồn kho hệ thống và tồn kho thực tế.            | TBD            |
| METRIC-004 | Số kênh bán được quản lý        | Số kênh bán hàng được đưa vào hệ thống.                         | TBD            |
| METRIC-005 | Thời gian tạo/cập nhật sản phẩm | Thời gian người dùng cần để tạo hoặc cập nhật sản phẩm.         | TBD            |
| METRIC-006 | Tần suất sử dụng dashboard      | Số lần người dùng truy cập dashboard để theo dõi số liệu.       | TBD            |
| METRIC-007 | Mức độ giảm thao tác thủ công   | Số thao tác lặp lại được giảm sau khi dùng hệ thống.            | TBD            |

---

# 10. Key Modules

Danh sách module ở mức tổng quan. Chi tiết sẽ được định nghĩa trong `03_FEATURE_LIST.md`.

| Module ID        | Module Name                    | Mục đích                                                   |
| ---------------- | ------------------------------ | ---------------------------------------------------------- |
| MOD-AUTH         | Authentication & Authorization | Quản lý đăng nhập, tài khoản và phân quyền.                |
| MOD-DASHBOARD    | Dashboard                      | Hiển thị tổng quan hoạt động kinh doanh.                   |
| MOD-PRODUCT      | Product Management             | Quản lý sản phẩm.                                          |
| MOD-CATEGORY     | Category Management            | Quản lý danh mục sản phẩm.                                 |
| MOD-ORDER        | Order Management               | Quản lý đơn hàng.                                          |
| MOD-INVENTORY    | Inventory Management           | Quản lý tồn kho.                                           |
| MOD-CUSTOMER     | Customer Management            | Quản lý khách hàng.                                        |
| MOD-CHANNEL      | Sales Channel Management       | Quản lý các kênh bán hàng, ưu tiên Lazada ở giai đoạn đầu. |
| MOD-SYNC         | Real-time Sync                 | Đồng bộ dữ liệu giữa ShopHub và kênh bán như Lazada.       |
| MOD-SHIPPING     | Shipping Integration           | Tích hợp đơn vị vận chuyển và theo dõi giao hàng.          |
| MOD-SUBSCRIPTION | Subscription & Billing         | Quản lý gói dịch vụ Pro, Premium cho từng shop.            |
| MOD-REPORT       | Reports & Analytics            | Báo cáo doanh thu, chi phí, lợi nhuận và hiệu suất kênh.   |
| MOD-USER         | User & Role Management         | Quản lý người dùng nội bộ và vai trò.                      |
| MOD-AUDIT        | Audit Log                      | Ghi nhận lịch sử thao tác quan trọng.                      |
| MOD-SETTING      | System Settings                | Cấu hình hệ thống.                                         |

---

# 11. Initial Feature Direction

Chi tiết tính năng sẽ nằm trong `03_FEATURE_LIST.md`. Phần này chỉ định hướng ban đầu.

| Feature Group          | Feature Direction                                                                                           |
| ---------------------- | ----------------------------------------------------------------------------------------------------------- |
| Authentication         | Đăng nhập, đăng xuất, kiểm tra phiên đăng nhập.                                                             |
| Authorization          | Phân quyền theo vai trò: Admin, Owner, Staff, Warehouse Staff.                                              |
| Dashboard              | Tổng quan doanh thu, đơn hàng, tồn kho, cảnh báo.                                                           |
| Products               | Danh sách sản phẩm, chi tiết sản phẩm, tạo/sửa sản phẩm, trạng thái sản phẩm.                               |
| Categories             | Danh sách danh mục, tạo/sửa/xóa danh mục.                                                                   |
| Orders                 | Danh sách đơn hàng, chi tiết đơn, cập nhật trạng thái, hủy đơn.                                             |
| Inventory              | Xem tồn kho, cập nhật nhập/xuất kho, cảnh báo tồn thấp.                                                     |
| Customers              | Danh sách khách hàng, chi tiết khách hàng, lịch sử mua hàng.                                                |
| Channels               | Danh sách kênh bán, trạng thái kết nối, cấu hình Lazada ở giai đoạn đầu.                                    |
| Sync                   | Đồng bộ real-time/gần real-time dữ liệu đơn hàng, sản phẩm, tồn kho, trạng thái từ Lazada tùy giới hạn API. |
| Shipping               | Tích hợp đơn vị vận chuyển, theo dõi trạng thái giao hàng và liên kết với trạng thái đơn hàng.              |
| Subscription & Payment | Quản lý gói dịch vụ cho từng shop như Pro, Premium và tích hợp thanh toán gói dịch vụ.                      |
| Reports                | Doanh thu, đơn hàng theo thời gian, sản phẩm bán chạy, hiệu suất kênh, lợi nhuận.                           |
| Users                  | Quản lý user nội bộ, gán role, khóa/mở tài khoản.                                                           |
| Audit                  | Lưu log các thao tác quan trọng.                                                                            |

---

# 12. Constraints

## 12.1 Business Constraints

| Constraint ID | Constraint                             | Mô tả                                                                                                           |
| ------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| CON-BIZ-001   | Phục vụ shop nhỏ và vừa                | Hệ thống cần đủ mạnh để quản lý toàn bộ hoạt động bán hàng, nhưng không quá nặng như ERP doanh nghiệp lớn.      |
| CON-BIZ-002   | Ưu tiên Lazada ở giai đoạn đầu         | Không tích hợp tất cả kênh ngay từ đầu để kiểm soát độ phức tạp.                                                |
| CON-BIZ-003   | Cần hỗ trợ nhiều shop/tenant           | Mỗi shop có dữ liệu, user, gói dịch vụ và cấu hình riêng.                                                       |
| CON-BIZ-004   | Có mô hình gói dịch vụ và thanh toán   | Hệ thống cần hỗ trợ các gói như Pro, Premium cho từng shop, đồng thời có tích hợp thanh toán cho các gói này.   |
| CON-BIZ-005   | Cần báo cáo lợi nhuận                  | Không chỉ báo cáo doanh thu; cần tính đến giá vốn, phí sàn, phí vận chuyển và chi phí liên quan nếu có dữ liệu. |
| CON-BIZ-006   | Chưa cần import Excel                  | Giai đoạn đầu không ưu tiên import Excel.                                                                       |
| CON-BIZ-007   | Ngôn ngữ giao diện chính là tiếng Việt | Toàn bộ UI/UX writing, message lỗi, label nên ưu tiên tiếng Việt.                                               |

## 12.2 Technical Constraints

| Constraint ID | Constraint                                 | Mô tả                                                                                                                                                               |
| ------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CON-TECH-001  | Tech stack chính                           | Backend dùng ASP.NET Core, frontend dùng Vite. Framework frontend cụ thể cần chốt ở Technical Architecture, mặc định nên là React + Vite nếu không có yêu cầu khác. |
| CON-TECH-002  | Real-time/gần real-time sync               | Hệ thống cần đồng bộ dữ liệu với Lazada theo thời gian thực hoặc gần thời gian thực, phụ thuộc khả năng API/webhook của Lazada.                                     |
| CON-TECH-003  | Multi-tenant architecture                  | Dữ liệu giữa các shop phải được cô lập rõ ràng bằng tenant/shop boundary.                                                                                           |
| CON-TECH-004  | Role-based access control                  | Cần phân quyền riêng cho bên shop và bên hệ thống ShopHub.                                                                                                          |
| CON-TECH-005  | Inventory model phức tạp hơn cơ bản        | Cần phân biệt tồn kho thực tế, tồn kho khả dụng và tồn kho đã giữ chỗ.                                                                                              |
| CON-TECH-006  | Shipping-ready architecture                | Order flow cần thiết kế sẵn core logic vận chuyển để sau này có thể tích hợp API của đơn vị vận chuyển mà không phải sửa lại toàn bộ nghiệp vụ.                     |
| CON-TECH-007  | API cần có format nhất quán                | Frontend/backend cần thống nhất request, response, error format.                                                                                                    |
| CON-TECH-008  | Audit log bắt buộc cho thao tác quan trọng | Cần ghi log thay đổi đơn hàng, tồn kho, gói dịch vụ, user/role và đồng bộ dữ liệu.                                                                                  |

---

# 13. Assumptions

Các giả định ban đầu, cần xác nhận lại trong quá trình làm tài liệu chi tiết.

| Assumption ID | Giả định                                                       | Ảnh hưởng nếu sai                                                         |
| ------------- | -------------------------------------------------------------- | ------------------------------------------------------------------------- |
| ASM-001       | Người dùng chính là chủ shop nhỏ/vừa và nhân viên vận hành.    | Scope sẽ tập trung vào vận hành thực tế, dễ dùng, không quá nặng như ERP. |
| ASM-002       | Giai đoạn đầu ưu tiên web dashboard.                           | Nếu cần mobile app ngay, scope thiết kế/dev sẽ tăng.                      |
| ASM-003       | Hệ thống sẽ có nhiều role: bên shop và bên ShopHub.            | Permission Matrix phải chia rõ quyền của seller-side và platform-side.    |
| ASM-004       | Product, Order, Inventory là 3 module lõi.                     | Nếu dự án ưu tiên marketing/CRM hơn, feature list sẽ thay đổi.            |
| ASM-005       | Kênh bán đầu tiên được ưu tiên là Lazada.                      | Các kênh khác sẽ đưa vào phase sau.                                       |
| ASM-006       | Hệ thống cần đồng bộ real-time/gần real-time.                  | Kiến trúc sync, queue, webhook, retry và audit log cần được thiết kế kỹ.  |
| ASM-007       | Hệ thống có subscription plan cho từng shop.                   | Cần module Subscription & Billing hoặc ít nhất Plan Management.           |
| ASM-008       | Hệ thống có hỗ trợ nhiều cửa hàng/tenant.                      | Data model phải có shopId/tenantId hoặc cơ chế tương đương.               |
| ASM-009       | Có báo cáo lợi nhuận nhưng không thay thế kế toán chuyên dụng. | Cần lưu/nhập được giá vốn và các loại phí liên quan.                      |
| ASM-010       | Ngôn ngữ giao diện chính là tiếng Việt.                        | UX Writing và error message cần nhất quán bằng tiếng Việt.                |

---

# 14. Risks

| Risk ID  | Rủi ro                                      | Mức độ | Cách giảm rủi ro                                                                      |
| -------- | ------------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| RISK-001 | Scope quá rộng                              | High   | Chốt rõ phase đầu: shop nhỏ/vừa, Lazada, module lõi trước.                            |
| RISK-002 | Tài liệu bị lệch nhau                       | High   | Dùng Traceability Matrix và ID cố định cho feature/flow/screen/API.                   |
| RISK-003 | Đồng bộ Lazada real-time phức tạp           | High   | Kiểm tra khả năng API/webhook, thiết kế retry, sync log, trạng thái lỗi đồng bộ.      |
| RISK-004 | Sai lệch tồn kho                            | High   | Thiết kế rõ Physical Stock, Available Stock, Reserved Stock và thời điểm trừ/nhả tồn. |
| RISK-005 | Multi-tenant bị rò dữ liệu                  | High   | Bắt buộc có tenant/shop boundary trong database, API, permission và query filter.     |
| RISK-006 | Phân quyền giữa ShopHub và shop bị lẫn      | High   | Tách rõ platform roles và seller roles trong Permission Matrix.                       |
| RISK-007 | Báo cáo lợi nhuận sai                       | Medium | Định nghĩa rõ công thức lợi nhuận, nguồn dữ liệu giá vốn/phí sàn/phí vận chuyển.      |
| RISK-008 | Giao diện dashboard quá rối                 | Medium | Chốt UI/UX Direction và ưu tiên dữ liệu quan trọng.                                   |
| RISK-009 | API thiếu nhất quán                         | Medium | Tạo API Specification và Development Guideline trước khi implement.                   |
| RISK-010 | Tích hợp vận chuyển làm phức tạp order flow | Medium | Xác định trạng thái vận chuyển và mapping với trạng thái đơn hàng.                    |

---

# 15. Prioritization Direction

## 15.1 Must-have

Các phần nên có trong phiên bản đầu tiên:

* Authentication & Authorization
* User Roles cơ bản
* Dashboard tổng quan
* Product Management
* Category Management
* Order Management
* Inventory Management cơ bản
* Customer Management cơ bản
* Permission Matrix
* API Specification cho module chính

## 15.2 Should-have

Các phần nên có nếu đủ thời gian:

* Sales Channel Management cho Lazada
* Real-time/gần real-time sync với Lazada
* Shipping Integration cơ bản
* Subscription Plan Management cho Pro/Premium
* Profit Reports cơ bản
* Audit Log cơ bản
* Notification/toast trong hệ thống

## 15.3 Could-have

Các phần có thể làm sau:

* Tích hợp thêm kênh bán ngoài Lazada
* Tích hợp thanh toán tự động cho subscription nếu cần
* Advanced reports
* CRM nâng cao
* Marketing automation

## 15.4 Later

Các phần nên để phase sau:

* Mobile app native
* AI recommendation
* Forecast inventory
* Advanced warehouse management
* Full accounting module
* Loyalty program nâng cao

---

# 16. Initial User Experience Direction

Giao diện của Multi-channel E-commerce Hub nên đi theo hướng:

| UX Direction ID | Định hướng         | Mô tả                                                             |
| --------------- | ------------------ | ----------------------------------------------------------------- |
| UX-001          | Dashboard-first    | Người dùng vào hệ thống là thấy ngay tình hình kinh doanh.        |
| UX-002          | Data-focused       | Ưu tiên bảng, filter, search, trạng thái rõ ràng.                 |
| UX-003          | Operation-friendly | Tối ưu cho thao tác thường ngày của nhân viên.                    |
| UX-004          | Low cognitive load | Không nhồi quá nhiều thông tin trên một màn.                      |
| UX-005          | Clear status       | Trạng thái đơn hàng, tồn kho, sản phẩm phải dễ nhận biết.         |
| UX-006          | Safe actions       | Hành động nguy hiểm như xóa/hủy cần confirm.                      |
| UX-007          | Fast filtering     | Danh sách đơn/sản phẩm cần lọc nhanh theo trạng thái, kênh, ngày. |

Chi tiết UI/UX sẽ được định nghĩa trong `10_UI_UX_DIRECTION.md`.

---

# 17. Initial Technical Direction

Đây là định hướng kỹ thuật ban đầu đã được cập nhật theo câu trả lời Open Questions.

| Area           | Direction                                                                                                                                           |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Frontend       | Vite. Nếu không có yêu cầu khác, đề xuất dùng React + Vite để phù hợp dashboard quản trị.                                                           |
| Backend        | ASP.NET Core Web API.                                                                                                                               |
| Database       | Thiết kế multi-tenant, mỗi dữ liệu nghiệp vụ cần gắn với shop/tenant tương ứng.                                                                     |
| Authentication | Đăng nhập bằng email/password ở giai đoạn đầu.                                                                                                      |
| Authorization  | Role-based access control, tách quyền bên shop và quyền bên ShopHub platform.                                                                       |
| API Style      | RESTful API, response format nhất quán.                                                                                                             |
| Sync           | Cần module đồng bộ Lazada real-time/gần real-time, có sync log, retry, trạng thái lỗi.                                                              |
| Inventory      | Cần phân biệt Physical Stock, Available Stock, Reserved Stock.                                                                                      |
| Shipping       | Giai đoạn đầu xây core shipping abstraction, shipment status, shipping provider model; API vận chuyển cụ thể sẽ tích hợp sau khi chốt nhà cung cấp. |
| Subscription   | Cần quản lý gói dịch vụ như Pro, Premium cho từng shop.                                                                                             |
| Reporting      | Cần báo cáo doanh thu và lợi nhuận.                                                                                                                 |
| Language       | Giao diện và thông báo chính bằng tiếng Việt.                                                                                                       |
| Logging        | Log lỗi hệ thống, lỗi đồng bộ, thao tác quan trọng.                                                                                                 |
| Audit          | Ghi lại thay đổi trạng thái đơn hàng, tồn kho, user/role, gói dịch vụ.                                                                              |
| Scalability    | Thiết kế module tách biệt để dễ mở rộng thêm kênh bán sau Lazada.                                                                                   |

## 17.1 Inventory Direction

Đề xuất chuẩn hóa cách gọi tồn kho như sau để tránh nhầm lẫn khi thiết kế database và business flow:

| Concept                  | Tên gợi ý          | Mô tả                                                                                             | Thời điểm thay đổi                                                             |
| ------------------------ | ------------------ | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| Kho vật lý               | Physical Warehouse | Mỗi shop có 1 kho vật lý chính. Đây là nơi phản ánh hàng thực tế của shop.                        | Được tạo theo từng shop/tenant.                                                |
| Kho thực                 | Physical Stock     | Số lượng hàng thực tế trong kho vật lý của shop.                                                  | Chỉ giảm khi hàng thật sự được giao/xuất kho. Tăng khi nhập kho hoặc hoàn kho. |
| Kho khả dụng / kho ảo    | Available Stock    | Số lượng có thể bán trên kênh bán. Đây là số lượng nên đồng bộ lên Lazada hoặc các kênh bán khác. | Giảm khi đơn được xác nhận/giữ hàng. Tăng khi hủy đơn hoặc nhả giữ hàng.       |
| Tồn chờ / tồn đã giữ chỗ | Reserved Stock     | Số lượng đã được giữ cho đơn hàng nhưng chưa xuất kho thực tế.                                    | Tăng khi xác nhận đơn, giảm khi giao hàng hoặc hủy đơn.                        |

Rule đề xuất:

| Rule ID      | Rule                                                                                                   |
| ------------ | ------------------------------------------------------------------------------------------------------ |
| RULE-INV-001 | Mỗi shop có 1 kho vật lý chính ở giai đoạn đầu.                                                        |
| RULE-INV-002 | Khi đơn mới được xác nhận, hệ thống tăng Reserved Stock và giảm Available Stock.                       |
| RULE-INV-003 | Khi đơn được giao/xuất kho thành công, hệ thống giảm Physical Stock và giảm Reserved Stock.            |
| RULE-INV-004 | Khi đơn bị hủy trước khi giao, hệ thống giảm Reserved Stock và tăng lại Available Stock.               |
| RULE-INV-005 | Physical Stock không nên giảm ngay khi đơn mới được tạo, vì hàng chưa thực sự rời kho.                 |
| RULE-INV-006 | Available Stock là số lượng nên đồng bộ lên kênh bán như Lazada để tránh bán vượt số lượng có thể bán. |
| RULE-INV-007 | Không hỗ trợ nhiều kho vật lý cho một shop ở giai đoạn đầu. Nếu cần nhiều kho, đưa vào phase sau.      |

## 17.2 Order Status Direction

Trạng thái đơn hàng cần hỗ trợ 2 nguồn:

| Source                    | Mô tả                                                                                                                |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Marketplace Status        | Trạng thái đến từ sàn như Lazada. Hệ thống cần lưu trạng thái gốc để đối soát và đồng bộ.                            |
| ShopHub Normalized Status | Trạng thái chuẩn hóa nội bộ của ShopHub để dashboard, báo cáo, tồn kho và vận chuyển hoạt động nhất quán.            |
| Shop-defined Status       | Một số shop có thể cần trạng thái nội bộ riêng để quản lý vận hành, nhưng không được phá vỡ normalized status chính. |

Rule đề xuất:

| Rule ID               | Rule                                                                                                      |
| --------------------- | --------------------------------------------------------------------------------------------------------- |
| RULE-ORDER-STATUS-001 | Hệ thống phải lưu trạng thái gốc từ Lazada nếu đơn đến từ Lazada.                                         |
| RULE-ORDER-STATUS-002 | Hệ thống phải mapping trạng thái sàn sang trạng thái chuẩn của ShopHub.                                   |
| RULE-ORDER-STATUS-003 | Shop có thể cấu hình trạng thái vận hành nội bộ nếu cần, nhưng trạng thái này chỉ phục vụ quản lý nội bộ. |
| RULE-ORDER-STATUS-004 | Báo cáo, tồn kho và permission nên dựa trên ShopHub Normalized Status để tránh lệch giữa các kênh.        |

## 17.3 Subscription Plan Initial Direction

Do chưa có phương án chính thức cho gói dịch vụ, tạm đề xuất giới hạn ban đầu như sau để làm cơ sở cho Feature List, Permission Matrix và Technical Architecture. Các giới hạn này có thể thay đổi sau.

| Plan                | Mục tiêu                               | Giới hạn đề xuất                                                                                                                       |
| ------------------- | -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Free / Trial        | Cho shop thử hệ thống                  | 1 shop, 1 kênh bán, tối đa 2 user, giới hạn số đơn/tháng, báo cáo cơ bản.                                                              |
| Pro                 | Gói chính cho shop nhỏ/vừa             | 1 shop, Lazada + thêm kênh khác khi có, tối đa 10 user, đồng bộ real-time/gần real-time, báo cáo doanh thu và lợi nhuận cơ bản.        |
| Premium             | Gói nâng cao cho shop vận hành lớn hơn | 1 shop, nhiều kênh bán khi hệ thống hỗ trợ, nhiều user hơn, báo cáo nâng cao, ưu tiên đồng bộ, audit log chi tiết hơn, hỗ trợ tốt hơn. |
| Enterprise / Custom | Gói tùy chỉnh nếu sau này cần          | Giới hạn và tính năng thỏa thuận riêng. Không phải trọng tâm giai đoạn đầu.                                                            |

Rule đề xuất:

| Rule ID       | Rule                                                                                                           |
| ------------- | -------------------------------------------------------------------------------------------------------------- |
| RULE-PLAN-001 | Mỗi shop phải thuộc một gói dịch vụ.                                                                           |
| RULE-PLAN-002 | Gói dịch vụ có thể giới hạn số user, số kênh bán, số đơn/tháng, báo cáo, audit log và tính năng nâng cao.      |
| RULE-PLAN-003 | Payment dùng để thanh toán gói dịch vụ của shop, không phải thanh toán đơn hàng của khách mua ở giai đoạn đầu. |
| RULE-PLAN-004 | Cần hỗ trợ trạng thái subscription như Trial, Active, Past Due, Suspended, Cancelled.                          |

## 17.4 Profit Report Direction

Báo cáo lợi nhuận cần cho phép shop tự điều chỉnh công thức hoặc các loại chi phí được tính vào lợi nhuận.

| Concept            | Mô tả                                                                            |
| ------------------ | -------------------------------------------------------------------------------- |
| Revenue            | Doanh thu từ đơn hàng.                                                           |
| Cost of Goods Sold | Giá vốn sản phẩm, do shop nhập hoặc cấu hình.                                    |
| Marketplace Fees   | Phí sàn như phí Lazada nếu lấy được từ dữ liệu đồng bộ hoặc nhập tay/cấu hình.   |
| Shipping Fees      | Phí vận chuyển, có thể lấy từ đơn vị vận chuyển hoặc cấu hình.                   |
| Discounts          | Giảm giá, voucher, khuyến mãi.                                                   |
| Other Costs        | Chi phí khác do shop tự định nghĩa.                                              |
| Profit Formula     | Công thức lợi nhuận do hệ thống đề xuất mặc định nhưng cho phép shop điều chỉnh. |

Rule đề xuất:

| Rule ID         | Rule                                                                                                                        |
| --------------- | --------------------------------------------------------------------------------------------------------------------------- |
| RULE-PROFIT-001 | Hệ thống cần có công thức lợi nhuận mặc định.                                                                               |
| RULE-PROFIT-002 | Shop có thể bật/tắt hoặc điều chỉnh các thành phần chi phí trong công thức lợi nhuận.                                       |
| RULE-PROFIT-003 | Nếu thiếu dữ liệu giá vốn hoặc phí, báo cáo phải hiển thị trạng thái dữ liệu chưa đầy đủ thay vì tính sai một cách âm thầm. |
| RULE-PROFIT-004 | Công thức lợi nhuận cần được lưu theo từng shop/tenant.                                                                     |

---

# 18. Related Documents

| Document                        | Relationship                                           |
| ------------------------------- | ------------------------------------------------------ |
| `00_PROJECT_IDEA.md`            | Nguồn ý tưởng ban đầu.                                 |
| `02_USER_ROLES.md`              | Phân tích chi tiết từng nhóm người dùng.               |
| `03_FEATURE_LIST.md`            | Chuyển scope và module thành danh sách feature cụ thể. |
| `04_BUSINESS_FLOW.md`           | Mô tả luồng nghiệp vụ chính.                           |
| `05_SITEMAP_AND_SCREEN_LIST.md` | Chuyển feature/flow thành danh sách màn hình.          |
| `06_SCREEN_REQUIREMENTS/`       | Mô tả chi tiết từng màn hình.                          |
| `07_DATA_MODEL.md`              | Chuyển nghiệp vụ thành entity và dữ liệu.              |
| `08_API_SPECIFICATION.md`       | Chốt contract giữa frontend và backend.                |
| `09_PERMISSION_MATRIX.md`       | Chốt quyền theo role/action.                           |
| `10_UI_UX_DIRECTION.md`         | Chốt hướng thiết kế giao diện.                         |
| `11_TECHNICAL_ARCHITECTURE.md`  | Chốt hướng kiến trúc kỹ thuật.                         |
| `00_TRACEABILITY_MATRIX.md`     | Mapping feature-flow-screen-api-permission-entity.     |

---

# 19. Open Questions

Các câu hỏi đã được cập nhật theo câu trả lời mới nhất. Những mục đã có định hướng sẽ chuyển sang trạng thái `Answered`. Những mục còn cần làm rõ chi tiết sẽ giữ `Open` hoặc `Need Detail`.

| Question ID | Question                                                                      | Answer / Direction                                                                                                                                                                                                                                       | Related Area           | Impact                                      | Status                                            |
| ----------- | ----------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------- | ------------------------------------------- | ------------------------------------------------- |
| Q-001       | Dự án phục vụ shop nhỏ/vừa hay doanh nghiệp lớn?                              | Phục vụ shop nhỏ và vừa.                                                                                                                                                                                                                                 | Target Users           | Ảnh hưởng scope, UI, permission, data model | Answered                                          |
| Q-002       | Giai đoạn đầu cần tích hợp kênh bán cụ thể nào?                               | Lazada.                                                                                                                                                                                                                                                  | Sales Channel          | Ảnh hưởng API, data sync, scope             | Answered                                          |
| Q-003       | Có cần đồng bộ dữ liệu real-time không, hay chỉ nhập/thao tác thủ công trước? | Cần đồng bộ real-time/gần real-time.                                                                                                                                                                                                                     | Integration            | Ảnh hưởng kiến trúc và độ phức tạp          | Answered                                          |
| Q-004       | Có cần xử lý thanh toán không?                                                | Có cả quản lý gói dịch vụ và tích hợp thanh toán cho các gói Pro/Premium. Payment ở đây phục vụ thanh toán subscription của shop, không phải thanh toán đơn hàng của khách mua ở giai đoạn đầu.                                                          | Subscription / Payment | Ảnh hưởng module, API, bảo mật              | Answered                                          |
| Q-005       | Có cần tích hợp đơn vị vận chuyển không?                                      | Có.                                                                                                                                                                                                                                                      | Shipping               | Ảnh hưởng order flow                        | Answered                                          |
| Q-006       | Có bao nhiêu role nội bộ ở phiên bản đầu?                                     | Cần chia 2 nhóm role: seller-side roles của từng shop và platform-side roles của hệ thống ShopHub. Chi tiết role/action sẽ chốt trong 02_USER_ROLES.md và 09_PERMISSION_MATRIX.md.                                                                       | Permission             | Ảnh hưởng Permission Matrix                 | Answered                                          |
| Q-007       | Đơn hàng có những trạng thái nào?                                             | Trạng thái phụ thuộc vào trạng thái đơn của sàn như Lazada và/hoặc trạng thái nội bộ do shop quy định. Hệ thống cần lưu marketplace status, normalized ShopHub status và có thể hỗ trợ shop-defined status.                                              | Order Flow             | Ảnh hưởng Business Flow, UI, API            | Answered                                          |
| Q-008       | Tồn kho được trừ khi nào: khi tạo đơn, xác nhận đơn hay giao hàng?            | Dùng mô hình Physical Stock / Available Stock / Reserved Stock. Available giảm khi xác nhận đơn, Physical giảm khi giao/xuất kho.                                                                                                                        | Inventory              | Ảnh hưởng rule nghiệp vụ rất lớn            | Answered                                          |
| Q-009       | Có cần quản lý nhiều kho không?                                               | Mỗi shop có 1 kho vật lý ở giai đoạn đầu. Kho vật lý khác với Available Stock và Reserved Stock. Không hỗ trợ nhiều kho vật lý cho một shop ở giai đoạn đầu.                                                                                             | Inventory              | Ảnh hưởng Data Model                        | Answered                                          |
| Q-010       | Có cần import/export Excel không?                                             | Chưa cần import Excel. Export chưa chốt.                                                                                                                                                                                                                 | Product/Order          | Ảnh hưởng feature và UI                     | Partially Answered                                |
| Q-011       | Có cần báo cáo lợi nhuận hay chỉ doanh thu?                                   | Cần báo cáo lợi nhuận.                                                                                                                                                                                                                                   | Reports                | Ảnh hưởng dữ liệu cost/expense              | Answered                                          |
| Q-012       | Có cần khách hàng đăng nhập vào hệ thống không?                               | Không. Khách mua hàng cuối không được đăng nhập vào ShopHub. ShopHub chỉ là dashboard/hệ thống quản trị nội bộ cho shop và đội ngũ vận hành ShopHub.                                                                                                     | Customer               | Ảnh hưởng auth và frontend public           | Answered                                          |
| Q-013       | Tech stack chính có phải ASP.NET Core + React/Next.js không?                  | Backend ASP.NET Core, frontend Vite. Framework frontend cụ thể cần chốt, đề xuất React + Vite.                                                                                                                                                           | Technical              | Ảnh hưởng Technical Architecture            | Partially Answered                                |
| Q-014       | Có cần hỗ trợ nhiều cửa hàng/tenant không?                                    | Có hỗ trợ nhiều cửa hàng/tenant.                                                                                                                                                                                                                         | Architecture           | Ảnh hưởng database và permission            | Answered                                          |
| Q-015       | Ngôn ngữ giao diện là tiếng Việt, tiếng Anh hay đa ngôn ngữ?                  | Tiếng Việt.                                                                                                                                                                                                                                              | UI/UX                  | Ảnh hưởng UX Writing                        | Answered                                          |
| Q-016       | Lazada sync cần đồng bộ những dữ liệu nào?                                    | Gần như tất cả dữ liệu quan trọng: sản phẩm, tồn kho khả dụng, đơn hàng, trạng thái đơn, trạng thái giao hàng nếu có, khách hàng, phí sàn, khuyến mãi/giảm giá và dữ liệu đối soát nếu API hỗ trợ. Cần xác minh giới hạn API Lazada ở tài liệu tích hợp. | Integration            | Ảnh hưởng API, sync job, data model         | Answered at Product Level / Need API Verification |
| Q-017       | Đơn vị vận chuyển nào cần tích hợp đầu tiên?                                  | Chưa chốt nhà vận chuyển. Giai đoạn đầu thiết kế core shipping model/flow trước để sau này chỉ cần tích hợp API nhà vận chuyển cụ thể vào.                                                                                                               | Shipping               | Ảnh hưởng integration và order flow         | Partially Answered                                |
| Q-018       | Công thức lợi nhuận chuẩn là gì?                                              | Hệ thống cần có công thức mặc định nhưng cho phép từng shop tự điều chỉnh thành phần chi phí/cách tính lợi nhuận.                                                                                                                                        | Reports                | Ảnh hưởng Data Model và Report              | Answered                                          |
| Q-019       | Các gói Pro/Premium khác nhau ở điểm nào?                                     | Chưa có phương án chính thức. Tạm đề xuất Free/Trial, Pro, Premium, Enterprise/Custom với giới hạn theo số user, số kênh, số đơn/tháng, báo cáo, audit log và mức hỗ trợ.                                                                                | Subscription           | Ảnh hưởng Permission, Feature Flag, Billing | Draft Proposed                                    |

---

# 20. Decisions Log

| Decision ID | Decision                                                                      | Reason                                                                                                 | Related Docs                                                              | Date |
| ----------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------- | ---- |
| DEC-001     | Dự án được định hướng là web dashboard quản lý bán hàng đa kênh.              | Phù hợp mục tiêu quản lý tập trung và vận hành nội bộ.                                                 | All                                                                       | TBD  |
| DEC-002     | Product, Order, Inventory là các module lõi ban đầu.                          | Đây là 3 nhóm nghiệp vụ quan trọng nhất của hệ thống e-commerce management.                            | 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md                                   | TBD  |
| DEC-003     | Hệ thống cần dùng ID cố định để trace tài liệu.                               | Tránh lệch giữa feature, flow, screen, API và permission.                                              | 00_TRACEABILITY_MATRIX.md                                                 | TBD  |
| DEC-004     | Dự án phục vụ shop nhỏ và vừa.                                                | Phù hợp mục tiêu quản lý toàn bộ vận hành cho nhà bán hàng nhỏ.                                        | 02_USER_ROLES.md, 03_FEATURE_LIST.md                                      | TBD  |
| DEC-005     | Kênh bán đầu tiên được ưu tiên là Lazada.                                     | Giảm scope tích hợp đa kênh ở giai đoạn đầu.                                                           | 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 08_API_SPECIFICATION.md          | TBD  |
| DEC-006     | Hệ thống cần hỗ trợ real-time/gần real-time sync.                             | Đảm bảo dữ liệu đơn hàng/tồn kho/trạng thái kênh bán được cập nhật kịp thời.                           | 11_TECHNICAL_ARCHITECTURE.md                                              | TBD  |
| DEC-007     | Hệ thống cần hỗ trợ multi-tenant/multiple shops.                              | Mỗi shop cần dữ liệu và cấu hình riêng.                                                                | 07_DATA_MODEL.md, 09_PERMISSION_MATRIX.md                                 | TBD  |
| DEC-008     | Hệ thống cần phân biệt quyền bên shop và quyền bên ShopHub.                   | Tránh lẫn giữa người bán và người vận hành nền tảng.                                                   | 02_USER_ROLES.md, 09_PERMISSION_MATRIX.md                                 | TBD  |
| DEC-009     | Inventory dùng mô hình Physical Stock / Available Stock / Reserved Stock.     | Tránh sai lệch giữa hàng thực tế, hàng có thể bán và hàng đã giữ cho đơn.                              | 04_BUSINESS_FLOW.md, 07_DATA_MODEL.md                                     | TBD  |
| DEC-010     | Backend dùng ASP.NET Core, frontend dùng Vite.                                | Đây là tech stack định hướng ban đầu.                                                                  | 11_TECHNICAL_ARCHITECTURE.md                                              | TBD  |
| DEC-011     | Ngôn ngữ giao diện chính là tiếng Việt.                                       | Phù hợp người dùng mục tiêu.                                                                           | 10_UI_UX_DIRECTION.md                                                     | TBD  |
| DEC-012     | Hệ thống có cả quản lý gói và tích hợp thanh toán subscription.               | ShopHub cần bán gói dịch vụ như Pro/Premium cho từng shop.                                             | 03_FEATURE_LIST.md, 08_API_SPECIFICATION.md, 11_TECHNICAL_ARCHITECTURE.md | TBD  |
| DEC-013     | Mỗi shop có 1 kho vật lý ở giai đoạn đầu.                                     | Giảm độ phức tạp so với multi-warehouse nhưng vẫn đủ quản lý tồn kho thực tế.                          | 04_BUSINESS_FLOW.md, 07_DATA_MODEL.md                                     | TBD  |
| DEC-014     | Trạng thái đơn cần lưu cả trạng thái sàn và trạng thái chuẩn hóa của ShopHub. | Lazada hoặc các sàn khác có status riêng, cần chuẩn hóa để dashboard/report hoạt động nhất quán.       | 04_BUSINESS_FLOW.md, 08_API_SPECIFICATION.md                              | TBD  |
| DEC-015     | Shipping integration sẽ đi theo hướng core-first.                             | Chưa chốt nhà vận chuyển nên cần thiết kế abstraction trước, tích hợp API sau.                         | 04_BUSINESS_FLOW.md, 11_TECHNICAL_ARCHITECTURE.md                         | TBD  |
| DEC-016     | Báo cáo lợi nhuận cho phép shop tự điều chỉnh công thức.                      | Mỗi shop có cách tính chi phí và lợi nhuận khác nhau.                                                  | 07_DATA_MODEL.md, 03_FEATURE_LIST.md                                      | TBD  |
| DEC-017     | Khách mua hàng cuối không được đăng nhập vào ShopHub.                         | ShopHub là hệ thống quản trị nội bộ cho shop và đội ngũ vận hành nền tảng, không phải customer portal. | 02_USER_ROLES.md, 03_FEATURE_LIST.md, 11_TECHNICAL_ARCHITECTURE.md        | TBD  |

---

# 21. Change Log

| Version | Date | Change                                                                                                                                                                                                                          | Author |
| ------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| 0.1     | TBD  | Initial draft for Project Brief                                                                                                                                                                                                 | TBD    |
| 0.2     | TBD  | Updated with answers for Open Questions: shop size, Lazada, real-time sync, subscription plans, shipping, tenant model, inventory model, profit reports, tech stack, Vietnamese language                                        | TBD    |
| 0.3     | TBD  | Updated Q-004, Q-006, Q-007, Q-009, Q-012, Q-016, Q-017, Q-018, Q-019; added subscription payment, order status direction, one physical warehouse per shop, shipping core-first, customizable profit formula, draft plan limits | TBD    |
| 0.4     | TBD  | Confirmed Q-012: end customers cannot log in to ShopHub; ShopHub is internal dashboard for shops and platform operators only                                                                                                    | TBD    |
