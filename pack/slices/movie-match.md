# Feature slice: movie-match

id: slice:movie-match
status: implemented; owner: core-api/movie-match

Shared video playback and anonymous movie match

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/movie-match-service.md, docs/services/realtime-gateway.md
- registry verification tests: apps/core-api/src/modules/movie-match/movie-match.integration.spec.ts
- service docs: docs/services/movie-match-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/movie-match](../fe/route-web-movie-match.md) | web | GET /movie-match/anon/current; GET /movie-match/anon/sessions/{id}/messages; POST /movie-match/anon/sessions/{id}/messages; PATCH /movie-match/anon/sessions/{id}/state; POST /movie-match/anon/sessions/{id}/finish; POST /movie-match/anon/sessions/{id}/reactions … | app/(app)/movie-match/page.tsx |
| [/movie-match/:sessionId](../fe/route-web-movie-match-sessionid.md) | web | GET /movie-match/sessions/{id}; PATCH /movie-match/sessions/{id}/state; POST /movie-match/sessions/{id}/end | app/(app)/movie-match/[sessionId]/page.tsx |

FE features (directories): [web/friend-chat](../fe/feature-web-friend-chat.md), [web/movie-match](../fe/feature-web-movie-match.md)

User actions that mutate this feature's data:
- useSendMovieAnonMessage -> POST /movie-match/anon/sessions/{id}/messages (apps/web/src/features/movie-match/components/anon-match-view.tsx:92)
- useFinishMovieAnonWatch -> POST /movie-match/anon/sessions/{id}/finish (apps/web/src/features/movie-match/components/anon-match-view.tsx:228)
- useUpdateMovieAnonState -> PATCH /movie-match/anon/sessions/{id}/state (apps/web/src/features/movie-match/components/anon-match-view.tsx:243)
- useSendMovieAnonReaction -> POST /movie-match/anon/sessions/{id}/reactions (apps/web/src/features/movie-match/components/anon-match-view.tsx:267)
- useRateMovieAnon -> POST /movie-match/anon/sessions/{id}/rating (apps/web/src/features/movie-match/components/anon-match-view.tsx:289)
- useDismissMovieAnon -> DELETE /movie-match/anon/current (apps/web/src/features/movie-match/components/anon-match-view.tsx:453)
- useJoinMovieAnonQueue -> POST /movie-match/anon/queue (apps/web/src/features/movie-match/components/anon-match-view.tsx:454)
- useDismissMovieAnon -> DELETE /movie-match/anon/current (apps/web/src/features/movie-match/components/anon-match-view.tsx:459)
- useJoinMovieAnonQueue -> POST /movie-match/anon/queue (apps/web/src/features/movie-match/components/anon-match-view.tsx:483)
- useCreateSession -> POST /movie-match/sessions (apps/web/src/features/movie-match/components/friend-picker.tsx:67)
- useUpdateState -> PATCH /movie-match/sessions/{id}/state (apps/web/src/features/movie-match/components/movie-session-view.tsx:144)
- useEndSession -> POST /movie-match/sessions/{id}/end (apps/web/src/features/movie-match/components/movie-session-view.tsx:157)

