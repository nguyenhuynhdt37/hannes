# Danh mục dependency (Phase 0)

**Ngày tra cứu:** 2026-11-13  
Các version là `latest` quan sát được trên npm registry vào ngày tra cứu, không phải phiên bản được chọn hay pin. `Needs verification` đánh dấu compatibility, release status hoặc product fit chưa xác minh. Catalog ghi evidence/candidate, không quyết định kiến trúc.

## Candidate dependencies

| Thành phần | Library đề xuất | Vai trò | Alternative | Rủi ro | Trạng thái |
|---|---|---|---|---|---|
| Runtime / package manager | `bun` | Runtime JS/TS, package manager, toolchain | Node.js + pnpm/npm | Native modules, deploy/CI target cần xác nhận | Needs verification — registry 1.4.4, không phải pin |
| TypeScript | `typescript` | Type checking và contracts | Bun transpiler; tsgo | Transpilation không phải type-check | Needs verification — registry 7.0.2; compiler version/target chưa chọn |
| Terminal UI | `@opentui/core` | TUI, Zig native core | Ink; OpenTUI Solid/React | Native binaries, OS/đóng gói, release cadence | Needs verification — registry 0.8.4 |
| AI SDK | `ai` | Generation, streaming, tools, provider registry | Provider API trực tiếp | API major tiến nhanh; cần đối chiếu model/feature | Needs verification — registry 7.0.172 |
| OpenAI-compatible adapter | `@ai-sdk/openai-compatible` | Custom provider qua baseURL/API key | `@ai-sdk/openai`; HTTP riêng | “Compatible” không bảo đảm tool/reasoning/stream parity | Needs verification — registry 3.0.92 |
| MCP TS SDK v2 | `@modelcontextprotocol/server`; `@modelcontextprotocol/client`; `@modelcontextprotocol/core` (chọn riêng theo nhu cầu) | MCP server, client và protocol primitives | v1 `@modelcontextprotocol/sdk` | v2 đổi package names/API; transport/adapters cần lựa chọn | v2 stable upstream; tag v2.4.0 và npm latest 2.4.0 được xác minh riêng |
| OpenCode plugin | `@opencode/plugin` | Plugin API/runtime OpenCode V2 | Plugin cục bộ hoặc API V1 | Chỉ dùng nếu cần OpenCode V2; version ghép với host | Needs verification — registry 2.3.0; upstream snapshot 2.2.0 |
| DeepSeek Cordis | `@deepseek-ai/cordis` | Context/service/plugin framework | Upstream `cordis` | Fork khác API/cadence; product fit chưa rõ | Needs verification — registry 4.1.0 |
| Cordis upstream | `cordis` | Core upstream Cordis | `@deepseek-ai/cordis` | Latest là prerelease 4.0.0-rc.13 | Needs verification — prerelease |
| HTTP | `hono` | HTTP API/router đa runtime | `Bun.serve`; Elysia | Middleware/auth/MCP adapter có thể chồng lớp | Needs verification — registry 4.13.18 |
| Schema | `zod` | Runtime validation, schema/type inference | Valibot; TypeBox; JSON Schema | Cần khớp major với SDK, tránh nhiều bản Zod | Needs verification — registry 4.6.7 |
| SQLite | Built-in `bun:sqlite` | SQLite driver đồng bộ tích hợp Bun | `better-sqlite3`; `node:sqlite` | Bun-specific; extensions/build/migration cần xác minh | Bun docs xác nhận module tích hợp; không cần npm package nếu dùng |
| Search | Binary upstream `rg` | Text search qua CLI/process | `@vscode/ripgrep`; grep | Cần phân phối binary cross-platform | Needs verification — wrapper registry 1.18.0 |
| Parsing | `web-tree-sitter` hoặc binding phù hợp | Incremental parsing/syntax tree | Native Tree-sitter bindings | WASM/native, grammar ABI và Bun path cần xác minh | Needs verification — web binding registry 0.27.2; chưa chọn binding |
| LSP client | `vscode-jsonrpc` + `vscode-languageserver-protocol` | JSON-RPC framing và LSP protocol types/client messages | Tự triển khai phần protocol tối thiểu | Hannes cần client; `vscode-languageserver` là server library, không đề xuất cho client; Bun streams/types cần kiểm tra | Needs verification — registry versions 10.0.0 / 3.18.5 |
| LSP server (nếu cần) | `vscode-languageserver` | Xây dựng Language Server Protocol server | Tự triển khai nhỏ | Không thay cho LSP client candidate ở dòng trên; Bun support chưa kiểm tra | Needs verification — registry 11.0.0 |
| Browser automation | `playwright` | Browser automation, e2e/control | WebDriver/BiDi | Upstream v1.65.0 nói CI test runner trên Bun 1.4.3; dùng `--bun`, một số hành vi khác Node | Bun runner được ghi nhận upstream; feature set Hannes cần xác minh |
| Telemetry API | `@opentelemetry/api` | Instrumentation API | Vendor API | Cần SDK/exporter riêng; Bun support chưa xác minh | Needs verification — registry 1.10.0 |
| Unit test | Bun built-in `bun:test` | Test runner tích hợp | Vitest | Cần kiểm tra coverage/report/integration cần thiết | API có trong Bun docs; nhu cầu tính năng cần xác minh |
| Unit test alternative | `vitest` | Test runner/Vite ecosystem | `bun:test` | Có thể dùng Bun, không bảo đảm mọi feature; coverage có giới hạn | Needs verification — registry 5.1.0; docs có Bun |
| Property testing | `fast-check` | Sinh input, kiểm tra invariant | Test thủ công; jsverify | Runner integration và seed reproducibility | Needs verification — registry 4.10.2 |
| “9Router” identity | Chưa đề xuất package | Có thể là project/router/gateway | Yêu cầu URL/repository chính xác | npm `9router`, GitHub org `9router`, repo `decolua/9router` không mặc định là một | Needs verification — npm 0.5.119; danh tính chưa rõ |

