# 04_BUSINESS_FLOW.md

## Metadata

| Field        | Value                                                                                                                                                                               |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Document ID  | DOC-04-BUSINESS-FLOW                                                                                                                                                                |
| Project Name | Multi-channel E-commerce Hub / ShopHub                                                                                                                                              |
| Version      | 0.3                                                                                                                                                                                 |
| Status       | Draft - Updated with Open Questions answers                                                                                                                                         |
| Owner        | TBD                                                                                                                                                                                 |
| Last Updated | TBD                                                                                                                                                                                 |
| Depends On   | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md                                                                                                                           |
| Used By      | 00_TRACEABILITY_MATRIX.md, 05_SITEMAP_AND_SCREEN_LIST.md, 06_SCREEN_REQUIREMENTS/, 07_DATA_MODEL.md, 08_API_SPECIFICATION.md, 09_PERMISSION_MATRIX.md, 11_TECHNICAL_ARCHITECTURE.md |

---

# 1. Purpose

Tài liệu này mô tả **luồng nghiệp vụ chính** của hệ thống ShopHub.

Mục tiêu:

* Làm rõ nghiệp vụ vận hành thực tế của hệ thống.
* Chuyển feature thành flow có actor, điều kiện, bước xử lý, trạng thái và lỗi.
* Giảm rủi ro dev/design/AI tự suy diễn sai nghiệp vụ.
* Làm đầu vào cho sitemap, screen requirement, data model, API specification và permission matrix.

Tài liệu này không mô tả chi tiết UI từng màn hình và không định nghĩa API contract chi tiết.

---

# 2. Business Flow Principles

| Rule ID       | Rule                                                                                                             |
| ------------- | ---------------------------------------------------------------------------------------------------------------- |
| RULE-FLOW-001 | Mỗi flow phải có Flow ID cố định.                                                                                |
| RULE-FLOW-002 | Mỗi flow phải liên kết với feature tương ứng trong `03_FEATURE_LIST.md`.                                         |
| RULE-FLOW-003 | Flow phải ghi rõ actor, trigger, preconditions, main flow, alternative flow, error cases và resulting state.     |
| RULE-FLOW-004 | Flow bên shop chỉ thao tác dữ liệu trong shop/tenant của actor.                                                  |
| RULE-FLOW-005 | Flow platform-side không được tự ý thay đổi dữ liệu nghiệp vụ của shop nếu không có quyền hoặc workflow rõ ràng. |
| RULE-FLOW-006 | Flow thay đổi dữ liệu nhạy cảm phải có audit log.                                                                |
| RULE-FLOW-007 | Flow có rủi ro mất dữ liệu hoặc ghi đè dữ liệu phải có confirm hoặc approval.                                    |
| RULE-FLOW-008 | Flow tích hợp Lazada phải có sync log, error state và khả năng retry/resync an toàn.                             |
| RULE-FLOW-009 | Flow inventory phải tuân thủ mô hình Physical Stock / Available Stock / Reserved Stock.                          |
| RULE-FLOW-010 | Flow subscription/payment phải đi qua payment/subscription flow, không đổi gói thủ công.                         |

---

# 3. Core Status Definitions

## 3.1 Shop Status

| Status    | Meaning                               | Notes                                         |
| --------- | ------------------------------------- | --------------------------------------------- |
| Draft     | Shop mới tạo, chưa hoàn tất cấu hình. | Có thể dùng trong onboarding.                 |
| Active    | Shop đang hoạt động bình thường.      | Được sử dụng tính năng theo gói.              |
| Suspended | Shop bị tạm khóa.                     | Có thể do billing, vi phạm hoặc admin action. |
| Cancelled | Shop đã hủy sử dụng dịch vụ.          | Không cho vận hành bình thường.               |

## 3.2 Subscription Status

| Status         | Meaning                                    |
| -------------- | ------------------------------------------ |
| Trial          | Đang dùng thử.                             |
| Active         | Gói đang hoạt động.                        |
| PendingPayment | Chờ thanh toán.                            |
| PastDue        | Quá hạn thanh toán.                        |
| Suspended      | Tạm khóa do vấn đề thanh toán hoặc policy. |
| Cancelled      | Đã hủy subscription.                       |

## 3.3 ShopHub Normalized Order Status - Draft

> Lưu ý: trạng thái chính xác cần được refine sau khi verify Lazada API. Bảng này là bản chuẩn hóa nội bộ ban đầu.

| ShopHub Status | Meaning                  | Inventory Impact                                                              |
| -------------- | ------------------------ | ----------------------------------------------------------------------------- |
| Pending        | Đơn mới/chờ xác nhận.    | Chưa giữ hàng nếu chưa xác nhận.                                              |
| Confirmed      | Đơn đã được xác nhận.    | Giảm Available Stock, tăng Reserved Stock.                                    |
| Processing     | Đơn đang xử lý/đóng gói. | Giữ Reserved Stock.                                                           |
| ReadyToShip    | Đơn sẵn sàng giao.       | Giữ Reserved Stock.                                                           |
| Shipping       | Đơn đang giao.           | Có thể giảm Physical Stock và Reserved Stock khi xác nhận xuất kho/giao hàng. |
| Completed      | Đơn hoàn tất.            | Không còn Reserved Stock.                                                     |
| Cancelled      | Đơn đã hủy.              | Nếu hủy trước xuất kho: trả lại Available Stock, giảm Reserved Stock.         |
| Returned       | Đơn hoàn/trả.            | Có thể tăng lại Physical Stock nếu hàng nhập lại kho.                         |
| Failed         | Đơn lỗi/xử lý thất bại.  | Cần kiểm tra theo case.                                                       |

## 3.4 Product Status Direction

Product status có 3 lớp:

| Status Layer                      | Meaning                                       |
| --------------------------------- | --------------------------------------------- |
| Marketplace Product Status        | Trạng thái sản phẩm từ Lazada hoặc sàn.       |
| ShopHub Normalized Product Status | Trạng thái chuẩn hóa nội bộ của ShopHub.      |
| Shop Custom Product Status        | Trạng thái tùy chỉnh nội bộ của shop nếu cần. |

Draft normalized product status:

| ShopHub Product Status | Meaning                              |
| ---------------------- | ------------------------------------ |
| Draft                  | Sản phẩm mới tạo, chưa sẵn sàng bán. |
| Active                 | Đang bán.                            |
| Inactive               | Tạm ẩn/ngừng bán.                    |
| OutOfStock             | Hết hàng khả dụng.                   |
| SyncPending            | Đang chờ đồng bộ.                    |
| SyncError              | Đồng bộ lỗi.                         |
| Archived               | Lưu trữ, không còn sử dụng chính.    |

---

# 4. Inventory Model Rules

| Concept            | Meaning                                              |
| ------------------ | ---------------------------------------------------- |
| Physical Warehouse | Mỗi shop có 1 kho vật lý ở phase đầu.                |
| Physical Stock     | Số lượng hàng thực tế trong kho vật lý.              |
| Available Stock    | Số lượng có thể bán/sync lên Lazada.                 |
| Reserved Stock     | Số lượng đã giữ cho đơn nhưng chưa xuất kho thực tế. |

## 4.1 Inventory Movement Rules

| Rule ID           | Event                         | Physical Stock |         Available Stock |                      Reserved Stock |
| ----------------- | ----------------------------- | -------------: | ----------------------: | ----------------------------------: |
| RULE-INV-MOVE-001 | Nhập kho                      |           Tăng |                    Tăng |                           Không đổi |
| RULE-INV-MOVE-002 | Xác nhận đơn                  |      Không đổi |                    Giảm |                                Tăng |
| RULE-INV-MOVE-003 | Xuất kho/giao hàng            |           Giảm |               Không đổi |                                Giảm |
| RULE-INV-MOVE-004 | Hủy đơn trước xuất kho        |      Không đổi |                    Tăng |                                Giảm |
| RULE-INV-MOVE-005 | Hoàn hàng nhập lại kho        |           Tăng | Tăng nếu có thể bán lại |                           Không đổi |
| RULE-INV-MOVE-006 | Điều chỉnh tồn kho được duyệt |      Tăng/Giảm |     Tăng/Giảm theo rule | Không đổi hoặc điều chỉnh theo case |

---

# 5. Flow Index

| Flow ID                            | Flow Name                      | Related Features                                                                                                                                      | Priority    |
| ---------------------------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| FLOW-TENANT-ONBOARD-SHOP           | Onboard New Shop               | FEAT-TENANT-CREATE-SHOP, FEAT-SUBSCRIPTION-VIEW-PLAN                                                                                                  | Must-have   |
| FLOW-USER-INVITE-SHOP-USER         | Invite Shop User               | FEAT-USER-INVITE-SHOP-USER, FEAT-USER-ASSIGN-ROLE                                                                                                     | Must-have   |
| FLOW-USER-ACCEPT-INVITE            | Accept Invitation              | FEAT-USER-ACCEPT-INVITE                                                                                                                               | Must-have   |
| FLOW-CHANNEL-CONNECT-LAZADA        | Connect Lazada Channel         | FEAT-CHANNEL-LAZADA-CONNECT                                                                                                                           | Must-have   |
| FLOW-SYNC-LAZADA-ORDER             | Sync Lazada Orders             | FEAT-SYNC-ORDER, FEAT-SYNC-ORDER-STATUS                                                                                                               | Must-have   |
| FLOW-SYNC-LAZADA-STOCK             | Sync Available Stock to Lazada | FEAT-SYNC-STOCK                                                                                                                                       | Must-have   |
| FLOW-SYNC-ERROR-RETRY              | Handle Sync Error / Retry      | FEAT-SYNC-ERROR, FEAT-SYNC-RETRY                                                                                                                      | Must-have   |
| FLOW-SYNC-MANUAL-RESYNC            | Manual Resync                  | FEAT-SYNC-MANUAL-RESYNC-APPROVAL                                                                                                                      | Should-have |
| FLOW-PRODUCT-CREATE                | Create Product                 | FEAT-PRODUCT-CREATE                                                                                                                                   | Must-have   |
| FLOW-PRODUCT-UPDATE-STATUS         | Update Product Status          | FEAT-PRODUCT-STATUS                                                                                                                                   | Must-have   |
| FLOW-ORDER-REVIEW-CONFIRM          | Review & Confirm Order         | FEAT-ORDER-DETAIL, FEAT-ORDER-UPDATE-STATUS                                                                                                           | Must-have   |
| FLOW-ORDER-CANCEL                  | Cancel Order                   | FEAT-ORDER-CANCEL                                                                                                                                     | Must-have   |
| FLOW-ORDER-SHIPMENT                | Process Shipment / Stock Out   | FEAT-SHIPPING-STATUS, FEAT-INVENTORY-STOCK-OUT                                                                                                        | Should-have |
| FLOW-INVENTORY-STOCK-IN            | Stock In                       | FEAT-INVENTORY-STOCK-IN                                                                                                                               | Must-have   |
| FLOW-INVENTORY-ADJUSTMENT-APPROVAL | Inventory Adjustment Approval  | FEAT-INVENTORY-ADJUSTMENT-REQUEST, FEAT-INVENTORY-ADJUSTMENT-APPROVAL                                                                                 | Must-have   |
| FLOW-CUSTOMER-CARE-TICKET          | Customer Care Issue / Ticket   | FEAT-CUSTOMER-ISSUE-TRACKING                                                                                                                          | Should-have |
| FLOW-PROFIT-FORMULA-APPROVAL       | Profit Formula Approval        | FEAT-REPORT-PROFIT-FORMULA-APPROVAL                                                                                                                   | Must-have   |
| FLOW-REPORT-EXPORT                 | Export Report                  | FEAT-REPORT-EXPORT                                                                                                                                    | Should-have |
| FLOW-SUBSCRIPTION-UPGRADE-PAYMENT  | Upgrade Plan with VNPay QR     | FEAT-SUBSCRIPTION-UPGRADE, FEAT-SUBSCRIPTION-PAYMENT                                                                                                  | Must-have   |
| FLOW-SUBSCRIPTION-PASTDUE-SUSPEND  | Past Due / Suspend Shop        | FEAT-SUBSCRIPTION-STATUS                                                                                                                              | Should-have |
| FLOW-AUTH-LOGIN-SESSION            | Login & Session Validation     | FEAT-AUTH-LOGIN, FEAT-AUTH-SESSION, FEAT-AUTH-RBAC                                                                                                    | Must-have   |
| FLOW-AUTH-RESET-PASSWORD           | Forgot / Reset Password        | FEAT-AUTH-FORGOT-PASSWORD, FEAT-AUTH-RESET-PASSWORD                                                                                                   | Should-have |
| FLOW-USER-PLATFORM-INVITE          | Invite Platform User           | FEAT-USER-INVITE-PLATFORM-USER, FEAT-USER-MANAGE-PLATFORM-USER                                                                                        | Must-have   |
| FLOW-TENANT-SHOP-SUSPEND-RESTORE   | Suspend / Restore Shop         | FEAT-TENANT-SHOP-STATUS                                                                                                                               | Must-have   |
| FLOW-DASHBOARD-SELLER-VIEW         | View Seller Dashboard          | FEAT-DASHBOARD-SELLER-OVERVIEW, FEAT-DASHBOARD-ORDER-SUMMARY, FEAT-DASHBOARD-INVENTORY-ALERT, FEAT-DASHBOARD-SYNC-ALERT                               | Must-have   |
| FLOW-CATEGORY-MANAGE               | Manage Category                | FEAT-CATEGORY-LIST, FEAT-CATEGORY-CREATE, FEAT-CATEGORY-UPDATE, FEAT-CATEGORY-DEACTIVATE                                                              | Must-have   |
| FLOW-PRODUCT-UPDATE                | Update Product Information     | FEAT-PRODUCT-UPDATE, FEAT-PRODUCT-PRICE, FEAT-PRODUCT-COST, FEAT-PRODUCT-LAZADA-SYNC-STATUS                                                           | Must-have   |
| FLOW-PRODUCT-DEACTIVATE            | Safe Product Deactivation      | FEAT-PRODUCT-DELETE-SAFE                                                                                                                              | Must-have   |
| FLOW-CUSTOMER-VIEW-MANAGE          | View Customer & Order History  | FEAT-CUSTOMER-LIST, FEAT-CUSTOMER-DETAIL, FEAT-CUSTOMER-ORDER-HISTORY                                                                                 | Must-have   |
| FLOW-REPORT-VIEW                   | View Reports                   | FEAT-REPORT-REVENUE, FEAT-REPORT-PROFIT, FEAT-REPORT-ORDER, FEAT-REPORT-PRODUCT, FEAT-REPORT-INVENTORY, FEAT-REPORT-CHANNEL, FEAT-REPORT-DATA-QUALITY | Must-have   |
| FLOW-SUBSCRIPTION-DOWNGRADE        | Downgrade Plan                 | FEAT-SUBSCRIPTION-DOWNGRADE, FEAT-SUBSCRIPTION-PLAN-LIMITS                                                                                            | Should-have |
| FLOW-AUDIT-VIEW                    | View Audit Log                 | FEAT-AUDIT-ORDER, FEAT-AUDIT-INVENTORY, FEAT-AUDIT-USER-ROLE, FEAT-AUDIT-SUBSCRIPTION, FEAT-AUDIT-SYNC, FEAT-AUDIT-REPORT-FORMULA                     | Must-have   |
| FLOW-NOTIFICATION-HANDLE           | Handle Notifications           | FEAT-NOTI-TOAST, FEAT-NOTI-SYNC-ERROR, FEAT-NOTI-LOW-STOCK, FEAT-NOTI-APPROVAL, FEAT-NOTI-SUBSCRIPTION                                                | Should-have |
| FLOW-SETTING-MANAGE                | Manage Settings                | FEAT-SETTING-SHOP-PROFILE, FEAT-SETTING-PROFIT-FORMULA, FEAT-SETTING-INVENTORY, FEAT-SETTING-CHANNEL, FEAT-SETTING-PLATFORM                           | Should-have |

