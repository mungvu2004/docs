# SCR-AUTH-ACCEPT-INVITATION - Accept Invitation

## Metadata

| Field | Value |
|---|---|
| Screen ID | SCR-AUTH-ACCEPT-INVITATION |
| Screen Name | Accept Invitation |
| Route | `/accept-invitation?token=...` |
| Screen Type | Auth |
| Module ID | MOD-AUTH, MOD-USER |
| Related Feature IDs | FEAT-USER-ACCEPT-INVITE, FEAT-AUTH-LOGIN |
| Related Flow IDs | FLOW-USER-ACCEPT-INVITE, FLOW-USER-INVITE-SHOP-USER, FLOW-USER-PLATFORM-INVITE |
| Allowed Roles | Invited User |
| Priority | Must-have |
| Status | Draft |
| Depends On | 02_USER_ROLES.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |
| Used By | UI/UX Design, Frontend, Backend API, Permission Matrix, QA |

---

## 1. Screen Purpose

Cho phép người được mời chấp nhận lời mời tham gia shop/platform bằng link email và OTP gửi qua email.

---

## 2. User Goal

User xác thực OTP, tạo tài khoản hoặc đăng nhập, sau đó được gán đúng role trong invitation.

---

## 3. Entry Points

| Entry Point | From Screen | Condition |
| --- | --- | --- |
| Invitation email link | Email | User được mời. |
| Direct URL with token | Browser | URL có token invitation. |

---

## 4. Exit Points / Navigation

| Action | Target Screen | Condition |
| --- | --- | --- |
| Accept success seller | SCR-SELLER-DASHBOARD or SCR-AUTH-LOGIN | User được mời vào shop. |
| Accept success platform | SCR-PLATFORM-DASHBOARD or SCR-AUTH-LOGIN | User được mời vào platform. |

---

## 5. Allowed Roles & Access Rules

| Role ID | Access Level | Notes |
| --- | --- | --- |
| Invited User | Full | Cần token hợp lệ và OTP đúng. |
| Non-invited User | No Access | Không có token hợp lệ thì không truy cập. |

---

## 6. Main UI Sections

| Section ID | Section Name | Purpose | Visible To |
| --- | --- | --- | --- |
| SEC-INVITE-SUMMARY | Invitation Summary | Hiển thị người mời, shop/platform, role. | Invited User |
| SEC-INVITE-OTP | OTP Verification | Nhập OTP qua email. | Invited User |
| SEC-INVITE-ACCOUNT | Account Setup/Login | Tạo mật khẩu hoặc đăng nhập tài khoản hiện có. | Invited User |

---

## 7. Data Displayed

| Data Field | Description | Source / Related Entity | Required | Notes |
| --- | --- | --- | --- | --- |
| Invited email | Email được mời. | Invitation | Yes | Không cho tự sửa. |
| Assigned roles | Role được gán sau accept. | Invitation | Yes |  |
| OTP status | Trạng thái OTP. | OTP service | Yes |  |

---

## 8. User Actions

| Action ID | Action Name | Actor / Role | Trigger | Expected Result | Related Flow |
| --- | --- | --- | --- | --- | --- |
| ACT-INVITE-VERIFY-OTP | Verify OTP | Invited User | Submit OTP | OTP hợp lệ cho phép tiếp tục. | FLOW-USER-ACCEPT-INVITE |
| ACT-INVITE-ACCEPT | Accept Invitation | Invited User | Confirm accept | User được gán role vào shop/platform. | FLOW-USER-ACCEPT-INVITE |

---

## 9. Action Rules

| Rule ID | Rule | Related Flow / Decision |
| --- | --- | --- |
| RULE-INVITE-001 | Invitation token phải hợp lệ và chưa hết hạn. | FLOW-USER-ACCEPT-INVITE |
| RULE-INVITE-002 | OTP gửi đến email được mời, không gửi sang email khác. | DEC-SITEMAP-009 |
| RULE-INVITE-003 | User chỉ được gán đúng role trong invitation. | 02_USER_ROLES.md |

---

## 10. Form Fields

| Field Name | Type | Required | Validation | Placeholder / Example | Notes |
| --- | --- | --- | --- | --- | --- |
| OTP Code | Text / numeric input | Yes | Đúng độ dài, chưa hết hạn | 123456 | OTP qua email. |
| New Password | Password input | Conditional | Password policy | •••••••• | Nếu user mới. |

---

## 11. Validation Rules

| Validation ID | Rule | Error Message |
| --- | --- | --- |
| VAL-INVITE-001 | Token hợp lệ. | Lời mời không hợp lệ hoặc đã hết hạn. |
| VAL-INVITE-002 | OTP đúng và chưa hết hạn. | Mã OTP không đúng hoặc đã hết hạn. |
| VAL-INVITE-003 | Email account khớp invitation. | Tài khoản không khớp email được mời. |

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
| Accept Invitation | Yes | No | N/A | User xác nhận tham gia. |

---

## 14. Notification / Toast

| Event | Message Type | Suggested Message |
| --- | --- | --- |
| OTP sent | Success | Mã OTP đã được gửi đến email của bạn. |
| Accept success | Success | Bạn đã chấp nhận lời mời thành công. |

---

## 15. Audit Log Requirement

| Action | Audit Required | Data to Log |
| --- | --- | --- |
| Invitation accepted | Yes | invitationId, invitedEmail, userId, scope, assignedRoles, time. |

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
| SCR-INVITE-SHOP-USER | Tạo seller invitation. |
| SCR-PLATFORM-INVITE-USER | Tạo platform invitation. |
| SCR-SHOP-USER-LIST | User xuất hiện sau accept. |

---

## 19. Open Questions

| Question ID | Question | Impact | Status |
| --- | --- | --- | --- |
| Q-SCR-AUTH-INVITE-001 | OTP dài bao nhiêu ký tự và hết hạn sau bao lâu? | Validation/security/UX. | Open |
| Q-SCR-AUTH-INVITE-002 | Invitation hết hạn sau bao lâu? | Copywriting và backend rule. | Open |

---

## 20. Design Notes

Cần hiển thị rõ ai mời, mời vào shop/platform nào, role gì. Không để user tự sửa email được mời.

---

## 21. Dev Notes

Token invitation và OTP là hai lớp xác thực khác nhau; accept phải idempotent hoặc xử lý trùng an toàn.

---

## 22. Verification Checklist

| Check Item | Result | Notes |
|---|---|---|
| Screen có Screen ID chưa? | Pass | SCR-AUTH-ACCEPT-INVITATION |
| Có route chưa? | Pass | `/accept-invitation?token=...` |
| Có map feature chưa? | Pass | FEAT-USER-ACCEPT-INVITE, FEAT-AUTH-LOGIN |
| Có map flow chưa? | Pass | FLOW-USER-ACCEPT-INVITE, FLOW-USER-INVITE-SHOP-USER, FLOW-USER-PLATFORM-INVITE |
| Có allowed roles chưa? | Pass | Invited User |
| Có action chính chưa? | Pass | Đã có user actions. |
| Có state handling chưa? | Pass | Loading/empty/error/permission/success. |
| Có validation nếu có form chưa? | Pass | Đã có validation. |
| Có open questions nếu thiếu thông tin chưa? | Pass | Đã ghi Open Questions. |
