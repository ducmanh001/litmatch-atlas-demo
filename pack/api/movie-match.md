# REST API of movie-match

id: api:movie-match
module: module:movie-match
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/movie-match.md

### POST /movie-match/sessions

Tạo phiên xem chung với 1 bạn (friendUserId + videoUrl): URL phải http/https và host thuộc MOVIE_MATCH_ALLOWED_VIDEO_HOSTS, bắt buộc areFriends (404 chung), idempotent theo cặp, 409 nếu một trong hai đang active ở phiên khác.

- handler: MovieMatchController.createSession (apps/core-api/src/modules/movie-match/movie-match.controller.ts:50); operationId: MovieMatchController_createSession
- auth: Bearer JWT
- body CreateMovieSessionDto: friendUserId*:string, videoUrl*:string[maxLength=4096]
- response 200 MovieSessionDto: id:string, partnerUserId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string(date-time), status:string, endedAt:string(date-time), endReason:string
- FE callers: web apps/web/src/features/movie-match/api.ts:26

### PATCH /movie-match/sessions/{id}/state

Participant cập nhật positionSeconds/isPlaying của phiên bạn bè (last-write-wins, không lock, không Idempotency-Key), 409 nếu đã ended, rồi publish movie.state.changed cho cả hai.

- handler: MovieMatchController.updateState (apps/core-api/src/modules/movie-match/movie-match.controller.ts:69); operationId: MovieMatchController_updateState
- auth: Bearer JWT
- params: path:id*:string
- body UpdateMovieStateDto: positionSeconds*:number, isPlaying*:boolean
- response 200 MovieSessionDto: id:string, partnerUserId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string(date-time), status:string, endedAt:string(date-time), endReason:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /movie-match/sessions/{id}/end

Một trong hai participant kết thúc phiên bạn bè (endReason=left), idempotent; đổi status và xoá 2 dòng active-participant cùng transaction rồi publish movie.session.ended.

- handler: MovieMatchController.endSession (apps/core-api/src/modules/movie-match/movie-match.controller.ts:90); operationId: MovieMatchController_endSession
- auth: Bearer JWT
- params: path:id*:string
- response 200 MovieSessionDto: id:string, partnerUserId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string(date-time), status:string, endedAt:string(date-time), endReason:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /movie-match/sessions/{id}

Poll fallback cho realtime: trả MovieSessionDto (kèm partnerUserId) cho participant; không tồn tại hoặc không phải participant đều 404 chung (chống IDOR).

- handler: MovieMatchController.getSession (apps/core-api/src/modules/movie-match/movie-match.controller.ts:104); operationId: MovieMatchController_getSession
- auth: Bearer JWT
- params: path:id*:string
- response 200 MovieSessionDto: id:string, partnerUserId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string(date-time), status:string, endedAt:string(date-time), endReason:string
- FE callers: web apps/web/src/features/movie-match/api.ts:39

### POST /movie-match/anon/queue

Vào hàng đợi ghép ẩn danh (idempotent): advisory lock tuần tự hoá matcher, ghép với entry cũ nhất vượt qua SafetyService.canPair, server chọn video từ MOVIE_MATCH_ANON_VIDEO_URLS; người đến trước chỉ biết đã ghép qua poll.

- handler: MovieMatchController.joinAnonQueue (apps/core-api/src/modules/movie-match/movie-match.controller.ts:117); operationId: MovieMatchController_joinAnonQueue
- auth: Bearer JWT
- response 200 MovieAnonStateDto: state:enum(idle|queued|watching|rating|completed), queuedAt:string, sessionId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string, expiresAt:string, myRating:enum(like|boring|rude), opponentRated:boolean, outcome:enum(matched|not_matched|cancelled|expired), partnerUserId:string
- FE callers: web apps/web/src/features/movie-match/anon-api.ts:63

### GET /movie-match/anon/current

Trả state phục hồi/poll của flow ẩn danh (idle|queued|watching|rating|completed) theo vai trò tương đối, không lộ partner; hết giờ xem được xử lý lazy (mở phase rating) ngay trong request này.

- handler: MovieMatchController.getAnonCurrent (apps/core-api/src/modules/movie-match/movie-match.controller.ts:131); operationId: MovieMatchController_getAnonCurrent
- auth: Bearer JWT
- response 200 MovieAnonStateDto: state:enum(idle|queued|watching|rating|completed), queuedAt:string, sessionId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string, expiresAt:string, myRating:enum(like|boring|rude), opponentRated:boolean, outcome:enum(matched|not_matched|cancelled|expired), partnerUserId:string
- FE callers: web apps/web/src/features/movie-match/anon-api.ts:38

### DELETE /movie-match/anon/current

Huỷ hàng đợi hoặc dismiss kết quả/phiên ẩn danh: phiên đang active bị chốt outcome=cancelled cho CẢ HAI và caller được giải phóng active-participant; không publish realtime, bên kia thấy qua poll.

