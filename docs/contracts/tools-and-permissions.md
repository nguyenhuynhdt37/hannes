# Tools và permission contracts

**Đề xuất v1; Not implemented.** Allow/Ask/Deny là quyết định policy, không là nhãn do tool/plugin tự khai. Dispatcher là enforcement point, security là decision owner; executor không nhận raw bypass path.

| Record | Fields | Invariants |
|---|---|---|
| ToolDescriptor | namespace, name, version, inputSchema, outputSchema?, effects, concurrencyClass, supportsCancel, timeoutClass | Registry generation cố định cho invocation |
| ToolInvocation | invocationId, sessionId, runId, toolRef, args, inputHash, target, expectedRevision, deadline | Schema validation trước permission; IDs không tái sử dụng payload khác |
| PermissionRequest | principal, capability, canonicalTargets, networkOrigin?, commandDigest?, inputHash, policyRevision | Targets được normalize, không chỉ raw strings |
| Decision | allow/ask/deny, reasonCode, constraints, approvalId?, expiresAt? | Deny thắng; ask không chạy tool |
| Lease | leaseId, principal, scopeHash, targetRevision, policyRevision, expiresAt, remainingUses | Không transferable; revoke áp dụng tại execution |
| ToolResult | invocationId, status, contentParts, artifactRefs, effectsReceipt, verified, error? | Output validated và bounded; model text không xác minh effects |

Capability scopes: fs.read/write theo canonical root/path; process.execute theo executable/args/env/cwd; network.connect theo origin; mcp.call theo server identity/tool/schema version; extension.service theo method. Shell string phức tạp không được allow bằng prefix mơ hồ; pipeline/redirection/substitution có thể phát sinh effects vượt parser, cần Ask hoặc sandbox ceiling.

Approval states: pending → approved/denied/expired/revoked; resolved chỉ một lần. Approval gắn exact args hash + target + policy revision + principal + descriptor version; thay bất kỳ thành phần nhạy cảm nào phải đánh giá lại. Persist trước hiển thị UI; expired/revoked không revive qua replay. Session-wide grants chỉ khi scope rõ và người dùng chủ động chọn; không lưu blanket shell allow ngầm.

Execution states: proposed → validated → awaiting_permission → runnable → started → succeeded/failed/cancelled/outcome_unknown. Persist started intent trước execution, result sau verification. Write kiểm expected file version; atomic replace khi khả thi; symlink và cwd recheck sát syscall. Check-then-use bằng string path không đủ chống race; OS handle-based controls/sandbox cần feasibility test.

Concurrency: reads trên snapshot có thể song song, conflicting writes khóa theo canonical target và session workspace lease; approval wait không giữ filesystem lock vô hạn. Timeout/cancel có partial effects: receipt ghi file revisions/process IDs/remote request IDs khi có. Không auto-retry non-idempotent tools nếu outcome unknown.

Output vượt giới hạn chuyển artifact reference sau redaction; binary không nhét vào prompt. Untrusted content được gắn nguồn; không cho output tool tự cấp permission. Error codes tối thiểu: INVALID_INPUT, DENIED, APPROVAL_EXPIRED, TARGET_CHANGED, TIMEOUT, CANCELLED, EXECUTION_FAILED, OUTCOME_UNKNOWN.

Acceptance: symlink swap, traversal, duplicate invocation, revoke giữa approve/execute, two writers, shell escape, forged MCP result, output flooding. Contract changes cần major/ADR khi đổi scope hoặc decision semantics. [Security](../subsystems/security.md) xác định enforcement chi tiết.
