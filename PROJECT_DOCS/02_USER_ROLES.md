# 02_USER_ROLES.md

## Metadata

| Field        | Value                                                                                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Document ID  | DOC-02-USER-ROLES                                                                                                                                      |
| Project Name | Multi-channel E-commerce Hub / ShopHub                                                                                                                 |
| Version      | 0.2                                                                                                                                                    |
| Status       | Draft - Updated with Open Questions answers                                                                                                            |
| Owner        | TBD                                                                                                                                                    |
| Last Updated | TBD                                                                                                                                                    |
| Depends On   | 01_PROJECT_BRIEF.md                                                                                                                                    |
| Used By      | 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md, 06_SCREEN_REQUIREMENTS/, 09_PERMISSION_MATRIX.md, 11_TECHNICAL_ARCHITECTURE.md |

---

# 1. Purpose

Tài liệu này dùng để xác định **các nhóm người dùng và vai trò chính** trong hệ thống ShopHub.

Mục tiêu của tài liệu:

* Làm rõ ai là người sử dụng hệ thống.
* Tách rõ vai trò bên **shop/nhà bán hàng** và vai trò bên **hệ thống ShopHub**.
* Xác định nhu cầu, trách nhiệm và phạm vi truy cập tổng quát của từng role.
* Làm nền tảng để tạo `03_FEATURE_LIST.md`, `09_PERMISSION_MATRIX.md`, `05_SITEMAP_AND_SCREEN_LIST.md` và `06_SCREEN_REQUIREMENTS/`.

Tài liệu này **không chốt permission chi tiết từng action**. Permission chi tiết sẽ được định nghĩa trong `09_PERMISSION_MATRIX.md`.

---

# 2. Scope

## 2.1 In Scope

Tài liệu này bao gồm:

* Nhóm người dùng chính của hệ thống.
* Role bên shop.
* Role bên hệ thống ShopHub.
* Mức quyền tổng quát của từng role.
* Nhu cầu chính của từng role.
* Những hành động chính mà từng role thường thực hiện.
* Ranh giới dữ liệu giữa các shop/tenant.

## 2.2 Out of Scope

Tài liệu này không bao gồm:

* Permission chi tiết theo từng nút/action.
* API authorization rule.
* Chi tiết giao diện từng màn hình.
* Data model chi tiết.
* Chi tiết gói dịch vụ Pro/Premium.
* Workflow nghiệp vụ chi tiết.

Các phần này sẽ nằm ở tài liệu sau:

| Nội dung                 | Tài liệu xử lý                                             |
| ------------------------ | ---------------------------------------------------------- |
| Permission chi tiết      | `09_PERMISSION_MATRIX.md`                                  |
| Feature theo từng module | `03_FEATURE_LIST.md`                                       |
| Luồng nghiệp vụ          | `04_BUSINESS_FLOW.md`                                      |
| Màn hình                 | `05_SITEMAP_AND_SCREEN_LIST.md`, `06_SCREEN_REQUIREMENTS/` |
| Data model               | `07_DATA_MODEL.md`                                         |
| API authorization        | `08_API_SPECIFICATION.md`                                  |

---

# 3. User Group Overview

Hệ thống ShopHub có 2 nhóm người dùng chính:

| User Group ID | Nhóm người dùng     | Mô tả                                                                                                                                             | Dữ liệu được truy cập                             |
| ------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| UG-SELLER     | Seller-side Users   | Người dùng thuộc từng shop/nhà bán hàng. Họ dùng ShopHub để quản lý bán hàng, đơn hàng, sản phẩm, tồn kho, khách hàng và báo cáo của shop mình.   | Chỉ dữ liệu thuộc shop/tenant của họ.             |
| UG-PLATFORM   | Platform-side Users | Người dùng thuộc đội ngũ vận hành hệ thống ShopHub. Họ quản lý nền tảng, shop, gói dịch vụ, thanh toán subscription, hỗ trợ và giám sát hệ thống. | Dữ liệu cấp platform theo phạm vi quyền được cấp. |

## 3.1 Important Rule

| Rule ID       | Rule                                                                                                                |
| ------------- | ------------------------------------------------------------------------------------------------------------------- |
| RULE-ROLE-001 | Role bên shop và role bên ShopHub phải được tách riêng.                                                             |
| RULE-ROLE-002 | Người dùng bên shop không được truy cập dữ liệu của shop khác.                                                      |
| RULE-ROLE-003 | Người dùng bên ShopHub chỉ được truy cập dữ liệu shop theo đúng quyền platform được cấp.                            |
| RULE-ROLE-004 | Khách mua hàng cuối không được đăng nhập vào ShopHub.                                                               |
| RULE-ROLE-005 | Mỗi user phải thuộc ít nhất một scope: seller-side hoặc platform-side.                                              |
| RULE-ROLE-006 | Không được dùng chung một role cho cả seller-side và platform-side nếu ý nghĩa quyền khác nhau.                     |
| RULE-ROLE-017 | Một tài khoản có thể có nhiều role/quyền trong cùng phạm vi được phép.                                              |
| RULE-ROLE-018 | Tài khoản bên shop, ngoại trừ Shop Owner khởi tạo ban đầu, chỉ được tạo thông qua lời mời của Shop Owner.           |
| RULE-ROLE-019 | Tài khoản bên ShopHub platform cũng phải được tạo thông qua lời mời hoặc cấp quyền từ platform admin có thẩm quyền. |

---

# 4. Tenant / Shop Access Model

ShopHub là hệ thống hỗ trợ nhiều shop/tenant.

| Concept           | Mô tả                                                                    |
| ----------------- | ------------------------------------------------------------------------ |
| Tenant / Shop     | Một không gian dữ liệu riêng của từng shop.                              |
| Shop User         | Người dùng thuộc về một shop cụ thể.                                     |
| Platform User     | Người dùng thuộc hệ thống ShopHub, không thuộc một shop bán hàng cụ thể. |
| Shop Boundary     | Ranh giới dữ liệu giữa các shop.                                         |
| Platform Boundary | Ranh giới quyền quản trị cấp hệ thống ShopHub.                           |

## 4.1 Tenant Rules

| Rule ID         | Rule                                                                                                                       |
| --------------- | -------------------------------------------------------------------------------------------------------------------------- |
| RULE-TENANT-001 | Mỗi shop có dữ liệu riêng: sản phẩm, đơn hàng, tồn kho, khách hàng, báo cáo, cấu hình.                                     |
| RULE-TENANT-002 | User bên shop chỉ được thao tác trong shop mà user đó thuộc về.                                                            |
| RULE-TENANT-003 | Giai đoạn đầu áp dụng mô hình 1 user thuộc 1 shop. Hỗ trợ 1 user thuộc nhiều shop là nhu cầu có thể có nhưng để phase sau. |
| RULE-TENANT-004 | Platform user có thể xem/quản lý nhiều shop nếu role cho phép.                                                             |
| RULE-TENANT-005 | API, query database và permission phải luôn kiểm tra tenant/shop boundary.                                                 |

---

# 5. Seller-side Roles

Đây là các role thuộc từng shop/nhà bán hàng.

## 5.1 Seller-side Role Summary

