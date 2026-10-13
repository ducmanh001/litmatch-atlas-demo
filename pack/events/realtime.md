# Realtime events (Socket.IO /signaling, Redis channel realtime:user:{userId})

id: events:realtime
contract: libs/common-dtos/src/lib/realtime-events.ts
best-effort; REST polling is the fallback

### soul.message

key: RealtimeEvents.SoulMessage

Message chat ẩn danh mới trong phòng Soul Match.

- payload: SoulMessageEventData
```ts
export interface SoulMessageEventData {
  sessionId: string;
  messageId: string;
  /** Đã tính per-recipient ở core-api — KHÔNG bao giờ chứa userId đối phương (ẩn danh). */
  senderRole: 'me' | 'partner';
  content: string;
  sentAt: string;
}
```
- producers: soul-match.sendMessage (apps/core-api/src/modules/soul-match/soul-match.service.ts:206)
- FE consumers: apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:71

### soul.matched

key: RealtimeEvents.SoulMatched

Cả 2 cùng "Thích" — đã match, profile unlock.

- payload: SoulMatchedEventData
```ts
export interface SoulMatchedEventData {
  sessionId: string;
}
```
- producers: soul-match.rate (apps/core-api/src/modules/soul-match/soul-match.service.ts:393)
- FE consumers: apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:78

### soul.ended

key: RealtimeEvents.SoulEnded

Một thành viên rời Soul Match — client phải đóng phòng ẩn danh ngay.

- payload: SoulEndedEventData
```ts
export interface SoulEndedEventData {
  sessionId: string;
  /** member_left — không chứa userId để giữ nguyên ẩn danh. */
  reason: 'member_left';
}
```
- producers: soul-match.endSession (apps/core-api/src/modules/soul-match/soul-match.service.ts:107)
- FE consumers: apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:85

### match.matched

key: RealtimeEvents.MatchMatched

Ticket được ghép cặp — chờ 2 bên confirm.

- payload: MatchMatchedEventData
```ts
export interface MatchMatchedEventData {
  ticketId: string;
  sessionId: string;
}
```
- producers: NONE FOUND
- FE consumers: none

### match.confirmed

key: RealtimeEvents.MatchConfirmed

Cả 2 đã confirm — session bắt đầu.

- payload: MatchConfirmedEventData
```ts
export interface MatchConfirmedEventData {
  ticketId: string;
  sessionId: string;
}
```
- producers: matching.tryPair (apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:374); matching.confirmTicket (apps/core-api/src/modules/matching/matching.service.ts:399); matching.acceptInvite (apps/core-api/src/modules/matching/services/invite.service.ts:411)
- FE consumers: apps/web/src/features/matching/components/queue-status-panel.tsx:103

### call.ended

key: RealtimeEvents.CallEnded

Voice call kết thúc (mọi lý do — kể cả server tự end hết free window).

- payload: CallEndedEventData
```ts
export interface CallEndedEventData {
  callId: string;
  matchSessionId: string | null;
  /** completed | free_limit | insufficient_balance | pending_timeout (calling-service.md § 1). */
  reason: string;
  durationSeconds: number;
}
```
- producers: calling.cleanupEndedCall (apps/core-api/src/modules/calling/calling.service.ts:700)
- FE consumers: apps/web/src/features/friend-chat/components/friend-call-room.tsx:94; apps/web/src/features/voice-match/components/voice-call-room.tsx:200

### friend.message

key: RealtimeEvents.FriendMessage

Message mới trong chat 1-1 lâu dài giữa 2 bạn (khác chat ẩn danh Soul Match).

- payload: FriendMessageEventData
```ts
export interface FriendMessageEventData {
  conversationId: string;
  messageId: string;
  /** KHÔNG ẩn danh (2 bên đã unlock profile khi thành bạn) — khác SoulMessageEventData. */
  senderUserId: string;
  content: string;
  /** NULL cho message thường — set khi reply story/video share (docs/services/feed-service.md § 8). */
  attachment: { kind: string; payload: Record<string, unknown> } | null;
  sentAt: string;
}
```
- producers: friend.sendMessage (apps/core-api/src/modules/friend/friend.service.ts:413)
- FE consumers: apps/web/src/features/friend-chat/components/conversation-thread.tsx:65; apps/web/src/features/notification/components/realtime-notification-bridge.tsx:54

### friend.streak.increased

key: RealtimeEvents.FriendStreakIncreased

Streak vừa tăng và chạm mốc milestone (STREAK_MILESTONE_DAYS) — không bắn cho ngày thường.

- payload: FriendStreakIncreasedEventData
```ts
export interface FriendStreakIncreasedEventData {
  conversationId: string;
  currentStreak: number;
}
```
- producers: friend.sendMessage (apps/core-api/src/modules/friend/friend.service.ts:394)
- FE consumers: none

### friend.streak.at_risk

key: RealtimeEvents.FriendStreakAtRisk

Cron cảnh báo streak sắp mất (chưa nhắn hôm nay, qua mốc giờ cảnh báo) — không ghi streak.

