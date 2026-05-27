# SCR-PRODUCT-DETAIL - Product Detail

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-PRODUCT-DETAIL |
| Screen Name | Product Detail |
| Route | `/app/products/:productId` |
| Screen Type | Seller |
| Module ID | MOD-PRODUCT |
| Related Feature IDs | FEAT-PRODUCT-LIST, FEAT-PRODUCT-DETAIL, FEAT-PRODUCT-CREATE, FEAT-PRODUCT-UPDATE, FEAT-PRODUCT-SKU, FEAT-PRODUCT-PRICE, FEAT-PRODUCT-COST, FEAT-PRODUCT-STATUS, FEAT-PRODUCT-LAZADA-SYNC-STATUS, FEAT-PRODUCT-DELETE-SAFE |
| Related Flow IDs | FLOW-PRODUCT-UPDATE, FLOW-PRODUCT-UPDATE-STATUS |
| Allowed Roles | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-PRODUCT-STAFF, ROLE-ACCOUNTANT limited, ROLE-SHOP-VIEWER view-only |
| Priority | Must-have |
| Status | Draft |
| Depends On | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Xem chi tiết sản phẩm, trạng thái, tồn kho và trạng thái sync Lazada.

---

## 2. User Goal

User muốn xem hoặc xử lý nghiệp vụ liên quan đến màn hình này một cách rõ ràng, đúng quyền và đúng dữ liệu.

---

## 3. Entry Points

| Entry Point | From Screen | Condition |
| --- | --- | --- |
| Main navigation | Sidebar / Menu | User có quyền truy cập. |
| Related detail/action | Màn hình liên quan | User đi theo flow nghiệp vụ. |

---

## 4. Exit Points / Navigation

| Action | Target Screen | Condition |
| --- | --- | --- |
| Back / Breadcrumb | Previous screen | User quay lại màn trước. |
| Open related detail | Related screen | User có quyền xem dữ liệu liên quan. |

---

## 5. Allowed Roles & Access Rules

| Role ID | Access Level | Notes |
| --- | --- | --- |
| ROLE-SHOP-OWNER | View / Limited by permission | Theo permission matrix. |
| ROLE-SHOP-MANAGER | View / Limited by permission | Theo permission matrix. |
| ROLE-PRODUCT-STAFF | View / Limited by permission | Theo permission matrix. |
| ROLE-ACCOUNTANT limited | View / Limited by permission | Theo permission matrix. |
| ROLE-SHOP-VIEWER view-only | View / Limited by permission | Theo permission matrix. |

---

## 6. Main UI Sections

| Section ID | Section Name | Purpose | Visible To |
| --- | --- | --- | --- |
| SEC-HEADER | Header / Title | Hiển thị tên màn hình, breadcrumb và action chính. | Allowed roles |
| SEC-CONTENT | Main Content | Hiển thị dữ liệu chính của màn hình. | Allowed roles |
| SEC-STATE | State Area | Hiển thị loading, empty, error, permission denied. | Allowed roles |

---

## 7. Data Displayed

| Data Field | Description | Source / Related Entity | Required | Notes |
| --- | --- | --- | --- | --- |
| Product name | Tên sản phẩm. | Product | Yes |  |
| SKU | Mã SKU trong phạm vi shop. | Product/SKU | Yes | Không trùng trong shop. |
| Price / Cost | Giá bán và giá vốn. | Product | Price yes / Cost should | Cost phục vụ profit report. |
| Product Status | Marketplace status, ShopHub status, custom status nếu có. | Product/Marketplace | Yes | Mapping Lazada chờ confirm. |
| Sync Status | Trạng thái đồng bộ Lazada. | Sync Log | Should | SyncError/SyncPending nếu có. |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-PRODUCT-CREATE | Create Product | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-PRODUCT-STAFF | Submit form | Product được tạo, audit log ghi nhận. | FLOW-PRODUCT-CREATE |
| ACT-PRODUCT-UPDATE | Update Product | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-PRODUCT-STAFF | Save changes | Product được cập nhật, sync pending nếu cần. | FLOW-PRODUCT-UPDATE |
| ACT-PRODUCT-DEACTIVATE | Deactivate / Archive Product | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER | Click deactivate/archive | Product chuyển Inactive/Archived, không xóa cứng nếu có đơn. | FLOW-PRODUCT-DEACTIVATE |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-PRODUCT-DETAIL-001 | Màn hình chỉ hiển thị dữ liệu theo đúng role và scope. | FLOW-PRODUCT-UPDATE, FLOW-PRODUCT-UPDATE-STATUS |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| Product Name | Text | Yes | Không rỗng | Áo thun basic |  |
| SKU | Text | Yes | Unique in shop | SKU-001 | Không trùng trong shop. |
| Category | Select | Should | Category active | Thời trang |  |
| Selling Price | Number | Yes | >= 0 | 199000 |  |
| Product Cost | Number | Should | >= 0 | 120000 | Cần cho profit report. |
| Status | Select | Yes | Valid status | Active | Theo ShopHub status. |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-PRODUCT-DETAIL-001 | User phải có quyền truy cập màn hình. | Bạn không có quyền truy cập màn hình này. |
| VAL-PRODUCT-DETAIL-002 | Dữ liệu phải thuộc đúng shop/tenant hoặc platform scope. | Không thể truy cập dữ liệu ngoài phạm vi được cấp quyền. |

