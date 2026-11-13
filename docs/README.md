# Điều hướng đặc tả Hannes

**Documentation-only, Phase 0. Tất cả tính năng: Not implemented.** Source placeholders không có declarations hay implementation. Đọc [quy tắc agents](../AGENTS.md) trước khi thay đổi repository. Nghiên cứu thực hiện 2026-11-13; tổng hợp/quality gate 2026-11-14. Không install/build/lint/test/start application trong Phase 0.

## Đường đọc cho người triển khai

1. [Vision](product/vision.md), [feature parity](product/feature-parity.md), [glossary](product/glossary.md).
2. [System overview](architecture/system-overview.md), [repository map từng file](architecture/repository-map.md), [dependency rules](architecture/dependency-rules.md).
3. Đọc toàn bộ contracts trước sửa code; chọn subsystem trong bảng dưới.
4. [Process model](architecture/process-model.md), [event flow](architecture/event-flow.md), [configuration/profiles](architecture/configuration-and-profiles.md).
5. [Roadmap/gates](delivery/implementation-roadmap.md), [test strategy](delivery/test-strategy.md), [threat model](delivery/threat-model.md), [dependency catalog](delivery/dependency-catalog.md).

## Chọn contract và subsystem

| Module / công việc | Contract | Đặc tả subsystem / bổ sung |
|---|---|---|
| contracts; core agent engine/loop/step | [events](contracts/agent-events.md) | [agent runtime](subsystems/agent-runtime.md) |
| core context/builder/compaction và memory | [model](contracts/model-provider.md), [sessions](contracts/sessions-and-storage.md) | [context/memory](subsystems/context-and-memory.md) |
| models; provider/router/9Router/usage | [model provider](contracts/model-provider.md) | [runtime](subsystems/agent-runtime.md), [catalog](delivery/dependency-catalog.md) |
| tools; workspace | [tools/permissions](contracts/tools-and-permissions.md) | [tools/workspace](subsystems/tools-and-workspace.md) |
| code-intel | [tools](contracts/tools-and-permissions.md) | [code intelligence](subsystems/code-intelligence.md) |
| mcp | [tools](contracts/tools-and-permissions.md), [events](contracts/agent-events.md) | [integration](subsystems/mcp-integration.md), [compatibility](compatibility/mcp.md) |
| extensions/skills/hooks | [plugins/hooks](contracts/plugins-and-hooks.md) | [plugins/skills](subsystems/plugins-and-skills.md) |
| compat-opencode | [plugins/hooks](contracts/plugins-and-hooks.md) | [V1/V2](compatibility/opencode-v1-v2.md), [TUI/GUI/RPC](compatibility/opencode-tui-gui-rpc.md) |
| compat-deepseek | [plugins/hooks](contracts/plugins-and-hooks.md) | [Cordis A/B/hybrid](compatibility/deepseek-cordis.md) |
| agents | [events](contracts/agent-events.md), [permissions](contracts/tools-and-permissions.md) | [subagents](subsystems/subagents.md) |
| security | [tools/permissions](contracts/tools-and-permissions.md) | [security](subsystems/security.md), [threat model](delivery/threat-model.md) |
| persistence | [sessions/storage](contracts/sessions-and-storage.md) | [persistence](subsystems/persistence.md) |
| apps CLI/TUI/server; SDK; future web/desktop | [events](contracts/agent-events.md), [sessions](contracts/sessions-and-storage.md) | [interfaces](subsystems/interfaces.md), [processes](architecture/process-model.md) |
| observability (roots/security/core, không package riêng) | [events](contracts/agent-events.md) | [observability](subsystems/observability.md) |
| testkit/tests/examples | Các contracts tương ứng | [test strategy](delivery/test-strategy.md), [matrix](compatibility/compatibility-matrix.md) |
| native | Không domain contract mới mặc định | [runtime ADR](adr/0001-typescript-bun.md) |

## Architecture Decision Records

- [0001 TypeScript/Bun và điều kiện Rust](adr/0001-typescript-bun.md).
- [0002 Contracts-first monorepo và composition roots](adr/0002-contracts-first.md).
- [0003 Adapters, host-specific compatibility, Cordis decision gate](adr/0003-plugin-compatibility.md).
- [0004 Security enforcement/trust boundaries](adr/0004-security-boundary.md).

## Nguồn khảo sát và giới hạn bằng chứng

Các URL/paths được nghiên cứu read-only. Snapshot code/docs là evidence về nguồn tham khảo, không chứng minh Hannes hoạt động hoặc upstream deployments có behavior bất biến. Main agent tổng hợp quyết định từ readers; không sao chép nguyên cấu trúc repository nào.

