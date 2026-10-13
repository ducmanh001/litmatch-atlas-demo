# web feature movie-match

id: fe:feature:web:movie-match
directory: apps/web/src/features/movie-match/ (8 source files, 4 components, 6 spec files)

routes whose data comes from this feature: [/movie-match](route-web-movie-match.md), [/movie-match/:sessionId](route-web-movie-match-sessionid.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useCurrentMovieAnon | GET /movie-match/anon/current | movieAnonKeys.current | (query) => isPollingMovieAnonState(query.state.data?.state) ? MOVIE_ANON_CURRENT_REFETCH_INTERVAL_MS : false |  |  | apps/web/src/features/movie-match/anon-api.ts:34 |
| useAnonStateMutation | (none directly) |  |  |  | setQueryData(movieAnonKeys.current); invalidateQueries({ queryKey: movieAnonKeys.current }) | apps/web/src/features/movie-match/anon-api.ts:48 |
| useJoinMovieAnonQueue | POST /movie-match/anon/queue |  |  |  |  | apps/web/src/features/movie-match/anon-api.ts:61 |
| useDismissMovieAnon | DELETE /movie-match/anon/current |  |  |  |  | apps/web/src/features/movie-match/anon-api.ts:68 |
| useUpdateMovieAnonState | PATCH /movie-match/anon/sessions/{id}/state |  |  |  |  | apps/web/src/features/movie-match/anon-api.ts:75 |
| useFinishMovieAnonWatch | POST /movie-match/anon/sessions/{id}/finish |  |  |  |  | apps/web/src/features/movie-match/anon-api.ts:87 |
| useRateMovieAnon | POST /movie-match/anon/sessions/{id}/rating |  |  |  |  | apps/web/src/features/movie-match/anon-api.ts:97 |
| useMovieAnonMessages | GET /movie-match/anon/sessions/{id}/messages | movieAnonKeys.messages(sessionId) | MOVIE_ANON_MESSAGES_REFETCH_INTERVAL_MS |  |  | apps/web/src/features/movie-match/anon-api.ts:108 |
| useSendMovieAnonMessage | POST /movie-match/anon/sessions/{id}/messages |  |  |  | invalidateQueries({ queryKey: movieAnonKeys.messages(sessionId), }) | apps/web/src/features/movie-match/anon-api.ts:130 |
| useSendMovieAnonReaction | POST /movie-match/anon/sessions/{id}/reactions |  |  |  |  | apps/web/src/features/movie-match/anon-api.ts:157 |
| useCreateSession | POST /movie-match/sessions |  |  |  |  | apps/web/src/features/movie-match/api.ts:23 |
| useSession | GET /movie-match/sessions/{id} | movieMatchKeys.session(sessionId) | (query) => isActiveSession(query.state.data?.status) ? MOVIE_SESSION_REFETCH_INTERVAL_MS : false |  |  | apps/web/src/features/movie-match/api.ts:35 |
| useUpdateState | PATCH /movie-match/sessions/{id}/state |  |  |  | invalidateQueries({ queryKey: movieMatchKeys.session(sessionId), }) | apps/web/src/features/movie-match/api.ts:51 |
| useEndSession | POST /movie-match/sessions/{id}/end |  |  |  | invalidateQueries({ queryKey: movieMatchKeys.session(sessionId), }) | apps/web/src/features/movie-match/api.ts:75 |

## Components

- apps/web/src/features/movie-match/components/anon-match-view.tsx: 9 interactive elements, uses useMovieAnonMessages, useSendMovieAnonMessage, useIdempotencyKey, useState, useEffect, useUpdateMovieAnonState, useFinishMovieAnonWatch, useSendMovieAnonReaction, useRef, useRealtimeEvent, useRateMovieAnon, useCurrentMovieAnon
- apps/web/src/features/movie-match/components/friend-picker.tsx: 1 interactive elements, uses useForm, useCreateSession
- apps/web/src/features/movie-match/components/movie-session-view.tsx: 1 interactive elements, uses useQueryClient, useSession, useUpdateState, useEndSession, useState, useRealtimeEvent, useEffect
- apps/web/src/features/movie-match/components/youtube-player.tsx: 0 interactive elements, uses useId, useRef, useState, useEffect

## Validation schemas

- createSessionSchema (apps/web/src/features/movie-match/create-session-schema.ts:8): friendUserId=z.string().min(1, 'Chọn 1 người bạn để xem chung.'); videoUrl=z.string().min(1, 'Nhập link video YouTube.')

## Realtime events

- movie.reaction.sent apps/web/src/features/movie-match/components/anon-match-view.tsx:201
- movie.state.changed apps/web/src/features/movie-match/components/anon-match-view.tsx:422
- movie.state.changed apps/web/src/features/movie-match/components/movie-session-view.tsx:51
- movie.session.ended apps/web/src/features/movie-match/components/movie-session-view.tsx:57

## Tests

- apps/web/src/features/movie-match/anon-api.spec.ts
- apps/web/src/features/movie-match/api.spec.ts
- apps/web/src/features/movie-match/components/anon-match-view.spec.tsx
- apps/web/src/features/movie-match/components/friend-picker.spec.tsx
- apps/web/src/features/movie-match/components/movie-session-view.spec.tsx
- apps/web/src/features/movie-match/youtube-url.spec.ts
