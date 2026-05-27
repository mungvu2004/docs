# 07_DATA_MODEL.md

## Metadata

| Field        | Value                                                                                                                                  |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| Document ID  | DOC-07-DATA-MODEL                                                                                                                      |
| Project Name | Multi-channel E-commerce Hub / ShopHub                                                                                                 |
| Version      | 0.2                                                                                                                                    |
| Status       | Draft - Updated ID, Role and Staging Strategy                                                                                          |
| Owner        | TBD                                                                                                                                    |
| Last Updated | TBD                                                                                                                                    |
| Depends On   | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md, 06_SCREEN_REQUIREMENTS/ |
| Used By      | 08_API_SPECIFICATION.md, 09_PERMISSION_MATRIX.md, 11_TECHNICAL_ARCHITECTURE.md, Backend Implementation, Database Design, QA Test Cases |

---

# 1. Purpose

Tài liệu này định nghĩa **mô hình dữ liệu logic** cho hệ thống ShopHub.

Mục tiêu:

* Xác định các entity chính của hệ thống.
* Làm rõ quan hệ giữa shop, user, role, product, order, inventory, channel, sync, report, subscription, payment, notification và audit.
* Đảm bảo tenant boundary: dữ liệu shop nào thuộc shop đó.
* Làm nền cho API Specification, Database Schema, Permission Matrix và Backend Implementation.
* Giảm rủi ro thiết kế database sai nghiệp vụ.

Tài liệu này **chưa phải database schema vật lý cuối cùng**. Kiểu dữ liệu cụ thể, index vật lý, migration, constraint và performance tuning sẽ được refine ở tài liệu kỹ thuật/database design.

---

# 2. Data Model Principles

| Rule ID       | Rule                                                                                                                                          |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| RULE-DATA-001 | Mọi dữ liệu seller-side phải gắn với `ShopId` hoặc entity thuộc shop.                                                                         |
| RULE-DATA-002 | User platform và user shop phải tách scope quyền rõ ràng.                                                                                     |
| RULE-DATA-003 | Không để platform-side tự ý sửa dữ liệu nghiệp vụ của shop nếu không có workflow rõ ràng.                                                     |
| RULE-DATA-004 | Entity có dữ liệu quan trọng phải có audit log hoặc history phù hợp.                                                                          |
| RULE-DATA-005 | Inventory phải tuân thủ mô hình `PhysicalStock`, `AvailableStock`, `ReservedStock`.                                                           |
| RULE-DATA-006 | Order phải lưu cả `MarketplaceStatus` và `ShopHubStatus`.                                                                                     |
| RULE-DATA-007 | Product phải hỗ trợ 3 lớp trạng thái: marketplace, normalized, custom.                                                                        |
| RULE-DATA-008 | Sync với Lazada phải có log, trạng thái, lỗi, retry và mapping dữ liệu.                                                                       |
| RULE-DATA-009 | Subscription chỉ đổi gói sau khi payment được xác minh hợp lệ.                                                                                |
| RULE-DATA-010 | Payment không được coi là thành công chỉ vì user quay lại màn success.                                                                        |
| RULE-DATA-011 | Các request approval phải có requester, approver, status, reason/evidence và audit trail.                                                     |
| RULE-DATA-012 | Không tạo customer account cho khách mua hàng cuối. Customer chỉ là dữ liệu từ đơn hàng/chăm sóc khách hàng.                                  |
| RULE-DATA-013 | Các field từ Lazada/API bên ngoài phải lưu được raw reference hoặc raw snapshot khi cần đối soát.                                             |
| RULE-DATA-014 | Entity có trạng thái phải dùng enum/status rõ ràng, không dùng text tự do nếu không cần custom.                                               |
| RULE-DATA-015 | Soft delete/deactivate được ưu tiên hơn hard delete với dữ liệu đã phát sinh giao dịch.                                                       |
| RULE-DATA-016 | Dữ liệu nhận từ API/webhook bên ngoài phải đi qua External/Staging tables trước khi cập nhật domain tables.                                   |
| RULE-DATA-017 | Entity nghiệp vụ chính nên có `InternalId` dạng BIGINT identity làm khóa chính DB và `Uid/PublicId` dạng UUID/GUID dùng cho API/URL/frontend. |
| RULE-DATA-018 | Không expose internal numeric ID ra public API/frontend URL nếu không cần thiết.                                                              |
| RULE-DATA-019 | BusinessCode dùng để hiển thị/tìm kiếm nghiệp vụ, không dùng làm primary key.                                                                 |
| RULE-DATA-020 | Role phải lưu bằng `RoleId` + `RoleCode` + `RoleName`; permission logic dùng `RoleCode` hoặc `PermissionCode`, không dùng display name.       |
| RULE-DATA-021 | Không hard-code `RoleId == 1/2/3` trong business logic vì ID seed có thể lệch giữa môi trường.                                                |

---

# 3. Data Model Scope

## 3.1 In Scope

| Area             | Included                                                                          |
| ---------------- | --------------------------------------------------------------------------------- |
| Tenant / Shop    | Shop, shop status, shop settings                                                  |
| User / Role      | User, role, user-shop membership, invitation                                      |
| Platform User    | Platform user membership and roles                                                |
| Product          | Product, SKU, product status, category, marketplace mapping                       |
| Inventory        | Physical / Available / Reserved stock, stock movement, adjustment request         |
| Order            | Order, order item, order status, order note, shipment                             |
| Customer         | Customer profile from orders, order history, ticket                               |
| Channel          | Sales channel, Lazada connection, channel config                                  |
| External/Staging | Webhook raw, order raw, product raw, finance raw, category raw, integration inbox |
| Sync             | Sync job/log/error/manual resync                                                  |
| Reports          | Report config, profit formula, export job                                         |
| Subscription     | Plan, subscription, payment, invoice                                              |
| Notification     | Notification/toast/event                                                          |
| Audit            | Audit log for sensitive actions                                                   |
| Settings         | Shop/platform settings                                                            |

## 3.2 Out of Scope

| Area                  | Reason                                       |
| --------------------- | -------------------------------------------- |
| Marketing / Promotion | Deferred phase.                              |
| Multi-warehouse       | Phase đầu mỗi shop có 1 kho vật lý.          |
| Multi-shop user       | Phase đầu user-shop là 1:1 ở seller side.    |
| Customer portal       | Khách mua hàng cuối không đăng nhập ShopHub. |
| Native mobile app     | Không thuộc data model riêng phase đầu.      |
| RAG assistant         | Deferred phase.                              |
| SLA management        | Deferred phase.                              |
| Excel import          | Chưa cần phase đầu.                          |

---

# 4. High-level Entity Map

```txt
Platform
├── PlatformUserMembership
├── PlatformMembershipRole
├── Plan
├── PlatformSetting
└── PlatformAuditLog / AuditLog

Shop / Tenant
├── Shop
├── ShopSetting
├── UserAccount
├── Role
├── ShopUserMembership
├── MembershipRole
├── Invitation
├── Product
│   ├── ProductSku
│   ├── Category
│   └── ProductMarketplaceMapping
├── Inventory
│   ├── Warehouse
│   ├── InventoryStock
│   ├── InventoryMovement
│   └── InventoryAdjustmentRequest
├── Sales Channel
│   ├── ChannelConnection
│   ├── ChannelCredential
│   └── ChannelConfig
├── External / Staging
│   ├── ExternalWebhookEvent
│   ├── ExternalOrderRaw
│   ├── ExternalProductRaw
│   ├── ExternalFinanceRaw
│   ├── ExternalCategoryRaw
│   └── IntegrationEventInbox
├── Sync
│   ├── SyncJob
│   ├── SyncLog
│   ├── SyncError
│   └── ManualResyncRequest
├── Order
│   ├── OrderItem
│   ├── OrderStatusHistory
│   ├── OrderNote
│   └── Shipment
├── Customer
│   ├── CustomerNote
│   ├── CustomerTicket
│   └── CustomerTicketComment
├── Report
│   ├── ProfitFormula
│   ├── ProfitFormulaApproval
│   ├── ReportExportJob
│   └── ReportDataQualityIssue
├── Subscription
│   ├── Subscription
│   ├── PaymentRecord
│   └── Invoice
├── Notification
└── AuditLog
```

---

# 5. ID Strategy & Common Field Rules

## 5.1 ID Strategy

ShopHub nên dùng mô hình ID nhiều lớp để cân bằng giữa hiệu năng database, bảo mật API và tính dễ đọc trong vận hành.

| ID Type        | Example                                           | Purpose                                             | Expose to Frontend/API? | Notes                                                            |
| -------------- | ------------------------------------------------- | --------------------------------------------------- | ----------------------- | ---------------------------------------------------------------- |
| InternalId     | `OrderId = 12345`                                 | Khóa chính nội bộ DB, join FK, query hiệu năng cao. | No                      | Dạng BIGINT identity. Không dùng trực tiếp trên URL public.      |
| PublicId / Uid | `OrderUid = 9f31b0f7-2c2a-4f7e-bf21-2c5a86c45a01` | ID public dùng cho API, URL, frontend.              | Yes                     | Dạng UUID/GUID. Giúp tránh đoán ID và lộ quy mô dữ liệu.         |
| BusinessCode   | `ORD-2026-000123`, `SKU-001`                      | Mã người dùng nhìn thấy, tìm kiếm, đối soát.        | Yes                     | Không dùng làm primary key. Có thể unique trong shop tùy entity. |
| ExternalId     | `ExternalOrderId = LazadaOrder123`                | ID từ Lazada/API bên ngoài.                         | Conditional             | Dùng mapping/đối soát với sàn.                                   |

## 5.2 Recommended Entity ID Pattern

| Entity        | Internal PK             | Public ID                | Business Code          | External ID                 |
| ------------- | ----------------------- | ------------------------ | ---------------------- | --------------------------- |
| Shop          | `ShopId: long`          | `ShopUid: Guid`          | `ShopCode`             | N/A                         |
| UserAccount   | `UserId: long`          | `UserUid: Guid`          | N/A                    | N/A                         |
| Product       | `ProductId: long`       | `ProductUid: Guid`       | `ProductCode`          | Through mapping table       |
| ProductSku    | `ProductSkuId: long`    | `ProductSkuUid: Guid`    | `SkuCode`              | Through mapping table       |
| Order         | `OrderId: long`         | `OrderUid: Guid`         | `OrderCode`            | `ExternalOrderId`           |
| Customer      | `CustomerId: long`      | `CustomerUid: Guid`      | N/A                    | `ExternalCustomerId` if any |
| PaymentRecord | `PaymentRecordId: long` | `PaymentRecordUid: Guid` | `PaymentCode` optional | `ProviderTransactionId`     |
| Invoice       | `InvoiceId: long`       | `InvoiceUid: Guid`       | `InvoiceCode`          | Provider invoice id if any  |

## 5.3 ID Rules

| Rule ID          | Rule                                                                                                       |
| ---------------- | ---------------------------------------------------------------------------------------------------------- |
| RULE-DATA-ID-001 | Internal numeric ID dùng làm primary key và foreign key trong database.                                    |
| RULE-DATA-ID-002 | Public API, frontend URL và external-facing response nên dùng `Uid/PublicId`.                              |
| RULE-DATA-ID-003 | BusinessCode dùng để người dùng đọc/tìm kiếm, không dùng làm primary key.                                  |
| RULE-DATA-ID-004 | ExternalId từ sàn phải đi kèm ChannelConnectionId/ChannelCode để tránh trùng giữa kênh.                    |
| RULE-DATA-ID-005 | Khi nhận request từ frontend bằng Uid, backend resolve sang InternalId sau khi kiểm tra tenant/permission. |
| RULE-DATA-ID-006 | Không viết business logic dựa vào việc `Id = 1/2/3` trừ seed/test nội bộ có kiểm soát.                     |

## 5.4 Common Fields for Most Entities

| Field           | Meaning                | Required              | Notes                                                      |
| --------------- | ---------------------- | --------------------- | ---------------------------------------------------------- |
| Id / InternalId | Primary identifier     | Yes                   | BIGINT identity recommended for relational DB primary key. |
| Uid / PublicId  | Public identifier      | Yes for main entities | UUID/GUID used for API/URL/frontend.                       |
| CreatedAt       | Created timestamp      | Yes                   | UTC recommended.                                           |
| CreatedByUserId | Creator user ID        | Conditional           | Null nếu system-created.                                   |
| UpdatedAt       | Last updated timestamp | Yes                   | UTC recommended.                                           |
| UpdatedByUserId | Last updater user ID   | Conditional           | Null nếu system-updated.                                   |
| IsDeleted       | Soft delete flag       | Should                | Dùng với entity cần soft delete.                           |
| DeletedAt       | Deleted timestamp      | Conditional           | Nếu soft delete.                                           |
| RowVersion      | Concurrency token      | Should                | Giúp tránh update đè dữ liệu.                              |

## 5.5 Tenant-scoped Entity Fields

| Field     | Meaning                    | Required    | Notes                          |
| --------- | -------------------------- | ----------- | ------------------------------ |
| ShopId    | Shop/tenant owner of data  | Yes         | Bắt buộc với seller-side data. |
| ScopeType | Seller / Platform / System | Conditional | Dùng cho entity dùng chung.    |
| Source    | Internal / Lazada / System | Conditional | Dùng cho dữ liệu sync.         |

---

# 6. Core Enums / Statuses

## 6.1 ShopStatus

| Value     | Meaning                               |
| --------- | ------------------------------------- |
| Draft     | Shop mới tạo, chưa hoàn tất cấu hình. |
| Active    | Shop hoạt động bình thường.           |
| Suspended | Shop bị tạm khóa.                     |
| Cancelled | Shop đã hủy sử dụng dịch vụ.          |

