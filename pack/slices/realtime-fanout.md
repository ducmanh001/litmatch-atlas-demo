# Feature slice: realtime-fanout

id: slice:realtime-fanout
status: implemented; owner: signaling-gateway with core-api producers

Authenticated Socket.IO fanout and distributed connection quota through Redis

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: libs/common-dtos/src/lib/realtime-events.ts, docs/services/realtime-gateway.md, specs/realtime.asyncapi.yaml
- registry verification tests: apps/signaling-gateway/src/app/signaling.integration.spec.ts, apps/signaling-gateway/src/app/signaling.horizontal-scale.integration.spec.ts
- service docs: docs/services/realtime-gateway.md, docs/services/soul-match-service.md, docs/services/matching-service.md, docs/services/calling-service.md, docs/services/friend-service.md, docs/services/party-room-service.md, docs/services/gift-service.md, docs/services/movie-match-service.md, docs/services/mini-game-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/movie-match](../fe/route-web-movie-match.md) | web | GET /friends; GET /movie-match/anon/current; GET /movie-match/anon/sessions/{id}/messages; POST /movie-match/anon/sessions/{id}/messages; PATCH /movie-match/anon/sessions/{id}/state; POST /movie-match/anon/sessions/{id}/finish … | app/(app)/movie-match/page.tsx |
| [/party/:roomId](../fe/route-web-party-roomid.md) | web | GET /party/rooms/{id}; GET /gifts; GET /party/rooms/{id}/comments; POST /party/rooms/{id}/leave; POST /party/rooms/{id}/speaker-invite/accept; POST /party/rooms/{id}/speaker-invite/decline … | app/(app)/party/[roomId]/page.tsx |
| [/matching](../fe/route-web-matching.md) | web | GET /matching/invites; GET /matching/tickets/current; GET /matching/tickets/{id}; POST /matching/invites/{id}/accept; POST /matching/invites/{id}/decline; DELETE /matching/tickets/{id} … | app/(app)/matching/page.tsx |
| [/chat/:friendUserId](../fe/route-web-chat-frienduserid.md) | web | GET /friends; GET /friends/{friendUserId}/conversation; GET /conversations/{id}/messages; POST /calling/friends/{friendUserId}/join; POST /conversations/{id}/read; POST /conversations/{id}/mute … | app/(app)/chat/[friendUserId]/page.tsx |
| [/matching/soul/:sessionId](../fe/route-web-matching-soul-sessionid.md) | web | GET /soul-match/sessions/{id}; GET /soul-match/sessions/{id}/messages; GET /soul-match/sessions/{id}/partner; POST /soul-match/sessions/{id}/end; POST /soul-match/sessions/{id}/messages; POST /soul-match/sessions/{id}/rating | app/(app)/matching/soul/[sessionId]/page.tsx |
| [/matching/voice/:sessionId](../fe/route-web-matching-voice-sessionid.md) | web | GET /calling/calls/{id}; POST /calling/calls/{id}/end; POST /calling/match-sessions/{matchSessionId}/end; POST /calling/calls/{id}/like; POST /calling/match-sessions/{matchSessionId}/join; POST /calling/friends/{friendUserId}/join | app/(app)/matching/voice/[sessionId]/page.tsx |
| [/movie-match/:sessionId](../fe/route-web-movie-match-sessionid.md) | web | GET /movie-match/sessions/{id}; GET /friends/{friendUserId}/conversation; GET /conversations/{id}/messages; POST /conversations/{id}/messages; PATCH /movie-match/sessions/{id}/state; POST /movie-match/sessions/{id}/end | app/(app)/movie-match/[sessionId]/page.tsx |
| [/users/:id](../fe/route-web-users-id.md) | web | GET /profiles/{profileUserId}/actions; GET /gifts; POST /profiles/{profileUserId}/follow; DELETE /profiles/{profileUserId}/follow; POST /profiles/{profileUserId}/conversation; POST /profiles/{profileUserId}/gifts | app/(app)/users/[id]/page.tsx |
| [/users/:id/followers](../fe/route-web-users-id-followers.md) | web | GET /profiles/{profileUserId}/follow-counts; GET /profiles/{profileUserId}/followers; GET /profiles/{profileUserId}/following; POST /profiles/{profileUserId}/follow; DELETE /profiles/{profileUserId}/follow | app/(app)/users/[id]/followers/page.tsx |
| [/users/:id/following](../fe/route-web-users-id-following.md) | web | GET /profiles/{profileUserId}/follow-counts; GET /profiles/{profileUserId}/followers; GET /profiles/{profileUserId}/following; POST /profiles/{profileUserId}/follow; DELETE /profiles/{profileUserId}/follow | app/(app)/users/[id]/following/page.tsx |
| [/chat/:friendUserId/call](../fe/route-web-chat-frienduserid-call.md) | web | GET /calling/calls/{id}; POST /calling/calls/{id}/end; POST /calling/match-sessions/{matchSessionId}/join; POST /calling/friends/{friendUserId}/join | app/(app)/chat/[friendUserId]/call/page.tsx |
| [/friends](../fe/route-web-friends.md) | web | GET /friends/connections; GET /friends; POST /profiles/{profileUserId}/conversation | app/(app)/friends/page.tsx |

