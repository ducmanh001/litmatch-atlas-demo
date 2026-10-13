# web route /movie-match

id: fe:route:web:/movie-match
file: apps/web/src/app/(app)/movie-match/page.tsx
layout: app (login required: AppChromeWithAuth)
reached source files: 21 (static import closure); data features: friend-chat, movie-match

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /friends | useFriends | friendChatKeys.friends |  |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:35 |
| GET /movie-match/anon/current | useCurrentMovieAnon | movieAnonKeys.current | (query) => isPollingMovieAnonState(query.state.data?.state) ? MOVIE_ANON_CURRENT_REFETCH_INTERVAL_MS : false |  | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/anon-api.ts:38 |
| GET /movie-match/anon/sessions/{id}/messages | useMovieAnonMessages | movieAnonKeys.messages(sessionId) | MOVIE_ANON_MESSAGES_REFETCH_INTERVAL_MS |  | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/anon-api.ts:112 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| form.onSubmit: (no literal label) | onSubmit | useSendMovieAnonMessage | POST /movie-match/anon/sessions/{id}/messages | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:92 |
| button.onClick: Kết thúc | () => finishWatch.mutate(undefined) | useFinishMovieAnonWatch | POST /movie-match/anon/sessions/{id}/finish | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:228 |
| (no matching UI element found; called from WatchingState) |  | useUpdateMovieAnonState | PATCH /movie-match/anon/sessions/{id}/state | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:243 |
| button.onClick: `Thả ${emoji}` | () => sendReaction.mutate(emoji, { onError: () => showToast('Không gửi được reaction.', 'warn'), }) | useSendMovieAnonReaction | POST /movie-match/anon/sessions/{id}/reactions | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:267 |
| (no matching UI element found; called from onRate) |  | useRateMovieAnon | POST /movie-match/anon/sessions/{id}/rating | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:289 |
| (no matching UI element found; called from restart) |  | useDismissMovieAnon | DELETE /movie-match/anon/current | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:453 |
| button.onClick: (no literal label) | () => joinQueue.mutate(undefined) | useJoinMovieAnonQueue | POST /movie-match/anon/queue | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:454 |
| (no matching UI element found; called from AnonMatchView) |  | useDismissMovieAnon | DELETE /movie-match/anon/current | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:459 |
| button.onClick: (no literal label) | () => joinQueue.mutate(undefined) | useJoinMovieAnonQueue | POST /movie-match/anon/queue | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/anon-match-view.tsx:483 |
| (no matching UI element found; called from FriendPicker) |  | useCreateSession | POST /movie-match/sessions | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/friend-picker.tsx:67 |

## Interactive elements (10)

- button.onClick Huỷ tìm kiếm -> onCancel (apps/web/src/features/movie-match/components/anon-match-view.tsx:63)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/movie-match/components/anon-match-view.tsx:133)
- button.onClick Kết thúc -> () => finishWatch.mutate(undefined) (apps/web/src/features/movie-match/components/anon-match-view.tsx:225)
- button.onClick `Thả ${emoji}` -> () => sendReaction.mutate(emoji, { onError: () => showToast('Không gử… (apps/web/src/features/movie-match/components/anon-match-view.tsx:262)
- button.onClick (no literal label) -> () => onRate('rude') (apps/web/src/features/movie-match/components/anon-match-view.tsx:325)
- button.onClick (no literal label) -> () => onRate('boring') (apps/web/src/features/movie-match/components/anon-match-view.tsx:334)
- button.onClick (no literal label) -> () => onRate('like') (apps/web/src/features/movie-match/components/anon-match-view.tsx:343)
- button.onClick Xem phim khác -> onRestart (apps/web/src/features/movie-match/components/anon-match-view.tsx:395)
- button.onClick (no literal label) -> () => joinQueue.mutate(undefined) (apps/web/src/features/movie-match/components/anon-match-view.tsx:480)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/movie-match/components/friend-picker.tsx:122)

## Realtime events listened

- movie.session.started (apps/web/src/app/(app)/movie-match/page.tsx:30)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)
- movie.reaction.sent (apps/web/src/features/movie-match/components/anon-match-view.tsx:201)
- movie.state.changed (apps/web/src/features/movie-match/components/anon-match-view.tsx:422)

## Navigation destinations

- /movie-match/{param} via router.replace (apps/web/src/app/(app)/movie-match/page.tsx:85)
- /home via link (apps/web/src/app/(app)/movie-match/page.tsx:49)
- /chat/${state.partnerUserId via link (apps/web/src/features/movie-match/components/anon-match-view.tsx:389)
- /friends via link (apps/web/src/features/movie-match/components/friend-picker.tsx:114)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- createSessionSchema (apps/web/src/features/movie-match/create-session-schema.ts:8): friendUserId=z.string().min(1, 'Chọn 1 người bạn để xem chung.'); videoUrl=z.string().min(1, 'Nhập link video YouTube.')

## Browser storage and cookies

- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (0)



## Tests

- apps/web/src/features/friend-chat/api.spec.ts
- apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx
- apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx
- apps/web/src/features/friend-chat/components/friends-directory.spec.tsx
- apps/web/src/features/friend-chat/components/message-list.spec.tsx
- apps/web/src/features/friend-chat/send-message-schema.spec.ts
- apps/web/src/features/movie-match/anon-api.spec.ts
- apps/web/src/features/movie-match/api.spec.ts
- apps/web/src/features/movie-match/components/anon-match-view.spec.tsx
- apps/web/src/features/movie-match/components/friend-picker.spec.tsx
- apps/web/src/features/movie-match/components/movie-session-view.spec.tsx
- apps/web/src/features/movie-match/youtube-url.spec.ts

## Backend

modules: [user](../modules/user.md), [friend](../modules/friend.md), [movie-match](../modules/movie-match.md)

service-map flows touching these endpoints: [movie-match-anon-chat-finish-reactions](../flows/movie-match-anon-chat-finish-reactions.md), [movie-match-anonymous](../flows/movie-match-anonymous.md), [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md), [movie-match-friend-session](../flows/movie-match-friend-session.md)
