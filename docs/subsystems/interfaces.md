# CLI, server, TUI và SDK

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Cung cấp command/event surfaces thống nhất cho headless CLI, interactive TUI và future web/desktop, không nhúng domain logic trong UI.

## 2. Trách nhiệm sở hữu

CLI sở hữu argument parsing/bootstrap/exit rendering; server sở hữu auth/routes/transport; SDK sở hữu protocol client/reconnect; TUI sở hữu editor/screens/render/theme/permission UX. Web/desktop chỉ .gitkeep.

## 3. Ngoài trách nhiệm

Không UI trực tiếp đọc SQLite hoặc execute tools. GUI plugin compatibility chưa có desktop host. Server routes không định nghĩa permission policy riêng.

## 4. Input, output và dữ liệu sở hữu

Input typed commands và auth/workspace scope. Output acknowledgement, snapshots, ordered durable events/deltas và structured errors. UI owns local drafts/scroll/reconnect cursor; server owns active connections, không own core domain state ngoài ports.

## 5. Xử lý và lifecycle

CLI run/serve/resume/models/mcp/plugins/doctor map vào commands/queries; startup resolve config then compose. TUI connect/auth → snapshot/replay → follow → render → submit expectedRevision. Permission dialog hiển thị exact action/target/expiry và trả scopeHash; không auto approve vì dialog đóng. Server sessions/agents/plugins routes chỉ expose supported capabilities.

## 6. Dependencies và interface boundaries

CLI/server là composition roots; TUI chỉ sdk; sdk chỉ contracts. HTTP commands và WebSocket events là dự kiến cần protocol ADR trước implementation; stdio mode nếu thêm phải có framing riêng. OpenCode SDK bridge là adapter, không Hannes SDK public contract.

## 7. Luồng sự kiện

UserSubmit → CommandAck → replay/follow events → canonical transcript update. Reconnect watermark ngăn gap; client detects seq gap và yêu cầu snapshot. Slow UI coalesce delta, giữ mọi approval/terminal event.

## 8. Errors, cancellation và recovery

Client disconnect không tự hủy detached run; cancel phải command explicit. Auth expiry pause subscriptions và reauth, không gửi secrets vào errors. Server crash reconnect bằng cursor, run recovery theo store; UI draft tách khỏi committed message. Doctor read-only mặc định và redacts diagnostics.

## 9. Concurrency, races và timeout

Bound subscription buffers/connections/body size; render loop không block server/core. Duplicate submit dedup commandId. UI approval có timeout/server authority; two clients approve cùng request chỉ một CAS thắng. TUI resize/render cancellation không cancel domain run.

## 10. Security considerations

Bind local server private endpoint/token; remote TLS/auth/workspace scopes và origin policy. Terminal output sanitize escape/control sequences; tool output không được inject terminal commands. SDK không expose credential bytes; plugin GUI main process là privileged host cần design riêng.

## 11. Thư viện dự kiến và alternatives

OpenTUI cho TUI; Hono cho server candidate; SDK fetch/WebSocket builtins. React/Solid binding chưa chọn và không suy ra từ .tsx. Alternative plain CLI trước; web/desktop framework chưa chọn.

## 12. Acceptance criteria và test cases

Cases: CLI headless Ask exit/pause, Ctrl-C cancel, reconnect lost delta, duplicate Submit, two approvals, terminal escape injection, huge output, resize, unauthorized session query. Pass khi UI không bypass gate và transcript canonical nhất quán.

## 13. Quyết định mở và rủi ro

Chưa chốt exit codes, local socket portability, server API version path, OpenTUI binding và remote deployment auth. Routes names trong tree là ownership placeholders, không endpoints hoạt động.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
