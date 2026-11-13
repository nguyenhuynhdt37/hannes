# Agent events và commands

**Thiết kế contract v1; Not implemented.** Public wire format dự kiến JSON-compatible; IDs là opaque strings, thời gian UTC RFC3339, số sequence là integer không âm an toàn hoặc decimal string nếu vượt giới hạn. Không truyền Error/function/SDK instances. Bảng này là schema mô tả, chưa có TypeScript declarations.

| Envelope field | Bắt buộc | Invariant |
|---|---|---|
| contractVersion | Có | Major/minor; reader từ chối major lạ |
| eventId, type | Có | ID duy nhất; type phân biệt payload |
| sessionId, sessionSeq | Durable | Seq tăng đơn điệu theo session, unique pair |
| runId, stepId, attemptId | Theo phạm vi | Attempt thuộc đúng step/run |
| correlationId, causationId | Có / khi có parent | Truy vết, không dùng thay authorization |
| emittedAt | Có | Không dùng sắp thứ tự |
| payload | Có | Validate theo type/version; giới hạn bytes |
| streamId, streamSeq | Delta | Tăng trong một attempt; không có sessionSeq giả |

| Type | Payload chính | Trạng thái / nghĩa |
|---|---|---|
| session.created / message.accepted | workspaceId, messageId, role, contentRefs, revision | Message durable trước model request |
| run.started | mode, configHash, budget, parentRunId? | idle → preparing |
| step.started / model.requested | contextHash, modelRef, attemptId | preparing → streaming |
| model.delta | kind(text/reasoning/tool-fragment), callId?, fragment | Ephemeral; reasoning có policy riêng |
| model.completed | finishReason, normalizedCalls, usage, responseRef | Chỉ complete call mới dispatch |
| tool.requested | invocationId, descriptorVersion, inputHash, target | validated request |
| approval.requested / resolved | approvalId, scopeHash, expiry, policyRevision / decision, principal | waiting_approval → runnable/denied |
| tool.started | invocationId, leaseId, targetRevision | Intent durable trước side effect |
| tool.completed / failed | invocationId, resultRef / error, outcomeCertainty | Một logical outcome; unknown cần reconcile |
| context.compacted | sourceRange, summaryRef, tokenEstimate, provenance | Không xóa bản gốc |
| run.cancel_requested | reason, requestedBy | nonterminal → cancelling |
| run.completed / failed / cancelled | finalMessageRef?, stopReason, usage, unresolvedInvocations | Terminal một lần |
| capability.changed / extension.failed | registryGeneration, capabilityId / error | Không đổi descriptor đang chạy |

Commands: SubmitMessage(commandId, sessionId, expectedRevision, content), CancelRun(commandId, runId), ResolveApproval(commandId, approvalId, scopeHash, decision), ResumeSession(commandId, checkpointId, expectedRevision). Server deduplicate commandId theo principal/session, trả cùng acknowledgement; payload khác cho cùng key là conflict. Unauthorized command không tạo domain action.

Error record: code, category(validation/auth/policy/provider/tool/storage/transport/cancel), safeMessage, retryable, outcomeCertainty(none/completed/unknown), diagnosticRef. Không nhét stack/secret/raw headers vào public event. Missing seq làm client resync; unknown minor event được bỏ qua chỉ nếu optional, unknown command bị reject.

Versioning: thêm optional field/minor event phải có default và negotiation; đổi semantics/required field cần major, ADR và migration. Store append cùng state revision trong transaction; publisher sau commit. At-least-once delivery, consumer dedup eventId; không hứa exactly-once side effects. Replay durable events không gọi lại executor.

Acceptance: duplicate Submit không tạo run thứ hai; concurrent terminal CAS thắng một; dropped deltas không phá durable replay; invalid schema bị chặn trước state mutation; restart không emit lại side effects. Xem [event flow](../architecture/event-flow.md).