## 6.2 SubscriptionStatus

| Value          | Meaning                     |
| -------------- | --------------------------- |
| Trial          | Đang dùng thử.              |
| Active         | Gói đang hoạt động.         |
| PendingPayment | Chờ thanh toán.             |
| PastDue        | Quá hạn thanh toán.         |
| Suspended      | Tạm khóa do billing/policy. |
| Cancelled      | Đã hủy subscription.        |

## 6.3 ShopHubOrderStatus - Draft

| Value       | Meaning                           | Inventory Impact                                           |
| ----------- | --------------------------------- | ---------------------------------------------------------- |
| Pending     | Đơn mới/chờ xác nhận.             | Chưa giữ hàng.                                             |
| Confirmed   | Đã xác nhận.                      | Available giảm, Reserved tăng.                             |
| Processing  | Đang xử lý/đóng gói.              | Giữ Reserved.                                              |
| ReadyToShip | Sẵn sàng giao.                    | Giữ Reserved.                                              |
| Shipping    | Đang giao.                        | Physical và Reserved giảm khi xác nhận xuất kho/giao hàng. |
| Completed   | Hoàn tất.                         | Không còn Reserved.                                        |
| Cancelled   | Đã hủy.                           | Nếu trước xuất kho: Available tăng, Reserved giảm.         |
| Returned    | Hoàn/trả.                         | Có thể tăng Physical nếu nhập lại.                         |
| Failed      | Lỗi/xử lý thất bại.               | Tùy case.                                                  |
| Unmapped    | Trạng thái sàn chưa mapping được. | Không tự xử lý tồn kho nếu chưa rõ.                        |

## 6.4 ProductStatus

| Value       | Meaning                        |
| ----------- | ------------------------------ |
| Draft       | Sản phẩm mới tạo, chưa bán.    |
| Active      | Đang bán.                      |
| Inactive    | Tạm ngừng bán.                 |
| OutOfStock  | Hết hàng khả dụng.             |
| SyncPending | Chờ đồng bộ.                   |
| SyncError   | Đồng bộ lỗi.                   |
| Archived    | Lưu trữ, không còn dùng chính. |

## 6.5 PaymentStatus

| Value          | Meaning                                 |
| -------------- | --------------------------------------- |
| PendingPayment | Đã tạo yêu cầu thanh toán, chờ kết quả. |
| Paid           | Thanh toán thành công và đã xác minh.   |
| Failed         | Thanh toán thất bại.                    |
| Expired        | QR/session thanh toán hết hạn.          |
| Cancelled      | Giao dịch bị hủy.                       |
| NeedReview     | Cần reconciliation/manual review.       |

## 6.6 SyncStatus

| Value          | Meaning               |
| -------------- | --------------------- |
| NotStarted     | Chưa sync.            |
| Pending        | Đang chờ sync.        |
| Running        | Đang chạy.            |
| Success        | Thành công.           |
| Error          | Có lỗi.               |
| Retrying       | Đang retry.           |
| Failed         | Thất bại sau retry.   |
| AuthError      | Lỗi token/credential. |
| RateLimited    | Bị giới hạn API.      |
| PartialSuccess | Thành công một phần.  |

## 6.7 ApprovalStatus

| Value           | Meaning     |
| --------------- | ----------- |
| Draft           | Nháp.       |
| PendingApproval | Chờ duyệt.  |
| Approved        | Đã duyệt.   |
| Rejected        | Bị từ chối. |
| Cancelled       | Bị hủy.     |

## 6.8 TicketStatus

| Value           | Meaning             |
| --------------- | ------------------- |
| Open            | Ticket mới.         |
| InProgress      | Đang xử lý.         |
| WaitingCustomer | Chờ phản hồi khách. |
| Resolved        | Đã xử lý.           |
| Closed          | Đã đóng.            |

## 6.9 TicketPriority

| Value  | Meaning             |
| ------ | ------------------- |
| Low    | Ưu tiên thấp.       |
| Medium | Ưu tiên trung bình. |
| High   | Ưu tiên cao.        |
| Urgent | Khẩn cấp.           |

## 6.10 StagingProcessingStatus

| Value      | Meaning                       |
| ---------- | ----------------------------- |
| Received   | Đã nhận dữ liệu raw.          |
| Pending    | Chờ xử lý.                    |
| Processing | Đang xử lý.                   |
| Processed  | Đã xử lý thành công.          |
| Failed     | Xử lý lỗi.                    |
| Ignored    | Bỏ qua có chủ đích.           |
| DeadLetter | Quá số lần retry, cần review. |

---

# 7. Tenant / Shop Data Model

## 7.1 Entity: Shop

### Purpose

Đại diện cho một shop/tenant sử dụng ShopHub.

| Field                 | Type Direction | Required    | Description                       | Notes                     |
| --------------------- | -------------- | ----------- | --------------------------------- | ------------------------- |
| ShopId                | Long           | Yes         | Internal ID shop.                 | Primary key.              |
| ShopUid               | Guid           | Yes         | Public ID.                        | Dùng cho API/URL nếu cần. |
| ShopCode              | String         | Yes         | Mã định danh shop.                | Unique.                   |
| ShopName              | String         | Yes         | Tên shop.                         |                           |
| OwnerUserId           | Long           | Yes         | User owner chính.                 | FK UserAccount.           |
| Status                | ShopStatus     | Yes         | Draft/Active/Suspended/Cancelled. |                           |
| DefaultLanguage       | String         | Yes         | Ngôn ngữ mặc định.                | `vi-VN`.                  |
| DefaultCurrency       | String         | Yes         | Tiền tệ mặc định.                 | `VND`.                    |
| PhysicalWarehouseId   | Long           | Conditional | Kho vật lý mặc định.              | Phase đầu mỗi shop 1 kho. |
| CurrentSubscriptionId | Long           | Conditional | Subscription hiện tại.            |                           |
| CreatedAt             | DateTime       | Yes         | Ngày tạo.                         | UTC.                      |
| UpdatedAt             | DateTime       | Yes         | Ngày cập nhật.                    | UTC.                      |

### Rules

| Rule ID            | Rule                                                        |
| ------------------ | ----------------------------------------------------------- |
| RULE-SHOP-DATA-001 | Mọi dữ liệu seller-side phải truy vết được về ShopId.       |
| RULE-SHOP-DATA-002 | Shop Suspended có thể bị hạn chế tạo/sửa dữ liệu nghiệp vụ. |
| RULE-SHOP-DATA-003 | Không hard delete shop nếu đã có dữ liệu nghiệp vụ.         |

---

## 7.2 Entity: ShopSetting

| Field                              | Type Direction | Required    | Description                           | Notes                                   |
| ---------------------------------- | -------------- | ----------- | ------------------------------------- | --------------------------------------- |
| ShopSettingId                      | Long           | Yes         | Internal ID setting.                  |                                         |
| ShopSettingUid                     | Guid           | Yes         | Public ID.                            |                                         |
| ShopId                             | Long           | Yes         | Shop owner.                           |                                         |
| InventoryAdjustmentThresholdAmount | Decimal        | Yes         | Ngưỡng tiền duyệt điều chỉnh tồn kho. | Mặc định 10.000.000 VND/sản phẩm.       |
| DefaultLowStockThreshold           | Integer        | No          | Ngưỡng cảnh báo tồn thấp mặc định.    | Có thể override theo product sau.       |
| ProfitFormulaId                    | Long           | Conditional | Công thức lợi nhuận hiện hành.        |                                         |
| Timezone                           | String         | Yes         | Timezone shop.                        | Default Asia/Ho_Chi_Minh hoặc cấu hình. |
| ReportExportDefaultFormat          | String         | No          | Excel/PDF.                            | Optional.                               |
| CreatedAt                          | DateTime       | Yes         |                                       |                                         |
| UpdatedAt                          | DateTime       | Yes         |                                       |                                         |

---

# 8. User / Role / Invitation Data Model

## 8.0 Role Storage Strategy

Role không nên được lưu hoặc xử lý bằng text hiển thị như `Shop Owner`, `Chủ shop`, `Owner`. Role cần có ID nội bộ và mã ổn định.

| Field        | Example                   | Purpose                                | Notes                                                         |
| ------------ | ------------------------- | -------------------------------------- | ------------------------------------------------------------- |
| RoleId       | `1`, `101`                | Primary key DB.                        | Có thể là BIGINT/INT identity hoặc seed cố định có kiểm soát. |
| RoleCode     | `ROLE-SHOP-OWNER`         | Mã ổn định dùng trong code/permission. | Field nên dùng trong business logic.                          |
| RoleName     | `Shop Owner` / `Chủ shop` | Tên hiển thị trên UI.                  | Có thể đổi/ngôn ngữ hóa mà không làm hỏng logic.              |
| ScopeType    | `Seller` / `Platform`     | Phân biệt role shop và role platform.  | Bắt buộc.                                                     |
| IsSystemRole | `true`                    | Role hệ thống hay role tùy chỉnh.      | Phase đầu ưu tiên system role.                                |

### Role Rules

| Rule ID            | Rule                                                                         |
| ------------------ | ---------------------------------------------------------------------------- |
| RULE-ROLE-DATA-001 | Bảng gán role chỉ lưu `RoleId`, không lưu text `Shop Owner` làm logic chính. |
| RULE-ROLE-DATA-002 | Business logic nên kiểm tra `RoleCode`, không kiểm tra `RoleName`.           |
| RULE-ROLE-DATA-003 | Không hard-code `RoleId == 1` trong code nghiệp vụ.                          |
| RULE-ROLE-DATA-004 | `RoleName` chỉ dùng để hiển thị, có thể dịch sang tiếng Việt/tiếng Anh.      |
| RULE-ROLE-DATA-005 | Seller-side role và platform-side role phải tách bằng `ScopeType`.           |

---

## 8.1 Entity: UserAccount

| Field        | Type Direction | Required    | Description                     | Notes                      |
| ------------ | -------------- | ----------- | ------------------------------- | -------------------------- |
| UserId       | Long           | Yes         | Internal ID user.               | Primary key.               |
| UserUid      | Guid           | Yes         | Public ID.                      | Dùng API/frontend.         |
| Email        | String         | Yes         | Email đăng nhập.                | Unique global recommended. |
| FullName     | String         | No          | Tên user.                       |                            |
| PasswordHash | String         | Conditional | Hash mật khẩu.                  | Không lưu plaintext.       |
| Status       | UserStatus     | Yes         | Active/Invited/Disabled/Locked. |                            |
| LastLoginAt  | DateTime       | No          | Lần login gần nhất.             |                            |
| CreatedAt    | DateTime       | Yes         |                                 |                            |
| UpdatedAt    | DateTime       | Yes         |                                 |                            |

Note: Khách mua hàng cuối không có `UserAccount` trong ShopHub.

---

## 8.2 Entity: Role

| Field        | Type Direction | Required | Description                                 | Notes                                                           |
| ------------ | -------------- | -------- | ------------------------------------------- | --------------------------------------------------------------- |
| RoleId       | Long/Int       | Yes      | Internal ID role.                           | Primary key DB.                                                 |
| RoleCode     | String         | Yes      | Mã role ổn định dùng trong code/permission. | Ví dụ `ROLE-SHOP-OWNER`. Unique.                                |
| RoleName     | String         | Yes      | Tên role hiển thị.                          | Ví dụ `Shop Owner` hoặc `Chủ shop`; không dùng làm logic chính. |
| ScopeType    | Enum           | Yes      | Seller / Platform.                          |                                                                 |
| IsSystemRole | Boolean        | Yes      | Role hệ thống hay custom.                   |                                                                 |
| Description  | String         | No       | Mô tả.                                      |                                                                 |

### Required Seller-side Roles

| Role Code             | Notes                                      |
| --------------------- | ------------------------------------------ |
| ROLE-SHOP-OWNER       | Toàn quyền shop.                           |
| ROLE-SHOP-MANAGER     | Quản lý vận hành, không quản lý user/role. |
| ROLE-ORDER-STAFF      | Xử lý đơn, hủy đơn nếu rule cho phép.      |
| ROLE-WAREHOUSE-STAFF  | Kho, stock in, adjustment request.         |
| ROLE-PRODUCT-STAFF    | Quản lý sản phẩm/danh mục.                 |
| ROLE-ACCOUNTANT       | Báo cáo, công thức lợi nhuận cần approval. |
| ROLE-CUSTOMER-SUPPORT | Ticket/chăm sóc khách hàng.                |
| ROLE-SHOP-VIEWER      | Chỉ xem.                                   |

### Required Platform-side Roles

| Role Code                      | Notes                                    |
| ------------------------------ | ---------------------------------------- |
| ROLE-SHOPHUB-SUPER-ADMIN       | Quyền cao nhất platform.                 |
| ROLE-SHOPHUB-ADMIN             | Quản trị vận hành platform.              |
| ROLE-SHOPHUB-SUPPORT           | Hỗ trợ, không impersonate user shop.     |
| ROLE-SHOPHUB-BILLING-ADMIN     | Quản lý billing, không đổi gói thủ công. |
| ROLE-SHOPHUB-INTEGRATION-ADMIN | Giám sát tích hợp/sync.                  |
| ROLE-SHOPHUB-AUDITOR           | Xem audit log.                           |

---

## 8.3 Entity: ShopUserMembership

| Field           | Type Direction | Required    | Description                      | Notes |
| --------------- | -------------- | ----------- | -------------------------------- | ----- |
| MembershipId    | Long           | Yes         | Internal ID membership.          |       |
| MembershipUid   | Guid           | Yes         | Public ID.                       |       |
| ShopId          | Long           | Yes         | Shop.                            |       |
| UserId          | Long           | Yes         | User.                            |       |
| Status          | Enum           | Yes         | Active/Invited/Disabled/Removed. |       |
| CreatedAt       | DateTime       | Yes         |                                  |       |
| CreatedByUserId | Long           | Conditional | Người tạo/mời.                   |       |

