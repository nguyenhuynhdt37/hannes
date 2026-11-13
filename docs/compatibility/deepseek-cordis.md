# DeepSeek Harness và Cordis compatibility

**Not implemented; chưa chốt A/B.** Truy cập 2026-11-13, Harness snapshot `39163dd30f703a79079e2d2446545cff3c453738`:

- [Cordis primer](https://github.com/deepseek-ai/deepseek-harness/blob/39163dd30f703a79079e2d2446545cff3c453738/docs/cordis-primer.md): Context/Service, injection, reversible effects/events và executable `!!js` config.
- [Vendor provenance](https://github.com/deepseek-ai/deepseek-harness/blob/39163dd30f703a79079e2d2446545cff3c453738/vendor/README.md): upstream Cordis 4.0.0-rc.12, core commit `01b396240ce47cf2779c40af9bc445bb748fe112`, loader/include pins và local diffs. npm fork version không chứng minh vendor equivalence.
- [App boot](https://github.com/deepseek-ai/deepseek-harness/blob/39163dd30f703a79079e2d2446545cff3c453738/packages/boot/app-boot/README.md), [HMR](https://github.com/deepseek-ai/deepseek-harness/blob/39163dd30f703a79079e2d2446545cff3c453738/packages/boot/hmr/README.md), [CLI reference](https://github.com/deepseek-ai/deepseek-harness/blob/39163dd30f703a79079e2d2446545cff3c453738/apps/cli/reference/README.md): bundle/profile patch composition, startup vs enabled hot reload, serialized Loader updates.
- [Cordis API registry](https://deepseek-harness.github.io/deepseek-harness/en/reference/cordis-api/registry.md), [lifecycle/effects](https://deepseek-harness.github.io/deepseek-harness/en/develop/cordis-tutorial/02-lifecycle-and-effects.md), [upstream Cordis](https://github.com/cordiverse/cordis). Context7 evidence complements repository docs, not runtime conformance.

## Surface inventory

| Capability | Evidence / semantics | Hannes mapping | Required fixture |
|---|---|---|---|
| Context/Service | Context service repository; object/function plugin with inject/apply or Service subclass | Scoped service proxies; identity/version mapping | Service available/missing/replaced, consumer reload |
| Dependency injection | Dependencies wait for service presence | Dependency DAG and pending state with diagnostics | Cycles/missing services/timeouts |
| Fiber lifecycle | PENDING → LOADING → ACTIVE → UNLOADING → DISPOSED, FAILED branch | Adapter lifecycle ledger, not OS threads | Async init fail, dispose, parent-child cleanup |
| Effects | ctx.effect/on reversible registrations, async disposal | Extension registrations and cleanup receipts | Reload twice, exact once dispose, leak test |
| Events | emit/waterfall/parallel/serial/bail modes | Separate mode-specific bridges | Return values/order/short-circuit/error propagation |
| Profiles/bundles | Ordered patch files/layers; templates differ | profile-loader preserves provenance and ordering | Same resolved config against pinned dsh |
| Reload | HMR serialized; some templates startup-only | Generation swap only for supported modes | File edit, bundle list change, rollback failure |
| Executable config | `!!js` supported upstream | Deny until explicit trusted review; no auto evaluation | Injection cannot obtain privileged service |

App-boot docs establish profiles combine bundles and cordis.patch.yml; bundle manifest lists ordered patches. Web template enables live reload while other templates may apply on startup. Installation changes can take effect next profile start; không đồng nhất mọi change với immediate HMR. Exact overlay precedence/schema/version phải lock bằng fixture, không áp Hannes config merge thay upstream semantics.

## A. In-process Cordis integration

Load reviewed Cordis runtime/fork in Bun, translate Hannes ports into scoped services; objects/functions có thể chia sẻ trực tiếp, preserving identity và synchronous event modes dễ hơn. Rủi ro: vendor deltas và package versions, ambient process quyền, dependency lifecycle chiếm domain ownership, service objects giữ stale grants. Chỉ trusted plugins; core loop/permission vẫn Hannes. Không expose raw `ctx.llm/tools/sessions` privileged implementations; wrappers giữ validation/authorization và registration generation.

Acceptance A: Bun compatibility, exact Context/Service semantics, fiber teardown/effect leaks, DI replacement, every service method permission coverage, upstream/fork differential tests. Không chọn chỉ vì TypeScript objects dễ chia sẻ.

## B. DeepSeek sidecar

Chạy dsh runtime trong process riêng và gọi **interface được upstream hỗ trợ sau khi xác minh**. Không mặc định dsh có JSON-RPC/HTTP/control API và không invent wire protocol để tuyên bố compatibility. Lợi ích: giữ upstream lifecycle/config gần nguyên bản và cô lập crash; chi phí IPC, serialization, auth, deadlines, cancellation, object identity không qua process. Sidecar vẫn có thể đọc filesystem/network bằng OS quyền nếu không sandbox.

Acceptance B: documented/versioned handshake, supported request/event/cancel surfaces, grant mediation cho side effects, crash recovery receipts, bounded payload/latency, credential isolation. Nếu dsh không cho intercept privileged actions thì không dùng sidecar cho capability đó; hỗ trợ delegated unbounded execution là không chấp nhận được.

## Decision gate và hybrid

Phase 7 trước tiên pin Harness/fork/upstream và corpus: pure effect plugins, tools/services, async fibers, profile composition và reload. So sánh A/B theo semantic coverage, permission enforceability, lifecycle fidelity, maintenance, Bun compatibility và IPC overhead đo thực tế. Hybrid có thể dùng A cho pure hooks và B cho runtime-specific services, nhưng phải tránh double ownership và double execution; mỗi capability có owner/route duy nhất.

Chưa xác minh: supported dsh control/IPC ABI, npm-vendor delta đầy đủ, exact fiber failure semantics, event error propagation và fixture corpus ngoài docs. Service bridge thay data shapes không đảm bảo semantic parity. Chọn A/B/hybrid cần ADR cập nhật [0003](../adr/0003-plugin-compatibility.md) và [0004](../adr/0004-security-boundary.md), không triển khai trong Phase 0.