---

## 12. State Handling

### 12.1 Loading State

Hiển thị skeleton/loading phù hợp với bảng, form hoặc detail. Disable action chính khi đang xử lý.

### 12.2 Empty State

Hiển thị thông báo rõ ràng khi chưa có dữ liệu, kèm CTA nếu user có quyền tạo dữ liệu.

### 12.3 Error State

Hiển thị lỗi dễ hiểu, có nút thử lại nếu lỗi có thể retry.

### 12.4 Permission Denied State

Hiển thị thông báo user không có quyền truy cập hoặc không có quyền thực hiện action.

### 12.5 Success State

Hiển thị toast hoặc inline message khi thao tác thành công.

---

## 13. Confirmation / Approval

| Action | Confirm Required | Approval Required | Approver | Notes |
| --- | --- | --- | --- | --- |
| Deactivate / Archive Product | Yes | No | N/A | Không xóa cứng nếu đã phát sinh đơn. |

---

## 14. Notification / Toast

| Event | Message Type | Suggested Message |
| --- | --- | --- |
| Load failed | Error | Không thể tải dữ liệu. Vui lòng thử lại. |
| Action success | Success | Thao tác thành công. |

---

## 15. Audit Log Requirement

| Action | Audit Required | Data to Log |
| --- | --- | --- |
| Create product | Yes | actor, productId, created fields, time |
| Update product | Yes | actor, productId, changed fields, before/after, time |
| Deactivate/archive | Yes | actor, productId, status before/after, time |

---

## 16. Responsive Requirement

| Device | Requirement |
|---|---|
| Desktop | Ưu tiên layout đầy đủ: table/filter/detail/action. |
| Tablet | Giữ đầy đủ chức năng chính, có thể thu gọn filter/action. |
| Mobile | Ưu tiên đọc thông tin và action quan trọng; table có thể chuyển thành card/list. |

---

## 17. Accessibility Notes

- Tất cả button/action phải có label rõ ràng.
- Form field phải có label và error message.
- Trạng thái loading/error/success cần có text, không chỉ màu sắc.
- Có focus state cho keyboard navigation.
- Không dùng màu làm cách duy nhất để truyền đạt trạng thái.

---

## 18. Related Screens

| Screen ID | Relationship |
| --- | --- |
| SCR-CATEGORY-LIST | Chọn/quản lý category. |
| SCR-INVENTORY-OVERVIEW | Xem tồn kho sản phẩm. |
| SCR-SYNC-LOG | Xem lịch sử sync sản phẩm. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-PRODUCT-DETAIL-001 | Danh sách field product Lazada hỗ trợ sync chính xác là gì? | Ảnh hưởng form và sync status. | Pending API Verification |

---

## 20. Design Notes

Màn product cần ưu tiên thao tác nhanh, trạng thái rõ ràng, cảnh báo sync lỗi và tránh nhầm giữa trạng thái sàn với trạng thái nội bộ.

---

## 21. Dev Notes

- Cần enforce permission ở backend, không chỉ ẩn nút trên frontend.
- Cần truyền tenant/shop scope trong mọi request seller-side.
- Các API lỗi phải trả message đủ để UI hiển thị trạng thái đúng.
- Các action nhạy cảm cần confirm/audit theo flow.

---

## 22. Verification Checklist

| Check Item | Result | Notes |
|---|---|---|
| Screen có Screen ID chưa? | Pass | SCR-PRODUCT-DETAIL |
| Có route chưa? | Pass | `/app/products/:productId` |
| Có map feature chưa? | Pass | FEAT-PRODUCT-LIST, FEAT-PRODUCT-DETAIL, FEAT-PRODUCT-CREATE, FEAT-PRODUCT-UPDATE, FEAT-PRODUCT-SKU, FEAT-PRODUCT-PRICE, FEAT-PRODUCT-COST, FEAT-PRODUCT-STATUS, FEAT-PRODUCT-LAZADA-SYNC-STATUS, FEAT-PRODUCT-DELETE-SAFE |
| Có map flow chưa? | Pass | FLOW-PRODUCT-UPDATE, FLOW-PRODUCT-UPDATE-STATUS |
| Có allowed roles chưa? | Pass | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-PRODUCT-STAFF, ROLE-ACCOUNTANT limited, ROLE-SHOP-VIEWER view-only |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation hoặc ghi không áp dụng. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi open questions. |
