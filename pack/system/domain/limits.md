# Domain rules, error handling, glossary, conventions: limits

id: system:domain:limits

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- Mọi khẳng định là theo mã tĩnh tại HEAD nhánh fix/multi-pod-readiness; chưa chạy hệ thống nên hành vi runtime (log thật, header thật, cấu hình triển khai) chưa được xác nhận.
- Giá trị mặc định lấy từ Joi default trong env.validation.ts; ConfigMap/secret ở môi trường thật có thể ghi đè và không được đối chiếu ở phần này.
- Header Retry-After và X-RateLimit do thư viện @nestjs/throttler đặt; bằng chứng lấy từ bản cài trong node_modules cục bộ (không thuộc repo) nên phiên bản chạy thật chưa xác nhận.
- Việc ServiceUnavailableException(code, message) ra COMMON_INTERNAL_ERROR kèm message là chuỗi mã được suy ra từ filter cộng với cách NestJS dựng body (node_modules/@nestjs/common); chưa có test HTTP xác nhận.
- Chưa đọc thân FriendService.ensureFriendship nên chưa xác nhận Conversation được tạo cùng transaction; chỉ xác nhận Friendship nhận `manager` của transaction cuộc gọi.
- Ánh xạ quota miễn phí cho VIP/SVIP (giới hạn 30/20 và 60/40) chỉ xác nhận ở env mặc định và docs/06; chưa đọc đoạn code chọn giới hạn theo tier.
- Luồng IAP verify (native) chưa được đọc chi tiết; chỉ xác nhận đường payOS có chặn guest.
- Danh sách 30 luật là lựa chọn theo độ nhạy khi sửa code, không phải toàn bộ luật (docs/06 tự ghi là danh sách tối thiểu).
