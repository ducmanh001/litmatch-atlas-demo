# Frontend state, cache and data fetching

id: fe:state

Server state is cached by TanStack Query. There is no global client store; browser storage holds only tokens/preferences (below).

## QueryClient configuration

- admin: new QueryClient({ defaultOptions: { queries: { staleTime: 30_000, retry: 1, refetchOnWindowFocus: false, }, }, }) (apps/admin/src/app/providers.tsx:10)
- web: new QueryClient({ defaultOptions: { queries: { staleTime: 30_000, retry: shouldRetryQuery, refetchOnWindowFocus: false, // Keep browser online recovery for public routes and sessions whose socket did not // reconnect. AppChrome still resynchronizes durable state after a socket reconnect. refetchOnReconnect: shouldRefetchOnReconnect, // High-frequency session fallbacks are for the active tab only.  (apps/web/src/app/providers.tsx:20)

## Query key factories

### avatarKeys (apps/web/src/features/avatar/api.ts)

```ts
export const avatarKeys = {
  all: ['avatar'] as const,
  me: ['avatar', 'me'] as const,
  catalog: ['avatar', 'catalog'] as const,
  myItems: ['avatar', 'my-items'] as const,
};
```

### discoveryKeys (apps/web/src/features/discovery/api.ts)

```ts
export const discoveryKeys = {
  browse: (filter: BrowseFilter) => ['discovery', 'browse', filter] as const,
  nearby: (filter: BrowseFilter) => ['discovery', 'nearby', filter] as const,
};
```

### feedKeys (apps/web/src/features/feed/api.ts)

```ts
export const feedKeys = {
  list: ['feed', 'list'] as const,
  detail: (postId: string) => ['feed', 'detail', postId] as const,
  comments: (postId: string) => ['feed', 'comments', postId] as const,
  reaction: (postId: string) => ['feed', 'reaction', postId] as const,
  userTimeline: (userId: string) => ['feed', 'user-timeline', userId] as const,
};
```

### friendChatKeys (apps/web/src/features/friend-chat/api.ts)

```ts
export const friendChatKeys = {
  friends: ['friend-chat', 'friends'] as const,
  connections: ['friend-chat', 'connections'] as const,
  conversation: (friendUserId: string) =>
    ['friend-chat', 'conversation', friendUserId] as const,
  partnerProfile: (friendUserId: string) =>
    ['friend-chat', 'partner-profile', friendUserId] as const,
  messages: (conversationId: string) =>
    ['friend-chat', 'messages', conversationId] as const,
};
```

### matchingKeys (apps/web/src/features/matching/api.ts)

```ts
export const matchingKeys = {
  all: ['matching'] as const,
  current: ['matching', 'ticket', 'current'] as const,
  ticket: (id: string) => ['matching', 'ticket', id] as const,
};
```

### inviteKeys (apps/web/src/features/matching/invite-api.ts)

```ts
export const inviteKeys = {
  received: ['matching', 'invites', 'received'] as const,
};
```

### movieAnonKeys (apps/web/src/features/movie-match/anon-api.ts)

```ts
export const movieAnonKeys = {
  current: ['movie-match', 'anon', 'current'] as const,
  messages: (sessionId: string) =>
    ['movie-match', 'anon', 'messages', sessionId] as const,
};
```

### movieMatchKeys (apps/web/src/features/movie-match/api.ts)

```ts
export const movieMatchKeys = {
  session: (sessionId: string) =>
    ['movie-match', 'session', sessionId] as const,
};
```

### notificationKeys (apps/web/src/features/notification/api.ts)

```ts
export const notificationKeys = {
  all: ['notifications'] as const,
  list: ['notifications', 'list'] as const,
  unreadCount: ['notifications', 'unread-count'] as const,
};
```

### palmMatchKeys (apps/web/src/features/palm-match/api.ts)

```ts
export const palmMatchKeys = {
  current: ['palm-match', 'current'] as const,
};
```

### partyRoomKeys (apps/web/src/features/party-room/api.ts)

```ts
export const partyRoomKeys = {
  list: ['party-room', 'list'] as const,
  detail: (roomId: string) => ['party-room', 'detail', roomId] as const,
  comments: (roomId: string) => ['party-room', 'comments', roomId] as const,
  profile: (userId: string) => ['party-room', 'profile', userId] as const,
};
```

### privacyKeys (apps/web/src/features/privacy/api.ts)

```ts
export const privacyKeys = {
  settings: ['privacy', 'settings'] as const,
};
```

### profileKeys (apps/web/src/features/profile/api.ts)

```ts
export const profileKeys = {
  public: (id: string) => ['profile', 'public', id] as const,
  presence: (id: string) => ['profile', 'presence', id] as const,
  actions: (id: string) => ['profile', 'actions', id] as const,
  followCounts: (id: string) => ['profile', 'follow-counts', id] as const,
  followList: (id: string, kind: FollowListKind) =>
    ['profile', 'follow-list', id, kind] as const,
};
```

### shortVideoKeys (apps/web/src/features/short-video/api.ts)

```ts
export const shortVideoKeys = {
  listAll: ['short-video', 'list'] as const,
  list: (sort: VideoFeedSort, scope: VideoFeedScope) =>
    ['short-video', 'list', sort, scope] as const,
  comments: (videoId: string) => ['short-video', 'comments', videoId] as const,
};
```

### soulMatchKeys (apps/web/src/features/soul-match/api.ts)

```ts
export const soulMatchKeys = {
  session: (id: string) => ['soul-match', 'session', id] as const,
  messages: (id: string) => ['soul-match', 'messages', id] as const,
  partner: (id: string) => ['soul-match', 'partner', id] as const,
};
```

### supportKeys (apps/web/src/features/support/api.ts)

```ts
export const supportKeys = {
  mine: ['support', 'tickets', 'mine'] as const,
};
```

### voiceMatchKeys (apps/web/src/features/voice-match/api.ts)

```ts
export const voiceMatchKeys = {
  call: (id: string) => ['voice-match', 'call', id] as const,
};
```

### walletKeys (apps/web/src/features/wallet/api.ts)

```ts
export const walletKeys = {
  wallet: ['wallet', 'me'] as const,
  iapProducts: ['wallet', 'iap-products'] as const,
  payosPackages: ['wallet', 'payos-packages'] as const,
  payosOrder: (orderId: string) => ['wallet', 'payos-order', orderId] as const,
  vipPlans: ['wallet', 'vip-plans'] as const,
};
```

### capabilityKeys (apps/web/src/shared/capabilities/api.ts)

```ts
export const capabilityKeys = {
  runtime: ['capabilities', 'runtime'] as const,
};
```

## Invalidations by mutation hooks

- web/useClaimAvatarItem (apps/web/src/features/avatar/api.ts:51) -> invalidateQueries({ queryKey: avatarKeys.myItems })
- web/useBuyAvatarItem (apps/web/src/features/avatar/api.ts:66) -> invalidateQueries({ queryKey: avatarKeys.myItems }); invalidateQueries({ queryKey: ['wallet', 'me'] })
- web/useEquipAvatarItem (apps/web/src/features/avatar/api.ts:86) -> invalidateQueries({ queryKey: avatarKeys.me })
- web/useSetNearbyVisible (apps/web/src/features/discovery/api.ts:74) -> invalidateQueries({ queryKey: ['discovery'] })
- web/useCreatePost (apps/web/src/features/feed/api.ts:63) -> invalidateQueries({ queryKey: feedKeys.list })
- web/useDeletePost (apps/web/src/features/feed/api.ts:82) -> removeQueries({ queryKey: feedKeys.detail(postId), exact: true, }); removeQueries({ queryKey: feedKeys.comments(postId), exact: true, }); removeQueries({ queryKey: feedKeys.reaction(postId), exact: true, }); invalidateQueries({ queryKey: feedKeys.list })
- web/useCreateComment (apps/web/src/features/feed/api.ts:137) -> invalidateQueries({ queryKey: feedKeys.comments(postId), }); invalidateQueries({ queryKey: feedKeys.detail(postId), }); invalidateQueries({ queryKey: feedKeys.list })
- web/useSetReaction (apps/web/src/features/feed/api.ts:171) -> invalidateQueries({ queryKey: feedKeys.reaction(postId), }); invalidateQueries({ queryKey: feedKeys.list }); invalidateQueries({ queryKey: feedKeys.detail(postId), })
- web/useSendFriendMessage (apps/web/src/features/friend-chat/api.ts:107) -> invalidateQueries({ queryKey: friendChatKeys.messages(conversationId), })
- web/useMarkConversationRead (apps/web/src/features/friend-chat/api.ts:136) -> invalidateQueries({ queryKey: friendChatKeys.friends, })
- web/useMuteConversation (apps/web/src/features/friend-chat/api.ts:154) -> invalidateQueries({ queryKey: friendChatKeys.friends, })
- web/useJoinQueue (apps/web/src/features/matching/api.ts:40) -> invalidateQueries({ queryKey: matchingKeys.current })
- web/useCancelTicket (apps/web/src/features/matching/api.ts:94) -> setQueryData(matchingKeys.current); invalidateQueries({ queryKey: matchingKeys.ticket(ticketId), }); invalidateQueries({ queryKey: matchingKeys.current })
- web/useConfirmTicket (apps/web/src/features/matching/api.ts:115) -> invalidateQueries({ queryKey: matchingKeys.ticket(ticketId), }); invalidateQueries({ queryKey: matchingKeys.current })
- web/useSpeedup (apps/web/src/features/matching/api.ts:134) -> invalidateQueries({ queryKey: matchingKeys.ticket(ticketId), })
- web/useAcceptInvite (apps/web/src/features/matching/invite-api.ts:41) -> invalidateQueries({ queryKey: inviteKeys.received })
- web/useDeclineInvite (apps/web/src/features/matching/invite-api.ts:56) -> invalidateQueries({ queryKey: inviteKeys.received })
- web/useAnonStateMutation (apps/web/src/features/movie-match/anon-api.ts:48) -> setQueryData(movieAnonKeys.current); invalidateQueries({ queryKey: movieAnonKeys.current })
- web/useSendMovieAnonMessage (apps/web/src/features/movie-match/anon-api.ts:130) -> invalidateQueries({ queryKey: movieAnonKeys.messages(sessionId), })
- web/useUpdateState (apps/web/src/features/movie-match/api.ts:51) -> invalidateQueries({ queryKey: movieMatchKeys.session(sessionId), })
- web/useEndSession (apps/web/src/features/movie-match/api.ts:75) -> invalidateQueries({ queryKey: movieMatchKeys.session(sessionId), })
- web/useMarkNotificationRead (apps/web/src/features/notification/api.ts:77) -> invalidateQueries({ queryKey: notificationKeys.all })
- web/usePalmStateMutation (apps/web/src/features/palm-match/api.ts:37) -> setQueryData(palmMatchKeys.current); invalidateQueries({ queryKey: palmMatchKeys.current })
- web/useCreateRoom (apps/web/src/features/party-room/api.ts:83) -> invalidateQueries({ queryKey: partyRoomKeys.list })
- web/useJoinRoom (apps/web/src/features/party-room/api.ts:120) -> setQueryData(partyRoomKeys.detail(roomId)); invalidateQueries({ queryKey: partyRoomKeys.list })
- web/useLeaveRoom (apps/web/src/features/party-room/api.ts:188) -> invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), }); invalidateQueries({ queryKey: partyRoomKeys.list })
- web/useChangeRole (apps/web/src/features/party-room/api.ts:205) -> invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), })
- web/useInviteSpeaker (apps/web/src/features/party-room/api.ts:227) -> invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), })
- web/useAcceptSpeakerInvite (apps/web/src/features/party-room/api.ts:245) -> invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), })
- web/useDeclineSpeakerInvite (apps/web/src/features/party-room/api.ts:263) -> invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), })
- web/useUpdatePrivacySettings (apps/web/src/features/privacy/api.ts:27) -> invalidateQueries({ queryKey: privacyKeys.settings })
- web/useFollowProfile (apps/web/src/features/profile/api.ts:107) -> invalidateQueries({ queryKey: profileKeys.actions(profileUserId), }); invalidateQueries({ queryKey: ['profile', 'follow-counts'], }); invalidateQueries({ queryKey: ['profile', 'follow-list'], }); invalidateQueries({ queryKey: ['friend-chat', 'connections'], })
- web/useUpdateProfile (apps/web/src/features/profile/api.ts:186) -> invalidateQueries({ queryKey: currentUserKey })
- web/useCreateVideoComment (apps/web/src/features/short-video/api.ts:88) -> invalidateQueries({ queryKey: shortVideoKeys.comments(videoId), }); invalidateQueries({ queryKey: shortVideoKeys.listAll })
- web/useSetVideoReaction (apps/web/src/features/short-video/api.ts:107) -> invalidateQueries({ queryKey: shortVideoKeys.listAll })
- web/useSendSoulMessage (apps/web/src/features/soul-match/api.ts:74) -> invalidateQueries({ queryKey: soulMatchKeys.messages(sessionId), }); invalidateQueries({ queryKey: soulMatchKeys.session(sessionId), })
- web/useRateSession (apps/web/src/features/soul-match/api.ts:101) -> invalidateQueries({ queryKey: soulMatchKeys.session(sessionId), })
- web/useEndSoulSession (apps/web/src/features/soul-match/api.ts:122) -> invalidateQueries({ queryKey: soulMatchKeys.session(sessionId), })
- web/useCreateSupportTicket (apps/web/src/features/support/api.ts:27) -> invalidateQueries({ queryKey: supportKeys.mine })
- web/useJoinCall (apps/web/src/features/voice-match/api.ts:24) -> setQueryData(voiceMatchKeys.call(joined.call.id))
- web/useJoinFriendCall (apps/web/src/features/voice-match/api.ts:47) -> setQueryData(voiceMatchKeys.call(joined.call.id))
- web/useEndCall (apps/web/src/features/voice-match/api.ts:87) -> invalidateQueries({ queryKey: voiceMatchKeys.call(callId), })
- web/useEndVoiceMatch (apps/web/src/features/voice-match/api.ts:105) -> setQueryData(matchingKeys.current); invalidateQueries({ queryKey: matchingKeys.all })
- web/usePurchaseVip (apps/web/src/features/wallet/api.ts:96) -> invalidateQueries({ queryKey: walletKeys.wallet })
- web/useVerifyIap (apps/web/src/features/wallet/api.ts:114) -> invalidateQueries({ queryKey: walletKeys.wallet })
- admin/useSetIapProductActive (apps/admin/src/features/config/api.ts:29) -> invalidateQueries({ queryKey: configKeys.catalog })
- admin/useSetVipPlanActive (apps/admin/src/features/config/api.ts:47) -> invalidateQueries({ queryKey: configKeys.catalog })
- admin/useRefundTransaction (apps/admin/src/features/economy/api.ts:53) -> invalidateQueries({ queryKey: economyKeys.wallet(userId) }); invalidateQueries({ queryKey: economyKeys.transactions(userId), })
- admin/useCreateGift (apps/admin/src/features/gifts/api.ts:26) -> invalidateQueries({ queryKey: giftsKeys.all })
- admin/useUpdateGift (apps/admin/src/features/gifts/api.ts:37) -> invalidateQueries({ queryKey: giftsKeys.all })
- admin/useResolveReport (apps/admin/src/features/moderation/api.ts:40) -> invalidateQueries({ queryKey: reportsKeys.all })
- admin/useDismissReport (apps/admin/src/features/moderation/api.ts:54) -> invalidateQueries({ queryKey: reportsKeys.all })
- admin/useBulkReportAction (apps/admin/src/features/moderation/api.ts:86) -> invalidateQueries({ queryKey: reportsKeys.all })
- admin/useUpdateSupportTicket (apps/admin/src/features/moderation/support-api.ts:29) -> invalidateQueries({ queryKey: adminSupportKeys.all })
- admin/useApproveVideo (apps/admin/src/features/moderation/videos-api.ts:47) -> invalidateQueries({ queryKey: pendingVideosKeys.all })
- admin/useRejectVideo (apps/admin/src/features/moderation/videos-api.ts:61) -> invalidateQueries({ queryKey: pendingVideosKeys.all })
- admin/useRemoveVideo (apps/admin/src/features/moderation/videos-api.ts:75) -> invalidateQueries({ queryKey: pendingVideosKeys.all })
- admin/useSetRolePermission (apps/admin/src/features/permissions/api.ts:26) -> invalidateQueries({ queryKey: permissionKeys.matrix })
- admin/useSetStaffRole (apps/admin/src/features/permissions/api.ts:56) -> invalidateQueries({ queryKey: permissionKeys.staff })
- admin/useCloseRoom (apps/admin/src/features/rooms/api.ts:49) -> invalidateQueries({ queryKey: roomsKeys.admin }); invalidateQueries({ queryKey: roomsKeys.all })
- admin/useBanUser (apps/admin/src/features/users/api.ts:45) -> invalidateQueries({ queryKey: usersKeys.all })
- admin/useUnbanUser (apps/admin/src/features/users/api.ts:58) -> invalidateQueries({ queryKey: usersKeys.all })

