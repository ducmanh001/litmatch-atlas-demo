# Cache and state: Redis, locks, idempotency, client state: limits

id: system:state:limits

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- Không xác minh được cấu hình Redis phía hạ tầng (maxmemory/eviction, persistence, TLS thực tế của dịch vụ hosted); mã chỉ truyền REDIS_URL cho ioredis.
- Tên kênh/khoá do @socket.io/redis-adapter tạo nằm trong thư viện, không có trong repo nên không liệt kê pattern cụ thể.
- Các ứng viên trong truth-redis không phải khoá Redis: ws://localhost:7880 (URL LiveKit mặc định), match-sessions/:id/join|end (route), quota_date/user_id/match_type (SQL guest quota), user:{userId} (tên room Socket.IO, xem inMemoryState) và party:comment:{userId}:{roomId}:{key} (giá trị cột party_room_comments.idempotency_key).
- RateLimitPort.refund và tham số operationId chưa có nơi gọi ngoài adapter (grep apps/core-api/src); Lua refund hiện là mã chưa được dùng.
- Không tìm thấy Cache-Control/ETag/max-age tuỳ chỉnh, cấu hình revalidate/force-cache của Next.js hay proxy_cache trong apps/core-api, apps/web/src, k8s/, deploy/, docker/ (grep tĩnh); lớp CDN Cloudflare (ADR 0006) và ingress nằm ngoài mã đã đọc.
- Chưa đọc nhánh so khớp nội dung khi replay của stories, support_tickets, videos, soul_chat_messages, movie_session_messages; các mục đó chỉ xác nhận ràng buộc unique và hàm dựng key.
- Admin: chưa thấy kết nối realtime hay xoá cache khi đăng xuất; chưa đọc đầy đủ apps/admin để khẳng định không có.
- Số pod thực tế, topology Redis (đơn/cluster) và hành vi khi Redis đầy hoặc failover không suy ra được từ mã.