- handler: MovieMatchController.dismissAnonCurrent (apps/core-api/src/modules/movie-match/movie-match.controller.ts:142); operationId: MovieMatchController_dismissAnonCurrent
- auth: Bearer JWT
- response 200 MovieAnonStateDto: state:enum(idle|queued|watching|rating|completed), queuedAt:string, sessionId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string, expiresAt:string, myRating:enum(like|boring|rude), opponentRated:boolean, outcome:enum(matched|not_matched|cancelled|expired), partnerUserId:string
- FE callers: web apps/web/src/features/movie-match/anon-api.ts:70

### PATCH /movie-match/anon/sessions/{id}/state

Cập nhật playback phiên ẩn danh (last-write-wins) chỉ khi còn trong phần xem (409 sau khi finish/hết giờ), khoá pessimistic_write dòng session rồi publish movie.state.changed.

- handler: MovieMatchController.updateAnonState (apps/core-api/src/modules/movie-match/movie-match.controller.ts:156); operationId: MovieMatchController_updateAnonState
- auth: Bearer JWT
- params: path:id*:string
- body UpdateMovieStateDto: positionSeconds*:number, isPlaying*:boolean
- response 200 MovieAnonStateDto: state:enum(idle|queued|watching|rating|completed), queuedAt:string, sessionId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string, expiresAt:string, myRating:enum(like|boring|rude), opponentRated:boolean, outcome:enum(matched|not_matched|cancelled|expired), partnerUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /movie-match/anon/sessions/{id}/finish

Bấm 'Kết thúc' phần xem chung: đặt watchEndedAt và dừng playback để mở phase rating cho cả hai, idempotent; publish movie.state.changed (isPlaying=false).

- handler: MovieMatchController.endAnonWatch (apps/core-api/src/modules/movie-match/movie-match.controller.ts:175); operationId: MovieMatchController_endAnonWatch
- auth: Bearer JWT
- params: path:id*:string
- response 200 MovieAnonStateDto: state:enum(idle|queued|watching|rating|completed), queuedAt:string, sessionId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string, expiresAt:string, myRating:enum(like|boring|rude), opponentRated:boolean, outcome:enum(matched|not_matched|cancelled|expired), partnerUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /movie-match/anon/sessions/{id}/rating

Chốt rating like/boring/rude đúng một lần (đổi giá trị → 409, chỉ mở sau watchEndedAt): non-like → not_matched ngay; cả hai like → FriendService.ensureFriendship(source=movie_match) cùng transaction rồi mới lộ partnerUserId.

- handler: MovieMatchController.rateAnon (apps/core-api/src/modules/movie-match/movie-match.controller.ts:191); operationId: MovieMatchController_rateAnon
- auth: Bearer JWT
- params: path:id*:string
- body RateMovieMatchDto: rating*:enum(like|boring|rude)
- response 200 MovieAnonStateDto: state:enum(idle|queued|watching|rating|completed), queuedAt:string, sessionId:string, videoUrl:string, positionSeconds:number, isPlaying:boolean, positionUpdatedAt:string, expiresAt:string, myRating:enum(like|boring|rude), opponentRated:boolean, outcome:enum(matched|not_matched|cancelled|expired), partnerUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /movie-match/anon/sessions/{id}/messages

Gửi chat ẩn danh trong phiên (HTTP 201): bắt buộc header Idempotency-Key (khoá DB movie:msg:{userId}:{key}, cùng key khác nội dung → 409), giới hạn MOVIE_MATCH_MESSAGE_MAX_LENGTH, không publish realtime (client poll).

- handler: MovieMatchController.sendAnonMessage (apps/core-api/src/modules/movie-match/movie-match.controller.ts:207); operationId: MovieMatchController_sendAnonMessage
- auth: Bearer JWT; requires Idempotency-Key header
- params: path:id*:string, header:Idempotency-Key*:string
- body SendMovieMessageDto: content*:string[maxLength=2000]
- response 200 MovieMessageDto: id:string, sender:enum(me|partner), content:string, sentAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /movie-match/anon/sessions/{id}/messages

Liệt kê chat của phiên ẩn danh theo cursor seq; chỉ participant đọc được, sender trả vai trò tương đối me|partner nên không lộ userId.

- handler: MovieMatchController.listAnonMessages (apps/core-api/src/modules/movie-match/movie-match.controller.ts:229); operationId: MovieMatchController_listAnonMessages
- auth: Bearer JWT
- params: path:id*:string, query:limit:number, query:cursor:string
- response 200 MovieMessagesPageDto: items:array<MovieMessageDto>, items[].id:string, items[].sender:enum(me|partner), items[].content:string, items[].sentAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /movie-match/anon/sessions/{id}/reactions

Gửi reaction emoji nổi (whitelist 4 emoji): realtime-only, không persist, không kèm userId, @Throttle 60 request/phút, trả 204 và publish movie.reaction.sent cho cả hai.

- handler: MovieMatchController.sendAnonReaction (apps/core-api/src/modules/movie-match/movie-match.controller.ts:250); operationId: MovieMatchController_sendAnonReaction
- auth: Bearer JWT; throttle: { default: { limit: 60, ttl: minutes(1) } }
- params: path:id*:string
- body ReactMovieDto: emoji*:enum(😂|😍|😱|👏)
- response 204: 
- FE callers: web apps/web/src/features/movie-match/anon-api.ts:160