Realtime events the frontend listens to for this feature:
- movie.session.started in /movie-match (apps/web/src/app/(app)/movie-match/page.tsx:30)
- movie.reaction.sent in /movie-match (apps/web/src/features/movie-match/components/anon-match-view.tsx:201)
- movie.state.changed in /movie-match (apps/web/src/features/movie-match/components/anon-match-view.tsx:422)
- movie.state.changed in /movie-match/:sessionId (apps/web/src/features/movie-match/components/movie-session-view.tsx:51)
- movie.session.ended in /movie-match/:sessionId (apps/web/src/features/movie-match/components/movie-session-view.tsx:57)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /movie-match/sessions ([movie-match](../api/movie-match.md)) | JWT |  |  | 1 |
| PATCH /movie-match/sessions/{id}/state ([movie-match](../api/movie-match.md)) | JWT |  |  | 0 |
| POST /movie-match/sessions/{id}/end ([movie-match](../api/movie-match.md)) | JWT |  |  | 0 |
| GET /movie-match/sessions/{id} ([movie-match](../api/movie-match.md)) | JWT |  |  | 1 |
| POST /movie-match/anon/queue ([movie-match](../api/movie-match.md)) | JWT |  |  | 1 |
| GET /movie-match/anon/current ([movie-match](../api/movie-match.md)) | JWT |  |  | 1 |
| DELETE /movie-match/anon/current ([movie-match](../api/movie-match.md)) | JWT |  |  | 1 |
| PATCH /movie-match/anon/sessions/{id}/state ([movie-match](../api/movie-match.md)) | JWT |  |  | 0 |
| POST /movie-match/anon/sessions/{id}/finish ([movie-match](../api/movie-match.md)) | JWT |  |  | 0 |
| POST /movie-match/anon/sessions/{id}/rating ([movie-match](../api/movie-match.md)) | JWT |  |  | 0 |
| POST /movie-match/anon/sessions/{id}/messages ([movie-match](../api/movie-match.md)) | JWT | required |  | 0 |
| GET /movie-match/anon/sessions/{id}/messages ([movie-match](../api/movie-match.md)) | JWT |  |  | 0 |
| POST /movie-match/anon/sessions/{id}/reactions ([movie-match](../api/movie-match.md)) | JWT |  | { default: { limit: 60, ttl: minutes(1) } } | 1 |


## 4. Backend flows (service map)

- [movie-match-anon-chat-finish-reactions](../flows/movie-match-anon-chat-finish-reactions.md): Xem phim ẩn danh: chat, reaction và kết thúc (6 endpoints of this feature)
- [movie-match-friend-session](../flows/movie-match-friend-session.md): Xem phim chung giữa bạn bè (4 endpoints of this feature)
- [movie-match-anonymous](../flows/movie-match-anonymous.md): Xem phim chung ẩn danh: hàng đợi, đồng bộ playback, rating (3 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `movie_match_queue_entries`, `movie_session_active_participants`, `movie_session_messages`, `movie_sessions` (see ../db/movie-match.md)
- status vocabularies enforced by CHECK:
  - movie_sessions: left | replaced
  - movie_sessions: active | ended

## 6. Events

- movie.session.started: producers movie-match.createSession; FE consumers 1
- movie.state.changed: producers movie-match.updateState, movie-match.publishAnonPlayback; FE consumers 2
- movie.session.ended: producers movie-match.endSession; FE consumers 1
- movie.reaction.sent: producers movie-match.sendAnonReaction; FE consumers 1

## 7. Errors

- error code groups: [MovieMatchErrors](../errors/moviematcherrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS`, `MOVIE_MATCH_MESSAGE_MAX_LENGTH`, `MOVIE_MATCH_ANON_VIDEO_URLS`, `MOVIE_MATCH_ANON_DURATION_SECONDS`, `MOVIE_MATCH_URL_MAX_LENGTH`, `MOVIE_MATCH_ALLOWED_VIDEO_HOSTS`, `REDIS_URL` (see ../config/)

## 9. Tests

- movie-match: 1 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx, apps/web/src/features/friend-chat/components/message-list.spec.tsx, apps/web/src/features/friend-chat/send-message-schema.spec.ts, apps/web/src/features/movie-match/anon-api.spec.ts, apps/web/src/features/movie-match/api.spec.ts, apps/web/src/features/movie-match/components/anon-match-view.spec.tsx, apps/web/src/features/movie-match/components/friend-picker.spec.tsx, apps/web/src/features/movie-match/components/movie-session-view.spec.tsx, apps/web/src/features/movie-match/youtube-url.spec.ts

## 10. Known issues (public subset of the ledger)

- P094 [open] Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention

See ../issues/index.md.