---

# 6. FLOW-TENANT-ONBOARD-SHOP - Onboard New Shop

## Related Features

* FEAT-TENANT-CREATE-SHOP
* FEAT-TENANT-VIEW-SHOP
* FEAT-SUBSCRIPTION-VIEW-PLAN
* FEAT-SUBSCRIPTION-STATUS

## Actor

* ShopHub Admin
* ShopHub Super Admin
* Shop Owner

## Trigger

Một shop mới đăng ký hoặc được ShopHub tạo trên hệ thống.

## Preconditions

* Shop chưa tồn tại trong hệ thống.
* Người tạo có quyền tạo shop.
* Gói dịch vụ ban đầu được xác định: Trial/Free/Pro/Premium.

## Main Flow

1. ShopHub Admin hoặc hệ thống khởi tạo shop mới.
2. Hệ thống tạo tenant/shop boundary.
3. Hệ thống tạo hoặc gắn Shop Owner đầu tiên cho shop.
4. Hệ thống gán subscription plan ban đầu cho shop.
5. Hệ thống đặt trạng thái shop là `Active` hoặc `Draft` tùy quy trình onboarding.
6. Hệ thống tạo cấu hình mặc định cho shop:

   * Ngôn ngữ: Tiếng Việt.
   * Một kho vật lý mặc định.
   * Công thức lợi nhuận mặc định.
   * Giới hạn theo gói dịch vụ.
7. Hệ thống ghi audit log tạo shop.
8. Shop Owner đăng nhập và tiếp tục cấu hình shop.

## Alternative Flows

### AF-001: Shop đăng ký nhưng chưa thanh toán

1. Hệ thống tạo shop ở trạng thái `Draft` hoặc `PendingPayment`.
2. Hệ thống yêu cầu hoàn tất thanh toán nếu gói cần trả phí.
3. Sau khi thanh toán thành công, shop chuyển sang `Active`.

### AF-002: Shop dùng Trial

1. Hệ thống tạo subscription status là `Trial`.
2. Hệ thống áp dụng giới hạn Trial.
3. Khi hết trial, chuyển sang payment/subscription flow.

## Error Cases

| Error ID       | Case                           | Handling                          |
| -------------- | ------------------------------ | --------------------------------- |
| ERR-TENANT-001 | Shop trùng thông tin định danh | Không tạo shop mới, hiển thị lỗi. |
| ERR-TENANT-002 | Người tạo không có quyền       | Từ chối thao tác.                 |
| ERR-TENANT-003 | Không tạo được tenant boundary | Rollback và ghi log lỗi.          |

## Resulting State

* Shop được tạo.
* Shop có tenant boundary riêng.
* Shop có Shop Owner.
* Shop có subscription plan ban đầu.
* Shop có 1 kho vật lý mặc định.

## Audit Log

Bắt buộc ghi:

* Ai tạo shop.
* Thời điểm tạo.
* Plan ban đầu.
* Trạng thái shop ban đầu.

---

# 7. FLOW-USER-INVITE-SHOP-USER - Invite Shop User

## Related Features

* FEAT-USER-INVITE-SHOP-USER
* FEAT-USER-ASSIGN-ROLE
* FEAT-AUTH-MULTI-ROLE

## Actor

* Shop Owner

## Trigger

Shop Owner muốn thêm nhân sự vào shop.

## Preconditions

* Actor là Shop Owner của shop hiện tại.
* Shop đang Active.
* Chưa vượt giới hạn số user theo subscription plan.
* Email/người được mời chưa tồn tại trong shop hiện tại hoặc chưa active.

## Main Flow

1. Shop Owner mở màn quản lý user.
2. Shop Owner nhập email người được mời.
3. Shop Owner chọn một hoặc nhiều role cho user.
4. Hệ thống kiểm tra giới hạn gói dịch vụ.
5. Hệ thống kiểm tra role hợp lệ trong phạm vi seller-side.
6. Hệ thống tạo invitation ở trạng thái `Pending`.
7. Hệ thống gửi lời mời cho user.
8. Hệ thống ghi audit log.

## Alternative Flows

### AF-001: User đã tồn tại nhưng chưa thuộc shop

1. Hệ thống tạo invitation để user tham gia shop.
2. Sau khi user accept, gán role theo invitation.

### AF-002: User đã thuộc shop

1. Hệ thống không tạo invitation mới.
2. Hiển thị cảnh báo user đã thuộc shop.

## Error Cases

| Error ID     | Case                       | Handling                                              |
| ------------ | -------------------------- | ----------------------------------------------------- |
| ERR-USER-001 | Vượt giới hạn user của gói | Không cho mời, gợi ý nâng gói nếu phù hợp.            |
| ERR-USER-002 | Shop Manager cố mời user   | Từ chối vì Shop Manager không được quản lý user/role. |
| ERR-USER-003 | Role không hợp lệ          | Không cho gửi lời mời.                                |

## Resulting State

* Invitation được tạo.
* User chưa có quyền cho đến khi accept invite.

## Audit Log

* Người mời.
* Email được mời.
* Role được gán.
* Thời điểm mời.

---

# 8. FLOW-USER-ACCEPT-INVITE - Accept Invitation

## Related Features

* FEAT-USER-ACCEPT-INVITE
* FEAT-AUTH-LOGIN

## Actor

* Invited User

## Trigger

Người được mời mở link invitation.

## Preconditions

* Invitation tồn tại.
* Invitation chưa hết hạn.
* Invitation đang ở trạng thái `Pending`.
* Shop vẫn Active.

## Main Flow

1. User mở invitation link.
2. Hệ thống kiểm tra token invitation.
3. Nếu user chưa có tài khoản, user tạo mật khẩu/tài khoản.
4. Nếu user đã có tài khoản, user đăng nhập để xác nhận.
5. Hệ thống gắn user vào shop.
6. Hệ thống gán role theo invitation.
7. Invitation chuyển sang `Accepted`.
8. Hệ thống ghi audit log.
9. User có thể đăng nhập vào ShopHub theo role được cấp.

## Error Cases

| Error ID       | Case               | Handling                                    |
| -------------- | ------------------ | ------------------------------------------- |
| ERR-INVITE-001 | Invitation hết hạn | Hiển thị lỗi và yêu cầu Shop Owner gửi lại. |
| ERR-INVITE-002 | Invitation đã dùng | Không cho dùng lại.                         |
| ERR-INVITE-003 | Shop bị Suspended  | Không cho accept cho đến khi shop Active.   |

## Resulting State

* User thuộc shop.
* User có role/quyền theo invitation.

---

# 9. FLOW-CHANNEL-CONNECT-LAZADA - Connect Lazada Channel

## Related Features

* FEAT-CHANNEL-LAZADA-CONNECT
* FEAT-CHANNEL-CONNECTION-STATUS
* FEAT-CHANNEL-CONFIG

## Actor

* Shop Owner
* Shop Manager nếu được cấp quyền
* Integration Admin hỗ trợ ở cấp platform

## Trigger

Shop muốn kết nối Lazada vào ShopHub.

## Preconditions

* Shop đang Active.
* Gói dịch vụ cho phép kết nối Lazada.
* Actor có quyền kết nối kênh.
* Chưa vượt giới hạn số kênh theo plan.

## Main Flow

1. Actor mở màn quản lý kênh bán.
2. Actor chọn kết nối Lazada.
3. Hệ thống chuyển đến quy trình xác thực/kết nối Lazada.
4. Actor hoàn tất xác thực với Lazada.
5. Hệ thống nhận thông tin kết nối/token nếu thành công.
6. Hệ thống lưu cấu hình kết nối Lazada cho shop.
7. Hệ thống kiểm tra kết nối.
8. Hệ thống đặt channel status là `Connected`.
9. Hệ thống tạo sync configuration ban đầu.
10. Hệ thống ghi audit log.

## Alternative Flows

### AF-001: Kết nối thành công nhưng chưa sync dữ liệu

1. Channel status là `Connected`.
2. Sync status là `NotStarted`.
3. User có thể chọn bắt đầu sync hoặc hệ thống chạy sync nền.

### AF-002: Kết nối cần platform support

1. Shop tạo yêu cầu hỗ trợ.
2. Integration Admin kiểm tra trạng thái kết nối.
3. Integration Admin hỗ trợ nhưng không impersonate user shop.

## Error Cases

| Error ID        | Case                            | Handling                                                |
| --------------- | ------------------------------- | ------------------------------------------------------- |
| ERR-CHANNEL-001 | Lazada authorization failed     | Hiển thị lỗi kết nối thất bại.                          |
| ERR-CHANNEL-002 | Plan không cho phép thêm kênh   | Không cho kết nối, gợi ý nâng gói.                      |
| ERR-CHANNEL-003 | Token hết hạn hoặc không hợp lệ | Channel status chuyển `AuthError`, yêu cầu kết nối lại. |

## Resulting State

* Lazada channel được kết nối hoặc báo lỗi rõ ràng.
* Có channel connection status.
* Có audit log.

---

# 10. FLOW-SYNC-LAZADA-ORDER - Sync Lazada Orders

## Related Features

* FEAT-SYNC-ORDER
* FEAT-SYNC-ORDER-STATUS
* FEAT-ORDER-MARKETPLACE-STATUS
* FEAT-ORDER-NORMALIZED-STATUS

## Actor

* System
* Integration Admin theo dõi
* Shop Owner / Shop Manager xem kết quả

## Trigger

* Lazada webhook nếu hỗ trợ.
* Scheduled polling/job nếu chưa có webhook.
* Manual sync theo workflow nếu cần.

## Preconditions

* Shop đang Active.
* Lazada channel đang Connected.
* Token/API credential hợp lệ.
* Lazada API hỗ trợ dữ liệu cần sync.

