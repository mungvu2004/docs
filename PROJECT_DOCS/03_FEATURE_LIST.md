# 03_FEATURE_LIST.md

## Metadata

| Field        | Value                                                                                                                                                                                                    |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Document ID  | DOC-03-FEATURE-LIST                                                                                                                                                                                      |
| Project Name | Multi-channel E-commerce Hub / ShopHub                                                                                                                                                                   |
| Version      | 0.2                                                                                                                                                                                                      |
| Status       | Draft - Updated with Open Questions answers                                                                                                                                                              |
| Owner        | TBD                                                                                                                                                                                                      |
| Last Updated | TBD                                                                                                                                                                                                      |
| Depends On   | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md                                                                                                                                                                    |
| Used By      | 00_TRACEABILITY_MATRIX.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md, 06_SCREEN_REQUIREMENTS/, 07_DATA_MODEL.md, 08_API_SPECIFICATION.md, 09_PERMISSION_MATRIX.md, 11_TECHNICAL_ARCHITECTURE.md |

---

# 1. Purpose

Tài liệu này dùng để xác định **toàn bộ module và tính năng chính** của hệ thống ShopHub ở mức Product Requirement.

Mục tiêu của tài liệu:

* Chốt hệ thống có những module nào.
* Chốt từng feature thuộc module nào.
* Gán ID cố định cho feature để liên kết với Business Flow, Screen, API, Permission và Data Model.
* Phân loại ưu tiên: Must-have, Should-have, Could-have, Later.
* Tách rõ tính năng dành cho **shop/nhà bán hàng** và tính năng dành cho **hệ thống ShopHub platform**.
* Tránh việc design/dev/AI tự sinh tính năng ngoài scope.

Tài liệu này **không mô tả chi tiết luồng nghiệp vụ từng bước**. Luồng chi tiết sẽ nằm trong `04_BUSINESS_FLOW.md`.

---

# 2. Feature Priority Definition

| Priority    | Ý nghĩa                                            | Cách xử lý                                               |
| ----------- | -------------------------------------------------- | -------------------------------------------------------- |
| Must-have   | Bắt buộc có để hệ thống vận hành được ở phase đầu. | Cần ưu tiên phân tích, thiết kế, code trước.             |
| Should-have | Nên có để sản phẩm đủ dùng thực tế hơn.            | Làm sau Must-have nếu còn thời gian.                     |
| Could-have  | Có thì tốt, nhưng chưa ảnh hưởng core flow.        | Có thể để sau khi core ổn định.                          |
| Later       | Chưa làm ở phase đầu.                              | Ghi nhận để tránh quên, nhưng không đưa vào scope chính. |

---

# 3. Feature Status Definition

| Status           | Ý nghĩa                                                    |
| ---------------- | ---------------------------------------------------------- |
| Draft            | Feature mới được đề xuất, chưa review kỹ.                  |
| Need Detail      | Cần làm rõ thêm ở Business Flow / Data Model / API.        |
| Ready for Flow   | Đủ thông tin để viết Business Flow.                        |
| Ready for Design | Đủ thông tin để viết Screen Requirement.                   |
| Ready for Dev    | Đủ thông tin để chuyển sang API/Data Model/Implementation. |
| Deferred         | Để phase sau.                                              |

---

# 4. Global Feature Rules

| Rule ID       | Rule                                                                                                            |
| ------------- | --------------------------------------------------------------------------------------------------------------- |
| RULE-FEAT-001 | Mỗi feature phải có Feature ID duy nhất.                                                                        |
| RULE-FEAT-002 | Mỗi feature phải thuộc một module rõ ràng.                                                                      |
| RULE-FEAT-003 | Feature không được tự sinh ngoài scope đã chốt trong Project Brief và User Roles.                               |
| RULE-FEAT-004 | Feature liên quan nhiều role phải được kiểm tra lại ở Permission Matrix.                                        |
| RULE-FEAT-005 | Feature có thay đổi dữ liệu quan trọng phải có audit log.                                                       |
| RULE-FEAT-006 | Feature liên quan Lazada sync phải có sync status và error handling ở tài liệu sau.                             |
| RULE-FEAT-007 | Feature liên quan tồn kho phải tuân thủ mô hình Physical Stock / Available Stock / Reserved Stock.              |
| RULE-FEAT-008 | Feature liên quan subscription/payment phải đi qua subscription/payment flow, không được thay đổi gói thủ công. |
| RULE-FEAT-009 | Feature bên shop chỉ thao tác dữ liệu thuộc shop/tenant của user.                                               |
| RULE-FEAT-010 | Feature bên platform không được mặc định can thiệp dữ liệu nghiệp vụ của shop nếu không có workflow rõ ràng.    |

---

# 5. Module Overview

| Module ID        | Module Name                     | Scope                                                 | Primary User Group         | Priority    |
| ---------------- | ------------------------------- | ----------------------------------------------------- | -------------------------- | ----------- |
| MOD-AUTH         | Authentication & Authorization  | Đăng nhập, phiên đăng nhập, phân quyền cơ bản         | Seller-side, Platform-side | Must-have   |
| MOD-TENANT       | Shop / Tenant Management        | Quản lý shop/tenant, shop boundary                    | Platform-side, Shop Owner  | Must-have   |
| MOD-USER         | User & Role Management          | Quản lý user, lời mời, role                           | Seller-side, Platform-side | Must-have   |
| MOD-DASHBOARD    | Dashboard                       | Tổng quan vận hành và kinh doanh                      | Seller-side, Platform-side | Must-have   |
| MOD-PRODUCT      | Product Management              | Sản phẩm, SKU, giá, trạng thái                        | Seller-side                | Must-have   |
| MOD-CATEGORY     | Category Management             | Danh mục sản phẩm                                     | Seller-side                | Must-have   |
| MOD-ORDER        | Order Management                | Đơn hàng, trạng thái, hủy đơn, xử lý đơn              | Seller-side                | Must-have   |
| MOD-CUSTOMER     | Customer Management & Care      | Khách hàng, ghi chú chăm sóc, lịch sử mua             | Seller-side                | Must-have   |
| MOD-INVENTORY    | Inventory Management            | Kho vật lý, tồn khả dụng, tồn giữ chỗ                 | Seller-side                | Must-have   |
| MOD-CHANNEL      | Sales Channel Management        | Kết nối và quản lý kênh bán, ưu tiên Lazada           | Seller-side, Platform-side | Must-have   |
| MOD-SYNC         | Real-time / Near Real-time Sync | Đồng bộ dữ liệu Lazada                                | Seller-side, Platform-side | Must-have   |
| MOD-SHIPPING     | Shipping Core                   | Core vận chuyển, shipment model, trạng thái giao hàng | Seller-side                | Should-have |
| MOD-REPORT       | Reports & Analytics             | Doanh thu, lợi nhuận, đơn hàng, tồn kho               | Seller-side                | Must-have   |
| MOD-SUBSCRIPTION | Subscription & Payment          | Gói dịch vụ, thanh toán subscription                  | Seller-side, Platform-side | Must-have   |
| MOD-AUDIT        | Audit Log                       | Log thao tác nhạy cảm                                 | Seller-side, Platform-side | Must-have   |
| MOD-NOTIFICATION | Notification                    | Toast, system notification, cảnh báo                  | Seller-side, Platform-side | Should-have |
| MOD-SETTING      | Settings                        | Cấu hình shop và platform                             | Seller-side, Platform-side | Should-have |
| MOD-MARKETING    | Marketing / Promotion           | Khuyến mãi, chiến dịch, promotion                     | Seller-side                | Later       |

---

# 6. MOD-AUTH - Authentication & Authorization

## Module Purpose

Cho phép user nội bộ của shop và user platform đăng nhập vào ShopHub, xác thực phiên làm việc và áp dụng quyền truy cập theo role.

## Features

| Feature ID                | Feature Name               | Description                                                  | Primary Roles              | Priority    | Status         |
| ------------------------- | -------------------------- | ------------------------------------------------------------ | -------------------------- | ----------- | -------------- |
| FEAT-AUTH-LOGIN           | Login                      | User đăng nhập vào ShopHub bằng tài khoản nội bộ.            | All login roles            | Must-have   | Ready for Flow |
| FEAT-AUTH-LOGOUT          | Logout                     | User đăng xuất khỏi hệ thống.                                | All login roles            | Must-have   | Ready for Flow |
| FEAT-AUTH-SESSION         | Session Management         | Kiểm tra trạng thái đăng nhập và phiên làm việc.             | All login roles            | Must-have   | Need Detail    |
| FEAT-AUTH-FORGOT-PASSWORD | Forgot Password            | User yêu cầu đặt lại mật khẩu.                               | All login roles            | Should-have | Need Detail    |
| FEAT-AUTH-RESET-PASSWORD  | Reset Password             | User đặt lại mật khẩu qua link/token hợp lệ.                 | All login roles            | Should-have | Need Detail    |
| FEAT-AUTH-RBAC            | Role-based Authorization   | Kiểm soát quyền truy cập dựa trên role/quyền.                | All login roles            | Must-have   | Need Detail    |
| FEAT-AUTH-MULTI-ROLE      | Multiple Roles per Account | Một tài khoản có thể có nhiều role/quyền trong cùng phạm vi. | Shop Owner, Platform Admin | Must-have   | Need Detail    |

