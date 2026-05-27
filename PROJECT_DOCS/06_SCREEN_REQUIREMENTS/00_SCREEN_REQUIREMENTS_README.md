# 00_SCREEN_REQUIREMENTS_README.md

## Metadata

| Field | Value |
|---|---|
| Document ID | DOC-06-SCREEN-REQUIREMENTS |
| Project Name | Multi-channel E-commerce Hub / ShopHub |
| Version | 0.2 |
| Status | Draft - Full Screen Requirement Package |
| Depends On | 01_PROJECT_BRIEF.md, 02_USER_ROLES.md, 03_FEATURE_LIST.md, 04_BUSINESS_FLOW.md, 05_SITEMAP_AND_SCREEN_LIST.md |

---

# 1. Purpose

Folder này chứa tài liệu yêu cầu chi tiết cho từng màn hình của ShopHub.

Mỗi screen trong `05_SITEMAP_AND_SCREEN_LIST.md` được tách thành một file `.md` riêng để dễ quản lý, review, giao cho designer/dev và mapping sang API/Permission/QA.

---

# 2. Screen Requirement Rules

| Rule ID | Rule |
|---|---|
| RULE-SCREEN-001 | Mỗi screen một file riêng. |
| RULE-SCREEN-002 | Không tạo screen ngoài sitemap nếu chưa có decision mới. |
| RULE-SCREEN-003 | Mỗi screen phải có Screen ID, route, module, feature, flow, allowed roles. |
| RULE-SCREEN-004 | Seller-side và Platform-side phải tách riêng. |
| RULE-SCREEN-005 | Không tạo customer portal cho khách mua hàng cuối. |
| RULE-SCREEN-006 | Action nhạy cảm phải có confirm/approval/audit nếu flow yêu cầu. |
| RULE-SCREEN-007 | Lazada API/mapping chưa confirm thì ghi Open Questions, không tự bịa field. |
| RULE-SCREEN-008 | Payment logic nằm ở backend/API/VNPay; UI chỉ hiển thị trạng thái đã xác minh. |
| RULE-SCREEN-009 | Inventory adjustment threshold cấu hình theo shop, mặc định 10.000.000 VND/sản phẩm. |
| RULE-SCREEN-010 | RAG, SLA, Marketing, Multi-warehouse để deferred. |

---

# 3. Folder Structure

```txt
06_SCREEN_REQUIREMENTS/
├── 00_SCREEN_REQUIREMENTS_README.md
├── SCREEN_COVERAGE_MATRIX.md
├── 01_AUTH/
├── 02_SELLER/
├── 03_PLATFORM/
└── 99_DEFERRED/
```

---

# 4. Notes

Bộ tài liệu này là bản draft chi tiết ở mức screen requirement. Khi sang API/Data Model/Permission Matrix, cần quay lại refine:
- Field chi tiết.
- Validation chi tiết.
- Permission action-level.
- API response/error states.
- Export report format.
- Lazada status mapping.
- VNPay reconciliation details.
