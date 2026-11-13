# Plugins và skills

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Quản lý extension discovery/install proposal/lifecycle/hooks/skills/commands bằng contracts; compatibility không được làm domain runtime phụ thuộc ecosystem.

## 2. Trách nhiệm sở hữu

extensions sở hữu manifest validation, host generation, registration ledger, config và capability service proxies. Adapters sở hữu translation. Skills text discovery giữ provenance và trust.

## 3. Ngoài trách nhiệm

Không tự install npm trong Phase 0; installer tương lai không cấp quyền chỉ vì package tải được. Không own model loop, global provider key hay GUI implementation.

## 4. Input, output và dữ liệu sở hữu

Input manifest/package identity/config/grants; output scoped registrations, diagnostics và lifecycle events. Own dependency graph/generations/installed metadata; no raw DB handle. Skill input là Markdown content với owner/path/hash, output context segment được budget.

## 5. Xử lý và lifecycle

Discover → verify package/host/contracts/config → resolve dependencies → approve requested grants → load → record reversible registrations → active → drain → dispose reverse order. Reload stage generation mới trước swap, failure rollback. Commands routed qua authorized command dispatcher; hook transforms phải validate lại.

## 6. Dependencies và interface boundaries

Depend contracts/security. Registrars/service ports inject từ composition roots. compat-opencode/deepseek gọi public extension APIs; không truy cập host internals. Host kinds server/tui/gui khai báo riêng.

## 7. Luồng sự kiện

ExtensionDiscovered → PermissionRequested → ExtensionLoaded → HookRegistered → HookInvoked → ExtensionDraining → ExtensionDisposed. Skill selected tạo context provenance, không trigger executable lifecycle.

## 8. Errors, cancellation và recovery

Init fail rollback effects; disposer fail ghi audit/quarantine, tiếp tục dọn các registrations khác. Hook timeout có policy fail-open cho observer hoặc fail-closed cho gate, không dùng một default mơ hồ. Cancel inflight hooks theo generation; không giả disposer luôn undo external effects.

## 9. Concurrency, races và timeout

Serialize dependency graph reload; stable hook order theo contract; bounded init/hook/dispose deadline. Inflight requests pin old generation tới drain; revoked grants chặn ngay cả generation cũ. Dependency service replacement invalidate consumers theo policy.

## 10. Security considerations

Untrusted npm package có ambient Bun quyền nếu in-process: proxy API không là sandbox. Chỉ trusted reviewed packages in-process; untrusted cần sandbox verified hoặc unsupported. Disable install scripts mặc định tương lai, integrity/provenance và transitive dependency review; skills chống prompt injection qua instruction hierarchy.

## 11. Thư viện dự kiến và alternatives

Bun loader/resolver, schema validation, optional Cordis qua compat layer. Alternative isolated worker/sidecar; capability RPC không tự cung cấp OS isolation. Không sử dụng upstream loaders ngoài review vì có thể evaluate config JS.

## 12. Acceptance criteria và test cases

Cases: malicious manifest, missing service, dependency cycle, init partial failure, double dispose, reload while hook running, grant revoke, unsupported GUI entrypoint, skill attempts override policy. Gate: no leaked registrations, no hidden privilege gain.

## 13. Quyết định mở và rủi ro

Chưa chốt signature distribution, marketplace trust, host isolation backend và hot-reload compatibility constraints. API compatibility chỉ theo fixtures/version; package loading không đồng nghĩa behavior compatible.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
