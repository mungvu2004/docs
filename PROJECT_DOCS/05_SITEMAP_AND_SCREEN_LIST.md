# 05_SITEMAP_AND_SCREEN_LIST.md

## Metadata

| Field        | Value                                                                                                                                                                       |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Document ID  | DOC-05-SITEMAP-SCREEN-LIST                                                                                                                                                  |
| Project Name | Multi-channel E-commerce Hub / ShopHub                                                                                                                                      |
| Version      | 0.2                                                                                                                                                                         |
| Status       | Draft - Updated Open Questions Answers                                                                                                                                      |
| Owner        | TBD                                                                                                                                                                         |
| Last Updated | TBD                                                                                                                                                                         |
| Depends On   | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md                                                                                              |
| Used By      | 00_TRACEABILITY_MATRIX.md, 06_SCREEN_REQUIREMENTS/, 07_DATA_MODEL.md, 08_API_SPECIFICATION.md, 09_PERMISSION_MATRIX.md, 10_UI_UX_DIRECTION.md, 11_TECHNICAL_ARCHITECTURE.md |

---

# 1. Purpose

Tài liệu này xác định **sitemap và danh sách màn hình** cho dự án Multi-channel E-commerce Hub / ShopHub.

Mục tiêu:

* Chuyển Project Brief, User Roles, Feature List và Business Flow thành danh sách màn hình cụ thể.
* Tách rõ màn hình dành cho **Seller-side** và **Platform-side**.
* Đảm bảo mỗi màn hình có Screen ID, route, module, feature, flow và role liên quan.
* Làm đầu vào trực tiếp cho `06_SCREEN_REQUIREMENTS/`.
* Tránh việc designer/dev/AI tự tạo màn hình ngoài scope.

Tài liệu này **không mô tả chi tiết UI component từng màn hình**. Chi tiết từng màn sẽ nằm trong `06_SCREEN_REQUIREMENTS/`.

---

# 2. Scope

## 2.1 In Scope

Tài liệu này bao gồm:

* Sitemap tổng quan cho Seller-side app.
* Sitemap tổng quan cho Platform-side app.
* Danh sách màn hình phase đầu.
* Mapping screen với module, feature, flow và role.
* Các màn hình background/system flow cần có màn giám sát.
* Các màn hình deferred để phase sau.
* Checklist verify coverage.

## 2.2 Out of Scope

Tài liệu này không bao gồm:

* Layout chi tiết từng màn hình.
* UI component chi tiết.
* Form field chi tiết.
* API contract.
* Database schema.
* Design system.
* Wireframe.
* Logic code.

---

# 3. Sitemap Principles

| Rule ID          | Rule                                                                                                                                      |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| RULE-SITEMAP-001 | Mỗi màn hình phải có Screen ID cố định.                                                                                                   |
| RULE-SITEMAP-002 | Mỗi màn hình phải map với ít nhất một Module ID.                                                                                          |
| RULE-SITEMAP-003 | Mỗi màn hình nên map với Feature ID và Flow ID liên quan.                                                                                 |
| RULE-SITEMAP-004 | Không tạo màn hình nếu không có feature/flow nguồn hoặc lý do rõ ràng.                                                                    |
| RULE-SITEMAP-005 | Seller-side screens và Platform-side screens phải tách riêng.                                                                             |
| RULE-SITEMAP-006 | Khách mua hàng cuối không được đăng nhập ShopHub, nên không tạo customer portal.                                                          |
| RULE-SITEMAP-007 | Màn hình liên quan dữ liệu shop phải tôn trọng tenant boundary.                                                                           |
| RULE-SITEMAP-008 | Background/system flow không nhất thiết có màn thao tác trực tiếp, nhưng phải có màn giám sát nếu cần.                                    |
| RULE-SITEMAP-009 | Marketing/Promotion để phase sau, không đưa vào phase đầu.                                                                                |
| RULE-SITEMAP-010 | Lazada API chưa chốt mapping trạng thái chi tiết, nên các màn Lazada chỉ mô tả ở mức connection, sync, status, error, log, retry, resync. |
| RULE-SITEMAP-011 | Payment logic do backend/API và VNPay xử lý; frontend chỉ hiển thị trạng thái thanh toán đã được hệ thống xác minh.                       |
| RULE-SITEMAP-012 | Export report phải cho phép chọn khoảng thời gian và cột cần xuất.                                                                        |
| RULE-SITEMAP-013 | Inventory adjustment threshold phải cấu hình được theo shop, không hard-code cố định.                                                     |

---

# 4. Route Naming Convention

## 4.1 General Rules

| Rule            | Description                                     |
| --------------- | ----------------------------------------------- |
| Auth routes     | Không nằm trong `/app` hoặc `/platform`.        |
| Seller routes   | Bắt đầu bằng `/app`.                            |
| Platform routes | Bắt đầu bằng `/platform`.                       |
| Detail routes   | Dùng `/:id` hoặc tên định danh rõ ràng.         |
| Create routes   | Dùng `/new`.                                    |
| Edit routes     | Dùng `/:id/edit`.                               |
| Settings routes | Gom vào `/settings` theo scope seller/platform. |

## 4.2 Route Examples

| Screen Type       | Route Pattern              | Example                         |
| ----------------- | -------------------------- | ------------------------------- |
| Auth              | `/login`                   | `/login`                        |
| Accept Invitation | `/accept-invitation`       | `/accept-invitation?token=...`  |
| Seller Dashboard  | `/app/dashboard`           | `/app/dashboard`                |
| Seller List       | `/app/{resource}`          | `/app/products`                 |
| Seller Detail     | `/app/{resource}/:id`      | `/app/orders/:orderId`          |
| Seller Create     | `/app/{resource}/new`      | `/app/products/new`             |
| Seller Edit       | `/app/{resource}/:id/edit` | `/app/products/:productId/edit` |
| Platform List     | `/platform/{resource}`     | `/platform/shops`               |
| Platform Detail   | `/platform/{resource}/:id` | `/platform/shops/:shopId`       |
| Platform Settings | `/platform/settings`       | `/platform/settings`            |

---

# 5. High-level Sitemap

## 5.1 Seller-side Sitemap

```txt
Seller App (/app)
├── Auth
│   ├── Login
│   ├── Forgot Password
│   ├── Reset Password
│   └── Accept Invitation + Email OTP
├── Dashboard
├── Products
│   ├── Product List
│   ├── Product Detail
│   ├── Create Product
│   └── Edit Product
├── Categories
│   ├── Category List
│   └── Create/Edit Category
├── Orders
│   ├── Order List
│   └── Order Detail
├── Customers
│   ├── Customer List
│   ├── Customer Detail
│   ├── Customer Ticket List
│   └── Customer Ticket Detail
├── Inventory
│   ├── Inventory Overview
│   ├── Stock In
│   ├── Adjustment Request
│   ├── Adjustment Approval
│   └── Low Stock Alerts
├── Channels
│   ├── Channel List
│   ├── Lazada Connection
│   └── Lazada Channel Detail
├── Sync Center
│   ├── Sync Dashboard
│   ├── Sync Log
│   ├── Sync Error Detail
│   └── Manual Resync
├── Shipping
│   └── Shipment Detail
├── Reports
│   ├── Reports Overview
│   ├── Revenue Report
│   ├── Profit Report
│   ├── Inventory Report
│   ├── Channel Report
│   └── Export Report
├── Subscription & Billing
│   ├── Current Plan
│   ├── Upgrade Plan
│   ├── Downgrade Plan
│   ├── VNPay QR Payment
│   ├── Payment Result
│   └── Invoice History
├── Users & Roles
│   ├── Shop User List
│   ├── Invite Shop User
│   └── Shop User Detail
├── Settings
│   ├── Shop Profile Settings
│   ├── Profit Formula Settings
│   ├── Inventory Settings
│   └── Channel Settings
├── Audit Log
└── Notifications
```

## 5.2 Platform-side Sitemap

```txt
Platform App (/platform)
├── Platform Dashboard
├── Shop Management
│   ├── Shop List
│   ├── Shop Detail
│   └── Suspend / Restore Shop
├── Platform Users
│   ├── Platform User List
│   └── Invite Platform User
├── Plans & Subscriptions
│   ├── Plan Management
│   ├── Subscription Management
│   └── Payment / Invoice Records
├── Integration Monitoring
│   ├── Lazada Integration Monitoring
│   ├── Sync Error Monitoring
│   └── Manual Resync Review
├── Support
│   └── Support Shop Lookup
├── Audit Log
└── Platform Settings
```

---

# 6. Auth Screens

| Screen ID                  | Screen Name       | Route                          | Module ID          | Related Feature IDs                                | Related Flow IDs         | Allowed Roles   | Purpose                                                                                                | Priority    | Status |
| -------------------------- | ----------------- | ------------------------------ | ------------------ | -------------------------------------------------- | ------------------------ | --------------- | ------------------------------------------------------------------------------------------------------ | ----------- | ------ |
| SCR-AUTH-LOGIN             | Login             | `/login`                       | MOD-AUTH           | FEAT-AUTH-LOGIN, FEAT-AUTH-SESSION, FEAT-AUTH-RBAC | FLOW-AUTH-LOGIN-SESSION  | All login roles | Đăng nhập vào ShopHub theo seller/platform scope.                                                      | Must-have   | Draft  |
| SCR-AUTH-FORGOT-PASSWORD   | Forgot Password   | `/forgot-password`             | MOD-AUTH           | FEAT-AUTH-FORGOT-PASSWORD                          | FLOW-AUTH-RESET-PASSWORD | All login roles | Gửi yêu cầu đặt lại mật khẩu.                                                                          | Should-have | Draft  |
| SCR-AUTH-RESET-PASSWORD    | Reset Password    | `/reset-password`              | MOD-AUTH           | FEAT-AUTH-RESET-PASSWORD                           | FLOW-AUTH-RESET-PASSWORD | All login roles | Đặt lại mật khẩu qua token hợp lệ.                                                                     | Should-have | Draft  |
| SCR-AUTH-ACCEPT-INVITATION | Accept Invitation | `/accept-invitation?token=...` | MOD-AUTH, MOD-USER | FEAT-USER-ACCEPT-INVITE, FEAT-AUTH-LOGIN           | FLOW-USER-ACCEPT-INVITE  | Invited User    | User được mời mở email invitation, nhập OTP gửi qua email, sau đó chấp nhận lời mời vào shop/platform. | Must-have   | Draft  |

## 6.1 Accept Invitation Flow Direction

| Step | Description                                               |
| ---- | --------------------------------------------------------- |
| 1    | Shop Owner hoặc Platform Admin mời user bằng email.       |
| 2    | Hệ thống gửi email invitation đến email đăng ký.          |
| 3    | User bấm link trong email.                                |
| 4    | User nhập mã OTP được gửi qua email.                      |
| 5    | Hệ thống xác thực OTP.                                    |
| 6    | User tạo mật khẩu mới hoặc đăng nhập nếu đã có tài khoản. |
| 7    | Hệ thống gắn user vào shop/platform với role đã được mời. |