### Rules

| Rule ID              | Rule                                                                            |
| -------------------- | ------------------------------------------------------------------------------- |
| RULE-MEMBER-DATA-001 | Phase đầu 1 user seller-side chỉ thuộc 1 shop.                                  |
| RULE-MEMBER-DATA-002 | Các tài khoản không phải Shop Owner chỉ được tạo qua invitation của Shop Owner. |
| RULE-MEMBER-DATA-003 | Shop Manager không được quản lý user/role.                                      |

---

## 8.4 Entity: MembershipRole

| Field            | Type Direction | Required    | Description    | Notes                     |
| ---------------- | -------------- | ----------- | -------------- | ------------------------- |
| MembershipRoleId | Long           | Yes         | Internal ID.   |                           |
| MembershipId     | Long           | Yes         | Membership.    |                           |
| RoleId           | Long/Int       | Yes         | Role.          | Không lưu text role name. |
| AssignedAt       | DateTime       | Yes         | Thời điểm gán. |                           |
| AssignedByUserId | Long           | Conditional | Người gán.     |                           |

---

## 8.5 Entity: PlatformUserMembership

| Field                 | Type Direction | Required    | Description                      | Notes |
| --------------------- | -------------- | ----------- | -------------------------------- | ----- |
| PlatformMembershipId  | Long           | Yes         | Internal ID.                     |       |
| PlatformMembershipUid | Guid           | Yes         | Public ID.                       |       |
| UserId                | Long           | Yes         | User.                            |       |
| Status                | Enum           | Yes         | Active/Invited/Disabled/Removed. |       |
| CreatedAt             | DateTime       | Yes         |                                  |       |
| CreatedByUserId       | Long           | Conditional | Platform admin tạo.              |       |

---

## 8.6 Entity: PlatformMembershipRole

| Field                    | Type Direction | Required    | Description          | Notes |
| ------------------------ | -------------- | ----------- | -------------------- | ----- |
| PlatformMembershipRoleId | Long           | Yes         | Internal ID.         |       |
| PlatformMembershipId     | Long           | Yes         | Platform membership. |       |
| RoleId                   | Long/Int       | Yes         | Platform role.       |       |
| AssignedAt               | DateTime       | Yes         | Thời điểm gán.       |       |
| AssignedByUserId         | Long           | Conditional | Người gán.           |       |

---

## 8.7 Entity: Invitation

| Field               | Type Direction | Required    | Description                         | Notes                      |
| ------------------- | -------------- | ----------- | ----------------------------------- | -------------------------- |
| InvitationId        | Long           | Yes         | Internal ID lời mời.                |                            |
| InvitationUid       | Guid           | Yes         | Public ID.                          |                            |
| ScopeType           | Enum           | Yes         | Seller / Platform.                  |                            |
| ShopId              | Long           | Conditional | Bắt buộc nếu Seller invitation.     |                            |
| InvitedEmail        | String         | Yes         | Email được mời.                     | OTP gửi đến email này.     |
| InvitedByUserId     | Long           | Yes         | Người mời.                          |                            |
| InvitationTokenHash | String         | Yes         | Hash token invitation.              | Không lưu token plaintext. |
| OtpHash             | String         | Conditional | Hash OTP.                           | Không lưu OTP plaintext.   |
| OtpExpiresAt        | DateTime       | Conditional | Hạn OTP.                            | Cần chốt.                  |
| ExpiresAt           | DateTime       | Yes         | Hạn invitation.                     | Cần chốt.                  |
| Status              | Enum           | Yes         | Pending/Accepted/Expired/Cancelled. |                            |
| AcceptedAt          | DateTime       | No          | Thời điểm accept.                   |                            |
| AcceptedByUserId    | Long           | No          | User accept.                        |                            |

### Relationships

| Relationship                | Type     | Notes                            |
| --------------------------- | -------- | -------------------------------- |
| Invitation - InvitationRole | 1 - many | Role sẽ được gán sau khi accept. |

### Rules

| Rule ID              | Rule                                               |
| -------------------- | -------------------------------------------------- |
| RULE-INVITE-DATA-001 | OTP chỉ gửi đến `InvitedEmail`.                    |
| RULE-INVITE-DATA-002 | Invitation token và OTP không lưu plaintext.       |
| RULE-INVITE-DATA-003 | User accept phải có email khớp với `InvitedEmail`. |
| RULE-INVITE-DATA-004 | Invitation đã accepted không dùng lại được.        |

---

# 9. Product / Category Data Model

## 9.1 Entity: Category

| Field            | Type Direction | Required | Description              | Notes                       |
| ---------------- | -------------- | -------- | ------------------------ | --------------------------- |
| CategoryId       | Long           | Yes      | Internal ID category.    |                             |
| CategoryUid      | Guid           | Yes      | Public ID.               |                             |
| ShopId           | Long           | Yes      | Shop owner.              |                             |
| ParentCategoryId | Long           | No       | Category cha.            | Nếu hỗ trợ phân cấp.        |
| CategoryName     | String         | Yes      | Tên danh mục.            |                             |
| CategoryCode     | String         | No       | Mã danh mục.             | Unique trong shop nếu dùng. |
| Status           | Enum           | Yes      | Active/Inactive.         |                             |
| LazadaCategoryId | String         | No       | Mapping category Lazada. | Chờ API verify.             |
| CreatedAt        | DateTime       | Yes      |                          |                             |
| UpdatedAt        | DateTime       | Yes      |                          |                             |

---

## 9.2 Entity: Product

| Field             | Type Direction | Required    | Description                    | Notes                       |
| ----------------- | -------------- | ----------- | ------------------------------ | --------------------------- |
| ProductId         | Long           | Yes         | Internal ID sản phẩm.          |                             |
| ProductUid        | Guid           | Yes         | Public ID.                     |                             |
| ShopId            | Long           | Yes         | Shop owner.                    |                             |
| CategoryId        | Long           | Conditional | Danh mục.                      |                             |
| ProductCode       | String         | No          | Mã nội bộ.                     | Có thể unique trong shop.   |
| ProductName       | String         | Yes         | Tên sản phẩm.                  |                             |
| Description       | Text           | No          | Mô tả.                         |                             |
| SellingPrice      | Decimal        | Yes         | Giá bán.                       | VND.                        |
| CostPrice         | Decimal        | Should      | Giá vốn.                       | Cần cho profit report.      |
| NormalizedStatus  | ProductStatus  | Yes         | Trạng thái ShopHub.            |                             |
| CustomStatus      | String         | No          | Trạng thái tùy chỉnh của shop. | Không dùng thay enum chính. |
| MarketplaceStatus | String         | No          | Trạng thái gốc từ Lazada.      | Raw status.                 |
| SyncStatus        | SyncStatus     | Yes         | Trạng thái sync.               |                             |
| IsArchived        | Boolean        | Yes         | Đã lưu trữ.                    |                             |
| CreatedAt         | DateTime       | Yes         |                                |                             |
| UpdatedAt         | DateTime       | Yes         |                                |                             |

### Rules

| Rule ID            | Rule                                                          |
| ------------------ | ------------------------------------------------------------- |
| RULE-PROD-DATA-001 | Product thuộc duy nhất một ShopId.                            |
| RULE-PROD-DATA-002 | Không hard delete product đã phát sinh order.                 |
| RULE-PROD-DATA-003 | Product status phải tách marketplace/normalized/custom.       |
| RULE-PROD-DATA-004 | Sửa field cần sync Lazada phải tạo SyncJob hoặc SyncPending.  |
| RULE-PROD-DATA-005 | CostPrice thiếu thì profit report phải cảnh báo data quality. |

---

## 9.3 Entity: ProductSku

| Field         | Type Direction | Required    | Description      | Notes                  |
| ------------- | -------------- | ----------- | ---------------- | ---------------------- |
| ProductSkuId  | Long           | Yes         | Internal ID SKU. |                        |
| ProductSkuUid | Guid           | Yes         | Public ID.       |                        |
| ProductId     | Long           | Yes         | Product cha.     |                        |
| ShopId        | Long           | Yes         | Shop owner.      | Denormalized để query. |
| SkuCode       | String         | Yes         | Mã SKU.          | Unique trong shop.     |
| SkuName       | String         | No          | Tên variant.     |                        |
| SellingPrice  | Decimal        | Conditional | Giá SKU.         | Nếu khác product.      |
| CostPrice     | Decimal        | Conditional | Giá vốn SKU.     | Nếu khác product.      |
| Status        | ProductStatus  | Yes         | Trạng thái SKU.  |                        |
| CreatedAt     | DateTime       | Yes         |                  |                        |
| UpdatedAt     | DateTime       | Yes         |                  |                        |

---

## 9.4 Entity: ProductMarketplaceMapping

| Field                        | Type Direction | Required    | Description          | Notes   |
| ---------------------------- | -------------- | ----------- | -------------------- | ------- |
| ProductMarketplaceMappingId  | Long           | Yes         | Internal ID mapping. |         |
| ProductMarketplaceMappingUid | Guid           | Yes         | Public ID.           |         |
| ShopId                       | Long           | Yes         | Shop.                |         |
| ChannelConnectionId          | Long           | Yes         | Kênh bán.            | Lazada. |
| ProductId                    | Long           | Yes         | Product nội bộ.      |         |
| ProductSkuId                 | Long           | Conditional | SKU nội bộ.          |         |
| ExternalProductId            | String         | Yes         | Product ID trên sàn. |         |
| ExternalSkuId                | String         | No          | SKU ID trên sàn.     |         |
| ExternalSkuCode              | String         | No          | SKU code từ sàn.     |         |
| MarketplaceStatus            | String         | No          | Status gốc.          |         |
| LastSyncedAt                 | DateTime       | No          | Lần sync cuối.       |         |
| SyncStatus                   | SyncStatus     | Yes         | Trạng thái sync.     |         |

---

# 10. Inventory Data Model

## 10.1 Entity: Warehouse

| Field         | Type Direction | Required | Description      | Notes                   |
| ------------- | -------------- | -------- | ---------------- | ----------------------- |
| WarehouseId   | Long           | Yes      | Internal ID kho. |                         |
| WarehouseUid  | Guid           | Yes      | Public ID.       |                         |
| ShopId        | Long           | Yes      | Shop.            |                         |
| WarehouseName | String         | Yes      | Tên kho.         | Default warehouse.      |
| WarehouseType | Enum           | Yes      | Physical.        | Phase đầu chỉ Physical. |
| Status        | Enum           | Yes      | Active/Inactive. |                         |
| CreatedAt     | DateTime       | Yes      |                  |                         |

### Rules

| Rule ID          | Rule                                                 |
| ---------------- | ---------------------------------------------------- |
| RULE-WH-DATA-001 | Phase đầu mỗi shop chỉ có 1 warehouse type Physical. |
| RULE-WH-DATA-002 | Multi-warehouse để deferred.                         |

---

## 10.2 Entity: InventoryStock

| Field             | Type Direction | Required    | Description               | Notes             |
| ----------------- | -------------- | ----------- | ------------------------- | ----------------- |
| InventoryStockId  | Long           | Yes         | Internal ID tồn kho.      |                   |
| InventoryStockUid | Guid           | Yes         | Public ID.                |                   |
| ShopId            | Long           | Yes         | Shop.                     |                   |
| WarehouseId       | Long           | Yes         | Kho vật lý.               |                   |
| ProductId         | Long           | Yes         | Product.                  |                   |
| ProductSkuId      | Long           | Conditional | SKU nếu có.               |                   |
| PhysicalStock     | Integer        | Yes         | Hàng thực tế trong kho.   |                   |
| AvailableStock    | Integer        | Yes         | Hàng có thể bán/sync sàn. |                   |
| ReservedStock     | Integer        | Yes         | Hàng đã giữ cho đơn.      |                   |
| LowStockThreshold | Integer        | No          | Ngưỡng cảnh báo.          | Product-specific. |
| LastMovementAt    | DateTime       | No          | Lần biến động gần nhất.   |                   |
| RowVersion        | Token          | Should      | Concurrency.              | Bắt buộc nên có.  |

### Rules

| Rule ID             | Rule                                                                                   |
| ------------------- | -------------------------------------------------------------------------------------- |
| RULE-STOCK-DATA-001 | PhysicalStock, AvailableStock, ReservedStock không được âm nếu không có rule đặc biệt. |
| RULE-STOCK-DATA-002 | Confirm order: AvailableStock giảm, ReservedStock tăng.                                |
| RULE-STOCK-DATA-003 | Ship/Stock out: PhysicalStock giảm, ReservedStock giảm.                                |
| RULE-STOCK-DATA-004 | Cancel before stock out: AvailableStock tăng, ReservedStock giảm.                      |
| RULE-STOCK-DATA-005 | Stock In: PhysicalStock tăng, AvailableStock tăng nếu có thể bán.                      |
| RULE-STOCK-DATA-006 | AvailableStock thay đổi phải tạo sync job Lazada nếu product đang sync.                |

---

## 10.3 Entity: InventoryMovement