## Business Rules

| Rule ID       | Rule                                                                                        |
| ------------- | ------------------------------------------------------------------------------------------- |
| RULE-AUTH-001 | Khách mua hàng cuối không được đăng nhập vào ShopHub.                                       |
| RULE-AUTH-002 | User bên shop chỉ truy cập dữ liệu shop của mình.                                           |
| RULE-AUTH-003 | User platform truy cập theo quyền platform được cấp.                                        |
| RULE-AUTH-004 | Một tài khoản có thể có nhiều role/quyền trong cùng phạm vi được phép.                      |
| RULE-AUTH-005 | Role seller-side và platform-side không được trộn lẫn nếu không có mô hình bảo mật rõ ràng. |

---

# 7. MOD-TENANT - Shop / Tenant Management

## Module Purpose

Quản lý shop/tenant trong hệ thống ShopHub. Mỗi shop là một không gian dữ liệu riêng.

## Features

| Feature ID                | Feature Name               | Description                                            | Primary Roles                      | Priority    | Status         |
| ------------------------- | -------------------------- | ------------------------------------------------------ | ---------------------------------- | ----------- | -------------- |
| FEAT-TENANT-CREATE-SHOP   | Create Shop                | Tạo shop/tenant mới khi onboarding.                    | ShopHub Admin, ShopHub Super Admin | Must-have   | Need Detail    |
| FEAT-TENANT-VIEW-SHOP     | View Shop Profile          | Xem thông tin shop.                                    | Shop Owner, ShopHub Admin, Support | Must-have   | Ready for Flow |
| FEAT-TENANT-UPDATE-SHOP   | Update Shop Profile        | Cập nhật thông tin shop trong phạm vi cho phép.        | Shop Owner, ShopHub Admin          | Should-have | Need Detail    |
| FEAT-TENANT-SHOP-STATUS   | Manage Shop Status         | Quản lý trạng thái shop: Active, Suspended, Cancelled. | ShopHub Admin, Billing Admin       | Must-have   | Need Detail    |
| FEAT-TENANT-BOUNDARY      | Tenant Data Boundary       | Đảm bảo dữ liệu giữa các shop không bị lẫn.            | System                             | Must-have   | Need Detail    |
| FEAT-TENANT-MULTI-CHANNEL | Multiple Channels per Shop | Một shop có thể kết nối nhiều nền tảng/kênh bán.       | Shop Owner, Shop Manager           | Must-have   | Ready for Flow |

## Business Rules

| Rule ID              | Rule                                                  |
| -------------------- | ----------------------------------------------------- |
| RULE-TENANT-FEAT-001 | Giai đoạn đầu, 1 user thuộc 1 shop.                   |
| RULE-TENANT-FEAT-002 | Một shop có thể có nhiều user nội bộ.                 |
| RULE-TENANT-FEAT-003 | Một shop có thể kết nối nhiều kênh/nền tảng bán hàng. |
| RULE-TENANT-FEAT-004 | Mọi dữ liệu nghiệp vụ phải gắn với shop/tenant.       |

---

# 8. MOD-USER - User & Role Management

## Module Purpose

Quản lý user nội bộ, lời mời tham gia shop/platform và role được gán cho từng tài khoản.

## Features

| Feature ID                     | Feature Name                          | Description                                        | Primary Roles                      | Priority  | Status           |
| ------------------------------ | ------------------------------------- | -------------------------------------------------- | ---------------------------------- | --------- | ---------------- |
| FEAT-USER-INVITE-SHOP-USER     | Invite Shop User                      | Shop Owner mời user vào shop.                      | Shop Owner                         | Must-have | Ready for Flow   |
| FEAT-USER-ACCEPT-INVITE        | Accept Invitation                     | Người được mời chấp nhận lời mời và tạo tài khoản. | Invited User                       | Must-have | Need Detail      |
| FEAT-USER-LIST-SHOP-USERS      | List Shop Users                       | Xem danh sách user trong shop.                     | Shop Owner                         | Must-have | Ready for Design |
| FEAT-USER-ASSIGN-ROLE          | Assign Roles                          | Gán một hoặc nhiều role cho user trong shop.       | Shop Owner                         | Must-have | Need Detail      |
| FEAT-USER-REMOVE-ROLE          | Remove Roles                          | Gỡ role khỏi user.                                 | Shop Owner                         | Must-have | Need Detail      |
| FEAT-USER-DEACTIVATE-SHOP-USER | Deactivate Shop User                  | Khóa user khỏi shop.                               | Shop Owner                         | Must-have | Need Detail      |
| FEAT-USER-INVITE-PLATFORM-USER | Invite Platform User                  | Platform admin mời user vào hệ thống ShopHub.      | ShopHub Super Admin, ShopHub Admin | Must-have | Need Detail      |
| FEAT-USER-MANAGE-PLATFORM-USER | Manage Platform Users                 | Quản lý user platform.                             | ShopHub Super Admin, ShopHub Admin | Must-have | Need Detail      |
| FEAT-USER-NO-MANAGER-ROLE-MGMT | Restrict Shop Manager User Management | Shop Manager không được quản lý user/role.         | System                             | Must-have | Ready for Dev    |

## Business Rules

| Rule ID       | Rule                                                                                                |
| ------------- | --------------------------------------------------------------------------------------------------- |
| RULE-USER-001 | Tài khoản bên shop, ngoại trừ Shop Owner khởi tạo ban đầu, chỉ được tạo qua lời mời của Shop Owner. |
| RULE-USER-002 | Shop Manager không được quản lý user/role.                                                          |
| RULE-USER-003 | Platform user được tạo qua lời mời/cấp quyền từ platform admin có thẩm quyền.                       |
| RULE-USER-004 | Một tài khoản có thể có nhiều role/quyền trong cùng phạm vi.                                        |
| RULE-USER-005 | Mọi thay đổi role/user cần audit log.                                                               |

---

# 9. MOD-DASHBOARD - Dashboard

## Module Purpose

Cung cấp cái nhìn tổng quan cho shop và platform.

Dashboard bên shop và dashboard bên ShopHub platform phải tách riêng vì mục đích khác nhau.

## Features

| Feature ID                       | Feature Name                | Description                                                     | Primary Roles                                              | Priority    | Status           |
| -------------------------------- | --------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------- | ----------- | ---------------- |
| FEAT-DASHBOARD-SELLER-OVERVIEW   | Seller Dashboard Overview   | Tổng quan doanh thu, đơn hàng, tồn kho, cảnh báo của shop.      | Shop Owner, Shop Manager                                   | Must-have   | Ready for Design |
| FEAT-DASHBOARD-ORDER-SUMMARY     | Order Summary Cards         | Hiển thị đơn mới, đơn đang xử lý, đơn hủy, đơn hoàn thành.      | Shop Owner, Shop Manager, Order Staff                      | Must-have   | Need Detail      |
| FEAT-DASHBOARD-INVENTORY-ALERT   | Inventory Alerts            | Cảnh báo sản phẩm sắp hết hàng hoặc tồn kho bất thường.         | Shop Owner, Shop Manager, Warehouse Staff                  | Must-have   | Need Detail      |
| FEAT-DASHBOARD-SYNC-ALERT        | Sync Alerts                 | Cảnh báo lỗi đồng bộ Lazada.                                    | Shop Owner, Shop Manager, Product Staff, Integration Admin | Must-have   | Need Detail      |
| FEAT-DASHBOARD-PROFIT-SUMMARY    | Profit Summary              | Tóm tắt doanh thu/lợi nhuận theo công thức của shop.            | Shop Owner, Accountant                                     | Should-have | Need Detail      |
| FEAT-DASHBOARD-PLATFORM-OVERVIEW | Platform Dashboard Overview | Tổng quan số shop, subscription, lỗi sync, trạng thái hệ thống. | ShopHub Admin, Super Admin                                 | Should-have | Need Detail      |

## Business Rules