## Main Flow

1. Hệ thống nhận sự kiện hoặc chạy sync job.
2. Hệ thống gọi/nhận dữ liệu đơn hàng từ Lazada.
3. Hệ thống kiểm tra đơn thuộc shop/channel hợp lệ.
4. Hệ thống lưu marketplace order data gốc cần thiết.
5. Hệ thống lưu marketplace status gốc.
6. Hệ thống mapping marketplace status sang ShopHub normalized status.
7. Nếu đơn mới, hệ thống tạo order trong ShopHub.
8. Nếu đơn đã tồn tại, hệ thống cập nhật thay đổi hợp lệ.
9. Hệ thống đồng bộ thông tin khách hàng từ đơn nếu có.
10. Hệ thống ghi sync log thành công.
11. Nếu cần, hệ thống phát notification cho role liên quan.

## Alternative Flows

### AF-001: Lazada API chưa hỗ trợ dữ liệu mong muốn

1. Hệ thống không giả định dữ liệu tồn tại.
2. Ghi nhận field chưa hỗ trợ hoặc chưa có dữ liệu.
3. Không làm fail toàn bộ sync nếu dữ liệu không bắt buộc.

### AF-002: Order đã tồn tại nhưng trạng thái cũ hơn

1. Hệ thống kiểm tra thời gian cập nhật/version.
2. Không ghi đè dữ liệu mới bằng dữ liệu cũ.
3. Ghi log nếu phát hiện conflict.

## Error Cases

| Error ID           | Case                               | Handling                                         |
| ------------------ | ---------------------------------- | ------------------------------------------------ |
| ERR-SYNC-ORDER-001 | Lazada API lỗi                     | Ghi sync error, retry theo policy.               |
| ERR-SYNC-ORDER-002 | Mapping status không tồn tại       | Ghi trạng thái `Unmapped`, cần review mapping.   |
| ERR-SYNC-ORDER-003 | Dữ liệu order thiếu field bắt buộc | Ghi lỗi data validation, không tạo order sai.    |
| ERR-SYNC-ORDER-004 | Token hết hạn                      | Channel chuyển `AuthError`, yêu cầu kết nối lại. |

## Resulting State

* Order được tạo/cập nhật.
* Marketplace status được lưu.
* ShopHub normalized status được cập nhật.
* Sync log được ghi.

## Audit / Sync Log

Sync log cần lưu:

* Shop ID.
* Channel ID.
* Sync type.
* External order ID.
* Status trước/sau nếu có.
* Kết quả sync.
* Lỗi nếu có.

---

# 11. FLOW-SYNC-LAZADA-STOCK - Sync Available Stock to Lazada

## Related Features

* FEAT-SYNC-STOCK
* FEAT-INVENTORY-AVAILABLE-STOCK

## Actor

* System
* Warehouse Staff / Shop Owner tạo thay đổi tồn kho
* Integration Admin theo dõi lỗi sync

## Trigger

* Available Stock thay đổi.
* Product được bật bán.
* Manual sync nếu cần.

## Preconditions

* Product có mapping/kết nối với Lazada nếu cần.
* Lazada channel đang Connected.
* Available Stock hợp lệ.
* Lazada API hỗ trợ cập nhật tồn kho.

## Main Flow

1. Có sự kiện thay đổi Available Stock.
2. Hệ thống xác định sản phẩm/kênh cần sync.
3. Hệ thống tạo sync job cập nhật tồn kho lên Lazada.
4. Hệ thống gửi Available Stock lên Lazada.
5. Nếu thành công, hệ thống ghi sync log success.
6. Nếu thất bại, hệ thống ghi sync log error và hiển thị cảnh báo.

## Error Cases

| Error ID           | Case                            | Handling                         |
| ------------------ | ------------------------------- | -------------------------------- |
| ERR-SYNC-STOCK-001 | Lazada không nhận stock value   | Ghi lỗi, không đánh dấu success. |
| ERR-SYNC-STOCK-002 | Product chưa mapping với Lazada | Ghi lỗi mapping, yêu cầu xử lý.  |
| ERR-SYNC-STOCK-003 | API limit/rate limit            | Retry theo policy.               |

## Resulting State

* Lazada nhận Available Stock mới hoặc sync error được ghi nhận.
* User liên quan thấy cảnh báo nếu sync lỗi.

---

# 12. FLOW-SYNC-ERROR-RETRY - Handle Sync Error / Retry

## Related Features

* FEAT-SYNC-ERROR
* FEAT-SYNC-RETRY
* FEAT-NOTI-SYNC-ERROR

## Actor

* System
* Integration Admin
* Shop Owner / Shop Manager xem cảnh báo

## Trigger

Một sync job thất bại.

## Preconditions

* Có sync log lỗi.
* Lỗi thuộc loại có thể retry hoặc cần user xử lý.

## Main Flow

1. Hệ thống ghi sync error.
2. Hệ thống phân loại lỗi:

   * Temporary error.
   * Auth error.
   * Data validation error.
   * Mapping error.
   * Rate limit.
3. Nếu lỗi tạm thời, hệ thống retry theo policy.
4. Nếu lỗi cần user xử lý, hệ thống hiển thị cảnh báo.
5. Integration Admin hoặc user có quyền xem chi tiết lỗi.
6. Khi lỗi được xử lý, user/system có thể retry lại.
7. Hệ thống cập nhật trạng thái sync log.

## Error Cases

| Error ID           | Case             | Handling                               |
| ------------------ | ---------------- | -------------------------------------- |
| ERR-SYNC-RETRY-001 | Retry quá số lần | Dừng retry, yêu cầu xử lý thủ công.    |
| ERR-SYNC-RETRY-002 | Lỗi auth         | Không retry vô hạn, yêu cầu reconnect. |
| ERR-SYNC-RETRY-003 | Lỗi dữ liệu      | Yêu cầu sửa dữ liệu trước khi retry.   |

## Resulting State

* Lỗi sync được ghi nhận rõ.
* Có trạng thái retry hoặc failed.
* User biết cần làm gì tiếp theo.

---

# 13. FLOW-SYNC-MANUAL-RESYNC - Manual Resync

## Related Features

* FEAT-SYNC-MANUAL-RESYNC-APPROVAL

## Actor

* Integration Admin
* Shop Owner
* System

## Trigger

Cần đồng bộ lại dữ liệu thủ công do lỗi hoặc nghi ngờ lệch dữ liệu.

## Preconditions

* Có quyền resync.
* Xác định rõ phạm vi resync: order/product/stock/customer/fees.
* Xác định resync có ảnh hưởng dữ liệu quan trọng không.

## Main Flow

1. Integration Admin hoặc user có quyền chọn thao tác manual resync.
2. Hệ thống yêu cầu chọn phạm vi resync.
3. Hệ thống đánh giá mức độ ảnh hưởng dữ liệu:

   * Low impact: chỉ đọc/đối chiếu/log.
   * High impact: có thể ghi đè/cập nhật order, stock, product, fees.
4. Nếu high impact, hệ thống yêu cầu Shop Owner xác nhận.
5. Shop Owner xem cảnh báo và xác nhận.
6. Hệ thống thực hiện resync trong phạm vi đã chọn.
7. Hệ thống ghi sync log và audit log.
8. Hệ thống hiển thị kết quả resync.

## Alternative Flows

### AF-001: Low impact resync

1. Hệ thống không cần Shop Owner xác nhận.
2. Vẫn ghi sync log.

### AF-002: Shop Owner từ chối xác nhận

1. Hệ thống không thực hiện resync.
2. Ghi log yêu cầu bị từ chối.

## Error Cases

| Error ID       | Case                       | Handling                                            |
| -------------- | -------------------------- | --------------------------------------------------- |
| ERR-RESYNC-001 | Không xác định được impact | Mặc định coi là high impact và yêu cầu xác nhận.    |
| ERR-RESYNC-002 | Resync thất bại giữa chừng | Ghi partial result, không đánh dấu success toàn bộ. |
| ERR-RESYNC-003 | User không có quyền        | Từ chối thao tác.                                   |

## Resulting State

* Dữ liệu được resync hoặc yêu cầu bị từ chối.
* Có log đầy đủ.

---

# 14. FLOW-PRODUCT-CREATE - Create Product

## Related Features

* FEAT-PRODUCT-CREATE
* FEAT-PRODUCT-SKU
* FEAT-PRODUCT-PRICE
* FEAT-PRODUCT-COST

## Actor

* Shop Owner
* Shop Manager
* Product Staff

## Trigger

User muốn tạo sản phẩm mới trong shop.

## Preconditions

* User thuộc shop hiện tại.
* User có quyền tạo sản phẩm.
* Shop đang Active.

## Main Flow

1. User mở màn tạo sản phẩm.
2. User nhập thông tin sản phẩm:

   * Tên sản phẩm.
   * SKU.
   * Danh mục.
   * Giá bán.
   * Giá vốn nếu có.
   * Mô tả/hình ảnh nếu có.
   * Trạng thái ban đầu.
3. Hệ thống validate dữ liệu.
4. Hệ thống kiểm tra SKU không trùng trong shop.
5. Hệ thống tạo sản phẩm.
6. Hệ thống đặt product status ban đầu.
7. Nếu sản phẩm cần sync Lazada, tạo sync pending hoặc yêu cầu cấu hình mapping.
8. Hệ thống ghi audit log.

## Error Cases

| Error ID        | Case                 | Handling                   |
| --------------- | -------------------- | -------------------------- |
| ERR-PRODUCT-001 | SKU trùng            | Không tạo, báo lỗi.        |
| ERR-PRODUCT-002 | Thiếu field bắt buộc | Hiển thị validation error. |
| ERR-PRODUCT-003 | Giá bán không hợp lệ | Không cho lưu.             |
| ERR-PRODUCT-004 | Shop bị Suspended    | Không cho tạo sản phẩm.    |

## Resulting State

* Product được tạo trong shop.
* Product có status rõ ràng.
* Có audit log.

---

# 15. FLOW-PRODUCT-UPDATE-STATUS - Update Product Status

## Related Features

* FEAT-PRODUCT-STATUS
* FEAT-PRODUCT-LAZADA-SYNC-STATUS

## Actor

* Shop Owner
* Shop Manager
* Product Staff
* System khi nhận status từ Lazada

## Trigger

* User cập nhật trạng thái sản phẩm.
* Lazada sync cập nhật marketplace product status.

## Preconditions

* Product thuộc shop hiện tại.
* Actor có quyền cập nhật product status hoặc system sync hợp lệ.

## Main Flow

1. Actor chọn sản phẩm.
2. Actor thay đổi trạng thái nội bộ hoặc hệ thống nhận trạng thái từ Lazada.
3. Hệ thống lưu marketplace status nếu đến từ Lazada.
4. Hệ thống mapping sang ShopHub normalized product status nếu cần.
5. Nếu shop dùng custom status, hệ thống lưu custom status riêng.
6. Nếu thay đổi ảnh hưởng khả năng bán, hệ thống kiểm tra Available Stock và channel sync.
7. Hệ thống ghi audit log.

## Error Cases

| Error ID               | Case                          | Handling                                  |
| ---------------------- | ----------------------------- | ----------------------------------------- |
| ERR-PRODUCT-STATUS-001 | Status từ Lazada chưa mapping | Ghi SyncError/Unmapped và yêu cầu review. |
| ERR-PRODUCT-STATUS-002 | User không có quyền           | Từ chối thao tác.                         |
| ERR-PRODUCT-STATUS-003 | Status không hợp lệ           | Không cho lưu.                            |

## Resulting State

* Product status được cập nhật theo lớp phù hợp.
* Có audit log/sync log nếu liên quan.

---

# 16. FLOW-ORDER-REVIEW-CONFIRM - Review & Confirm Order

## Related Features

* FEAT-ORDER-LIST
* FEAT-ORDER-DETAIL
* FEAT-ORDER-UPDATE-STATUS
* FEAT-INVENTORY-AVAILABLE-STOCK
* FEAT-INVENTORY-RESERVED-STOCK

## Actor

* Shop Owner
* Shop Manager
* Order Staff

## Trigger

Có đơn mới từ Lazada hoặc đơn ở trạng thái cần xác nhận.

## Preconditions

* Order thuộc shop hiện tại.
* Order đang ở trạng thái cho phép xác nhận.
* User có quyền xử lý đơn.
* Sản phẩm/tồn kho đủ điều kiện xác nhận.

## Main Flow

1. User mở danh sách đơn hàng.
2. User chọn đơn cần xử lý.
3. User kiểm tra thông tin đơn:

   * Sản phẩm.
   * Số lượng.
   * Khách hàng.
   * Địa chỉ/giao hàng nếu có.
   * Marketplace status.
   * ShopHub normalized status.