| Role ID               | Role Name        | Mô tả ngắn                                     | Access Level Summary                                                   |
| --------------------- | ---------------- | ---------------------------------------------- | ---------------------------------------------------------------------- |
| ROLE-SHOP-OWNER       | Shop Owner       | Chủ shop hoặc người quản lý cao nhất của shop. | Toàn quyền trong phạm vi shop của mình.                                |
| ROLE-SHOP-MANAGER     | Shop Manager     | Người quản lý vận hành thay mặt chủ shop.      | Quyền quản lý hầu hết nghiệp vụ, trừ cấu hình nhạy cảm.                |
| ROLE-ORDER-STAFF      | Order Staff      | Nhân viên xử lý đơn hàng.                      | Quản lý đơn hàng trong phạm vi được giao.                              |
| ROLE-WAREHOUSE-STAFF  | Warehouse Staff  | Nhân viên kho.                                 | Quản lý tồn kho, nhập/xuất kho, kiểm tra hàng.                         |
| ROLE-PRODUCT-STAFF    | Product Staff    | Nhân viên quản lý sản phẩm.                    | Tạo/sửa sản phẩm, danh mục, thông tin bán hàng.                        |
| ROLE-ACCOUNTANT       | Accountant       | Người theo dõi doanh thu, chi phí, lợi nhuận.  | Xem báo cáo, dữ liệu tài chính, đối soát.                              |
| ROLE-SHOP-VIEWER      | Shop Viewer      | Người chỉ xem dữ liệu.                         | Chỉ xem, không chỉnh sửa.                                              |
| ROLE-CUSTOMER-SUPPORT | Customer Support | Nhân viên chăm sóc khách hàng của shop.        | Xem đơn/khách hàng và ghi nhận chăm sóc khách hàng trong phạm vi shop. |

---

## 5.2 ROLE-SHOP-OWNER - Shop Owner

### Description

Shop Owner là người sở hữu hoặc chịu trách nhiệm cao nhất của một shop trên ShopHub.

Với nhóm shop nhỏ/vừa, Shop Owner thường là người quản lý gần như toàn bộ hoạt động bán hàng, bao gồm sản phẩm, đơn hàng, tồn kho, nhân sự, báo cáo và cấu hình shop.

### Main Needs

| Need ID     | Nhu cầu                                            |
| ----------- | -------------------------------------------------- |
| NEED-SO-001 | Xem toàn cảnh tình hình kinh doanh của shop.       |
| NEED-SO-002 | Quản lý sản phẩm, đơn hàng, tồn kho và khách hàng. |
| NEED-SO-003 | Theo dõi doanh thu, chi phí và lợi nhuận.          |
| NEED-SO-004 | Quản lý nhân sự trong shop.                        |
| NEED-SO-005 | Quản lý kết nối Lazada và các kênh bán sau này.    |
| NEED-SO-006 | Quản lý gói dịch vụ ShopHub của shop.              |

### Main Actions

| Action Group    | Hành động tổng quát                                                    |
| --------------- | ---------------------------------------------------------------------- |
| Dashboard       | Xem dashboard tổng quan.                                               |
| Product         | Tạo, sửa, xem, quản lý trạng thái sản phẩm.                            |
| Category        | Quản lý danh mục sản phẩm.                                             |
| Order           | Xem, xử lý, cập nhật trạng thái, hủy đơn nếu hợp lệ.                   |
| Inventory       | Xem và điều chỉnh tồn kho theo rule.                                   |
| Customer        | Xem thông tin khách hàng và lịch sử mua hàng.                          |
| Report          | Xem báo cáo doanh thu, lợi nhuận, đơn hàng, tồn kho.                   |
| User Management | Mời, khóa, phân quyền user trong shop.                                 |
| Channel         | Quản lý kết nối Lazada và các kênh bán.                                |
| Subscription    | Xem gói dịch vụ, thanh toán subscription, nâng/hạ gói nếu được hỗ trợ. |

### Access Boundary

| Boundary      | Rule                                                       |
| ------------- | ---------------------------------------------------------- |
| Shop Data     | Chỉ truy cập dữ liệu thuộc shop của mình.                  |
| Platform Data | Không được truy cập dữ liệu quản trị cấp ShopHub platform. |
| Other Shops   | Không được xem hoặc thao tác dữ liệu shop khác.            |

### Not Allowed

* Không được truy cập dữ liệu shop khác.
* Không được quản lý platform user của ShopHub.
* Không được thay đổi cấu hình hệ thống toàn cục của ShopHub.
* Không được can thiệp subscription/payment của shop khác.

---

## 5.3 ROLE-SHOP-MANAGER - Shop Manager

### Description

Shop Manager là người được Shop Owner giao quyền quản lý vận hành shop hằng ngày.

Role này phù hợp với shop có nhân sự vận hành riêng, cần người quản lý đơn hàng, sản phẩm, tồn kho và báo cáo nhưng không nhất thiết được can thiệp vào các thiết lập nhạy cảm như thanh toán subscription hoặc owner account.

### Main Needs

| Need ID     | Nhu cầu                                         |
| ----------- | ----------------------------------------------- |
| NEED-SM-001 | Theo dõi hoạt động vận hành hằng ngày.          |
| NEED-SM-002 | Quản lý đơn hàng và sản phẩm.                   |
| NEED-SM-003 | Kiểm tra tồn kho và xử lý vấn đề vận hành.      |
| NEED-SM-004 | Xem báo cáo để điều phối công việc.             |
| NEED-SM-005 | Quản lý nhân viên cấp dưới nếu được phân quyền. |

### Main Actions

| Action Group       | Hành động tổng quát                                                                      |
| ------------------ | ---------------------------------------------------------------------------------------- |
| Dashboard          | Xem dashboard vận hành.                                                                  |
| Product            | Quản lý sản phẩm và danh mục.                                                            |
| Order              | Xử lý và cập nhật đơn hàng.                                                              |
| Inventory          | Theo dõi tồn kho, yêu cầu điều chỉnh tồn kho.                                            |
| Customer           | Xem thông tin khách hàng phục vụ xử lý đơn.                                              |
| Report             | Xem báo cáo vận hành cơ bản.                                                             |
| Staff Coordination | Theo dõi hoạt động của nhân viên nếu được cấp quyền, nhưng không được quản lý user/role. |

### Access Boundary

| Boundary           | Rule                                                                   |
| ------------------ | ---------------------------------------------------------------------- |
| Shop Data          | Chỉ truy cập dữ liệu thuộc shop của mình.                              |
| Sensitive Settings | Không mặc định có quyền thay đổi subscription, billing, owner account. |
| Platform Data      | Không có quyền platform.                                               |

### Not Allowed

* Không được quản lý user/role.
* Không được thay đổi gói dịch vụ nếu chưa được cấp quyền.
* Không được truy cập billing/payment nếu chưa được cấp quyền.
* Không được xóa shop.
* Không được truy cập dữ liệu shop khác.

---

## 5.4 ROLE-ORDER-STAFF - Order Staff

### Description

Order Staff là nhân viên phụ trách xử lý đơn hàng hằng ngày.

Role này tập trung vào việc xem đơn mới, kiểm tra thông tin đơn, cập nhật trạng thái xử lý và phối hợp với kho/vận chuyển.

### Main Needs

| Need ID     | Nhu cầu                                           |
| ----------- | ------------------------------------------------- |
| NEED-OS-001 | Xem danh sách đơn hàng cần xử lý.                 |
| NEED-OS-002 | Lọc đơn theo trạng thái, kênh bán, thời gian.     |
| NEED-OS-003 | Xem chi tiết đơn hàng và thông tin khách.         |
| NEED-OS-004 | Cập nhật trạng thái xử lý đơn theo rule.          |
| NEED-OS-005 | Nhận biết lỗi đồng bộ hoặc trạng thái bất thường. |

### Main Actions

| Action Group          | Hành động tổng quát                                                                      |
| --------------------- | ---------------------------------------------------------------------------------------- |
| Order List            | Xem, tìm kiếm, lọc đơn hàng.                                                             |
| Order Detail          | Xem thông tin sản phẩm, khách hàng, thanh toán, vận chuyển.                              |
| Order Processing      | Xác nhận, cập nhật trạng thái, ghi chú xử lý.                                            |
| Cancellation          | Được hủy đơn nếu trạng thái đơn, rule nghiệp vụ và permission cho phép.                  |
| Customer Care         | Chăm sóc khách hàng liên quan đến đơn hàng, ghi chú trao đổi, theo dõi vấn đề của khách. |
| Shipping Coordination | Theo dõi thông tin giao hàng ở mức cơ bản.                                               |

