| Rule    | Nội dung                                                  | Ví dụ                                                       |
| ------- | --------------------------------------------------------- | ----------------------------------------------------------- |
| Rule 1  | Mỗi tài liệu chỉ giải quyết **một nhóm vấn đề chính**     | Feature List chỉ nói tính năng, không viết chi tiết API     |
| Rule 2  | Mỗi thông tin chỉ có **một nơi chốt chính**               | Quyền nằm ở Permission Matrix, không rải khắp các file      |
| Rule 3  | Tài liệu sau phải dựa trên tài liệu trước                 | API Spec phải dựa trên Feature + Flow + Screen + Data Model |
| Rule 4  | Không tự bịa nghiệp vụ                                    | Chưa rõ thì ghi vào Open Questions                          |
| Rule 5  | Tất cả module, feature, flow, screen, API phải có ID      | `FEAT-ORDER-CANCEL`, `SCR-ORDER-DETAIL`                     |
| Rule 6  | Mỗi tài liệu phải có phần liên kết sang tài liệu khác     | Related Features, Related Screens, Related APIs             |
| Rule 7  | Không copy-paste cùng một rule ở nhiều nơi                | Rule hủy đơn chỉ viết chính ở Business Flow                 |
| Rule 8  | Mỗi tài liệu phải có trạng thái                           | Draft / Review / Approved / Deprecated                      |
| Rule 9  | Khi sửa một tài liệu, phải kiểm tra tài liệu liên quan    | Sửa feature thì phải check flow, screen, API                |
| Rule 10 | Tài liệu phải đủ để người khác làm tiếp mà không cần đoán | Designer/dev/AI đọc xong hiểu phải làm gì                   |