4. User chọn xác nhận đơn.
5. Hệ thống kiểm tra tồn kho khả dụng.
6. Nếu đủ hàng, hệ thống:

   * Giảm Available Stock.
   * Tăng Reserved Stock.
   * Cập nhật order status thành `Confirmed` hoặc status tương ứng.
7. Hệ thống ghi order audit log.
8. Hệ thống tạo inventory movement log.
9. Nếu cần sync lại stock lên Lazada, hệ thống tạo sync job.

## Alternative Flows

### AF-001: Không đủ Available Stock

1. Hệ thống không xác nhận đơn.
2. Hiển thị cảnh báo thiếu hàng.
3. User xử lý theo nghiệp vụ: chờ nhập hàng, hủy đơn, hoặc hành động khác tùy rule.

### AF-002: Đơn đã được sàn cập nhật trạng thái khác

1. Hệ thống sync trạng thái mới.
2. User phải refresh/review lại trước khi xác nhận.

## Error Cases

| Error ID              | Case                            | Handling          |
| --------------------- | ------------------------------- | ----------------- |
| ERR-ORDER-CONFIRM-001 | Available Stock không đủ        | Không xác nhận.   |
| ERR-ORDER-CONFIRM-002 | Order status không cho xác nhận | Từ chối thao tác. |
| ERR-ORDER-CONFIRM-003 | User không có quyền             | Từ chối thao tác. |

## Resulting State

* Order được xác nhận.
* Available Stock giảm.
* Reserved Stock tăng.
* Có audit log và inventory log.

---

# 17. FLOW-ORDER-CANCEL - Cancel Order

## Related Features

* FEAT-ORDER-CANCEL
* FEAT-ORDER-AUDIT
* FEAT-INVENTORY-AVAILABLE-STOCK
* FEAT-INVENTORY-RESERVED-STOCK

## Actor

* Shop Owner
* Shop Manager
* Order Staff

## Trigger

User muốn hủy đơn hàng.

## Preconditions

* Order thuộc shop hiện tại.
* User có quyền hủy đơn.
* Order status cho phép hủy.
* Nếu đơn đến từ Lazada, cần kiểm tra rule/trạng thái của Lazada.

## Main Flow

1. User mở chi tiết đơn hàng.
2. User chọn `Hủy đơn`.
3. Hệ thống kiểm tra trạng thái đơn có được hủy không.
4. Hệ thống hiển thị confirm dialog.
5. User nhập/chọn lý do hủy nếu bắt buộc.
6. User xác nhận hủy.
7. Hệ thống cập nhật order status thành `Cancelled` hoặc status tương ứng.
8. Nếu đơn đã reserved stock nhưng chưa xuất kho:

   * Giảm Reserved Stock.
   * Tăng Available Stock.
9. Nếu cần sync hủy đơn lên Lazada, hệ thống tạo sync job hoặc gọi API nếu được hỗ trợ.
10. Hệ thống ghi order audit log.
11. Hệ thống ghi inventory movement log nếu có thay đổi tồn kho.

## Alternative Flows

### AF-001: User thoát confirm dialog

1. Hệ thống không hủy đơn.
2. Order giữ nguyên trạng thái.

### AF-002: Lazada không cho hủy đơn

1. Hệ thống không cập nhật hủy thành công.
2. Hiển thị lỗi từ sàn hoặc trạng thái không cho hủy.
3. Ghi sync/error log nếu có gọi API.

## Error Cases

| Error ID             | Case                         | Handling                                                    |
| -------------------- | ---------------------------- | ----------------------------------------------------------- |
| ERR-ORDER-CANCEL-001 | Order status không cho hủy   | Không cho hủy.                                              |
| ERR-ORDER-CANCEL-002 | User không confirm           | Không thực hiện.                                            |
| ERR-ORDER-CANCEL-003 | User không có quyền          | Từ chối thao tác.                                           |
| ERR-ORDER-CANCEL-004 | Sync hủy lên Lazada thất bại | Order cần trạng thái pending sync hoặc sync error tùy rule. |

## Resulting State

* Order bị hủy nếu hợp lệ.
* Tồn kho được hoàn lại nếu đã reserved nhưng chưa xuất.
* Có confirm, audit log và inventory log.

---

# 18. FLOW-ORDER-SHIPMENT - Process Shipment / Stock Out

## Related Features

* FEAT-SHIPPING-STATUS
* FEAT-SHIPPING-TRACKING
* FEAT-INVENTORY-STOCK-OUT

## Actor

* Warehouse Staff
* Order Staff
* Shop Manager
* System nếu nhận trạng thái từ vận chuyển/sàn

## Trigger

Đơn sẵn sàng giao hoặc có trạng thái vận chuyển cần xử lý.

## Preconditions

* Order đã Confirmed/Processing/ReadyToShip.
* Order có Reserved Stock.
* User có quyền xử lý giao/xuất kho.

## Main Flow

1. User mở đơn cần giao/xuất kho.
2. User kiểm tra sản phẩm và số lượng cần xuất.
3. User xác nhận xuất kho/giao hàng.
4. Hệ thống kiểm tra Reserved Stock.
5. Hệ thống giảm Physical Stock.
6. Hệ thống giảm Reserved Stock.
7. Hệ thống cập nhật order/shipping status.
8. Nếu có tracking code, lưu tracking information.
9. Hệ thống ghi inventory movement log.
10. Hệ thống ghi order audit log.
11. Nếu cần, hệ thống sync trạng thái/tồn kho lên Lazada.

## Error Cases

| Error ID     | Case                        | Handling            |
| ------------ | --------------------------- | ------------------- |
| ERR-SHIP-001 | Reserved Stock không đủ     | Không cho xuất kho. |
| ERR-SHIP-002 | Order status không cho xuất | Từ chối thao tác.   |
| ERR-SHIP-003 | User không có quyền         | Từ chối thao tác.   |

## Resulting State

* Physical Stock giảm.
* Reserved Stock giảm.
* Order/shipment status được cập nhật.

---

# 19. FLOW-INVENTORY-STOCK-IN - Stock In

## Related Features

* FEAT-INVENTORY-STOCK-IN
* FEAT-INVENTORY-LOG

## Actor

* Warehouse Staff
* Shop Owner
* Shop Manager

## Trigger

Shop nhập thêm hàng vào kho vật lý.

## Preconditions

* Product tồn tại trong shop.
* User có quyền nhập kho.
* Shop đang Active.

## Main Flow

1. User mở màn nhập kho.
2. User chọn sản phẩm.
3. User nhập số lượng nhập kho.
4. User nhập lý do/ghi chú hoặc chứng từ nếu có.
5. Hệ thống validate số lượng.
6. Hệ thống tăng Physical Stock.
7. Hệ thống tăng Available Stock nếu hàng có thể bán.
8. Hệ thống ghi inventory movement log.
9. Nếu Available Stock thay đổi, tạo sync job cập nhật Lazada.

## Error Cases

| Error ID         | Case                  | Handling          |
| ---------------- | --------------------- | ----------------- |
| ERR-STOCK-IN-001 | Số lượng không hợp lệ | Không cho lưu.    |
| ERR-STOCK-IN-002 | Product không tồn tại | Không cho nhập.   |
| ERR-STOCK-IN-003 | User không có quyền   | Từ chối thao tác. |

## Resulting State

* Physical Stock tăng.
* Available Stock tăng nếu phù hợp.
* Có inventory log.

---

# 20. FLOW-INVENTORY-ADJUSTMENT-APPROVAL - Inventory Adjustment Approval

## Related Features

* FEAT-INVENTORY-ADJUSTMENT-REQUEST
* FEAT-INVENTORY-ADJUSTMENT-APPROVAL
* FEAT-INVENTORY-LOG

## Actor

* Warehouse Staff tạo yêu cầu.
* Shop Owner hoặc Shop Manager duyệt.

## Trigger

Warehouse Staff phát hiện tồn kho cần điều chỉnh.

## Preconditions

* Product thuộc shop hiện tại.
* Warehouse Staff có quyền tạo yêu cầu điều chỉnh.
* Adjustment có lý do và bằng chứng.

## Main Flow

1. Warehouse Staff mở màn tạo yêu cầu điều chỉnh tồn kho.
2. Warehouse Staff chọn sản phẩm.
3. Warehouse Staff nhập số lượng điều chỉnh.
4. Warehouse Staff nhập lý do.
5. Warehouse Staff đính kèm bằng chứng nếu cần.
6. Hệ thống tạo adjustment request ở trạng thái `PendingApproval`.
7. Hệ thống thông báo cho Shop Owner/Shop Manager.
8. Shop Owner/Shop Manager xem yêu cầu.
9. Người duyệt chọn Approve hoặc Reject.
10. Nếu Approve:

    * Hệ thống áp dụng điều chỉnh tồn kho.
    * Cập nhật Physical Stock/Available Stock theo rule.
    * Ghi inventory movement log.
    * Ghi audit log.
    * Nếu Available Stock thay đổi, tạo sync job Lazada.
11. Nếu Reject:

    * Hệ thống lưu lý do từ chối.
    * Không thay đổi tồn kho.

## Alternative Flows

### AF-001: Shop Manager là người duyệt

Shop Manager được duyệt adjustment theo decision hiện tại, dù không được quản lý user/role.

### AF-002: Thiếu bằng chứng

1. Người duyệt có thể reject hoặc yêu cầu bổ sung.
2. Yêu cầu vẫn chưa có hiệu lực.

## Error Cases

| Error ID    | Case                                   | Handling                  |
| ----------- | -------------------------------------- | ------------------------- |
| ERR-ADJ-001 | Không có lý do                         | Không cho gửi yêu cầu.    |
| ERR-ADJ-002 | User không có quyền duyệt              | Không cho approve/reject. |
| ERR-ADJ-003 | Adjustment làm tồn kho âm không hợp lệ | Không áp dụng.            |

## Resulting State

* Nếu approved: tồn kho thay đổi và có log.
* Nếu rejected: tồn kho không đổi.

---

# 21. FLOW-CUSTOMER-CARE-TICKET - Customer Care Issue / Ticket

## Related Features

* FEAT-CUSTOMER-ISSUE-TRACKING
* FEAT-CUSTOMER-NOTES
* FEAT-CUSTOMER-ORDER-HISTORY

## Actor

* Customer Support
* Order Staff
* Shop Manager
* Shop Owner

## Trigger

Khách hàng có vấn đề/khiếu nại/yêu cầu hỗ trợ liên quan đơn hàng.

## Preconditions

* Customer thuộc shop hiện tại.
* User có quyền xem customer/order tương ứng.

## Main Flow

1. User tìm khách hàng hoặc mở từ chi tiết đơn hàng.
2. User tạo issue/ticket mới.
3. User nhập thông tin:

   * Loại vấn đề.
   * Mô tả.
   * Đơn hàng liên quan nếu có.
   * Mức độ ưu tiên nếu cần.
4. Hệ thống tạo ticket ở trạng thái `Open`.
5. User ghi chú chăm sóc khách hàng.
6. User có thể cập nhật trạng thái ticket:

   * Open.
   * InProgress.
   * WaitingCustomer.
   * Resolved.
   * Closed.
7. Nếu vấn đề cần xử lý đơn, user chuyển cho Order Staff/Shop Manager.
8. Hệ thống ghi lịch sử ticket.

## Error Cases

| Error ID       | Case                      | Handling              |
| -------------- | ------------------------- | --------------------- |
| ERR-TICKET-001 | Customer không thuộc shop | Không cho truy cập.   |
| ERR-TICKET-002 | User không có quyền       | Từ chối thao tác.     |
| ERR-TICKET-003 | Thiếu mô tả vấn đề        | Không cho tạo ticket. |

## Resulting State

* Ticket được tạo và theo dõi riêng.
* Có lịch sử chăm sóc khách hàng.

---

# 22. FLOW-PROFIT-FORMULA-APPROVAL - Profit Formula Approval

## Related Features

* FEAT-REPORT-PROFIT-FORMULA
* FEAT-REPORT-PROFIT-FORMULA-APPROVAL
* FEAT-REPORT-DATA-QUALITY

## Actor

* Accountant tạo/chỉnh nháp.
* Shop Owner hoặc Shop Manager duyệt.

## Trigger

Shop muốn thay đổi công thức lợi nhuận.

## Preconditions

* User có quyền đề xuất hoặc chỉnh công thức.
* Công thức hợp lệ về mặt cấu trúc.
* Shop đang Active.

## Main Flow

1. Accountant mở phần cấu hình công thức lợi nhuận.
2. Accountant chỉnh thành phần công thức:

   * Revenue.
   * Cost of Goods Sold.
   * Marketplace Fees.
   * Shipping Fees.
   * Discounts.
   * Other Costs.