- payload: FriendStreakAtRiskEventData
```ts
export interface FriendStreakAtRiskEventData {
  conversationId: string;
}
```
- producers: friend.warnOne (apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:94)
- FE consumers: none

### party.member.joined

key: RealtimeEvents.PartyMemberJoined

Member vào/ra Party Room (fanout cho member active còn lại trong phòng).

- payload: PartyMemberJoinedEventData
```ts
export interface PartyMemberJoinedEventData {
  roomId: string;
  userId: string;
  /** host | speaker | audience (party-room-service.md § 2). */
  role: string;
}
```
- producers: party-room.joinRoom (apps/core-api/src/modules/party-room/party-room.service.ts:328)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:103

### party.member.left

key: RealtimeEvents.PartyMemberLeft



- payload: PartyMemberLeftEventData
```ts
export interface PartyMemberLeftEventData {
  roomId: string;
  userId: string;
}
```
- producers: party-room.leaveRoom (apps/core-api/src/modules/party-room/party-room.service.ts:411); party-room.finalizeDisconnectedMember (apps/core-api/src/modules/party-room/party-room.service.ts:928)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:109

### party.member.disconnected

key: RealtimeEvents.PartyMemberDisconnected

Member rớt mạng ngoài ý muốn; vẫn active trong grace period để reconnect.

- payload: PartyMemberDisconnectedEventData
```ts
export interface PartyMemberDisconnectedEventData {
  roomId: string;
  userId: string;
  disconnectedAt: string;
}
```
- producers: party-room.handleParticipantLeft (apps/core-api/src/modules/party-room/party-room.service.ts:1231)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:115

### party.member.reconnected

key: RealtimeEvents.PartyMemberReconnected

Member reconnect trước khi grace hết.

- payload: PartyMemberReconnectedEventData
```ts
export interface PartyMemberReconnectedEventData {
  roomId: string;
  userId: string;
}
```
- producers: party-room.joinRoom (apps/core-api/src/modules/party-room/party-room.service.ts:346)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:121

### party.role.changed

key: RealtimeEvents.PartyRoleChanged

Host cấp/thu quyền speaker — grant SFU đã đổi xong ở server trước khi publish.

- payload: PartyRoleChangedEventData
```ts
export interface PartyRoleChangedEventData {
  roomId: string;
  userId: string;
  role: string;
}
```
- producers: party-room.changeRole (apps/core-api/src/modules/party-room/party-room.service.ts:494); party-room.publishRoleChanged (apps/core-api/src/modules/party-room/party-room.service.ts:667)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:127

### party.speaker.invite.received

key: RealtimeEvents.PartySpeakerInviteReceived

Host gửi lời mời speaker — chỉ fanout tới audience được mời.

- payload: PartySpeakerInviteReceivedEventData
```ts
export interface PartySpeakerInviteReceivedEventData {
  roomId: string;
}
```
- producers: party-room.inviteSpeaker (apps/core-api/src/modules/party-room/party-room.service.ts:560)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:133

### party.room.closed

key: RealtimeEvents.PartyRoomClosed

Phòng đóng (host rời, hết member, sweeper) — client phải rời UI phòng.

- payload: PartyRoomClosedEventData
```ts
export interface PartyRoomClosedEventData {
  roomId: string;
  /** host_left | empty | swept (party-room-service.md § 4). */
  reason: string;
}
```
- producers: party-room.closeRoomById (apps/core-api/src/modules/party-room/party-room.service.ts:1053)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:139

### party.host.disconnected

key: RealtimeEvents.PartyHostDisconnected

Host rớt kết nối NGOÀI Ý MUỐN (webhook participant_left) — phòng CHƯA đóng, đang trong grace chờ host tự kết nối lại (party-room-service.md § 4). Chỉ gợi ý refetch sớm, REST poll `GET /party/rooms/:id` (field `hostDisconnectedAt`) vẫn là nguồn sự thật.

- payload: PartyHostDisconnectedEventData
```ts
export interface PartyHostDisconnectedEventData {
  roomId: string;
}
```
- producers: party-room.markHostDisconnected (apps/core-api/src/modules/party-room/party-room.service.ts:1295)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:166

### party.host.reconnected

key: RealtimeEvents.PartyHostReconnected

Host đã kết nối lại trong lúc grace — huỷ lịch đóng phòng.

- payload: PartyHostReconnectedEventData
```ts
export interface PartyHostReconnectedEventData {
  roomId: string;
}
```
- producers: party-room.joinRoom (apps/core-api/src/modules/party-room/party-room.service.ts:340)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:172

### party.comment.created

key: RealtimeEvents.PartyCommentCreated

Comment mới trong Party Room — publish sau khi comment commit.

- payload: PartyCommentCreatedEventData
```ts
export interface PartyCommentCreatedEventData {
  roomId: string;
  commentId: string;
  senderUserId: string;
  content: string;
  sentAt: string;
}
```
- producers: party-room.sendComment (apps/core-api/src/modules/party-room/party-room.service.ts:825)
- FE consumers: apps/web/src/features/party-room/components/party-chat.tsx:76

### gift.sent

key: RealtimeEvents.GiftSent