| Rule ID       | Rule                                                                                             |
| ------------- | ------------------------------------------------------------------------------------------------ |
| RULE-DASH-001 | Dashboard bên shop chỉ hiển thị dữ liệu của shop hiện tại.                                       |
| RULE-DASH-002 | Dashboard platform không được tự động lộ dữ liệu nhạy cảm của shop nếu role không có quyền.      |
| RULE-DASH-003 | Nếu dữ liệu lợi nhuận thiếu giá vốn/phí, dashboard phải hiển thị trạng thái dữ liệu chưa đầy đủ. |

---

# 10. MOD-PRODUCT - Product Management

## Module Purpose

Quản lý sản phẩm của shop, bao gồm thông tin cơ bản, SKU, giá, trạng thái, dữ liệu đồng bộ Lazada và liên kết tồn kho.

## Features

| Feature ID                      | Feature Name               | Description                                                                                            | Primary Roles                                              | Priority  | Status           |
| ------------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- | --------- | ---------------- |
| FEAT-PRODUCT-LIST               | List Products              | Xem danh sách sản phẩm của shop.                                                                       | Shop Owner, Shop Manager, Product Staff, Shop Viewer       | Must-have | Ready for Design |
| FEAT-PRODUCT-DETAIL             | View Product Detail        | Xem chi tiết sản phẩm.                                                                                 | Shop Owner, Shop Manager, Product Staff                    | Must-have | Ready for Design |
| FEAT-PRODUCT-CREATE             | Create Product             | Tạo sản phẩm mới.                                                                                      | Shop Owner, Shop Manager, Product Staff                    | Must-have | Ready for Flow   |
| FEAT-PRODUCT-UPDATE             | Update Product             | Cập nhật thông tin sản phẩm.                                                                           | Shop Owner, Shop Manager, Product Staff                    | Must-have | Ready for Flow   |
| FEAT-PRODUCT-STATUS             | Manage Product Status      | Quản lý trạng thái sản phẩm theo trạng thái từ sàn như Lazada và trạng thái tùy chỉnh nội bộ của shop. | Shop Owner, Shop Manager, Product Staff                    | Must-have | Need Detail      |
| FEAT-PRODUCT-SKU                | Manage SKU                 | Quản lý SKU và mã định danh sản phẩm.                                                                  | Shop Owner, Shop Manager, Product Staff                    | Must-have | Need Detail      |
| FEAT-PRODUCT-PRICE              | Manage Product Price       | Quản lý giá bán sản phẩm.                                                                              | Shop Owner, Shop Manager, Product Staff                    | Must-have | Need Detail      |
| FEAT-PRODUCT-COST               | Manage Product Cost        | Quản lý giá vốn phục vụ báo cáo lợi nhuận.                                                             | Shop Owner, Accountant, Product Staff                      | Must-have | Need Detail      |
| FEAT-PRODUCT-LAZADA-SYNC-STATUS | Product Lazada Sync Status | Xem trạng thái đồng bộ sản phẩm với Lazada.                                                            | Shop Owner, Shop Manager, Product Staff, Integration Admin | Must-have | Need Detail      |
| FEAT-PRODUCT-DELETE-SAFE        | Safe Product Deactivation  | Không xóa cứng sản phẩm nếu đã phát sinh đơn, ưu tiên inactive/deactivate.                             | Shop Owner, Shop Manager                                   | Must-have | Need Detail      |

## Business Rules

| Rule ID          | Rule                                                                                                                                             |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| RULE-PRODUCT-001 | Sản phẩm thuộc một shop cụ thể.                                                                                                                  |
| RULE-PRODUCT-002 | SKU không được trùng trong phạm vi một shop.                                                                                                     |
| RULE-PRODUCT-003 | Sản phẩm đã phát sinh đơn không nên xóa cứng.                                                                                                    |
| RULE-PRODUCT-004 | Giá vốn cần cho báo cáo lợi nhuận, nếu thiếu phải cảnh báo dữ liệu không đầy đủ.                                                                 |
| RULE-PRODUCT-005 | Product status cần hỗ trợ trạng thái thực tế từ sàn, trạng thái chuẩn hóa của ShopHub, trạng thái lỗi đồng bộ và trạng thái tùy chỉnh theo shop. |

---

# 11. MOD-CATEGORY - Category Management

## Module Purpose

Quản lý danh mục sản phẩm trong phạm vi shop.

## Features

| Feature ID                   | Feature Name            | Description                                        | Primary Roles                                | Priority    | Status           |
| ---------------------------- | ----------------------- | -------------------------------------------------- | -------------------------------------------- | ----------- | ---------------- |
| FEAT-CATEGORY-LIST           | List Categories         | Xem danh sách danh mục.                            | Shop Owner, Shop Manager, Product Staff      | Must-have   | Ready for Design |
| FEAT-CATEGORY-CREATE         | Create Category         | Tạo danh mục mới.                                  | Shop Owner, Shop Manager, Product Staff      | Must-have   | Ready for Flow   |
| FEAT-CATEGORY-UPDATE         | Update Category         | Cập nhật danh mục.                                 | Shop Owner, Shop Manager, Product Staff      | Must-have   | Need Detail      |
| FEAT-CATEGORY-DEACTIVATE     | Deactivate Category     | Vô hiệu hóa danh mục.                              | Shop Owner, Shop Manager                     | Should-have | Need Detail      |
| FEAT-CATEGORY-LAZADA-MAPPING | Lazada Category Mapping | Mapping danh mục shop với danh mục Lazada nếu cần. | Shop Owner, Product Staff, Integration Admin | Should-have | Need Detail      |

## Business Rules

| Rule ID           | Rule                                                               |
| ----------------- | ------------------------------------------------------------------ |
| RULE-CATEGORY-001 | Danh mục thuộc một shop cụ thể.                                    |
| RULE-CATEGORY-002 | Danh mục đang có sản phẩm không nên xóa cứng.                      |
| RULE-CATEGORY-003 | Mapping Lazada category phụ thuộc khả năng API và rule của Lazada. |

---

# 12. MOD-ORDER - Order Management

## Module Purpose

Quản lý đơn hàng từ Lazada và các kênh khác trong tương lai. Hệ thống cần lưu trạng thái gốc từ sàn, trạng thái chuẩn hóa của ShopHub và trạng thái nội bộ của shop nếu cần.

## Features

| Feature ID                    | Feature Name                    | Description                                                                                                       | Primary Roles                                                       | Priority    | Status           |
| ----------------------------- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- | ----------- | ---------------- |
| FEAT-ORDER-LIST               | List Orders                     | Xem danh sách đơn hàng.                                                                                           | Shop Owner, Shop Manager, Order Staff, Customer Support             | Must-have   | Ready for Design |
| FEAT-ORDER-DETAIL             | View Order Detail               | Xem chi tiết đơn hàng, sản phẩm, khách, vận chuyển, phí.                                                          | Shop Owner, Shop Manager, Order Staff, Customer Support, Accountant | Must-have   | Ready for Design |
| FEAT-ORDER-FILTER             | Filter Orders                   | Lọc theo trạng thái, kênh, ngày, khách hàng.                                                                      | Shop Owner, Shop Manager, Order Staff                               | Must-have   | Ready for Design |
| FEAT-ORDER-NORMALIZED-STATUS  | ShopHub Normalized Order Status | Chuẩn hóa trạng thái đơn từ Lazada sang trạng thái ShopHub.                                                       | System                                                              | Must-have   | Need Detail      |
| FEAT-ORDER-MARKETPLACE-STATUS | Store Marketplace Order Status  | Lưu trạng thái gốc từ Lazada.                                                                                     | System                                                              | Must-have   | Need Detail      |
| FEAT-ORDER-SHOP-STATUS        | Shop-defined Internal Status    | Cho phép shop có trạng thái nội bộ nếu cần.                                                                       | Shop Owner, Shop Manager                                            | Should-have | Need Detail      |
| FEAT-ORDER-UPDATE-STATUS      | Update Order Status             | Cập nhật trạng thái đơn theo rule.                                                                                | Shop Owner, Shop Manager, Order Staff                               | Must-have   | Need Detail      |
| FEAT-ORDER-CANCEL             | Cancel Order                    | Order Staff được hủy đơn nếu trạng thái/rule/permission cho phép, nhưng phải có bước confirm trước khi thực hiện. | Shop Owner, Shop Manager, Order Staff                               | Must-have   | Need Detail      |
| FEAT-ORDER-NOTES              | Order Notes                     | Ghi chú xử lý đơn hàng.                                                                                           | Shop Owner, Shop Manager, Order Staff, Customer Support             | Should-have | Ready for Flow   |
| FEAT-ORDER-AUDIT              | Order Audit Trail               | Xem lịch sử thay đổi đơn hàng.                                                                                    | Shop Owner, Shop Manager, Auditor                                   | Must-have   | Need Detail      |