### Access Boundary

| Boundary  | Rule                                                                           |
| --------- | ------------------------------------------------------------------------------ |
| Product   | Có thể xem sản phẩm liên quan đến đơn, nhưng không mặc định được sửa sản phẩm. |
| Inventory | Có thể xem tồn kho liên quan, nhưng không mặc định được điều chỉnh tồn kho.    |
| Report    | Không mặc định được xem báo cáo tài chính/lợi nhuận.                           |
| Settings  | Không được truy cập cấu hình shop nhạy cảm.                                    |

### Not Allowed

* Không được thay đổi subscription/payment.
* Không được quản lý user/role.
* Không được chỉnh công thức lợi nhuận.
* Không được sửa dữ liệu tồn kho nếu không có quyền kho.
* Không được truy cập dữ liệu shop khác.

---

## 5.5 ROLE-WAREHOUSE-STAFF - Warehouse Staff

### Description

Warehouse Staff là nhân viên phụ trách kho của shop.

Giai đoạn đầu, mỗi shop có **1 kho vật lý chính**. Role này thao tác với Physical Stock, Available Stock và Reserved Stock theo rule tồn kho đã chốt. Warehouse Staff có thể đề xuất/ghi nhận điều chỉnh tồn kho, nhưng mọi thay đổi tồn kho cần có lý do, bằng chứng và phải được Shop Owner hoặc Shop Manager duyệt trước khi có hiệu lực.

### Main Needs

| Need ID     | Nhu cầu                                           |
| ----------- | ------------------------------------------------- |
| NEED-WS-001 | Xem số lượng tồn kho thực tế.                     |
| NEED-WS-002 | Xem sản phẩm sắp hết hàng.                        |
| NEED-WS-003 | Cập nhật nhập kho/xuất kho theo nghiệp vụ.        |
| NEED-WS-004 | Kiểm tra hàng đã giữ cho đơn nhưng chưa xuất kho. |
| NEED-WS-005 | Theo dõi lịch sử biến động tồn kho.               |

### Main Actions

| Action Group             | Hành động tổng quát                                                                                                   |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| Inventory                | Xem Physical Stock, Available Stock, Reserved Stock.                                                                  |
| Stock In                 | Ghi nhận nhập kho.                                                                                                    |
| Stock Out                | Ghi nhận xuất kho/giao hàng theo rule.                                                                                |
| Stock Adjustment Request | Tạo yêu cầu điều chỉnh tồn kho, bắt buộc có lý do và bằng chứng. Yêu cầu cần được Shop Owner hoặc Shop Manager duyệt. |
| Low Stock                | Xem cảnh báo sắp hết hàng.                                                                                            |
| Inventory Log            | Xem lịch sử nhập/xuất/điều chỉnh kho.                                                                                 |

### Access Boundary

| Boundary         | Rule                                                                       |
| ---------------- | -------------------------------------------------------------------------- |
| Order            | Có thể xem thông tin đơn liên quan đến xuất kho.                           |
| Product          | Có thể xem thông tin sản phẩm, không mặc định được sửa thông tin bán hàng. |
| Financial Report | Không được xem báo cáo tài chính/lợi nhuận mặc định.                       |
| Settings         | Không được truy cập cấu hình shop nhạy cảm.                                |

### Not Allowed

* Không được thay đổi giá bán sản phẩm nếu chưa được cấp quyền.
* Không được quản lý subscription/payment.
* Không được quản lý user/role.
* Không được xem dữ liệu shop khác.

---

## 5.6 ROLE-PRODUCT-STAFF - Product Staff

### Description

Product Staff là nhân viên phụ trách quản lý thông tin sản phẩm, danh mục, hình ảnh, giá bán, trạng thái sản phẩm và dữ liệu bán hàng liên quan.

### Main Needs

| Need ID     | Nhu cầu                                                 |
| ----------- | ------------------------------------------------------- |
| NEED-PS-001 | Tạo và cập nhật sản phẩm.                               |
| NEED-PS-002 | Quản lý SKU, giá, mô tả, hình ảnh, trạng thái sản phẩm. |
| NEED-PS-003 | Quản lý danh mục sản phẩm.                              |
| NEED-PS-004 | Theo dõi trạng thái đồng bộ sản phẩm lên Lazada.        |
| NEED-PS-005 | Kiểm tra lỗi dữ liệu sản phẩm.                          |

### Main Actions

| Action Group   | Hành động tổng quát                                      |
| -------------- | -------------------------------------------------------- |
| Product        | Tạo, sửa, xem sản phẩm.                                  |
| Category       | Quản lý danh mục sản phẩm.                               |
| Product Status | Bật/tắt hoặc thay đổi trạng thái sản phẩm nếu được phép. |
| Channel Sync   | Xem trạng thái đồng bộ sản phẩm với Lazada.              |
| Validation     | Sửa lỗi thiếu thông tin sản phẩm.                        |

### Access Boundary

| Boundary     | Rule                                                                         |
| ------------ | ---------------------------------------------------------------------------- |
| Order        | Có thể xem dữ liệu đơn liên quan sản phẩm nếu cần, không mặc định xử lý đơn. |
| Inventory    | Có thể xem tồn kho, không mặc định điều chỉnh kho.                           |
| Report       | Không mặc định xem báo cáo lợi nhuận.                                        |
| Subscription | Không truy cập subscription/payment.                                         |

### Not Allowed

* Không được xử lý/hủy đơn nếu không có quyền order.
* Không được điều chỉnh tồn kho nếu không có quyền kho.
* Không được quản lý user/role.
* Không được truy cập dữ liệu shop khác.

---

## 5.7 ROLE-ACCOUNTANT - Accountant

### Description

Accountant là người phụ trách theo dõi dữ liệu tài chính, doanh thu, chi phí, lợi nhuận và đối soát ở mức shop.

Role này không phải kế toán đầy đủ thay thế phần mềm kế toán chuyên dụng, nhưng cần đủ dữ liệu để shop đánh giá hiệu quả kinh doanh.

### Main Needs

| Need ID     | Nhu cầu                                                          |
| ----------- | ---------------------------------------------------------------- |
| NEED-AC-001 | Xem doanh thu theo thời gian.                                    |
| NEED-AC-002 | Xem lợi nhuận theo công thức của shop.                           |
| NEED-AC-003 | Kiểm tra phí sàn, phí vận chuyển, giảm giá, giá vốn.             |
| NEED-AC-004 | Xuất hoặc tổng hợp dữ liệu báo cáo nếu được hỗ trợ.              |
| NEED-AC-005 | Phát hiện dữ liệu thiếu khiến báo cáo lợi nhuận không chính xác. |

### Main Actions

| Action Group   | Hành động tổng quát                                      |
| -------------- | -------------------------------------------------------- |
| Revenue Report | Xem báo cáo doanh thu.                                   |
| Profit Report  | Xem báo cáo lợi nhuận.                                   |
| Cost Settings  | Xem hoặc cấu hình thành phần chi phí nếu được cấp quyền. |
| Reconciliation | Kiểm tra dữ liệu phí/đối soát nếu có dữ liệu từ Lazada.  |
| Data Quality   | Xem cảnh báo thiếu giá vốn hoặc thiếu phí.               |

### Access Boundary

| Boundary   | Rule                                               |
| ---------- | -------------------------------------------------- |
| Order      | Có thể xem dữ liệu đơn phục vụ báo cáo.            |
| Product    | Có thể xem giá vốn/giá bán nếu được cấp quyền.     |
| Inventory  | Có thể xem dữ liệu tồn nếu liên quan báo cáo.      |
| Operations | Không mặc định được xử lý đơn hoặc điều chỉnh kho. |

### Not Allowed

