# DEFERRED_SCREENS.md

## Metadata

| Field | Value |
|---|---|
| Document ID | DOC-06-DEFERRED-SCREENS |
| Project Name | Multi-channel E-commerce Hub / ShopHub |
| Version | 0.1 |
| Status | Draft |
| Depends On | 05_SITEMAP_AND_SCREEN_LIST.md |

---

# 1. Purpose

Tài liệu này ghi lại các màn hình **không thuộc phase đầu** để tránh nhầm lẫn scope.

---

# 2. Deferred Screen List

| Deferred Screen | Reason | Related Module | Phase |
|---|---|---|---|
| Promotion Management | Marketing/Promotion để phase sau. | MOD-MARKETING | Later |
| Campaign Management | Chưa thuộc core scope. | MOD-MARKETING | Later |
| Voucher Management | Chưa thuộc core scope. | MOD-MARKETING | Later |
| Marketing Staff Role Screens | Chưa tạo role Marketing Staff phase đầu. | MOD-MARKETING | Later |
| Lazada Marketing / Ads Screens | Lazada API có nhóm ads/marketing nhưng Marketing/Promotion đang để phase sau. | MOD-MARKETING | Later |
| Lazada Chat / Message Integration Screens | Phase đầu dùng ticket nội bộ; tích hợp chat/message để sau. | MOD-CUSTOMER | Later / Could-have |
| Customer Support RAG Assistant | RAG cho CSKH để phase sau. | MOD-CUSTOMER | Later |
| SLA Management | Phase đầu chỉ có Priority + Assignee, chưa cần SLA. | MOD-CUSTOMER | Later |
| Multi-warehouse Management | Phase đầu mỗi shop chỉ có 1 kho vật lý. | MOD-INVENTORY | Later |
| Multi-shop User Management | Phase đầu 1 user thuộc 1 shop. | MOD-TENANT, MOD-USER | Later |
| Native Mobile App Screens | Ưu tiên web dashboard. | General | Later |
| Advanced CRM Screens | Customer care phase đầu chỉ có customer/ticket cơ bản. | MOD-CUSTOMER | Later |
| AI Recommendation Screens | Không thuộc scope phase đầu. | General | Later |
| Excel Import Screens | Đã chốt chưa cần import Excel phase đầu. | General | Later |
| E-Tickets / LazPay / Wallet Screens | Không thuộc core scope hiện tại. | Integration | Later |
| DataMoat / Risk / Analytics Screens | Không thuộc phase đầu. | Integration | Later |

---

# 3. Rules

| Rule ID | Rule |
|---|---|
| RULE-DEFERRED-001 | Không được đưa deferred screens vào phase đầu nếu chưa có decision mới. |
| RULE-DEFERRED-002 | Nếu muốn kích hoạt deferred screen, phải cập nhật Feature List, Business Flow, Sitemap và Screen Requirement. |
| RULE-DEFERRED-003 | RAG/AI/Marketing/Multi-warehouse đều phải có tài liệu riêng trước khi design/code. |
