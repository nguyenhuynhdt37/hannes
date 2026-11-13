# Quy tắc phụ thuộc

**Not implemented.** Mọi package được phép phụ thuộc `contracts`; contracts không phụ thuộc package Hannes nào và không xuất foreign SDK types. Public entry là `src/index.ts` tương lai; file khác private trừ subpath đã được ADR công bố. Không import trực tiếp `src/` của package khác. Không circular dependencies, kể cả type-only imports và event command cycles.

| Consumer | Hannes dependencies được phép ngoài contracts | Lý do |
|---|---|---|
| core | Không | Gọi injected model/tool/policy/store/agent ports |
| models | Không | Chuyển provider wire format |
| workspace | Không | Filesystem/version/snapshot ownership |
| security | Không | Policies và audit qua ports, không gọi tool executor |
| persistence | Không | SQLite transaction ownership |
| code-intel | workspace | Đọc normalized snapshots, không ghi code |
| tools | workspace, security, code-intel | Dispatcher kiểm quyền trước executor |
| mcp | security | Map permissions, register tool descriptors qua port |
| extensions | security | Grants và mediated service ports |
| compat-opencode / compat-deepseek | extensions | Chuyển hooks/services; không import core internals |
| agents | Không | Scheduler gọi runtime ports; không import core |
| sdk | Không | Client protocol, không nhúng core |
| testkit | Không | Fakes hợp đồng, không production dependency |
| apps/cli, apps/server | Implementations cần composition | Chỉ composition root được nối graph |
| apps/tui | sdk | UI chỉ command/event protocol |
| apps/web, apps/desktop | Chưa có imports | Host tương lai cần review |

Dependencies của tools lên code-intel chỉ cho read/search; code-intel không biết tools. Extension/tool/MCP registration dùng injected registrars trong contracts, tránh extensions↔tools hoặc mcp↔tools. Agents nhận RunLauncher port do root cung cấp, tránh agents↔core. Store không import core; audit sink inject, tránh security↔persistence.

Permission invariant: validate → normalize target → evaluate policy → persist intent/approval evidence → revalidate lease/target → execute → record outcome. Mọi đường thực thi, kể cả plugin/MCP/sidecar, phải đi qua cổng này. Kiểm tra trước UI confirmation không đủ nếu target thay đổi sau đó.

DAG graph, public entry checks và side-effect enforcement sẽ thành checks ở phase triển khai. Phase 0 chỉ mô tả. ADR bắt buộc cho dependency edge mới, shared ownership, public type/version thay đổi, process boundary hay thay security model.