3. Hệ thống validate công thức.
4. Hệ thống tạo bản nháp `PendingApproval`.
5. Hệ thống thông báo cho Shop Owner/Shop Manager.
6. Người duyệt xem thay đổi trước/sau.
7. Người duyệt Approve hoặc Reject.
8. Nếu Approve:

   * Công thức mới có hiệu lực.
   * Hệ thống ghi audit log.
9. Nếu Reject:

   * Công thức cũ giữ nguyên.
   * Hệ thống lưu lý do từ chối nếu có.

## Error Cases

| Error ID       | Case                       | Handling                             |
| -------------- | -------------------------- | ------------------------------------ |
| ERR-PROFIT-001 | Công thức không hợp lệ     | Không cho gửi duyệt.                 |
| ERR-PROFIT-002 | Người duyệt không có quyền | Từ chối thao tác.                    |
| ERR-PROFIT-003 | Dữ liệu đầu vào thiếu      | Cảnh báo report có thể không đầy đủ. |

## Resulting State

* Công thức được áp dụng nếu approved.
* Có audit log.

---

# 23. FLOW-REPORT-EXPORT - Export Report

## Related Features

* FEAT-REPORT-EXPORT

## Actor

* Shop Owner
* Shop Manager
* Accountant

## Trigger

User muốn xuất báo cáo ra Excel hoặc PDF.

## Preconditions

* User có quyền xem loại báo cáo đó.
* Dữ liệu report thuộc shop hiện tại.
* Format export cho loại báo cáo đã được định nghĩa.

## Main Flow

1. User mở báo cáo.
2. User chọn khoảng thời gian/bộ lọc.
3. User chọn Export.
4. User chọn định dạng:

   * Excel.
   * PDF.
5. Hệ thống kiểm tra quyền export.
6. Hệ thống áp dụng format tương ứng cho loại báo cáo.
7. Hệ thống tạo file export.
8. User tải file.
9. Hệ thống ghi audit log nếu báo cáo chứa dữ liệu nhạy cảm.

## Error Cases

| Error ID       | Case                        | Handling                               |
| -------------- | --------------------------- | -------------------------------------- |
| ERR-EXPORT-001 | Không có quyền export       | Từ chối thao tác.                      |
| ERR-EXPORT-002 | Format chưa được định nghĩa | Không export, báo lỗi cấu hình.        |
| ERR-EXPORT-003 | Dữ liệu quá lớn             | Yêu cầu thu hẹp bộ lọc hoặc xử lý nền. |

## Resulting State

* File Excel/PDF được tạo.
* Có log nếu cần.

---

# 24. FLOW-SUBSCRIPTION-UPGRADE-PAYMENT - Upgrade Plan with VNPay QR

## Related Features

* FEAT-SUBSCRIPTION-UPGRADE
* FEAT-SUBSCRIPTION-PAYMENT
* FEAT-SUBSCRIPTION-STATUS
* FEAT-SUBSCRIPTION-NO-MANUAL-CHANGE

## Actor

* Shop Owner
* System
* Billing Admin theo dõi

## Trigger

Shop Owner muốn nâng gói dịch vụ.

## Preconditions

* Shop đang Active hoặc Trial.
* Shop Owner có quyền quản lý subscription.
* Plan mới hợp lệ.
* VNPay QR payment đang khả dụng.

## Main Flow

1. Shop Owner mở màn subscription.
2. Shop Owner chọn gói muốn nâng cấp.
3. Hệ thống hiển thị thông tin gói, giá và giới hạn.
4. Shop Owner xác nhận nâng gói.
5. Hệ thống tạo payment request.
6. Hệ thống tạo VNPay QR payment.
7. Shop Owner thanh toán qua QR.
8. Hệ thống nhận kết quả thanh toán từ VNPay.
9. Nếu payment success:

   * Cập nhật subscription plan.
   * Cập nhật subscription status `Active`.
   * Áp dụng giới hạn/tính năng mới.
   * Ghi subscription audit log.
10. Nếu payment failed/pending:

* Không đổi gói.
* Hiển thị trạng thái thanh toán.

## Alternative Flows

### AF-001: Payment pending

1. Subscription chưa đổi sang gói mới.
2. Payment ở trạng thái `PendingPayment`.
3. Hệ thống chờ callback hoặc user kiểm tra lại.

### AF-002: Billing Admin xem giao dịch

1. Billing Admin xem payment record.
2. Billing Admin không được đổi gói thủ công ngoài flow.

## Error Cases

| Error ID    | Case                                                                         | Handling                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| ERR-PAY-001 | VNPay QR tạo thất bại                                                        | Không tạo payment, báo lỗi.                                                                                               |
| ERR-PAY-002 | Payment failed                                                               | Không đổi gói.                                                                                                            |
| ERR-PAY-003 | Payment callback không hợp lệ                                                | Không cập nhật subscription, ghi security log.                                                                            |
| ERR-PAY-004 | Billing Admin cố đổi gói thủ công                                            | Từ chối thao tác.                                                                                                         |
| ERR-PAY-005 | VNPay callback chậm hoặc user đã thanh toán nhưng hệ thống chưa nhận kết quả | Giữ payment ở trạng thái PendingPayment, cho phép kiểm tra lại trạng thái giao dịch qua reconciliation/query transaction. |
| ERR-PAY-006 | VNPay callback success nhưng update subscription lỗi                         | Không bỏ qua lỗi; ghi critical log, tạo payment reconciliation task để xử lý bù.                                          |

## Resulting State

* Nếu thanh toán thành công: gói mới có hiệu lực.
* Nếu thanh toán thất bại: gói cũ giữ nguyên.
* Nếu thanh toán đang chờ hoặc callback chậm: subscription chưa đổi gói, payment ở trạng thái `PendingPayment`.
* Có payment record và audit log.

## VNPay Callback / Reconciliation Direction

| Rule ID      | Rule                                                                                                                                         |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| RULE-PAY-001 | Không cập nhật subscription chỉ dựa vào việc user quay lại trang thành công; phải dựa trên callback/IPN hoặc bước xác minh giao dịch hợp lệ. |
| RULE-PAY-002 | Payment phải có các trạng thái tối thiểu: PendingPayment, Paid, Failed, Expired, Cancelled, NeedReview.                                      |
| RULE-PAY-003 | Nếu callback đến chậm, hệ thống giữ payment ở PendingPayment và cho phép kiểm tra lại giao dịch.                                             |
| RULE-PAY-004 | Nếu callback success nhưng cập nhật subscription lỗi, cần ghi critical log và đưa vào payment reconciliation queue/task.                     |
| RULE-PAY-005 | Subscription chỉ được đổi gói sau khi payment được xác nhận hợp lệ.                                                                          |
| RULE-PAY-006 | Billing Admin không được tự đổi gói thủ công để sửa lỗi thanh toán; chỉ được xử lý thông qua reconciliation workflow có log.                 |

---

# 25. FLOW-SUBSCRIPTION-PASTDUE-SUSPEND - Past Due / Suspend Shop

## Related Features

* FEAT-SUBSCRIPTION-STATUS
* FEAT-NOTI-SUBSCRIPTION
* FEAT-TENANT-SHOP-STATUS

## Actor

* System
* Billing Admin theo dõi
* Shop Owner nhận thông báo

## Trigger

Subscription quá hạn thanh toán.

## Preconditions

* Shop đang dùng gói có thanh toán.
* Payment đến hạn nhưng chưa thanh toán thành công.

## Main Flow

1. Hệ thống kiểm tra subscription định kỳ.
2. Nếu quá hạn, subscription chuyển `PastDue`.
3. Hệ thống gửi notification cho Shop Owner.
4. Nếu quá hạn vượt ngưỡng cho phép, hệ thống chuyển subscription sang `Suspended`.
5. Shop status có thể chuyển `Suspended` hoặc giới hạn tính năng theo rule.
6. Hệ thống ghi subscription audit log.

## Alternative Flows

### AF-001: Shop thanh toán sau khi PastDue

1. Shop Owner thanh toán qua VNPay QR.
2. Payment success.
3. Subscription chuyển về `Active`.
4. Shop được khôi phục quyền/tính năng theo gói.

## Error Cases

| Error ID    | Case                                          | Handling                        |
| ----------- | --------------------------------------------- | ------------------------------- |
| ERR-SUB-001 | Không gửi được notification                   | Ghi log, retry gửi.             |
| ERR-SUB-002 | Payment success nhưng update subscription lỗi | Ghi critical log, cần xử lý bù. |

## Resulting State

* Subscription chuyển đúng trạng thái.
* Shop bị giới hạn hoặc khôi phục theo payment status.

---

# 26. Cross-flow Approval Rules

| Approval Flow             | Requester         | Approver                       | Required Evidence        | Notes                                             |
| ------------------------- | ----------------- | ------------------------------ | ------------------------ | ------------------------------------------------- |
| Inventory Adjustment      | Warehouse Staff   | Shop Owner hoặc Shop Manager   | Lý do + bằng chứng       | Áp dụng trước khi tồn kho thay đổi.               |
| Profit Formula Change     | Accountant        | Shop Owner hoặc Shop Manager   | Công thức trước/sau      | Áp dụng trước khi công thức có hiệu lực.          |
| Manual Resync High Impact | Integration Admin | Shop Owner                     | Phạm vi + impact warning | Chỉ cần khi ảnh hưởng dữ liệu quan trọng.         |
| Cancel Order              | Order Staff       | Self-confirm nếu rule cho phép | Lý do hủy nếu cần        | Không cần approval riêng, nhưng bắt buộc confirm. |

## 26.1 Inventory Adjustment Approval Threshold

Điều chỉnh tồn kho có thể được phân loại theo **giá trị chênh lệch tiền** để quyết định mức duyệt.

| Level                 | Điều kiện đề xuất                                                       | Approver                                                                                                            | Notes                                         |
| --------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------- |
| Low Impact            | Chênh lệch nhỏ hơn 10.000.000 VND cho từng sản phẩm                     | Shop Manager hoặc Shop Owner                                                                                        | Vẫn cần lý do và bằng chứng.                  |
| High Impact           | Chênh lệch từ 10.000.000 VND trở lên cho từng sản phẩm                  | Shop Owner khuyến nghị là người duyệt chính; Shop Manager có thể duyệt nếu được cấu hình rõ trong Permission Matrix | Cần audit log chi tiết hơn.                   |
| Critical / Suspicious | Chênh lệch bất thường, lặp lại nhiều lần, hoặc ảnh hưởng nhiều sản phẩm | Shop Owner                                                                                                          | Có thể cần khóa thao tác hoặc review sâu hơn. |

Công thức gợi ý để tính giá trị chênh lệch:

```txt
Inventory Adjustment Value = ABS(Quantity Difference) × Product Cost hoặc Product Selling Price
```

Ưu tiên dùng **Product Cost** nếu có dữ liệu giá vốn. Nếu thiếu giá vốn, có thể dùng giá bán tạm thời nhưng phải đánh dấu là ước tính.

---

# 27. Cross-flow Audit Requirements

Các flow sau bắt buộc có audit log:

| Flow ID                            | Audit Required | Reason                                  |
| ---------------------------------- | -------------- | --------------------------------------- |
| FLOW-TENANT-ONBOARD-SHOP           | Yes            | Tạo shop/tenant.                        |
| FLOW-USER-INVITE-SHOP-USER         | Yes            | Thay đổi user/role.                     |
| FLOW-CHANNEL-CONNECT-LAZADA        | Yes            | Kết nối kênh bán.                       |
| FLOW-SYNC-MANUAL-RESYNC            | Yes            | Có thể ảnh hưởng dữ liệu.               |
| FLOW-PRODUCT-CREATE                | Yes            | Tạo dữ liệu sản phẩm.                   |
| FLOW-PRODUCT-UPDATE-STATUS         | Yes            | Ảnh hưởng trạng thái bán.               |
| FLOW-ORDER-REVIEW-CONFIRM          | Yes            | Ảnh hưởng đơn và tồn kho.               |
| FLOW-ORDER-CANCEL                  | Yes            | Ảnh hưởng đơn và tồn kho.               |
| FLOW-ORDER-SHIPMENT                | Yes            | Ảnh hưởng xuất kho.                     |
| FLOW-INVENTORY-STOCK-IN            | Yes            | Ảnh hưởng tồn kho.                      |
| FLOW-INVENTORY-ADJUSTMENT-APPROVAL | Yes            | Điều chỉnh tồn kho nhạy cảm.            |
| FLOW-PROFIT-FORMULA-APPROVAL       | Yes            | Ảnh hưởng báo cáo lợi nhuận.            |
| FLOW-SUBSCRIPTION-UPGRADE-PAYMENT  | Yes            | Ảnh hưởng billing/gói dịch vụ.          |
| FLOW-SUBSCRIPTION-PASTDUE-SUSPEND  | Yes            | Ảnh hưởng trạng thái shop/subscription. |

---

# 28. Additional Coverage Flows

