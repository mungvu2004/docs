# SCR-AUTH-LOGIN - Login

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-AUTH-LOGIN |
| Screen Name | Login |
| Route | `/login` |
| Screen Type | Auth |
| Module ID | MOD-AUTH |
| Related Feature IDs | FEAT-AUTH-LOGIN, FEAT-AUTH-SESSION, FEAT-AUTH-RBAC |
| Related Flow IDs | FLOW-AUTH-LOGIN-SESSION |
| Allowed Roles | All login roles |
| Priority | Must-have |
| Status | Draft |
| Depends On | 02_USER_ROLES.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Cho phép seller-side user và platform-side user đăng nhập vào ShopHub. Không dành cho khách mua hàng cuối.

---

## 2. User Goal

User đăng nhập thành công và được điều hướng đến dashboard đúng scope: seller hoặc platform.

---

## 3. Entry Points

| Entry Point | From Screen | Condition |
| --- | --- | --- |
| Direct URL | Browser | User truy cập `/login`. |
| Session expired | Any protected screen | Phiên đăng nhập hết hạn. |

---

## 4. Exit Points / Navigation

| Action | Target Screen | Condition |
| --- | --- | --- |
| Login success seller | SCR-SELLER-DASHBOARD | User thuộc seller-side. |
| Login success platform | SCR-PLATFORM-DASHBOARD | User thuộc platform-side. |
| Forgot password | SCR-AUTH-FORGOT-PASSWORD | User chọn quên mật khẩu. |

---

## 5. Allowed Roles & Access Rules

| Role ID | Access Level | Notes |
| --- | --- | --- |
| All seller-side roles | Full | Nếu account active và shop cho phép truy cập. |
| All platform-side roles | Full | Nếu account active. |
| Customer / Buyer | No Access | Không có customer portal. |

---

## 6. Main UI Sections

| Section ID | Section Name | Purpose | Visible To |
| --- | --- | --- | --- |
| SEC-LOGIN-BRANDING | Branding Area | Logo/tên ShopHub. | All |
| SEC-LOGIN-FORM | Login Form | Nhập email/mật khẩu. | All |
| SEC-LOGIN-ERROR | Error Area | Hiển thị lỗi đăng nhập. | All |

---

## 7. Data Displayed

| Data Field | Description | Source / Related Entity | Required | Notes |
| --- | --- | --- | --- | --- |
| Email | Email đăng nhập. | User | Yes |  |
| Error message | Lỗi đăng nhập. | Auth API | No | Không lộ chi tiết nhạy cảm. |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-LOGIN-SUBMIT | Submit Login | All login roles | Click Login | Đăng nhập hoặc hiển thị lỗi. | FLOW-AUTH-LOGIN-SESSION |
| ACT-LOGIN-FORGOT | Go to Forgot Password | All login roles | Click forgot password | Đi tới forgot password. | FLOW-AUTH-RESET-PASSWORD |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-LOGIN-001 | Không cho khách mua hàng cuối đăng nhập ShopHub. | DEC-SITEMAP-002 |
| RULE-LOGIN-002 | Điều hướng theo seller/platform scope sau login. | FLOW-AUTH-LOGIN-SESSION |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| Email | Email input | Yes | Email format | owner@shop.com |  |
| Password | Password input | Yes | Không rỗng | •••••••• |  |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-LOGIN-001 | Email không rỗng và đúng định dạng. | Email không hợp lệ. |
| VAL-LOGIN-002 | Password không rỗng. | Vui lòng nhập mật khẩu. |
| VAL-LOGIN-003 | Auth failed. | Email hoặc mật khẩu không đúng. |

---

## 12. State Handling

### 12.1 Loading State

Disable action chính và hiển thị trạng thái đang xử lý.

### 12.2 Empty State

Không áp dụng cho auth screen, trừ khi token/invitation không có dữ liệu.

### 12.3 Error State

Hiển thị lỗi rõ ràng, không lộ thông tin nhạy cảm.

### 12.4 Permission Denied State

Chặn truy cập khi token không hợp lệ, user không đúng scope hoặc account không có quyền.

### 12.5 Success State

Hiển thị thông báo thành công và điều hướng đến màn phù hợp.

---

## 13. Confirmation / Approval

| Action | Confirm Required | Approval Required | Approver | Notes |
| --- | --- | --- | --- | --- |
| Login | No | No | N/A |  |

---

## 14. Notification / Toast

| Event | Message Type | Suggested Message |
| --- | --- | --- |
| Login success | Success | Đăng nhập thành công. |
| Login failed | Error | Email hoặc mật khẩu không đúng. |

---

## 15. Audit Log Requirement

| Action | Audit Required | Data to Log |
| --- | --- | --- |
| Login success / repeated failed login | Recommended | user/email, time, IP/device nếu có. |

---

## 16. Responsive Requirement

| Device | Requirement |
|---|---|
| Desktop | Form căn giữa, dễ đọc, thông tin rõ ràng. |
| Tablet | Layout một cột, thao tác chính dễ bấm. |
| Mobile | Input/button full width, OTP/password dễ nhập. |

---

## 17. Accessibility Notes

- Mọi input phải có label rõ ràng.
- Error message phải liên kết với input tương ứng.
- Có thể submit bằng keyboard.
- Trạng thái loading/error/success phải có text, không chỉ dùng màu.

---

## 18. Related Screens

| Screen ID | Relationship |
| --- | --- |
| SCR-AUTH-FORGOT-PASSWORD | Quên mật khẩu. |
| SCR-SELLER-DASHBOARD | Seller login success. |
| SCR-PLATFORM-DASHBOARD | Platform login success. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-AUTH-LOGIN-001 | Có cần Remember Me không? | Session policy. | Open |

---

## 20. Design Notes

Login phải đơn giản, rõ ràng, không gây hiểu nhầm đây là cổng khách mua hàng.

---

## 21. Dev Notes

Backend phải enforce RBAC/scope; frontend không tự quyết định quyền.

---

## 22. Verification Checklist

| Check Item | Result | Notes |
|---|---|---|
| Screen có Screen ID chưa? | Pass | SCR-AUTH-LOGIN |
| Có route chưa? | Pass | `/login` |
| Có map feature chưa? | Pass | FEAT-AUTH-LOGIN, FEAT-AUTH-SESSION, FEAT-AUTH-RBAC |
| Có map flow chưa? | Pass | FLOW-AUTH-LOGIN-SESSION |
| Có allowed roles chưa? | Pass | All login roles |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi Open Questions. |
