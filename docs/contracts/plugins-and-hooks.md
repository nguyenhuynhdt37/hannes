# Extensions, plugins và hooks

**Đề xuất v1; Not implemented.** Hannes manifest là domain data; OpenCode/Cordis manifests được adapter chuyển sang đây, lưu original reference và cảnh báo loss. Không giả định mọi ecosystem dùng cùng lifecycle.

| Record | Fields | Invariant |
|---|---|---|
| ExtensionManifest | id, version, entrypoints, contractRange, hostKinds, requestedCapabilities, dependencies, configSchema, provenance | ID/version/host xác thực trước load; request không bằng grant |
| ExtensionContext | extensionId, generation, scopedLogger, configSnapshot, grantedServiceProxies, abort | Không raw DB/shell/credential/core handles |
| Registration | registrationId, kind, ownerGeneration, priority, disposer | Dispose idempotent; stale generation không callable |
| HookRequest | hookId, eventType, immutableSnapshot, deadline, runId? | Hook không mutate shared object |
| HookResult | continue/veto/transform, patch?, diagnostics | Chỉ hook được cấp transform mới đổi allowed fields |
| ServiceDescriptor | namespace, version, methods, schemas, permissionScopes | DI presence không cấp privilege |

Hook categories: observer (async, không sửa run), transform (ordered, pure hoặc side effects riêng qua dispatcher), gate (veto trong scope; không bypass deny). Hooks trước/ sau model/tool có semantics explicit: beforePrompt trước context render, contextTransform sau assemble trước budget check; beforeTool sau validation trước policy, afterTool sau receipt. Hook transform args buộc normalize/schema/permission lại. Không allow hook tự thay successful receipt thành fake success.

Lifecycle: discovered → verified → configured → loading → active → draining → disposed; failed từ loading/active. Config dependencies được resolve trong DAG; missing required service pending theo timeout, cycle là lỗi. Init tạo registration ledger; fail rollback theo reverse order. Reload serialize theo extension graph, chuẩn bị generation mới rồi switch; không mixed generations; inflight calls drain/cancel theo compatibility policy.

Hook ordering: phase → declared priority → stable extension ID → registration index. Không tương thích synchronous upstream transform không được đổi sang async silently; adapter phải reject hoặc cung cấp host đúng semantics. Timeouts riêng init/hook/dispose; không giữ run lock trong unbounded callback. In-process arbitrary JS không thể được capability proxies bảo vệ khỏi ambient process quyền: chỉ trusted plugins; untrusted cần sandbox thật hoặc từ chối.

Skills là text + provenance + scope, không executable permission. Commands gồm metadata và handler port có cùng permission path. Discovery paths không tự thực thi npm scripts; installer cần version/integrity/provenance review ở phase được phép. Dependency conflicts không silently chọn version khác.

Errors: config_invalid, dependency_missing, incompatible_host, unsupported_api, hook_timeout, dispose_failed; safe diagnostics, quarantine policy, không che failure làm success. Contract version negotiated riêng upstream API versions. Acceptance: rollback init, double dispose, reload during tool, hook reorder, incompatible GUI host, denied service bridge, malicious package discovery. [Compatibility matrix](../compatibility/compatibility-matrix.md) quy định từng ecosystem.