| Field                      | Type Direction | Required    | Description                                                                  | Notes                    |
| -------------------------- | -------------- | ----------- | ---------------------------------------------------------------------------- | ------------------------ |
| InventoryMovementId        | Long           | Yes         | Internal ID movement.                                                        |                          |
| InventoryMovementUid       | Guid           | Yes         | Public ID.                                                                   |                          |
| ShopId                     | Long           | Yes         | Shop.                                                                        |                          |
| WarehouseId                | Long           | Yes         | Warehouse.                                                                   |                          |
| ProductId                  | Long           | Yes         | Product.                                                                     |                          |
| ProductSkuId               | Long           | Conditional | SKU.                                                                         |                          |
| MovementType               | Enum           | Yes         | StockIn, OrderConfirmReserve, StockOut, CancelRelease, ReturnIn, Adjustment. |                          |
| PhysicalDelta              | Integer        | Yes         | Thay đổi Physical.                                                           | Có thể 0.                |
| AvailableDelta             | Integer        | Yes         | Thay đổi Available.                                                          | Có thể 0.                |
| ReservedDelta              | Integer        | Yes         | Thay đổi Reserved.                                                           | Có thể 0.                |
| Quantity                   | Integer        | Yes         | Số lượng nghiệp vụ chính.                                                    |                          |
| Reason                     | String         | Conditional | Lý do.                                                                       | Bắt buộc với adjustment. |
| EvidenceFileUrl            | String         | No          | Bằng chứng.                                                                  | Nếu có.                  |
| RelatedOrderId             | Long           | No          | Đơn liên quan.                                                               |                          |
| RelatedAdjustmentRequestId | Long           | No          | Request liên quan.                                                           |                          |
| CreatedAt                  | DateTime       | Yes         |                                                                              |                          |
| CreatedByUserId            | Long           | Conditional |                                                                              | Null nếu system.         |

---

## 10.4 Entity: InventoryAdjustmentRequest

| Field                  | Type Direction | Required    | Description                        | Notes                                     |
| ---------------------- | -------------- | ----------- | ---------------------------------- | ----------------------------------------- |
| AdjustmentRequestId    | Long           | Yes         | Internal ID request.               |                                           |
| AdjustmentRequestUid   | Guid           | Yes         | Public ID.                         |                                           |
| ShopId                 | Long           | Yes         | Shop.                              |                                           |
| WarehouseId            | Long           | Yes         | Warehouse.                         |                                           |
| ProductId              | Long           | Yes         | Product.                           |                                           |
| ProductSkuId           | Long           | Conditional | SKU.                               |                                           |
| RequestedQuantityDelta | Integer        | Yes         | Số lượng muốn điều chỉnh.          | Có thể âm/dương.                          |
| EstimatedValueImpact   | Decimal        | Yes         | Giá trị chênh lệch ước tính.       | ABS(delta) × CostPrice hoặc SellingPrice. |
| ImpactLevel            | Enum           | Yes         | Low/High/Critical.                 | Dựa theo threshold shop.                  |
| Reason                 | Text           | Yes         | Lý do.                             | Bắt buộc.                                 |
| EvidenceFileUrl        | String         | Should      | Bằng chứng.                        |                                           |
| Status                 | ApprovalStatus | Yes         | PendingApproval/Approved/Rejected. |                                           |
| RequestedByUserId      | Long           | Yes         | Warehouse Staff.                   |                                           |
| ApprovedByUserId       | Long           | No          | Người duyệt.                       |                                           |
| ApprovedAt             | DateTime       | No          |                                    |                                           |
| RejectedReason         | Text           | No          | Lý do từ chối.                     |                                           |

---

# 11. Order / Shipment Data Model

## 11.1 Entity: Order

| Field                  | Type Direction     | Required    | Description                       | Notes                       |
| ---------------------- | ------------------ | ----------- | --------------------------------- | --------------------------- |
| OrderId                | Long               | Yes         | Internal ID.                      |                             |
| OrderUid               | Guid               | Yes         | Public ID.                        |                             |
| ShopId                 | Long               | Yes         | Shop.                             |                             |
| ChannelConnectionId    | Long               | Conditional | Kênh bán.                         | Lazada nếu từ Lazada.       |
| ExternalOrderId        | String             | Conditional | ID đơn từ sàn.                    |                             |
| OrderCode              | String             | Yes         | Mã đơn hiển thị.                  |                             |
| CustomerId             | Long               | Conditional | Customer từ đơn.                  |                             |
| MarketplaceStatus      | String             | Conditional | Trạng thái gốc từ Lazada.         |                             |
| ShopHubStatus          | ShopHubOrderStatus | Yes         | Trạng thái chuẩn hóa.             |                             |
| SyncStatus             | SyncStatus         | Yes         | Trạng thái sync.                  |                             |
| OrderTotalAmount       | Decimal            | Yes         | Tổng tiền đơn.                    |                             |
| Currency               | String             | Yes         | VND.                              |                             |
| OrderedAt              | DateTime           | Conditional | Thời điểm đặt đơn.                |                             |
| ConfirmedAt            | DateTime           | No          | Xác nhận đơn.                     |                             |
| CancelledAt            | DateTime           | No          | Hủy đơn.                          |                             |
| CompletedAt            | DateTime           | No          | Hoàn tất.                         |                             |
| RawMarketplaceDataJson | Json/Text          | No          | Snapshot dữ liệu sàn sau mapping. | Không thay thế staging raw. |
| CreatedAt              | DateTime           | Yes         |                                   |                             |
| UpdatedAt              | DateTime           | Yes         |                                   |                             |
| RowVersion             | Token              | Should      | Concurrency.                      |                             |

### Rules

| Rule ID             | Rule                                                                                          |
| ------------------- | --------------------------------------------------------------------------------------------- |
| RULE-ORDER-DATA-001 | Order phải lưu cả MarketplaceStatus và ShopHubStatus.                                         |
| RULE-ORDER-DATA-002 | Nếu status sàn chưa mapping được, ShopHubStatus có thể là Unmapped và không tự xử lý tồn kho. |
| RULE-ORDER-DATA-003 | Không ghi đè order mới bằng dữ liệu sync cũ.                                                  |
| RULE-ORDER-DATA-004 | Mọi thay đổi trạng thái order phải có OrderStatusHistory.                                     |
| RULE-ORDER-DATA-005 | Hủy đơn phải có confirm và audit log.                                                         |

---

## 11.2 Entity: OrderItem

| Field               | Type Direction | Required    | Description            | Notes                                  |
| ------------------- | -------------- | ----------- | ---------------------- | -------------------------------------- |
| OrderItemId         | Long           | Yes         | Internal ID item.      |                                        |
| OrderItemUid        | Guid           | Yes         | Public ID.             |                                        |
| OrderId             | Long           | Yes         | Order.                 |                                        |
| ShopId              | Long           | Yes         | Shop.                  | Denormalized.                          |
| ProductId           | Long           | Conditional | Product nội bộ.        | Có thể null nếu chưa mapping.          |
| ProductSkuId        | Long           | Conditional | SKU nội bộ.            |                                        |
| ExternalProductId   | String         | No          | Product ID sàn.        |                                        |
| ExternalSkuId       | String         | No          | SKU ID sàn.            |                                        |
| ProductNameSnapshot | String         | Yes         | Tên tại thời điểm đặt. | Không phụ thuộc product name hiện tại. |
| SkuSnapshot         | String         | No          | SKU snapshot.          |                                        |
| Quantity            | Integer        | Yes         | Số lượng.              | > 0.                                   |
| UnitPrice           | Decimal        | Yes         | Đơn giá.               |                                        |
| DiscountAmount      | Decimal        | No          | Giảm giá.              |                                        |
| TotalAmount         | Decimal        | Yes         | Thành tiền.            |                                        |
| CostPriceSnapshot   | Decimal        | Should      | Giá vốn snapshot.      | Phục vụ profit report.                 |

---

## 11.3 Entity: OrderStatusHistory

| Field                     | Type Direction     | Required    | Description                     | Notes                                 |
| ------------------------- | ------------------ | ----------- | ------------------------------- | ------------------------------------- |
| OrderStatusHistoryId      | Long               | Yes         | Internal ID.                    |                                       |
| OrderId                   | Long               | Yes         | Order.                          |                                       |
| ShopId                    | Long               | Yes         | Shop.                           |                                       |
| PreviousMarketplaceStatus | String             | No          | Status sàn trước.               |                                       |
| NewMarketplaceStatus      | String             | No          | Status sàn mới.                 |                                       |
| PreviousShopHubStatus     | ShopHubOrderStatus | No          | Status nội bộ trước.            |                                       |
| NewShopHubStatus          | ShopHubOrderStatus | Yes         | Status nội bộ mới.              |                                       |
| ChangeSource              | Enum               | Yes         | User/System/Lazada/Integration. |                                       |
| Reason                    | String             | No          | Lý do.                          | Bắt buộc khi cancel nếu rule yêu cầu. |
| CreatedAt                 | DateTime           | Yes         |                                 |                                       |
| CreatedByUserId           | Long               | Conditional | Null nếu system.                |                                       |

---

## 11.4 Entity: OrderNote

| Field           | Type Direction | Required | Description       | Notes                    |
| --------------- | -------------- | -------- | ----------------- | ------------------------ |
| OrderNoteId     | Long           | Yes      | Internal ID.      |                          |
| OrderNoteUid    | Guid           | Yes      | Public ID.        |                          |
| OrderId         | Long           | Yes      | Order.            |                          |
| ShopId          | Long           | Yes      | Shop.             |                          |
| NoteText        | Text           | Yes      | Nội dung ghi chú. |                          |
| Visibility      | Enum           | Yes      | Internal.         | Phase đầu internal only. |
| CreatedAt       | DateTime       | Yes      |                   |                          |
| CreatedByUserId | Long           | Yes      | Người tạo.        |                          |

---

## 11.5 Entity: Shipment

| Field                     | Type Direction | Required | Description                                             | Notes                        |
| ------------------------- | -------------- | -------- | ------------------------------------------------------- | ---------------------------- |
| ShipmentId                | Long           | Yes      | Internal ID shipment.                                   |                              |
| ShipmentUid               | Guid           | Yes      | Public ID.                                              |                              |
| ShopId                    | Long           | Yes      | Shop.                                                   |                              |
| OrderId                   | Long           | Yes      | Order.                                                  |                              |
| ShippingProviderCode      | String         | No       | Đơn vị vận chuyển.                                      | API provider để sau.         |
| TrackingCode              | String         | No       | Mã vận đơn.                                             |                              |
| MarketplaceShippingStatus | String         | No       | Status giao hàng từ sàn.                                |                              |
| ShopHubShippingStatus     | Enum           | Yes      | Pending/ReadyToShip/Shipping/Delivered/Failed/Returned. |                              |
| StockOutConfirmedAt       | DateTime       | No       | Thời điểm xác nhận xuất kho.                            |                              |
| StockOutConfirmedByUserId | Long           | No       | Người xác nhận.                                         | Warehouse Staff/Order Staff. |
| CreatedAt                 | DateTime       | Yes      |                                                         |                              |
| UpdatedAt                 | DateTime       | Yes      |                                                         |                              |

---

# 12. Customer / Ticket Data Model

## 12.1 Entity: Customer

| Field              | Type Direction | Required | Description           | Notes                            |
| ------------------ | -------------- | -------- | --------------------- | -------------------------------- |
| CustomerId         | Long           | Yes      | Internal ID customer. |                                  |
| CustomerUid        | Guid           | Yes      | Public ID.            |                                  |
| ShopId             | Long           | Yes      | Shop.                 |                                  |
| ExternalCustomerId | String         | No       | ID từ sàn nếu có.     |                                  |
| CustomerName       | String         | No       | Tên khách.            | Có thể mask theo quyền.          |
| Phone              | String         | No       | Số điện thoại.        | Dữ liệu nhạy cảm.                |
| Email              | String         | No       | Email khách nếu có.   | Dữ liệu nhạy cảm.                |
| AddressSnapshot    | Text/Json      | No       | Địa chỉ từ order.     | Có thể lưu theo order nhiều hơn. |
| LastOrderAt        | DateTime       | No       | Đơn gần nhất.         |                                  |
| TotalOrders        | Integer        | No       | Tổng số đơn.          | Có thể tính hoặc cache.          |
| CreatedAt          | DateTime       | Yes      |                       |                                  |
| UpdatedAt          | DateTime       | Yes      |                       |                                  |

### Rules

| Rule ID           | Rule                                         |
| ----------------- | -------------------------------------------- |
| RULE-CUS-DATA-001 | Customer không có login account.             |
| RULE-CUS-DATA-002 | Customer data chỉ dùng trong phạm vi shop.   |
| RULE-CUS-DATA-003 | Dữ liệu nhạy cảm cần mask theo role nếu cần. |

---

## 12.2 Entity: CustomerTicket

| Field           | Type Direction | Required    | Description             | Notes                  |
| --------------- | -------------- | ----------- | ----------------------- | ---------------------- |
| TicketId        | Long           | Yes         | Internal ID ticket.     |                        |
| TicketUid       | Guid           | Yes         | Public ID.              |                        |
| ShopId          | Long           | Yes         | Shop.                   |                        |
| CustomerId      | Long           | Conditional | Khách liên quan.        |                        |
| RelatedOrderId  | Long           | No          | Đơn liên quan.          |                        |
| Title           | String         | Yes         | Tiêu đề ticket.         |                        |
| Description     | Text           | Yes         | Nội dung vấn đề.        |                        |
| Status          | TicketStatus   | Yes         | Open/InProgress/...     |                        |
| Priority        | TicketPriority | Yes         | Low/Medium/High/Urgent. | Phase đầu có priority. |
| AssigneeUserId  | Long           | No          | Người phụ trách.        | Phase đầu có assignee. |
| CreatedByUserId | Long           | Yes         | Người tạo.              |                        |
| ResolvedAt      | DateTime       | No          | Thời điểm xử lý xong.   |                        |
| ClosedAt        | DateTime       | No          | Thời điểm đóng.         |                        |

---

