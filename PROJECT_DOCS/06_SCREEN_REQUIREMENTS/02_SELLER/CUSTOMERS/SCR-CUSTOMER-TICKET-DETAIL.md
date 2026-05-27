# SCR-CUSTOMER-TICKET-DETAIL - Customer Ticket Detail

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-CUSTOMER-TICKET-DETAIL |
| Screen Name | Customer Ticket Detail |
| Route | `/app/customer-tickets/:ticketId` |
| Screen Type | Seller |
| Module ID | MOD-CUSTOMER |
| Related Feature IDs | FEAT-CUSTOMER-LIST, FEAT-CUSTOMER-DETAIL, FEAT-CUSTOMER-ORDER-HISTORY, FEAT-CUSTOMER-NOTES, FEAT-CUSTOMER-ISSUE-TRACKING, FEAT-CUSTOMER-NO-LOGIN |
| Related Flow IDs | FLOW-CUSTOMER-VIEW-MANAGE, FLOW-CUSTOMER-CARE-TICKET |
| Allowed Roles | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-CUSTOMER-SUPPORT, ROLE-ORDER-STAFF, ROLE-ACCOUNTANT limited |
| Priority | Should-have |
| Status | Draft |
| Depends On | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Xử lý và theo dõi chi tiết ticket CSKH.

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
| ROLE-CUSTOMER-SUPPORT | View / Limited by permission | Theo permission matrix. |
| ROLE-ORDER-STAFF | View / Limited by permission | Theo permission matrix. |
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
| Customer profile | Tên, liên hệ, thông tin từ đơn hàng. | Customer | Yes | Chỉ hiển thị theo quyền. |
| Order history | Lịch sử đơn của khách trong shop. | Order | Should |  |
| Ticket status | Open/InProgress/WaitingCustomer/Resolved/Closed. | Customer Ticket | For ticket screens | SLA để later. |
| Priority / Assignee | Mức ưu tiên và người phụ trách. | Customer Ticket | For ticket screens | Phase đầu có priority + assignee. |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-CUSTOMER-VIEW | View Customer | Allowed roles | Open customer | Thông tin khách hiển thị theo quyền. | FLOW-CUSTOMER-VIEW-MANAGE |
| ACT-TICKET-CREATE | Create Ticket | ROLE-CUSTOMER-SUPPORT, ROLE-ORDER-STAFF, ROLE-SHOP-MANAGER | Create ticket | Ticket Open được tạo. | FLOW-CUSTOMER-CARE-TICKET |
| ACT-TICKET-UPDATE | Update Ticket | ROLE-CUSTOMER-SUPPORT, ROLE-SHOP-MANAGER | Save ticket update | Ticket status/assignee/priority/note cập nhật. | FLOW-CUSTOMER-CARE-TICKET |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-CUSTOMER-TICKET-DETAIL-001 | Màn hình chỉ hiển thị dữ liệu theo đúng role và scope. | FLOW-CUSTOMER-VIEW-MANAGE, FLOW-CUSTOMER-CARE-TICKET |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| Ticket Title | Text | For ticket | Không rỗng | Khách hỏi về đơn hàng |  |
| Priority | Select | For ticket | Low/Medium/High | High | SLA later. |
| Assignee | User select | Should | User có quyền | Customer Support A |  |
| Status | Select | For ticket | Valid ticket status | Open |  |
| Note | Textarea | No | Max length | Nội dung xử lý |  |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-CUSTOMER-TICKET-DETAIL-001 | User phải có quyền truy cập màn hình. | Bạn không có quyền truy cập màn hình này. |
| VAL-CUSTOMER-TICKET-DETAIL-002 | Dữ liệu phải thuộc đúng shop/tenant hoặc platform scope. | Không thể truy cập dữ liệu ngoài phạm vi được cấp quyền. |

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
| Create/update ticket | Recommended | actor, ticketId, status/assignee/priority changes, time |

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
| SCR-ORDER-DETAIL | Ticket có thể liên quan đơn hàng. |
| SCR-CUSTOMER-DETAIL | Ticket thuộc khách hàng. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-CUSTOMER-TICKET-DETAIL-001 | Có cần tích hợp RAG/Chat Lazada ở phase này không? | Ảnh hưởng Customer Support flow. | Later |

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
| Screen có Screen ID chưa? | Pass | SCR-CUSTOMER-TICKET-DETAIL |
| Có route chưa? | Pass | `/app/customer-tickets/:ticketId` |
| Có map feature chưa? | Pass | FEAT-CUSTOMER-LIST, FEAT-CUSTOMER-DETAIL, FEAT-CUSTOMER-ORDER-HISTORY, FEAT-CUSTOMER-NOTES, FEAT-CUSTOMER-ISSUE-TRACKING, FEAT-CUSTOMER-NO-LOGIN |
| Có map flow chưa? | Pass | FLOW-CUSTOMER-VIEW-MANAGE, FLOW-CUSTOMER-CARE-TICKET |
| Có allowed roles chưa? | Pass | ROLE-SHOP-OWNER, ROLE-SHOP-MANAGER, ROLE-CUSTOMER-SUPPORT, ROLE-ORDER-STAFF, ROLE-ACCOUNTANT limited |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation hoặc ghi không áp dụng. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi open questions. |