FE features (directories): [web/friend-chat](../fe/feature-web-friend-chat.md), [web/movie-match](../fe/feature-web-movie-match.md), [web/party-room](../fe/feature-web-party-room.md), [web/matching](../fe/feature-web-matching.md), [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md), [web/voice-match](../fe/feature-web-voice-match.md), [web/soul-match](../fe/feature-web-soul-match.md), [web/feed](../fe/feature-web-feed.md), [web/profile](../fe/feature-web-profile.md), [web/short-video](../fe/feature-web-short-video.md), [admin/dashboard](../fe/feature-admin-dashboard.md), [admin/rooms](../fe/feature-admin-rooms.md)

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
- useLeaveRoom -> POST /party/rooms/{id}/leave (apps/web/src/features/party-room/components/party-stage.tsx:232)
- useAcceptSpeakerInvite -> POST /party/rooms/{id}/speaker-invite/accept (apps/web/src/features/party-room/components/party-stage.tsx:365)
- useDeclineSpeakerInvite -> POST /party/rooms/{id}/speaker-invite/decline (apps/web/src/features/party-room/components/party-stage.tsx:377)
- useSendGift -> POST /party/rooms/{roomId}/gifts (apps/web/src/features/party-room/components/gift-panel.tsx:76)
- useChangeRole -> POST /party/rooms/{id}/members/{userId}/role (apps/web/src/features/party-room/components/member-list.tsx:124)
- useInviteSpeaker -> POST /party/rooms/{id}/members/{userId}/speaker-invite (apps/web/src/features/party-room/components/member-list.tsx:178)
- useSendRoomComment -> POST /party/rooms/{id}/comments (apps/web/src/features/party-room/components/party-chat.tsx:113)
- useDeclineInvite -> POST /matching/invites/{id}/decline (apps/web/src/features/matching/components/incoming-invites.tsx:141)
- useAcceptInvite -> POST /matching/invites/{id}/accept (apps/web/src/features/matching/components/incoming-invites.tsx:151)
- useConfirmTicket -> POST /matching/tickets/{id}/confirm (apps/web/src/features/matching/components/queue-status-panel.tsx:143)
- useCancelTicket -> DELETE /matching/tickets/{id} (apps/web/src/features/matching/components/queue-status-panel.tsx:158)
- useCancelTicket -> DELETE /matching/tickets/{id} (apps/web/src/features/matching/components/queue-status-panel.tsx:326)
- useJoinQueue -> POST /matching/tickets (apps/web/src/features/matching/components/match-type-picker.tsx:69)
- useJoinQueue -> POST /matching/tickets (apps/web/src/features/matching/components/direct-queue-start.tsx:30)
- useSpeedup -> POST /matching/tickets/{id}/speedup (apps/web/src/features/matching/components/speedup-button.tsx:37)