## 12.3 Entity: CustomerTicketComment

| Field            | Type Direction | Required | Description          | Notes               |
| ---------------- | -------------- | -------- | -------------------- | ------------------- |
| TicketCommentId  | Long           | Yes      | Internal ID comment. |                     |
| TicketCommentUid | Guid           | Yes      | Public ID.           |                     |
| TicketId         | Long           | Yes      | Ticket.              |                     |
| ShopId           | Long           | Yes      | Shop.                |                     |
| CommentText      | Text           | Yes      | Nội dung.            |                     |
| IsInternal       | Boolean        | Yes      | Ghi chú nội bộ.      | Phase đầu internal. |
| CreatedByUserId  | Long           | Yes      | Người tạo.           |                     |
| CreatedAt        | DateTime       | Yes      |                      |                     |

---

# 13. External Integration / Staging Data Model

## 13.1 Purpose

Dữ liệu từ Lazada hoặc API/webhook bên ngoài **không nên cập nhật thẳng vào domain tables** như `Order`, `Product`, `InventoryStock`, `PaymentRecord`.

Luồng xử lý chuẩn:

```txt
Lazada API / Webhook
        ↓
External Raw / Staging Tables
        ↓
Validation + Deduplication + Mapping + Version Check
        ↓
Domain Tables chính
        ↓
SyncLog / SyncError / AuditLog / Notification
```

Mục tiêu:

* Không làm bẩn bảng nghiệp vụ chính bằng dữ liệu ngoài chưa validate.
* Có dữ liệu raw để debug/đối soát khi sync lỗi.
* Chống webhook/API gửi trùng.
* Chống dữ liệu cũ ghi đè dữ liệu mới.
* Tách rõ external payload với domain model nội bộ.

---

## 13.2 Entity: ExternalWebhookEvent

| Field                   | Type Direction          | Required    | Description                                   | Notes                                  |
| ----------------------- | ----------------------- | ----------- | --------------------------------------------- | -------------------------------------- |
| ExternalWebhookEventId  | Long                    | Yes         | Internal PK.                                  |                                        |
| ExternalWebhookEventUid | Guid                    | Yes         | Public/debug UID.                             |                                        |
| ChannelCode             | String                  | Yes         | Lazada, etc.                                  |                                        |
| ShopId                  | Long                    | Conditional | Shop nếu xác định được.                       | Có thể null trước mapping.             |
| ChannelConnectionId     | Long                    | Conditional | Connection nếu xác định được.                 |                                        |
| ExternalEventId         | String                  | Conditional | Event ID từ sàn nếu có.                       | Dùng chống trùng.                      |
| EventType               | String                  | Yes         | OrderUpdated/ProductUpdated/etc.              | Raw type.                              |
| EventOccurredAt         | DateTime                | No          | Thời điểm event từ sàn.                       |                                        |
| ReceivedAt              | DateTime                | Yes         | Thời điểm ShopHub nhận.                       |                                        |
| RawPayloadJson          | Json/Text               | Yes         | Payload raw.                                  | Mask/protect dữ liệu nhạy cảm nếu cần. |
| ProcessingStatus        | StagingProcessingStatus | Yes         | Received/Processing/Processed/Failed/Ignored. |                                        |
| ProcessingError         | Text                    | No          | Lỗi xử lý nếu có.                             |                                        |
| ProcessedAt             | DateTime                | No          | Đã xử lý lúc nào.                             |                                        |

### Rules

| Rule ID              | Rule                                                                                         |
| -------------------- | -------------------------------------------------------------------------------------------- |
| RULE-EXT-WEBHOOK-001 | Webhook phải được lưu trước khi xử lý domain nếu payload có giá trị nghiệp vụ.               |
| RULE-EXT-WEBHOOK-002 | Nếu có ExternalEventId, phải chống xử lý trùng.                                              |
| RULE-EXT-WEBHOOK-003 | Không tin tuyệt đối vào dữ liệu webhook; phải validate và mapping trước khi cập nhật domain. |

---

## 13.3 Entity: ExternalOrderRaw

| Field               | Type Direction | Required | Description                    | Notes                         |
| ------------------- | -------------- | -------- | ------------------------------ | ----------------------------- |
| ExternalOrderRawId  | Long           | Yes      | Internal PK.                   |                               |
| ExternalOrderRawUid | Guid           | Yes      | Public/debug UID.              |                               |
| ShopId              | Long           | Yes      | Shop sau khi xác định.         |                               |
| ChannelConnectionId | Long           | Yes      | Kênh bán.                      | Lazada.                       |
| ExternalOrderId     | String         | Yes      | ID đơn từ Lazada.              |                               |
| ExternalOrderCode   | String         | No       | Mã đơn từ sàn.                 |                               |
| MarketplaceStatus   | String         | Yes      | Status gốc từ sàn.             |                               |
| ExternalUpdatedAt   | DateTime       | No       | Thời điểm update từ sàn.       | Dùng chống dữ liệu cũ ghi đè. |
| RawPayloadJson      | Json/Text      | Yes      | Payload raw.                   |                               |
| MappingStatus       | Enum           | Yes      | Pending/Mapped/Failed/Ignored. |                               |
| MappedOrderId       | Long           | No       | Order nội bộ sau mapping.      |                               |
| MappingError        | Text           | No       | Lỗi mapping.                   |                               |
| ReceivedAt          | DateTime       | Yes      | Thời điểm nhận.                |                               |
| ProcessedAt         | DateTime       | No       | Thời điểm xử lý.               |                               |

### Rules

| Rule ID            | Rule                                                                                           |
| ------------------ | ---------------------------------------------------------------------------------------------- |
| RULE-EXT-ORDER-001 | Không tạo/cập nhật `Order` nếu raw order thiếu field bắt buộc.                                 |
| RULE-EXT-ORDER-002 | Nếu `ExternalUpdatedAt` cũ hơn dữ liệu đang có, không ghi đè domain order.                     |
| RULE-EXT-ORDER-003 | Nếu marketplace status chưa mapping được, domain order có thể giữ `Unmapped` và tạo SyncError. |

---

## 13.4 Entity: ExternalProductRaw

| Field                 | Type Direction | Required | Description                    | Notes |
| --------------------- | -------------- | -------- | ------------------------------ | ----- |
| ExternalProductRawId  | Long           | Yes      | Internal PK.                   |       |
| ExternalProductRawUid | Guid           | Yes      | Public/debug UID.              |       |
| ShopId                | Long           | Yes      | Shop.                          |       |
| ChannelConnectionId   | Long           | Yes      | Kênh bán.                      |       |
| ExternalProductId     | String         | Yes      | Product ID từ sàn.             |       |
| ExternalSkuId         | String         | No       | SKU ID từ sàn.                 |       |
| MarketplaceStatus     | String         | No       | Product/SKU status gốc.        |       |
| ExternalUpdatedAt     | DateTime       | No       | Update time từ sàn.            |       |
| RawPayloadJson        | Json/Text      | Yes      | Payload raw.                   |       |
| MappingStatus         | Enum           | Yes      | Pending/Mapped/Failed/Ignored. |       |
| MappedProductId       | Long           | No       | Product nội bộ.                |       |
| MappedProductSkuId    | Long           | No       | SKU nội bộ.                    |       |
| MappingError          | Text           | No       | Lỗi mapping.                   |       |
| ReceivedAt            | DateTime       | Yes      |                                |       |
| ProcessedAt           | DateTime       | No       |                                |       |

---

## 13.5 Entity: ExternalFinanceRaw

| Field                 | Type Direction | Required    | Description                                    | Notes                      |
| --------------------- | -------------- | ----------- | ---------------------------------------------- | -------------------------- |
| ExternalFinanceRawId  | Long           | Yes         | Internal PK.                                   |                            |
| ExternalFinanceRawUid | Guid           | Yes         | Public/debug UID.                              |                            |
| ShopId                | Long           | Conditional | Shop.                                          | Có thể null trước mapping. |
| SourceProvider        | String         | Yes         | Lazada/VNPay/etc.                              |                            |
| ChannelConnectionId   | Long           | No          | Nếu liên quan sàn.                             |                            |
| ExternalTransactionId | String         | Conditional | ID giao dịch nguồn ngoài.                      |                            |
| ExternalOrderId       | String         | No          | Đơn liên quan nếu có.                          |                            |
| FinanceDataType       | Enum           | Yes         | Fee/Payout/Transaction/Payment/Reconciliation. |                            |
| Amount                | Decimal        | No          | Số tiền nếu parse được.                        |                            |
| Currency              | String         | No          | VND/etc.                                       |                            |
| ExternalStatus        | String         | No          | Status gốc.                                    |                            |
| ExternalOccurredAt    | DateTime       | No          | Thời điểm từ nguồn ngoài.                      |                            |
| RawPayloadJson        | Json/Text      | Yes         | Payload raw.                                   |                            |
| MappingStatus         | Enum           | Yes         | Pending/Mapped/Failed/Ignored.                 |                            |
| MappingError          | Text           | No          | Lỗi mapping.                                   |                            |
| ReceivedAt            | DateTime       | Yes         |                                                |                            |
| ProcessedAt           | DateTime       | No          |                                                |                            |

---

## 13.6 Entity: ExternalCategoryRaw

| Field                    | Type Direction | Required | Description               | Notes |
| ------------------------ | -------------- | -------- | ------------------------- | ----- |
| ExternalCategoryRawId    | Long           | Yes      | Internal PK.              |       |
| ExternalCategoryRawUid   | Guid           | Yes      | Public/debug UID.         |       |
| ChannelCode              | String         | Yes      | Lazada.                   |       |
| ExternalCategoryId       | String         | Yes      | Category ID từ sàn.       |       |
| ParentExternalCategoryId | String         | No       | Category cha từ sàn.      |       |
| CategoryName             | String         | Yes      | Tên category từ sàn.      |       |
| AttributePayloadJson     | Json/Text      | No       | Attributes theo category. |       |
| BrandPayloadJson         | Json/Text      | No       | Brand list nếu có.        |       |
| RawPayloadJson           | Json/Text      | Yes      | Raw payload.              |       |
| SyncedAt                 | DateTime       | Yes      | Lần sync category.        |       |
| Status                   | Enum           | Yes      | Active/Inactive/Unknown.  |       |

---

## 13.7 Entity: IntegrationEventInbox

| Field                    | Type Direction          | Required    | Description                                     | Notes               |
| ------------------------ | ----------------------- | ----------- | ----------------------------------------------- | ------------------- |
| IntegrationEventInboxId  | Long                    | Yes         | Internal PK.                                    |                     |
| IntegrationEventInboxUid | Guid                    | Yes         | Public/debug UID.                               |                     |
| ShopId                   | Long                    | Conditional | Shop.                                           |                     |
| ChannelConnectionId      | Long                    | Conditional | Kênh.                                           |                     |
| SourceEntityType         | String                  | Yes         | ExternalWebhookEvent/ExternalOrderRaw/etc.      |                     |
| SourceEntityId           | Long                    | Yes         | ID staging source.                              |                     |
| EventType                | String                  | Yes         | Loại event cần xử lý.                           |                     |
| IdempotencyKey           | String                  | Yes         | Khóa chống xử lý trùng.                         | Unique recommended. |
| Status                   | StagingProcessingStatus | Yes         | Pending/Processing/Processed/Failed/DeadLetter. |                     |
| RetryCount               | Integer                 | Yes         | Số lần retry.                                   |                     |
| MaxRetryCount            | Integer                 | Yes         | Retry tối đa.                                   |                     |
| LastError                | Text                    | No          | Lỗi gần nhất.                                   |                     |
| AvailableAt              | DateTime                | Yes         | Khi nào được xử lý.                             | Hỗ trợ delay/retry. |
| CreatedAt                | DateTime                | Yes         |                                                 |                     |
| ProcessedAt              | DateTime                | No          |                                                 |                     |

### Rules

| Rule ID             | Rule                                                                                            |
| ------------------- | ----------------------------------------------------------------------------------------------- |
| RULE-INBOX-DATA-001 | Cập nhật domain từ external data nên đi qua inbox/event processor để đảm bảo retry/idempotency. |
| RULE-INBOX-DATA-002 | Mỗi event cần có IdempotencyKey để tránh xử lý trùng.                                           |
| RULE-INBOX-DATA-003 | Event xử lý quá số lần retry phải chuyển DeadLetter để review.                                  |

---

## 13.8 Staging Retention Direction

| Data Type             | Retention Direction                                                   | Notes                                  |
| --------------------- | --------------------------------------------------------------------- | -------------------------------------- |
| ExternalWebhookEvent  | 30/60/90 ngày tùy policy.                                             | Giữ để debug webhook.                  |
| ExternalOrderRaw      | Nên giữ lâu hơn webhook thường.                                       | Phục vụ đối soát đơn.                  |
| ExternalProductRaw    | Có thể giữ bản gần nhất + lịch sử lỗi.                                | Tránh storage quá lớn.                 |
| ExternalFinanceRaw    | Nên giữ lâu hơn.                                                      | Phục vụ đối soát tài chính/lợi nhuận.  |
| ExternalCategoryRaw   | Cache/update định kỳ.                                                 | Không cần lịch sử dài nếu không audit. |
| IntegrationEventInbox | Giữ processed trong thời gian giới hạn; DeadLetter giữ đến khi xử lý. |                                        |

---

# 14. Channel / Lazada / Sync Data Model

## 14.1 Entity: ChannelConnection