## Business Rules

| Rule ID        | Rule                                                                                                                                |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| RULE-ORDER-001 | Đơn hàng thuộc một shop cụ thể.                                                                                                     |
| RULE-ORDER-002 | Đơn từ Lazada phải lưu marketplace status gốc.                                                                                      |
| RULE-ORDER-003 | Hệ thống cần mapping marketplace status sang ShopHub normalized status.                                                             |
| RULE-ORDER-004 | Order Staff được hủy đơn nếu trạng thái, rule và permission cho phép, nhưng bắt buộc phải confirm trước khi hệ thống thực hiện hủy. |
| RULE-ORDER-005 | Hủy đơn phải ảnh hưởng đúng đến Available Stock và Reserved Stock.                                                                  |
| RULE-ORDER-006 | Mọi thay đổi trạng thái đơn cần audit log.                                                                                          |

---

# 13. MOD-CUSTOMER - Customer Management & Care

## Module Purpose

Quản lý dữ liệu khách hàng được đồng bộ từ đơn hàng/kênh bán và hỗ trợ chăm sóc khách hàng nội bộ của shop.

Khách mua hàng cuối **không được đăng nhập vào ShopHub**.

## Features

| Feature ID                   | Feature Name                     | Description                                                                                                    | Primary Roles                                           | Priority    | Status           |
| ---------------------------- | -------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- | ----------- | ---------------- |
| FEAT-CUSTOMER-LIST           | List Customers                   | Xem danh sách khách hàng thuộc shop.                                                                           | Shop Owner, Shop Manager, Customer Support, Accountant  | Must-have   | Ready for Design |
| FEAT-CUSTOMER-DETAIL         | View Customer Detail             | Xem thông tin khách, lịch sử đơn hàng.                                                                         | Shop Owner, Shop Manager, Customer Support, Order Staff | Must-have   | Ready for Design |
| FEAT-CUSTOMER-ORDER-HISTORY  | Customer Order History           | Xem lịch sử mua hàng của khách.                                                                                | Shop Owner, Shop Manager, Customer Support, Order Staff | Must-have   | Need Detail      |
| FEAT-CUSTOMER-NOTES          | Customer Care Notes              | Ghi chú chăm sóc khách hàng.                                                                                   | Shop Owner, Shop Manager, Customer Support, Order Staff | Should-have | Ready for Flow   |
| FEAT-CUSTOMER-ISSUE-TRACKING | Customer Issue / Ticket Tracking | Customer Support được tạo issue/ticket riêng để theo dõi vấn đề, khiếu nại hoặc yêu cầu hỗ trợ của khách hàng. | Customer Support, Order Staff, Shop Manager             | Should-have | Need Detail      |
| FEAT-CUSTOMER-NO-LOGIN       | No Customer Login                | Khách mua hàng cuối không có tài khoản đăng nhập ShopHub.                                                      | System                                                  | Must-have   | Ready for Dev    |

## Business Rules

| Rule ID                | Rule                                                                 |
| ---------------------- | -------------------------------------------------------------------- |
| RULE-CUSTOMER-FEAT-001 | Khách mua hàng cuối không được đăng nhập vào ShopHub.                |
| RULE-CUSTOMER-FEAT-002 | Dữ liệu khách hàng chỉ được xem trong phạm vi shop sở hữu đơn hàng.  |
| RULE-CUSTOMER-FEAT-003 | Customer Support không mặc định được xem dữ liệu tài chính nhạy cảm. |
| RULE-CUSTOMER-FEAT-004 | Ghi chú chăm sóc khách hàng cần lưu người tạo và thời gian tạo.      |

---

# 14. MOD-INVENTORY - Inventory Management

## Module Purpose

Quản lý tồn kho của shop theo mô hình:

* Physical Warehouse: mỗi shop có 1 kho vật lý ở giai đoạn đầu.
* Physical Stock: số lượng hàng thực tế.
* Available Stock: số lượng có thể bán/sync lên kênh bán.
* Reserved Stock: số lượng đã giữ cho đơn nhưng chưa xuất kho.

## Features

| Feature ID                         | Feature Name                  | Description                                                          | Primary Roles                                            | Priority    | Status           |
| ---------------------------------- | ----------------------------- | -------------------------------------------------------------------- | -------------------------------------------------------- | ----------- | ---------------- |
| FEAT-INVENTORY-VIEW                | View Inventory                | Xem tồn kho theo sản phẩm.                                           | Shop Owner, Shop Manager, Warehouse Staff, Product Staff | Must-have   | Ready for Design |
| FEAT-INVENTORY-PHYSICAL-STOCK      | Physical Stock Tracking       | Theo dõi số lượng hàng thực tế trong kho.                            | Warehouse Staff, Shop Owner, Shop Manager                | Must-have   | Need Detail      |
| FEAT-INVENTORY-AVAILABLE-STOCK     | Available Stock Tracking      | Theo dõi số lượng có thể bán/sync lên Lazada.                        | Warehouse Staff, Shop Owner, Shop Manager                | Must-have   | Need Detail      |
| FEAT-INVENTORY-RESERVED-STOCK      | Reserved Stock Tracking       | Theo dõi số lượng đang giữ cho đơn.                                  | Warehouse Staff, Shop Owner, Shop Manager                | Must-have   | Need Detail      |
| FEAT-INVENTORY-STOCK-IN            | Stock In                      | Ghi nhận nhập kho.                                                   | Warehouse Staff                                          | Must-have   | Need Detail      |
| FEAT-INVENTORY-STOCK-OUT           | Stock Out                     | Ghi nhận xuất kho/giao hàng.                                         | Warehouse Staff                                          | Must-have   | Need Detail      |
| FEAT-INVENTORY-ADJUSTMENT-REQUEST  | Inventory Adjustment Request  | Warehouse Staff tạo yêu cầu điều chỉnh tồn kho kèm lý do/bằng chứng. | Warehouse Staff                                          | Must-have   | Ready for Flow   |
| FEAT-INVENTORY-ADJUSTMENT-APPROVAL | Inventory Adjustment Approval | Shop Owner hoặc Shop Manager duyệt yêu cầu điều chỉnh tồn kho.       | Shop Owner, Shop Manager                                 | Must-have   | Ready for Flow   |
| FEAT-INVENTORY-LOW-STOCK-ALERT     | Low Stock Alert               | Cảnh báo sắp hết hàng.                                               | Shop Owner, Shop Manager, Warehouse Staff                | Should-have | Need Detail      |
| FEAT-INVENTORY-LOG                 | Inventory Movement Log        | Lịch sử biến động tồn kho.                                           | Shop Owner, Shop Manager, Warehouse Staff, Auditor       | Must-have   | Need Detail      |

## Business Rules

| Rule ID           | Rule                                                                |
| ----------------- | ------------------------------------------------------------------- |
| RULE-INV-FEAT-001 | Mỗi shop có 1 kho vật lý ở giai đoạn đầu.                           |
| RULE-INV-FEAT-002 | Xác nhận đơn làm giảm Available Stock và tăng Reserved Stock.       |
| RULE-INV-FEAT-003 | Giao/xuất kho làm giảm Physical Stock và giảm Reserved Stock.       |
| RULE-INV-FEAT-004 | Hủy đơn trước giao làm tăng Available Stock và giảm Reserved Stock. |
| RULE-INV-FEAT-005 | Điều chỉnh tồn kho cần lý do, bằng chứng và approval.               |
| RULE-INV-FEAT-006 | Mọi biến động tồn kho phải có inventory log.                        |

---

# 15. MOD-CHANNEL - Sales Channel Management

## Module Purpose

Quản lý kênh bán hàng được kết nối với shop. Giai đoạn đầu ưu tiên Lazada.

## Features

| Feature ID                       | Feature Name                  | Description                                     | Primary Roles                                        | Priority    | Status           |
| -------------------------------- | ----------------------------- | ----------------------------------------------- | ---------------------------------------------------- | ----------- | ---------------- |
| FEAT-CHANNEL-LIST                | List Sales Channels           | Xem danh sách kênh bán của shop.                | Shop Owner, Shop Manager                             | Must-have   | Ready for Design |
| FEAT-CHANNEL-LAZADA-CONNECT      | Connect Lazada                | Kết nối shop với Lazada.                        | Shop Owner, Shop Manager, Integration Admin          | Must-have   | Need Detail      |
| FEAT-CHANNEL-LAZADA-DISCONNECT   | Disconnect Lazada             | Ngắt kết nối Lazada nếu được phép.              | Shop Owner, Integration Admin                        | Should-have | Need Detail      |
| FEAT-CHANNEL-CONNECTION-STATUS   | Channel Connection Status     | Xem trạng thái kết nối kênh.                    | Shop Owner, Shop Manager, Support, Integration Admin | Must-have   | Need Detail      |
| FEAT-CHANNEL-CONFIG              | Channel Configuration         | Cấu hình thông tin kết nối/kênh bán.            | Shop Owner, Integration Admin                        | Must-have   | Need Detail      |
| FEAT-CHANNEL-MULTI-CHANNEL-READY | Multi-channel Ready Structure | Cấu trúc sẵn sàng mở rộng thêm kênh sau Lazada. | System                                               | Should-have | Need Detail      |