Realtime events the frontend listens to for this feature:
- movie.session.started in /movie-match (apps/web/src/app/(app)/movie-match/page.tsx:30)
- movie.reaction.sent in /movie-match (apps/web/src/features/movie-match/components/anon-match-view.tsx:201)
- movie.state.changed in /movie-match (apps/web/src/features/movie-match/components/anon-match-view.tsx:422)
- party.member.joined in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:102)
- party.member.left in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:108)
- party.member.disconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:114)
- party.member.reconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:120)
- party.role.changed in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:126)
- party.speaker.invite.received in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:132)
- party.room.closed in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:138)
- party.host.disconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:165)
- party.host.reconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:171)
- gift.sent in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:177)
- party.comment.created in /party/:roomId (apps/web/src/features/party-room/components/party-chat.tsx:75)
- match.confirmed in /matching (apps/web/src/features/matching/components/queue-status-panel.tsx:102)
- friend.message in /chat/:friendUserId (apps/web/src/features/friend-chat/components/conversation-thread.tsx:64)
- soul.message in /matching/soul/:sessionId (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:71)
- soul.matched in /matching/soul/:sessionId (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:78)
- soul.ended in /matching/soul/:sessionId (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:85)
- call.ended in /matching/voice/:sessionId (apps/web/src/features/voice-match/components/voice-call-room.tsx:200)
- movie.state.changed in /movie-match/:sessionId (apps/web/src/features/movie-match/components/movie-session-view.tsx:51)
- movie.session.ended in /movie-match/:sessionId (apps/web/src/features/movie-match/components/movie-session-view.tsx:57)
- call.ended in /chat/:friendUserId/call (apps/web/src/features/friend-chat/components/friend-call-room.tsx:94)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /soul-match/sessions/{id} ([soul-match](../api/soul-match.md)) | JWT |  |  | 1 |
| GET /soul-match/sessions/{id}/messages ([soul-match](../api/soul-match.md)) | JWT |  |  | 0 |
| POST /soul-match/sessions/{id}/messages ([soul-match](../api/soul-match.md)) | JWT | required | { default: { limit: 60, ttl: minutes(1) } } | 0 |
| POST /soul-match/sessions/{id}/rating ([soul-match](../api/soul-match.md)) | JWT |  |  | 0 |
| POST /soul-match/sessions/{id}/end ([soul-match](../api/soul-match.md)) | JWT |  |  | 1 |
| GET /soul-match/sessions/{id}/partner ([soul-match](../api/soul-match.md)) | JWT |  |  | 0 |
| POST /matching/invites ([matching](../api/matching.md)) | JWT |  | { default: { limit: 10, ttl: minutes(1) } } | 1 |
| GET /matching/invites ([matching](../api/matching.md)) | JWT |  |  | 1 |
| GET /matching/invites/{id} ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/invites/{id}/accept ([matching](../api/matching.md)) | JWT |  |  | 1 |
| POST /matching/invites/{id}/decline ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/invites/{id}/cancel ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/tickets ([matching](../api/matching.md)) | JWT | required | { default: { limit: 10, ttl: minutes(1) } } | 1 |
| GET /matching/tickets/current ([matching](../api/matching.md)) | JWT |  |  | 1 |
| GET /matching/tickets/{id} ([matching](../api/matching.md)) | JWT |  |  | 1 |
| DELETE /matching/tickets/{id} ([matching](../api/matching.md)) | JWT |  |  | 1 |
| POST /matching/tickets/{id}/confirm ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/tickets/{id}/speedup ([matching](../api/matching.md)) | JWT | required |  | 0 |
| POST /calling/match-sessions/{matchSessionId}/join ([calling](../api/calling.md)) | JWT |  | { default: { limit: 20, ttl: minutes(1) } } | 0 |
| POST /calling/friends/{friendUserId}/join ([calling](../api/calling.md)) | JWT |  | { default: { limit: 20, ttl: minutes(1) } } | 0 |
| GET /calling/calls/{id} ([calling](../api/calling.md)) | JWT |  |  | 1 |
| POST /calling/calls/{id}/end ([calling](../api/calling.md)) | JWT |  |  | 1 |
| POST /calling/match-sessions/{matchSessionId}/end ([calling](../api/calling.md)) | JWT |  |  | 0 |
| POST /calling/calls/{id}/like ([calling](../api/calling.md)) | JWT |  |  | 1 |
| POST /calling/webhooks/livekit ([calling](../api/calling.md)) | public |  |  | 0 |
| GET /profiles/{profileUserId}/follow-counts ([friend](../api/friend.md)) | JWT |  |  | 0 |
| GET /profiles/{profileUserId}/followers ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /profiles/{profileUserId}/following ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /profiles/{profileUserId}/actions ([friend](../api/friend.md)) | JWT |  |  | 0 |
| POST /profiles/{profileUserId}/follow ([friend](../api/friend.md)) | JWT |  |  | 0 |
| DELETE /profiles/{profileUserId}/follow ([friend](../api/friend.md)) | JWT |  |  | 0 |
| POST /profiles/{profileUserId}/conversation ([friend](../api/friend.md)) | JWT |  |  | 0 |
| GET /friends ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /friends/connections ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /friends/{friendUserId}/conversation ([friend](../api/friend.md)) | JWT |  |  | 0 |
| GET /conversations/{id}/messages ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /conversations/{id}/streak ([friend](../api/friend.md)) | JWT |  |  | 0 |
| POST /conversations/{id}/read ([friend](../api/friend.md)) | JWT |  |  | 1 |
| POST /conversations/{id}/mute ([friend](../api/friend.md)) | JWT |  |  | 1 |
| POST /conversations/{id}/messages ([friend](../api/friend.md)) | JWT | required |  | 1 |