| Field                | Type Direction | Required | Description                                  | Notes             |
| -------------------- | -------------- | -------- | -------------------------------------------- | ----------------- |
| ChannelConnectionId  | Long           | Yes      | Internal ID connection.                      |                   |
| ChannelConnectionUid | Guid           | Yes      | Public ID.                                   |                   |
| ShopId               | Long           | Yes      | Shop.                                        |                   |
| ChannelCode          | String         | Yes      | Lazada, Shopee later, etc.                   | Phase đầu Lazada. |
| ChannelName          | String         | Yes      | Tên hiển thị.                                |                   |
| Status               | Enum           | Yes      | Connected/AuthError/Disconnected/NotStarted. |                   |
| ExternalSellerId     | String         | No       | Seller ID từ Lazada.                         |                   |
| ExternalShopName     | String         | No       | Tên shop trên sàn.                           |                   |
| ConnectedAt          | DateTime       | No       | Kết nối thành công.                          |                   |
| DisconnectedAt       | DateTime       | No       | Ngắt kết nối.                                |                   |
| LastHealthCheckAt    | DateTime       | No       | Kiểm tra gần nhất.                           |                   |
| CreatedAt            | DateTime       | Yes      |                                              |                   |
| UpdatedAt            | DateTime       | Yes      |                                              |                   |

---

## 14.2 Entity: ChannelCredential

| Field                 | Type Direction | Required    | Description                       | Notes                |
| --------------------- | -------------- | ----------- | --------------------------------- | -------------------- |
| CredentialId          | Long           | Yes         | Internal ID credential.           |                      |
| ChannelConnectionId   | Long           | Yes         | Kênh.                             |                      |
| AccessTokenEncrypted  | String         | Conditional | Token mã hóa.                     | Không lưu plaintext. |
| RefreshTokenEncrypted | String         | Conditional | Refresh token mã hóa.             |                      |
| TokenExpiresAt        | DateTime       | No          | Hạn token.                        |                      |
| Status                | Enum           | Yes         | Active/Expired/Revoked/AuthError. |                      |
| CreatedAt             | DateTime       | Yes         |                                   |                      |
| UpdatedAt             | DateTime       | Yes         |                                   |                      |

### Rules

| Rule ID            | Rule                            |
| ------------------ | ------------------------------- |
| RULE-CRED-DATA-001 | Token/secret phải mã hóa.       |
| RULE-CRED-DATA-002 | Không expose token ra frontend. |

---

## 14.3 Entity: SyncJob

| Field               | Type Direction | Required | Description                                  | Notes          |
| ------------------- | -------------- | -------- | -------------------------------------------- | -------------- |
| SyncJobId           | Long           | Yes      | Internal ID job.                             |                |
| SyncJobUid          | Guid           | Yes      | Public/debug UID.                            |                |
| ShopId              | Long           | Yes      | Shop.                                        |                |
| ChannelConnectionId | Long           | Yes      | Kênh.                                        |                |
| SyncType            | Enum           | Yes      | Order/Product/Stock/Fees/Category/Logistics. |                |
| TriggerType         | Enum           | Yes      | Webhook/Scheduled/Manual/SystemEvent.        |                |
| Status              | SyncStatus     | Yes      | Pending/Running/Success/Error...             |                |
| RequestedByUserId   | Long           | No       | Nếu manual.                                  |                |
| StartedAt           | DateTime       | No       |                                              |                |
| FinishedAt          | DateTime       | No       |                                              |                |
| RetryCount          | Integer        | Yes      | Số lần retry.                                | Default 0.     |
| MaxRetryCount       | Integer        | Yes      | Số lần retry tối đa.                         | Theo policy.   |
| ImpactLevel         | Enum           | No       | Low/High/Critical.                           | Manual resync. |

---

## 14.4 Entity: SyncLog

| Field               | Type Direction | Required    | Description                           | Notes        |
| ------------------- | -------------- | ----------- | ------------------------------------- | ------------ |
| SyncLogId           | Long           | Yes         | Internal ID log.                      |              |
| SyncLogUid          | Guid           | Yes         | Public/debug UID.                     |              |
| SyncJobId           | Long           | Conditional | Job liên quan.                        |              |
| ShopId              | Long           | Yes         | Shop.                                 |              |
| ChannelConnectionId | Long           | Yes         | Kênh.                                 |              |
| EntityType          | Enum           | Yes         | Order/Product/Stock/Fee/Category.     |              |
| InternalEntityId    | Long           | No          | ID nội bộ.                            |              |
| ExternalEntityId    | String         | No          | ID từ sàn.                            |              |
| Action              | Enum           | Yes         | Pull/Push/Create/Update/Delete/Retry. |              |
| Status              | SyncStatus     | Yes         | Kết quả.                              |              |
| Message             | Text           | No          | Thông tin ngắn.                       |              |
| RawRequestJson      | Json/Text      | No          | Request snapshot.                     | Có thể mask. |
| RawResponseJson     | Json/Text      | No          | Response snapshot.                    | Có thể mask. |
| CreatedAt           | DateTime       | Yes         |                                       |              |

---

## 14.5 Entity: SyncError

| Field            | Type Direction | Required    | Description                                              | Notes |
| ---------------- | -------------- | ----------- | -------------------------------------------------------- | ----- |
| SyncErrorId      | Long           | Yes         | Internal ID lỗi.                                         |       |
| SyncErrorUid     | Guid           | Yes         | Public/debug UID.                                        |       |
| SyncLogId        | Long           | Conditional | Log liên quan.                                           |       |
| ShopId           | Long           | Yes         | Shop.                                                    |       |
| ErrorType        | Enum           | Yes         | Auth/DataValidation/Mapping/RateLimit/Temporary/Unknown. |       |
| ErrorCode        | String         | No          | Code từ API hoặc nội bộ.                                 |       |
| ErrorMessage     | Text           | Yes         | Message.                                                 |       |
| IsRetryable      | Boolean        | Yes         | Có thể retry không.                                      |       |
| ResolvedAt       | DateTime       | No          | Đã xử lý.                                                |       |
| ResolvedByUserId | Long           | No          | Người xử lý.                                             |       |
| ResolutionNote   | Text           | No          | Ghi chú xử lý.                                           |       |

---

## 14.6 Entity: ManualResyncRequest

| Field                  | Type Direction | Required | Description                                  | Notes                  |
| ---------------------- | -------------- | -------- | -------------------------------------------- | ---------------------- |
| ManualResyncRequestId  | Long           | Yes      | Internal ID request.                         |                        |
| ManualResyncRequestUid | Guid           | Yes      | Public ID.                                   |                        |
| ShopId                 | Long           | Yes      | Shop.                                        |                        |
| ChannelConnectionId    | Long           | Yes      | Kênh.                                        |                        |
| RequestedScope         | Enum/List      | Yes      | Order/Product/Stock/Fees/Customer.           |                        |
| ImpactLevel            | Enum           | Yes      | Low/High/Critical.                           | Nếu không rõ thì high. |
| Status                 | ApprovalStatus | Yes      | PendingApproval/Approved/Rejected/Cancelled. |                        |
| RequestedByUserId      | Long           | Yes      | Người tạo.                                   |                        |
| ApprovedByUserId       | Long           | No       | Shop Owner nếu high impact.                  |                        |
| Reason                 | Text           | Yes      | Lý do resync.                                |                        |
| WarningMessageSnapshot | Text           | No       | Cảnh báo đã hiển thị.                        |                        |
| ExecutedSyncJobId      | Long           | No       | Job đã chạy.                                 |                        |
| CreatedAt              | DateTime       | Yes      |                                              |                        |

---

# 15. Report / Profit / Export Data Model

## 15.1 Entity: ProfitFormula

| Field                 | Type Direction | Required | Description                            | Notes                            |
| --------------------- | -------------- | -------- | -------------------------------------- | -------------------------------- |
| ProfitFormulaId       | Long           | Yes      | Internal ID công thức.                 |                                  |
| ProfitFormulaUid      | Guid           | Yes      | Public ID.                             |                                  |
| ShopId                | Long           | Yes      | Shop.                                  |                                  |
| FormulaName           | String         | Yes      | Tên công thức.                         |                                  |
| FormulaDefinitionJson | Json           | Yes      | Định nghĩa công thức.                  | Không dùng text không kiểm soát. |
| Status                | Enum           | Yes      | Draft/Active/Archived/PendingApproval. |                                  |
| EffectiveFrom         | DateTime       | No       | Hiệu lực từ.                           |                                  |
| CreatedByUserId       | Long           | Yes      | Người tạo.                             | Accountant/Owner.                |
| ApprovedByUserId      | Long           | No       | Người duyệt.                           |                                  |
| ApprovedAt            | DateTime       | No       |                                        |                                  |

### Formula Components - Draft

| Component       | Meaning             |
| --------------- | ------------------- |
| Revenue         | Doanh thu đơn hàng. |
| CostOfGoodsSold | Giá vốn sản phẩm.   |
| MarketplaceFees | Phí sàn.            |
| ShippingFees    | Phí vận chuyển.     |
| Discounts       | Giảm giá.           |
| OtherCosts      | Chi phí khác.       |

---

## 15.2 Entity: ProfitFormulaApproval

| Field                       | Type Direction | Required | Description                | Notes |
| --------------------------- | -------------- | -------- | -------------------------- | ----- |
| ApprovalId                  | Long           | Yes      | Internal ID approval.      |       |
| ApprovalUid                 | Guid           | Yes      | Public ID.                 |       |
| ShopId                      | Long           | Yes      | Shop.                      |       |
| ProfitFormulaId             | Long           | Yes      | Công thức nháp.            |       |
| PreviousFormulaSnapshotJson | Json           | Yes      | Công thức cũ.              |       |
| NewFormulaSnapshotJson      | Json           | Yes      | Công thức mới.             |       |
| RequestedByUserId           | Long           | Yes      | Accountant.                |       |
| Status                      | ApprovalStatus | Yes      | Pending/Approved/Rejected. |       |
| ApprovedByUserId            | Long           | No       | Owner/Manager.             |       |
| ApprovedAt                  | DateTime       | No       |                            |       |
| RejectedReason              | Text           | No       | Lý do từ chối.             |       |

---

## 15.3 Entity: ReportExportJob

| Field               | Type Direction | Required | Description                               | Notes      |
| ------------------- | -------------- | -------- | ----------------------------------------- | ---------- |
| ReportExportJobId   | Long           | Yes      | Internal ID export job.                   |            |
| ReportExportJobUid  | Guid           | Yes      | Public ID.                                |            |
| ShopId              | Long           | Yes      | Shop.                                     |            |
| ReportType          | Enum           | Yes      | Revenue/Profit/Inventory/Channel/Order.   |            |
| ExportFormat        | Enum           | Yes      | Excel/PDF.                                |            |
| SelectedColumnsJson | Json           | Yes      | Cột cần xuất.                             | User chọn. |
| FilterJson          | Json           | Yes      | Time range/filter.                        |            |
| Status              | Enum           | Yes      | Pending/Running/Completed/Failed/Expired. |            |
| FileUrl             | String         | No       | File export.                              |            |
| RequestedByUserId   | Long           | Yes      | User yêu cầu.                             |            |
| CreatedAt           | DateTime       | Yes      |                                           |            |
| CompletedAt         | DateTime       | No       |                                           |            |

---

## 15.4 Entity: ReportDataQualityIssue

| Field               | Type Direction | Required | Description                                        | Notes |
| ------------------- | -------------- | -------- | -------------------------------------------------- | ----- |
| DataQualityIssueId  | Long           | Yes      | Internal ID issue.                                 |       |
| DataQualityIssueUid | Guid           | Yes      | Public ID.                                         |       |
| ShopId              | Long           | Yes      | Shop.                                              |       |
| ReportType          | Enum           | Yes      | Profit/Revenue/etc.                                |       |
| RelatedEntityType   | String         | No       | Product/Order/Fee.                                 |       |
| RelatedEntityId     | Long           | No       | Entity liên quan.                                  |       |
| IssueType           | Enum           | Yes      | MissingCost/MissingFee/MissingMapping/InvalidData. |       |
| Severity            | Enum           | Yes      | Low/Medium/High.                                   |       |
| Message             | Text           | Yes      | Nội dung cảnh báo.                                 |       |
| IsResolved          | Boolean        | Yes      | Đã xử lý chưa.                                     |       |
| CreatedAt           | DateTime       | Yes      |                                                    |       |

---

# 16. Subscription / Payment / Invoice Data Model

## 16.1 Entity: Plan

| Field                | Type Direction | Required | Description                | Notes                    |
| -------------------- | -------------- | -------- | -------------------------- | ------------------------ |
| PlanId               | Long/Int       | Yes      | Internal ID gói.           |                          |
| PlanCode             | String         | Yes      | Trial/Pro/Premium/etc.     | Logic nên dùng PlanCode. |
| PlanName             | String         | Yes      | Tên gói.                   | Display name.            |
| PriceAmount          | Decimal        | Yes      | Giá.                       | VND.                     |
| BillingCycle         | Enum           | Yes      | Monthly/Yearly/etc.        |                          |
| MaxUsers             | Integer        | Yes      | Giới hạn user.             |                          |
| MaxChannels          | Integer        | Yes      | Giới hạn kênh.             |                          |
| MaxOrdersPerMonth    | Integer        | No       | Giới hạn đơn/tháng nếu có. |                          |
| AllowAdvancedReports | Boolean        | Yes      | Có báo cáo nâng cao không. |                          |
| AllowAuditLog        | Boolean        | Yes      | Có audit log không.        |                          |
| SyncPriority         | Enum           | No       | Normal/High.               |                          |
| Status               | Enum           | Yes      | Active/Inactive/Archived.  |                          |

---

## 16.2 Entity: Subscription

