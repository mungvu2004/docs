# 06_SCREEN_REQUIREMENTS/

## 00_SCREEN_REQUIREMENTS_README.md

## Metadata

| Field        | Value                                                                                                         |
| ------------ | ------------------------------------------------------------------------------------------------------------- |
| Document ID  | DOC-06-SCREEN-REQUIREMENTS                                                                                    |
| Project Name | Multi-channel E-commerce Hub / ShopHub                                                                        |
| Version      | 0.1                                                                                                           |
| Status       | Draft                                                                                                         |
| Owner        | TBD                                                                                                           |
| Last Updated | TBD                                                                                                           |
| Depends On   | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By      | UI/UX Design, Wireframe, Frontend Implementation, Backend API Design, Permission Matrix, QA Test Cases        |

---

# 1. Purpose

Folder `06_SCREEN_REQUIREMENTS/` dùng để mô tả yêu cầu chi tiết cho từng màn hình của ShopHub.

Mỗi màn hình trong `05_SITEMAP_AND_SCREEN_LIST.md` cần được chuyển thành một tài liệu riêng, theo format thống nhất, để:

* Designer hiểu màn hình cần phục vụ mục tiêu gì.
* Developer hiểu màn hình cần dữ liệu, action, state và permission nào.
* QA có cơ sở viết test case.
* Product/Business kiểm soát việc không thiếu hoặc lệch scope.
* AI/code agent không tự bịa thêm màn hình hoặc nghiệp vụ ngoài tài liệu nguồn.

---

# 2. Screen Requirement Rules

| Rule ID         | Rule                                                                                                               |
| --------------- | ------------------------------------------------------------------------------------------------------------------ |
| RULE-SCREEN-001 | Mỗi screen phải có một file riêng.                                                                                 |
| RULE-SCREEN-002 | Mỗi screen phải có Screen ID cố định theo `05_SITEMAP_AND_SCREEN_LIST.md`.                                         |
| RULE-SCREEN-003 | Không tạo screen ngoài sitemap nếu không có quyết định bổ sung rõ ràng.                                            |
| RULE-SCREEN-004 | Mỗi screen phải map với Module ID, Feature ID và Flow ID.                                                          |
| RULE-SCREEN-005 | Mỗi screen phải ghi rõ Allowed Roles và access level.                                                              |
| RULE-SCREEN-006 | Screen liên quan dữ liệu shop phải tôn trọng tenant boundary.                                                      |
| RULE-SCREEN-007 | Screen có action nhạy cảm phải có confirmation, approval hoặc audit log nếu flow yêu cầu.                          |
| RULE-SCREEN-008 | Không copy toàn bộ business flow dài vào screen; chỉ reference Flow ID và rule quan trọng.                         |
| RULE-SCREEN-009 | Nếu thiếu thông tin, ghi vào Open Questions, không tự suy diễn.                                                    |
| RULE-SCREEN-010 | Lazada API chưa chốt mapping chi tiết, nên screen Lazada chỉ mô tả dữ liệu ở mức connection/status/sync/log/error. |
| RULE-SCREEN-011 | Payment logic do backend/API và VNPay xử lý; screen chỉ hiển thị trạng thái đã được hệ thống xác minh.             |
| RULE-SCREEN-012 | Khách mua hàng cuối không có screen đăng nhập/customer portal trong ShopHub.                                       |
| RULE-SCREEN-013 | Marketing/Promotion, RAG Assistant, SLA, multi-warehouse, multi-shop user để `99_DEFERRED/`.                       |

---

# 3. Folder Structure

```txt
06_SCREEN_REQUIREMENTS/
├── 00_SCREEN_REQUIREMENTS_README.md
├── SCREEN_COVERAGE_MATRIX.md
├── 01_AUTH/
│   ├── SCR-AUTH-LOGIN.md
│   ├── SCR-AUTH-FORGOT-PASSWORD.md
│   ├── SCR-AUTH-RESET-PASSWORD.md
│   └── SCR-AUTH-ACCEPT-INVITATION.md
├── 02_SELLER/
│   ├── DASHBOARD/
│   ├── PRODUCTS/
│   ├── CATEGORIES/
│   ├── ORDERS/
│   ├── CUSTOMERS/
│   ├── INVENTORY/
│   ├── CHANNELS_SYNC/
│   ├── SHIPPING/
│   ├── REPORTS/
│   ├── SUBSCRIPTION_BILLING/
│   ├── USERS_ROLES/
│   ├── SETTINGS/
│   ├── AUDIT/
│   └── NOTIFICATIONS/
├── 03_PLATFORM/
│   ├── DASHBOARD/
│   ├── SHOP_MANAGEMENT/
│   ├── PLATFORM_USERS/
│   ├── PLANS_SUBSCRIPTIONS/
│   ├── INTEGRATION_MONITORING/
│   ├── SUPPORT/
│   ├── AUDIT/
│   └── SETTINGS/
├── 04_SHARED/
└── 99_DEFERRED/
    └── DEFERRED_SCREENS.md
```

