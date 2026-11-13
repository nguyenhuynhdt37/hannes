# ADR 0003: Compatibility adapters thay fork upstream

Status: Accepted for architecture planning; implementation Not implemented. Ngày quyết định: 2026-11-14. Những lựa chọn ghi rõ deferred chưa được chốt.

## Context

OpenCode V1/V2 có entry/hook/context/host semantics khác; GUI experimental khác server. DeepSeek dùng vendored Cordis và profile/HMR riêng. Nguồn truy cập 2026-11-13: [OpenCode migration](https://opencode.ai/docs/build/plugins/migrate-v1/), [plugin parts](https://opencode.ai/docs/build/plugins/), [Harness vendor](https://github.com/deepseek-ai/deepseek-harness/blob/39163dd30f703a79079e2d2446545cff3c453738/vendor/README.md).

## Decision

Giữ Hannes domain độc lập và dùng compat-opencode/compat-deepseek chuyển APIs qua extension contracts. Công bố support theo capability/version/host/fixture; unsupported explicit. Không fork toàn OpenCode, không claim parity từ import success. Cordis A in-process/B sidecar/hybrid chưa quyết định: Phase 7 inventory + tests rồi ADR cập nhật. Sidecar chỉ dùng supported interface được xác minh.

## Alternatives

Fork toàn OpenCode dễ giữ một snapshot nhưng tăng merge/license/maintenance coupling; chạy upstream hoàn toàn làm mất ownership runtime; generic universal plugin context không giữ timing/lifecycle/privilege semantics.

## Consequences

Phải maintain translation tables, SDK/RPC codecs và differential fixture corpus. TUI cần own host API mapping; GUI cần desktop renderer/main host và tests riêng. Partial compatibility công khai thay vì stubs fake success. Native extensions cũng dùng cùng permission path.

## Risks

Upstream API drift, private deep imports, Effect vs Promise API, vendor fork deltas, lifecycle mismatch và ambient plugin quyền. Sidecar IPC chưa chắc tồn tại/đủ privilege mediation. Hybrid có thể double ownership nếu routing không unique.

## Revisit Conditions

Tests chứng minh adapter không tái hiện critical semantics, supported IPC xuất hiện/thay đổi, host APIs stable hoặc corpus cho thấy nhu cầu mới. Chọn A/B cần evidence về lifecycle fidelity, enforceability, Bun compatibility và maintenance cost.
