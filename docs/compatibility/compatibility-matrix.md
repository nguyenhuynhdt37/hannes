# Compatibility matrix

Tất cả status là **Not implemented**. Target version là phạm vi cần pin để viết fixtures, không là cam kết support. `Needs verification` áp dụng cho exact pin/interoperability khi chưa có bằng chứng. Acceptance tests dưới đây chưa chạy.

| Ecosystem | Capability | Target version | Strategy | Status | Acceptance test |
|---|---|---|---|---|---|
| Hannes | Model/tool/session contracts | Hannes proposed v1 | Native Support | Not implemented | Roundtrip schema, replay, unknown fields |
| 9Router | Text/tool streaming | Needs verification: endpoint/version | Planned | Not implemented | Fragmented calls, usage, auth, errors với endpoint đúng |
| OpenAI-compatible | Chat completions | Provider-specific pin needed | Adapter | Not implemented | Chat-only endpoint không gọi Responses |
| MCP | stdio tools | SDK v2 docs v2.4.0; protocol negotiated | Native Support | Not implemented | initialize/list/call/cancel/crash |
| MCP | Streamable HTTP | SDK v2.4.0 candidate | Native Support | Not implemented | session/resume/disconnect/OAuth |
| MCP | resources | Negotiated server capability | Adapter | Not implemented | URI scope, paginated list/read, content limits |
| MCP | prompts | Negotiated server capability | Adapter | Not implemented | arguments validation, provenance, no privilege gain |
| MCP | OAuth remote auth | SDK v2 OAuth API; AS-specific | Native Support | Not implemented | state/PKCE/issuer/refresh/revoke |
| MCP | Legacy SSE | Legacy protocol pin needed | Unsupported | Not implemented | Reject rõ transport unsupported; bridge chỉ sau ADR |
| OpenCode V1 | Function hooks/tools | V1 source pin required | Compatibility Shim | Not implemented | Golden plugin load/hook/tool/session calls |
| OpenCode V1 | Object dual entry | >=1.18.29 theo migration docs, exact pin needed | Adapter | Not implemented | server()/setup() đúng host, không double load |
| OpenCode V2 | Server plugins | Docs 2026-11-13; exact host/package pin needed | Adapter | Not implemented | define/setup/dispose, ordered transforms, service calls |
| OpenCode V2 | TUI plugins | Matching host + @opencode/plugin pin needed | Compatibility Shim | Not implemented | commands/routes/slots/dialog/storage và remote context |
| OpenCode V2 | GUI renderer plugins | Experimental GUI API snapshot; exact pin needed | Planned | Not implemented | Desktop window lifecycle/renderer thật; server load không đủ |
| OpenCode V2 | GUI main entry | Desktop ABI/host pin needed | Planned | Not implemented | Privilege scope, window close và cleanup |
| OpenCode V2 | Typed RPC | Matching server/client/plugin schema pin | Adapter | Not implemented | Codec/errors/cancel/version + auth, không chỉ type-check |
| OpenCode | npm/discovery | Config plugins schema tại pinned host | Adapter | Not implemented | package exports/integrity/local paths/trust |
| OpenCode | Private internals/deep imports | Không target | Unsupported | Not implemented | Fail explicit API unsupported |
| DeepSeek | Context/Service DI | Harness 39163dd…; vendored upstream 4.0.0-rc.12 | Adapter | Not implemented | Missing service/inject/replacement/isolation |
| DeepSeek | Fiber/effects/cleanup | Cùng snapshot; exact installed fork pin needed | Adapter | Not implemented | Init fail/reload/dispose order và leak detection |
| DeepSeek | Profiles/bundles | Harness pinned snapshot | Compatibility Shim | Not implemented | Ordered patch composition, diagnostics unsupported fields |
| DeepSeek | Service/event bridging | Same snapshot; event modes mapped riêng | Adapter | Not implemented | emit/serial/parallel/waterfall/bail semantics |
| DeepSeek | dsh runtime delegation | Supported IPC interface Needs verification | Sidecar Delegation | Not implemented | Handshake/auth/cancel/crash/receipts; không invent RPC |
| DeepSeek | Arbitrary !!js config | Không auto-enable | Unsupported | Not implemented | Reject executable config chưa trusted/approved |
| Cordis upstream | Generic upstream plugins | 4.0.0-rc.12 reference; fork deltas cần verify | Planned | Not implemented | Same services/lifecycle fixture against both runtimes |
| Interfaces | Hannes desktop/web | Host chưa chọn | Planned | Not implemented | UI/auth/permission integration và plugin host conformance |

Chi tiết [MCP](mcp.md), [OpenCode APIs](opencode-v1-v2.md), [UI/RPC](opencode-tui-gui-rpc.md), [DeepSeek/Cordis](deepseek-cordis.md). Native Support là strategy dự kiến, không nghĩa đã có implementation.