---

# 7. Seller-side Screen List

| Screen ID                         | Screen Name                   | Route                                                     | Module ID                       | Related Feature IDs                                                                                                                                    | Related Flow IDs                                                                                | Allowed Roles                                                               | Purpose                                                                     | Priority    | Status |
| --------------------------------- | ----------------------------- | --------------------------------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------- | ----------- | ------ |
| SCR-SELLER-DASHBOARD              | Seller Dashboard              | `/app/dashboard`                                          | MOD-DASHBOARD                   | FEAT-DASHBOARD-SELLER-OVERVIEW, FEAT-DASHBOARD-ORDER-SUMMARY, FEAT-DASHBOARD-INVENTORY-ALERT, FEAT-DASHBOARD-SYNC-ALERT, FEAT-DASHBOARD-PROFIT-SUMMARY | FLOW-DASHBOARD-SELLER-VIEW                                                                      | Shop Owner, Shop Manager, Accountant, Order Staff, Warehouse Staff          | Xem tổng quan vận hành và kinh doanh của shop.                              | Must-have   | Draft  |
| SCR-PRODUCT-LIST                  | Product List                  | `/app/products`                                           | MOD-PRODUCT                     | FEAT-PRODUCT-LIST                                                                                                                                      | FLOW-PRODUCT-UPDATE, FLOW-PRODUCT-DEACTIVATE                                                    | Shop Owner, Shop Manager, Product Staff, Shop Viewer                        | Xem, tìm kiếm, lọc sản phẩm.                                                | Must-have   | Draft  |
| SCR-PRODUCT-DETAIL                | Product Detail                | `/app/products/:productId`                                | MOD-PRODUCT                     | FEAT-PRODUCT-DETAIL, FEAT-PRODUCT-LAZADA-SYNC-STATUS                                                                                                   | FLOW-PRODUCT-UPDATE, FLOW-PRODUCT-UPDATE-STATUS                                                 | Shop Owner, Shop Manager, Product Staff, Accountant                         | Xem chi tiết sản phẩm, trạng thái, tồn kho và sync.                         | Must-have   | Draft  |
| SCR-PRODUCT-CREATE                | Create Product                | `/app/products/new`                                       | MOD-PRODUCT                     | FEAT-PRODUCT-CREATE, FEAT-PRODUCT-SKU, FEAT-PRODUCT-PRICE, FEAT-PRODUCT-COST                                                                           | FLOW-PRODUCT-CREATE                                                                             | Shop Owner, Shop Manager, Product Staff                                     | Tạo sản phẩm mới.                                                           | Must-have   | Draft  |
| SCR-PRODUCT-EDIT                  | Edit Product                  | `/app/products/:productId/edit`                           | MOD-PRODUCT                     | FEAT-PRODUCT-UPDATE, FEAT-PRODUCT-PRICE, FEAT-PRODUCT-COST, FEAT-PRODUCT-STATUS                                                                        | FLOW-PRODUCT-UPDATE, FLOW-PRODUCT-UPDATE-STATUS                                                 | Shop Owner, Shop Manager, Product Staff, Accountant limited                 | Cập nhật thông tin sản phẩm.                                                | Must-have   | Draft  |
| SCR-CATEGORY-LIST                 | Category List                 | `/app/categories`                                         | MOD-CATEGORY                    | FEAT-CATEGORY-LIST, FEAT-CATEGORY-CREATE, FEAT-CATEGORY-UPDATE, FEAT-CATEGORY-DEACTIVATE                                                               | FLOW-CATEGORY-MANAGE                                                                            | Shop Owner, Shop Manager, Product Staff                                     | Quản lý danh mục sản phẩm.                                                  | Must-have   | Draft  |
| SCR-CATEGORY-FORM                 | Category Create/Edit          | `/app/categories/new`, `/app/categories/:categoryId/edit` | MOD-CATEGORY                    | FEAT-CATEGORY-CREATE, FEAT-CATEGORY-UPDATE, FEAT-CATEGORY-LAZADA-MAPPING                                                                               | FLOW-CATEGORY-MANAGE                                                                            | Shop Owner, Shop Manager, Product Staff                                     | Tạo/sửa danh mục và mapping nếu cần.                                        | Must-have   | Draft  |
| SCR-ORDER-LIST                    | Order List                    | `/app/orders`                                             | MOD-ORDER                       | FEAT-ORDER-LIST, FEAT-ORDER-FILTER, FEAT-ORDER-MARKETPLACE-STATUS, FEAT-ORDER-NORMALIZED-STATUS                                                        | FLOW-ORDER-REVIEW-CONFIRM, FLOW-SYNC-LAZADA-ORDER                                               | Shop Owner, Shop Manager, Order Staff, Customer Support, Accountant limited | Xem và lọc danh sách đơn hàng.                                              | Must-have   | Draft  |
| SCR-ORDER-DETAIL                  | Order Detail                  | `/app/orders/:orderId`                                    | MOD-ORDER                       | FEAT-ORDER-DETAIL, FEAT-ORDER-UPDATE-STATUS, FEAT-ORDER-CANCEL, FEAT-ORDER-NOTES, FEAT-ORDER-AUDIT                                                     | FLOW-ORDER-REVIEW-CONFIRM, FLOW-ORDER-CANCEL, FLOW-ORDER-SHIPMENT                               | Shop Owner, Shop Manager, Order Staff, Customer Support, Accountant limited | Xem và xử lý chi tiết đơn hàng.                                             | Must-have   | Draft  |
| SCR-CUSTOMER-LIST                 | Customer List                 | `/app/customers`                                          | MOD-CUSTOMER                    | FEAT-CUSTOMER-LIST                                                                                                                                     | FLOW-CUSTOMER-VIEW-MANAGE                                                                       | Shop Owner, Shop Manager, Customer Support, Order Staff, Accountant         | Xem và tìm kiếm khách hàng của shop.                                        | Must-have   | Draft  |
| SCR-CUSTOMER-DETAIL               | Customer Detail               | `/app/customers/:customerId`                              | MOD-CUSTOMER                    | FEAT-CUSTOMER-DETAIL, FEAT-CUSTOMER-ORDER-HISTORY, FEAT-CUSTOMER-NOTES                                                                                 | FLOW-CUSTOMER-VIEW-MANAGE                                                                       | Shop Owner, Shop Manager, Customer Support, Order Staff                     | Xem thông tin khách và lịch sử đơn hàng.                                    | Must-have   | Draft  |
| SCR-CUSTOMER-TICKET-LIST          | Customer Ticket List          | `/app/customer-tickets`                                   | MOD-CUSTOMER                    | FEAT-CUSTOMER-ISSUE-TRACKING                                                                                                                           | FLOW-CUSTOMER-CARE-TICKET                                                                       | Shop Owner, Shop Manager, Customer Support, Order Staff                     | Xem danh sách issue/ticket CSKH.                                            | Should-have | Draft  |
| SCR-CUSTOMER-TICKET-DETAIL        | Customer Ticket Detail        | `/app/customer-tickets/:ticketId`                         | MOD-CUSTOMER                    | FEAT-CUSTOMER-ISSUE-TRACKING, FEAT-CUSTOMER-NOTES                                                                                                      | FLOW-CUSTOMER-CARE-TICKET                                                                       | Shop Owner, Shop Manager, Customer Support, Order Staff                     | Xử lý và theo dõi ticket CSKH.                                              | Should-have | Draft  |
| SCR-INVENTORY-OVERVIEW            | Inventory Overview            | `/app/inventory`                                          | MOD-INVENTORY                   | FEAT-INVENTORY-VIEW, FEAT-INVENTORY-PHYSICAL-STOCK, FEAT-INVENTORY-AVAILABLE-STOCK, FEAT-INVENTORY-RESERVED-STOCK, FEAT-INVENTORY-LOG                  | FLOW-INVENTORY-STOCK-IN, FLOW-INVENTORY-ADJUSTMENT-APPROVAL                                     | Shop Owner, Shop Manager, Warehouse Staff, Product Staff                    | Xem tồn kho và lịch sử biến động.                                           | Must-have   | Draft  |
| SCR-INVENTORY-STOCK-IN            | Stock In                      | `/app/inventory/stock-in`                                 | MOD-INVENTORY                   | FEAT-INVENTORY-STOCK-IN                                                                                                                                | FLOW-INVENTORY-STOCK-IN                                                                         | Shop Owner, Shop Manager, Warehouse Staff                                   | Nhập kho.                                                                   | Must-have   | Draft  |
| SCR-INVENTORY-ADJUSTMENT-REQUEST  | Inventory Adjustment Request  | `/app/inventory/adjustments/new`                          | MOD-INVENTORY                   | FEAT-INVENTORY-ADJUSTMENT-REQUEST                                                                                                                      | FLOW-INVENTORY-ADJUSTMENT-APPROVAL                                                              | Warehouse Staff                                                             | Tạo yêu cầu điều chỉnh tồn kho có lý do/bằng chứng.                         | Must-have   | Draft  |
| SCR-INVENTORY-ADJUSTMENT-APPROVAL | Inventory Adjustment Approval | `/app/inventory/adjustments/:adjustmentId`                | MOD-INVENTORY                   | FEAT-INVENTORY-ADJUSTMENT-APPROVAL                                                                                                                     | FLOW-INVENTORY-ADJUSTMENT-APPROVAL                                                              | Shop Owner, Shop Manager                                                    | Duyệt/từ chối điều chỉnh tồn kho.                                           | Must-have   | Draft  |
| SCR-INVENTORY-LOW-STOCK           | Low Stock Alerts              | `/app/inventory/low-stock`                                | MOD-INVENTORY, MOD-NOTIFICATION | FEAT-INVENTORY-LOW-STOCK-ALERT, FEAT-NOTI-LOW-STOCK                                                                                                    | FLOW-INVENTORY-LOW-STOCK-ALERT                                                                  | Shop Owner, Shop Manager, Warehouse Staff                                   | Xem cảnh báo hàng sắp hết.                                                  | Should-have | Draft  |
| SCR-CHANNEL-LIST                  | Channel List                  | `/app/channels`                                           | MOD-CHANNEL                     | FEAT-CHANNEL-LIST, FEAT-CHANNEL-CONNECTION-STATUS                                                                                                      | FLOW-CHANNEL-CONNECT-LAZADA, FLOW-CHANNEL-DISCONNECT-LAZADA                                     | Shop Owner, Shop Manager                                                    | Xem danh sách kênh bán đã/kế hoạch kết nối.                                 | Must-have   | Draft  |
| SCR-LAZADA-CONNECTION             | Lazada Connection             | `/app/channels/lazada/connect`                            | MOD-CHANNEL                     | FEAT-CHANNEL-LAZADA-CONNECT, FEAT-CHANNEL-CONFIG                                                                                                       | FLOW-CHANNEL-CONNECT-LAZADA                                                                     | Shop Owner, Shop Manager                                                    | Kết nối Lazada.                                                             | Must-have   | Draft  |
| SCR-LAZADA-CHANNEL-DETAIL         | Lazada Channel Detail         | `/app/channels/lazada`                                    | MOD-CHANNEL                     | FEAT-CHANNEL-CONNECTION-STATUS, FEAT-CHANNEL-LAZADA-DISCONNECT                                                                                         | FLOW-CHANNEL-CONNECT-LAZADA, FLOW-CHANNEL-DISCONNECT-LAZADA                                     | Shop Owner, Shop Manager                                                    | Xem trạng thái kết nối Lazada và ngắt kết nối nếu cần.                      | Should-have | Draft  |
| SCR-SYNC-DASHBOARD                | Sync Dashboard                | `/app/sync`                                               | MOD-SYNC                        | FEAT-SYNC-LOG, FEAT-SYNC-ERROR                                                                                                                         | FLOW-SYNC-ERROR-RETRY, FLOW-SYNC-MANUAL-RESYNC                                                  | Shop Owner, Shop Manager, Product Staff                                     | Xem tổng quan đồng bộ và lỗi sync.                                          | Must-have   | Draft  |
| SCR-SYNC-LOG                      | Sync Log                      | `/app/sync/logs`                                          | MOD-SYNC                        | FEAT-SYNC-LOG                                                                                                                                          | FLOW-SYNC-LAZADA-ORDER, FLOW-SYNC-LAZADA-STOCK, FLOW-SYNC-LAZADA-PRODUCT, FLOW-SYNC-LAZADA-FEES | Shop Owner, Shop Manager, Product Staff                                     | Xem lịch sử đồng bộ.                                                        | Must-have   | Draft  |
| SCR-SYNC-ERROR-DETAIL             | Sync Error Detail             | `/app/sync/errors/:syncErrorId`                           | MOD-SYNC                        | FEAT-SYNC-ERROR, FEAT-SYNC-RETRY                                                                                                                       | FLOW-SYNC-ERROR-RETRY                                                                           | Shop Owner, Shop Manager, Product Staff                                     | Xem lỗi sync và hướng xử lý.                                                | Must-have   | Draft  |
| SCR-MANUAL-RESYNC                 | Manual Resync                 | `/app/sync/manual-resync`                                 | MOD-SYNC                        | FEAT-SYNC-MANUAL-RESYNC-APPROVAL                                                                                                                       | FLOW-SYNC-MANUAL-RESYNC                                                                         | Shop Owner, Shop Manager limited                                            | Yêu cầu hoặc xác nhận manual resync dữ liệu quan trọng.                     | Should-have | Draft  |
| SCR-SHIPMENT-DETAIL               | Shipment Detail               | `/app/shipments/:shipmentId`                              | MOD-SHIPPING                    | FEAT-SHIPPING-STATUS, FEAT-SHIPPING-TRACKING, FEAT-SHIPPING-MAP-ORDER-STATUS                                                                           | FLOW-ORDER-SHIPMENT                                                                             | Shop Owner, Shop Manager, Order Staff, Warehouse Staff                      | Xem/tracking trạng thái giao hàng và xuất kho.                              | Should-have | Draft  |
| SCR-REPORTS-OVERVIEW              | Reports Overview              | `/app/reports`                                            | MOD-REPORT                      | FEAT-REPORT-REVENUE, FEAT-REPORT-PROFIT, FEAT-REPORT-ORDER, FEAT-REPORT-PRODUCT, FEAT-REPORT-INVENTORY, FEAT-REPORT-CHANNEL                            | FLOW-REPORT-VIEW                                                                                | Shop Owner, Shop Manager, Accountant                                        | Xem danh sách báo cáo.                                                      | Must-have   | Draft  |
| SCR-REPORT-REVENUE                | Revenue Report                | `/app/reports/revenue`                                    | MOD-REPORT                      | FEAT-REPORT-REVENUE                                                                                                                                    | FLOW-REPORT-VIEW, FLOW-REPORT-EXPORT                                                            | Shop Owner, Shop Manager, Accountant                                        | Báo cáo doanh thu.                                                          | Must-have   | Draft  |
| SCR-REPORT-PROFIT                 | Profit Report                 | `/app/reports/profit`                                     | MOD-REPORT                      | FEAT-REPORT-PROFIT, FEAT-REPORT-DATA-QUALITY                                                                                                           | FLOW-REPORT-VIEW, FLOW-PROFIT-FORMULA-APPROVAL                                                  | Shop Owner, Accountant                                                      | Báo cáo lợi nhuận và cảnh báo dữ liệu thiếu.                                | Must-have   | Draft  |
| SCR-REPORT-INVENTORY              | Inventory Report              | `/app/reports/inventory`                                  | MOD-REPORT                      | FEAT-REPORT-INVENTORY                                                                                                                                  | FLOW-REPORT-VIEW                                                                                | Shop Owner, Shop Manager, Warehouse Staff                                   | Báo cáo tồn kho.                                                            | Should-have | Draft  |
| SCR-REPORT-CHANNEL                | Channel Report                | `/app/reports/channels`                                   | MOD-REPORT                      | FEAT-REPORT-CHANNEL                                                                                                                                    | FLOW-REPORT-VIEW                                                                                | Shop Owner, Shop Manager                                                    | Báo cáo hiệu suất kênh bán.                                                 | Should-have | Draft  |
| SCR-REPORT-EXPORT                 | Export Report                 | `/app/reports/export`                                     | MOD-REPORT                      | FEAT-REPORT-EXPORT                                                                                                                                     | FLOW-REPORT-EXPORT                                                                              | Shop Owner, Shop Manager, Accountant                                        | Export báo cáo Excel/PDF, cho phép chọn time range và cột cần xuất.         | Should-have | Draft  |
| SCR-PROFIT-FORMULA-SETTINGS       | Profit Formula Settings       | `/app/settings/profit-formula`                            | MOD-REPORT, MOD-SETTING         | FEAT-REPORT-PROFIT-FORMULA, FEAT-SETTING-PROFIT-FORMULA                                                                                                | FLOW-PROFIT-FORMULA-APPROVAL, FLOW-SETTING-MANAGE                                               | Shop Owner, Accountant                                                      | Cấu hình công thức lợi nhuận.                                               | Must-have   | Draft  |
| SCR-PROFIT-FORMULA-APPROVAL       | Profit Formula Approval       | `/app/approvals/profit-formula/:approvalId`               | MOD-REPORT                      | FEAT-REPORT-PROFIT-FORMULA-APPROVAL                                                                                                                    | FLOW-PROFIT-FORMULA-APPROVAL                                                                    | Shop Owner, Shop Manager                                                    | Duyệt thay đổi công thức lợi nhuận.                                         | Must-have   | Draft  |
| SCR-SUBSCRIPTION-CURRENT-PLAN     | Current Plan                  | `/app/subscription`                                       | MOD-SUBSCRIPTION                | FEAT-SUBSCRIPTION-VIEW-PLAN, FEAT-SUBSCRIPTION-PLAN-LIMITS                                                                                             | FLOW-SUBSCRIPTION-UPGRADE-PAYMENT, FLOW-SUBSCRIPTION-DOWNGRADE                                  | Shop Owner                                                                  | Xem gói hiện tại và giới hạn.                                               | Must-have   | Draft  |
| SCR-SUBSCRIPTION-UPGRADE          | Upgrade Plan                  | `/app/subscription/upgrade`                               | MOD-SUBSCRIPTION                | FEAT-SUBSCRIPTION-UPGRADE, FEAT-SUBSCRIPTION-PLAN-LIMITS                                                                                               | FLOW-SUBSCRIPTION-UPGRADE-PAYMENT                                                               | Shop Owner                                                                  | Nâng gói dịch vụ.                                                           | Must-have   | Draft  |
| SCR-SUBSCRIPTION-DOWNGRADE        | Downgrade Plan                | `/app/subscription/downgrade`                             | MOD-SUBSCRIPTION                | FEAT-SUBSCRIPTION-DOWNGRADE, FEAT-SUBSCRIPTION-PLAN-LIMITS                                                                                             | FLOW-SUBSCRIPTION-DOWNGRADE                                                                     | Shop Owner                                                                  | Hạ gói dịch vụ.                                                             | Should-have | Draft  |
| SCR-PAYMENT-VNPAY-QR              | VNPay QR Payment              | `/app/subscription/payment/vnpay-qr`                      | MOD-SUBSCRIPTION                | FEAT-SUBSCRIPTION-PAYMENT                                                                                                                              | FLOW-SUBSCRIPTION-UPGRADE-PAYMENT                                                               | Shop Owner                                                                  | Thanh toán subscription qua VNPay QR.                                       | Must-have   | Draft  |
| SCR-PAYMENT-RESULT                | Payment Result                | `/app/subscription/payment/result`                        | MOD-SUBSCRIPTION                | FEAT-SUBSCRIPTION-PAYMENT, FEAT-SUBSCRIPTION-STATUS                                                                                                    | FLOW-SUBSCRIPTION-UPGRADE-PAYMENT                                                               | Shop Owner                                                                  | Hiển thị kết quả thanh toán: Success, Failed, Pending, Expired, NeedReview. | Must-have   | Draft  |
| SCR-INVOICE-HISTORY               | Invoice History               | `/app/subscription/invoices`                              | MOD-SUBSCRIPTION                | FEAT-SUBSCRIPTION-INVOICE                                                                                                                              | FLOW-SUBSCRIPTION-INVOICE-RECORD                                                                | Shop Owner                                                                  | Xem lịch sử hóa đơn/thanh toán.                                             | Should-have | Draft  |
| SCR-SHOP-USER-LIST                | Shop User List                | `/app/users`                                              | MOD-USER                        | FEAT-USER-LIST-SHOP-USERS, FEAT-USER-DEACTIVATE-SHOP-USER                                                                                              | FLOW-USER-INVITE-SHOP-USER                                                                      | Shop Owner                                                                  | Xem user trong shop.                                                        | Must-have   | Draft  |
| SCR-INVITE-SHOP-USER              | Invite Shop User              | `/app/users/invite`                                       | MOD-USER                        | FEAT-USER-INVITE-SHOP-USER, FEAT-USER-ASSIGN-ROLE                                                                                                      | FLOW-USER-INVITE-SHOP-USER                                                                      | Shop Owner                                                                  | Mời user vào shop.                                                          | Must-have   | Draft  |
| SCR-SHOP-USER-DETAIL              | Shop User Detail              | `/app/users/:userId`                                      | MOD-USER                        | FEAT-USER-ASSIGN-ROLE, FEAT-USER-REMOVE-ROLE, FEAT-USER-DEACTIVATE-SHOP-USER                                                                           | FLOW-USER-INVITE-SHOP-USER                                                                      | Shop Owner                                                                  | Xem/gán/gỡ role hoặc khóa user.                                             | Must-have   | Draft  |
| SCR-SHOP-SETTINGS                 | Shop Settings                 | `/app/settings`                                           | MOD-SETTING                     | FEAT-SETTING-SHOP-PROFILE, FEAT-SETTING-INVENTORY, FEAT-SETTING-CHANNEL                                                                                | FLOW-SETTING-MANAGE                                                                             | Shop Owner, Shop Manager limited                                            | Cấu hình shop, bao gồm ngưỡng duyệt tồn kho có thể cấu hình theo shop.      | Should-have | Draft  |
| SCR-SELLER-AUDIT-LOG              | Seller Audit Log              | `/app/audit-logs`                                         | MOD-AUDIT                       | FEAT-AUDIT-ORDER, FEAT-AUDIT-INVENTORY, FEAT-AUDIT-USER-ROLE, FEAT-AUDIT-SUBSCRIPTION, FEAT-AUDIT-SYNC, FEAT-AUDIT-REPORT-FORMULA                      | FLOW-AUDIT-VIEW                                                                                 | Shop Owner, Shop Manager limited, Accountant limited                        | Xem audit log trong phạm vi shop.                                           | Must-have   | Draft  |
| SCR-SELLER-NOTIFICATIONS          | Notifications                 | `/app/notifications`                                      | MOD-NOTIFICATION                | FEAT-NOTI-TOAST, FEAT-NOTI-SYNC-ERROR, FEAT-NOTI-LOW-STOCK, FEAT-NOTI-APPROVAL, FEAT-NOTI-SUBSCRIPTION                                                 | FLOW-NOTIFICATION-HANDLE                                                                        | All seller-side roles                                                       | Xem thông báo hệ thống.                                                     | Should-have | Draft  |