* Không được xử lý/hủy đơn nếu không được cấp quyền.
* Không được quản lý user/role mặc định.
* Không được thay đổi subscription/payment nếu không được cấp quyền.
* Không được truy cập dữ liệu shop khác.

---

## 5.8 ROLE-CUSTOMER-SUPPORT - Customer Support

### Description

Customer Support là nhân viên chăm sóc khách hàng của shop.

Role này tập trung vào việc xem thông tin khách hàng, lịch sử mua hàng, đơn hàng liên quan và ghi nhận quá trình hỗ trợ/chăm sóc khách hàng. Role này không phải khách mua hàng cuối và vẫn là tài khoản nội bộ của shop.

### Main Needs

| Need ID     | Nhu cầu                                            |
| ----------- | -------------------------------------------------- |
| NEED-CS-001 | Xem thông tin khách hàng thuộc shop.               |
| NEED-CS-002 | Xem lịch sử đơn hàng của khách.                    |
| NEED-CS-003 | Ghi chú chăm sóc khách hàng.                       |
| NEED-CS-004 | Theo dõi vấn đề/khiếu nại liên quan đến đơn hàng.  |
| NEED-CS-005 | Phối hợp với Order Staff để xử lý vấn đề đơn hàng. |

### Main Actions

| Action Group    | Hành động tổng quát                                      |
| --------------- | -------------------------------------------------------- |
| Customer Lookup | Tìm kiếm và xem thông tin khách hàng.                    |
| Order History   | Xem lịch sử đơn hàng của khách.                          |
| Customer Note   | Ghi chú chăm sóc khách hàng.                             |
| Issue Tracking  | Ghi nhận vấn đề/khiếu nại ở mức cơ bản.                  |
| Coordination    | Chuyển vấn đề cho Order Staff hoặc Shop Manager nếu cần. |

### Access Boundary

| Boundary       | Rule                                                          |
| -------------- | ------------------------------------------------------------- |
| Customer Data  | Chỉ xem dữ liệu khách hàng thuộc shop của mình.               |
| Order Data     | Có thể xem đơn hàng liên quan đến khách hàng để hỗ trợ.       |
| Financial Data | Không mặc định xem lợi nhuận, giá vốn hoặc báo cáo tài chính. |
| Operations     | Không mặc định được hủy đơn, sửa tồn kho, sửa sản phẩm.       |

### Not Allowed

* Không được truy cập dữ liệu shop khác.
* Không được quản lý user/role.
* Không được điều chỉnh tồn kho.
* Không được sửa sản phẩm.
* Không được xem dữ liệu tài chính nhạy cảm nếu chưa được cấp quyền.

---

## 5.9 ROLE-SHOP-VIEWER - Shop Viewer

### Description

Shop Viewer là người chỉ có quyền xem dữ liệu trong shop.

Role này phù hợp với người cần theo dõi tình hình nhưng không được phép thay đổi dữ liệu.

### Main Needs

| Need ID     | Nhu cầu                                          |
| ----------- | ------------------------------------------------ |
| NEED-SV-001 | Xem dashboard.                                   |
| NEED-SV-002 | Xem sản phẩm, đơn hàng, tồn kho ở mức read-only. |
| NEED-SV-003 | Xem báo cáo nếu được cấp quyền.                  |

### Main Actions

| Action Group   | Hành động tổng quát        |
| -------------- | -------------------------- |
| View Dashboard | Xem dashboard.             |
| View Product   | Xem sản phẩm.              |
| View Order     | Xem đơn hàng.              |
| View Inventory | Xem tồn kho.               |
| View Report    | Xem báo cáo được cho phép. |

### Access Boundary

| Boundary       | Rule                                        |
| -------------- | ------------------------------------------- |
| Write Access   | Không có quyền tạo/sửa/xóa.                 |
| Sensitive Data | Chỉ xem dữ liệu nhạy cảm nếu được cấu hình. |
| Shop Data      | Chỉ xem trong shop của mình.                |

### Not Allowed

* Không được tạo/sửa/xóa dữ liệu.
* Không được xử lý đơn.
* Không được điều chỉnh kho.
* Không được quản lý user/role.
* Không được thay đổi cấu hình.

---

# 6. Platform-side Roles

Đây là các role thuộc hệ thống ShopHub, không phải người bán hàng trực tiếp.

## 6.1 Platform-side Role Summary

| Role ID                        | Role Name           | Mô tả ngắn                                      | Access Level Summary                                  |
| ------------------------------ | ------------------- | ----------------------------------------------- | ----------------------------------------------------- |
| ROLE-SHOPHUB-SUPER-ADMIN       | ShopHub Super Admin | Quản trị cao nhất của nền tảng ShopHub.         | Toàn quyền cấp platform.                              |
| ROLE-SHOPHUB-ADMIN             | ShopHub Admin       | Quản trị vận hành hệ thống.                     | Quản lý shop, cấu hình, giám sát hệ thống theo quyền. |
| ROLE-SHOPHUB-SUPPORT           | ShopHub Support     | Nhân viên hỗ trợ shop.                          | Tra cứu và hỗ trợ dữ liệu shop theo phạm vi.          |
| ROLE-SHOPHUB-BILLING-ADMIN     | Billing Admin       | Quản lý gói dịch vụ và thanh toán subscription. | Quản lý plan, subscription, payment, invoice.         |
| ROLE-SHOPHUB-INTEGRATION-ADMIN | Integration Admin   | Quản lý tích hợp và đồng bộ.                    | Theo dõi kết nối Lazada, sync log, lỗi đồng bộ.       |
| ROLE-SHOPHUB-AUDITOR           | Platform Auditor    | Người kiểm tra log và tuân thủ.                 | Xem audit log, không chỉnh sửa dữ liệu nghiệp vụ.     |

---

## 6.2 ROLE-SHOPHUB-SUPER-ADMIN - ShopHub Super Admin

### Description

ShopHub Super Admin là role có quyền cao nhất ở cấp platform.

Role này nên được cấp rất hạn chế, chỉ dành cho người chịu trách nhiệm quản trị toàn hệ thống.

### Main Needs

| Need ID      | Nhu cầu                           |
| ------------ | --------------------------------- |
| NEED-SSA-001 | Quản lý toàn bộ hệ thống ShopHub. |
| NEED-SSA-002 | Quản lý platform user và role.    |
| NEED-SSA-003 | Kiểm tra toàn bộ shop/tenant.     |
| NEED-SSA-004 | Cấu hình hệ thống cấp platform.   |
| NEED-SSA-005 | Xử lý sự cố nghiêm trọng.         |

### Main Actions

| Action Group    | Hành động tổng quát                        |
| --------------- | ------------------------------------------ |
| Platform User   | Tạo, sửa, khóa platform user.              |
| Shop Management | Xem/quản lý tất cả shop.                   |
| Subscription    | Quản lý plan/subscription/payment nếu cần. |
| Integration     | Giám sát tích hợp Lazada và sync.          |
| Audit           | Xem audit log cấp platform.                |
| System Settings | Cấu hình hệ thống toàn cục.                |

### Access Boundary

| Boundary  | Rule                                                        |
| --------- | ----------------------------------------------------------- |
| Platform  | Toàn quyền platform.                                        |
| Shop Data | Có thể truy cập dữ liệu shop theo mục đích quản trị/hỗ trợ. |
| Security  | Mọi thao tác nhạy cảm bắt buộc ghi audit log.               |

### Not Allowed / Caution

* Không nên dùng role này cho thao tác hằng ngày.
* Không nên cấp cho nhiều người.
* Không nên dùng để xử lý nghiệp vụ thay shop nếu không có lý do rõ ràng.

---

## 6.3 ROLE-SHOPHUB-ADMIN - ShopHub Admin

### Description

ShopHub Admin là người quản trị vận hành platform ở mức thông thường.