## Realtime events and the routes that listen

- friend.message: /chat/:friendUserId
- (dynamic: event): /chat/:friendUserId, /chat/:friendUserId/call, /matching, /matching/soul/:sessionId, /matching/voice/:sessionId, /movie-match, /movie-match/:sessionId, /party/:roomId
- call.ended: /chat/:friendUserId/call, /matching/voice/:sessionId
- match.confirmed: /matching
- soul.message: /matching/soul/:sessionId
- soul.matched: /matching/soul/:sessionId
- soul.ended: /matching/soul/:sessionId
- movie.session.started: /movie-match
- movie.reaction.sent: /movie-match
- movie.state.changed: /movie-match, /movie-match/:sessionId
- movie.session.ended: /movie-match/:sessionId
- party.member.joined: /party/:roomId
- party.member.left: /party/:roomId
- party.member.disconnected: /party/:roomId
- party.member.reconnected: /party/:roomId
- party.role.changed: /party/:roomId
- party.speaker.invite.received: /party/:roomId
- party.room.closed: /party/:roomId
- party.host.disconnected: /party/:roomId
- party.host.reconnected: /party/:roomId
- gift.sent: /party/:roomId
- party.comment.created: /party/:roomId

## Cross-cutting pieces (doc comments from code)

### apps/web/src/shared/api/client.ts