---

# 8. Platform-side Screen List

| Screen ID                                  | Screen Name                   | Route                                     | Module ID             | Related Feature IDs                                                     | Related Flow IDs                                                    | Allowed Roles                                                        | Purpose                                                      | Priority    | Status |
| ------------------------------------------ | ----------------------------- | ----------------------------------------- | --------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------ | ----------- | ------ |
| SCR-PLATFORM-DASHBOARD                     | Platform Dashboard            | `/platform/dashboard`                     | MOD-DASHBOARD         | FEAT-DASHBOARD-PLATFORM-OVERVIEW                                        | FLOW-DASHBOARD-PLATFORM-VIEW                                        | ShopHub Super Admin, ShopHub Admin, Billing Admin, Integration Admin | Xem tổng quan hệ thống ShopHub.                              | Should-have | Draft  |
| SCR-PLATFORM-SHOP-LIST                     | Shop List                     | `/platform/shops`                         | MOD-TENANT            | FEAT-TENANT-VIEW-SHOP, FEAT-TENANT-SHOP-STATUS                          | FLOW-TENANT-ONBOARD-SHOP, FLOW-TENANT-SHOP-SUSPEND-RESTORE          | ShopHub Super Admin, ShopHub Admin, Support                          | Xem danh sách shop/tenant.                                   | Must-have   | Draft  |
| SCR-PLATFORM-SHOP-DETAIL                   | Shop Detail                   | `/platform/shops/:shopId`                 | MOD-TENANT            | FEAT-TENANT-VIEW-SHOP, FEAT-TENANT-UPDATE-SHOP, FEAT-TENANT-SHOP-STATUS | FLOW-TENANT-SHOP-SUSPEND-RESTORE                                    | ShopHub Super Admin, ShopHub Admin, Support, Billing Admin limited   | Xem chi tiết shop, trạng thái, gói, sync summary.            | Must-have   | Draft  |
| SCR-PLATFORM-SHOP-SUSPEND-RESTORE          | Suspend / Restore Shop        | `/platform/shops/:shopId/status`          | MOD-TENANT            | FEAT-TENANT-SHOP-STATUS                                                 | FLOW-TENANT-SHOP-SUSPEND-RESTORE                                    | ShopHub Super Admin, ShopHub Admin, Billing Admin billing-related    | Tạm khóa hoặc khôi phục shop.                                | Must-have   | Draft  |
| SCR-PLATFORM-USER-LIST                     | Platform User List            | `/platform/users`                         | MOD-USER              | FEAT-USER-MANAGE-PLATFORM-USER                                          | FLOW-USER-PLATFORM-INVITE                                           | ShopHub Super Admin, ShopHub Admin                                   | Xem và quản lý platform users.                               | Must-have   | Draft  |
| SCR-PLATFORM-INVITE-USER                   | Invite Platform User          | `/platform/users/invite`                  | MOD-USER              | FEAT-USER-INVITE-PLATFORM-USER                                          | FLOW-USER-PLATFORM-INVITE                                           | ShopHub Super Admin, ShopHub Admin                                   | Mời user vào hệ thống ShopHub platform.                      | Must-have   | Draft  |
| SCR-PLATFORM-PLAN-MANAGEMENT               | Plan Management               | `/platform/plans`                         | MOD-SUBSCRIPTION      | FEAT-SUBSCRIPTION-PLAN-MGMT, FEAT-SUBSCRIPTION-PLAN-LIMITS              | FLOW-SUBSCRIPTION-PLAN-MANAGE                                       | ShopHub Super Admin, Billing Admin                                   | Quản lý định nghĩa gói Free/Trial/Pro/Premium.               | Should-have | Draft  |
| SCR-PLATFORM-SUBSCRIPTION-MANAGEMENT       | Subscription Management       | `/platform/subscriptions`                 | MOD-SUBSCRIPTION      | FEAT-SUBSCRIPTION-STATUS, FEAT-SUBSCRIPTION-PLAN-LIMITS                 | FLOW-SUBSCRIPTION-PASTDUE-SUSPEND                                   | ShopHub Super Admin, Billing Admin                                   | Theo dõi subscription của các shop.                          | Must-have   | Draft  |
| SCR-PLATFORM-PAYMENT-INVOICE-RECORDS       | Payment / Invoice Records     | `/platform/payments`                      | MOD-SUBSCRIPTION      | FEAT-SUBSCRIPTION-INVOICE, FEAT-SUBSCRIPTION-PAYMENT                    | FLOW-SUBSCRIPTION-INVOICE-RECORD, FLOW-SUBSCRIPTION-UPGRADE-PAYMENT | Billing Admin, ShopHub Super Admin                                   | Xem payment/invoice subscription.                            | Must-have   | Draft  |
| SCR-PLATFORM-LAZADA-INTEGRATION-MONITORING | Lazada Integration Monitoring | `/platform/integrations/lazada`           | MOD-CHANNEL, MOD-SYNC | FEAT-CHANNEL-CONNECTION-STATUS, FEAT-SYNC-LOG, FEAT-SYNC-ERROR          | FLOW-CHANNEL-CONNECT-LAZADA, FLOW-SYNC-ERROR-RETRY                  | Integration Admin, ShopHub Admin, Support limited                    | Giám sát kết nối Lazada toàn hệ thống.                       | Must-have   | Draft  |
| SCR-PLATFORM-SYNC-ERROR-MONITORING         | Sync Error Monitoring         | `/platform/sync/errors`                   | MOD-SYNC              | FEAT-SYNC-ERROR, FEAT-SYNC-RETRY, FEAT-SYNC-LOG                         | FLOW-SYNC-ERROR-RETRY, FLOW-SYNC-MANUAL-RESYNC                      | Integration Admin, ShopHub Admin, Support limited                    | Giám sát lỗi đồng bộ.                                        | Must-have   | Draft  |
| SCR-PLATFORM-MANUAL-RESYNC-REVIEW          | Manual Resync Review          | `/platform/sync/manual-resync/:requestId` | MOD-SYNC              | FEAT-SYNC-MANUAL-RESYNC-APPROVAL                                        | FLOW-SYNC-MANUAL-RESYNC                                             | Integration Admin, Shop Owner via seller confirmation                | Xem/đánh giá yêu cầu resync có ảnh hưởng dữ liệu quan trọng. | Should-have | Draft  |
| SCR-PLATFORM-SUPPORT-SHOP-LOOKUP           | Support Shop Lookup           | `/platform/support/shops`                 | MOD-TENANT, MOD-SYNC  | FEAT-TENANT-VIEW-SHOP, FEAT-SYNC-ERROR                                  | FLOW-DASHBOARD-PLATFORM-VIEW                                        | ShopHub Support, ShopHub Admin                                       | Tra cứu shop để hỗ trợ, không impersonate.                   | Must-have   | Draft  |
| SCR-PLATFORM-AUDIT-LOG                     | Platform Audit Log            | `/platform/audit-logs`                    | MOD-AUDIT             | FEAT-AUDIT-USER-ROLE, FEAT-AUDIT-SUBSCRIPTION, FEAT-AUDIT-SYNC          | FLOW-AUDIT-VIEW                                                     | ShopHub Super Admin, ShopHub Admin, Platform Auditor                 | Xem audit log cấp platform.                                  | Must-have   | Draft  |
| SCR-PLATFORM-SETTINGS                      | Platform Settings             | `/platform/settings`                      | MOD-SETTING           | FEAT-SETTING-PLATFORM                                                   | FLOW-SETTING-MANAGE                                                 | ShopHub Super Admin, ShopHub Admin                                   | Cấu hình platform.                                           | Should-have | Draft  |

