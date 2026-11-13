# Context và memory

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Xây prompt có provenance và ngân sách token; nén lịch sử có kiểm soát; giữ instructions/memory phù hợp workspace và profile.

## 2. Trách nhiệm sở hữu

core/context/builder sở hữu ordering và token ledger; compaction sở hữu summary lineage/source ranges. Durable summaries lưu qua persistence; code-intel sở hữu chỉ mục, không context cache.

## 3. Ngoài trách nhiệm

Không tự scan toàn filesystem, tự cấp quyền đọc memory, sửa project instructions hay coi vector/index result là nguồn sự thật. Không cung cấp memory shared vô điều kiện giữa agents.

## 4. Input, output và dữ liệu sở hữu

Input: pinned rules/profile, transcript, retrieved snippets, model capability metadata, tool descriptors và budget. Output: immutable context snapshot + hash, token estimate, source revisions và dropped-content diagnostics. Owned: in-run cache và compaction plan.

## 5. Xử lý và lifecycle

Assemble trusted system → explicit user intent → scoped project instructions có nguồn → selected history → retrieved untrusted content → pending tool state. Reserve output/tool overhead trước chọn input. Nếu vượt window: trim low-priority retrieval, compact old completed turns, vẫn vượt thì dừng với diagnostic. Không nén mất pending approval/call IDs, user constraints hay receipts. Summary lưu sourceRange, model/config, hash và uncertainty.

## 6. Dependencies và interface boundaries

Core chỉ gọi contracts ports để đọc instructions/memory/search/store/model. LSP/index reads qua query port và workspace revision. Không import trực tiếp code-intel/persistence trong core. Persistent memory là artifact/session record qua store, chưa cần package mới.

## 7. Luồng sự kiện

ContextPrepared → BudgetEstimated → CompactionRequested → ContextCompacted → ModelRequested. Compaction cũng dùng model budget riêng và có thể fail. Retrieval source revision đi cùng snippet; event compact không xóa raw transcript.

## 8. Errors, cancellation và recovery

Compaction lỗi giữ original history, thử deterministic trimming hoặc báo không đủ context; không âm thầm bỏ instructions. Cancel dừng summary attempt; unfinished summary không commit. Resume invalidate cache nếu model/config/workspace revision thay đổi.

## 9. Concurrency, races và timeout

Cache key gồm principal/workspace, model/tokenizer version, prompt hash, tool schema generation và policy revision; không shared cross-tenant. Token counts ước tính có margin cấu hình; contextLimit unknown dùng safe ceiling đã xác minh. Parallel subagents nhận snapshots riêng; parent merge chỉ vetted summaries, không mutable cache chung.

## 10. Security considerations

Project instructions/skills/retrieved memory là untrusted thấp hơn system policy; provenance phải còn sau compaction. Secrets scan/redact trước đưa prompt/cache; memory write có consent/scope và retention. Cache không trở thành đường đọc dữ liệu ngoài quyền hiện tại.

## 11. Thư viện dự kiến và alternatives

Tokenizer phù hợp model nếu được provider công bố; fallback conservative estimator có calibration. Tree-sitter/ripgrep/LSP chỉ là retrieval sources. Alternative embedding store để sau, chưa thêm dependency/package. Provider prefix cache chỉ dùng khi semantics/usage được xác minh.

## 12. Acceptance criteria và test cases

Cases: window sát giới hạn; non-Latin text estimate; compaction preserve deny/user instruction và call/result pairs; stale snippet; revoked read permission invalidates cache; two agents không thấy private context nhau; malicious README prompt injection không override policy.

## 13. Quyết định mở và rủi ro

Chưa chốt tokenizer 9Router, persistent memory consent UX, summary evaluation và cache encryption. Rủi ro summary drift, source staleness và leakage qua shared provider cache. Không gọi memory là học tự động đã triển khai.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
