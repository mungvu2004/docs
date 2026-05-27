# SCR-SELLER-DASHBOARD - Seller Dashboard

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-SELLER-DASHBOARD |
| Screen Name | Seller Dashboard |
| Route | `/app/dashboard` |
| Screen Type | Seller |
| Module ID | MOD-DASHBOARD |
| Related Feature IDs | FEAT-DASHBOARD-SELLER-OVERVIEW, FEAT-DASHBOARD-ORDER-SUMMARY, FEAT-DASHBOARD-INVENTORY-ALERT, FEAT-DASHBOARD-SYNC-ALERT, FEAT-DASHBOARD-PROFIT-SUMMARY |
| Related Flow IDs | FLOW-DASHBOARD-SELLER-VIEW |
| Allowed Roles | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-ACCOUNTANT, ROLE-ORDER-STAFF, ROLE-WAREHOUSE-STAFF |
| Priority | Must-have |
| Status | Draft |
| Depends On | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Hiển thị tổng quan vận hành và kinh doanh của shop theo đúng quyền của user.

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
| ROLE-ACCOUNTANT | View / Limited by permission | Theo permission matrix. |
| ROLE-ORDER-STAFF | View / Limited by permission | Theo permission matrix. |
| ROLE-WAREHOUSE-STAFF | View / Limited by permission | Theo permission matrix. |

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
| Order summary | Đơn mới, đang xử lý, hủy, hoàn thành. | Orders | Yes | Order Staff có thể xem phần đơn hàng. |
| Inventory alerts | Hàng sắp hết, tồn kho bất thường. | Inventory | Yes | Warehouse Staff xem chi tiết liên quan. |
| Sync alerts | Lỗi đồng bộ Lazada cần xử lý. | Sync Log | Yes | Không tự bịa field API Lazada. |
| Revenue / Profit summary | Tóm tắt doanh thu/lợi nhuận nếu có quyền. | Reports | No | Ẩn với role không có quyền tài chính. |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-DASH-VIEW-ORDER | Open Order Summary | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-ORDER-STAFF | Click order card | Đi đến Order List với filter tương ứng. | FLOW-ORDER-REVIEW-CONFIRM |
| ACT-DASH-VIEW-INVENTORY | Open Inventory Alert | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-WAREHOUSE-STAFF | Click inventory alert | Đi đến Inventory / Low Stock. | FLOW-INVENTORY-LOW-STOCK-ALERT |
| ACT-DASH-VIEW-SYNC | Open Sync Alert | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-PRODUCT-STAFF | Click sync alert | Đi đến Sync Error Detail hoặc Sync Log. | FLOW-SYNC-ERROR-RETRY |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-SELLER-DASHBOARD-001 | Màn hình chỉ hiển thị dữ liệu theo đúng role và scope. | FLOW-DASHBOARD-SELLER-VIEW |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| Không áp dụng | - | - | - | - | Màn này không có form chính hoặc form sẽ được chi tiết ở bước UI/API. |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-SELLER-DASHBOARD-001 | User phải có quyền truy cập màn hình. | Bạn không có quyền truy cập màn hình này. |
| VAL-SELLER-DASHBOARD-002 | Dữ liệu phải thuộc đúng shop/tenant hoặc platform scope. | Không thể truy cập dữ liệu ngoài phạm vi được cấp quyền. |

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
| Không áp dụng | No | No | N/A | Không có hành động nguy hiểm mặc định. |

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
| View | No | Không bắt buộc log khi chỉ xem, trừ dữ liệu nhạy cảm. |

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
| SCR-ORDER-LIST | Mở danh sách đơn theo trạng thái. |
| SCR-INVENTORY-LOW-STOCK | Mở cảnh báo tồn kho. |
| SCR-SYNC-ERROR-DETAIL | Xem lỗi đồng bộ. |
| SCR-REPORT-PROFIT | Xem báo cáo lợi nhuận nếu có quyền. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-DASH-001 | Seller dashboard có cần cho user tự cấu hình widget không? | Ảnh hưởng UX dashboard. | Later |

---

## 20. Design Notes

Thiết kế cần ưu tiên sự rõ ràng, dễ scan dữ liệu, action chính nổi bật, tránh quá nhiều thông tin phụ trên cùng một màn.

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
| Screen có Screen ID chưa? | Pass | SCR-SELLER-DASHBOARD |
| Có route chưa? | Pass | `/app/dashboard` |
| Có map feature chưa? | Pass | FEAT-DASHBOARD-SELLER-OVERVIEW, FEAT-DASHBOARD-ORDER-SUMMARY, FEAT-DASHBOARD-INVENTORY-ALERT, FEAT-DASHBOARD-SYNC-ALERT, FEAT-DASHBOARD-PROFIT-SUMMARY |
| Có map flow chưa? | Pass | FLOW-DASHBOARD-SELLER-VIEW |
| Có allowed roles chưa? | Pass | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-ACCOUNTANT, ROLE-ORDER-STAFF, ROLE-WAREHOUSE-STAFF |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation hoặc ghi không áp dụng. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi open questions. |
