# Persistence và recovery

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Lưu session/events/checkpoints bền, cung cấp replay/read views và reconcile sau crash mà không replay tool side effects.

## 2. Trách nhiệm sở hữu

persistence sở hữu SQLite connection lifecycle/schema/migrations, session/event stores, checkpoints, artifact metadata và transactional writes. Core sở hữu domain transitions, security sở hữu audit content policy.

## 3. Ngoài trách nhiệm

Không ghi filesystem source, không own event ordering decisions của core, không làm approval UI, không serialize live plugin/model handles.

## 4. Input, output và dữ liệu sở hữu

Input transactions với expectedRevision/writerEpoch và records theo contracts. Output committed revision/seq, snapshots/replay streams hoặc conflict/storage errors. Own durable entities và schemaVersion; sensitive artifact content retention tách metadata.

## 5. Xử lý và lifecycle

Open → acquire migration lock → verify schema/backup compatibility → configure validated journal settings → recover interrupted runs → grant writer lease → serve transactions. Commit event+state+intent atomically; publish after commit. Checkpoint references durable prefix và workspace revision; shutdown flush then release.

## 6. Dependencies và interface boundaries

Chỉ contracts. StorePort, EventAppendPort và AuditSink adapters inject; no core imports. SQLite API choice Bun built-in vs library qua catalog, chưa có schema/config implementation.

## 7. Luồng sự kiện

AppendRequested → TransactionCommitted → EventPublished; crash giữa commit/publish cho replay từ cursor. RecoveryFoundInterruptedRun → InvocationReconciled/OutcomeUnknown → ResumeReady. Không rebuild read models bằng cách gọi tools.

## 8. Errors, cancellation và recovery

Disk full/corrupt DB/unsupported newer schema dừng writes với diagnostics; không tạo DB trống che dữ liệu mất. Restore backup explicit. Crash sau tool start trước result giữ unknown; user/reconciler quyết định bằng receipts. Cancel transaction rollback trước commit, sau commit trả committed status.

## 9. Concurrency, races và timeout

SQLite single-writer bounded queue, busy timeout hữu hạn, per-session CAS và writerEpoch fencing. Reader snapshots không giữ transaction quá lâu; checkpoint/GC không xóa artifacts đang referenced. Filesystem và SQLite không chung atomic boundary nên receipts bắt buộc.

## 10. Security considerations

DB chứa code/prompts nhạy cảm: OS file permissions, retention/delete/export controls, authorization tại read API. Không coi SQLite mặc định encrypted. Tokens dùng vault references. Audit redaction và restricted access, prompt content không log trùng nhiều nơi.

## 11. Thư viện dự kiến và alternatives

SQLite qua Bun candidate; alternative better-sqlite3 cần verify native ABI/Bun hoặc external service nếu remote scale. WAL suitability trên network filesystem chưa chốt; default không đặt shared DB trên network FS.

## 12. Acceptance criteria và test cases

Cases: crash before/after commit, duplicate event append, writer lease lost, disk full lúc persist approval/result, migration interruption, corrupt checkpoint, GC references, concurrent resume. Pass: no duplicated side effects, replay consistent prefix, unknown effects hiện rõ.

## 13. Quyết định mở và rủi ro

Chưa chốt migrations tool, encryption, artifact location, backup schedule, audit retention và WAL tuning. Test power-loss/fault injection ở phase triển khai; Phase 0 không chạy test.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