## Business Rules

| Rule ID          | Rule                                                             |
| ---------------- | ---------------------------------------------------------------- |
| RULE-CHANNEL-001 | Giai đoạn đầu ưu tiên Lazada.                                    |
| RULE-CHANNEL-002 | Một shop có thể kết nối nhiều kênh trong tương lai.              |
| RULE-CHANNEL-003 | Kết nối/ngắt kết nối kênh là thao tác nhạy cảm và cần audit log. |
| RULE-CHANNEL-004 | Không được để user shop này xem kết nối của shop khác.           |

---

# 16. MOD-SYNC - Real-time / Near Real-time Sync

## Module Purpose

Đồng bộ dữ liệu giữa ShopHub và Lazada. Mục tiêu là real-time nếu Lazada hỗ trợ webhook, hoặc near real-time nếu cần polling/job.

## Features

| Feature ID                       | Feature Name           | Description                                                                                               | Primary Roles                              | Priority    | Status      |
| -------------------------------- | ---------------------- | --------------------------------------------------------------------------------------------------------- | ------------------------------------------ | ----------- | ----------- |
| FEAT-SYNC-ORDER                  | Sync Lazada Orders     | Đồng bộ đơn hàng từ Lazada.                                                                               | System, Integration Admin                  | Must-have   | Need Detail |
| FEAT-SYNC-ORDER-STATUS           | Sync Order Status      | Đồng bộ trạng thái đơn hàng.                                                                              | System, Integration Admin                  | Must-have   | Need Detail |
| FEAT-SYNC-PRODUCT                | Sync Products          | Đồng bộ sản phẩm nếu API hỗ trợ.                                                                          | System, Product Staff, Integration Admin   | Must-have   | Need Detail |
| FEAT-SYNC-STOCK                  | Sync Available Stock   | Đồng bộ Available Stock lên Lazada.                                                                       | System, Warehouse Staff, Integration Admin | Must-have   | Need Detail |
| FEAT-SYNC-CUSTOMER               | Sync Customer Data     | Đồng bộ dữ liệu khách hàng từ đơn hàng.                                                                   | System                                     | Should-have | Need Detail |
| FEAT-SYNC-FEES                   | Sync Marketplace Fees  | Đồng bộ phí sàn/khuyến mãi/đối soát nếu API hỗ trợ.                                                       | System, Accountant, Integration Admin      | Should-have | Need Detail |
| FEAT-SYNC-LOG                    | Sync Log               | Lưu log đồng bộ.                                                                                          | Integration Admin, Support, Shop Owner     | Must-have   | Need Detail |
| FEAT-SYNC-ERROR                  | Sync Error Handling    | Hiển thị và xử lý lỗi đồng bộ.                                                                            | Integration Admin, Support, Shop Owner     | Must-have   | Need Detail |
| FEAT-SYNC-RETRY                  | Retry Sync             | Retry sync theo workflow an toàn.                                                                         | Integration Admin                          | Must-have   | Need Detail |
| FEAT-SYNC-MANUAL-RESYNC-APPROVAL | Manual Resync Approval | Manual resync chỉ cần xác nhận Shop Owner khi thao tác có khả năng ảnh hưởng dữ liệu quan trọng của shop. | Integration Admin, Shop Owner              | Should-have | Need Detail |

## Business Rules

| Rule ID       | Rule                                                                                                                         |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| RULE-SYNC-001 | Cần ưu tiên sync gần như tất cả dữ liệu quan trọng nếu Lazada API hỗ trợ.                                                    |
| RULE-SYNC-002 | Lazada API hiện chưa chốt được khả năng hỗ trợ chi tiết, nên không được giả định API hỗ trợ tất cả dữ liệu trước khi verify. |
| RULE-SYNC-003 | Manual resync có nguy cơ ảnh hưởng dữ liệu quan trọng của shop cần workflow an toàn và xác nhận Shop Owner.                  |
| RULE-SYNC-004 | Sync lỗi cần có trạng thái, log và khả năng retry.                                                                           |
| RULE-SYNC-005 | Available Stock là số lượng nên sync lên Lazada để tránh bán vượt.                                                           |

---

# 17. MOD-SHIPPING - Shipping Core

## Module Purpose

Thiết kế core vận chuyển trước để sau này có thể tích hợp API đơn vị vận chuyển cụ thể mà không phải sửa lại toàn bộ nghiệp vụ.

Giai đoạn đầu chưa chốt nhà vận chuyển.

## Features

| Feature ID                         | Feature Name                        | Description                                               | Primary Roles                              | Priority    | Status      |
| ---------------------------------- | ----------------------------------- | --------------------------------------------------------- | ------------------------------------------ | ----------- | ----------- |
| FEAT-SHIPPING-SHIPMENT-MODEL       | Shipment Model                      | Tạo cấu trúc dữ liệu shipment cho đơn hàng.               | System                                     | Should-have | Need Detail |
| FEAT-SHIPPING-STATUS               | Shipping Status                     | Theo dõi trạng thái giao hàng ở mức chuẩn hóa.            | Order Staff, Warehouse Staff, Shop Manager | Should-have | Need Detail |
| FEAT-SHIPPING-PROVIDER-ABSTRACTION | Shipping Provider Abstraction       | Thiết kế lớp provider để sau này tích hợp API vận chuyển. | System, Integration Admin                  | Should-have | Need Detail |
| FEAT-SHIPPING-TRACKING             | Tracking Information                | Lưu mã vận đơn/tracking nếu có.                           | Order Staff, Warehouse Staff               | Should-have | Need Detail |
| FEAT-SHIPPING-MAP-ORDER-STATUS     | Map Shipping Status to Order Status | Mapping trạng thái vận chuyển với trạng thái đơn.         | System                                     | Should-have | Need Detail |

## Business Rules

| Rule ID       | Rule                                                                              |
| ------------- | --------------------------------------------------------------------------------- |
| RULE-SHIP-001 | Chưa chốt nhà vận chuyển ở phase đầu.                                             |
| RULE-SHIP-002 | Cần thiết kế core shipping trước để dễ tích hợp API sau.                          |
| RULE-SHIP-003 | Trạng thái vận chuyển không được làm lệch trạng thái đơn hàng chuẩn hóa.          |
| RULE-SHIP-004 | Xuất kho/giao hàng phải liên quan đến rule giảm Physical Stock và Reserved Stock. |

---

# 18. MOD-REPORT - Reports & Analytics

## Module Purpose

Cung cấp báo cáo doanh thu, đơn hàng, tồn kho, sản phẩm và lợi nhuận cho shop.

Lợi nhuận cần có công thức mặc định nhưng cho phép shop tự điều chỉnh thành phần chi phí, với approval khi cần.

## Features

