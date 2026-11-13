# Configuration và profiles

**Not implemented.** Tài liệu này là data design; Phase 0 không tạo config thực tế. Profile là snapshot lựa chọn model/mode/tools/extensions/budgets cho một run; bundle là nhóm plugin/profile có provenance, không là quyền thực thi tự động.

| Lớp | Nội dung | Quy tắc |
|---|---|---|
| Built-in defaults | Timeouts, limits, default mode | Giá trị an toàn, không secrets |
| User | Provider references, preferences | File user ngoài repo, vị trí còn cần quyết định |
| Workspace | Instructions, tools đề xuất | Untrusted trước workspace trust approval |
| Profile/bundle | Model, mode, skills, extensions | Version/provenance; không nâng quyền |
| CLI/session override | Model/budget/run options | Có audit, không vượt policy |
| Organization/security policy | Deny scopes, maximum limits | Ceiling áp dụng sau merge, không override bằng workspace |

Merge cho preferences: defaults < user < workspace < profile < explicit override; arrays thay nguyên danh sách, không merge mập mờ; unknown keys bị reject hoặc diagnostic theo schema version. Security merge riêng: Deny thắng Allow, grants là giao của các ceilings; policy không dùng quy tắc last-write-wins. Secrets chỉ là credentialRef, resolve tại boundary cần dùng.

Mỗi resolved snapshot có schemaVersion, revision/hash, nguồn từng giá trị, plugin versions và policyRevision. Run pin snapshot; provider endpoint thay không đổi âm thầm run đang chạy. Reload tạo generation mới, validate toàn bộ trước switch; fail giữ generation cũ. Revocation áp dụng ngay và hủy queued approvals; không chờ profile reload.

Plan mode mặc định chỉ read và produce plan; Build có thể yêu cầu write/execute; Review read/diff/test tùy grant. Mode không cấp permission. Unknown model capability chặn tool/multimodal request hoặc downgrade rõ ràng được chấp thuận, không strip silently.

9Router endpoint/auth/model naming chưa được user cung cấp và không tự suy ra từ tên. Không đồng nhất 9Router với OpenRouter. Phase 1 cần nguồn chính thức và fixture trước chọn adapter settings. DeepSeek profile translation phải ghi field unsupported, không drop security/cleanup semantics.

Acceptance: cấu hình cùng inputs cho cùng snapshot hash; malformed reload không nửa áp dụng; workspace không override deny; logs không lộ credential; profile pin tái hiện model/tool descriptors. Format config, vị trí lưu, signed bundles và schema migration cần ADR trước triển khai.
