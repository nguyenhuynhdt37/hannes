# Thuật ngữ

| Thuật ngữ | Nghĩa trong Hannes |
|---|---|
| Contract | Dữ liệu/ports công khai, có version; không chứa SDK-specific objects |
| Composition root | App bootstrap nối implementations với ports |
| Run / step / attempt | Một lần thực hiện yêu cầu / một lượt model-tools / một lần thử I/O; ID tách riêng |
| Session / branch | Hội thoại bền / nhánh lịch sử với checkpoint cha |
| Event | Sự kiện bất biến đã xảy ra; command là ý định có thể bị từ chối |
| Delta | Mảnh stream chưa hoàn chỉnh, không đủ quyền execute |
| Tool invocation | Lệnh đã chuẩn hóa có invocationId, input hash và quyền gắn với đối tượng cụ thể |
| Capability | Năng lực model, protocol hoặc quyền extension; luôn ghi rõ loại |
| Permission lease | Quyền giới hạn scope/thời hạn/policy revision; có thể bị thu hồi |
| Extension / plugin | Gói mở rộng Hannes / gói từ ecosystem cụ thể qua adapter |
| Skill | Nội dung hướng dẫn không đáng tin mặc định; không tự cấp quyền |
| Hook | Callback lifecycle với budget và quyền rõ ràng |
| Host / Client / Server MCP | App quản lý integrations / một peer connection / dịch vụ cung cấp capabilities |
| Fiber | Đơn vị lifecycle Cordis; không đồng nhất với OS thread |
| Sidecar | Process bên cạnh, có IPC; không mặc nhiên là sandbox |
| Snapshot / checkpoint | Mốc workspace / mốc runtime-state; phải phối hợp, không thay thế nhau |
| Recovery unknown | Không xác định side effect đã xảy ra; cần reconcile, không tự retry |
| Not implemented | Đặc tả chưa có implementation; mọi capability Phase 0 ở trạng thái này |
| Needs verification | Thiếu chứng cứ tên/version/API/interoperability để cam kết |

Đọc [event contract](../contracts/agent-events.md) để phân biệt durable sequence và ephemeral deltas.
