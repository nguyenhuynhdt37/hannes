# Hannes CLI

Hannes hướng tới một coding agent độc lập: runtime headless, CLI/TUI, tools trong workspace, provider routing và khả năng mở rộng qua MCP, plugins và adapters. Feature parity là mục tiêu kiểm chứng theo capability, không phải tuyên bố tương thích hiện tại.

**Trạng thái: Documentation-only — Phase 0.** Mọi file TypeScript/TSX là placeholder 0 byte. Chưa có application chạy được, dependencies, build configuration hay tests có thể thực thi. Không có lệnh cài đặt hoặc khởi chạy ở giai đoạn này.

Kiến trúc đề xuất: contracts ở đáy; core điều phối qua ports được inject; models/tools/security/persistence thực thi các ports; adapters chuyển đổi giao thức; apps là composition roots. SQLite giữ session và event log. Bun/TypeScript, OpenTUI, AI SDK và MCP SDK chính thức là lựa chọn cần đánh giá phiên bản trước Phase triển khai.

```text
hannes/
├── README.md, AGENTS.md
├── apps/          cli, tui, server; web và desktop dự trữ
├── packages/      contracts, core, models, tools, workspace, mcp,
│                 extensions, compat-opencode, compat-deepseek,
│                 code-intel, security, persistence, agents, sdk, testkit
├── docs/          product, architecture, contracts, subsystems,
│                 compatibility, delivery, adr
├── tests/         contracts, compatibility, security, end-to-end
├── examples/      mcp, opencode-plugin, deepseek-plugin, hannes-plugin
├── native/        dự trữ tối ưu native
├── scripts/       dự trữ
└── .github/workflows/  chỉ .gitkeep
```

Bắt đầu tại [chỉ mục tài liệu](docs/README.md), [kiến trúc](docs/architecture/system-overview.md), [quy tắc phụ thuộc](docs/architecture/dependency-rules.md) và [contracts](docs/contracts/agent-events.md). [Bản đồ repository](docs/architecture/repository-map.md) giải thích từng placeholder. [Nguồn và dependencies](docs/delivery/dependency-catalog.md) ghi nhận bằng chứng nghiên cứu.

[Roadmap](docs/delivery/implementation-roadmap.md): Phase 1 contracts/runtime/9Router/tools; Phase 2 streaming/storage/permissions/TUI; Phase 3 context/recovery/Git/intelligence; Phase 4 MCP; Phase 5 extensions; Phase 6 OpenCode; Phase 7 DeepSeek; Phase 8 subagents/sandbox; Phase 9 interfaces/marketplace/conformance. Phase 0 dừng ở scaffold và tài liệu.