---

# 9. Platform Dashboard Widget Requirements

| Widget ID                           | Widget Name              | Purpose                                         | Visible To                                             | Priority    |
| ----------------------------------- | ------------------------ | ----------------------------------------------- | ------------------------------------------------------ | ----------- |
| WDG-PLATFORM-TOTAL-SHOPS            | Total Shops              | Tổng số shop trên hệ thống.                     | Super Admin, Admin                                     | Should-have |
| WDG-PLATFORM-ACTIVE-SHOPS           | Active Shops             | Số shop đang hoạt động.                         | Super Admin, Admin                                     | Should-have |
| WDG-PLATFORM-SUSPENDED-SHOPS        | Suspended Shops          | Số shop bị tạm khóa.                            | Super Admin, Admin, Billing Admin limited              | Should-have |
| WDG-PLATFORM-NEW-SHOPS              | New Shops This Month     | Số shop mới trong tháng.                        | Super Admin, Admin                                     | Should-have |
| WDG-PLATFORM-SUBSCRIPTION-SUMMARY   | Subscription Summary     | Tổng quan Trial / Active / PastDue / Suspended. | Super Admin, Admin, Billing Admin                      | Should-have |
| WDG-PLATFORM-PAYMENT-PENDING-FAILED | Payment Pending / Failed | Giao dịch thanh toán đang chờ hoặc lỗi.         | Super Admin, Billing Admin                             | Should-have |
| WDG-PLATFORM-PAST-DUE-SHOPS         | Past Due Shops           | Shop quá hạn thanh toán.                        | Super Admin, Billing Admin                             | Should-have |
| WDG-PLATFORM-LAZADA-CONNECTED       | Lazada Connected Shops   | Số shop đã kết nối Lazada.                      | Super Admin, Admin, Integration Admin                  | Should-have |
| WDG-PLATFORM-SYNC-ERROR-SUMMARY     | Sync Error Summary       | Tổng quan lỗi đồng bộ.                          | Super Admin, Admin, Integration Admin, Support limited | Should-have |
| WDG-PLATFORM-CRITICAL-SYNC          | Critical Sync Errors     | Lỗi đồng bộ nghiêm trọng cần xử lý.             | Integration Admin, Super Admin                         | Should-have |
| WDG-PLATFORM-RESYNC-REQUESTS        | Manual Resync Requests   | Yêu cầu manual resync đang chờ.                 | Integration Admin, Super Admin                         | Should-have |
| WDG-PLATFORM-SUPPORT-ISSUES         | Support Tickets / Issues | Vấn đề support cần xử lý.                       | Support, Admin                                         | Should-have |
| WDG-PLATFORM-AUDIT-EVENTS           | Recent Audit Events      | Các thao tác nhạy cảm gần đây.                  | Super Admin, Auditor                                   | Could-have  |
| WDG-PLATFORM-SYSTEM-ALERTS          | System Alerts            | Cảnh báo hệ thống.                              | Super Admin, Admin                                     | Should-have |