---

# 4. Screen File Naming Convention

| Screen Type      | Naming Example                         |
| ---------------- | -------------------------------------- |
| Auth             | `SCR-AUTH-LOGIN.md`                    |
| Seller Dashboard | `SCR-SELLER-DASHBOARD.md`              |
| Product          | `SCR-PRODUCT-LIST.md`                  |
| Order            | `SCR-ORDER-DETAIL.md`                  |
| Inventory        | `SCR-INVENTORY-ADJUSTMENT-APPROVAL.md` |
| Platform         | `SCR-PLATFORM-SHOP-LIST.md`            |
| Deferred         | `DEFERRED_SCREENS.md`                  |

---

# 5. Screen Requirement Template

```md
# [SCREEN_ID] - [SCREEN_NAME]

## Metadata

| Field | Value |
|---|---|
| Screen ID | |
| Screen Name | |
| Route | |
| Screen Type | Auth / Seller / Platform / Shared |
| Module ID | |
| Related Feature IDs | |
| Related Flow IDs | |
| Allowed Roles | |
| Priority | Must-have / Should-have / Could-have / Later |
| Status | Draft / Ready for Design / Ready for Dev |
| Depends On | |
| Used By | |

---

## 1. Screen Purpose

## 2. User Goal

## 3. Entry Points

| Entry Point | From Screen | Condition |
|---|---|---|

## 4. Exit Points / Navigation

| Action | Target Screen | Condition |
|---|---|---|

## 5. Allowed Roles & Access Rules

| Role ID | Access Level | Notes |
|---|---|---|

Access Level:
- Full
- Manage
- View
- Limited View
- No Access

## 6. Main UI Sections

| Section ID | Section Name | Purpose | Visible To |
|---|---|---|---|

## 7. Data Displayed

| Data Field | Description | Source / Related Entity | Required | Notes |
|---|---|---|---|---|

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
|---|---|---|---|---|---|

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
|---|---|---|

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
|---|---|---|---|---|---|

## 11. Validation Rules

| Validation ID | Rule | Error Message |
|---|---|---|

## 12. State Handling

### 12.1 Loading State

### 12.2 Empty State

### 12.3 Error State

### 12.4 Permission Denied State

### 12.5 Success State

## 13. Confirmation / Approval

| Action | Confirm Required | Approval Required | Approver | Notes |
|---|---|---|---|---|

## 14. Notification / Toast

| Event | Message Type | Suggested Message |
|---|---|---|

## 15. Audit Log Requirement

| Action | Audit Required | Data to Log |
|---|---|---|

## 16. Responsive Requirement

## 17. Accessibility Notes

## 18. Related Screens

| Screen ID | Relationship |
|---|---|

## 19. Open Questions

| Question ID | Question | Impact | Status |
|---|---|---|

## 20. Design Notes

## 21. Dev Notes

## 22. Verification Checklist

| Check Item | Result | Notes |
|---|---|---|
| Screen có Screen ID chưa? | Pass/Fail | |
| Có route chưa? | Pass/Fail | |
| Có map feature chưa? | Pass/Fail | |
| Có map flow chưa? | Pass/Fail | |
| Có allowed roles chưa? | Pass/Fail | |
| Có action chính chưa? | Pass/Fail | |
| Có state handling chưa? | Pass/Fail | |
| Có validation nếu có form chưa? | Pass/Fail | |
| Có open questions nếu thiếu thông tin chưa? | Pass/Fail | |
```

---

# 6. Screen Index from `05_SITEMAP_AND_SCREEN_LIST.md`

## 6.1 Auth Screens

