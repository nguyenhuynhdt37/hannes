# Subagents và scheduler

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Điều phối child runs và background tasks sau khi single-agent invariants ổn định; parent chịu trách nhiệm budget và review kết quả.

## 2. Trách nhiệm sở hữu

agents sở hữu profiles, task DAG/scheduler, queue, budget reservations và child run metadata. Core vẫn sở hữu mỗi run state machine; launcher port nối hai bên.

## 3. Ngoài trách nhiệm

Không thay core loop, shared mutable transcript, tự mở rộng quyền hay tự chạy unlimited fanout. Background task không được tách khỏi audit/retention.

## 4. Input, output và dữ liệu sở hữu

Input task spec, parentRunId, profile, context snapshot, allowed capabilities, token/time/cost ceilings. Output task state, child run refs, artifact/diff summaries và accounting. Own DAG, reservations và queue leases.

## 5. Xử lý và lifecycle

Validate task/dependencies → derive grants bằng giao với parent → reserve budget → enqueue → lease worker → launch isolated context → collect receipts/results → review merge → settle reservation. Task states queued/running/waiting_approval/completed/failed/cancelled/recovery_required. Child output không tự merge files.

## 6. Dependencies và interface boundaries

Chỉ contracts; RunLauncher, WorkspacePort, StorePort inject. Core gọi DelegationPort; không agents↔core imports. Context transfer là explicit immutable snapshot + provenance; branch/worktree requests qua workspace tool path.

## 7. Luồng sự kiện

DelegationRequested → BudgetReserved → TaskQueued → ChildStarted → ChildCompleted → ParentReviewRequested → BudgetSettled. Parent terminal cancel cascades theo detach policy; detached child cần owner và budget riêng rõ ràng.

## 8. Errors, cancellation và recovery

Worker crash dùng lease epoch fence và mark unknown invocations; retry task không replay side effects. Child failure trả structured result, parent có thể replan trong remaining budget. Cancel parent truyền deadline cho descendants; không orphan task âm thầm.

## 9. Concurrency, races và timeout

Max active children, recursion depth và queue length hữu hạn; budget reserve atomically tránh oversubscription. Shared file writes bị khóa hoặc dùng separate worktrees; merge conflict cần parent/user review. Scheduler fair queues và starvation diagnostics; approval wait có expiry.

## 10. Security considerations

Child không nhận secrets/private memory ngoài explicit scope; grants không lớn hơn parent, revoke cascade. Tool output giữa children là untrusted. Worktree khác không tự thành sandbox và có thể cùng .git state.

## 11. Thư viện dự kiến và alternatives

Bun async scheduler và SQLite store ports dự kiến. Alternative external queue/worker processes sau profiling; không thêm package queue mới trong scaffold. Native workers không cần cho lifecycle design.

## 12. Acceptance criteria và test cases

Cases: fanout cạnh tranh budget, cyclic DAG, parent cancel, child crash after write, two children same file, hidden context leak, revoked parent grant, queue lease expiry. Pass khi không vượt reserve và không mất accounting/unknown outcomes.

## 13. Quyết định mở và rủi ro

Chưa chốt background detach UX, distributed workers, worktree merge policy, delegated approval routing. Rủi ro token runaway và recursively delegating tasks; giới hạn phải có trước bật feature.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