Rule: Platform Dashboard không được hiển thị sâu dữ liệu kinh doanh của từng shop nếu role không có quyền. Đây là dashboard giám sát vận hành nền tảng, không phải dashboard xem doanh thu/lợi nhuận chi tiết của seller.

---

# 10. Lazada API Scope Direction

Dựa trên thông tin hiện có, Lazada API có thể hỗ trợ nhiều nhóm dữ liệu/thao tác. Tuy nhiên, tài liệu này chỉ dùng để xác định màn hình và phạm vi hiển thị, không chốt API contract chi tiết.

| Nhóm Lazada API               | Đưa vào phase đầu? | Screen Impact                                                         | Notes                                                   |
| ----------------------------- | ------------------ | --------------------------------------------------------------------- | ------------------------------------------------------- |
| Auth / Token                  | Yes                | SCR-LAZADA-CONNECTION, SCR-LAZADA-CHANNEL-DETAIL                      | Bắt buộc để connect Lazada.                             |
| Seller / Shop Info            | Yes                | SCR-LAZADA-CHANNEL-DETAIL, SCR-PLATFORM-LAZADA-INTEGRATION-MONITORING | Cần để gắn shop với Lazada.                             |
| Orders                        | Yes                | SCR-ORDER-LIST, SCR-ORDER-DETAIL, SCR-SYNC-LOG                        | Core.                                                   |
| Order Status                  | Yes                | SCR-ORDER-LIST, SCR-ORDER-DETAIL                                      | Hiển thị Marketplace Status + ShopHub Status.           |
| Products                      | Yes                | SCR-PRODUCT-LIST, SCR-PRODUCT-DETAIL, SCR-SYNC-LOG                    | Core.                                                   |
| SKU / Stock / Price           | Yes                | SCR-PRODUCT-DETAIL, SCR-INVENTORY-OVERVIEW, SCR-SYNC-LOG              | Core.                                                   |
| Category / Attributes / Brand | Should-have        | SCR-CATEGORY-FORM, SCR-PRODUCT-CREATE, SCR-PRODUCT-EDIT               | Phục vụ product mapping.                                |
| Logistics                     | Should-have        | SCR-SHIPMENT-DETAIL, SCR-ORDER-DETAIL                                 | Phục vụ shipping core.                                  |
| Finance / Fee / Transaction   | Should-have        | SCR-REPORT-PROFIT, SCR-SYNC-LOG                                       | Phục vụ profit report.                                  |
| Marketing / Ads               | Later              | Deferred Screens                                                      | Marketing/Promotion để phase sau.                       |
| Chat / Message                | Later / Could-have | Customer Support later                                                | Phase đầu dùng ticket nội bộ, RAG/Chat tích hợp để sau. |
| E-Tickets / LazPay / Wallet   | Later              | N/A                                                                   | Không thuộc core scope hiện tại.                        |
| DataMoat / Risk / Analytics   | Later              | N/A                                                                   | Không thuộc phase đầu.                                  |

---

# 11. Order Status Display Direction

Vì mapping Lazada status chưa chốt chi tiết, các màn Order cần hiển thị theo hướng an toàn:

| Status Type        | Description                             | Display Location                          |
| ------------------ | --------------------------------------- | ----------------------------------------- |
| Marketplace Status | Trạng thái gốc từ Lazada hoặc sàn.      | Order List, Order Detail                  |
| ShopHub Status     | Trạng thái chuẩn hóa của ShopHub.       | Order List, Order Detail, Reports         |
| Sync Status        | Trạng thái đồng bộ giữa ShopHub và sàn. | Order Detail, Sync Log, Sync Error Detail |

Rule:

| Rule ID                       | Rule                                                                                                          |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------- |
| RULE-ORDER-STATUS-DISPLAY-001 | Không được chỉ hiển thị ShopHub Status nếu trạng thái gốc từ sàn vẫn quan trọng cho đối soát.                 |
| RULE-ORDER-STATUS-DISPLAY-002 | Nếu Marketplace Status chưa mapping được, hiển thị trạng thái gốc và đánh dấu `Unmapped` hoặc `Need Mapping`. |
| RULE-ORDER-STATUS-DISPLAY-003 | Nếu ShopHub và sàn lệch trạng thái, màn Order Detail phải có cảnh báo sync/status mismatch.                   |

---

# 12. Report Export Direction

| Requirement      | Description                                                        |
| ---------------- | ------------------------------------------------------------------ |
| Export Format    | Excel hoặc PDF.                                                    |
| Report Format    | Mỗi loại báo cáo có format riêng, thiết kế chi tiết sau.           |
| Time Range       | User được chọn khoảng thời gian xuất báo cáo.                      |
| Column Selection | User được chọn các cột cần xuất.                                   |
| Large Export     | Export nhỏ xử lý trực tiếp; export lớn có thể chạy background job. |

Các màn liên quan:

| Screen ID            | Impact                                                         |
| -------------------- | -------------------------------------------------------------- |
| SCR-REPORT-EXPORT    | Phải có chọn loại báo cáo, thời gian, định dạng, cột cần xuất. |
| SCR-REPORT-REVENUE   | Có action export revenue report.                               |
| SCR-REPORT-PROFIT    | Có action export profit report.                                |
| SCR-REPORT-INVENTORY | Có action export inventory report.                             |
| SCR-REPORT-CHANNEL   | Có action export channel report.                               |

---

# 13. Customer Support / RAG Direction

Phase đầu Customer Support chỉ cần ticket nội bộ cơ bản.

| Capability      | Phase   | Notes                                                                                             |
| --------------- | ------- | ------------------------------------------------------------------------------------------------- |
| Customer Ticket | Phase 1 | Có ticket list/detail.                                                                            |
| Priority        | Phase 1 | Ticket có mức ưu tiên cơ bản.                                                                     |
| Assignee        | Phase 1 | Có người phụ trách xử lý.                                                                         |
| Status          | Phase 1 | Open, InProgress, WaitingCustomer, Resolved, Closed.                                              |
| Notes           | Phase 1 | Ghi chú chăm sóc khách hàng.                                                                      |
| SLA             | Later   | Chưa cần trong phase đầu.                                                                         |
| RAG Assistant   | Later   | Sau này có thể dùng RAG để gợi ý trả lời, tra cứu chính sách, lịch sử đơn, hướng xử lý khiếu nại. |

---

# 14. Inventory Approval Threshold Direction

Ngưỡng duyệt điều chỉnh tồn kho không hard-code cố định. Mỗi shop có thể cấu hình.

| Setting                                 | Default Proposal                        | Notes                                                                                    |
| --------------------------------------- | --------------------------------------- | ---------------------------------------------------------------------------------------- |
| Inventory Adjustment Approval Threshold | 10.000.000 VND / sản phẩm               | Có thể cấu hình theo shop.                                                               |
| Value Formula                           | ABS(Quantity Difference) × Product Cost | Nếu thiếu Product Cost, có thể dùng Selling Price tạm thời và đánh dấu ước tính.         |
| Low Impact                              | Dưới ngưỡng cấu hình                    | Shop Manager hoặc Shop Owner có thể duyệt.                                               |
| High Impact                             | Từ ngưỡng cấu hình trở lên              | Ưu tiên Shop Owner duyệt; Shop Manager chỉ được duyệt nếu Permission Matrix cấu hình rõ. |

Các màn liên quan:

| Screen ID                         | Impact                                        |
| --------------------------------- | --------------------------------------------- |
| SCR-INVENTORY-ADJUSTMENT-REQUEST  | Hiển thị giá trị chênh lệch ước tính.         |
| SCR-INVENTORY-ADJUSTMENT-APPROVAL | Hiển thị impact level: Low / High / Critical. |
| SCR-SHOP-SETTINGS                 | Cho phép cấu hình ngưỡng duyệt tồn kho.       |
| SCR-SELLER-AUDIT-LOG              | Log thay đổi ngưỡng duyệt.                    |

---

# 15. Payment Result Direction

Payment logic nằm ở backend/API và VNPay. Frontend không tự quyết định thanh toán thành công hay thất bại.

`SCR-PAYMENT-RESULT` là màn dùng chung để hiển thị kết quả đã được hệ thống xác minh.

| Payment UI State | Meaning                                                        | User Message Direction                            |
| ---------------- | -------------------------------------------------------------- | ------------------------------------------------- |
| Success          | VNPay/API/backend xác nhận thanh toán thành công.              | Gói mới đã được kích hoạt hoặc đang được áp dụng. |
| Failed           | Thanh toán thất bại.                                           | Gói chưa thay đổi, user có thể thử lại.           |
| Pending          | Đang chờ callback/xác minh.                                    | Hệ thống đang kiểm tra giao dịch.                 |
| Expired          | QR hoặc phiên thanh toán hết hạn.                              | User cần tạo giao dịch mới.                       |
| NeedReview       | Có dấu hiệu thanh toán nhưng cần reconciliation/manual review. | Hệ thống cần kiểm tra lại giao dịch.              |

Rule:

| Rule ID                 | Rule                                                                                               |
| ----------------------- | -------------------------------------------------------------------------------------------------- |
| RULE-PAYMENT-RESULT-001 | Không cần tạo nhiều màn payment result riêng. Một màn `SCR-PAYMENT-RESULT` xử lý nhiều trạng thái. |
| RULE-PAYMENT-RESULT-002 | Subscription chỉ đổi gói sau khi backend xác minh payment thành công.                              |
| RULE-PAYMENT-RESULT-003 | Nếu payment pending/need review, màn phải giải thích rõ gói chưa được đổi ngay.                    |

---

# 16. Screen Group by Module

| Module ID        | Module Name                     | Screens                                                                                                                                                                                                                                                      | Coverage Status |
| ---------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------- |
| MOD-AUTH         | Authentication & Authorization  | SCR-AUTH-LOGIN, SCR-AUTH-FORGOT-PASSWORD, SCR-AUTH-RESET-PASSWORD, SCR-AUTH-ACCEPT-INVITATION                                                                                                                                                                | Covered         |
| MOD-TENANT       | Shop / Tenant Management        | SCR-PLATFORM-SHOP-LIST, SCR-PLATFORM-SHOP-DETAIL, SCR-PLATFORM-SHOP-SUSPEND-RESTORE, SCR-PLATFORM-SUPPORT-SHOP-LOOKUP                                                                                                                                        | Covered         |
| MOD-USER         | User & Role Management          | SCR-AUTH-ACCEPT-INVITATION, SCR-SHOP-USER-LIST, SCR-INVITE-SHOP-USER, SCR-SHOP-USER-DETAIL, SCR-PLATFORM-USER-LIST, SCR-PLATFORM-INVITE-USER                                                                                                                 | Covered         |
| MOD-DASHBOARD    | Dashboard                       | SCR-SELLER-DASHBOARD, SCR-PLATFORM-DASHBOARD                                                                                                                                                                                                                 | Covered         |
| MOD-PRODUCT      | Product Management              | SCR-PRODUCT-LIST, SCR-PRODUCT-DETAIL, SCR-PRODUCT-CREATE, SCR-PRODUCT-EDIT                                                                                                                                                                                   | Covered         |
| MOD-CATEGORY     | Category Management             | SCR-CATEGORY-LIST, SCR-CATEGORY-FORM                                                                                                                                                                                                                         | Covered         |
| MOD-ORDER        | Order Management                | SCR-ORDER-LIST, SCR-ORDER-DETAIL                                                                                                                                                                                                                             | Covered         |
| MOD-CUSTOMER     | Customer Management & Care      | SCR-CUSTOMER-LIST, SCR-CUSTOMER-DETAIL, SCR-CUSTOMER-TICKET-LIST, SCR-CUSTOMER-TICKET-DETAIL                                                                                                                                                                 | Covered         |
| MOD-INVENTORY    | Inventory Management            | SCR-INVENTORY-OVERVIEW, SCR-INVENTORY-STOCK-IN, SCR-INVENTORY-ADJUSTMENT-REQUEST, SCR-INVENTORY-ADJUSTMENT-APPROVAL, SCR-INVENTORY-LOW-STOCK                                                                                                                 | Covered         |
| MOD-CHANNEL      | Sales Channel Management        | SCR-CHANNEL-LIST, SCR-LAZADA-CONNECTION, SCR-LAZADA-CHANNEL-DETAIL, SCR-PLATFORM-LAZADA-INTEGRATION-MONITORING                                                                                                                                               | Covered         |
| MOD-SYNC         | Real-time / Near Real-time Sync | SCR-SYNC-DASHBOARD, SCR-SYNC-LOG, SCR-SYNC-ERROR-DETAIL, SCR-MANUAL-RESYNC, SCR-PLATFORM-SYNC-ERROR-MONITORING, SCR-PLATFORM-MANUAL-RESYNC-REVIEW                                                                                                            | Covered         |
| MOD-SHIPPING     | Shipping Core                   | SCR-SHIPMENT-DETAIL                                                                                                                                                                                                                                          | Covered         |
| MOD-REPORT       | Reports & Analytics             | SCR-REPORTS-OVERVIEW, SCR-REPORT-REVENUE, SCR-REPORT-PROFIT, SCR-REPORT-INVENTORY, SCR-REPORT-CHANNEL, SCR-REPORT-EXPORT, SCR-PROFIT-FORMULA-SETTINGS, SCR-PROFIT-FORMULA-APPROVAL                                                                           | Covered         |
| MOD-SUBSCRIPTION | Subscription & Payment          | SCR-SUBSCRIPTION-CURRENT-PLAN, SCR-SUBSCRIPTION-UPGRADE, SCR-SUBSCRIPTION-DOWNGRADE, SCR-PAYMENT-VNPAY-QR, SCR-PAYMENT-RESULT, SCR-INVOICE-HISTORY, SCR-PLATFORM-PLAN-MANAGEMENT, SCR-PLATFORM-SUBSCRIPTION-MANAGEMENT, SCR-PLATFORM-PAYMENT-INVOICE-RECORDS | Covered         |
| MOD-AUDIT        | Audit Log                       | SCR-SELLER-AUDIT-LOG, SCR-PLATFORM-AUDIT-LOG                                                                                                                                                                                                                 | Covered         |
| MOD-NOTIFICATION | Notification                    | SCR-SELLER-NOTIFICATIONS, SCR-INVENTORY-LOW-STOCK                                                                                                                                                                                                            | Covered         |
| MOD-SETTING      | Settings                        | SCR-SHOP-SETTINGS, SCR-PROFIT-FORMULA-SETTINGS, SCR-PLATFORM-SETTINGS                                                                                                                                                                                        | Covered         |
| MOD-MARKETING    | Marketing / Promotion           | Deferred only                                                                                                                                                                                                                                                | Deferred        |

---

# 17. Screen Group by Role

## 17.1 Seller-side Roles

| Role ID               | Main Screens                                                                                            | Notes                                                        |
| --------------------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| ROLE-SHOP-OWNER       | Tất cả seller-side screens                                                                              | Toàn quyền trong phạm vi shop, trừ hạn chế theo plan/status. |
| ROLE-SHOP-MANAGER     | Dashboard, Products, Categories, Orders, Customers, Inventory, Reports, Settings limited, Audit limited | Không được quản lý user/role.                                |
| ROLE-ORDER-STAFF      | Dashboard limited, Order List, Order Detail, Customer Detail, Customer Ticket                           | Được hủy đơn nếu rule cho phép và phải confirm.              |
| ROLE-WAREHOUSE-STAFF  | Dashboard limited, Inventory Overview, Stock In, Adjustment Request, Shipment Detail, Low Stock         | Điều chỉnh tồn kho cần lý do/bằng chứng và approval.         |
| ROLE-PRODUCT-STAFF    | Products, Categories, Sync Log, Sync Error Detail                                                       | Không mặc định xử lý đơn hoặc điều chỉnh kho.                |
| ROLE-ACCOUNTANT       | Reports, Profit Report, Profit Formula Settings, Invoice History limited, Customer/Order view limited   | Chỉnh công thức lợi nhuận cần approval.                      |
| ROLE-CUSTOMER-SUPPORT | Customer List, Customer Detail, Ticket List, Ticket Detail, Order Detail limited                        | Không phải khách mua hàng cuối.                              |
| ROLE-SHOP-VIEWER      | Dashboard limited, Product/Order/Inventory/Report view limited                                          | Chỉ xem, không chỉnh sửa.                                    |

## 17.2 Platform-side Roles

| Role ID                        | Main Screens                                                                                        | Notes                                                         |
| ------------------------------ | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| ROLE-SHOPHUB-SUPER-ADMIN       | Tất cả platform-side screens                                                                        | Quyền cao nhất platform, không dùng cho thao tác thường ngày. |
| ROLE-SHOPHUB-ADMIN             | Platform Dashboard, Shop Management, Platform User limited, Integration Monitoring, Audit, Settings | Quản trị vận hành platform.                                   |
| ROLE-SHOPHUB-SUPPORT           | Support Shop Lookup, Shop Detail limited, Integration/Synchronization status limited                | Không được impersonate user shop.                             |
| ROLE-SHOPHUB-BILLING-ADMIN     | Plan Management, Subscription Management, Payment/Invoice Records, Shop Detail billing limited      | Không đổi gói thủ công ngoài flow.                            |
| ROLE-SHOPHUB-INTEGRATION-ADMIN | Integration Monitoring, Sync Error Monitoring, Manual Resync Review                                 | Resync dữ liệu quan trọng cần Shop Owner xác nhận.            |
| ROLE-SHOPHUB-AUDITOR           | Platform Audit Log, audit-related detail screens                                                    | Chủ yếu read-only.                                            |

---

# 18. Screen Group by Business Flow