- CSRF_TOKEN_STORAGE_KEY (line 11): Key localStorage cho csrfToken (docs/12 § 12.6, ADR 0007) — refresh token là cookie httpOnly, không ở đây.
- apiClient (line 21): Client REST duy nhất của app (docs/12 § 12.3). Storage adapter SSR-safe nên module này import được từ server component, nhưng session chỉ tồn tại phía browser.

### apps/web/src/shared/api/query-retry-policy.ts

- shouldRetryQuery (line 11): React Query retry policy for REST requests. A retry is useful for a transient network/server failure, but retrying a 4xx response multiplies traffic for an error the client cannot repair. Keep the global budget at one retry; feature-specific mutations can still opt out when their operation is not safe to replay.
- shouldRefetchOnReconnect (line 24): Avoid the one overlapping online refetch already covered by socket REST resync.

### apps/web/src/app/providers.tsx

- createQueryClient (line 19): Default server-state khai đúng 1 lần (docs/13 § 13.4); per-query override phải có lý do.

### apps/web/src/shared/realtime/socket.ts

- socket (line 24): MỘT socket instance cho cả app (docs/13 § 13.8) — component không tự io(), chỉ đi qua subscribeRealtime/onReconnected. Socket là kênh DELTA: sau reconnect phải refetch state nền qua REST (onReconnected → invalidate query), không đoán state từ event bị miss.
- connectRealtime (line 74): Gọi khi user đã đăng nhập và vào vùng realtime; idempotent.
- disconnectRealtime (line 80): Gọi khi logout — đóng kết nối và quên instance (token user khác không dùng lại socket cũ).
- isRealtimeConnected (line 89): Snapshot cho query fallback — realtime là delta, còn REST vẫn là reconciliation backstop.
- subscribeRealtimeConnection (line 94): Subscribe trạng thái transport để các query đổi tần suất fallback mà không polling mù.
- subscribeRealtime (line 100): Đăng ký listener theo event đã khai trong common-dtos — trả cleanup, hook PHẢI gọi khi unmount.
- onReconnected (line 110): Đăng ký callback sau khi socket nối LẠI (không phải lần connect đầu) — nơi refetch REST.

