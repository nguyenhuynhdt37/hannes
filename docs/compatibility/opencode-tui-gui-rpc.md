# OpenCode TUI, GUI và RPC: host riêng

**Not implemented.** Nguồn truy cập 2026-11-13: [TUI/CLI plugin docs](https://opencode.ai/docs/build/plugins/cli/), [GUI docs](https://opencode.ai/docs/build/plugins/gui/), [RPC docs](https://opencode.ai/docs/build/plugins/rpc/), [overview](https://opencode.ai/docs/build/plugins/). Snapshot GUI registry [packages/app/src/runtime/server/registry.tsx](https://github.com/anomalyco/opencode/blob/f6e0c2d0833ffe772035f221fed923e1cd5fb408/packages/app/src/runtime/server/registry.tsx). Package version/renderer ABI exact pin vẫn Needs verification; đọc docs không phải chạy compatibility tests.

| Part | Upstream công khai đã khảo sát | Hannes boundary / điều kiện |
|---|---|---|
| V2 server | Root entry setup/context | v2/server + extensions; không có render surface |
| V2 TUI | `./tui`, `@opencode/plugin/tui`; renderer/theme, commands/routes/slots/storage/dialogs, client/data/events | v2/tui adapter phải có equivalent host hoặc reject từng surface |
| V2 GUI renderer | `./gui-experimental`; setup cho mỗi Desktop window | v2/gui placeholder; phải có desktop renderer/window lifecycle |
| V2 GUI main | main trong desktop main process | Privileged bridge với grants riêng; không map vào server process âm thầm |
| V2 RPC | Typed contract được server implement và parts/clients gọi | v2/rpc codec/request/error/cancel bridge + authorization |
| SDK | Client model/tool/session API | v2/sdk-bridge map supported methods, không export Hannes core internals |

TUI theo server-loaded plugins, context client có thể trỏ connected/remote server; Hannes adapter không được giả client luôn local. Global/server state và per-terminal UI state phải tách; theme/render callbacks không được chạy trong headless server. Hannes chọn OpenTUI nhưng cùng renderer family không đủ chứng minh binary/API/state compatibility. Routes/slots/keybindings/dialogs cần mapping exact layout/focus/cleanup và input semantics.

GUI Desktop trong docs tải packages từ global configuration; setup có lifecycle từng window, main có lifecycle process khác. Hannes apps/desktop hiện chỉ `.gitkeep`, chưa có renderer, desktop main host, window RPC hay API permissions. Vì vậy GUI luôn Planned/Not implemented, không được đánh dấu tương thích sau khi server plugin chạy. Plugin đòi DOM/framework/native APIs hoặc desktop private storage không thể shim bằng server callbacks.

RPC contracts gồm method names, schemas, errors, cancellation và lifetime; TypeScript types không đủ wire compatibility. Hannes dự kiến translate supported requests qua authenticated server boundary, scope principal/workspace/extension, namespace methods, enforce payload/time limits và convert errors không lộ secrets. Không giả OpenCode dùng cùng Hannes HTTP/WebSocket protocol. Exact framing/serialization/subscription ordering phải lấy từ pinned RPC API trong Phase 6.

Lifecycle: server approves plugin package → reports available UI parts → client checks API/host version → loads only trusted supported parts → registers per-host disposers → unload window/terminal/server theo ownership. Remote server không được tự đẩy arbitrary executable UI code tới client mà không local trust/version check. Reload của một host không mặc nhiên reload tất cả windows; generations và disconnection errors explicit.

Acceptance riêng: TUI command registration/keybinding/focus/dialog close/dispose và remote client; GUI two windows setup/dispose độc lập, main shutdown và restricted native API; RPC schema mismatch/method unknown/error roundtrip/cancel/auth/slow subscriber; SDK session IDs/pagination/event order. Headless load UI-only plugin phải báo incompatible_host. Các tests chưa triển khai/chạy.

Chưa xác minh: full GUI API declarations/experimental stability, RPC codec và cancellation guarantees, renderer binding compatibility, plugin distribution trust và real fixture corpus. [Contracts](../contracts/plugins-and-hooks.md) và [matrix](compatibility-matrix.md) là cổng công bố support.