| Flow ID                            | Flow Name                                          | Required Screens                                                                                       | Coverage Status | Notes                                                       |
| ---------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | --------------- | ----------------------------------------------------------- |
| FLOW-TENANT-ONBOARD-SHOP           | Onboard New Shop                                   | SCR-PLATFORM-SHOP-LIST, SCR-PLATFORM-SHOP-DETAIL                                                       | Covered         | Onboarding có thể dùng màn shop detail/status.              |
| FLOW-USER-INVITE-SHOP-USER         | Invite Shop User                                   | SCR-SHOP-USER-LIST, SCR-INVITE-SHOP-USER, SCR-SHOP-USER-DETAIL                                         | Covered         | Seller-side invite.                                         |
| FLOW-USER-ACCEPT-INVITE            | Accept Invitation                                  | SCR-AUTH-ACCEPT-INVITATION, SCR-AUTH-LOGIN                                                             | Covered         | Có màn Accept Invitation + Email OTP.                       |
| FLOW-CHANNEL-CONNECT-LAZADA        | Connect Lazada Channel                             | SCR-CHANNEL-LIST, SCR-LAZADA-CONNECTION, SCR-LAZADA-CHANNEL-DETAIL                                     | Covered         | Lazada API field chi tiết xử lý sau.                        |
| FLOW-SYNC-LAZADA-ORDER             | Sync Lazada Orders                                 | SCR-SYNC-DASHBOARD, SCR-SYNC-LOG, SCR-SYNC-ERROR-DETAIL, SCR-ORDER-LIST                                | Covered         | Background flow có monitoring.                              |
| FLOW-SYNC-LAZADA-STOCK             | Sync Available Stock to Lazada                     | SCR-SYNC-DASHBOARD, SCR-SYNC-LOG, SCR-INVENTORY-OVERVIEW                                               | Covered         | Background flow có monitoring.                              |
| FLOW-SYNC-ERROR-RETRY              | Handle Sync Error / Retry                          | SCR-SYNC-ERROR-DETAIL, SCR-PLATFORM-SYNC-ERROR-MONITORING                                              | Covered         | Có seller/platform monitoring.                              |
| FLOW-SYNC-MANUAL-RESYNC            | Manual Resync                                      | SCR-MANUAL-RESYNC, SCR-PLATFORM-MANUAL-RESYNC-REVIEW                                                   | Covered         | Cần confirm khi ảnh hưởng dữ liệu quan trọng.               |
| FLOW-PRODUCT-CREATE                | Create Product                                     | SCR-PRODUCT-CREATE                                                                                     | Covered         |                                                             |
| FLOW-PRODUCT-UPDATE-STATUS         | Update Product Status                              | SCR-PRODUCT-DETAIL, SCR-PRODUCT-EDIT                                                                   | Covered         | Product status theo sàn/custom shop.                        |
| FLOW-ORDER-REVIEW-CONFIRM          | Review & Confirm Order                             | SCR-ORDER-LIST, SCR-ORDER-DETAIL                                                                       | Covered         | Xác nhận đơn ảnh hưởng Available/Reserved stock.            |
| FLOW-ORDER-CANCEL                  | Cancel Order                                       | SCR-ORDER-DETAIL                                                                                       | Covered         | Bắt buộc confirm.                                           |
| FLOW-ORDER-SHIPMENT                | Process Shipment / Stock Out                       | SCR-ORDER-DETAIL, SCR-SHIPMENT-DETAIL                                                                  | Covered         | Warehouse giao hàng làm giảm Physical Stock.                |
| FLOW-INVENTORY-STOCK-IN            | Stock In                                           | SCR-INVENTORY-STOCK-IN, SCR-INVENTORY-OVERVIEW                                                         | Covered         |                                                             |
| FLOW-INVENTORY-ADJUSTMENT-APPROVAL | Inventory Adjustment Approval                      | SCR-INVENTORY-ADJUSTMENT-REQUEST, SCR-INVENTORY-ADJUSTMENT-APPROVAL, SCR-SHOP-SETTINGS                 | Covered         | Ngưỡng tiền được cấu hình theo shop.                        |
| FLOW-CUSTOMER-CARE-TICKET          | Customer Care Issue / Ticket                       | SCR-CUSTOMER-TICKET-LIST, SCR-CUSTOMER-TICKET-DETAIL, SCR-CUSTOMER-DETAIL                              | Covered         | Ticket có priority/assignee cơ bản; RAG later.              |
| FLOW-PROFIT-FORMULA-APPROVAL       | Profit Formula Approval                            | SCR-PROFIT-FORMULA-SETTINGS, SCR-PROFIT-FORMULA-APPROVAL                                               | Covered         | Accountant cần approval.                                    |
| FLOW-REPORT-EXPORT                 | Export Report                                      | SCR-REPORT-EXPORT, report screens                                                                      | Covered         | Export Excel/PDF, chọn time range và cột xuất.              |
| FLOW-SUBSCRIPTION-UPGRADE-PAYMENT  | Upgrade Plan with VNPay QR                         | SCR-SUBSCRIPTION-UPGRADE, SCR-PAYMENT-VNPAY-QR, SCR-PAYMENT-RESULT                                     | Covered         | VNPay QR.                                                   |
| FLOW-SUBSCRIPTION-PASTDUE-SUSPEND  | Past Due / Suspend Shop                            | SCR-SUBSCRIPTION-CURRENT-PLAN, SCR-PLATFORM-SUBSCRIPTION-MANAGEMENT, SCR-PLATFORM-SHOP-SUSPEND-RESTORE | Covered         |                                                             |
| FLOW-AUTH-LOGIN-SESSION            | Login & Session Validation                         | SCR-AUTH-LOGIN                                                                                         | Covered         |                                                             |
| FLOW-AUTH-RESET-PASSWORD           | Forgot / Reset Password                            | SCR-AUTH-FORGOT-PASSWORD, SCR-AUTH-RESET-PASSWORD                                                      | Covered         |                                                             |
| FLOW-USER-PLATFORM-INVITE          | Invite Platform User                               | SCR-PLATFORM-USER-LIST, SCR-PLATFORM-INVITE-USER, SCR-AUTH-ACCEPT-INVITATION                           | Covered         | Platform invited user cũng dùng Accept Invitation + OTP.    |
| FLOW-TENANT-SHOP-SUSPEND-RESTORE   | Suspend / Restore Shop                             | SCR-PLATFORM-SHOP-SUSPEND-RESTORE, SCR-PLATFORM-SHOP-DETAIL                                            | Covered         |                                                             |
| FLOW-DASHBOARD-SELLER-VIEW         | View Seller Dashboard                              | SCR-SELLER-DASHBOARD                                                                                   | Covered         |                                                             |
| FLOW-CATEGORY-MANAGE               | Manage Category                                    | SCR-CATEGORY-LIST, SCR-CATEGORY-FORM                                                                   | Covered         |                                                             |
| FLOW-PRODUCT-UPDATE                | Update Product Information                         | SCR-PRODUCT-DETAIL, SCR-PRODUCT-EDIT                                                                   | Covered         |                                                             |
| FLOW-PRODUCT-DEACTIVATE            | Safe Product Deactivation                          | SCR-PRODUCT-DETAIL, SCR-PRODUCT-LIST                                                                   | Covered         | Deactivate/archive, không xóa cứng nếu có đơn.              |
| FLOW-CUSTOMER-VIEW-MANAGE          | View Customer & Order History                      | SCR-CUSTOMER-LIST, SCR-CUSTOMER-DETAIL                                                                 | Covered         |                                                             |
| FLOW-REPORT-VIEW                   | View Reports                                       | SCR-REPORTS-OVERVIEW, SCR-REPORT-REVENUE, SCR-REPORT-PROFIT, SCR-REPORT-INVENTORY, SCR-REPORT-CHANNEL  | Covered         |                                                             |
| FLOW-SUBSCRIPTION-DOWNGRADE        | Downgrade Plan                                     | SCR-SUBSCRIPTION-DOWNGRADE                                                                             | Covered         |                                                             |
| FLOW-AUDIT-VIEW                    | View Audit Log                                     | SCR-SELLER-AUDIT-LOG, SCR-PLATFORM-AUDIT-LOG                                                           | Covered         |                                                             |
| FLOW-NOTIFICATION-HANDLE           | Handle Notifications                               | SCR-SELLER-NOTIFICATIONS                                                                               | Covered         |                                                             |
| FLOW-SETTING-MANAGE                | Manage Settings                                    | SCR-SHOP-SETTINGS, SCR-PROFIT-FORMULA-SETTINGS, SCR-PLATFORM-SETTINGS                                  | Covered         |                                                             |
| FLOW-DASHBOARD-PLATFORM-VIEW       | View Platform Dashboard                            | SCR-PLATFORM-DASHBOARD                                                                                 | Covered         | Widget list đã bổ sung trong tài liệu này.                  |
| FLOW-CHANNEL-DISCONNECT-LAZADA     | Disconnect Lazada Channel                          | SCR-LAZADA-CHANNEL-DETAIL                                                                              | Covered         |                                                             |
| FLOW-SYNC-LAZADA-PRODUCT           | Sync Lazada Products                               | SCR-SYNC-LOG, SCR-SYNC-ERROR-DETAIL, SCR-PRODUCT-DETAIL                                                | Covered         | Background + monitoring.                                    |
| FLOW-SYNC-LAZADA-FEES              | Sync Lazada Fees / Discounts / Reconciliation Data | SCR-SYNC-LOG, SCR-REPORT-PROFIT                                                                        | Covered         | Nếu API không hỗ trợ, report hiển thị data quality warning. |
| FLOW-INVENTORY-LOW-STOCK-ALERT     | Low Stock Alert                                    | SCR-INVENTORY-LOW-STOCK, SCR-SELLER-NOTIFICATIONS                                                      | Covered         |                                                             |
| FLOW-SUBSCRIPTION-PLAN-MANAGE      | Manage Plan Definition                             | SCR-PLATFORM-PLAN-MANAGEMENT                                                                           | Covered         |                                                             |
| FLOW-SUBSCRIPTION-INVOICE-RECORD   | Invoice / Payment Record                           | SCR-INVOICE-HISTORY, SCR-PLATFORM-PAYMENT-INVOICE-RECORDS                                              | Covered         |                                                             |

---

# 19. Background/System Flows Without Direct Screen

| Flow ID                           | Why No Direct Screen                                    | Monitoring Screen                                                   |
| --------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------- |
| FLOW-SYNC-LAZADA-ORDER            | Chạy nền qua webhook/polling/job.                       | SCR-SYNC-DASHBOARD, SCR-SYNC-LOG, SCR-SYNC-ERROR-DETAIL             |
| FLOW-SYNC-LAZADA-STOCK            | Chạy nền khi Available Stock thay đổi.                  | SCR-SYNC-DASHBOARD, SCR-SYNC-LOG, SCR-INVENTORY-OVERVIEW            |
| FLOW-SYNC-LAZADA-PRODUCT          | Chạy nền khi product thay đổi hoặc nhận sync từ Lazada. | SCR-SYNC-LOG, SCR-SYNC-ERROR-DETAIL, SCR-PRODUCT-DETAIL             |
| FLOW-SYNC-LAZADA-FEES             | Chạy nền nếu Lazada API hỗ trợ phí/đối soát.            | SCR-SYNC-LOG, SCR-REPORT-PROFIT                                     |
| FLOW-SYNC-ERROR-RETRY             | Retry có thể tự động theo policy.                       | SCR-SYNC-ERROR-DETAIL, SCR-PLATFORM-SYNC-ERROR-MONITORING           |
| FLOW-SUBSCRIPTION-PASTDUE-SUSPEND | System job kiểm tra quá hạn.                            | SCR-PLATFORM-SUBSCRIPTION-MANAGEMENT, SCR-SUBSCRIPTION-CURRENT-PLAN |
| FLOW-NOTIFICATION-HANDLE          | Tạo notification theo event.                            | SCR-SELLER-NOTIFICATIONS                                            |
| FLOW-INVENTORY-LOW-STOCK-ALERT    | Hệ thống kiểm tra ngưỡng tồn thấp.                      | SCR-INVENTORY-LOW-STOCK                                             |

---

# 20. Deferred Screens

