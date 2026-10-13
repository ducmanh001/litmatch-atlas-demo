# web route /movie-match/:sessionId

id: fe:route:web:/movie-match/:sessionId
file: apps/web/src/app/(app)/movie-match/[sessionId]/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Movie Match
reached source files: 27 (static import closure); data features: friend-chat, movie-match

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /movie-match/sessions/{id} | useSession | movieMatchKeys.session(sessionId) | (query) => isActiveSession(query.state.data?.status) ? MOVIE_SESSION_REFETCH_INTERVAL_MS : false |  | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/api.ts:39 |
| GET /users/{id} | usePartnerProfile | friendChatKeys.partnerProfile(friendUserId) |  |  | [user](../api/user.md) | apps/web/src/features/friend-chat/api.ts:76 |
| GET /friends/{friendUserId}/conversation | useConversationWithFriend | friendChatKeys.conversation(friendUserId) |  |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:62 |
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /conversations/{id}/messages | useConversationMessages | friendChatKeys.messages(conversationId) | FRIEND_MESSAGES_REFETCH_INTERVAL_MS |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:91 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from MessageComposer) |  | useSendFriendMessage | POST /conversations/{id}/messages | [friend](../api/friend.md) | apps/web/src/features/friend-chat/components/message-composer.tsx:63 |
| (no matching UI element found; called from MovieSessionView) |  | useUpdateState | PATCH /movie-match/sessions/{id}/state | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/movie-session-view.tsx:144 |
| Button.onClick: (no literal label) | () => endSession.mutate() | useEndSession | POST /movie-match/sessions/{id}/end | [movie-match](../api/movie-match.md) | apps/web/src/features/movie-match/components/movie-session-view.tsx:157 |

Other write endpoints reachable (called outside a detected .mutate): POST /media/images/upload-intent (uploadImage apps/web/src/shared/media/image-upload.ts:31)

## Interactive elements (3)

- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/friend-chat/components/message-composer.tsx:83)
- button.onClick Đính kèm ảnh -> () => setShowImageInput((shown) => { if (shown) { setImageFile(null);… (apps/web/src/features/friend-chat/components/message-composer.tsx:85)
- Button.onClick (no literal label) -> () => endSession.mutate() (apps/web/src/features/movie-match/components/movie-session-view.tsx:151)

## Realtime events listened

- movie.state.changed (apps/web/src/features/movie-match/components/movie-session-view.tsx:51)
- movie.session.ended (apps/web/src/features/movie-match/components/movie-session-view.tsx:57)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)

## Navigation destinations

- /movie-match via link (apps/web/src/features/movie-match/components/movie-session-view.tsx:112)
- /wallet via link (apps/web/src/features/friend-chat/components/message-composer.tsx:118)
- /home via link (apps/web/src/features/movie-match/components/movie-session-view.tsx:118)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

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

modules: [movie-match](../modules/movie-match.md), [user](../modules/user.md), [friend](../modules/friend.md), [media](../modules/media.md)

service-map flows touching these endpoints: [movie-match-friend-session](../flows/movie-match-friend-session.md), [feed-post-with-image](../flows/feed-post-with-image.md), [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md), [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md), [message-push-notification](../flows/message-push-notification.md)