| File                                    | Screen ID                  | Priority    | Status |
| --------------------------------------- | -------------------------- | ----------- | ------ |
| `01_AUTH/SCR-AUTH-LOGIN.md`             | SCR-AUTH-LOGIN             | Must-have   | Draft  |
| `01_AUTH/SCR-AUTH-FORGOT-PASSWORD.md`   | SCR-AUTH-FORGOT-PASSWORD   | Should-have | Draft  |
| `01_AUTH/SCR-AUTH-RESET-PASSWORD.md`    | SCR-AUTH-RESET-PASSWORD    | Should-have | Draft  |
| `01_AUTH/SCR-AUTH-ACCEPT-INVITATION.md` | SCR-AUTH-ACCEPT-INVITATION | Must-have   | Draft  |

## 6.2 Seller-side Screens

| Group                            | Screens                                                                                                                                                                            |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Dashboard                        | SCR-SELLER-DASHBOARD                                                                                                                                                               |
| Products                         | SCR-PRODUCT-LIST, SCR-PRODUCT-DETAIL, SCR-PRODUCT-CREATE, SCR-PRODUCT-EDIT                                                                                                         |
| Categories                       | SCR-CATEGORY-LIST, SCR-CATEGORY-FORM                                                                                                                                               |
| Orders                           | SCR-ORDER-LIST, SCR-ORDER-DETAIL                                                                                                                                                   |
| Customers                        | SCR-CUSTOMER-LIST, SCR-CUSTOMER-DETAIL, SCR-CUSTOMER-TICKET-LIST, SCR-CUSTOMER-TICKET-DETAIL                                                                                       |
| Inventory                        | SCR-INVENTORY-OVERVIEW, SCR-INVENTORY-STOCK-IN, SCR-INVENTORY-ADJUSTMENT-REQUEST, SCR-INVENTORY-ADJUSTMENT-APPROVAL, SCR-INVENTORY-LOW-STOCK                                       |
| Channels / Sync                  | SCR-CHANNEL-LIST, SCR-LAZADA-CONNECTION, SCR-LAZADA-CHANNEL-DETAIL, SCR-SYNC-DASHBOARD, SCR-SYNC-LOG, SCR-SYNC-ERROR-DETAIL, SCR-MANUAL-RESYNC                                     |
| Shipping                         | SCR-SHIPMENT-DETAIL                                                                                                                                                                |
| Reports                          | SCR-REPORTS-OVERVIEW, SCR-REPORT-REVENUE, SCR-REPORT-PROFIT, SCR-REPORT-INVENTORY, SCR-REPORT-CHANNEL, SCR-REPORT-EXPORT, SCR-PROFIT-FORMULA-SETTINGS, SCR-PROFIT-FORMULA-APPROVAL |
| Subscription / Billing           | SCR-SUBSCRIPTION-CURRENT-PLAN, SCR-SUBSCRIPTION-UPGRADE, SCR-SUBSCRIPTION-DOWNGRADE, SCR-PAYMENT-VNPAY-QR, SCR-PAYMENT-RESULT, SCR-INVOICE-HISTORY                                 |
| Users / Roles                    | SCR-SHOP-USER-LIST, SCR-INVITE-SHOP-USER, SCR-SHOP-USER-DETAIL                                                                                                                     |
| Settings / Audit / Notifications | SCR-SHOP-SETTINGS, SCR-SELLER-AUDIT-LOG, SCR-SELLER-NOTIFICATIONS                                                                                                                  |

## 6.3 Platform-side Screens

| Group                  | Screens                                                                                                           |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Dashboard              | SCR-PLATFORM-DASHBOARD                                                                                            |
| Shop Management        | SCR-PLATFORM-SHOP-LIST, SCR-PLATFORM-SHOP-DETAIL, SCR-PLATFORM-SHOP-SUSPEND-RESTORE                               |
| Platform Users         | SCR-PLATFORM-USER-LIST, SCR-PLATFORM-INVITE-USER                                                                  |
| Plans / Subscriptions  | SCR-PLATFORM-PLAN-MANAGEMENT, SCR-PLATFORM-SUBSCRIPTION-MANAGEMENT, SCR-PLATFORM-PAYMENT-INVOICE-RECORDS          |
| Integration Monitoring | SCR-PLATFORM-LAZADA-INTEGRATION-MONITORING, SCR-PLATFORM-SYNC-ERROR-MONITORING, SCR-PLATFORM-MANUAL-RESYNC-REVIEW |
| Support                | SCR-PLATFORM-SUPPORT-SHOP-LOOKUP                                                                                  |
| Audit / Settings       | SCR-PLATFORM-AUDIT-LOG, SCR-PLATFORM-SETTINGS                                                                     |
