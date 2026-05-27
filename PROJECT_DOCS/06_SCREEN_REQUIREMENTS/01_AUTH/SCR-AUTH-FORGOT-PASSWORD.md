# SCR-AUTH-FORGOT-PASSWORD - Forgot Password

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-AUTH-FORGOT-PASSWORD |
| Screen Name | Forgot Password |
| Route | `/forgot-password` |
| Screen Type | Auth |
| Module ID | MOD-AUTH |
| Related Feature IDs | FEAT-AUTH-FORGOT-PASSWORD |
| Related Flow IDs | FLOW-AUTH-RESET-PASSWORD |
| Allowed Roles | All login roles |
| Priority | Should-have |
| Status | Draft |
| Depends On | 02_USER_ROLES.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Cho phép user yêu cầu đặt lại mật khẩu bằng email đăng ký.

---

## 2. User Goal

User nhận hướng dẫn đặt lại mật khẩu nếu email hợp lệ.

---

## 3. Entry Points

| Entry Point | From Screen | Condition |
| --- | --- | --- |
| Forgot password link | SCR-AUTH-LOGIN | User bấm quên mật khẩu. |

---

## 4. Exit Points / Navigation

| Action | Target Screen | Condition |
| --- | --- | --- |
| Back to login | SCR-AUTH-LOGIN | User quay lại. |
| Open reset link | SCR-AUTH-RESET-PASSWORD | User mở link trong email. |

---

## 5. Allowed Roles & Access Rules

| Role ID | Access Level | Notes |
| --- | --- | --- |
| All login roles | Full | Seller/platform users. |
| Customer / Buyer | No Access | Không thuộc ShopHub. |

---

## 6. Main UI Sections

| Section ID | Section Name | Purpose | Visible To |
| --- | --- | --- | --- |
| SEC-FORGOT-FORM | Forgot Password Form | Nhập email để gửi hướng dẫn. | All |
| SEC-FORGOT-SUCCESS | Success Message | Thông báo trung tính. | All |

---

## 7. Data Displayed

| Data Field | Description | Source / Related Entity | Required | Notes |
| --- | --- | --- | --- | --- |
| Email | Email user nhập. | User input | Yes | Không xác nhận công khai email có tồn tại hay không. |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-FORGOT-SUBMIT | Send Reset Request | All login roles | Submit email | Gửi hướng dẫn nếu email hợp lệ. | FLOW-AUTH-RESET-PASSWORD |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-FORGOT-001 | Không tiết lộ email có tồn tại hay không. | Security best practice |
| RULE-FORGOT-002 | Reset token/link phải có thời hạn. | FLOW-AUTH-RESET-PASSWORD |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| Email | Email input | Yes | Email format | owner@shop.com |  |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-FORGOT-001 | Email không rỗng và đúng định dạng. | Email không hợp lệ. |

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
| Send reset request | No | No | N/A |  |

---

## 14. Notification / Toast

| Event | Message Type | Suggested Message |
| --- | --- | --- |
| Request sent | Success | Nếu email tồn tại trong hệ thống, hướng dẫn đặt lại mật khẩu đã được gửi. |

---

## 15. Audit Log Requirement

| Action | Audit Required | Data to Log |
| --- | --- | --- |
| Forgot password request | Recommended | email, time, IP/device nếu có. |

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
| SCR-AUTH-LOGIN | Quay lại login. |
| SCR-AUTH-RESET-PASSWORD | Reset password từ email. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-AUTH-FORGOT-001 | Reset dùng link, OTP hay cả hai? | Ảnh hưởng reset flow. | Open |

---

## 20. Design Notes

Message phải trung tính để tránh lộ email có tồn tại.

---

## 21. Dev Notes

Cần rate-limit request và không log token plaintext.

---

## 22. Verification Checklist

| Check Item | Result | Notes |
|---|---|---|
| Screen có Screen ID chưa? | Pass | SCR-AUTH-FORGOT-PASSWORD |
| Có route chưa? | Pass | `/forgot-password` |
| Có map feature chưa? | Pass | FEAT-AUTH-FORGOT-PASSWORD |
| Có map flow chưa? | Pass | FLOW-AUTH-RESET-PASSWORD |
| Có allowed roles chưa? | Pass | All login roles |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi Open Questions. |
