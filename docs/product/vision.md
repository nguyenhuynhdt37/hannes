# Tầm nhìn sản phẩm

Trạng thái tất cả capability: **Not implemented**. Hannes là harness độc lập phục vụ developer thao tác trong repository với vòng lặp có thể quan sát, dừng, kiểm duyệt và tiếp tục. Người dùng mục tiêu cần quyền sở hữu session, thay provider và tích hợp công cụ mà không phụ thuộc một UI.

Luồng sản phẩm đích: chọn workspace/profile → gửi yêu cầu → thấy stream và tool requests → chấp thuận theo policy → kiểm tra diff/kết quả → tiếp tục hoặc resume. CLI headless phù hợp scripting, TUI cho tương tác, server cho clients. Web/desktop chỉ là chỗ dự trữ.

Ưu tiên: tính đúng đắn của contracts và permission trước trải nghiệm; durable session trước orchestration phức tạp; compatibility công khai theo capability và version. Không hứa tái tạo private API hoặc binary plugins. Không coi output model là bằng chứng tool thành công.

Phạm vi thành công đích: thực hiện chỉnh sửa có review, biết trạng thái side effects sau interruption, chi phí/tokens có budget, plugin failures không âm thầm phá session. Ngoài phạm vi Phase 0: tính năng chạy được và benchmark marketing.

Quyết định sản phẩm được kiểm chứng bằng [feature matrix](feature-parity.md), [roadmap](../delivery/implementation-roadmap.md) và [threat model](../delivery/threat-model.md). Không cam kết thời gian triển khai thiếu cơ sở.
