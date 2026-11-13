# Quy tắc cho coding agents

Repository đang ở **Phase 0: documentation-only**. Chỉ tạo thư mục, source `.ts`/`.tsx` 0 byte, `.gitkeep` 0 byte và Markdown có nội dung. Không thêm implementation, declarations, imports, TODO comments, tests có code, dependencies, package manifests, build configs, CI hoặc binaries. Không install/build/lint/test/start application trong Phase 0.

Trước mọi phase triển khai, đọc [docs](docs/README.md), toàn bộ [contracts](docs/contracts/agent-events.md), [dependency rules](docs/architecture/dependency-rules.md), [module ownership](docs/architecture/repository-map.md) và [roadmap](docs/delivery/implementation-roadmap.md). Chỉ triển khai phạm vi phase được giao; nguồn rỗng không phải bằng chứng tính năng hoạt động.

- Public contracts chỉ được thay đổi cùng ADR, migration/versioning và cập nhật consumers. Không deep-import private files giữa packages.
- Không thêm dependency chưa đánh giá tên package, phiên bản, giấy phép, provenance, Bun/OS compatibility và threat surface; cập nhật catalog trước khi cài trong phase được phép.
- Không bypass permission engine, kể cả builtin tools, plugin hooks, MCP, SDK, subagents và recovery. Process riêng không đồng nghĩa sandbox.
- Plugin chỉ nhận capability được cấp; không truyền database handle, raw shell, secrets hoặc toàn quyền workspace qua context.
- Tôn trọng ownership: core điều phối; security quyết định quyền; workspace kiểm soát filesystem; persistence sở hữu durable writes; adapters chỉ chuyển đổi.
- Trong phase triển khai, viết tests có ý nghĩa cho invariants, conformance, cancellation và failure modes; cập nhật docs cùng thay đổi. Không dùng mock success để chứng minh compatibility.
- Không tự chuyển ngôn ngữ/framework hoặc mở rộng package tree. Thay đổi ranh giới cần ADR và phạm vi được giao.
- Không commit credentials, raw prompts chứa bí mật hoặc output nhạy cảm. Log phải redacted.
- Nghiên cứu upstream phải lưu URL/path, ngày truy cập, revision và giới hạn bằng chứng. `Needs verification` tốt hơn suy đoán.
- Tất cả tính năng chưa triển khai mang trạng thái `Not implemented`. GUI compatibility cần GUI host riêng và acceptance tests riêng.

Phase 0 được xác minh bằng filesystem/content/link inspection; không chạy application hoặc test suite. Quy tắc này không ngăn đọc nguồn công khai để nghiên cứu.
