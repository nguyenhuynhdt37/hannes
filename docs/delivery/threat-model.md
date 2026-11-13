# Threat model

**Thiết kế Phase 0; Not implemented.** Assets: source/workspace và Git history, model/MCP credentials, session/private memory, OS/network permissions, audit evidence và user approvals. Trust boundaries: model output → core; repository/skills → context; clients → authenticated server; plugin → host; MCP server → client; tool executor → OS; SQLite/artifacts → authorized reads. Process boundary riêng không tự là security sandbox.

| Threat / attacker-controlled input | Attack | Mitigation và owner | Negative test / residual risk |
|---|---|---|---|
| Repository paths | ../, absolute path, symlink/junction escape | workspace canonical scopes + security policy + handle/OS enforcement | Swap symlink sau approval; TOCTOU vẫn cần backend tests |
| Concurrent editor/tool | Target đổi sau approval | Revision/hash bind và recheck sát execute; write locks | Two writers; external process không obey lock nên recheck |
| Model/prompt injection | README/tool result yêu cầu đọc secrets/run shell | Instruction hierarchy, untrusted provenance, mandatory permission | Malicious retrieved text; model vẫn có thể đề xuất nguy hiểm, gate phải chặn |
| Shell arguments/env/cwd | Substitution/redirection/pipeline exfiltration | Explicit argv, env allowlist, command digest, sandbox ceiling | Prefix allow bypass; parser không đủ đảm bảo safety |
| Untrusted npm plugin | Import-time code đọc OS/network | Review/provenance/integrity, no scripts mặc định, trusted-only in-process | Ambient API exploit; capability proxies không sandbox JS |
| Plugin lifecycle | Leaked hooks/stale service grants | Generation ledger, rollback/dispose, revocation recheck | Reload race, disposer fail; external effect có thể không undo |
| Local MCP executable | Malicious stdio process/env leak | Trust binary/cwd/env, scoped tool grants, OS isolation nếu có | stderr secrets/flood; separate process vẫn có ambient quyền |
| Remote MCP | Forged schema/result, tool exfiltration | Identity/schema pin, permission mapping, result bounds/provenance | Server changes tool after grant; remote action cannot rollback |
| Remote URLs/OAuth | SSRF, issuer mix-up, callback/state theft | URL/origin policy, PKCE/state single-use, issuer/resource bound vault | Loopback/private metadata fetch, bad redirect, wrong state |
| Credentials | Logs/env/session export leakage | Vault refs, redaction trước sink, least-scope token, revoke/rotation | Raw errors/child output leak; scanner false negatives |
| Forged approval | UI sends wrong principal/hash or stale approval | Authenticated command, scopeHash/policyRevision/expiry, CAS | Replay/revoke race; long-running effects may finish after revoke |
| Remote client | Read other session/workspace, event subscribe leakage | Per-command/subscription authorization, tenant scope, TLS/auth | Guess IDs, stale reconnect token; deployment model chưa chọn |
| Tool output / terminal | Escape/control sequences, huge binary payload | TUI sanitization, content/artifact bounds/backpressure | OSC/control injection, memory flood |
| Storage corruption/crash | Missing receipt causes replay effects | Transactional intent, checkpoints, fencing, unknown state | Crash after remote write; manual reconcile may be necessary |
| Subagent | Privilege/budget escalation, context leak | Intersection grants, isolated snapshots, atomic budget reservations | Recursive fanout, parent revoke, shared worktree races |
| Audit/telemetry | Secret export or forged evidence | Redaction/access/retention, required audit gate vs optional telemetry | Exporter down; append-only table not admin-tamper-proof |

Permission model: deny ceilings thắng, Allow có exact scope và expiry, Ask pause khi thiếu grant. Persistent permission phải thể hiện principal/workspace/target/policy revision; revoke invalidates queued approvals và cached leases, propagates tới children/connections. Không tự chuyển Ask thành Allow trong headless hay khi UI disconnect. Safe path resolution phải áp read cũng như write; network và remote execution có policies riêng.

Sandbox thiết kế theo supported OS: filesystem/network/process constraints phải được backend xác nhận enforce được; unsupported fail closed hoặc capability disabled công khai. Không tuyên bố container/process/worker mặc định chống escape; threat assumptions và residual risks cần platform evidence. Untrusted in-process plugins không được enable dựa vào API wrapper.

Security acceptance gate: từng row có negative/fault test và owner; tất cả side-effect paths (builtin/MCP/plugin/sidecar/subagent/recovery) phải truy vết qua cùng enforcement. Tool intent và approval durable trước risky execution; unknown outcome không auto retry. Secrets không xuất trong repository artifacts, session exports hoặc telemetry fixtures. Incident recovery gồm revoke grants/tokens, quarantine extension/server, fence runs và preserve redacted audit.

Open decisions: OS sandbox backend, vault integration, remote multi-tenant model, encrypted storage và tamper-evident audit. [ADR 0004](../adr/0004-security-boundary.md), [security subsystem](../subsystems/security.md) và [test strategy](test-strategy.md) quản lý các gate. Đây không phải security certification.
