# Error codes MovieMatchErrors

id: errors:MovieMatchErrors
module: module:movie-match

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| MOVIE_SESSION_NOT_FRIEND | NOT_FOUND | 2 | `friendUserId` không phải bạn của caller HOẶC caller tự nhắm chính mình — gộp 1 mã, cùng 404 (chống oracle dò userId, cùng pattern `FriendErrors.NOT_FRIEND`). | apps/core-api/src/modules/movie-match/movie-match.errors.ts:7 |
| MOVIE_SESSION_ALREADY_ACTIVE | CONFLICT | 2 | Caller đang active với 1 cặp KHÁC — không tự ý kết thúc session cũ thay user (spec § 3). | apps/core-api/src/modules/movie-match/movie-match.errors.ts:9 |
| MOVIE_SESSION_NOT_FOUND | BAD_REQUEST,NOT_FOUND | 4 | Session không tồn tại HOẶC caller không phải participant — cùng 1 mã (docs/10 § 10.1.D). | apps/core-api/src/modules/movie-match/movie-match.errors.ts:11 |
| MOVIE_SESSION_ENDED | CONFLICT | 5 | Session đã `ended` mà vẫn thao tác state. | apps/core-api/src/modules/movie-match/movie-match.errors.ts:13 |
| MOVIE_SESSION_INVALID_VIDEO_URL | UNPROCESSABLE_ENTITY | 4 | `videoUrl` sai format, quá dài, hoặc domain không nằm trong whitelist. | apps/core-api/src/modules/movie-match/movie-match.errors.ts:15 |
| MOVIE_SESSION_RATING_NOT_OPEN | CONFLICT | 1 | Rating gọi trước khi phase xem kết thúc (chưa "Kết thúc"/chưa hết giờ). | apps/core-api/src/modules/movie-match/movie-match.errors.ts:17 |
| MOVIE_SESSION_RATING_CONFLICT | CONFLICT | 1 | Đổi rating đã chốt — mỗi bên chỉ đánh giá một lần. | apps/core-api/src/modules/movie-match/movie-match.errors.ts:19 |
| MOVIE_SESSION_MESSAGE_TOO_LONG | UNPROCESSABLE_ENTITY | 1 | Chat quá MOVIE_MATCH_MESSAGE_MAX_LENGTH. | apps/core-api/src/modules/movie-match/movie-match.errors.ts:21 |
| MOVIE_SESSION_MESSAGE_IDEMPOTENCY_CONFLICT | CONFLICT | 1 | Cùng Idempotency-Key nhưng nội dung/session khác (docs/05 § 5.10). | apps/core-api/src/modules/movie-match/movie-match.errors.ts:23 |
| MOVIE_SESSION_REACTION_INVALID | UNPROCESSABLE_ENTITY | 1 | Reaction emoji ngoài whitelist. | apps/core-api/src/modules/movie-match/movie-match.errors.ts:25 |
| MOVIE_SESSION_ANON_VIDEO_POOL_EMPTY | CONFLICT | 1 | Không có video nào trong MOVIE_MATCH_ANON_VIDEO_URLS — lỗi cấu hình vận hành. | apps/core-api/src/modules/movie-match/movie-match.errors.ts:27 |