Các flow bên dưới được bổ sung sau bước verify để đảm bảo không bị thiếu module/flow quan trọng so với `03_FEATURE_LIST.md`.

---

## 28.1 FLOW-AUTH-LOGIN-SESSION - Login & Session Validation

### Related Features

* FEAT-AUTH-LOGIN
* FEAT-AUTH-SESSION
* FEAT-AUTH-RBAC
* FEAT-AUTH-MULTI-ROLE

### Actor

* Seller-side user
* Platform-side user

### Trigger

User đăng nhập vào ShopHub.

### Preconditions

* User có tài khoản hợp lệ.
* User không bị khóa.
* Shop hoặc platform account đang ở trạng thái cho phép truy cập.

### Main Flow

1. User nhập thông tin đăng nhập.
2. Hệ thống xác thực thông tin.
3. Hệ thống kiểm tra user thuộc seller-side hay platform-side.
4. Nếu là seller-side user, hệ thống kiểm tra shop/tenant status.
5. Hệ thống tải danh sách role/quyền của user.
6. Hệ thống tạo session/token.
7. User được đưa vào dashboard phù hợp với scope của mình.
8. Hệ thống ghi login event nếu cần.

### Error Cases

| Error ID     | Case                      | Handling                                                            |
| ------------ | ------------------------- | ------------------------------------------------------------------- |
| ERR-AUTH-001 | Sai thông tin đăng nhập   | Từ chối đăng nhập.                                                  |
| ERR-AUTH-002 | User bị khóa              | Từ chối đăng nhập, hiển thị thông báo phù hợp.                      |
| ERR-AUTH-003 | Shop bị Suspended         | Cho đăng nhập hạn chế hoặc chặn theo rule subscription/shop status. |
| ERR-AUTH-004 | User không có role hợp lệ | Từ chối truy cập dashboard.                                         |

### Resulting State

* User đăng nhập thành công và có quyền theo role.
* Hoặc bị từ chối với lý do rõ ràng.

---

## 28.2 FLOW-AUTH-RESET-PASSWORD - Forgot / Reset Password

### Related Features

* FEAT-AUTH-FORGOT-PASSWORD
* FEAT-AUTH-RESET-PASSWORD

### Actor

* Login user bị quên mật khẩu

### Trigger

User chọn quên mật khẩu.

### Preconditions

* Email tồn tại trong hệ thống.
* User không bị khóa vĩnh viễn.

### Main Flow

1. User nhập email.
2. Hệ thống kiểm tra email.
3. Hệ thống tạo reset token có thời hạn.
4. Hệ thống gửi link reset password.
5. User mở link và nhập mật khẩu mới.
6. Hệ thống validate mật khẩu.
7. Hệ thống cập nhật mật khẩu.
8. Token hết hiệu lực.
9. User đăng nhập lại bằng mật khẩu mới.

### Error Cases

| Error ID      | Case                    | Handling                   |
| ------------- | ----------------------- | -------------------------- |
| ERR-RESET-001 | Token hết hạn           | Yêu cầu gửi lại link.      |
| ERR-RESET-002 | Token không hợp lệ      | Từ chối reset.             |
| ERR-RESET-003 | Mật khẩu không đạt rule | Hiển thị validation error. |

---

## 28.3 FLOW-USER-PLATFORM-INVITE - Invite Platform User

### Related Features

* FEAT-USER-INVITE-PLATFORM-USER
* FEAT-USER-MANAGE-PLATFORM-USER

### Actor

* ShopHub Super Admin
* ShopHub Admin có quyền

### Trigger

Platform cần thêm nhân sự vận hành ShopHub.

### Preconditions

* Actor có quyền mời platform user.
* Role được gán là platform-side role hợp lệ.

### Main Flow

1. Platform admin mở màn quản lý platform user.
2. Nhập email người được mời.
3. Chọn platform role.
4. Hệ thống tạo invitation.
5. Người được mời accept invitation.
6. Hệ thống tạo/gán platform account.
7. Hệ thống ghi audit log.

### Error Cases

| Error ID      | Case                     | Handling                    |
| ------------- | ------------------------ | --------------------------- |
| ERR-PUSER-001 | Role không hợp lệ        | Không cho gửi invite.       |
| ERR-PUSER-002 | Actor không đủ quyền     | Từ chối thao tác.           |
| ERR-PUSER-003 | User đã là platform user | Không tạo invitation trùng. |

---

## 28.4 FLOW-TENANT-SHOP-SUSPEND-RESTORE - Suspend / Restore Shop

### Related Features

* FEAT-TENANT-SHOP-STATUS
* FEAT-SUBSCRIPTION-STATUS

### Actor

* System
* ShopHub Admin
* Billing Admin trong trường hợp billing-related

### Trigger

Shop cần bị tạm khóa hoặc khôi phục.

### Preconditions

* Shop tồn tại.
* Actor/system có lý do hợp lệ.

### Main Flow

1. Actor/system xác định lý do suspend hoặc restore.
2. Hệ thống kiểm tra quyền và trạng thái hiện tại.
3. Nếu suspend:

   * Shop status chuyển `Suspended`.
   * Hạn chế hoặc chặn các flow vận hành theo rule.
4. Nếu restore:

   * Kiểm tra điều kiện khôi phục.
   * Shop status chuyển lại `Active`.
5. Hệ thống ghi audit log.
6. Hệ thống thông báo cho Shop Owner nếu cần.

### Error Cases

| Error ID            | Case                       | Handling                       |
| ------------------- | -------------------------- | ------------------------------ |
| ERR-SHOP-STATUS-001 | Actor không có quyền       | Từ chối thao tác.              |
| ERR-SHOP-STATUS-002 | Điều kiện restore chưa đạt | Không restore, hiển thị lý do. |

---

## 28.5 FLOW-DASHBOARD-SELLER-VIEW - View Seller Dashboard

### Related Features

* FEAT-DASHBOARD-SELLER-OVERVIEW
* FEAT-DASHBOARD-ORDER-SUMMARY
* FEAT-DASHBOARD-INVENTORY-ALERT
* FEAT-DASHBOARD-SYNC-ALERT
* FEAT-DASHBOARD-PROFIT-SUMMARY

### Actor

* Shop Owner
* Shop Manager
* Accountant
* Order Staff / Warehouse Staff với view hạn chế

### Trigger

User mở dashboard shop.

### Preconditions

* User đã đăng nhập.
* User thuộc shop hiện tại.
* User có quyền xem dashboard.

### Main Flow

1. User mở dashboard.
2. Hệ thống xác định shop/tenant hiện tại.
3. Hệ thống tải các chỉ số theo role:

   * Order summary.
   * Inventory alert.
   * Sync alert.
   * Revenue/profit nếu có quyền.
4. Hệ thống kiểm tra dữ liệu thiếu, ví dụ thiếu giá vốn/phí.
5. Hệ thống hiển thị cảnh báo dữ liệu chưa đầy đủ nếu cần.
6. User có thể đi đến màn chi tiết từ từng widget/card.

### Error Cases

| Error ID     | Case                                     | Handling                       |
| ------------ | ---------------------------------------- | ------------------------------ |
| ERR-DASH-001 | User không có quyền xem chỉ số tài chính | Ẩn financial widget.           |
| ERR-DASH-002 | Dữ liệu sync lỗi                         | Hiển thị sync alert.           |
| ERR-DASH-003 | Dữ liệu profit thiếu                     | Hiển thị data quality warning. |

---

## 28.6 FLOW-CATEGORY-MANAGE - Manage Category

### Related Features

* FEAT-CATEGORY-LIST
* FEAT-CATEGORY-CREATE
* FEAT-CATEGORY-UPDATE
* FEAT-CATEGORY-DEACTIVATE
* FEAT-CATEGORY-LAZADA-MAPPING

### Actor

* Shop Owner
* Shop Manager
* Product Staff

### Trigger

User cần quản lý danh mục sản phẩm.

### Preconditions

* User thuộc shop hiện tại.
* User có quyền quản lý category.

### Main Flow

1. User mở danh sách category.
2. User tạo hoặc sửa category.
3. Hệ thống validate tên/mã category trong phạm vi shop.
4. Nếu category cần mapping Lazada, user hoặc Integration Admin cấu hình mapping.
5. Hệ thống lưu thay đổi.
6. Hệ thống ghi audit log nếu thay đổi ảnh hưởng sản phẩm/kênh bán.

### Error Cases

| Error ID    | Case                                          | Handling                                |
| ----------- | --------------------------------------------- | --------------------------------------- |
| ERR-CAT-001 | Category trùng tên/mã nếu rule không cho phép | Không cho lưu.                          |
| ERR-CAT-002 | Category đang có sản phẩm nhưng bị xóa cứng   | Không cho xóa cứng, đề xuất deactivate. |
| ERR-CAT-003 | Mapping Lazada không hợp lệ                   | Hiển thị mapping error.                 |

---

## 28.7 FLOW-PRODUCT-UPDATE - Update Product Information

### Related Features

* FEAT-PRODUCT-UPDATE
* FEAT-PRODUCT-PRICE
* FEAT-PRODUCT-COST
* FEAT-PRODUCT-LAZADA-SYNC-STATUS

### Actor

* Shop Owner
* Shop Manager
* Product Staff
* Accountant với dữ liệu giá vốn nếu được cấp quyền

### Trigger

User cập nhật thông tin sản phẩm.

### Preconditions

* Product thuộc shop hiện tại.
* User có quyền sửa field tương ứng.

### Main Flow

1. User mở chi tiết sản phẩm.
2. User cập nhật thông tin được phép sửa.
3. Hệ thống validate dữ liệu.
4. Nếu sửa giá vốn, hệ thống kiểm tra quyền tài chính.
5. Nếu sửa field cần sync Lazada, hệ thống đánh dấu sync pending.
6. Hệ thống lưu thay đổi.
7. Hệ thống ghi audit log.
8. Hệ thống tạo sync job nếu cần.

### Error Cases

| Error ID         | Case                                 | Handling                        |
| ---------------- | ------------------------------------ | ------------------------------- |
| ERR-PROD-UPD-001 | User không có quyền sửa field        | Từ chối field/action.           |
| ERR-PROD-UPD-002 | Dữ liệu không hợp lệ                 | Hiển thị validation error.      |
| ERR-PROD-UPD-003 | Sync field nhưng chưa kết nối Lazada | Lưu nội bộ, cảnh báo chưa sync. |

---

## 28.8 FLOW-PRODUCT-DEACTIVATE - Safe Product Deactivation

### Related Features

* FEAT-PRODUCT-DELETE-SAFE

### Actor

* Shop Owner
* Shop Manager

### Trigger

User muốn xóa hoặc ngừng sử dụng sản phẩm.

### Preconditions

* Product thuộc shop hiện tại.
* User có quyền deactivate/archive.

### Main Flow

1. User chọn deactivate/archive sản phẩm.
2. Hệ thống kiểm tra sản phẩm đã phát sinh đơn chưa.
3. Nếu đã phát sinh đơn, hệ thống không xóa cứng.
4. Hệ thống chuyển product status sang `Inactive` hoặc `Archived`.
5. Nếu sản phẩm đang sync Lazada, hệ thống tạo sync job cập nhật trạng thái nếu API hỗ trợ.
6. Hệ thống ghi audit log.

### Error Cases

| Error ID         | Case                                | Handling            |
| ---------------- | ----------------------------------- | ------------------- |
| ERR-PROD-DEL-001 | User cố xóa cứng sản phẩm đã có đơn | Không cho xóa cứng. |
| ERR-PROD-DEL-002 | User không có quyền                 | Từ chối thao tác.   |

---

## 28.9 FLOW-CUSTOMER-VIEW-MANAGE - View Customer & Order History

### Related Features

* FEAT-CUSTOMER-LIST
* FEAT-CUSTOMER-DETAIL
* FEAT-CUSTOMER-ORDER-HISTORY
* FEAT-CUSTOMER-NO-LOGIN

### Actor

* Shop Owner
* Shop Manager
* Customer Support
* Order Staff
* Accountant với quyền xem phù hợp

### Trigger

User cần tra cứu khách hàng hoặc lịch sử mua hàng.

### Preconditions

* Customer thuộc shop hiện tại.
* User có quyền xem dữ liệu khách hàng.

### Main Flow

1. User mở danh sách khách hàng.
2. User tìm kiếm/lọc khách hàng.
3. User mở chi tiết khách hàng.
4. Hệ thống hiển thị thông tin khách hàng theo quyền.
5. Hệ thống hiển thị lịch sử đơn hàng của khách trong shop.
6. User có thể tạo ghi chú hoặc ticket nếu có quyền.

### Error Cases

| Error ID    | Case                                     | Handling              |
| ----------- | ---------------------------------------- | --------------------- |
| ERR-CUS-001 | Customer không thuộc shop                | Từ chối truy cập.     |
| ERR-CUS-002 | User không có quyền xem dữ liệu nhạy cảm | Ẩn hoặc mask dữ liệu. |