… 33 more endpoints in the api files


## 4. Backend flows (service map)

- [movie-match-anon-chat-finish-reactions](../flows/movie-match-anon-chat-finish-reactions.md): Xem phim ẩn danh: chat, reaction và kết thúc (6 endpoints of this feature)
- [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md): Danh sách bạn, đọc tin và streak (5 endpoints of this feature)
- [matching-to-call](../flows/matching-to-call.md): Từ hàng đợi ghép đôi đến cuộc gọi thoại (5 endpoints of this feature)
- [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md): Danh sách follower/following và danh sách bạn bè (4 endpoints of this feature)
- [matching-ticket-status-cancel-speedup](../flows/matching-ticket-status-cancel-speedup.md): Xem, huỷ và tăng tốc vé ghép đôi (4 endpoints of this feature)
- [movie-match-friend-session](../flows/movie-match-friend-session.md): Xem phim chung giữa bạn bè (4 endpoints of this feature)
- [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md): Xem phòng party và bình luận trong phòng (4 endpoints of this feature)
- [friend-profile-follow-and-actions](../flows/friend-profile-follow-and-actions.md): Theo dõi và xem hành động khả dụng trên hồ sơ (3 endpoints of this feature)
- [matching-invite-create](../flows/matching-invite-create.md): Mời chơi có chủ đích: tạo và xem lời mời (3 endpoints of this feature)
- [movie-match-anonymous](../flows/movie-match-anonymous.md): Xem phim chung ẩn danh: hàng đợi, đồng bộ playback, rating (3 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `call_sessions`, `conversation_member_states`, `conversation_streaks`, `conversations`, `friendships`, `gift_events`, `gifts`, `guest_match_quotas`, `match_daily_quotas`, `match_invites`, `match_sessions`, `match_tickets`, `messages`, `mini_game_active_participants`, `mini_game_sessions`, `movie_match_queue_entries`, `movie_session_active_participants`, `movie_session_messages`, `movie_sessions`, `party_room_comments`, `party_room_members`, `party_rooms`, `profile_chat_contacts`, `profile_follows`, `soul_chat_messages`, `soul_match_ratings`, `voice_match_reactions` (see ../db/signaling-gateway.md, ../db/soul-match.md, ../db/matching.md, ../db/calling.md, ../db/friend.md, ../db/party-room.md, ../db/gift.md, ../db/movie-match.md, ../db/mini-game.md)
- status vocabularies enforced by CHECK:
  - call_sessions: voice_match | friend
  - call_sessions: completed | free_limit | insufficient_balance | pending_timeout
  - call_sessions: pending | active | ended
  - friendships: soul_match | voice_match | palm_match | movie_match
  - match_daily_quotas: soul | voice
  - match_invites: soul | voice
  - match_invites: pending | accepted | declined | expired | cancelled
  - match_sessions: soul | voice
  - match_sessions: pending_confirm | confirmed | ended | expired
  - match_tickets: any | male | female
  - match_tickets: soul | voice
  - match_tickets: queued | matched | confirmed | expired | cancelled
  - mini_game_sessions: rock | paper | scissors
  - mini_game_sessions: rock | paper | scissors
  - mini_game_sessions: waiting_moves | resolved | cancelled
  - movie_sessions: left | replaced
  - movie_sessions: active | ended
  - soul_match_ratings: rude | boring | like

## 6. Events

- soul.message: producers soul-match.sendMessage; FE consumers 1
- soul.matched: producers soul-match.rate; FE consumers 1
- soul.ended: producers soul-match.endSession; FE consumers 1
- match.matched: producers NONE; FE consumers 0
- match.confirmed: producers matching.tryPair, matching.confirmTicket, matching.acceptInvite; FE consumers 1
- call.ended: producers calling.cleanupEndedCall; FE consumers 2
- friend.message: producers friend.sendMessage; FE consumers 2
- friend.streak.increased: producers friend.sendMessage; FE consumers 0
- friend.streak.at_risk: producers friend.warnOne; FE consumers 0
- party.member.joined: producers party-room.joinRoom; FE consumers 1
- party.member.left: producers party-room.leaveRoom, party-room.finalizeDisconnectedMember; FE consumers 1
- party.member.disconnected: producers party-room.handleParticipantLeft; FE consumers 1
- party.member.reconnected: producers party-room.joinRoom; FE consumers 1
- party.role.changed: producers party-room.changeRole, party-room.publishRoleChanged; FE consumers 1
- party.speaker.invite.received: producers party-room.inviteSpeaker; FE consumers 1
- party.room.closed: producers party-room.closeRoomById; FE consumers 1
- party.host.disconnected: producers party-room.markHostDisconnected; FE consumers 1
- party.host.reconnected: producers party-room.joinRoom; FE consumers 1
- party.comment.created: producers party-room.sendComment; FE consumers 1
- gift.sent: producers gift.publishGiftSent; FE consumers 1
- movie.session.started: producers movie-match.createSession; FE consumers 1
- movie.state.changed: producers movie-match.updateState, movie-match.publishAnonPlayback; FE consumers 2
- movie.session.ended: producers movie-match.endSession; FE consumers 1
- movie.reaction.sent: producers movie-match.sendAnonReaction; FE consumers 1
- minigame.session.started: producers mini-game.createSession; FE consumers 0
- minigame.session.resolved: producers mini-game.submitMove; FE consumers 0

## 7. Errors

- error code groups: [CallingErrors](../errors/callingerrors.md), [FriendErrors](../errors/frienderrors.md), [ProfileSocialErrors](../errors/profilesocialerrors.md), [GiftErrors](../errors/gifterrors.md), [MatchingErrors](../errors/matchingerrors.md), [MiniGameErrors](../errors/minigameerrors.md), [MovieMatchErrors](../errors/moviematcherrors.md), [PartyRoomErrors](../errors/partyroomerrors.md), [SoulMatchErrors](../errors/soulmatcherrors.md)
- codes the FE references for this feature: CALLING_SESSION_NOT_FOUND, CALLING_SESSION_NOT_CALLABLE, CALLING_CALL_ENDED

## 8. Configuration

env vars read by the module(s): `CALLING_FREE_CALL_SECONDS`, `CALLING_TOKEN_TTL_SECONDS`, `LIVEKIT_URL`, `LIVEKIT_REGION_URLS`, `CALLING_TICKER_INTERVAL_MS`, `CALLING_PENDING_TIMEOUT_SECONDS`, `CALLING_RECONNECT_WINDOW_SECONDS`, `REDIS_URL`, `FRIEND_MESSAGE_MAX_LENGTH`, `STREAK_WARNING_CHECK_INTERVAL_MS`, `PROFILE_DIRECT_MESSAGE_DAILY_FIRST_CHAT_THRESHOLD`, `STREAK_WARNING_HOURS`, `STREAK_MILESTONE_DAYS`, `GIFT_POINTS_RATE_PERCENT`, `MATCHING_INVITE_SWEEPER_INTERVAL_MS`, `MATCHING_MATCHER_INTERVAL_MS`, `MATCHING_MATCHER_BATCH_SIZE`, `MATCHING_SWEEPER_INTERVAL_MS`, `MATCHING_QUEUE_MAX_WAIT_SECONDS`, `MATCHING_CONFIRM_TIMEOUT_SECONDS`, `MATCHING_SPEEDUP_PRICE_DIAMOND`, `MATCHING_SVIP_SPEEDUP_PRICE_DIAMOND`, `MATCHING_VIP_SPEEDUP_PRICE_DIAMOND`, `MATCHING_EXTRA_SOUL_PRICE_DIAMOND`, `MATCHING_EXTRA_VOICE_PRICE_DIAMOND`, `MATCHING_PRIORITY_BOOST_MS`, `MATCHING_AGE_BAND_SIZE`, `MATCHING_TRUST_PENALTY_MS_PER_POINT`, `MATCHING_TRUST_PENALTY_MAX_MS`, `MATCHING_GUEST_DAILY_LIMIT`, `MATCHING_SVIP_DAILY_SOUL_LIMIT`, `MATCHING_SVIP_DAILY_VOICE_LIMIT`, `MATCHING_VIP_DAILY_SOUL_LIMIT`, `MATCHING_VIP_DAILY_VOICE_LIMIT`, `MATCHING_DAILY_SOUL_LIMIT`, `MATCHING_DAILY_VOICE_LIMIT`, `MATCHING_GUEST_QUOTA_PEPPER`, `MATCHING_INVITE_RATE_LIMIT_PER_HOUR`, `MATCHING_INVITE_TTL_SECONDS`, `MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS`, `MOVIE_MATCH_MESSAGE_MAX_LENGTH`, `MOVIE_MATCH_ANON_VIDEO_URLS`, `MOVIE_MATCH_ANON_DURATION_SECONDS`, `MOVIE_MATCH_URL_MAX_LENGTH`, `MOVIE_MATCH_ALLOWED_VIDEO_HOSTS`, `PARTY_SWEEPER_INTERVAL_MS`, `PARTY_HOST_GRACE_CHECK_INTERVAL_MS`, `PARTY_HOST_DISCONNECT_GRACE_SECONDS`, `PARTY_MEMBER_DISCONNECT_GRACE_SECONDS`, `PARTY_STALE_ROOM_SECONDS`, `PARTY_TITLE_MAX_LENGTH`, `PARTY_MAX_SPEAKERS`, `PARTY_MAX_MEMBERS`, `PARTY_EMPTY_ROOM_TIMEOUT_SECONDS`, `PARTY_COMMENT_MAX_LENGTH`, `PARTY_TOKEN_TTL_SECONDS`, `SOUL_CHAT_MESSAGE_MAX_LENGTH`, `SOUL_CHAT_DURATION_SECONDS`, `SOUL_RATING_WINDOW_SECONDS`, `LOG_LEVEL`, `NODE_ENV`, `JWT_SECRET`, `WS_CONNECTION_LEASE_MS`, `WS_MAX_CONNECTIONS_PER_USER`, `JWT_SECRET_PREVIOUS`, `SENTRY_DSN`, `SENTRY_RELEASE`, `CORS_ORIGINS`, `SIGNALING_PORT` (see ../config/)

## 9. Tests

- calling: 3 unit spec files, 1 integration spec files
- friend: 4 unit spec files, 1 integration spec files
- gift: 0 unit spec files, 1 integration spec files
- matching: 6 unit spec files, 1 integration spec files
- mini-game: 1 unit spec files, 1 integration spec files
- movie-match: 1 unit spec files, 1 integration spec files
- party-room: 1 unit spec files, 1 integration spec files
- soul-match: 1 unit spec files, 1 integration spec files
- signaling-gateway: 9 unit spec files, 2 integration spec files
- e2e files that call these endpoints: apps/core-api-e2e/src/core-api/livekit-webhook-content-type.spec.ts, apps/web/e2e/login-and-join-matching-queue.spec.ts
- FE specs: apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx, apps/web/src/features/friend-chat/components/message-list.spec.tsx, apps/web/src/features/friend-chat/send-message-schema.spec.ts, apps/web/src/features/movie-match/anon-api.spec.ts, apps/web/src/features/movie-match/api.spec.ts, apps/web/src/features/movie-match/components/anon-match-view.spec.tsx, apps/web/src/features/movie-match/components/friend-picker.spec.tsx, apps/web/src/features/movie-match/components/movie-session-view.spec.tsx, apps/web/src/features/movie-match/youtube-url.spec.ts

## 10. Known issues (public subset of the ledger)

- P071 [fixed] Cổng gọi điện có thể bypass sau khi block (bắt được khi tự review)
- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never
- P090 [fixed] Huỷ ván mini-game ghi đè ván đã resolved
- P094 [open] Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention
- P004 [fixed] Adapter CLAUDE.md / skills lệch bản canonical (symlink tay)
- P036 [fixed] Windows: hook/runner không chạy được pnpm (.cmd: 127 / EINVAL)
- P038 [fixed] Windows: Jest không chạy (thiếu VC++ runtime, resolver native)
- P039 [fixed] Pre-push trên Windows đỏ giả do giả định POSIX / công cụ Linux-only
- P041 [fixed] Timeout cứng quá chặt / chạy song song làm test flaky
- P043 [fixed] Nx cache bật cho test có state → có thể trả kết quả cũ

See ../issues/index.md.
