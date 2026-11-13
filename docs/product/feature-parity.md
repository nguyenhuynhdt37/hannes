# Feature parity theo capability

Đây là backlog mục tiêu; mọi hàng **Not implemented**. Nguồn tham khảo mô tả công khai, không chứng minh Hannes tương đương.

| Capability | Cảm hứng | Đích Hannes | Acceptance / phase |
|---|---|---|---|
| Headless agent và tools | Codex, Goose | Một loop độc lập UI | P1: task đọc/chỉnh sửa có kết quả xác thực |
| Provider abstraction | OpenCode | 9Router trước; adapter đổi endpoint | P1: fixture tool call/usage/error qua cùng contract |
| Streaming/TUI | OpenCode | Delta có sequence, cancel qua TUI | P2: partial call không execute |
| Sessions/resume | Goose, Codex | Event log/checkpoint bền | P2–3: crash không replay shell mù |
| Context/project rules | Coding agents tham khảo | Budget, instructions, compaction có provenance | P3: không mất pending approval |
| Git/code intelligence | OpenCode/Codex | Diff, search, LSP, AST | P3: stale index được đánh dấu |
| MCP | Goose/OpenCode | Official SDK; tools/resources/prompts | P4: transport/OAuth/permission fixtures |
| Skills/hooks/plugins | OpenCode, DeepSeek | Capability-scoped extension host | P5: rollback registration khi init lỗi |
| OpenCode compatibility | API công khai được pin | V1/V2 server/TUI/GUI/RPC riêng | P6: từng fixture riêng; GUI chưa có host không đạt |
| Cordis composition | DeepSeek Harness | In-process/sidecar/hybrid chưa chốt | P7: effect cleanup, reload, service version |
| Subagents/background/sandbox | Codex/harness patterns | Budget phân bổ, isolation kiểm chứng | P8: child không mở rộng quyền |
| Web/desktop/marketplace | OpenCode/Goose | Interfaces và cài plugin có provenance | P9: host và conformance gates |

Chi tiết phiên bản, strategy và bài kiểm định tại [compatibility matrix](../compatibility/compatibility-matrix.md). Không dùng phần trăm tương thích tổng hợp.