### Business Rules

| Rule ID           | Rule                                                                                |
| ----------------- | ----------------------------------------------------------------------------------- |
| RULE-CUS-FLOW-001 | Khách mua hàng cuối không có tài khoản đăng nhập ShopHub.                           |
| RULE-CUS-FLOW-002 | Customer data chỉ dùng trong phạm vi quản lý đơn hàng/chăm sóc khách hàng của shop. |

---

## 28.10 FLOW-REPORT-VIEW - View Reports

### Related Features

* FEAT-REPORT-REVENUE
* FEAT-REPORT-PROFIT
* FEAT-REPORT-ORDER
* FEAT-REPORT-PRODUCT
* FEAT-REPORT-INVENTORY
* FEAT-REPORT-CHANNEL
* FEAT-REPORT-DATA-QUALITY

### Actor

* Shop Owner
* Shop Manager
* Accountant
* Role khác nếu được cấp quyền view report hạn chế

### Trigger

User mở báo cáo.

### Preconditions

* User có quyền xem loại báo cáo.
* Dữ liệu thuộc shop hiện tại.

### Main Flow

1. User chọn loại báo cáo.
2. User chọn khoảng thời gian/filter.
3. Hệ thống tải dữ liệu theo tenant và permission.
4. Hệ thống tính toán chỉ số.
5. Nếu là profit report, hệ thống áp dụng công thức lợi nhuận hiện hành của shop.
6. Nếu thiếu giá vốn/phí/dữ liệu đầu vào, hệ thống hiển thị warning.
7. User xem báo cáo hoặc chuyển sang export nếu có quyền.

### Error Cases

| Error ID       | Case                            | Handling                       |
| -------------- | ------------------------------- | ------------------------------ |
| ERR-REPORT-001 | User không có quyền xem báo cáo | Từ chối hoặc ẩn report.        |
| ERR-REPORT-002 | Dữ liệu thiếu                   | Hiển thị data quality warning. |
| ERR-REPORT-003 | Bộ lọc không hợp lệ             | Hiển thị validation error.     |

---

## 28.11 FLOW-SUBSCRIPTION-DOWNGRADE - Downgrade Plan

### Related Features

* FEAT-SUBSCRIPTION-DOWNGRADE
* FEAT-SUBSCRIPTION-PLAN-LIMITS
* FEAT-SUBSCRIPTION-STATUS

### Actor

* Shop Owner
* System

### Trigger

Shop Owner muốn hạ gói dịch vụ.

### Preconditions

* Shop Owner có quyền subscription.
* Plan mới hợp lệ.
* Hệ thống xác định được ảnh hưởng của việc hạ gói.

### Main Flow

1. Shop Owner chọn hạ gói.
2. Hệ thống hiển thị thay đổi giới hạn/tính năng.
3. Hệ thống kiểm tra shop hiện có vượt giới hạn gói mới không:

   * Số user.
   * Số kênh.
   * Số đơn/tháng.
   * Report/audit feature.
4. Nếu vượt giới hạn, hệ thống yêu cầu xử lý trước hoặc cho biết tính năng nào sẽ bị khóa.
5. Shop Owner confirm downgrade.
6. Hệ thống cập nhật subscription theo policy.
7. Hệ thống ghi audit log.

### Error Cases

| Error ID          | Case                       | Handling                                         |
| ----------------- | -------------------------- | ------------------------------------------------ |
| ERR-DOWNGRADE-001 | Shop vượt giới hạn gói mới | Chặn downgrade hoặc yêu cầu xử lý vượt giới hạn. |
| ERR-DOWNGRADE-002 | User không confirm         | Không thay đổi gói.                              |

---

## 28.12 FLOW-AUDIT-VIEW - View Audit Log

### Related Features

* FEAT-AUDIT-ORDER
* FEAT-AUDIT-INVENTORY
* FEAT-AUDIT-USER-ROLE
* FEAT-AUDIT-SUBSCRIPTION
* FEAT-AUDIT-SYNC
* FEAT-AUDIT-REPORT-FORMULA

### Actor

* Shop Owner
* ShopHub Admin
* Platform Auditor
* Role chuyên trách theo phạm vi log

### Trigger

User cần kiểm tra lịch sử thao tác.

### Preconditions

* User có quyền xem audit log tương ứng.
* Log thuộc shop hoặc platform scope mà user được phép xem.

### Main Flow

1. User mở audit log.
2. User chọn filter theo thời gian, actor, module, action.
3. Hệ thống tải audit log theo permission và tenant boundary.
4. User xem chi tiết log.
5. Nếu log chứa dữ liệu nhạy cảm, hệ thống mask dữ liệu theo quyền.

### Error Cases

| Error ID      | Case                        | Handling          |
| ------------- | --------------------------- | ----------------- |
| ERR-AUDIT-001 | User không có quyền xem log | Từ chối truy cập. |
| ERR-AUDIT-002 | Log thuộc shop khác         | Từ chối truy cập. |

---

## 28.13 FLOW-NOTIFICATION-HANDLE - Handle Notifications

### Related Features

* FEAT-NOTI-TOAST
* FEAT-NOTI-SYNC-ERROR
* FEAT-NOTI-LOW-STOCK
* FEAT-NOTI-APPROVAL
* FEAT-NOTI-SUBSCRIPTION

### Actor

* System
* User nhận notification

### Trigger

Có sự kiện cần thông báo.

### Main Flow

1. Hệ thống phát hiện event:

   * Sync error.
   * Low stock.
   * Approval request.
   * Subscription issue.
   * Action success/fail.
2. Hệ thống xác định người nhận theo role và tenant.
3. Hệ thống tạo notification/toast.
4. User xem notification.
5. User click để đi đến màn xử lý nếu có.

### Error Cases

| Error ID     | Case                                     | Handling                      |
| ------------ | ---------------------------------------- | ----------------------------- |
| ERR-NOTI-001 | Không xác định được người nhận           | Ghi log, không gửi sai người. |
| ERR-NOTI-002 | Notification liên quan dữ liệu shop khác | Không hiển thị.               |

---

## 28.14 FLOW-SETTING-MANAGE - Manage Settings

### Related Features

* FEAT-SETTING-SHOP-PROFILE
* FEAT-SETTING-PROFIT-FORMULA
* FEAT-SETTING-INVENTORY
* FEAT-SETTING-CHANNEL
* FEAT-SETTING-PLATFORM

### Actor

* Shop Owner
* Shop Manager với một số shop settings
* ShopHub Admin / Super Admin với platform settings

### Trigger

User cần thay đổi cấu hình.

### Preconditions

* User có quyền với setting tương ứng.
* Setting thuộc shop/platform scope phù hợp.

### Main Flow

1. User mở màn settings.
2. User chọn nhóm setting.
3. Hệ thống hiển thị field theo quyền.
4. User cập nhật cấu hình.
5. Hệ thống validate.
6. Nếu setting nhạy cảm, hệ thống yêu cầu confirm hoặc approval.
7. Hệ thống lưu setting.
8. Hệ thống ghi audit log nếu cần.

### Error Cases

| Error ID        | Case                                    | Handling                                |
| --------------- | --------------------------------------- | --------------------------------------- |
| ERR-SETTING-001 | User không có quyền                     | Từ chối thao tác.                       |
| ERR-SETTING-002 | Setting không hợp lệ                    | Không lưu, hiển thị lỗi.                |
| ERR-SETTING-003 | Setting ảnh hưởng sync/inventory/report | Yêu cầu confirm hoặc approval tùy rule. |

---

# 29. Verification Coverage Matrix

Bảng này dùng để tự kiểm tra xem module trong `03_FEATURE_LIST.md` đã có flow tương ứng trong tài liệu này chưa.

| Module ID        | Module Name                     | Coverage Status | Related Flow(s)                                                                                                                | Verify Note                                                                 |
| ---------------- | ------------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| MOD-AUTH         | Authentication & Authorization  | Covered         | FLOW-AUTH-LOGIN-SESSION, FLOW-AUTH-RESET-PASSWORD                                                                              | Đã bổ sung sau verify.                                                      |
| MOD-TENANT       | Shop / Tenant Management        | Covered         | FLOW-TENANT-ONBOARD-SHOP, FLOW-TENANT-SHOP-SUSPEND-RESTORE                                                                     | Đủ cho onboarding/status; multi-shop user để later.                         |
| MOD-USER         | User & Role Management          | Covered         | FLOW-USER-INVITE-SHOP-USER, FLOW-USER-ACCEPT-INVITE, FLOW-USER-PLATFORM-INVITE                                                 | Đủ cho invite seller/platform.                                              |
| MOD-DASHBOARD    | Dashboard                       | Covered         | FLOW-DASHBOARD-SELLER-VIEW                                                                                                     | Platform dashboard cần chi tiết hơn ở Screen Requirement nếu làm phase đầu. |
| MOD-PRODUCT      | Product Management              | Covered         | FLOW-PRODUCT-CREATE, FLOW-PRODUCT-UPDATE, FLOW-PRODUCT-UPDATE-STATUS, FLOW-PRODUCT-DEACTIVATE                                  | Đủ core product.                                                            |
| MOD-CATEGORY     | Category Management             | Covered         | FLOW-CATEGORY-MANAGE                                                                                                           | Đủ CRUD/deactivate/mapping mức flow.                                        |
| MOD-ORDER        | Order Management                | Covered         | FLOW-ORDER-REVIEW-CONFIRM, FLOW-ORDER-CANCEL, FLOW-ORDER-SHIPMENT                                                              | Cần chốt mapping Lazada status sau.                                         |
| MOD-CUSTOMER     | Customer Management & Care      | Covered         | FLOW-CUSTOMER-VIEW-MANAGE, FLOW-CUSTOMER-CARE-TICKET                                                                           | Đã có view/history/ticket.                                                  |
| MOD-INVENTORY    | Inventory Management            | Covered         | FLOW-INVENTORY-STOCK-IN, FLOW-INVENTORY-ADJUSTMENT-APPROVAL, FLOW-ORDER-REVIEW-CONFIRM, FLOW-ORDER-SHIPMENT, FLOW-ORDER-CANCEL | Inventory liên quan nhiều flow order/shipping.                              |
| MOD-CHANNEL      | Sales Channel Management        | Covered         | FLOW-CHANNEL-CONNECT-LAZADA                                                                                                    | Disconnect Lazada chưa chi tiết, có thể thêm nếu cần ở phase đầu.           |
| MOD-SYNC         | Real-time / Near Real-time Sync | Covered         | FLOW-SYNC-LAZADA-ORDER, FLOW-SYNC-LAZADA-STOCK, FLOW-SYNC-ERROR-RETRY, FLOW-SYNC-MANUAL-RESYNC                                 | Phụ thuộc verify Lazada API.                                                |
| MOD-SHIPPING     | Shipping Core                   | Covered         | FLOW-ORDER-SHIPMENT                                                                                                            | Core-first; provider API để sau.                                            |
| MOD-REPORT       | Reports & Analytics             | Covered         | FLOW-REPORT-VIEW, FLOW-PROFIT-FORMULA-APPROVAL, FLOW-REPORT-EXPORT                                                             | Đủ view/formula/export.                                                     |
| MOD-SUBSCRIPTION | Subscription & Payment          | Covered         | FLOW-SUBSCRIPTION-UPGRADE-PAYMENT, FLOW-SUBSCRIPTION-DOWNGRADE, FLOW-SUBSCRIPTION-PASTDUE-SUSPEND                              | Payment VNPay QR đã có.                                                     |
| MOD-AUDIT        | Audit Log                       | Covered         | FLOW-AUDIT-VIEW + Cross-flow Audit Requirements                                                                                | Đủ view/log requirement.                                                    |
| MOD-NOTIFICATION | Notification                    | Covered         | FLOW-NOTIFICATION-HANDLE                                                                                                       | Đủ mức core notification.                                                   |
| MOD-SETTING      | Settings                        | Covered         | FLOW-SETTING-MANAGE                                                                                                            | Chi tiết field setting để sang Screen/Data.                                 |
| MOD-MARKETING    | Marketing / Promotion           | Deferred        | N/A                                                                                                                            | Đã quyết định Later. Không thiếu flow phase đầu.                            |

## 29.1 Verification Result

Sau khi kiểm tra lại với `01_PROJECT_BRIEF.md`, `02_USER_ROLES.md` và `03_FEATURE_LIST.md`, tài liệu `04_BUSINESS_FLOW.md` hiện đã bao phủ đủ các module phase đầu ở mức business flow.

Các điểm **chưa thể chốt chính xác tuyệt đối** vì phụ thuộc thông tin bên ngoài hoặc quyết định chi tiết sau:

| Item                      | Vì sao chưa chốt được                                         | Tài liệu cần xử lý tiếp                      |
| ------------------------- | ------------------------------------------------------------- | -------------------------------------------- |
| Lazada API support        | Chưa verify API thực tế hỗ trợ dữ liệu/webhook nào.           | Technical Architecture / Integration Spec    |
| Lazada status mapping     | Cần mapping trạng thái thật từ Lazada.                        | Business Flow update / Data Model / API Spec |
| VNPay callback edge cases | Cần thiết kế callback/reconciliation chi tiết.                | Technical Architecture / API Spec            |
| Report export format      | Cần format riêng cho từng loại báo cáo.                       | Screen Requirement / Report Spec             |
| Platform dashboard detail | Đã có module nhưng chưa viết sâu từng chỉ số.                 | Sitemap / Screen Requirement                 |
| Channel disconnect flow   | Có feature nhưng chưa chi tiết vì không chắc cần ở phase đầu. | Có thể bổ sung nếu đưa vào Must-have         |

---

# 30. Open Questions

| Question ID | Question                                                                                                                                                  | Related Area                                                                                                                                                                                                                                              | Impact                 | Status                                       |                                 |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------- | -------------------------------------------- | ------------------------------- |
| Q-FLOW-001  | Lazada API hỗ trợ chính xác những webhook/API nào?                                                                                                        | Chờ tài liệu/API confirm. Không tự giả định khả năng API.                                                                                                                                                                                                 | Sync / Integration     | Ảnh hưởng flow sync real-time/near real-time | Pending External Confirmation   |
| Q-FLOW-002  | Danh sách mapping trạng thái Lazada → ShopHub Normalized Order Status là gì?                                                                              | Chờ tài liệu/API confirm. Tạm giữ normalized status draft trong tài liệu này.                                                                                                                                                                             | Order                  | Ảnh hưởng order flow, UI, report, inventory  | Pending External Confirmation   |
| Q-FLOW-003  | Danh sách mapping Lazada Product Status → ShopHub Product Status là gì?                                                                                   | Chờ tài liệu/API confirm. Tạm giữ product status direction ở mức draft.                                                                                                                                                                                   | Product / Sync         | Ảnh hưởng product flow và sync               | Pending External Confirmation   |
| Q-FLOW-004  | Hủy đơn trên ShopHub có cần gọi API hủy Lazada ngay không, hay chỉ cập nhật nội bộ trước?                                                                 | Trạng thái trên sàn và trên ShopHub phải đồng bộ. Nếu ShopHub thực hiện hủy đơn, hệ thống phải đồng bộ trạng thái hủy lên sàn nếu API cho phép; nếu sync thất bại phải có trạng thái pending/sync error, không được âm thầm lệch.                         | Order / Sync           | Ảnh hưởng cancel flow                        | Answered                        |
| Q-FLOW-005  | Khi đơn Shipping thì thời điểm chính xác giảm Physical Stock là lúc nào: khi bàn giao vận chuyển, khi Lazada báo shipped, hay khi shop xác nhận xuất kho? | Có 2 mốc quan trọng: Order Staff bấm xác nhận đơn thì giảm Available Stock và tăng Reserved Stock; Warehouse Staff bấm giao hàng/xuất kho thì giảm Physical Stock và giảm Reserved Stock, sau đó đồng bộ trạng thái/tồn kho lên sàn.                      | Inventory / Shipping   | Ảnh hưởng tồn kho thực                       | Answered                        |
| Q-FLOW-006  | VNPay QR callback và reconciliation xử lý thế nào khi callback lỗi/chậm?                                                                                  | Phương án đề xuất: payment giữ PendingPayment cho đến khi có callback/IPN hoặc xác minh giao dịch hợp lệ. Nếu callback chậm/lỗi, hệ thống dùng reconciliation/query transaction để kiểm tra lại. Subscription chỉ đổi gói khi payment được xác nhận Paid. | Payment                | Ảnh hưởng subscription flow                  | Answered with Proposed Approach |
| Q-FLOW-007  | Export report có cần chạy background job cho file lớn không?                                                                                              | Câu hỏi này nghĩa là: nếu báo cáo ít dữ liệu thì tạo file ngay; nếu báo cáo lớn, hệ thống nên xử lý nền để tránh màn hình bị treo. Đề xuất: phase đầu export nhỏ xử lý trực tiếp, export lớn chuyển background job.                                       | Reports                | Ảnh hưởng kỹ thuật và UX                     | Explained / Proposed            |
| Q-FLOW-008  | Ticket chăm sóc khách hàng có cần SLA/priority/assignee không?                                                                                            | Câu hỏi này nghĩa là ticket có cần mức ưu tiên, người phụ trách và hạn xử lý không. Đề xuất: phase đầu nên có Priority và Assignee cơ bản; SLA để phase sau nếu chưa cần vận hành CSKH chuyên nghiệp.                                                     | Customer Care          | Ảnh hưởng Customer Support module            | Explained / Proposed            |
| Q-FLOW-009  | Shop Manager được duyệt inventory adjustment đến ngưỡng nào, hay duyệt mọi adjustment?                                                                    | Đề xuất theo mức tiền chênh lệch từng sản phẩm. Dưới 10.000.000 VND: Shop Manager hoặc Shop Owner có thể duyệt. Từ 10.000.000 VND trở lên: ưu tiên Shop Owner duyệt, Shop Manager chỉ được duyệt nếu Permission Matrix cấu hình rõ.                       | Inventory / Permission | Ảnh hưởng Permission Matrix                  | Answered as Draft Rule          |
| Q-FLOW-010  | Manual resync thế nào được coi là ảnh hưởng dữ liệu quan trọng?                                                                                           | Các dữ liệu quan trọng gồm: trạng thái đơn hàng, tiền/thanh toán/phí/lợi nhuận, trạng thái sản phẩm, tồn kho, dữ liệu sync có khả năng ghi đè, dữ liệu khách hàng nhạy cảm. Manual resync tác động các nhóm này cần xác nhận Shop Owner.                  | Sync / Permission      | Cần rule phân loại impact                    | Answered                        |

---

# 31. Decisions Log

| Decision ID  | Decision                                                                                               | Reason                                                                     | Related Docs                                          | Status         |
| ------------ | ------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------- | ----------------------------------------------------- | -------------- |
| DEC-FLOW-001 | Khách mua hàng cuối không đăng nhập ShopHub.                                                           | ShopHub là dashboard nội bộ cho shop/platform.                             | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md                 | Accepted       |
| DEC-FLOW-002 | Giai đoạn đầu 1 user thuộc 1 shop.                                                                     | Đơn giản hóa tenant boundary.                                              | 02_USER_ROLES.md                                      | Accepted       |
| DEC-FLOW-003 | Một shop có thể kết nối nhiều nền tảng/kênh bán.                                                       | ShopHub phục vụ quản lý multi-channel.                                     | 01_PROJECT_BRIEF.md                                   | Accepted       |
| DEC-FLOW-004 | Kênh đầu tiên ưu tiên Lazada.                                                                          | Giảm scope tích hợp.                                                       | 01_PROJECT_BRIEF.md, 03_FEATURE_LIST.md               | Accepted       |
| DEC-FLOW-005 | Inventory dùng Physical / Available / Reserved Stock.                                                  | Tránh sai lệch tồn kho.                                                    | 01_PROJECT_BRIEF.md, 03_FEATURE_LIST.md               | Accepted       |
| DEC-FLOW-006 | Mỗi shop có 1 kho vật lý ở phase đầu.                                                                  | Giảm độ phức tạp inventory.                                                | 01_PROJECT_BRIEF.md                                   | Accepted       |
| DEC-FLOW-007 | Warehouse adjustment cần lý do, bằng chứng và approval.                                                | Điều chỉnh tồn kho là thao tác nhạy cảm.                                   | 02_USER_ROLES.md, 03_FEATURE_LIST.md                  | Accepted       |
| DEC-FLOW-008 | Accountant chỉnh công thức lợi nhuận cần approval.                                                     | Ảnh hưởng báo cáo tài chính.                                               | 02_USER_ROLES.md, 03_FEATURE_LIST.md                  | Accepted       |
| DEC-FLOW-009 | Order Staff được hủy đơn nếu rule cho phép nhưng bắt buộc confirm.                                     | Giảm rủi ro hủy nhầm.                                                      | 03_FEATURE_LIST.md                                    | Accepted       |
| DEC-FLOW-010 | Subscription payment dùng VNPay QR.                                                                    | Đã chốt payment provider cho subscription.                                 | 03_FEATURE_LIST.md                                    | Accepted       |
| DEC-FLOW-011 | Billing Admin không được đổi gói thủ công.                                                             | Mọi thay đổi gói phải qua payment/subscription flow.                       | 02_USER_ROLES.md, 03_FEATURE_LIST.md                  | Accepted       |
| DEC-FLOW-012 | Manual resync cần Shop Owner xác nhận khi ảnh hưởng dữ liệu quan trọng.                                | Cân bằng giữa an toàn dữ liệu và vận hành.                                 | 03_FEATURE_LIST.md                                    | Accepted       |
| DEC-FLOW-013 | Customer Support được tạo issue/ticket riêng.                                                          | Cần theo dõi chăm sóc khách hàng tách khỏi ghi chú đơn giản.               | 03_FEATURE_LIST.md                                    | Accepted       |
| DEC-FLOW-014 | Product status theo trạng thái sàn và có thể custom theo shop.                                         | Phù hợp vận hành Lazada và shop.                                           | 03_FEATURE_LIST.md                                    | Accepted       |
| DEC-FLOW-015 | Trạng thái trên sàn và trạng thái trên ShopHub phải được đồng bộ.                                      | Tránh lệch trạng thái đơn giữa Lazada và ShopHub.                          | 08_API_SPECIFICATION.md, 11_TECHNICAL_ARCHITECTURE.md | Accepted       |
| DEC-FLOW-016 | Physical Stock giảm khi Warehouse Staff xác nhận giao/xuất kho.                                        | Phản ánh đúng thời điểm hàng thực sự rời kho.                              | 07_DATA_MODEL.md, 09_PERMISSION_MATRIX.md             | Accepted       |
| DEC-FLOW-017 | Available Stock giảm và Reserved Stock tăng khi Order Staff xác nhận đơn.                              | Giữ hàng cho đơn nhưng chưa trừ kho thực.                                  | 07_DATA_MODEL.md, 09_PERMISSION_MATRIX.md             | Accepted       |
| DEC-FLOW-018 | VNPay subscription payment cần callback/IPN và reconciliation.                                         | Tránh đổi gói sai khi callback chậm/lỗi.                                   | 08_API_SPECIFICATION.md, 11_TECHNICAL_ARCHITECTURE.md | Accepted       |
| DEC-FLOW-019 | Export report nhỏ xử lý trực tiếp, export lớn nên chạy background job.                                 | Tránh treo UI khi dữ liệu lớn.                                             | 08_API_SPECIFICATION.md, 11_TECHNICAL_ARCHITECTURE.md | Draft Accepted |
| DEC-FLOW-020 | Ticket CSKH phase đầu nên có Priority và Assignee cơ bản; SLA để phase sau.                            | Đủ dùng để vận hành chăm sóc khách hàng mà chưa quá phức tạp.              | 05_SITEMAP_AND_SCREEN_LIST.md, 07_DATA_MODEL.md       | Draft Accepted |
| DEC-FLOW-021 | Inventory adjustment approval dùng ngưỡng giá trị chênh lệch khoảng 10.000.000 VND theo từng sản phẩm. | Giúp phân loại điều chỉnh nhỏ/lớn và quyền duyệt.                          | 09_PERMISSION_MATRIX.md                               | Draft Accepted |
| DEC-FLOW-022 | Manual resync ảnh hưởng dữ liệu quan trọng cần xác nhận Shop Owner.                                    | Bảo vệ order status, money, product status, inventory và dữ liệu nhạy cảm. | 09_PERMISSION_MATRIX.md, 11_TECHNICAL_ARCHITECTURE.md | Accepted       |

---

# 32. Change Log

| Version | Date | Change                                                                                                                                                                                                                                                                                                                 | Author |
| ------- | ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| 0.1     | TBD  | Initial Business Flow based on Project Brief v0.4, User Roles v0.2 and Feature List v0.2                                                                                                                                                                                                                               | TBD    |
| 0.2     | TBD  | Self-verified flow coverage against Feature List; added missing coverage flows for auth, platform invite, shop suspend/restore, dashboard, category, product update/deactivate, customer view, report view, subscription downgrade, audit view, notification handling and settings; added verification coverage matrix | TBD    |
| 0.3     | TBD  | Updated Q-FLOW-001 to Q-FLOW-010 answers; added VNPay callback/reconciliation direction, inventory adjustment approval threshold, direct/background report export proposal, ticket priority/assignee proposal, manual resync important data classification                                                             | TBD    |