| Deferred Screen                           | Reason                                                                        | Related Module       | Phase              |
| ----------------------------------------- | ----------------------------------------------------------------------------- | -------------------- | ------------------ |
| Promotion Management                      | Marketing/Promotion để phase sau.                                             | MOD-MARKETING        | Later              |
| Campaign Management                       | Chưa thuộc core scope.                                                        | MOD-MARKETING        | Later              |
| Voucher Management                        | Chưa thuộc core scope.                                                        | MOD-MARKETING        | Later              |
| Marketing Staff Role Screens              | Chưa tạo role Marketing Staff phase đầu.                                      | MOD-MARKETING        | Later              |
| Lazada Marketing / Ads Screens            | Lazada API có nhóm ads/marketing nhưng Marketing/Promotion đang để phase sau. | MOD-MARKETING        | Later              |
| Lazada Chat / Message Integration Screens | Phase đầu dùng ticket nội bộ; tích hợp chat/message để sau.                   | MOD-CUSTOMER         | Later / Could-have |
| Customer Support RAG Assistant            | RAG cho CSKH để phase sau.                                                    | MOD-CUSTOMER         | Later              |
| SLA Management                            | Phase đầu chỉ có Priority + Assignee, chưa cần SLA.                           | MOD-CUSTOMER         | Later              |
| Multi-warehouse Management                | Phase đầu mỗi shop chỉ có 1 kho vật lý.                                       | MOD-INVENTORY        | Later              |
| Multi-shop User Management                | Phase đầu 1 user thuộc 1 shop.                                                | MOD-TENANT, MOD-USER | Later              |
| Native Mobile App Screens                 | Ưu tiên web dashboard.                                                        | General              | Later              |
| Advanced CRM Screens                      | Customer care phase đầu chỉ có customer/ticket cơ bản.                        | MOD-CUSTOMER         | Later              |
| AI Recommendation Screens                 | Không thuộc scope phase đầu.                                                  | General              | Later              |
| Excel Import Screens                      | Đã chốt chưa cần import Excel phase đầu.                                      | General              | Later              |
| E-Tickets / LazPay / Wallet Screens       | Không thuộc core scope hiện tại.                                              | Integration          | Later              |
| DataMoat / Risk / Analytics Screens       | Không thuộc phase đầu.                                                        | Integration          | Later              |

---

# 21. Open Questions

| Question ID   | Question                                                                              | Related Screen/Module              | Impact                                                                                                                        | Status                           |
| ------------- | ------------------------------------------------------------------------------------- | ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| Q-SITEMAP-001 | Có cần màn Accept Invitation riêng không?                                             | SCR-AUTH-ACCEPT-INVITATION         | Đã thêm màn riêng, có email invitation và OTP qua email.                                                                      | Answered                         |
| Q-SITEMAP-002 | Platform Dashboard cần widget nào?                                                    | SCR-PLATFORM-DASHBOARD             | Đã đề xuất widget shop, subscription, payment, sync, support, audit, system alert.                                            | Answered                         |
| Q-SITEMAP-003 | Lazada API support ảnh hưởng màn Sync thế nào?                                        | Sync / Lazada / Reports            | Đã định hướng scope phase đầu/later. API contract chi tiết vẫn cần verify ở Integration Spec.                                 | Answered / Need API Verification |
| Q-SITEMAP-004 | Mapping trạng thái Lazada → ShopHub chưa rõ thì Order screen hiển thị status thế nào? | SCR-ORDER-LIST, SCR-ORDER-DETAIL   | Hiển thị cả Marketplace Status và ShopHub Status; mapping chi tiết xử lý sau.                                                 | Answered / Pending Mapping       |
| Q-SITEMAP-005 | Export report cần gì?                                                                 | SCR-REPORT-EXPORT                  | Có format riêng thiết kế sau; phase này cần chọn time range, file type và cột cần xuất.                                       | Answered                         |
| Q-SITEMAP-006 | Ticket CSKH có cần SLA/RAG không?                                                     | Customer Ticket Screens            | Phase đầu có Priority + Assignee + Status + Notes; RAG/SLA để sau.                                                            | Answered / Later                 |
| Q-SITEMAP-007 | Shop Manager duyệt inventory adjustment trên 10 triệu được không?                     | Inventory Approval / Shop Settings | Ngưỡng tiền cấu hình theo shop; mặc định 10 triệu/sản phẩm. Shop Manager duyệt mức cao chỉ khi Permission Matrix cấu hình rõ. | Answered                         |
| Q-SITEMAP-008 | Payment Result có cần không nếu thanh toán qua API?                                   | SCR-PAYMENT-RESULT                 | Có. API/backend xử lý thanh toán; UI cần hiển thị trạng thái Success/Failed/Pending/Expired/NeedReview.                       | Answered                         |

---

# 22. Decisions Log

| Decision ID     | Decision                                                                                                | Reason                                                          | Related Docs                            | Status   |
| --------------- | ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- | --------------------------------------- | -------- |
| DEC-SITEMAP-001 | Tách Seller-side và Platform-side sitemap.                                                              | Tránh lẫn quyền nhà bán hàng với quyền vận hành nền tảng.       | 02_USER_ROLES.md                        | Accepted |
| DEC-SITEMAP-002 | Không tạo customer portal cho khách mua hàng cuối.                                                      | Khách mua hàng cuối không được đăng nhập ShopHub.               | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md   | Accepted |
| DEC-SITEMAP-003 | Tạo màn Sync Dashboard / Sync Log / Sync Error Detail cho các sync background flow.                     | Các flow sync chạy nền nhưng cần giám sát.                      | 04_BUSINESS_FLOW.md                     | Accepted |
| DEC-SITEMAP-004 | Tạo màn VNPay QR Payment và Payment Result.                                                             | Subscription payment dùng VNPay QR.                             | 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md | Accepted |
| DEC-SITEMAP-005 | Tạo màn Customer Ticket List/Detail.                                                                    | Customer Support được tạo issue/ticket riêng.                   | 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md | Accepted |
| DEC-SITEMAP-006 | Tạo màn Inventory Adjustment Request/Approval.                                                          | Warehouse adjustment cần lý do, bằng chứng và approval.         | 02_USER_ROLES.md, 04_BUSINESS_FLOW.md   | Accepted |
| DEC-SITEMAP-007 | Marketing/Promotion đưa vào Deferred Screens.                                                           | Đã chốt để phase sau.                                           | 03_FEATURE_LIST.md                      | Accepted |
| DEC-SITEMAP-008 | Lazada API chưa confirm mapping chi tiết nên màn Lazada không tự bịa field kỹ thuật.                    | Tránh sai scope tích hợp.                                       | 04_BUSINESS_FLOW.md                     | Accepted |
| DEC-SITEMAP-009 | Thêm màn `SCR-AUTH-ACCEPT-INVITATION`.                                                                  | User được mời cần nhập OTP qua email và chấp nhận lời mời.      | 02_USER_ROLES.md, 04_BUSINESS_FLOW.md   | Accepted |
| DEC-SITEMAP-010 | Platform Dashboard gồm widget shop, subscription, payment, sync, support, audit và system alert.        | Đủ giám sát vận hành platform.                                  | 04_BUSINESS_FLOW.md                     | Accepted |
| DEC-SITEMAP-011 | Lazada API phase đầu ưu tiên auth, shop, order, product, SKU/stock/price, category, logistics, finance. | Phục vụ core e-commerce hub.                                    | 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md | Accepted |
| DEC-SITEMAP-012 | Order screen hiển thị cả Marketplace Status và ShopHub Status.                                          | Tránh lệch hoặc mất thông tin đối soát với sàn.                 | 04_BUSINESS_FLOW.md                     | Accepted |
| DEC-SITEMAP-013 | Export report cho phép chọn time range, format Excel/PDF và các cột cần xuất.                           | Phù hợp nhu cầu báo cáo thực tế.                                | 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md | Accepted |
| DEC-SITEMAP-014 | RAG cho Customer Support để phase sau.                                                                  | Tránh làm phức tạp phase đầu.                                   | 03_FEATURE_LIST.md                      | Accepted |
| DEC-SITEMAP-015 | Inventory adjustment threshold được cấu hình theo shop, mặc định đề xuất 10.000.000 VND/sản phẩm.       | Từng shop có mức kiểm soát khác nhau.                           | 04_BUSINESS_FLOW.md                     | Accepted |
| DEC-SITEMAP-016 | Payment Result dùng một màn chung cho Success/Failed/Pending/Expired/NeedReview.                        | Logic payment nằm ở backend/API, frontend chỉ hiển thị kết quả. | 04_BUSINESS_FLOW.md                     | Accepted |

---

# 23. Change Log

| Version | Date | Change                                                                                                                                                                                                                                                                                    | Author |
| ------- | ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| 0.1     | TBD  | Initial Sitemap and Screen List based on Project Brief, User Roles, Feature List and Business Flow                                                                                                                                                                                        | TBD    |
| 0.2     | TBD  | Updated Open Questions answers: Accept Invitation with email OTP, Platform Dashboard widgets, Lazada API scope direction, Marketplace/ShopHub status display, report export options, Customer Support RAG later, configurable inventory approval threshold, unified Payment Result screen | TBD    |

---

# 24. Sitemap Verification Checklist

| Check Item                                                             | Result | Notes                                                                                         |
| ---------------------------------------------------------------------- | ------ | --------------------------------------------------------------------------------------------- |
| Tất cả Must-have module đã có screen chưa?                             | Pass   | Các module phase đầu đã có screen tương ứng.                                                  |
| Tất cả Must-have feature đã có screen hoặc system flow chưa?           | Pass   | Background sync/payment/notification có monitoring screen.                                    |
| Seller-side và platform-side đã tách rõ chưa?                          | Pass   | Seller routes `/app`, platform routes `/platform`.                                            |
| Khách mua hàng cuối có bị tạo nhầm screen login không?                 | Pass   | Không có customer portal/customer login.                                                      |
| Mỗi screen có Screen ID chưa?                                          | Pass   | Tất cả screen trong bảng đều có Screen ID.                                                    |
| Mỗi screen có route chưa?                                              | Pass   | Tất cả screen phase đầu đều có route đề xuất.                                                 |
| Mỗi screen có related feature/flow chưa?                               | Pass   | Đã map feature và flow chính.                                                                 |
| Các background flow có monitoring screen chưa?                         | Pass   | Sync, subscription past due, notification, low stock đều có monitoring screen.                |
| Deferred screens có được tách khỏi phase đầu chưa?                     | Pass   | Marketing, RAG, SLA, multi-warehouse, multi-shop user, mobile app, AI, advanced CRM để Later. |
| Lazada API chưa confirm mapping chi tiết có bị tự bịa field API không? | Pass   | Chỉ định hướng scope và để Integration Spec/API Spec xử lý chi tiết.                          |
| VNPay QR payment có màn đầy đủ chưa?                                   | Pass   | Có Upgrade Plan, VNPay QR Payment, Payment Result, Invoice History.                           |
| Payment Result có xử lý pending/expired/need review chưa?              | Pass   | Một màn chung xử lý nhiều trạng thái.                                                         |
| Inventory adjustment approval có màn đầy đủ chưa?                      | Pass   | Có Request, Approval, Settings threshold, Audit Log.                                          |
| Customer Support ticket có màn đầy đủ chưa?                            | Pass   | Có Ticket List và Ticket Detail.                                                              |
| Accept Invitation + OTP đã có màn chưa?                                | Pass   | Đã thêm `SCR-AUTH-ACCEPT-INVITATION`.                                                         |