| Field               | Type Direction     | Required    | Description               | Notes |
| ------------------- | ------------------ | ----------- | ------------------------- | ----- |
| SubscriptionId      | Long               | Yes         | Internal ID subscription. |       |
| SubscriptionUid     | Guid               | Yes         | Public ID.                |       |
| ShopId              | Long               | Yes         | Shop.                     |       |
| PlanId              | Long/Int           | Yes         | Gói hiện tại.             |       |
| Status              | SubscriptionStatus | Yes         | Trial/Active/PastDue/etc. |       |
| StartedAt           | DateTime           | Yes         | Bắt đầu.                  |       |
| CurrentPeriodStart  | DateTime           | Conditional | Kỳ hiện tại.              |       |
| CurrentPeriodEnd    | DateTime           | Conditional | Kỳ kết thúc.              |       |
| CancelledAt         | DateTime           | No          | Hủy.                      |       |
| SuspendedAt         | DateTime           | No          | Tạm khóa.                 |       |
| LastPaymentRecordId | Long               | No          | Payment gần nhất.         |       |

---

## 16.3 Entity: PaymentRecord

| Field                      | Type Direction | Required    | Description             | Notes                    |
| -------------------------- | -------------- | ----------- | ----------------------- | ------------------------ |
| PaymentRecordId            | Long           | Yes         | Internal ID payment.    |                          |
| PaymentRecordUid           | Guid           | Yes         | Public ID.              |                          |
| PaymentCode                | String         | No          | Mã payment hiển thị.    | Optional.                |
| ShopId                     | Long           | Yes         | Shop.                   |                          |
| SubscriptionId             | Long           | Conditional | Subscription liên quan. |                          |
| PlanId                     | Long/Int       | Conditional | Gói thanh toán.         |                          |
| Provider                   | String         | Yes         | VNPay.                  |                          |
| ProviderTransactionId      | String         | No          | ID giao dịch VNPay.     |                          |
| Amount                     | Decimal        | Yes         | Số tiền.                |                          |
| Currency                   | String         | Yes         | VND.                    |                          |
| Status                     | PaymentStatus  | Yes         | Pending/Paid/Failed/... |                          |
| PaymentRequestPayloadJson  | Json/Text      | No          | Snapshot request.       | Mask dữ liệu nhạy cảm.   |
| PaymentCallbackPayloadJson | Json/Text      | No          | Snapshot callback.      | Mask dữ liệu nhạy cảm.   |
| CreatedAt                  | DateTime       | Yes         |                         |                          |
| PaidAt                     | DateTime       | No          |                         | Khi xác minh thành công. |
| ExpiredAt                  | DateTime       | No          | QR/session hết hạn.     |                          |
| NeedReviewReason           | Text           | No          | Lý do need review.      |                          |

### Rules

| Rule ID           | Rule                                                                             |
| ----------------- | -------------------------------------------------------------------------------- |
| RULE-PAY-DATA-001 | Không cập nhật subscription chỉ vì user quay lại success page.                   |
| RULE-PAY-DATA-002 | Payment success phải dựa trên callback/IPN hoặc verification hợp lệ.             |
| RULE-PAY-DATA-003 | Callback success nhưng update subscription lỗi phải tạo reconciliation task/log. |
| RULE-PAY-DATA-004 | Payment payload phải mask dữ liệu nhạy cảm.                                      |

---

## 16.4 Entity: Invoice

| Field           | Type Direction | Required    | Description           | Notes   |
| --------------- | -------------- | ----------- | --------------------- | ------- |
| InvoiceId       | Long           | Yes         | Internal ID invoice.  |         |
| InvoiceUid      | Guid           | Yes         | Public ID.            |         |
| ShopId          | Long           | Yes         | Shop.                 |         |
| PaymentRecordId | Long           | Yes         | Payment liên quan.    |         |
| InvoiceCode     | String         | Yes         | Mã invoice.           | Unique. |
| Amount          | Decimal        | Yes         | Tổng tiền.            |         |
| Currency        | String         | Yes         | VND.                  |         |
| Status          | Enum           | Yes         | Draft/Paid/Cancelled. |         |
| IssuedAt        | DateTime       | Conditional | Ngày phát hành.       |         |
| FileUrl         | String         | No          | File invoice nếu có.  |         |

---

# 17. Notification Data Model

## 17.1 Entity: Notification

| Field             | Type Direction | Required    | Description                                      | Notes                           |
| ----------------- | -------------- | ----------- | ------------------------------------------------ | ------------------------------- |
| NotificationId    | Long           | Yes         | Internal ID notification.                        |                                 |
| NotificationUid   | Guid           | Yes         | Public ID.                                       |                                 |
| ShopId            | Long           | Conditional | Shop liên quan.                                  | Null nếu platform notification. |
| RecipientUserId   | Long           | Yes         | Người nhận.                                      |                                 |
| NotificationType  | Enum           | Yes         | SyncError/LowStock/Approval/Subscription/System. |                                 |
| Title             | String         | Yes         | Tiêu đề.                                         |                                 |
| Message           | Text           | Yes         | Nội dung.                                        |                                 |
| RelatedEntityType | String         | No          | Order/Product/etc.                               |                                 |
| RelatedEntityId   | Long           | No          | Entity liên quan.                                | Internal ID.                    |
| IsRead            | Boolean        | Yes         | Đã đọc chưa.                                     |                                 |
| CreatedAt         | DateTime       | Yes         |                                                  |                                 |
| ReadAt            | DateTime       | No          |                                                  |                                 |

---

# 18. Audit Log Data Model

## 18.1 Entity: AuditLog

| Field             | Type Direction | Required    | Description                          | Notes                      |
| ----------------- | -------------- | ----------- | ------------------------------------ | -------------------------- |
| AuditLogId        | Long           | Yes         | Internal ID log.                     |                            |
| AuditLogUid       | Guid           | Yes         | Public ID.                           |                            |
| ScopeType         | Enum           | Yes         | Seller/Platform/System.              |                            |
| ShopId            | Long           | Conditional | Shop nếu seller-side.                |                            |
| ActorUserId       | Long           | Conditional | User thực hiện.                      | Null nếu system.           |
| ActorRoleSnapshot | String/Json    | No          | Role tại thời điểm thao tác.         | Nên lưu RoleCode snapshot. |
| Action            | String         | Yes         | Tên action.                          |                            |
| EntityType        | String         | Yes         | Entity bị tác động.                  |                            |
| EntityId          | Long/String    | Conditional | Internal entity ID hoặc external ID. |                            |
| EntityUid         | Guid           | No          | Public ID nếu có.                    |                            |
| BeforeJson        | Json/Text      | No          | Dữ liệu trước.                       | Mask nếu nhạy cảm.         |
| AfterJson         | Json/Text      | No          | Dữ liệu sau.                         | Mask nếu nhạy cảm.         |
| Reason            | Text           | No          | Lý do nếu có.                        |                            |
| IpAddress         | String         | No          | IP.                                  | Optional.                  |
| UserAgent         | String         | No          | User agent.                          | Optional.                  |
| CreatedAt         | DateTime       | Yes         |                                      |                            |

### Audit Required Actions

| Area         | Actions                                            |
| ------------ | -------------------------------------------------- |
| User/Role    | Invite, assign role, remove role, disable user.    |
| Inventory    | Stock in, adjustment request, approval, stock out. |
| Order        | Confirm, cancel, status change.                    |
| Product      | Create, update, deactivate/archive.                |
| Channel/Sync | Connect/disconnect channel, manual resync.         |
| Report       | Profit formula change/approval, sensitive export.  |
| Subscription | Upgrade/downgrade/payment/status change.           |
| Platform     | Suspend/restore shop, plan management.             |

---

# 19. Settings Data Model

## 19.1 Entity: PlatformSetting

| Field             | Type Direction | Required    | Description          | Notes   |
| ----------------- | -------------- | ----------- | -------------------- | ------- |
| PlatformSettingId | Long           | Yes         | Internal ID setting. |         |
| SettingKey        | String         | Yes         | Key.                 | Unique. |
| SettingValueJson  | Json           | Yes         | Value.               |         |
| Description       | String         | No          | Mô tả.               |         |
| UpdatedByUserId   | Long           | Conditional | Người sửa.           |         |
| UpdatedAt         | DateTime       | Yes         |                      |         |

---

# 20. Data Relationships Summary

| Parent Entity          | Child Entity           | Relationship                                              | Notes                           |
| ---------------------- | ---------------------- | --------------------------------------------------------- | ------------------------------- |
| Shop                   | ShopUserMembership     | 1 - many                                                  | User trong shop.                |
| UserAccount            | ShopUserMembership     | 1 - many logically, but phase đầu seller-side 1 shop/user | Có thể mở rộng sau.             |
| ShopUserMembership     | MembershipRole         | 1 - many                                                  | Một user nhiều role trong shop. |
| UserAccount            | PlatformUserMembership | 1 - many                                                  | Platform scope.                 |
| PlatformUserMembership | PlatformMembershipRole | 1 - many                                                  | Platform user nhiều role.       |
| Shop                   | Product                | 1 - many                                                  |                                 |
| Product                | ProductSku             | 1 - many                                                  |                                 |
| Product                | InventoryStock         | 1 - 1/many                                                | Tùy SKU.                        |
| Shop                   | Warehouse              | 1 - 1 phase đầu                                           |                                 |
| InventoryStock         | InventoryMovement      | 1 - many                                                  |                                 |
| Shop                   | Order                  | 1 - many                                                  |                                 |
| Order                  | OrderItem              | 1 - many                                                  |                                 |
| Order                  | OrderStatusHistory     | 1 - many                                                  |                                 |
| Order                  | Shipment               | 1 - many or 1 - 1                                         | Phase đầu có thể 1 - 1.         |
| Customer               | Order                  | 1 - many                                                  | Customer từ order.              |
| Customer               | CustomerTicket         | 1 - many                                                  |                                 |
| Shop                   | ChannelConnection      | 1 - many                                                  |                                 |
| ChannelConnection      | ExternalWebhookEvent   | 1 - many                                                  | Raw event từ sàn.               |
| ChannelConnection      | ExternalOrderRaw       | 1 - many                                                  | Raw order từ sàn.               |
| ChannelConnection      | ExternalProductRaw     | 1 - many                                                  | Raw product từ sàn.             |
| ChannelConnection      | SyncJob                | 1 - many                                                  |                                 |
| SyncJob                | SyncLog                | 1 - many                                                  |                                 |
| SyncLog                | SyncError              | 1 - many or 1 - 1                                         |                                 |
| Shop                   | Subscription           | 1 - many                                                  | Lịch sử subscription.           |
| Subscription           | PaymentRecord          | 1 - many                                                  |                                 |
| PaymentRecord          | Invoice                | 1 - 0/1 or 1 - many                                       | Tùy policy.                     |

---

# 21. Data Integrity Rules

| Rule ID            | Rule                                                                                                   |
| ------------------ | ------------------------------------------------------------------------------------------------------ |
| RULE-INTEGRITY-001 | Không cho dữ liệu seller-side thiếu ShopId.                                                            |
| RULE-INTEGRITY-002 | Không cho truy cập dữ liệu khác ShopId.                                                                |
| RULE-INTEGRITY-003 | Không hard delete dữ liệu đã phát sinh giao dịch.                                                      |
| RULE-INTEGRITY-004 | Các update inventory/order/payment cần dùng transaction.                                               |
| RULE-INTEGRITY-005 | Các update tồn kho cần concurrency control.                                                            |
| RULE-INTEGRITY-006 | Sync từ Lazada không được ghi đè dữ liệu mới bằng dữ liệu cũ.                                          |
| RULE-INTEGRITY-007 | Payment success phải idempotent, callback trùng không được tạo nhiều lần đổi gói.                      |
| RULE-INTEGRITY-008 | Invitation accept phải idempotent hoặc xử lý trùng an toàn.                                            |
| RULE-INTEGRITY-009 | Audit log không được chứa secret/token/password plaintext.                                             |
| RULE-INTEGRITY-010 | Raw external payload nếu lưu phải mask hoặc bảo vệ dữ liệu nhạy cảm.                                   |
| RULE-INTEGRITY-011 | Không update domain tables trực tiếp từ API/webhook ngoài nếu chưa qua validate/mapping/deduplication. |
| RULE-INTEGRITY-012 | External event/webhook phải xử lý idempotent, event trùng không được tạo tác động nghiệp vụ nhiều lần. |
| RULE-INTEGRITY-013 | Dữ liệu external cũ hơn domain data hiện tại không được ghi đè dữ liệu mới.                            |
| RULE-INTEGRITY-014 | Role permission không được phụ thuộc vào RoleName hiển thị.                                            |
| RULE-INTEGRITY-015 | Public API không nên expose internal numeric ID nếu không có lý do nội bộ rõ ràng.                     |

---

# 22. Index / Query Direction

> Đây là định hướng index logic, chưa phải script database.