Role này có thể quản lý shop, trạng thái shop, cấu hình cơ bản và hỗ trợ vận hành hệ thống.

### Main Needs

| Need ID     | Nhu cầu                                 |
| ----------- | --------------------------------------- |
| NEED-SA-001 | Xem danh sách shop đang dùng hệ thống.  |
| NEED-SA-002 | Quản lý trạng thái shop.                |
| NEED-SA-003 | Giám sát tình trạng vận hành.           |
| NEED-SA-004 | Kiểm tra lỗi hệ thống hoặc lỗi đồng bộ. |
| NEED-SA-005 | Hỗ trợ các vấn đề platform.             |

### Main Actions

| Action Group       | Hành động tổng quát                               |
| ------------------ | ------------------------------------------------- |
| Shop Management    | Xem, kích hoạt, tạm khóa shop nếu được cấp quyền. |
| Platform Dashboard | Xem dashboard cấp hệ thống.                       |
| Sync Monitoring    | Xem lỗi đồng bộ tổng quan.                        |
| User Support       | Hỗ trợ user bên shop.                             |
| System Config      | Cấu hình platform cơ bản nếu được cấp quyền.      |

### Access Boundary

| Boundary  | Rule                                                                                  |
| --------- | ------------------------------------------------------------------------------------- |
| Platform  | Có quyền quản trị platform trong phạm vi được cấp.                                    |
| Shop Data | Có thể xem dữ liệu shop phục vụ hỗ trợ, nhưng không mặc định chỉnh dữ liệu nghiệp vụ. |
| Billing   | Không mặc định có toàn quyền billing nếu không phải Billing Admin.                    |

### Not Allowed

* Không được tự ý thay đổi dữ liệu đơn hàng/tồn kho của shop nếu không có workflow hỗ trợ rõ ràng.
* Không được truy cập payment/billing nếu không có quyền billing.
* Không được thay đổi audit log.

---

## 6.4 ROLE-SHOPHUB-SUPPORT - ShopHub Support

### Description

ShopHub Support là nhân viên hỗ trợ shop trong quá trình sử dụng hệ thống.

Role này cần có quyền tra cứu thông tin để hỗ trợ nhưng phải giới hạn quyền chỉnh sửa dữ liệu nhạy cảm.

### Main Needs

| Need ID     | Nhu cầu                                    |
| ----------- | ------------------------------------------ |
| NEED-SS-001 | Tìm kiếm shop cần hỗ trợ.                  |
| NEED-SS-002 | Xem thông tin cấu hình shop.               |
| NEED-SS-003 | Xem trạng thái đồng bộ Lazada.             |
| NEED-SS-004 | Xem lỗi phát sinh để hướng dẫn shop xử lý. |
| NEED-SS-005 | Ghi chú hỗ trợ.                            |

### Main Actions

| Action Group | Hành động tổng quát                         |
| ------------ | ------------------------------------------- |
| Shop Lookup  | Tìm và xem thông tin shop.                  |
| User Lookup  | Xem thông tin user bên shop phục vụ hỗ trợ. |
| Sync Issue   | Xem lỗi đồng bộ và trạng thái sync.         |
| Support Note | Ghi chú hỗ trợ.                             |
| Guide User   | Hướng dẫn shop xử lý vấn đề.                |

### Access Boundary

| Boundary             | Rule                                                    |
| -------------------- | ------------------------------------------------------- |
| Read-only by Default | Mặc định chủ yếu là quyền xem.                          |
| Sensitive Data       | Dữ liệu tài chính/payment cần hạn chế hoặc ẩn một phần. |
| Shop Data            | Chỉ truy cập khi có lý do hỗ trợ.                       |

### Not Allowed

* Không được thay đổi đơn hàng/tồn kho/sản phẩm của shop nếu không có quyền đặc biệt.
* Không được quản lý subscription/payment.
* Không được xem dữ liệu nhạy cảm vượt quá nhu cầu hỗ trợ.
* Không được xóa/sửa audit log.

---

## 6.5 ROLE-SHOPHUB-BILLING-ADMIN - Billing Admin

### Description

Billing Admin là người quản lý gói dịch vụ, subscription và thanh toán của các shop trên ShopHub.

Payment ở đây là payment cho **gói dịch vụ ShopHub**, không phải payment của khách mua hàng trên Lazada.

### Main Needs

| Need ID     | Nhu cầu                                 |
| ----------- | --------------------------------------- |
| NEED-BA-001 | Quản lý danh sách gói dịch vụ.          |
| NEED-BA-002 | Xem subscription của từng shop.         |
| NEED-BA-003 | Theo dõi trạng thái thanh toán.         |
| NEED-BA-004 | Xử lý nâng/hạ gói, tạm khóa do quá hạn. |
| NEED-BA-005 | Xem lịch sử invoice/payment.            |

### Main Actions

| Action Group    | Hành động tổng quát                                         |
| --------------- | ----------------------------------------------------------- |
| Plan Management | Tạo/sửa/cấu hình gói Free/Trial, Pro, Premium, Enterprise.  |
| Subscription    | Quản lý subscription của shop.                              |
| Payment         | Theo dõi thanh toán gói dịch vụ.                            |
| Invoice         | Xem/tạo/quản lý hóa đơn nếu hệ thống hỗ trợ.                |
| Billing Status  | Cập nhật trạng thái Active, Past Due, Suspended, Cancelled. |

### Access Boundary

| Boundary          | Rule                                                        |
| ----------------- | ----------------------------------------------------------- |
| Billing Data      | Có quyền với dữ liệu plan/subscription/payment.             |
| Shop Operations   | Không mặc định được chỉnh đơn hàng/sản phẩm/tồn kho.        |
| Platform Settings | Không toàn quyền platform nếu không phải Admin/Super Admin. |

### Not Allowed

* Không được xử lý đơn hàng của shop.
* Không được điều chỉnh tồn kho.
* Không được sửa dữ liệu sản phẩm của shop.
* Không được can thiệp audit log.

---

## 6.6 ROLE-SHOPHUB-INTEGRATION-ADMIN - Integration Admin

### Description

Integration Admin là người phụ trách theo dõi và quản lý các tích hợp bên ngoài, ưu tiên Lazada ở giai đoạn đầu.

Role này cần hiểu trạng thái kết nối, sync job, sync log, lỗi đồng bộ và retry.

### Main Needs

| Need ID     | Nhu cầu                                                 |
| ----------- | ------------------------------------------------------- |
| NEED-IA-001 | Theo dõi trạng thái kết nối Lazada.                     |
| NEED-IA-002 | Xem sync log và lỗi đồng bộ.                            |
| NEED-IA-003 | Kích hoạt retry hoặc manual resync nếu được phép.       |
| NEED-IA-004 | Kiểm tra dữ liệu nào đang đồng bộ lỗi.                  |
| NEED-IA-005 | Chuẩn bị nền tảng để tích hợp thêm kênh/vận chuyển sau. |

### Main Actions

| Action Group        | Hành động tổng quát                               |
| ------------------- | ------------------------------------------------- |
| Integration Config  | Xem/cấu hình kết nối tích hợp nếu được cấp quyền. |
| Sync Monitoring     | Theo dõi sync job, sync status, sync error.       |
| Retry               | Thực hiện retry/manual resync nếu được phép.      |
| Provider Management | Quản lý provider tích hợp ở mức platform.         |
| Incident Support    | Hỗ trợ xử lý sự cố tích hợp.                      |

### Access Boundary

| Boundary         | Rule                                                                              |
| ---------------- | --------------------------------------------------------------------------------- |
| Integration Data | Có quyền xem dữ liệu tích hợp và sync log.                                        |
| Business Data    | Chỉ xem dữ liệu nghiệp vụ liên quan đến lỗi sync, không mặc định chỉnh trực tiếp. |
| Billing          | Không có quyền billing mặc định.                                                  |

### Not Allowed

