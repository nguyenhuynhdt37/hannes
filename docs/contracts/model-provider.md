# Model provider port

**Đề xuất v1; Not implemented.** `models` sở hữu chuyển đổi provider; core chỉ biết normalized request/stream/result. Không để AI SDK điều khiển loop/tool execution thay core. AI SDK cung cấp primitives; tool descriptors không có privileged execute callbacks truyền cho model layer.

| Request field | Quy tắc |
|---|---|
| requestId, runId, stepId, attemptId | Attempt mới khi retry; trace ID không chứa prompt |
| providerRef, modelId, configHash | Model được pin; không tự đổi endpoint |
| messages | role + typed content parts; tool results liên kết callId |
| systemInstructions | Trusted/untrusted segments và provenance phân biệt |
| toolDescriptors | Namespaced name, schemaVersion, inputSchema; không code |
| limits | maxInputTokens, maxOutputTokens, deadline, cost ceiling |
| generation | sampling hỗ trợ thực tế; unsupported field gây diagnostic |
| abortSignal | Port nội bộ; wire dùng cancellation ID |
| credentialRef | Resolve trong adapter; không serialize credential vào events |

| Capability field | Giá trị và invariant |
|---|---|
| text / image / audio | supported / unsupported / unknown |
| toolCalling / parallelTools | Chỉ dùng khi verified; unknown không suy ra từ tên model |
| structuredOutput / reasoning | Semantics riêng provider; không ép equivalence |
| contextLimit / outputLimit | Metadata có source/revision; thiếu thì ceiling bảo thủ |
| usage / cancellation | Mức hỗ trợ; cancel remote không bảo đảm rollback |

Stream events: started; textDelta; reasoningDelta (ẩn theo policy); toolCallStart(callId,name); toolCallDelta(callId,fragment); toolCallComplete(callId,args); usageUpdate; completed(finishReason); failed(error). Deltas không được mutate canonical call đã complete; callId không trùng trong response. Tool JSON phải parse/schema validate sau completion, giới hạn size/depth, reject incomplete/truncated arguments. Out-of-order fragments chỉ buffer trong limit; unknown call ID là protocol error.

Usage: input/output/cacheRead/cacheWrite/reasoning tokens có thể null khi provider không báo; estimated flag bắt buộc nếu ước tính. Không cộng reasoning hai lần khi đã bao gồm output; giữ raw usage reference đã redacted và normalized accounting policy. Price snapshot/currency được version; cost unknown không ghi 0.

Finish reasons: stop, tool_calls, length, content_filter, error, cancelled, unknown. Length không đồng nghĩa tool call đầy đủ. Router ưu tiên 9Router sau xác minh endpoint; fallback provider chỉ theo configured policy, không gửi lại dữ liệu sang tenant/provider khác tự động. OpenAI-compatible không chứng minh Responses, tools hoặc usage đều tương thích.

Retry: 429/temporary transport trước effects có backoff + Retry-After ceiling; auth/invalid input không retry. Khi đã stream partial text, retry tạo attempt mới và replace/mark previous partial, không nối thành message sai. Timeout gồm connect/first-byte/idle/total; các giá trị pin trong resolved config và test theo fake clock ở phase triển khai.

Version migration cần ADR khi đổi role/part semantics. Acceptance fixtures: interleaved calls, malformed JSON, SSE disconnect, no usage, provider refusing tools, cancellation và secret redaction. 9Router model list/auth/limits là Needs verification trước Phase 1.
