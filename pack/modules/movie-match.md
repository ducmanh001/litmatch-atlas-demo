# Module movie-match

id: module:movie-match
kind: core-api module
code: apps/core-api/src/modules/movie-match

Sở hữu phiên xem chung 2 người (friend mode: 2 bạn tự chọn URL YouTube; anonymous mode: hàng đợi ghép người lạ, server chọn video, chat + rating, chỉ mutual-like mới mở Friendship) và là nguồn sự thật duy nhất của playback state. KHÔNG stream/proxy video (client tự phát từ URL), KHÔNG có SFU/LiveKit hay tiền, KHÔNG sở hữu quan hệ bạn (Friend) hay block/report (Safety).

## Facts

- tables owned: movie_match_queue_entries, movie_session_active_participants, movie_session_messages, movie_sessions
- API: 13 endpoints, 0 public, 0 admin-only, 1 require Idempotency-Key, 1 with own throttle
- publishes realtime: movie.session.started, movie.state.changed, movie.session.ended, movie.reaction.sent
- env vars read: MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS, MOVIE_MATCH_MESSAGE_MAX_LENGTH, MOVIE_MATCH_ANON_VIDEO_URLS, MOVIE_MATCH_ANON_DURATION_SECONDS, MOVIE_MATCH_URL_MAX_LENGTH, MOVIE_MATCH_ALLOWED_VIDEO_HOSTS, REDIS_URL
- error codes: 11 in MovieMatchErrors
- tests: 1 unit spec files, 1 integration spec files
- history: 6 commits (0 fixes), last 2026-08-13
- docs: docs/services/movie-match-service.md (last changed 2026-10-09)
- features: movie-match

## Where to read next

- tables: ../db/movie-match.md
- API contract: ../api/movie-match.md
- why it talks to other services: ../service-map/movie-match.md and ../service-map/movie-match-techniques.md
- error codes: ../errors/moviematcherrors.md
- flows involving it: [movie-match-anon-chat-finish-reactions](../flows/movie-match-anon-chat-finish-reactions.md), [movie-match-anonymous](../flows/movie-match-anonymous.md), [movie-match-friend-session](../flows/movie-match-friend-session.md)

## Linked issues (heuristic)

- P094 [open] Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention

See ../issues/index.md.