* Không được tự ý sửa dữ liệu đơn/sản phẩm/tồn kho của shop nếu không có workflow rõ ràng.
* Không được quản lý subscription/payment.
* Không được truy cập dữ liệu không liên quan đến tích hợp nếu không cần thiết.

---

## 6.7 ROLE-SHOPHUB-AUDITOR - Platform Auditor

### Description

Platform Auditor là người có nhiệm vụ kiểm tra lịch sử thao tác, audit log, sự kiện bảo mật và các thay đổi nhạy cảm trong hệ thống.

Role này thường là read-only.

### Main Needs

| Need ID     | Nhu cầu                                         |
| ----------- | ----------------------------------------------- |
| NEED-PA-001 | Xem audit log cấp platform.                     |
| NEED-PA-002 | Xem lịch sử thao tác nhạy cảm.                  |
| NEED-PA-003 | Kiểm tra thay đổi role/permission/subscription. |
| NEED-PA-004 | Kiểm tra hành vi bất thường.                    |

### Main Actions

| Action Group      | Hành động tổng quát                      |
| ----------------- | ---------------------------------------- |
| Audit Log         | Xem audit log.                           |
| Security Event    | Xem sự kiện bảo mật.                     |
| Change History    | Xem lịch sử thay đổi dữ liệu quan trọng. |
| Compliance Review | Kiểm tra hoạt động tuân thủ.             |

### Access Boundary

| Boundary      | Rule                                                   |
| ------------- | ------------------------------------------------------ |
| Read-only     | Chỉ xem, không chỉnh sửa.                              |
| Audit Data    | Có quyền xem audit log theo phạm vi được cấp.          |
| Business Data | Chỉ xem metadata hoặc dữ liệu liên quan audit nếu cần. |

### Not Allowed

* Không được sửa/xóa audit log.
* Không được thay đổi dữ liệu nghiệp vụ.
* Không được quản lý user/role nếu không có quyền admin.
* Không được thao tác billing/subscription.

---

# 7. Non-login Users

## 7.1 End Customer

| Field                     | Value                                                                                                                                                  |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| User Group ID             | USER-END-CUSTOMER                                                                                                                                      |
| Login Access              | Not Allowed                                                                                                                                            |
| Description               | Khách mua hàng cuối trên Lazada hoặc các kênh bán khác.                                                                                                |
| Relationship with ShopHub | Dữ liệu khách hàng có thể được đồng bộ/lưu trong ShopHub để shop quản lý đơn hàng và chăm sóc khách hàng, nhưng khách mua không đăng nhập vào ShopHub. |

### Rules

| Rule ID           | Rule                                                                                                      |
| ----------------- | --------------------------------------------------------------------------------------------------------- |
| RULE-CUSTOMER-001 | Khách mua hàng cuối không được có tài khoản đăng nhập ShopHub.                                            |
| RULE-CUSTOMER-002 | ShopHub không phải customer portal ở giai đoạn đầu.                                                       |
| RULE-CUSTOMER-003 | Dữ liệu khách hàng chỉ được dùng trong phạm vi quản lý đơn hàng, báo cáo và chăm sóc khách hàng của shop. |
| RULE-CUSTOMER-004 | Nhân viên shop chỉ được xem dữ liệu khách hàng thuộc shop của mình.                                       |

---

# 8. Role Hierarchy

## 8.1 Seller-side Hierarchy

| Level | Role                                                                                                   | Ghi chú                                                             |
| ----: | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------- |
|     1 | ROLE-SHOP-OWNER                                                                                        | Quyền cao nhất trong phạm vi shop.                                  |
|     2 | ROLE-SHOP-MANAGER                                                                                      | Quản lý vận hành, có thể được cấp nhiều quyền nhưng thấp hơn Owner. |
|     3 | ROLE-ORDER-STAFF / ROLE-WAREHOUSE-STAFF / ROLE-PRODUCT-STAFF / ROLE-ACCOUNTANT / ROLE-CUSTOMER-SUPPORT | Role chuyên trách theo nghiệp vụ.                                   |
|     4 | ROLE-SHOP-VIEWER                                                                                       | Chỉ xem.                                                            |

## 8.2 Platform-side Hierarchy

| Level | Role                                                        | Ghi chú                                  |
| ----: | ----------------------------------------------------------- | ---------------------------------------- |
|     1 | ROLE-SHOPHUB-SUPER-ADMIN                                    | Quyền cao nhất cấp platform.             |
|     2 | ROLE-SHOPHUB-ADMIN                                          | Quản trị platform thông thường.          |
|     3 | ROLE-SHOPHUB-BILLING-ADMIN / ROLE-SHOPHUB-INTEGRATION-ADMIN | Role chuyên trách cấp platform.          |
|     4 | ROLE-SHOPHUB-SUPPORT / ROLE-SHOPHUB-AUDITOR                 | Hỗ trợ hoặc kiểm tra, quyền hạn chế hơn. |

---

# 9. Access Level Summary

Bảng này chỉ là tổng quan. Permission chi tiết sẽ nằm trong `09_PERMISSION_MATRIX.md`.

| Area           | Shop Owner | Shop Manager | Order Staff         | Warehouse Staff | Product Staff | Accountant | Shop Viewer | ShopHub Admin | Support      | Billing Admin | Integration Admin | Auditor    |
| -------------- | ---------- | ------------ | ------------------- | --------------- | ------------- | ---------- | ----------- | ------------- | ------------ | ------------- | ----------------- | ---------- |
| Shop Dashboard | Full       | View/Manage  | Limited View        | Limited View    | Limited View  | View       | View        | Support View  | Support View | No            | No                | Audit View |
| Products       | Full       | Manage       | View                | View            | Manage        | View       | View        | Support View  | Support View | No            | Sync View         | Audit View |
| Orders         | Full       | Manage       | Manage              | Related View    | View          | View       | View        | Support View  | Support View | No            | Sync View         | Audit View |
| Inventory      | Full       | Manage       | View                | Manage          | View          | View       | View        | Support View  | Support View | No            | Sync View         | Audit View |
| Customers      | Full       | Manage       | View/Update limited | No/Limited      | No/Limited    | View       | View        | Support View  | Support View | No            | Sync View         | Audit View |

Note: Customer Support là role seller-side chuyên trách chăm sóc khách hàng. Trong Permission Matrix, role này cần được thêm vào bảng chi tiết với quyền tập trung vào Customer, Order History và Support Notes.
| Reports | Full | View | No/Limited | No/Limited | No/Limited | View/Manage | View limited | Platform View | No/Limited | Billing View | Sync View | Audit View |
| User Management | Full | Limited | No | No | No | No | No | Platform Manage | No/Limited | No | No | Audit View |
| Subscription/Payment | Full/Limited | No/Limited | No | No | No | View limited | No | Platform Manage | No/Limited | Manage | No | Audit View |
| Lazada Sync | Full | Manage/View | View errors | View related | View/Manage product sync | View report data | View limited | Platform Manage | Support View | No | Manage | Audit View |
| Platform Settings | No | No | No | No | No | No | No | Manage | No/Limited | Billing only | Integration only | View only |
| Audit Log | Shop-level View | Shop-level View limited | No | No | No | View financial logs | No | Platform View | Support logs | Billing logs | Sync logs | Full View |

Legend:

| Value        | Meaning                             |
| ------------ | ----------------------------------- |
| Full         | Có quyền đầy đủ trong phạm vi role. |
| Manage       | Có thể xem và thao tác quản lý.     |
| View         | Chỉ xem.                            |
| Limited View | Chỉ xem một phần.                   |
| Support View | Xem phục vụ hỗ trợ.                 |
| Sync View    | Xem dữ liệu liên quan đồng bộ.      |
| Audit View   | Xem phục vụ kiểm tra/audit.         |
| No           | Không có quyền mặc định.            |

---