| Feature ID                          | Feature Name                | Description                                                              | Primary Roles                             | Priority    | Status         |
| ----------------------------------- | --------------------------- | ------------------------------------------------------------------------ | ----------------------------------------- | ----------- | -------------- |
| FEAT-REPORT-REVENUE                 | Revenue Report              | Báo cáo doanh thu theo thời gian.                                        | Shop Owner, Shop Manager, Accountant      | Must-have   | Need Detail    |
| FEAT-REPORT-PROFIT                  | Profit Report               | Báo cáo lợi nhuận theo công thức của shop.                               | Shop Owner, Accountant                    | Must-have   | Need Detail    |
| FEAT-REPORT-PROFIT-FORMULA          | Profit Formula Settings     | Cấu hình công thức lợi nhuận.                                            | Shop Owner, Accountant                    | Must-have   | Need Detail    |
| FEAT-REPORT-PROFIT-FORMULA-APPROVAL | Profit Formula Approval     | Accountant chỉnh công thức cần Shop Owner hoặc Shop Manager duyệt.       | Shop Owner, Shop Manager, Accountant      | Must-have   | Ready for Flow |
| FEAT-REPORT-ORDER                   | Order Report                | Báo cáo số lượng đơn, trạng thái đơn, đơn hủy.                           | Shop Owner, Shop Manager                  | Should-have | Need Detail    |
| FEAT-REPORT-PRODUCT                 | Product Performance Report  | Báo cáo sản phẩm bán chạy/chậm.                                          | Shop Owner, Shop Manager, Product Staff   | Should-have | Need Detail    |
| FEAT-REPORT-INVENTORY               | Inventory Report            | Báo cáo tồn kho, hàng sắp hết, biến động tồn.                            | Shop Owner, Shop Manager, Warehouse Staff | Should-have | Need Detail    |
| FEAT-REPORT-CHANNEL                 | Channel Performance Report  | Báo cáo hiệu suất theo kênh bán.                                         | Shop Owner, Shop Manager                  | Should-have | Need Detail    |
| FEAT-REPORT-DATA-QUALITY            | Report Data Quality Warning | Cảnh báo dữ liệu thiếu như giá vốn/phí khiến báo cáo chưa chính xác.     | Shop Owner, Accountant                    | Must-have   | Need Detail    |
| FEAT-REPORT-EXPORT                  | Export Reports              | Xuất báo cáo dạng Excel hoặc PDF, có format riêng cho từng loại báo cáo. | Shop Owner, Shop Manager, Accountant      | Should-have | Need Detail    |

## Business Rules

| Rule ID         | Rule                                                                               |
| --------------- | ---------------------------------------------------------------------------------- |
| RULE-REPORT-001 | Profit report cần công thức mặc định.                                              |
| RULE-REPORT-002 | Shop có thể tùy chỉnh thành phần chi phí trong công thức lợi nhuận.                |
| RULE-REPORT-003 | Accountant chỉnh công thức lợi nhuận cần approval.                                 |
| RULE-REPORT-004 | Nếu thiếu dữ liệu giá vốn/phí, hệ thống phải cảnh báo dữ liệu chưa đầy đủ.         |
| RULE-REPORT-005 | Báo cáo chỉ hiển thị dữ liệu thuộc shop hiện tại.                                  |
| RULE-REPORT-006 | Export báo cáo cần hỗ trợ Excel hoặc PDF. Mỗi loại báo cáo có thể có format riêng. |

---

# 19. MOD-SUBSCRIPTION - Subscription & Payment

## Module Purpose

Quản lý gói dịch vụ ShopHub cho từng shop và thanh toán subscription.

Payment trong module này là thanh toán gói dịch vụ ShopHub, **không phải thanh toán đơn hàng của khách mua**.

## Features

| Feature ID                         | Feature Name                        | Description                                                               | Primary Roles              | Priority    | Status           |
| ---------------------------------- | ----------------------------------- | ------------------------------------------------------------------------- | -------------------------- | ----------- | ---------------- |
| FEAT-SUBSCRIPTION-VIEW-PLAN        | View Current Plan                   | Shop xem gói hiện tại.                                                    | Shop Owner                 | Must-have   | Ready for Design |
| FEAT-SUBSCRIPTION-PLAN-LIMITS      | Plan Limits                         | Áp dụng giới hạn theo gói: user, kênh, đơn, báo cáo, audit log.           | System, Billing Admin      | Must-have   | Need Detail      |
| FEAT-SUBSCRIPTION-UPGRADE          | Upgrade Plan                        | Nâng gói qua subscription/payment flow.                                   | Shop Owner                 | Must-have   | Need Detail      |
| FEAT-SUBSCRIPTION-DOWNGRADE        | Downgrade Plan                      | Hạ gói qua subscription/payment flow.                                     | Shop Owner                 | Should-have | Need Detail      |
| FEAT-SUBSCRIPTION-PAYMENT          | Subscription Payment                | Thanh toán gói dịch vụ qua VNPay bằng QR payment.                         | Shop Owner, Billing Admin  | Must-have   | Need Detail      |
| FEAT-SUBSCRIPTION-INVOICE          | Invoice / Payment Record Management | Quản lý hóa đơn/payment record cho giao dịch thanh toán gói qua VNPay QR. | Billing Admin, Shop Owner  | Should-have | Need Detail      |
| FEAT-SUBSCRIPTION-STATUS           | Subscription Status                 | Trạng thái Trial, Active, Past Due, Suspended, Cancelled.                 | System, Billing Admin      | Must-have   | Need Detail      |
| FEAT-SUBSCRIPTION-NO-MANUAL-CHANGE | No Manual Plan Change               | Billing Admin không được đổi gói thủ công ngoài flow.                     | System                     | Must-have   | Ready for Dev    |
| FEAT-SUBSCRIPTION-PLAN-MGMT        | Manage Plan Definition              | Platform quản lý định nghĩa gói Free/Trial, Pro, Premium, Enterprise.     | Billing Admin, Super Admin | Should-have | Need Detail      |

## Draft Plan Limits

| Plan                | Suggested Limits                                                                                                     | Status                               |
| ------------------- | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| Free / Trial        | 1 shop, 1 kênh, tối đa 2 user, giới hạn số đơn/tháng, báo cáo cơ bản.                                                | Draft                                |
| Pro                 | 1 shop, Lazada + thêm kênh khi có, tối đa 10 user, sync real-time/gần real-time, báo cáo doanh thu/lợi nhuận cơ bản. | Draft - based on 01_PROJECT_BRIEF.md |
| Premium             | 1 shop, nhiều kênh khi hỗ trợ, nhiều user hơn, báo cáo nâng cao, audit log chi tiết hơn, ưu tiên sync/hỗ trợ.        | Draft - based on 01_PROJECT_BRIEF.md |
| Enterprise / Custom | Gói tùy chỉnh.                                                                                                       | Later                                |

## Business Rules

| Rule ID      | Rule                                                                                              |
| ------------ | ------------------------------------------------------------------------------------------------- |
| RULE-SUB-001 | Mỗi shop phải có một subscription plan.                                                           |
| RULE-SUB-002 | Mọi thay đổi gói phải đi qua subscription/payment flow.                                           |
| RULE-SUB-003 | Billing Admin không được đổi gói thủ công.                                                        |
| RULE-SUB-004 | Thay đổi subscription/payment cần audit log.                                                      |
| RULE-SUB-006 | Payment provider cho subscription phase đầu là VNPay, hình thức thanh toán qua QR.                |
| RULE-SUB-005 | Plan limit cần được kiểm tra khi tạo user, kết nối kênh, xem report hoặc dùng tính năng nâng cao. |

---

# 20. MOD-AUDIT - Audit Log

## Module Purpose

Ghi nhận lịch sử thao tác nhạy cảm để truy vết, kiểm tra lỗi và đảm bảo an toàn dữ liệu.

## Features

| Feature ID                | Feature Name             | Description                                               | Primary Roles                        | Priority  | Status      |
| ------------------------- | ------------------------ | --------------------------------------------------------- | ------------------------------------ | --------- | ----------- |
| FEAT-AUDIT-ORDER          | Order Audit Log          | Log thay đổi trạng thái đơn, hủy đơn, ghi chú quan trọng. | Shop Owner, Auditor, ShopHub Admin   | Must-have | Need Detail |
| FEAT-AUDIT-INVENTORY      | Inventory Audit Log      | Log nhập/xuất/điều chỉnh tồn kho.                         | Shop Owner, Warehouse Staff, Auditor | Must-have | Need Detail |
| FEAT-AUDIT-USER-ROLE      | User & Role Audit Log    | Log mời user, gán/gỡ role, khóa user.                     | Shop Owner, ShopHub Admin, Auditor   | Must-have | Need Detail |
| FEAT-AUDIT-SUBSCRIPTION   | Subscription Audit Log   | Log thay đổi gói, thanh toán, trạng thái subscription.    | Billing Admin, Auditor               | Must-have | Need Detail |
| FEAT-AUDIT-SYNC           | Sync Audit Log           | Log retry, manual resync, lỗi sync.                       | Integration Admin, Auditor           | Must-have | Need Detail |
| FEAT-AUDIT-REPORT-FORMULA | Profit Formula Audit Log | Log thay đổi công thức lợi nhuận.                         | Shop Owner, Accountant, Auditor      | Must-have | Need Detail |

## Business Rules

| Rule ID        | Rule                                                                                      |
| -------------- | ----------------------------------------------------------------------------------------- |
| RULE-AUDIT-001 | Audit log không được sửa/xóa bởi user thông thường.                                       |
| RULE-AUDIT-002 | Mọi thao tác nhạy cảm cần lưu actor, thời gian, hành động, dữ liệu trước/sau nếu phù hợp. |
| RULE-AUDIT-003 | Audit log phải tôn trọng tenant boundary.                                                 |
| RULE-AUDIT-004 | Platform auditor chỉ xem theo phạm vi được cấp.                                           |