Quà tặng trong Party Room — publish SAU khi transaction tiền commit (docs/10 § Gift).

- payload: GiftSentEventData
```ts
export interface GiftSentEventData {
  roomId: string;
  giftEventId: string;
  giftCode: string;
  senderUserId: string;
  receiverUserId: string;
  priceDiamond: number;
  /** PTS người nhận thực nhận (0 nếu receiver là guest — docs/06 § Gift). */
  pointsAwarded: number;
  sentAt: string;
}
```
- producers: gift.publishGiftSent (apps/core-api/src/modules/gift/gift.service.ts:481)
- FE consumers: apps/web/src/features/party-room/components/party-stage.tsx:177

### movie.session.started

key: RealtimeEvents.MovieSessionStarted

Movie Match: phiên xem chung mới được tạo (docs/services/movie-match-service.md § 5).

- payload: MovieSessionStartedEventData
```ts
export interface MovieSessionStartedEventData {
  sessionId: string;
  videoUrl: string;
  /** Người gọi POST /movie-match/sessions — cả 2 bên đã là bạn nên lộ userId là AN TOÀN (không ẩn danh). */
  initiatorUserId: string;
}
```
- producers: movie-match.createSession (apps/core-api/src/modules/movie-match/movie-match.service.ts:173)
- FE consumers: apps/web/src/app/(app)/movie-match/page.tsx:31

### movie.state.changed

key: RealtimeEvents.MovieStateChanged

Movie Match: playback state đổi (vị trí/play-pause) — last-write-wins, không lock.

- payload: MovieStateChangedEventData
```ts
export interface MovieStateChangedEventData {
  sessionId: string;
  videoUrl: string;
  positionSeconds: number;
  isPlaying: boolean;
  /** ISO string — server timestamp, client nội suy vị trí hiện tại khi isPlaying=true. */
  positionUpdatedAt: string;
}
```
- producers: movie-match.updateState (apps/core-api/src/modules/movie-match/movie-match.service.ts:221); movie-match.publishAnonPlayback (apps/core-api/src/modules/movie-match/movie-match.service.ts:845)
- FE consumers: apps/web/src/features/movie-match/components/anon-match-view.tsx:423; apps/web/src/features/movie-match/components/movie-session-view.tsx:52

### movie.session.ended

key: RealtimeEvents.MovieSessionEnded

Movie Match: phiên xem chung kết thúc (chủ động rời — chưa có nhánh hết hạn tự động).

- payload: MovieSessionEndedEventData
```ts
export interface MovieSessionEndedEventData {
  sessionId: string;
  /** left | replaced (movie-match-service.md § 3, § 5). */
  reason: string;
}
```
- producers: movie-match.endSession (apps/core-api/src/modules/movie-match/movie-match.service.ts:271)
- FE consumers: apps/web/src/features/movie-match/components/movie-session-view.tsx:58

### movie.reaction.sent

key: RealtimeEvents.MovieReactionSent

Movie Match ẩn danh: reaction emoji nổi trên video — hiệu ứng ephemeral, KHÔNG persist, KHÔNG kèm userId (2 bên chưa lộ danh tính). Client miss event chỉ mất hiệu ứng.

- payload: MovieReactionSentEventData
```ts
export interface MovieReactionSentEventData {
  sessionId: string;
  emoji: string;
}
```
- producers: movie-match.sendAnonReaction (apps/core-api/src/modules/movie-match/movie-match.service.ts:632)
- FE consumers: apps/web/src/features/movie-match/components/anon-match-view.tsx:202

### minigame.session.started

key: RealtimeEvents.MiniGameSessionStarted

Mini Game: ván oẳn tù tì mới được tạo (docs/services/mini-game-service.md § 5).

- payload: MiniGameSessionStartedEventData
```ts
export interface MiniGameSessionStartedEventData {
  sessionId: string;
  gameType: string;
  /** Người gọi POST /mini-game/sessions — cả 2 bên đã là bạn nên lộ userId là AN TOÀN. */
  initiatorUserId: string;
}
```
- producers: mini-game.createSession (apps/core-api/src/modules/mini-game/mini-game.service.ts:121)
- FE consumers: none

### minigame.session.resolved

key: RealtimeEvents.MiniGameSessionResolved

Mini Game: ván oẳn tù tì đã resolve — publish SAU KHI CẢ HAI đã nộp move (docs/services/ mini-game-service.md § 3, § 5). KHÔNG có event trung gian kiểu "1 bên đã nộp move" — chỉ 1 event duy nhất kèm CẢ HAI move, để tránh channel side khác suy luận được ai nộp trước/sau.

- payload: MiniGameSessionResolvedEventData
```ts
export interface MiniGameSessionResolvedEventData {
  sessionId: string;
  /** rock | paper | scissors — của userLowId. Chỉ publish khi CẢ HAI đã nộp (đã resolve). */
  lowMove: string;
  /** rock | paper | scissors — của userHighId. */
  highMove: string;
  /** userId thắng — null nếu hoà. */
  winnerUserId: string | null;
}
```
- producers: mini-game.submitMove (apps/core-api/src/modules/mini-game/mini-game.service.ts:227)
- FE consumers: none