# 10. Role-to-Module Mapping

| Module ID        | Module Name                    | Primary Roles                                                       | Notes                                                        |
| ---------------- | ------------------------------ | ------------------------------------------------------------------- | ------------------------------------------------------------ |
| MOD-AUTH         | Authentication & Authorization | All login roles                                                     | Khách mua hàng không có login access.                        |
| MOD-DASHBOARD    | Dashboard                      | Shop Owner, Shop Manager, Accountant, ShopHub Admin                 | Dashboard bên shop và dashboard bên platform cần tách riêng. |
| MOD-PRODUCT      | Product Management             | Shop Owner, Shop Manager, Product Staff                             | Order Staff/Warehouse Staff có thể xem sản phẩm liên quan.   |
| MOD-CATEGORY     | Category Management            | Shop Owner, Shop Manager, Product Staff                             | Quyền chi tiết chốt ở Permission Matrix.                     |
| MOD-ORDER        | Order Management               | Shop Owner, Shop Manager, Order Staff                               | Warehouse Staff chỉ xem phần liên quan xuất kho.             |
| MOD-INVENTORY    | Inventory Management           | Shop Owner, Shop Manager, Warehouse Staff                           | Mỗi shop có 1 kho vật lý ở giai đoạn đầu.                    |
| MOD-CUSTOMER     | Customer Management            | Shop Owner, Shop Manager, Order Staff, Customer Support, Accountant | Tùy dữ liệu nhạy cảm mà giới hạn quyền xem.                  |
| MOD-CHANNEL      | Sales Channel Management       | Shop Owner, Shop Manager, Integration Admin                         | Ưu tiên Lazada.                                              |
| MOD-SYNC         | Real-time Sync                 | Integration Admin, Shop Owner, Shop Manager, Product Staff          | Cần sync log và retry.                                       |
| MOD-SHIPPING     | Shipping Integration           | Shop Owner, Shop Manager, Order Staff, Warehouse Staff              | Giai đoạn đầu core-first, tích hợp API sau.                  |
| MOD-SUBSCRIPTION | Subscription & Billing         | Shop Owner, Billing Admin, ShopHub Admin                            | Payment cho gói dịch vụ ShopHub.                             |
| MOD-REPORT       | Reports & Analytics            | Shop Owner, Shop Manager, Accountant                                | Có báo cáo lợi nhuận tùy chỉnh.                              |
| MOD-USER         | User & Role Management         | Shop Owner, Shop Manager, ShopHub Admin                             | Tách user bên shop và platform user.                         |
| MOD-AUDIT        | Audit Log                      | Shop Owner, ShopHub Admin, Auditor                                  | Scope audit khác nhau theo role.                             |
| MOD-SETTING      | System Settings                | Shop Owner, ShopHub Admin, Super Admin                              | Cần tách shop settings và platform settings.                 |

---

# 11. Key Role Rules

| Rule ID       | Rule                                                                                                                                                                                                                   |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RULE-ROLE-007 | Shop Owner là role cao nhất trong phạm vi một shop, nhưng không có quyền platform.                                                                                                                                     |
| RULE-ROLE-008 | ShopHub Super Admin là role cao nhất cấp platform, nhưng không nên dùng để xử lý nghiệp vụ hằng ngày thay shop.                                                                                                        |
| RULE-ROLE-009 | ShopHub Support mặc định nên là read-only hoặc limited action.                                                                                                                                                         |
| RULE-ROLE-010 | Billing Admin chỉ quản lý subscription/payment, không mặc định thao tác dữ liệu nghiệp vụ shop.                                                                                                                        |
| RULE-ROLE-011 | Integration Admin quản lý sync/tích hợp, không mặc định sửa trực tiếp dữ liệu nghiệp vụ shop.                                                                                                                          |
| RULE-ROLE-012 | Accountant có thể xem dữ liệu tài chính/lợi nhuận nhưng không mặc định xử lý đơn hoặc sửa tồn kho. Accountant có thể đề xuất chỉnh công thức lợi nhuận, nhưng thay đổi phải được Shop Owner hoặc Shop Manager approve. |
| RULE-ROLE-013 | Warehouse Staff chỉ thao tác tồn kho trong phạm vi rule inventory. Mọi điều chỉnh tồn kho cần có lý do, bằng chứng và cần Shop Owner hoặc Shop Manager duyệt.                                                          |
| RULE-ROLE-014 | Product Staff không mặc định được xử lý đơn hàng.                                                                                                                                                                      |
| RULE-ROLE-015 | Order Staff không mặc định được sửa sản phẩm hoặc điều chỉnh kho, nhưng được hủy đơn nếu trạng thái/rule/permission cho phép.                                                                                          |
| RULE-ROLE-016 | Mọi thao tác nhạy cảm cần audit log.                                                                                                                                                                                   |
| RULE-ROLE-020 | ShopHub Support không được impersonate user của shop.                                                                                                                                                                  |
| RULE-ROLE-021 | Billing Admin không được thay đổi gói thủ công; mọi thay đổi gói phải đi qua payment/subscription flow.                                                                                                                |
| RULE-ROLE-022 | Integration Admin không được retry/manual resync dữ liệu shop một cách tự do; thao tác này cần workflow rõ ràng và/hoặc xác nhận của Shop Owner khi có nguy cơ ảnh hưởng dữ liệu shop.                                 |

---

# 12. Sensitive Actions

Các hành động sau cần kiểm soát kỹ ở `09_PERMISSION_MATRIX.md`:

| Sensitive Action ID | Action                          | Vì sao nhạy cảm                                                                                                       |
| ------------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| SA-001              | Xóa hoặc vô hiệu hóa sản phẩm   | Có thể ảnh hưởng đơn hàng/kênh bán.                                                                                   |
| SA-002              | Hủy đơn hàng                    | Ảnh hưởng doanh thu, tồn kho, trải nghiệm khách hàng.                                                                 |
| SA-003              | Điều chỉnh Physical Stock       | Ảnh hưởng tồn kho thực tế.                                                                                            |
| SA-004              | Điều chỉnh Available Stock      | Ảnh hưởng số lượng bán trên Lazada/kênh bán.                                                                          |
| SA-005              | Thay đổi công thức lợi nhuận    | Ảnh hưởng báo cáo tài chính.                                                                                          |
| SA-006              | Xem báo cáo lợi nhuận           | Dữ liệu nhạy cảm của shop.                                                                                            |
| SA-007              | Quản lý user/role               | Ảnh hưởng bảo mật.                                                                                                    |
| SA-008              | Nâng/hạ gói dịch vụ             | Ảnh hưởng billing và tính năng shop.                                                                                  |
| SA-009              | Thanh toán subscription         | Ảnh hưởng tài chính.                                                                                                  |
| SA-010              | Tạm khóa shop                   | Ảnh hưởng khả năng sử dụng hệ thống.                                                                                  |
| SA-011              | Manual resync dữ liệu Lazada    | Có thể gây ghi đè hoặc lệch dữ liệu. Cần workflow rõ ràng và/hoặc xác nhận của Shop Owner khi ảnh hưởng dữ liệu shop. |
| SA-012              | Thay đổi mapping trạng thái đơn | Ảnh hưởng order flow, report, inventory.                                                                              |

---

# 13. Open Questions

