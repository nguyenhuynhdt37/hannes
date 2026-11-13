# Sessions và storage contracts

**Đề xuất v1; Not implemented.** Persistence là owner mọi SQLite write; core chỉ gọi transaction ports. Không có schema SQL/migration implementation trong Phase 0.

| Entity | Fields dự kiến | Invariant |
|---|---|---|
| Session | sessionId, workspaceId, ownerId, createdAt, revision, status, configHash | Scope workspace/owner bất biến trừ migration rõ |
| Message | messageId, sessionId, role, partsRefs, parentId?, provenance, state | Không sửa message committed âm thầm |
| Run | runId, sessionId, mode, state, writerEpoch, budgets, stopReason? | Một active writer/session; CAS revision |
| Event | eventId, sessionId, sessionSeq, envelope | Append-only; unique sequence |
| Invocation | invocationId, inputHash, targetRevision, state, receiptRef | Một logical command; unknown không replay |
| Checkpoint | checkpointId, sessionSeq, transcriptRef, contextRef, configHash, workspaceSnapshotRef, pendingInvocations | Tham chiếu event prefix đã committed |
| Grant | grantId, scopeHash, policyRevision, expiry, revocation | Secrets không nằm trong grant |
| Artifact | artifactId, mediaType, contentHash, size, retention, owner | Authorization khi đọc, content không mặc định trusted |

Session states: open/archived/deleting/deleted. Run states là runtime contract. Message states: partial/committed/interrupted; partial stream snapshot không giả thành complete message. Run recovery chuyển interrupted/recovery_required, không trực tiếp running trước reconcile.

Atomic unit: append durable events + update materialized session/run revision + invocation metadata trong cùng transaction. Publish sau commit. Checkpoint chỉ tham chiếu complete transaction; workspace filesystem không atomic cùng SQLite, do đó receipt và expectedRevision bắt buộc. SQLite single-writer, WAL dự kiến cần thử Bun/OS; busy timeout hữu hạn, queue bounded; lease epoch ngăn writer cũ sau restart ghi tiếp.

Resume input: sessionId, checkpointId hoặc durable cursor, expectedRevision, principal. Output: canonical transcript, active/unknown invocations, pinned config/profiles, workspace mismatch diagnostics. Không hồi sinh approval đã expired, model stream cũ, plugin handles hoặc credential bytes.

Retention: tách event metadata, prompt/tool content, audit và artifacts; configurable deletion theo owner; tombstone trước GC references. Backups phải mã hóa khi chứa nội dung repository và không lưu tokens. SQLite không tự mã hóa; encrypted DB/OS storage còn cần chọn. Export bỏ secrets và đánh dấu schemaVersion; import là untrusted data và không chạy embedded commands.

Migrations: monotonic schemaVersion, backup/restore và exclusive migration lock, reject newer unsupported DB; không âm thầm downgrade. Durable envelope major khác DB schema version. Rebuild views từ events cần đảm bảo không replay effects.

Acceptance: power-loss giữa intent/effect/result; concurrent resume; migration interruption; disk full trước approval và trước result; artifact GC vẫn giữ checkpoint references; tenant isolation và deletion. Chi tiết [persistence subsystem](../subsystems/persistence.md).
