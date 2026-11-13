# Code intelligence

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Cung cấp lexical search, symbols/AST và LSP diagnostics có revision provenance để context builder chọn dữ liệu.

## 2. Trách nhiệm sở hữu

code-intel sở hữu LSP sessions/document versions, parser lifecycle, index generations và query normalization. Workspace là nguồn file/snapshot; tools sở hữu thao tác sửa.

## 3. Ngoài trách nhiệm

Không tự áp LSP workspace edits, chạy compiler tùy ý hoặc coi diagnostics là build/test pass. LSP code actions có side effects phải thành tool request mới.

## 4. Input, output và dữ liệu sở hữu

Input workspace snapshot, file changes, language configuration và query. Output bounded matches/symbols/diagnostics với path, range, source revision, freshness và server identity. Own index metadata, không own canonical source.

## 5. Xử lý và lifecycle

Resolve granted root → start configured language server → initialize capabilities → open/sync documents → parse/index changed files → publish generation → serve query. Watcher overflow gây full scoped rescan. Rename/delete update index transactionally; parser failure giữ lexical fallback với stale flag.

## 6. Dependencies và interface boundaries

Depend contracts/workspace. Query port inject vào core/tools. LSP transport/schema không leak ra contracts; canonical positions phải ghi encoding UTF-8/UTF-16 negotiation để không sai range.

## 7. Luồng sự kiện

WorkspaceChanged → IndexInvalidated → IndexUpdated; LspDiagnosticsPublished có documentVersion. Query trả snapshot watermark; context từ chối hoặc đánh dấu nếu source revision mới hơn index.

## 8. Errors, cancellation và recovery

Server crash restart bounded với backoff, invalidate diagnostics và reopen current documents. Cancel query gửi LSP cancellation khi có; late result bị bỏ theo request generation. Parse error không làm agent run fail nếu lexical fallback đủ.

## 9. Concurrency, races và timeout

Coalesce watcher changes, parse worker bounded, limit per-language servers và project file count. LSP request timeout, debounce và indexing IO budgets không chặn permission/event sequencer. Race external file vs open document dùng explicit version/source policy.

## 10. Security considerations

LSP executable là code ngoài cần trust approval; project configuration có thể khởi chạy processes. Exclude secrets/vendor/generated paths theo grant và query scope. Không follow symlink ngoài root. Tree-sitter native/WASM grammar provenance cần review.

## 11. Thư viện dự kiến và alternatives

ripgrep, Tree-sitter và vscode-jsonrpc/vscode-languageserver-protocol là ứng viên; exact Bun/native compatibility xem catalog. Alternative lexical-only ban đầu; embedding/symbol database riêng chưa quyết định.

## 12. Acceptance criteria và test cases

Cases: Unicode ranges, delete/rename storm, watcher overflow, stale diagnostics, LSP crash, cancel after restart, grammar load failure, symlink outside scope. Pass khi query không bịa freshness và edits vẫn qua dispatcher.

## 13. Quyết định mở và rủi ro

Chưa chốt persistent index storage, grammar distribution, LSP server discovery và languages ưu tiên. Rủi ro resource exhaustion, native ABI/Bun mismatches và stale context.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
