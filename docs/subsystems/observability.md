# Observability và diagnostics

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Giúp truy vết run/model/tool/plugin/MCP, đo latency/usage và chẩn đoán failure mà không rò prompts/secrets.

## 2. Trách nhiệm sở hữu

Core phát domain events; security sở hữu redaction/audit policy; composition roots nối subscribers/exporters; models phát usage/capability diagnostics. Không thêm observability package ngoài baseline.

## 3. Ngoài trách nhiệm

Không dùng telemetry thay durable event log, không thu raw content mặc định và không coi metric là execution receipt. Không làm policy decisions dựa trên best-effort log.

## 4. Input, output và dữ liệu sở hữu

Input events/span lifecycle/usage counters với IDs. Output redacted logs, traces, bounded metrics và audit records. Own ephemeral export buffers; durable audit qua injected store sink. Correlation IDs không chứa user/repo names mặc định.

## 5. Xử lý và lifecycle

Create run span → child model/tool/approval spans → normalize error/usage → redact at producer boundary → bounded queue → configured exporter. Explicit opt-in content capture phải scope/retention riêng; doctor xuất sanitized report. Required audit commit là blocking gate, ordinary telemetry best-effort.

## 6. Dependencies và interface boundaries

Chỉ contracts ở producers; roots nối OpenTelemetry/exporters dự kiến. Security RedactorPort/AuditSink dùng chung, không circular security↔persistence. Public event schemas không phụ thuộc OTel SDK types.

## 7. Luồng sự kiện

RunStarted tạo trace; ModelRequested/Completed và ToolStarted/Completed đóng spans; ApprovalRequested có waiting duration riêng; Run terminal settles usage. Missing completion vì crash tạo interrupted annotation khi recovery, không fake successful span.

## 8. Errors, cancellation và recovery

Exporter lỗi drop theo bounded policy + count dropped, không lặp log vô hạn. Audit failure xử lý fail-closed theo security contract. Cancel/shutdown flush trong deadline, không chờ exporter mãi. Không dùng raw provider error body làm safeMessage.

## 9. Concurrency, races và timeout

Low-cardinality metrics: provider/tool class/status, không runId/path làm label. Traces dùng sampled correlation; audit không sampling risky decisions. Queue backpressure không block model delta render trừ required audit. Export timeout/retry bounded.

## 10. Security considerations

Redact auth headers/tokens/env/paths/content trước disk/network; child stderr có thể chứa credentials. Telemetry remote disabled cho tới configured consent. Audit access controls và tamper resistance chưa chọn; append-only app table không bảo đảm chống admin tamper.

## 11. Thư viện dự kiến và alternatives

OpenTelemetry API/SDK candidates; alternatives structured local logs trước. Không tự tạo collector/deployment. Usage accounting lưu normalized plus estimated flags, không dùng span count để tính tiền.

## 12. Acceptance criteria và test cases

Cases: token trong provider error/header/tool stderr; exporter offline/output flood; nested child correlation; unknown usage không thành 0; audit disk full; crash missing end; high-cardinality guard. Gate: no secrets trong exported payload và bounded memory.

## 13. Quyết định mở và rủi ro

Chưa chốt exporter/sampling/retention và tamper-evident audit backend. Rủi ro redaction false negative và content leakage qua attributes; cần adversarial fixtures trước bật telemetry.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