### apps/web/src/shared/realtime/use-realtime-connection.ts

- useRealtimeConnection (line 8): Transport khỏe thì giảm tần suất reconciliation; mất socket thì poll sát hơn.

### apps/web/src/shared/auth/auth-gate.tsx

- AuthGate (line 17): Guard client cho nhóm route (app) — docs/12 § 12.5. Chờ mount xong mới quyết định (tránh hydration mismatch: server snapshot luôn unauthenticated). Ẩn UI không phải bảo mật (docs/13 § 13.11) — enforcement thật ở backend.

### apps/web/src/shared/auth/use-session.ts

- useSessionStatus (line 13): Trạng thái đăng nhập phản ứng theo TokenStore. Server snapshot luôn `false` (session chỉ có ở browser) — component dùng hook này phải chịu được first render unauthenticated.

### apps/web/src/shared/auth/use-current-user.ts

- currentUserKey (line 13): "Mình là ai" — dùng để so `senderUserId` trong các luồng chat không ẩn danh (Friend chat).

### apps/web/src/shared/capabilities/api.ts



### libs/api-client/src/lib/token-store.ts

- browserCsrfTokenStorage (line 40): localStorage adapter — SSR-safe: trước khi có `window` (Next server render) mọi thao tác là no-op trả null, vì session chỉ tồn tại phía browser (docs/12 § 12.6).