| Entity                | Suggested Lookup / Index Direction                                              | Reason                                |
| --------------------- | ------------------------------------------------------------------------------- | ------------------------------------- |
| Shop                  | ShopUid, ShopCode, Status                                                       | Quản lý tenant và public lookup.      |
| UserAccount           | UserUid, Email                                                                  | Login/invite/public lookup.           |
| Role                  | RoleCode, ScopeType                                                             | Permission lookup.                    |
| ShopUserMembership    | ShopId + UserId                                                                 | Check membership.                     |
| Product               | ProductUid, ShopId + ProductName, ShopId + Status                               | Product list/filter/public lookup.    |
| ProductSku            | ProductSkuUid, ShopId + SkuCode                                                 | SKU unique lookup.                    |
| InventoryStock        | ShopId + ProductId + ProductSkuId                                               | Tồn kho.                              |
| InventoryMovement     | ShopId + ProductId + CreatedAt                                                  | Lịch sử tồn.                          |
| Order                 | OrderUid, ShopId + OrderCode, ShopId + ExternalOrderId, ShopId + ShopHubStatus  | Order list/sync/public lookup.        |
| Customer              | CustomerUid, ShopId + Phone/Email if available                                  | Tìm khách.                            |
| ChannelConnection     | ShopId + ChannelCode                                                            | Kết nối kênh.                         |
| SyncLog               | ShopId + ChannelConnectionId + CreatedAt, Status                                | Sync monitoring.                      |
| SyncError             | ShopId + ErrorType + ResolvedAt                                                 | Xử lý lỗi.                            |
| PaymentRecord         | PaymentRecordUid, ShopId + ProviderTransactionId, Status                        | Payment reconciliation/public lookup. |
| AuditLog              | ShopId + CreatedAt, ActorUserId, EntityType                                     | Audit search.                         |
| Notification          | RecipientUserId + IsRead + CreatedAt                                            | Notification list.                    |
| ExternalWebhookEvent  | ChannelCode + ExternalEventId, ProcessingStatus + ReceivedAt                    | Webhook dedup/retry.                  |
| ExternalOrderRaw      | ShopId + ChannelConnectionId + ExternalOrderId, MappingStatus                   | Order staging/mapping.                |
| ExternalProductRaw    | ShopId + ChannelConnectionId + ExternalProductId + ExternalSkuId, MappingStatus | Product staging/mapping.              |
| ExternalFinanceRaw    | SourceProvider + ExternalTransactionId, ShopId + MappingStatus                  | Finance/payment/fee reconciliation.   |
| IntegrationEventInbox | IdempotencyKey, Status + AvailableAt                                            | Idempotent event processing.          |

---

# 23. Data Model by Screen Coverage

| Screen Group           | Main Entities                                                                                                                                                                                                      |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Auth                   | UserAccount, Invitation, Role, Membership                                                                                                                                                                          |
| Seller Dashboard       | Order, InventoryStock, SyncError, ReportDataQualityIssue, Subscription                                                                                                                                             |
| Products               | Product, ProductSku, Category, ProductMarketplaceMapping, InventoryStock                                                                                                                                           |
| Categories             | Category, ProductMarketplaceMapping, ExternalCategoryRaw                                                                                                                                                           |
| Orders                 | ExternalOrderRaw, Order, OrderItem, OrderStatusHistory, OrderNote, Shipment, Customer                                                                                                                              |
| Customers              | Customer, CustomerTicket, CustomerTicketComment, Order                                                                                                                                                             |
| Inventory              | Warehouse, InventoryStock, InventoryMovement, InventoryAdjustmentRequest                                                                                                                                           |
| Channels / Sync        | ExternalWebhookEvent, ExternalOrderRaw, ExternalProductRaw, ExternalFinanceRaw, ExternalCategoryRaw, IntegrationEventInbox, ChannelConnection, ChannelCredential, SyncJob, SyncLog, SyncError, ManualResyncRequest |
| Shipping               | Shipment, Order, InventoryMovement                                                                                                                                                                                 |
| Reports                | ExternalFinanceRaw, ProfitFormula, ProfitFormulaApproval, ReportExportJob, ReportDataQualityIssue, Order, Product, Inventory                                                                                       |
| Subscription / Billing | Plan, Subscription, PaymentRecord, Invoice, ExternalFinanceRaw                                                                                                                                                     |
| Users / Roles          | UserAccount, ShopUserMembership, MembershipRole, PlatformUserMembership, PlatformMembershipRole, Role, Invitation                                                                                                  |
| Settings               | ShopSetting, PlatformSetting                                                                                                                                                                                       |
| Audit                  | AuditLog                                                                                                                                                                                                           |
| Notifications          | Notification                                                                                                                                                                                                       |
| Platform               | Shop, PlatformUserMembership, Plan, Subscription, PaymentRecord, SyncError, AuditLog                                                                                                                               |

---

# 24. Open Questions

| Question ID | Question                                                                         | Related Area        | Impact                                                                                 | Status                        |
| ----------- | -------------------------------------------------------------------------------- | ------------------- | -------------------------------------------------------------------------------------- | ----------------------------- |
| Q-DATA-001  | Password policy cụ thể là gì?                                                    | User/Auth           | Ảnh hưởng validation và security.                                                      | Open                          |
| Q-DATA-002  | OTP invitation hết hạn sau bao lâu và có bao nhiêu lần nhập sai?                 | Invitation/Auth     | Ảnh hưởng data fields và security policy.                                              | Open                          |
| Q-DATA-003  | Invitation hết hạn sau bao lâu?                                                  | Invitation          | Ảnh hưởng invite flow.                                                                 | Open                          |
| Q-DATA-004  | Lazada order status mapping cụ thể là gì?                                        | Order/Sync          | Ảnh hưởng ShopHubOrderStatus mapping.                                                  | Pending External Confirmation |
| Q-DATA-005  | Lazada product status mapping cụ thể là gì?                                      | Product/Sync        | Ảnh hưởng ProductStatus mapping.                                                       | Pending External Confirmation |
| Q-DATA-006  | Lazada fees/transaction API trả dữ liệu phí chi tiết thế nào?                    | Report/Profit       | Ảnh hưởng profit report.                                                               | Pending External Confirmation |
| Q-DATA-007  | Format export Excel/PDF từng loại báo cáo gồm những cột nào?                     | Report Export       | Ảnh hưởng ReportExportJob và UI.                                                       | Open                          |
| Q-DATA-008  | Shipping provider API sẽ tích hợp cụ thể bên nào?                                | Shipment            | Ảnh hưởng shipment fields.                                                             | Open                          |
| Q-DATA-009  | Có cần lưu raw payload Lazada đầy đủ hay chỉ snapshot field cần thiết?           | Sync/Storage        | Ảnh hưởng storage/security.                                                            | Open                          |
| Q-DATA-010  | Có cần field-level masking policy cho customer phone/email không?                | Customer/Permission | Ảnh hưởng UI/API/Permission.                                                           | Open                          |
| Q-DATA-011  | Trial/Pro/Premium plan limit chính xác là gì?                                    | Subscription/Plan   | Ảnh hưởng Plan fields.                                                                 | Open                          |
| Q-DATA-012  | Report export lớn lưu file trong bao lâu?                                        | Report Export       | Ảnh hưởng storage/cleanup.                                                             | Open                          |
| Q-DATA-013  | Retention policy chính thức cho External/Staging tables là bao lâu?              | External/Staging    | Ảnh hưởng storage, audit, debug và chi phí.                                            | Open                          |
| Q-DATA-014  | Dùng BIGINT identity hay GUID làm clustered PK vật lý trong database cụ thể nào? | Database Design     | Tài liệu này đề xuất BIGINT internal PK + GUID public ID, cần xác nhận theo DB engine. | Draft Proposed                |
| Q-DATA-015  | Có cần custom role/permission ngoài system roles trong phase đầu không?          | Role/Permission     | Ảnh hưởng Role/Permission schema.                                                      | Open                          |
| Q-DATA-016  | Có cần bảng Permission/RolePermission riêng ngay phase đầu không?                | Role/Permission     | Ảnh hưởng 09_PERMISSION_MATRIX và backend authorization.                               | Open                          |

---

# 25. Decisions Log

| Decision ID  | Decision                                                                                                              | Reason                                                                 | Related Docs | Status         |
| ------------ | --------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- | ------------ | -------------- |
| DEC-DATA-001 | Mọi seller-side entity phải có ShopId.                                                                                | Đảm bảo tenant boundary.                                               | 01, 04       | Accepted       |
| DEC-DATA-002 | Phase đầu mỗi shop có 1 kho vật lý.                                                                                   | Giảm scope inventory.                                                  | 01, 04       | Accepted       |
| DEC-DATA-003 | Inventory dùng Physical/Available/Reserved Stock.                                                                     | Tránh sai tồn kho.                                                     | 04           | Accepted       |
| DEC-DATA-004 | Order lưu MarketplaceStatus và ShopHubStatus.                                                                         | Tránh mất thông tin trạng thái sàn.                                    | 04, 05       | Accepted       |
| DEC-DATA-005 | Product lưu marketplace/normalized/custom status.                                                                     | Hỗ trợ trạng thái sàn và trạng thái nội bộ.                            | 04           | Accepted       |
| DEC-DATA-006 | Customer không có login account.                                                                                      | ShopHub không có customer portal phase đầu.                            | 01, 05       | Accepted       |
| DEC-DATA-007 | Invitation dùng email + OTP qua email.                                                                                | Đã chốt ở sitemap.                                                     | 05           | Accepted       |
| DEC-DATA-008 | Inventory adjustment threshold cấu hình theo shop, default 10.000.000 VND/sản phẩm.                                   | Linh hoạt theo shop.                                                   | 04, 05       | Accepted       |
| DEC-DATA-009 | Ticket phase đầu có Priority và Assignee; SLA/RAG để later.                                                           | Giảm phức tạp phase đầu.                                               | 04, 05       | Accepted       |
| DEC-DATA-010 | Payment dùng VNPay QR và cần callback/IPN/reconciliation.                                                             | Tránh đổi gói sai.                                                     | 04           | Accepted       |
| DEC-DATA-011 | Billing Admin không được đổi gói thủ công.                                                                            | Subscription phải qua payment flow.                                    | 02, 04       | Accepted       |
| DEC-DATA-012 | Export report cho chọn time range và cột cần xuất.                                                                    | Đáp ứng nhu cầu báo cáo thực tế.                                       | 05           | Accepted       |
| DEC-DATA-013 | Marketing/RAG/SLA/Multi-warehouse/Multi-shop user để deferred.                                                        | Giữ scope phase đầu.                                                   | 05, 06       | Accepted       |
| DEC-DATA-014 | Dữ liệu từ API/webhook ngoài phải đi qua External/Staging tables trước khi cập nhật domain tables.                    | Giảm rủi ro dữ liệu sàn làm sai domain data, hỗ trợ debug/retry/dedup. | 04, 07, 08   | Accepted       |
| DEC-DATA-015 | Entity nghiệp vụ chính dùng BIGINT identity làm internal primary key và UUID/GUID làm public ID cho API/URL/frontend. | Cân bằng hiệu năng DB và an toàn public API.                           | 07, 08, 11   | Draft Accepted |
| DEC-DATA-016 | BusinessCode dùng để hiển thị/tìm kiếm, không dùng làm primary key.                                                   | Tránh phụ thuộc mã nghiệp vụ có thể đổi format.                        | 07           | Accepted       |
| DEC-DATA-017 | Role lưu bằng RoleId + RoleCode + RoleName; permission logic dùng RoleCode/PermissionCode, không dùng RoleName.       | RoleName có thể đổi/ngôn ngữ hóa; RoleId không nên hard-code.          | 07, 09       | Accepted       |

---

# 26. Verification Checklist

| Check Item                                                  | Result | Notes                                                                                                                                |
| ----------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Tất cả module phase đầu có entity chính chưa?               | Pass   | Auth, tenant, user, product, inventory, order, customer, channel, external/staging, sync, report, subscription, audit, notification. |
| Tenant boundary có rõ không?                                | Pass   | Mọi seller-side entity có ShopId hoặc liên kết tới entity có ShopId.                                                                 |
| Inventory model đúng flow không?                            | Pass   | Physical/Available/Reserved và movement rules đã định nghĩa.                                                                         |
| Order status có tách Marketplace và ShopHub không?          | Pass   | Order lưu cả 2.                                                                                                                      |
| Product status có tách marketplace/normalized/custom không? | Pass   | Product và mapping đã định nghĩa.                                                                                                    |
| Payment/subscription có tránh đổi gói thủ công không?       | Pass   | Subscription chỉ đổi sau payment verified.                                                                                           |
| Customer portal có bị tạo nhầm không?                       | Pass   | Customer không có UserAccount.                                                                                                       |
| Invitation + OTP có data model chưa?                        | Pass   | Invitation có token hash, OTP hash, expiry.                                                                                          |
| Approval flow có data model chưa?                           | Pass   | InventoryAdjustmentRequest, ProfitFormulaApproval, ManualResyncRequest.                                                              |
| Sync/error/retry có data model chưa?                        | Pass   | SyncJob, SyncLog, SyncError.                                                                                                         |
| Report export có data model chưa?                           | Pass   | ReportExportJob, SelectedColumnsJson, FilterJson.                                                                                    |
| External/Staging data model đã có chưa?                     | Pass   | ExternalWebhookEvent, ExternalOrderRaw, ExternalProductRaw, ExternalFinanceRaw, ExternalCategoryRaw, IntegrationEventInbox.          |
| ID strategy đã rõ chưa?                                     | Pass   | Internal BIGINT PK + UUID PublicId + BusinessCode + ExternalId.                                                                      |
| Role storage strategy đã rõ chưa?                           | Pass   | RoleId + RoleCode + RoleName; logic dùng RoleCode, không dùng RoleName/hard-code RoleId.                                             |
| Các điểm chưa chốt có Open Questions chưa?                  | Pass   | Lazada mapping/API, VNPay edge, export format, OTP policy, retention staging, permission schema.                                     |

---

# 27. Change Log

| Version | Date | Change                                                                                                                                                                                                                                                  | Author |
| ------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| 0.1     | TBD  | Initial logical data model based on Project Brief, User Roles, Feature List, Business Flow, Sitemap and Screen Requirements                                                                                                                             | TBD    |
| 0.2     | TBD  | Added External/Staging data model, ID strategy with BIGINT internal PK + UUID public ID + BusinessCode + ExternalId, role storage strategy using RoleId/RoleCode/RoleName, updated data rules, integrity rules, decisions, open questions and checklist | TBD    |
