# Security và enforcement

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Đưa Allow/Ask/Deny thành enforcement trước mọi side effect, bảo vệ workspace/secrets/network và cho phép audit/revocation.

## 2. Trách nhiệm sở hữu

security sở hữu policies, approvals, sandbox capability negotiation, credential resolution và redaction/audit policy. Executors chịu trách nhiệm thi hành constraints; workspace kiểm paths sát IO.

## 3. Ngoài trách nhiệm

Không tự execute tool, không xác nhận model intent đáng tin, không coi child process là sandbox hoặc shell parser là isolation. Auth transport của server không thay tool authorization.

## 4. Input, output và dữ liệu sở hữu

Input principal, normalized target/action, trust origin, policy snapshot và prior grant. Output decision/lease/reason/audit record. Own policy revisions, approval ledger via store port, secret references; không lưu keys trong transcript.

## 5. Xử lý và lifecycle

Authenticate → establish workspace trust → canonicalize action → apply deny ceilings → check allow scopes → Ask nếu chưa quyết → persist approval → bind exact hash/expiry → recheck revoke/target → enforce sandbox/executor constraints → audit outcome. Không có UI thì Ask trả approval_required và pause, không tự Allow.

## 6. Dependencies và interface boundaries

Chỉ contracts; audit/store/OS keychain ports inject. Tools, MCP, extensions gọi policy API; adapters không tự đổi denied thành allowed. Sandbox backend trả enforced capabilities; nếu không enforce được constraint phải fail closed hoặc explicit unsupported policy, không giả downgrade.

## 7. Luồng sự kiện

PolicyEvaluated → ApprovalRequested/Resolved → LeaseIssued → LeaseConsumed/Revoked → ExecutionAudited. Revocation tăng policy revision, invalidate cached decisions và queued requests. Audit metadata được durable trước risky actions nếu sink bắt buộc.

## 8. Errors, cancellation và recovery

Policy parse lỗi fail startup/chặn affected execution. Secret resolve lỗi không fallback sang env toàn bộ. Audit write failure chặn action với required audit policy. Cancel approval ghi trạng thái, không reuse decision. Unknown tool effects giữ unresolved record.

## 9. Concurrency, races và timeout

Lease validation/consumption CAS; revoke chạy đồng thời phải được kiểm sát execution và abort running tasks khi backend hỗ trợ. TOCTOU filesystem cần handle-based/sandbox strategy; không giữ approval vô thời hạn. Secret cache TTL và zero/log redaction limits phải rõ.

## 10. Security considerations

Chống traversal/symlink, shell/env injection, npm supply chain, MCP SSRF/OAuth redirect abuse, prompt injection và cross-tenant session access. Deny-by-policy ceilings; approval scope không rộng ngầm. Secrets OS vault, env allowlist, sanitized child outputs; encrypted transport. Permission persistence có expiry/revoke và không migrate thành quyền rộng hơn.

## 11. Thư viện dự kiến và alternatives

OS-specific sandbox backend chưa chọn; Bun subprocess và secure storage adapters cần testing. Alternatives containers/VMs cho stronger isolation với cost cao. Không tự viết cryptography/token storage format.

## 12. Acceptance criteria và test cases

Cases theo threat-model: symlink race, malicious shell substitutions, npm ambient access, MCP exfiltration, forged approval principal/hash, revoke race, headless Ask, audit disk full, sandbox unavailable. Gate: không execution khi enforcement chưa đủ.

## 13. Quyết định mở và rủi ro

Chưa chốt OS support matrix, secret vault package, sandbox primitives và org policy distribution. Process isolation chỉ failure boundary; in-process plugins trusted-only cho tới khi có isolation verified.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