---

# 21. MOD-NOTIFICATION - Notification

## Module Purpose

Thông báo cho user về trạng thái quan trọng như lỗi sync, đơn cần xử lý, tồn kho thấp, yêu cầu duyệt, thanh toán subscription.

## Features

| Feature ID             | Feature Name              | Description                                          | Primary Roles                                              | Priority    | Status      |
| ---------------------- | ------------------------- | ---------------------------------------------------- | ---------------------------------------------------------- | ----------- | ----------- |
| FEAT-NOTI-TOAST        | Toast Notification        | Thông báo thao tác thành công/thất bại trên UI.      | All login roles                                            | Must-have   | Need Detail |
| FEAT-NOTI-SYNC-ERROR   | Sync Error Notification   | Thông báo lỗi đồng bộ.                               | Shop Owner, Shop Manager, Product Staff, Integration Admin | Should-have | Need Detail |
| FEAT-NOTI-LOW-STOCK    | Low Stock Notification    | Thông báo hàng sắp hết.                              | Shop Owner, Shop Manager, Warehouse Staff                  | Should-have | Need Detail |
| FEAT-NOTI-APPROVAL     | Approval Notification     | Thông báo yêu cầu duyệt tồn kho/công thức lợi nhuận. | Shop Owner, Shop Manager, Accountant, Warehouse Staff      | Should-have | Need Detail |
| FEAT-NOTI-SUBSCRIPTION | Subscription Notification | Thông báo thanh toán/gói sắp hết hạn/quá hạn.        | Shop Owner, Billing Admin                                  | Should-have | Need Detail |

## Business Rules

| Rule ID       | Rule                                                              |
| ------------- | ----------------------------------------------------------------- |
| RULE-NOTI-001 | Notification phải tôn trọng role và tenant boundary.              |
| RULE-NOTI-002 | Lỗi nghiêm trọng như sync fail hoặc payment fail cần hiển thị rõ. |
| RULE-NOTI-003 | Approval request cần thông báo cho đúng người có quyền duyệt.     |

---

# 22. MOD-SETTING - Settings

## Module Purpose

Quản lý cấu hình shop và cấu hình platform.

## Features

| Feature ID                  | Feature Name            | Description                                    | Primary Roles                 | Priority    | Status            |
| --------------------------- | ----------------------- | ---------------------------------------------- | ----------------------------- | ----------- | ----------------- |
| FEAT-SETTING-SHOP-PROFILE   | Shop Profile Settings   | Cấu hình thông tin shop.                       | Shop Owner                    | Should-have | Need Detail       |
| FEAT-SETTING-PROFIT-FORMULA | Profit Formula Settings | Cấu hình công thức lợi nhuận của shop.         | Shop Owner, Accountant        | Must-have   | Linked to Report  |
| FEAT-SETTING-INVENTORY      | Inventory Settings      | Cấu hình ngưỡng tồn thấp, rule tồn kho cơ bản. | Shop Owner, Shop Manager      | Should-have | Need Detail       |
| FEAT-SETTING-CHANNEL        | Channel Settings        | Cấu hình kênh bán như Lazada.                  | Shop Owner, Integration Admin | Must-have   | Linked to Channel |
| FEAT-SETTING-PLATFORM       | Platform Settings       | Cấu hình hệ thống cấp ShopHub.                 | Super Admin, ShopHub Admin    | Should-have | Need Detail       |

## Business Rules

| Rule ID          | Rule                                                             |
| ---------------- | ---------------------------------------------------------------- |
| RULE-SETTING-001 | Shop settings chỉ áp dụng trong phạm vi shop.                    |
| RULE-SETTING-002 | Platform settings chỉ dành cho platform user có quyền.           |
| RULE-SETTING-003 | Cấu hình ảnh hưởng dữ liệu tài chính/tồn kho/sync cần audit log. |

---

# 22.1 Frontend Technical Decision

| Decision Area  | Value                                                                               |
| -------------- | ----------------------------------------------------------------------------------- |
| Frontend stack | React + Vite                                                                        |
| Reason         | Phù hợp dashboard quản trị, tốc độ dev nhanh, dễ tổ chức component và tích hợp API. |

---

# 23. MOD-MARKETING - Marketing / Promotion

## Module Purpose

Quản lý promotion/marketing nếu sau này shop cần.

Module này **không nằm trong scope phase đầu**.

## Features

| Feature ID               | Feature Name         | Description                         | Primary Roles               | Priority | Status   |
| ------------------------ | -------------------- | ----------------------------------- | --------------------------- | -------- | -------- |
| FEAT-MARKETING-PROMOTION | Promotion Management | Quản lý khuyến mãi.                 | Shop Owner, Marketing Staff | Later    | Deferred |
| FEAT-MARKETING-CAMPAIGN  | Campaign Management  | Quản lý chiến dịch marketing.       | Shop Owner, Marketing Staff | Later    | Deferred |
| FEAT-MARKETING-VOUCHER   | Voucher Management   | Quản lý voucher nếu cần.            | Shop Owner, Marketing Staff | Later    | Deferred |
| FEAT-MARKETING-ROLE      | Marketing Staff Role | Role riêng cho marketing/promotion. | Shop Owner                  | Later    | Deferred |

## Business Rules

| Rule ID      | Rule                                                                      |
| ------------ | ------------------------------------------------------------------------- |
| RULE-MKT-001 | Marketing/Promotion để phase sau.                                         |
| RULE-MKT-002 | Chưa tạo role Marketing Staff ở phase đầu nếu chưa có nhu cầu triển khai. |

---

# 24. Must-have Feature Summary

| Module           | Must-have Features                                                                                           |
| ---------------- | ------------------------------------------------------------------------------------------------------------ |
| MOD-AUTH         | Login, Logout, Session, RBAC, Multiple Roles                                                                 |
| MOD-TENANT       | Create Shop, View Shop, Shop Status, Tenant Boundary, Multiple Channels per Shop                             |
| MOD-USER         | Invite Shop User, Accept Invite, List Users, Assign/Remove Role, Deactivate User, Platform User Management   |
| MOD-DASHBOARD    | Seller Overview, Order Summary, Inventory Alerts, Sync Alerts                                                |
| MOD-PRODUCT      | Product List, Detail, Create, Update, Status, SKU, Price, Cost, Sync Status, Safe Deactivation               |
| MOD-CATEGORY     | List, Create, Update Categories                                                                              |
| MOD-ORDER        | Order List, Detail, Filter, Marketplace Status, Normalized Status, Update Status, Cancel Order, Audit        |
| MOD-CUSTOMER     | Customer List, Detail, Order History, No Customer Login                                                      |
| MOD-INVENTORY    | View Inventory, Physical/Available/Reserved Stock, Stock In/Out, Adjustment Request/Approval, Inventory Log  |
| MOD-CHANNEL      | List Channels, Connect Lazada, Connection Status, Channel Config                                             |
| MOD-SYNC         | Sync Orders, Status, Products, Stock, Sync Log, Error Handling, Retry                                        |
| MOD-REPORT       | Revenue Report, Profit Report, Profit Formula, Profit Formula Approval, Data Quality Warning, Export Reports |
| MOD-SUBSCRIPTION | View Plan, Plan Limits, Upgrade, Payment, Subscription Status, No Manual Plan Change                         |
| MOD-AUDIT        | Order, Inventory, User/Role, Subscription, Sync, Profit Formula Audit Logs                                   |
| MOD-NOTIFICATION | Toast Notification                                                                                           |
| MOD-SETTING      | Profit Formula Settings, Channel Settings                                                                    |

---

# 25. Deferred / Later Feature Summary

| Feature ID                     | Feature Name                          | Reason                                              |
| ------------------------------ | ------------------------------------- | --------------------------------------------------- |
| FEAT-MARKETING-PROMOTION       | Promotion Management                  | Để phase sau.                                       |
| FEAT-MARKETING-CAMPAIGN        | Campaign Management                   | Để phase sau.                                       |
| FEAT-MARKETING-VOUCHER         | Voucher Management                    | Để phase sau.                                       |
| FEAT-MARKETING-ROLE            | Marketing Staff Role                  | Chưa cần role riêng ở phase đầu.                    |
| FEAT-TENANT-MULTI-SHOP-USER    | One User Multiple Shops               | Có thể cần sau, hiện phase đầu 1 user thuộc 1 shop. |
| FEAT-INVENTORY-MULTI-WAREHOUSE | Multiple Physical Warehouses per Shop | Phase đầu mỗi shop chỉ có 1 kho vật lý.             |
| FEAT-IMPORT-EXCEL              | Excel Import                          | Đã chốt chưa cần import Excel ở phase đầu.          |
| FEAT-MOBILE-APP                | Native Mobile App                     | Ưu tiên web dashboard.                              |
| FEAT-AI-RECOMMENDATION         | AI Recommendation                     | Không thuộc scope phase đầu.                        |
| FEAT-ADVANCED-CRM              | Advanced CRM                          | Không thuộc scope phase đầu.                        |

