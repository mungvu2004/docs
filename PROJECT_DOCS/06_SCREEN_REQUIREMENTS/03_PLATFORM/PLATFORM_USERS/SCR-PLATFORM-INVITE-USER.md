# SCR-PLATFORM-INVITE-USER - Invite Platform User

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-PLATFORM-INVITE-USER |
| Screen Name | Invite Platform User |
| Route | `/platform/users/invite` |
| Screen Type | Platform |
| Module ID | MOD-USER |
| Related Feature IDs | FEAT-USER-INVITE-PLATFORM-USER |
| Related Flow IDs | FLOW-USER-PLATFORM-INVITE |
| Allowed Roles | ROLE-SHOPHUB-SUPER-ADMIN, ROLE-SHOPHUB-ADMIN |
| Priority | Must-have |
| Status | Draft |
| Depends On | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Mời user vào hệ thống ShopHub platform.

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
| ROLE-SHOPHUB-SUPER-ADMIN | View / Limited by permission | Theo permission matrix. |
| ROLE-SHOPHUB-ADMIN | View / Limited by permission | Theo permission matrix. |

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
| Platform scoped data | Dữ liệu vận hành nền tảng liên quan màn hình. | MOD-USER | Yes | Không lộ dữ liệu shop nếu role không có quyền. |
| Status / Summary | Trạng thái shop/subscription/payment/sync/audit tùy màn. | Platform entities | Yes |  |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-PLATFORM-VIEW | View / Manage Platform Data | ROLE-SHOPHUB-SUPER-ADMIN, ROLE-SHOPHUB-ADMIN | User mở hoặc thao tác màn | Dữ liệu hiển thị/cập nhật theo platform permission. | FLOW-USER-PLATFORM-INVITE |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-PLATFORM-INVITE-USER-001 | Màn hình chỉ hiển thị dữ liệu theo đúng role và scope. | FLOW-USER-PLATFORM-INVITE |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| Không áp dụng | - | - | - | - | Màn này không có form chính hoặc form sẽ được chi tiết ở bước UI/API. |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-PLATFORM-INVITE-USER-001 | User phải có quyền truy cập màn hình. | Bạn không có quyền truy cập màn hình này. |
| VAL-PLATFORM-INVITE-USER-002 | Dữ liệu phải thuộc đúng shop/tenant hoặc platform scope. | Không thể truy cập dữ liệu ngoài phạm vi được cấp quyền. |

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
| Suspend/Restore/Plan change/Manual resync review | Yes for sensitive actions | Depends on flow | Authorized platform role / Shop Owner for high-impact resync | Không impersonate user shop. |

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
| Platform sensitive action | Yes | actor, target, action, before/after, time |

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
| Theo sitemap | Liên kết điều hướng theo flow liên quan. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-PLATFORM-INVITE-USER-001 | Field chi tiết của màn platform này cần chốt ở API/Data Model nào? | Ảnh hưởng table/filter/detail. | Open |

---

## 20. Design Notes

Platform screen chỉ phục vụ vận hành ShopHub. Không dùng để can thiệp dữ liệu nghiệp vụ của shop nếu không có workflow rõ ràng.

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
| Screen có Screen ID chưa? | Pass | SCR-PLATFORM-INVITE-USER |
| Có route chưa? | Pass | `/platform/users/invite` |
| Có map feature chưa? | Pass | FEAT-USER-INVITE-PLATFORM-USER |
| Có map flow chưa? | Pass | FLOW-USER-PLATFORM-INVITE |
| Có allowed roles chưa? | Pass | ROLE-SHOPHUB-SUPER-ADMIN, ROLE-SHOPHUB-ADMIN |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation hoặc ghi không áp dụng. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi open questions. |