| Nguồn yêu cầu | Revision / paths đã khảo sát | Kết luận dùng trong thiết kế |
|---|---|---|
| [OpenCode](https://github.com/anomalyco/opencode) | `f6e0c2d0833ffe772035f221fed923e1cd5fb408`; packages/, packages/plugin/src/promise/plugin.ts, packages/plugin/src/effect/plugin.ts, packages/schema/src/config/plugin.ts | Separation contracts/protocol/core/interfaces; Hannes giữ baseline riêng; [ADR 0002](adr/0002-contracts-first.md) |
| [Codex CLI](https://github.com/openai/codex) | `69231e119bfca0b14453515e97d99688ce1c47c9`; [core/config](https://github.com/openai/codex/blob/69231e119bfca0b14453515e97d99688ce1c47c9/codex-rs/core/src/config/mod.rs), [protocol models](https://github.com/openai/codex/blob/69231e119bfca0b14453515e97d99688ce1c47c9/codex-rs/protocol/src/models.rs), [session](https://github.com/openai/codex/blob/69231e119bfca0b14453515e97d99688ce1c47c9/codex-rs/core/src/session/mod.rs) | Tách policy/execution/state, không coi internal protocol là public plugin ABI; [ADR 0004](adr/0004-security-boundary.md) |
| [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) | `39163dd30f703a79079e2d2446545cff3c453738`; docs/cordis-primer.md, vendor/README.md, packages/boot/app-boot/README.md, packages/boot/hmr/README.md, apps/cli/reference/README.md | Vendored Cordis, reversible effects, DI, profiles; A/B phải kiểm chứng; [analysis](compatibility/deepseek-cordis.md) |
| [Goose](https://github.com/aaif-goose/goose) | `73b0f040a81369b4f0654ef64074cd3cd654b1da`; [README](https://github.com/aaif-goose/goose/blob/73b0f040a81369b4f0654ef64074cd3cd654b1da/README.md), [crates/goose-agent/README.md](https://github.com/aaif-goose/goose/blob/73b0f040a81369b4f0654ef64074cd3cd654b1da/crates/goose-agent/README.md) | Headless machinery/host interfaces, persisted approval trước UI; cancel không undo external actions |
| [OpenCode Plugin API](https://opencode.ai/docs/build/plugins/) | migrate-v1/, server/, cli/, gui/, rpc/; dynamic docs snapshot 2026-11-13 | V1/V2 và host parts không tương đương; [analysis](compatibility/opencode-v1-v2.md), [UI/RPC](compatibility/opencode-tui-gui-rpc.md) |
| [MCP SDK v2](https://ts.sdk.modelcontextprotocol.io/v2/) | [v2.4.0 docs](https://github.com/modelcontextprotocol/typescript-sdk/tree/v2.4.0/docs), migration/upgrade-to-v2.md, clients/oauth.md | Official SDK, split packages, protocol negotiation và OAuth app-owned state check; [analysis](compatibility/mcp.md) |

Tất cả hàng truy cập 2026-11-13; nghiên cứu current-library docs dùng Context7 bổ trợ GitHub/docs/registry. [Catalog](delivery/dependency-catalog.md) ghi observed versions và primary URLs; observed version không phải selected pin. Exact 9Router identity, npm-vendor Cordis differences, Bun interoperability, RPC framing và GUI host parity còn Needs verification.

## Phase 0 quality gate

Kiểm kê toàn cây và map đúng 118 source placeholders; kiểm 0 byte cho source/.gitkeep, Markdown có nội dung và links nội bộ tồn tại; kiểm không có manifests/configs/lockfiles/dependencies/binaries/artifacts/implementation. Nội dung từng subsystem có 13 mục; contracts có fields/invariants/versioning; compatibility rows đều Not implemented. Đây là kiểm tra tài liệu/filesystem, không test suite. Không tiếp tục Phase 1 sau scaffold.

Kết quả kiểm kê 2026-11-14: **77 thư mục con (78 nếu tính root), 118 source files 0 byte, 13 .gitkeep 0 byte, 41 Markdown có nội dung; tổng 172 files**. Không phát hiện link nội bộ trỏ tới file thiếu, file ngoài loại được phép hoặc credential pattern trong Markdown. Mỗi subsystem có đủ 13 mục; repository-map bao phủ đúng tập 118 source files. Không thực thi build/lint/tests/application hay cài packages; kết quả này chỉ xác nhận scaffold và documentation, không xác nhận runtime behavior.
