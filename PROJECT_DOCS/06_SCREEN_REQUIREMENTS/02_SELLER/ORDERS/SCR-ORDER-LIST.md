# SCR-ORDER-LIST - Order List

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-ORDER-LIST |
| Screen Name | Order List |
| Route | `/app/orders` |
| Screen Type | Seller |
| Module ID | MOD-ORDER |
| Related Feature IDs | FEAT-ORDER-LIST, FEAT-ORDER-DETAIL, FEAT-ORDER-FILTER, FEAT-ORDER-MARKETPLACE-STATUS, FEAT-ORDER-NORMALIZED-STATUS, FEAT-ORDER-UPDATE-STATUS, FEAT-ORDER-CANCEL, FEAT-ORDER-NOTES, FEAT-ORDER-AUDIT |
| Related Flow IDs | FLOW-ORDER-REVIEW-CONFIRM, FLOW-ORDER-CANCEL, FLOW-ORDER-SHIPMENT, FLOW-SYNC-LAZADA-ORDER |
| Allowed Roles | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-ORDER-STAFF, ROLE-CUSTOMER-SUPPORT, ROLE-ACCOUNTANT limited |
| Priority | Must-have |
| Status | Draft |
| Depends On | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Xem, tìm kiếm, lọc danh sách đơn hàng từ Lazada và các kênh khác sau này.

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
| ROLE-ORDER-STAFF | View / Limited by permission | Theo permission matrix. |
| ROLE-CUSTOMER-SUPPORT | View / Limited by permission | Theo permission matrix. |
| ROLE-ACCOUNTANT limited | View / Limited by permission | Theo permission matrix. |

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
| Order code | Mã đơn nội bộ và mã đơn sàn. | Order | Yes |  |
| Marketplace Status | Trạng thái gốc từ Lazada/sàn. | Marketplace Order | Yes | Chưa mapping thì hiển thị gốc. |
| ShopHub Status | Trạng thái chuẩn hóa nội bộ. | Order | Yes | Pending/Confirmed/Processing/... |
| Customer info | Thông tin khách hàng theo quyền. | Customer | Yes | Có thể mask nếu role hạn chế. |
| Items / Stock impact | Sản phẩm, số lượng, ảnh hưởng tồn kho. | Order Item, Inventory | Yes | Confirm giảm Available, tăng Reserved. |
| Sync status | Trạng thái đồng bộ với sàn. | Sync Log | Should | Cảnh báo nếu lệch. |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-ORDER-CONFIRM | Confirm Order | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-ORDER-STAFF | Click confirm | Available giảm, Reserved tăng, order confirmed. | FLOW-ORDER-REVIEW-CONFIRM |
| ACT-ORDER-CANCEL | Cancel Order | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-ORDER-STAFF | Click cancel + confirm | Order cancelled nếu rule cho phép, tồn kho hoàn lại nếu chưa xuất. | FLOW-ORDER-CANCEL |
| ACT-ORDER-SHIP | Ship / Stock Out | ROLE-WAREHOUSE-STAFF, ROLE-ORDER-STAFF, ROLE-SHOP-MANAGER | Click ship/stock out | Physical và Reserved giảm khi giao/xuất kho. | FLOW-ORDER-SHIPMENT |
| ACT-ORDER-NOTE | Add Order Note | ROLE-ORDER-STAFF, ROLE-CUSTOMER-SUPPORT, ROLE-SHOP-MANAGER | Add note | Ghi chú đơn được lưu. | FLOW-ORDER-REVIEW-CONFIRM |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-ORDER-LIST-001 | Màn hình chỉ hiển thị dữ liệu theo đúng role và scope. | FLOW-ORDER-REVIEW-CONFIRM, FLOW-ORDER-CANCEL, FLOW-ORDER-SHIPMENT, FLOW-SYNC-LAZADA-ORDER |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| Không áp dụng | - | - | - | - | Màn này không có form chính hoặc form sẽ được chi tiết ở bước UI/API. |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-ORDER-LIST-001 | User phải có quyền truy cập màn hình. | Bạn không có quyền truy cập màn hình này. |
| VAL-ORDER-LIST-002 | Dữ liệu phải thuộc đúng shop/tenant hoặc platform scope. | Không thể truy cập dữ liệu ngoài phạm vi được cấp quyền. |

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
| Cancel Order | Yes | No | N/A | Bắt buộc confirm, có lý do nếu rule yêu cầu. |
| Ship / Stock Out | Yes | No | N/A | Ảnh hưởng Physical Stock. |

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
| Confirm order | Yes | actor, orderId, status before/after, stock impact |
| Cancel order | Yes | actor, orderId, reason, status before/after, stock impact |
| Ship / Stock Out | Yes | actor, orderId, shipmentId, stock impact |

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
| SCR-CUSTOMER-DETAIL | Xem khách hàng của đơn. |
| SCR-SHIPMENT-DETAIL | Xem vận chuyển/xuất kho. |
| SCR-SYNC-ERROR-DETAIL | Xem lỗi sync liên quan đơn. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-ORDER-LIST-001 | Mapping trạng thái Lazada cụ thể sang ShopHub Status là gì? | Ảnh hưởng filter/status/action. | Pending Mapping |

---

## 20. Design Notes

Order screen phải hiển thị rõ Marketplace Status và ShopHub Status để tránh lệch giữa sàn và hệ thống.

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
| Screen có Screen ID chưa? | Pass | SCR-ORDER-LIST |
| Có route chưa? | Pass | `/app/orders` |
| Có map feature chưa? | Pass | FEAT-ORDER-LIST, FEAT-ORDER-DETAIL, FEAT-ORDER-FILTER, FEAT-ORDER-MARKETPLACE-STATUS, FEAT-ORDER-NORMALIZED-STATUS, FEAT-ORDER-UPDATE-STATUS, FEAT-ORDER-CANCEL, FEAT-ORDER-NOTES, FEAT-ORDER-AUDIT |
| Có map flow chưa? | Pass | FLOW-ORDER-REVIEW-CONFIRM, FLOW-ORDER-CANCEL, FLOW-ORDER-SHIPMENT, FLOW-SYNC-LAZADA-ORDER |
| Có allowed roles chưa? | Pass | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-ORDER-STAFF, ROLE-CUSTOMER-SUPPORT, ROLE-ACCOUNTANT limited |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation hoặc ghi không áp dụng. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi open questions. |
