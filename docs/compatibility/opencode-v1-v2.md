# OpenCode V1 và V2 plugin compatibility

**Not implemented.** Nguồn truy cập 2026-11-13: [plugin overview](https://opencode.ai/docs/build/plugins/), [V1 migration](https://opencode.ai/docs/build/plugins/migrate-v1/), [server API](https://opencode.ai/docs/build/plugins/server/). Snapshot repository `f6e0c2d0833ffe772035f221fed923e1cd5fb408`: [Promise plugin](https://github.com/anomalyco/opencode/blob/f6e0c2d0833ffe772035f221fed923e1cd5fb408/packages/plugin/src/promise/plugin.ts), [Effect plugin](https://github.com/anomalyco/opencode/blob/f6e0c2d0833ffe772035f221fed923e1cd5fb408/packages/plugin/src/effect/plugin.ts), [config schema](https://github.com/anomalyco/opencode/blob/f6e0c2d0833ffe772035f221fed923e1cd5fb408/packages/schema/src/config/plugin.ts). Docs có thể tiến nhanh hơn package release; phải pin đồng thời host và API version cho fixtures.

## V1

V1 function entry nhận plugin context và trả tools/hooks. Tên/context fields và hooks cụ thể phải inventory từ pinned V1 declaration trước implementation; không suy ngược toàn bộ từ V2 docs. Migration docs chỉ ra object dual entry hỗ trợ từ >=1.18.29, sử dụng `server()` và `setup()` cho đường host khác nhau. V1 implementations không tự chạy dưới V2. Hannes v1/adapter dự kiến chuyển supported hooks/tool registration sang extension contracts, map client calls qua sdk-bridge và expose unsupported API error có tên method.

Không giữ một global OpenCode-shaped object giả trống. Plugin cần filesystem ambient access, undocumented internals, direct SDK types hoặc hook timing không tái hiện được có thể không compatible. Hook thay tool args phải đánh giá permission lại; legacy API không được bỏ gate.

## V2 server

V2 `Plugin.define` khai id/setup; setup nhận context và có cleanup. API context nhóm theo domain, có transform/hook/subscribe/reload trong docs. Registrations kết thúc khi unload và có dispose. Ordered synchronous transforms không được đổi thành async callback queue; nếu host Hannes không giữ semantics thì reject capability. Promise và Effect surfaces phải khảo sát riêng; không coi hai entry cùng ABI chỉ do cùng tên.

| Expected API | Hannes dự kiến | Strategy / giới hạn |
|---|---|---|
| Plugin context/setup/dispose | ExtensionContext + generation ledger | Adapter, ownership/cleanup test |
| Tools | ToolDescriptor/Invocation | Schema/error/output shim; permissions luôn Hannes |
| Model/provider selection | ModelPort + capability snapshot | Mapping IDs/usage; unsupported model extensions báo rõ |
| Session/messages/events | Session/Command/Event ports | SDK bridge, translate IDs/order/content; không pass DB |
| Hooks/transforms | Hooks contract | Timing/order/async/veto semantics phải match từng hook |
| Client SDK | Hannes SDK proxy | Chỉ mapped methods; transport shapes/error types cần fixture |
| Config/storage | Namespaced extension config/store ports | Không expose raw SQLite hoặc OpenCode private storage |
| Reload | Extension generation transition | Dispose ordering, inflight calls và service invalidation |
| UI/RPC parts | Host riêng | Không suy ra từ server support |

## Discovery và package loading

Docs V2 dùng `plugins`, thay `plugin` V1; package có thể export server `.`, `./tui`, `./gui-experimental`, `./rpc` với một `@opencode/plugin` dependency. Discovery/config chỉ tạo install/load proposal. Hannes kiểm local path/npm identity, version/integrity, exports, dependencies, host availability và trust trước evaluation. Không tự chạy install scripts hoặc tải package trong Phase 0. V1/V2 entry detection explicit, không thử execute hai lần để đoán version.

## Test gates và rủi ro

Fixture corpus cần license/provenance, host/package pins, expected registrations/events/results/cleanup và negative cases. Mỗi fixture chạy baseline upstream rồi Hannes trong phase được phép; so sánh semantics, không chỉ plugin import thành công. Cases: missing API, schema mismatch, hook order, synchronous transform, SDK pagination/errors, reload/dispose, permission deny, remote session scope và package version conflict.

V1 exact API snapshot, V2 Effect interop, OpenCode SDK coverage và plugin corpus thực tế còn Needs verification. GUI experimental API là target riêng và có thể unsupported cho tới host được thiết kế. Không fork toàn repository; xem [ADR](../adr/0003-plugin-compatibility.md), [UI/RPC](opencode-tui-gui-rpc.md), [matrix](compatibility-matrix.md).
