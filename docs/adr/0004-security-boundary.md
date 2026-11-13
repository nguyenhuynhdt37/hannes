# ADR 0004: Runtime, plugins và MCP security boundary

Status: Accepted for architecture planning; implementation Not implemented. Ngày quyết định: 2026-11-14. Những lựa chọn ghi rõ deferred chưa được chốt.

## Context

Model/repository/plugin/MCP inputs đều có thể bị attacker điều khiển. [Codex permission configuration](https://github.com/openai/codex/blob/69231e119bfca0b14453515e97d99688ce1c47c9/codex-rs/core/src/config/mod.rs) cho thấy approval/sandbox policy layering. [Goose agent design](https://github.com/aaif-goose/goose/blob/73b0f040a81369b4f0654ef64074cd3cd654b1da/crates/goose-agent/README.md) tách approval và chỉ chạy executable calls, đồng thời nêu cancellation không undo external effects. Nguồn truy cập 2026-11-13; không suy ra private enforcement.

## Decision

Security là policy owner; dispatcher/executors bắt buộc enforce normalized targets, args-bound approvals và lease revocation trước effects. Workspace rechecks path/revision sát IO. MCP/plugins/sidecar/subagents không có đường bypass. Process boundary chỉ failure isolation; untrusted JS in-process unsupported cho tới isolation verified. Secrets qua references/vault, audit redacted.

## Alternatives

Trust mọi plugin hoặc server theo config đơn giản nhưng không an toàn; sandbox-only không thay semantic approval; UI-only approval bỏ qua headless/remote paths; phân tán permission logic trong adapters tạo inconsistency.

## Consequences

Minimum permission gate có ngay Phase 1, không đợi Phase 2 UI hoặc Phase 8 sandbox. In-process plugins chỉ trusted reviewed packages. Deny thắng, Ask pause, unsupported enforcement fail closed. Persistence intents/unknown outcomes và audit failures thành first-class design.

## Risks

TOCTOU và OS sandbox differences, remote side effects không undo được, revocation có thể đến sau syscall, redaction không tuyệt đối. Không hứa exactly-once shell hoặc process isolation tương đương sandbox.

## Revisit Conditions

Thêm executor/host/remote topology, grant persistence semantics, OS backend hoặc secret/audit store; phát hiện bypass/escape; đổi trust model. Bắt buộc threat-model update, adversarial tests và scoped rollout evidence trước enable.