## Phiên bản registry quan sát

Tra trực tiếp npm registry `dist-tags.latest` ngày 2026-11-13, ngoại trừ các package MCP v2 và LSP protocol được xác minh thêm ngày 2026-11-14. Link package mở metadata registry trực tiếp; version quan sát không phải pin.

| Package | Latest quan sát | Ghi chú |
|---|---:|---|
| [`bun`](https://registry.npmjs.org/bun) | 1.4.4 | npm |
| [`@opentui/core`](https://registry.npmjs.org/%40opentui%2fcore) | 0.8.4 | npm |
| [`ai`](https://registry.npmjs.org/ai) | 7.0.172 | npm |
| [`@ai-sdk/openai-compatible`](https://registry.npmjs.org/%40ai-sdk%2fopenai-compatible) | 3.0.92 | npm |
| [`@modelcontextprotocol/sdk`](https://registry.npmjs.org/%40modelcontextprotocol%2fsdk) | 1.32.2 | v1; v2 dùng package names mới |
| [`@modelcontextprotocol/server`](https://registry.npmjs.org/%40modelcontextprotocol%2fserver) | 2.4.0 | npm latest checked 2026-11-14; upstream tag v2.4.0 |
| [`@modelcontextprotocol/client`](https://registry.npmjs.org/%40modelcontextprotocol%2fclient) | 2.4.0 | npm latest checked 2026-11-14; upstream tag v2.4.0 |
| [`@modelcontextprotocol/core`](https://registry.npmjs.org/%40modelcontextprotocol%2fcore) | 2.4.0 | npm latest checked 2026-11-14; upstream tag v2.4.0 |
| [`@opencode/plugin`](https://registry.npmjs.org/%40opencode%2fplugin) | 2.3.0 | npm; upstream metadata snapshot 2.2.0, version drift |
| [`@deepseek-ai/cordis`](https://registry.npmjs.org/%40deepseek-ai%2fcordis) | 4.1.0 | npm |
| [`cordis`](https://registry.npmjs.org/cordis) | 4.0.0-rc.13 | prerelease |
| [`hono`](https://registry.npmjs.org/hono) | 4.13.18 | npm |
| [`zod`](https://registry.npmjs.org/zod) | 4.6.7 | npm |
| [`better-sqlite3`](https://registry.npmjs.org/better-sqlite3) | 13.1.1 | alternative |
| [`@vscode/ripgrep`](https://registry.npmjs.org/%40vscode%2fripgrep) | 1.18.0 | wrapper alternative |
| [`web-tree-sitter`](https://registry.npmjs.org/web-tree-sitter) | 0.27.2 | candidate |
| [`vscode-languageserver`](https://registry.npmjs.org/vscode-languageserver) | 11.0.0 | server package |
| [`vscode-jsonrpc`](https://registry.npmjs.org/vscode-jsonrpc) | 10.0.0 | LSP client framing |
| [`vscode-languageserver-protocol`](https://registry.npmjs.org/vscode-languageserver-protocol) | 3.18.5 | LSP protocol/client types |
| [`playwright`](https://registry.npmjs.org/playwright) | 1.65.0 | npm |
| [`@opentelemetry/api`](https://registry.npmjs.org/%40opentelemetry%2fapi) | 1.10.0 | API only |
| [`vitest`](https://registry.npmjs.org/vitest) | 5.1.0 | npm |
| [`fast-check`](https://registry.npmjs.org/fast-check) | 4.10.2 | npm |
| [`9router`](https://registry.npmjs.org/9router) | 0.5.119 | danh tính chưa xác định |
| [`typescript`](https://registry.npmjs.org/typescript) | 7.0.2 | thêm registry check 2026-11-14 |

## Nguồn chính

| Chủ đề | Nguồn |
|---|---|
| Bun, SQLite, test | [Bun runtime](https://bun.sh/docs/runtime), [SQLite](https://bun.sh/docs/runtime/sqlite), [bun:test](https://bun.sh/docs/test), [npm bun](https://www.npmjs.com/package/bun) |
| OpenTUI | [Upstream](https://github.com/anomalyco/opentui), [npm package](https://www.npmjs.com/package/@opentui/core) |
| Vercel AI SDK | [Provider management](https://ai-sdk.dev/docs/ai-sdk-core/provider-management), [OpenAI-compatible providers](https://ai-sdk.dev/providers/openai-compatible-providers), [SDK source docs](https://github.com/vercel/ai/blob/main/content/providers/04-openai-compatible-providers/index.mdx) |
| MCP SDK v2 | [Official repo](https://github.com/modelcontextprotocol/typescript-sdk), [v2 server](https://github.com/modelcontextprotocol/typescript-sdk/tree/main/packages/server), [v2.4.0 release](https://github.com/modelcontextprotocol/typescript-sdk/releases/tag/v2.4.0) |
| OpenCode | [Plugin build docs](https://opencode.ai/docs/build/plugins/), [upstream plugin package](https://github.com/anomalyco/opencode/tree/dev/packages/plugin) |
| Cordis | [DeepSeek package](https://www.npmjs.com/package/@deepseek-ai/cordis), [DeepSeek harness note](https://github.com/deepseek-ai/deepseek-harness/blob/master/vendor/README.md), [Upstream Cordis](https://github.com/cordiverse/cordis) |
| Hono / Zod | [Hono](https://hono.dev/docs/), [Zod](https://zod.dev/) |
| SQLite | [Bun SQLite](https://bun.sh/docs/runtime/sqlite), [better-sqlite3](https://github.com/WiseLibs/better-sqlite3) |
| ripgrep | [Upstream](https://github.com/BurntSushi/ripgrep), [npm wrapper](https://www.npmjs.com/package/@vscode/ripgrep) |
| Tree-sitter | [Official docs](https://tree-sitter.github.io/tree-sitter/), [web binding](https://www.npmjs.com/package/web-tree-sitter) |
| LSP client / JSON-RPC | [vscode-jsonrpc](https://github.com/microsoft/vscode-languageserver-node/tree/main/jsonrpc), [vscode-languageserver-protocol](https://github.com/microsoft/vscode-languageserver-node/tree/main/protocol), [upstream repository](https://github.com/microsoft/vscode-languageserver-node) |
| Playwright / Bun | [v1.65.0 release](https://github.com/microsoft/playwright/releases/tag/v1.65.0), [Bun discussion](https://github.com/microsoft/playwright/issues/38095) |
| OpenTelemetry | [OpenTelemetry JS](https://github.com/open-telemetry/opentelemetry-js), [API npm](https://www.npmjs.com/package/@opentelemetry/api) |
| Vitest / fast-check | [Vitest guide](https://vitest.dev/guide/), [coverage](https://vitest.dev/guide/coverage), [fast-check](https://fast-check.dev/docs/introduction/) |
| 9Router | [npm](https://www.npmjs.com/package/9router), [GitHub org](https://github.com/9router), [decolua/9router](https://github.com/decolua/9router) |

**Cần xác minh tiếp:** OS/đóng gói; Bun support riêng cho LSP/JSON-RPC, exporters, parser và TUI; MCP transport/auth; nhu cầu OpenCode plugin; Cordis fork; ý nghĩa “9Router”; compiler/test toolchain. Không có dependency nào được pin ở đây.
