# SCR-AUTH-RESET-PASSWORD - Reset Password

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-AUTH-RESET-PASSWORD |
| Screen Name | Reset Password |
| Route | `/reset-password` |
| Screen Type | Auth |
| Module ID | MOD-AUTH |
| Related Feature IDs | FEAT-AUTH-RESET-PASSWORD |
| Related Flow IDs | FLOW-AUTH-RESET-PASSWORD |
| Allowed Roles | All login roles |
| Priority | Should-have |
| Status | Draft |
| Depends On | 02_USER_ROLES.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Cho phép user đặt mật khẩu mới sau khi có token/link reset hợp lệ.

---

## 2. User Goal

User cập nhật mật khẩu và đăng nhập lại.

---

## 3. Entry Points

| Entry Point | From Screen | Condition |
| --- | --- | --- |
| Reset link | Email | User mở link reset. |

---

## 4. Exit Points / Navigation

| Action | Target Screen | Condition |
| --- | --- | --- |
| Reset success | SCR-AUTH-LOGIN | Mật khẩu cập nhật thành công. |
| Token expired | SCR-AUTH-FORGOT-PASSWORD | Token hết hạn. |

---

## 5. Allowed Roles & Access Rules

| Role ID | Access Level | Notes |
| --- | --- | --- |
| All login roles | Full | Cần token hợp lệ. |

---

## 6. Main UI Sections

| Section ID | Section Name | Purpose | Visible To |
| --- | --- | --- | --- |
| SEC-RESET-FORM | Reset Password Form | Nhập mật khẩu mới. | All |
| SEC-RESET-ERROR | Error Area | Hiển thị token/validation error. | All |

---

## 7. Data Displayed

| Data Field | Description | Source / Related Entity | Required | Notes |
| --- | --- | --- | --- | --- |
| Token status | Hợp lệ/hết hạn/không hợp lệ. | Auth API | Yes | Không hiển thị token. |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-RESET-SUBMIT | Submit New Password | All login roles | Submit form | Mật khẩu được cập nhật. | FLOW-AUTH-RESET-PASSWORD |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-RESET-001 | Token reset phải hợp lệ và chưa hết hạn. | FLOW-AUTH-RESET-PASSWORD |
| RULE-RESET-002 | Password mới phải đạt password policy. | Auth policy |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| New Password | Password input | Yes | Theo password policy | •••••••• |  |
| Confirm Password | Password input | Yes | Khớp New Password | •••••••• |  |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-RESET-001 | Password đạt policy. | Mật khẩu chưa đạt yêu cầu. |
| VAL-RESET-002 | Confirm Password khớp. | Mật khẩu xác nhận không khớp. |
| VAL-RESET-003 | Token hợp lệ. | Link đặt lại mật khẩu không hợp lệ hoặc đã hết hạn. |

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
| Reset password | No | No | N/A |  |

---

## 14. Notification / Toast

| Event | Message Type | Suggested Message |
| --- | --- | --- |
| Reset success | Success | Mật khẩu đã được cập nhật. Vui lòng đăng nhập lại. |

---

## 15. Audit Log Requirement

| Action | Audit Required | Data to Log |
| --- | --- | --- |
| Password reset success | Recommended | user/email, time, IP/device nếu có. |

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
| SCR-AUTH-FORGOT-PASSWORD | Yêu cầu lại link. |
| SCR-AUTH-LOGIN | Đăng nhập sau reset. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-AUTH-RESET-001 | Password policy cụ thể là gì? | Validation và UX. | Open |

---

## 20. Design Notes

Hiển thị password policy ngắn gọn.

---

## 21. Dev Notes

Token reset dùng một lần, không log token/password.

---

## 22. Verification Checklist

| Check Item | Result | Notes |
|---|---|---|
| Screen có Screen ID chưa? | Pass | SCR-AUTH-RESET-PASSWORD |
| Có route chưa? | Pass | `/reset-password` |
| Có map feature chưa? | Pass | FEAT-AUTH-RESET-PASSWORD |
| Có map flow chưa? | Pass | FLOW-AUTH-RESET-PASSWORD |
| Có allowed roles chưa? | Pass | All login roles |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi Open Questions. |