| Question ID | Question                                                                                                | Related Area                                                                                                                                                      | Impact                           | Status                                         |                                 |
| ----------- | ------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | ---------------------------------------------- | ------------------------------- |
| Q-ROLE-001  | Có cần cho một user thuộc nhiều shop không?                                                             | Có thể cần trong tương lai, nhưng giai đoạn đầu chốt 1 user thuộc 1 shop. Một shop có thể kết nối nhiều nền tảng/kênh bán.                                        | Tenant / User Management         | Ảnh hưởng data model và permission             | Answered                        |
| Q-ROLE-002  | Shop Manager có được quản lý user/role không?                                                           | Không. Shop Manager không được quản lý user/role.                                                                                                                 | Seller-side Permission           | Ảnh hưởng Permission Matrix                    | Answered                        |
| Q-ROLE-003  | Accountant có được chỉnh công thức lợi nhuận không, hay chỉ Shop Owner được chỉnh?                      | Accountant có thể đề xuất/chỉnh nháp công thức lợi nhuận, nhưng phải được Shop Owner hoặc Shop Manager approve trước khi áp dụng.                                 | Reports / Profit                 | Ảnh hưởng report settings                      | Answered                        |
| Q-ROLE-004  | Order Staff có được hủy đơn không, hay chỉ được đề xuất hủy?                                            | Order Staff được hủy đơn nếu trạng thái đơn, rule nghiệp vụ và permission cho phép. Ngoài ra cần bổ sung năng lực chăm sóc khách hàng.                            | Order Management / Customer Care | Ảnh hưởng order flow                           | Answered                        |
| Q-ROLE-005  | Warehouse Staff có được điều chỉnh tồn kho trực tiếp không, hay phải tạo yêu cầu chờ duyệt?             | Warehouse Staff được tạo yêu cầu điều chỉnh tồn kho, bắt buộc có lý do và bằng chứng, sau đó gửi Shop Owner hoặc Shop Manager duyệt.                              | Inventory                        | Ảnh hưởng inventory flow                       | Answered                        |
| Q-ROLE-006  | ShopHub Support có được impersonate user của shop không?                                                | Không. ShopHub Support không được impersonate user của shop.                                                                                                      | Support / Security               | Ảnh hưởng bảo mật rất lớn                      | Answered                        |
| Q-ROLE-007  | Billing Admin có được thay đổi gói thủ công không, hay mọi thay đổi phải qua payment/subscription flow? | Không được thay đổi gói thủ công. Mọi thay đổi phải qua payment/subscription flow.                                                                                | Subscription / Payment           | Ảnh hưởng billing logic                        | Answered                        |
| Q-ROLE-008  | Integration Admin có được retry/manual resync trên dữ liệu của shop không, hay cần Shop Owner xác nhận? | Integration Admin không được retry/manual resync một cách tự do. Cần workflow rõ ràng và/hoặc xác nhận của Shop Owner khi thao tác có thể ảnh hưởng dữ liệu shop. | Sync                             | Ảnh hưởng sync safety                          | Answered with Safety Constraint |
| Q-ROLE-009  | Có cần role riêng cho Marketing/Promotion không?                                                        | Có thể cần, nhưng để phase sau.                                                                                                                                   | Promotion / Channel              | Có thể cần sau nếu quản lý khuyến mãi          | Later                           |
| Q-ROLE-010  | Có cần role riêng cho Customer Support bên shop không?                                                  | Có. Cần role Customer Support cho chăm sóc khách hàng.                                                                                                            | Customer / Order                 | Ảnh hưởng Customer Management và Order Support | Answered                        |

---

# 14. Decisions Log

| Decision ID  | Decision                                                                  | Reason                                                                     | Related Docs                                   | Status   |
| ------------ | ------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ---------------------------------------------- | -------- |
| DEC-ROLE-001 | Role được chia thành 2 nhóm: seller-side và platform-side.                | Tránh lẫn quyền của nhà bán hàng với quyền vận hành nền tảng ShopHub.      | 01_PROJECT_BRIEF.md, 09_PERMISSION_MATRIX.md   | Accepted |
| DEC-ROLE-002 | Khách mua hàng cuối không được đăng nhập vào ShopHub.                     | ShopHub là dashboard/hệ thống quản trị nội bộ, không phải customer portal. | 01_PROJECT_BRIEF.md                            | Accepted |
| DEC-ROLE-003 | Shop Owner là role cao nhất trong phạm vi một shop.                       | Shop nhỏ/vừa thường cần một người quản lý toàn bộ vận hành.                | 03_FEATURE_LIST.md, 09_PERMISSION_MATRIX.md    | Accepted |
| DEC-ROLE-004 | ShopHub Super Admin là role cao nhất cấp platform.                        | Cần role quản trị toàn hệ thống nhưng phải hạn chế sử dụng.                | 09_PERMISSION_MATRIX.md                        | Accepted |
| DEC-ROLE-005 | Mỗi shop có dữ liệu riêng và phải tách theo tenant boundary.              | Hệ thống hỗ trợ nhiều shop/tenant.                                         | 07_DATA_MODEL.md, 11_TECHNICAL_ARCHITECTURE.md | Accepted |
| DEC-ROLE-006 | Permission chi tiết không chốt trong tài liệu này.                        | Tránh trùng lặp với Permission Matrix.                                     | 09_PERMISSION_MATRIX.md                        | Accepted |
| DEC-ROLE-007 | Giai đoạn đầu 1 user thuộc 1 shop.                                        | Đơn giản hóa tenant boundary; nhu cầu multi-shop user để phase sau.        | 07_DATA_MODEL.md, 09_PERMISSION_MATRIX.md      | Accepted |
| DEC-ROLE-008 | Shop Manager không được quản lý user/role.                                | Giảm rủi ro phân quyền sai trong shop.                                     | 09_PERMISSION_MATRIX.md                        | Accepted |
| DEC-ROLE-009 | Accountant chỉnh công thức lợi nhuận cần approval.                        | Công thức lợi nhuận ảnh hưởng dữ liệu tài chính nên cần duyệt.             | 04_BUSINESS_FLOW.md, 09_PERMISSION_MATRIX.md   | Accepted |
| DEC-ROLE-010 | Warehouse Staff điều chỉnh tồn kho cần lý do, bằng chứng và approval.     | Điều chỉnh tồn kho là thao tác nhạy cảm.                                   | 04_BUSINESS_FLOW.md, 09_PERMISSION_MATRIX.md   | Accepted |
| DEC-ROLE-011 | ShopHub Support không được impersonate user shop.                         | Giảm rủi ro bảo mật và trách nhiệm thao tác.                               | 11_TECHNICAL_ARCHITECTURE.md                   | Accepted |
| DEC-ROLE-012 | Billing Admin không đổi gói thủ công, phải qua subscription/payment flow. | Đảm bảo billing có lịch sử và logic nhất quán.                             | 04_BUSINESS_FLOW.md, 08_API_SPECIFICATION.md   | Accepted |
| DEC-ROLE-013 | Cần thêm role Customer Support bên shop.                                  | Shop cần chăm sóc khách hàng và hỗ trợ đơn hàng.                           | 03_FEATURE_LIST.md, 09_PERMISSION_MATRIX.md    | Accepted |
| DEC-ROLE-014 | Một tài khoản có thể có nhiều role/quyền trong cùng phạm vi.              | Phù hợp thực tế vận hành shop nhỏ/vừa, một người có thể kiêm nhiều việc.   | 07_DATA_MODEL.md, 09_PERMISSION_MATRIX.md      | Accepted |
| DEC-ROLE-015 | Tài khoản nội bộ được tạo qua lời mời.                                    | Kiểm soát onboarding và quyền truy cập.                                    | 04_BUSINESS_FLOW.md, 08_API_SPECIFICATION.md   | Accepted |

---

# 15. Change Log

| Version | Date | Change                                                                                                                                                                                                                                                                                                                                                                     | Author |
| ------- | ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| 0.1     | TBD  | Initial draft for User Roles based on Project Brief v0.4                                                                                                                                                                                                                                                                                                                   | TBD    |
| 0.2     | TBD  | Updated Open Questions answers: one user one shop for phase 1, no user/role management for Shop Manager, accountant profit formula approval, order cancellation/customer care, inventory adjustment approval, no support impersonation, subscription/payment-only plan changes, safe resync rule, Customer Support role, multi-role accounts, invite-only account creation | TBD    |
