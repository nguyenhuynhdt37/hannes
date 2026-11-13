# ADR 0002: Contracts-first monorepo

Status: Accepted for architecture planning; implementation Not implemented. Ngày quyết định: 2026-11-14. Những lựa chọn ghi rõ deferred chưa được chốt.

## Context

Cần một runtime cho headless/server/TUI và future web/desktop, cùng model/tools/extensions có vòng đời khác nhau. [OpenCode packages](https://github.com/anomalyco/opencode/tree/f6e0c2d0833ffe772035f221fed923e1cd5fb408/packages) thể hiện separation core/schema/protocol/server/clients; đây là cảm hứng, không sao chép tree. Truy cập 2026-11-13.

## Decision

Contracts là tầng thấp nhất, chứa domain records và ports; implementations ở models/tools/security/persistence và adapters. Core chỉ contracts, composition tại apps/cli/server. SDK không embed runtime; TUI chỉ SDK. Dùng đúng baseline packages, không thêm event-bus/observability/config package riêng: ownership được phân định bằng ports và roots.

## Alternatives

Monolithic app ít overhead ban đầu nhưng UI/plugin dễ kéo domain coupling; microservices từ đầu tạo distributed state/security burden; packages theo upstream tree gắn Hannes vào semantics bên ngoài.

## Consequences

Mỗi datum có owner, public entry rõ, không circular kể cả type-only. Các module giao tiếp qua versioned contracts; dependency edges và migration có review. Event bus port trong contracts, sequencer core, durability persistence; observability wiring ở roots.

## Risks

Contracts thiết kế quá sớm có thể overfit; injection tạo nhiều ports; documentation có thể trôi khỏi implementation. Giảm bằng vertical slice Phase 1, contract fixtures và ownership review.

## Revisit Conditions

Có cycle thực sự không giải bằng port, shared data hai owners, transport/domain schema diverge hoặc scaling cần services. Thay public contract/edge/process ownership bắt buộc ADR và migration.
