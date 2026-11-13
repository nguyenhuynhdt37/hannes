# MCP integration

**Trạng thái: Not implemented.** Mọi lifecycle và acceptance dưới đây là đặc tả cho phase tương lai.

## 1. Mục đích và phạm vi

Hannes là MCP Host; manager tạo một Client cho mỗi Server connection. Server cung cấp tools/resources/prompts; tích hợp theo SDK chính thức, không viết lại protocol.

## 2. Trách nhiệm sở hữu

MCP package sở hữu discovery, namespacing, client connections, negotiated capability registry và permission translation; security sở hữu decisions, extensions không quản lý MCP protocol.

## 3. Ngoài trách nhiệm

Không biến remote server thành trusted code, không cấp toàn bộ shell/network credentials, không thay tool dispatcher. Hannes server HTTP/WebSocket không đồng nghĩa MCP Server; expose Hannes như MCP Server là capability tương lai riêng.

## 4. Input, output và dữ liệu sở hữu

Input server identity/config/credentialRef, transport kind, approved capabilities. Output namespaced descriptors, validated results/content/resources/prompts và connection events. Own connection epoch/capability hash; credential bytes chỉ tồn tại tại transport auth boundary.

## 5. Xử lý và lifecycle

Discover không execute → validate config/trust → connect stdio hoặc Streamable HTTP → initialize và negotiate protocol/capabilities → initialized → list tools/resources/prompts theo advertised capabilities → register generation. Invoke chỉ sau permission; validate result, content types/size/schema nếu có; mark untrusted provenance. Close unregister trước dispose. Resources/prompts không tự cấp quyền fetch URLs.

## 6. Dependencies và interface boundaries

Depend contracts/security; official SDK split v2 client/core packages dự kiến. Tool registrations qua injected port, tránh mcp↔tools. SDK semver v2 khác protocol negotiated revision. MCP Streamable HTTP là transport riêng, không dùng WebSocket của Hannes.

## 7. Luồng sự kiện

Host discover → Client initialize → Server capabilities → Client tools/list → Hannes registration → User/model tool request → Hannes permission → Client tools/call → Server result → validate → transcript. Resources/read và prompts/get có scope riêng; list_changed refresh theo server support.

## 8. Errors, cancellation và recovery

Stdio crash unregister capabilities, mark inflight calls unknown nếu có side effects, reconnect bounded rồi renegotiate. HTTP disconnect có thể resume SDK-supported stream/session; không giả định tool retry an toàn. Cancel dùng SDK notification/abort theo negotiated support, timeout không chứng minh remote action dừng. Không giữ capability grants cũ khi identity/schema đổi.

## 9. Concurrency, races và timeout

Connection lock serialize initialize/reconnect/dispose; epochs bỏ late responses. Bound concurrent calls/server, list pagination, payload và progress flood. Connect/auth/idle/request/overall deadlines riêng. OAuth flow có state/PKCE TTL và một active auth transaction/issuer/client binding.

## 10. Security considerations

Remote OAuth dùng SDK discovery/authorization helpers: issuer/origin validation, PKCE, user state validation, redirect allowlist, token audience/resource binding. OS credential store cho refresh tokens; config/logs chỉ credentialRef. SDK docs yêu cầu app tự kiểm OAuth state. Không forward provider API keys sang MCP. SSRF protections và stdio env allowlist. Process riêng không sandbox.

## 11. Thư viện dự kiến và alternatives

Official MCP SDK v2 preferred; snapshot docs v2.4.0 có package split và removed legacy transports, xem compatibility/mcp. Alternative pin supported v1 chỉ qua ADR nếu Bun v2 không đạt; không hand-roll wire protocol.

## 12. Acceptance criteria và test cases

Cases: stdio happy flow; Streamable HTTP OAuth expired/refresh/revoked; state mismatch; capability/schema change; malformed/oversized results; tool denies; remote write timeout unknown; crash reconnect; cancellation; resource URI SSRF; two auth transactions; negotiated unsupported protocol. Matrix ghi Not implemented.

## 13. Quyết định mở và rủi ro

Chưa pin exact v2 package set/peer versions và Bun stdio/Web Crypto interoperability. Remote server trust/discovery UX, OAuth storage OS và supported protocol revisions cần conformance fixtures. Ví dụ flow là kế hoạch, chưa chạy.

Tham chiếu: [contracts](../contracts/agent-events.md), [tools/permissions](../contracts/tools-and-permissions.md), [dependencies](../architecture/dependency-rules.md), [event flow và sequence diagram](../architecture/event-flow.md), [catalog](../delivery/dependency-catalog.md), [roadmap](../delivery/implementation-roadmap.md).
