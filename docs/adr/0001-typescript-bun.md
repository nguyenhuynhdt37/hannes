# ADR 0001: TypeScript/Bun; Rust optional

Status: Accepted for architecture planning; implementation Not implemented. Ngày quyết định: 2026-11-14. Những lựa chọn ghi rõ deferred chưa được chốt.

## Context

Coding harness cần contracts dùng chung, provider/MCP/plugin ecosystem TypeScript và UI terminal. Bun được user ưu tiên; không có benchmark hay runtime probe trong Phase 0. Nguồn: [Bun](https://bun.sh/docs/runtime), [TypeScript](https://www.typescriptlang.org/docs/), [catalog](../delivery/dependency-catalog.md), truy cập nghiên cứu 2026-11-13.

## Decision

Chọn TypeScript làm ngôn ngữ chính và Bun làm default runtime/package manager cho thiết kế. Chưa pin versions, chưa cài dependencies/config. Bun transpilation không thay typecheck. OpenTUI và native integrations phải được xác minh theo OS. Rust chỉ bổ sung dưới native khi profiling hoặc isolation/native API yêu cầu, qua stable serialization/FFI boundary có ADR.

## Alternatives

Node + pnpm giảm một số rủi ro native ecosystem nhưng khác default đã chọn; full Rust harness mạnh về native nhưng làm plugin/API interop phức tạp; mixed language từ đầu tăng packaging cost chưa có bằng chứng cần thiết.

## Consequences

Ưu tiên shared schemas, SDK và adapters TS. Public contracts không chứa Bun types để có khả năng đổi host. Build/typecheck/test tooling sẽ được chọn trong phase triển khai với configs hợp lệ, không tạo dummy manifests.

## Risks

Bun compatibility cho MCP stdio/Web Crypto, native parser/SQLite/TUI, subprocess và LSP chưa kiểm chứng. Native binaries và prereleases tăng matrix; provider SDK major drift có thể ảnh hưởng.

## Revisit Conditions

Bun không đạt required compatibility probes; đo CPU/memory cho thấy bottleneck không xử lý được bằng TS; cần sandbox OS binding. ADR mới phải mô tả portability, packaging, license và invariants giữ nguyên.
