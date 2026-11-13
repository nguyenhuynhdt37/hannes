# Tools và workspace

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Cung cấp read/write/edit/search/shell/git có permission, version checks và receipts; giữ mọi filesystem operation trong workspace scope được cấp.

## 2. Trách nhiệm sở hữu

Tools sở hữu registry/schema/dispatcher/output validation. Workspace sở hữu canonical path, watcher, patch, snapshots và worktree lifecycle. Builtins mô tả intent rồi dùng controlled executors; search code intelligence chỉ đọc.

## 3. Ngoài trách nhiệm

Không chứa agent loop, model routing, policy decisions, UI confirmation hoặc SQLite writes trực tiếp. Git tool không tự commit/push destructive operation khi chưa có scope.

## 4. Input, output và dữ liệu sở hữu

Input ToolInvocation và lease; output ToolResult, bounded artifacts, file revision/diff/process receipts. Workspace owns snapshot IDs, file hash/revision và worktree membership; không sở hữu user modifications ngoài tool.

## 5. Xử lý và lifecycle

Register descriptors → validate args → resolve path/cwd/origin → policy/approval → acquire target lock → revalidate lease/version → persist intent → execute → verify effect → persist result → release. Edit kiểm preimage hash; patch không match thì conflict, không fuzzy overwrite. Snapshot không thay Git history. Shell dùng explicit executable/argv/cwd/env, string shell cần policy riêng.

## 6. Dependencies và interface boundaries

Tools → contracts/workspace/security/code-intel. Workspace chỉ contracts. Dispatcher dùng store/event ports để ledger; native OS calls nằm sau enforcement. Public ToolRegistry port cho MCP/extensions, không cho plugin giữ executor handle.

## 7. Luồng sự kiện

ToolRequested → ValidationFailed hoặc PermissionEvaluated → ToolStarted → WorkspaceChanged → ToolCompleted/Failed. Watcher chỉ invalidation signal, không chứng minh commit; readback/hash mới xác nhận version.

## 8. Errors, cancellation và recovery

Write lỗi giữa bước phải ghi receipt và partial/unknown status; rollback chỉ khi không ghi đè user changes. Cancel shell gửi termination theo platform rồi kill sau grace; grandchildren có thể sống nếu chưa có sandbox/job control. Git conflict trả structured error; không reset/clean để tự sửa.

## 9. Concurrency, races và timeout

Locks keyed canonical workspace/target; multi-file patch lock theo thứ tự ổn định tránh deadlock. Không giữ lock khi đợi approval. Shell output bounded, stream backpressure và total/idle deadlines. External editor/Git process có thể đổi files: recheck revisions; watcher events có thể trễ/coalesce.

## 10. Security considerations

Traversal, symlink escape, TOCTOU, .git internals và worktree paths là boundaries. Realpath một lần không đủ. Sensitive reads cũng cần policy; env allowlist, không echo secrets. Untrusted output không được tái nhập thành command. Shell parsing không thay sandbox.

## 11. Thư viện dự kiến và alternatives

ripgrep cho lexical search; OS filesystem và Git binary adapters; patch implementation cần chọn sau roundtrip tests. Alternatives libgit2/native Rust nếu có profiling. Không cài binaries hay dependencies Phase 0.

## 12. Acceptance criteria và test cases

Cases: symlink swap/read/write; two writers; file đổi sau approval; partial patch; shell timeout còn child; huge/binary output; user dirty Git tree; Windows drive/case path; worktree belongs wrong repo. Gate: receipts chính xác, không mất user edits, không escape granted roots.

## 13. Quyết định mở và rủi ro

Chưa chốt atomic multi-file transaction strategy, Git rollback semantics, shell sandbox backend từng OS. Native optimization không được đổi path/permission invariants.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