---

# 26. Open Questions

| Question ID | Question                                                                                             | Related Area                                                                                                          | Impact                | Status                                                |                                          |
| ----------- | ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | --------------------- | ----------------------------------------------------- | ---------------------------------------- |
| Q-FEAT-001  | Frontend Vite dùng React hay Vue?                                                                    | Dùng React + Vite.                                                                                                    | Technical / Frontend  | Ảnh hưởng Technical Architecture và UI implementation | Answered                                 |
| Q-FEAT-002  | Lazada API thực tế hỗ trợ những dữ liệu nào?                                                         | Hiện chưa chốt được. Cần verify Lazada API ở tài liệu Integration/Technical Architecture.                             | Sync / Integration    | Ảnh hưởng scope sync chính xác                        | Open / Need API Verification             |
| Q-FEAT-003  | Danh sách trạng thái chuẩn của ShopHub cho Order là gì?                                              | Chưa chốt trong Feature List. Sẽ xử lý ở Business Flow vì phụ thuộc mapping trạng thái sàn và trạng thái nội bộ shop. | Order / Business Flow | Ảnh hưởng Business Flow, UI, API                      | Open                                     |
| Q-FEAT-004  | Product status chuẩn gồm những trạng thái nào?                                                       | Trạng thái sản phẩm theo trạng thái từ sàn và có thể custom theo shop. Cần mapping cụ thể ở Business Flow/Data Model. | Product               | Ảnh hưởng UI, API, Sync                               | Answered at Product Level / Need Mapping |
| Q-FEAT-005  | Gói Pro/Premium giới hạn cụ thể bao nhiêu user, order, channel?                                      | Dùng giới hạn đã nhắc ở 01_PROJECT_BRIEF.md làm bản draft ban đầu.                                                    | Subscription          | Ảnh hưởng plan enforcement                            | Answered as Draft                        |
| Q-FEAT-006  | Payment provider cho subscription là gì?                                                             | VNPay, thanh toán qua QR.                                                                                             | Payment               | Ảnh hưởng API và Technical Architecture               | Answered                                 |
| Q-FEAT-007  | Có cần export Excel không, dù đã chốt chưa cần import Excel?                                         | Có export báo cáo dạng Excel hoặc PDF, mỗi loại báo cáo có format riêng.                                              | Reports / Data Export | Ảnh hưởng feature scope                               | Answered                                 |
| Q-FEAT-008  | Có cần duyệt hủy đơn với Order Staff không, hay Order Staff được hủy trực tiếp nếu rule cho phép?    | Nếu rule cho phép hủy thì vẫn phải có bước confirm trước khi thực hiện.                                               | Order                 | Ảnh hưởng Business Flow và Permission Matrix          | Answered                                 |
| Q-FEAT-009  | Customer Support có được tạo issue/ticket riêng không hay chỉ ghi chú khách hàng?                    | Có. Customer Support được tạo issue/ticket riêng.                                                                     | Customer Care         | Ảnh hưởng Customer Module                             | Answered                                 |
| Q-FEAT-010  | Manual resync cần xác nhận Shop Owner trong mọi trường hợp hay chỉ khi ảnh hưởng dữ liệu quan trọng? | Chỉ cần xác nhận Shop Owner khi manual resync ảnh hưởng dữ liệu quan trọng.                                           | Sync                  | Ảnh hưởng Sync Flow                                   | Answered                                 |

---

# 27. Decisions Log

| Decision ID  | Decision                                                                                | Reason                                                                     | Related Docs                                          | Status   |
| ------------ | --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------- | -------- |
| DEC-FEAT-001 | Feature List tách rõ seller-side và platform-side module.                               | Tránh lẫn quyền vận hành shop với quyền vận hành nền tảng.                 | 02_USER_ROLES.md                                      | Accepted |
| DEC-FEAT-002 | Phase đầu ưu tiên Lazada.                                                               | Giảm scope tích hợp đa kênh.                                               | 01_PROJECT_BRIEF.md                                   | Accepted |
| DEC-FEAT-003 | Khách mua hàng cuối không có feature đăng nhập.                                         | ShopHub là dashboard nội bộ, không phải customer portal.                   | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md                 | Accepted |
| DEC-FEAT-004 | Mỗi shop có 1 kho vật lý ở phase đầu.                                                   | Giảm độ phức tạp inventory.                                                | 01_PROJECT_BRIEF.md                                   | Accepted |
| DEC-FEAT-005 | Inventory dùng Physical Stock / Available Stock / Reserved Stock.                       | Tránh sai lệch tồn kho khi có đơn và sync kênh bán.                        | 01_PROJECT_BRIEF.md                                   | Accepted |
| DEC-FEAT-006 | Subscription/payment là payment cho gói ShopHub, không phải payment đơn hàng khách mua. | Làm rõ phạm vi payment.                                                    | 01_PROJECT_BRIEF.md                                   | Accepted |
| DEC-FEAT-007 | Billing Admin không được đổi gói thủ công.                                              | Mọi thay đổi gói phải có flow và audit.                                    | 02_USER_ROLES.md                                      | Accepted |
| DEC-FEAT-008 | ShopHub Support không được impersonate user shop.                                       | Giảm rủi ro bảo mật.                                                       | 02_USER_ROLES.md                                      | Accepted |
| DEC-FEAT-009 | Thêm Customer Support role và feature chăm sóc khách hàng.                              | Shop cần hỗ trợ khách hàng liên quan đơn hàng.                             | 02_USER_ROLES.md                                      | Accepted |
| DEC-FEAT-010 | Marketing/Promotion để phase sau.                                                       | Chưa thuộc core scope.                                                     | 02_USER_ROLES.md                                      | Accepted |
| DEC-FEAT-011 | Frontend dùng React + Vite.                                                             | Phù hợp dashboard quản trị và tech stack đã chốt.                          | 11_TECHNICAL_ARCHITECTURE.md                          | Accepted |
| DEC-FEAT-012 | Payment provider cho subscription là VNPay QR.                                          | ShopHub cần thanh toán gói dịch vụ qua QR.                                 | 08_API_SPECIFICATION.md, 11_TECHNICAL_ARCHITECTURE.md | Accepted |
| DEC-FEAT-013 | Export báo cáo hỗ trợ Excel hoặc PDF.                                                   | Shop cần xuất báo cáo theo format từng loại báo cáo.                       | 06_SCREEN_REQUIREMENTS/, 08_API_SPECIFICATION.md      | Accepted |
| DEC-FEAT-014 | Customer Support được tạo issue/ticket.                                                 | Chăm sóc khách hàng cần theo dõi vấn đề riêng, không chỉ ghi chú đơn giản. | 04_BUSINESS_FLOW.md, 07_DATA_MODEL.md                 | Accepted |
| DEC-FEAT-015 | Product status theo sàn và có thể custom theo shop.                                     | Cần linh hoạt với Lazada và vận hành nội bộ.                               | 04_BUSINESS_FLOW.md, 07_DATA_MODEL.md                 | Accepted |
| DEC-FEAT-016 | Manual resync chỉ cần Shop Owner xác nhận khi ảnh hưởng dữ liệu quan trọng.             | Giảm ma sát vận hành nhưng vẫn bảo vệ dữ liệu nhạy cảm.                    | 04_BUSINESS_FLOW.md, 09_PERMISSION_MATRIX.md          | Accepted |
| DEC-FEAT-017 | Lazada API chưa chốt được khả năng chi tiết.                                            | Không giả định khả năng API trước khi verify.                              | 11_TECHNICAL_ARCHITECTURE.md                          | Accepted |

---

# 28. Change Log

| Version | Date | Change                                                                                                                                                                                                                                                                                                                            | Author |
| ------- | ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| 0.1     | TBD  | Initial Feature List based on Project Brief v0.4 and User Roles v0.2                                                                                                                                                                                                                                                              | TBD    |
| 0.2     | TBD  | Updated Open Questions answers: React + Vite, Lazada API not finalized, product status from marketplace/custom shop status, plan limits from Project Brief, VNPay QR subscription payment, Excel/PDF report export, cancel confirmation, customer support issue/ticket, manual resync confirmation only for important data impact | TBD    |
