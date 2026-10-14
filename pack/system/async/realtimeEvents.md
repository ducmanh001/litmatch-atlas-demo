# Async flows: realtime, outbox, jobs, queues: realtimeEvents

id: system:async:realtimeEvents

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### soul.message

- key: SoulMessage
- audience: Cả hai thành viên của phòng Soul Match; mỗi người một envelope riêng (payload tính per-recipient).
- trigger: Gửi tin nhắn MỚI trong phase chatting; publish sau khi lưu `soul_chat_messages`, replay Idempotency-Key không bắn lại.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: messageId; type: string; note: soul_chat_messages.id
  - field: senderRole; type: 'me' | 'partner'; note: Tính theo người nhận: 'me' cho người gửi, 'partner' cho đối phương; KHÔNG bao giờ chứa userId đối phương
  - field: content; type: string; note: Nội dung tin nhắn
  - field: sentAt; type: string; note: ISO string, thời điểm tạo ở server
- producers:
  - module: soul-match; function: sendMessage; condition: Sau transaction lưu tin; chỉ với tin MỚI (nhánh replay unique-violation không publish); gửi cho [người gửi: senderRole=me, đối phương: senderRole=partner]. [evidence: apps/core-api/src/modules/soul-match/soul-match.service.ts:206]
- consumers:
  - app: web (SoulChatPhaseView); handling: Nếu data.sessionId khớp phòng đang mở thì invalidate query messages của phòng (refetch REST). [evidence: apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:71]
- bestEffort: true
- restFallback: GET /api/v1/soul-match/sessions/{id}/messages (web poll cứng mỗi 5s: SOUL_MESSAGES_REFETCH_INTERVAL_MS)
- notes: Ẩn danh được quyết ở core-api lúc publish (senderRole), gateway không biết gì về ẩn danh.
- contract: libs/common-dtos/src/lib/realtime-events.ts:36

### soul.matched

- key: SoulMatched
- audience: Cả hai user của session Soul Match.
- trigger: Cả hai cùng "Thích" và việc đó TẠO MỚI Friendship (newlyMatched); publish sau commit transaction rating.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
- producers:
  - module: soul-match; function: rate; condition: Sau commit; chỉ khi ensureFriendship trả created=true (newlyMatched); gửi cho [user, partner]. [evidence: apps/core-api/src/modules/soul-match/soul-match.service.ts:393]
- consumers:
  - app: web (SoulChatPhaseView); handling: Nếu sessionId khớp thì invalidate query session (refetch REST để lấy matched=true). [evidence: apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:78]
- bestEffort: true
- restFallback: GET /api/v1/soul-match/sessions/{id} (poll 10s khi phase còn mở: SOUL_SESSION_REFETCH_INTERVAL_MS)
- notes: Replay rating không bắn lại. Unlock hồ sơ đối phương dựa vào Friendship trong DB, không dựa vào event.
- contract: libs/common-dtos/src/lib/realtime-events.ts:38

### soul.ended

- key: SoulEnded
- audience: Cả hai user của session (userAId, userBId).
- trigger: Một bên rời phòng (POST /soul-match/sessions/{id}/end); publish sau MatchingService.endSoulSession.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: reason; type: 'member_left'; note: Luôn 'member_left'; không chứa userId để giữ ẩn danh
- producers:
  - module: soul-match; function: endSession; condition: Sau khi MatchingService.endSoulSession thành công; gửi cho userAId và userBId. [evidence: apps/core-api/src/modules/soul-match/soul-match.service.ts:107]
- consumers:
  - app: web (SoulChatPhaseView); handling: Nếu sessionId khớp thì lưu endedReason cho UI và invalidate query session. [evidence: apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:85]
- bestEffort: true
- restFallback: GET /api/v1/soul-match/sessions/{id} (poll 10s khi phase còn mở)
- notes: Rời phòng là transition durable ở DB; event chỉ để UI đóng ngay.
- contract: libs/common-dtos/src/lib/realtime-events.ts:40

### match.matched

- key: MatchMatched
- audience: Không có (chưa có producer).
- trigger: Khai báo trong hợp đồng: "Ticket được ghép cặp — chờ 2 bên confirm". Hiện KHÔNG có nơi nào publish (đã grep apps/ và libs/); luồng hiện tại ghép xong là confirmed ngay và phát `match.confirmed`.
- payload:
  - field: ticketId; type: string; note: match_tickets.id (kiểu đã khai, chưa dùng)
  - field: sessionId; type: string; note: match_sessions.id (kiểu đã khai, chưa dùng)
- bestEffort: true
- restFallback: GET /api/v1/matching/tickets/{id} (không liên quan vì event không được phát)
- notes: Event mồ côi: có tên + kiểu payload trong libs/common-dtos nhưng không có producer và không có consumer ở web. Chỉ gateway integration spec dùng nó làm event mẫu. Comment trong matching.metrics.ts vẫn nhắc `match.matched` dù code publish `match.confirmed`.
- contract: libs/common-dtos/src/lib/realtime-events.ts:42

### match.confirmed

- key: MatchConfirmed
- audience: Cả hai user của cặp; mỗi người một envelope chứa ticketId của chính mình.
- trigger: Phiên ghép được chốt confirmed: matcher ghép thành công, hoặc confirm legacy đủ 2 bên, hoặc accept invite (bỏ qua hàng đợi).
- payload:
  - field: ticketId; type: string; note: match_tickets.id của CHÍNH người nhận event
  - field: sessionId; type: string; note: match_sessions.id đã ở trạng thái confirmed
- producers:
  - module: matching; function: tryPair; condition: Sau commit tryPair (matcher worker) ghép thành công; mỗi user một envelope {ticketId của user đó, sessionId}; tiếp theo gửi push. [evidence: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:374]
  - module: matching; function: confirmTicket; condition: Chỉ khi chính lời gọi này chốt đủ 2 confirm (completedPair) — luồng legacy matched→confirmed; replay không bắn lại. [evidence: apps/core-api/src/modules/matching/matching.service.ts:399]
  - module: matching; function: acceptInvite; condition: Sau commit accept invite: tạo trực tiếp 2 ticket + session, không đi qua hàng đợi Redis; dùng chung event với auto-match. [evidence: apps/core-api/src/modules/matching/services/invite.service.ts:411]
- consumers:
  - app: web (QueueStatusPanel); handling: Nếu ticketId khớp ticket đang chờ thì invalidate query ticket; nếu chưa có ticket nào (invite được accept lúc đang mở Matching) thì setTicketId(data.ticketId) để UI bắt kịp. [evidence: apps/web/src/features/matching/components/queue-status-panel.tsx:103]
- bestEffort: true
- restFallback: GET /api/v1/matching/tickets/{id} (web poll 10s khi ticket queued/matched: MATCHING_TICKET_REFETCH_INTERVAL_MS) và GET /api/v1/matching/tickets/current
- notes: Event duy nhất báo "đã ghép xong" ở luồng hiện tại; có 3 producer khác file/ngữ cảnh. Push notification được gửi riêng sau commit (NotificationService.sendPush).
- contract: libs/common-dtos/src/lib/realtime-events.ts:44

### call.ended

- key: CallEnded
- audience: Cả hai user của call (userAId, userBId).
- trigger: Call chuyển sang `ended` ở MỌI nhánh (member bấm end, ticker, webhook LiveKit, reconcile); chỉ lời gọi thực hiện transition mới publish.
- payload:
  - field: callId; type: string; note: call_sessions.id
  - field: matchSessionId; type: string | null; note: match_sessions.id, hoặc null với friend call (không dùng MatchSession)
  - field: reason; type: string; note: Theo comment hợp đồng: completed | free_limit | insufficient_balance | pending_timeout. Enum CallEndReason có `insufficient_balance` nhưng không có nhánh code nào dùng; call không tính Diamond theo phút
  - field: durationSeconds; type: number; note: Thời lượng theo giờ server (endedAt − startedAt đã loại cửa sổ reconnect); 0 nếu call chưa từng active
- producers:
  - module: calling; function: cleanupEndedCall; condition: Sau commit endById, sau MatchingService.endVoiceSessionForCall và livekit.deleteRoom; gửi cho cả hai; chỉ khi justEnded (không bắn đôi khi retry). [evidence: apps/core-api/src/modules/calling/calling.service.ts:700]
- consumers:
  - app: web (FriendCallRoom); handling: Nếu callId khớp thì đánh dấu sessionEnded, ngắt media LiveKit và invalidate query call. [evidence: apps/web/src/features/friend-chat/components/friend-call-room.tsx:94]
  - app: web (VoiceCallRoom); handling: Nếu callId khớp thì đánh dấu sessionEnded, ngắt media LiveKit và invalidate query call. [evidence: apps/web/src/features/voice-match/components/voice-call-room.tsx:200]
- bestEffort: true
- restFallback: GET /api/v1/calling/calls/{id} (web poll 1s khi call còn active: VOICE_CALL_REFETCH_INTERVAL_MS)
- notes: Thứ tự trong cleanupEndedCall: chốt MatchSession ở Matching → deleteRoom LiveKit (best-effort) → publish. Nếu endVoiceSessionForCall ném lỗi thì deleteRoom và publish bị bỏ qua và không retry (call đã ended trong DB, client biết qua poll).
- contract: libs/common-dtos/src/lib/realtime-events.ts:46

### friend.message

- key: FriendMessage
- audience: Cả hai bạn trong conversation (cả người gửi).
- trigger: Gửi tin chat 1-1 (POST /conversations/{id}/messages); publish sau khi lưu message.
- payload:
  - field: conversationId; type: string; note: conversations.id
  - field: messageId; type: string; note: messages.id
  - field: senderUserId; type: string; note: Không ẩn danh (hai bên đã unlock hồ sơ)
  - field: content; type: string; note: Nội dung tin (có thể rỗng khi là tin ảnh)
  - field: attachment; type: { kind: string; payload: Record<string, unknown> } | null; note: NULL cho tin thường; set khi reply story/chia sẻ video/ảnh
  - field: sentAt; type: string; note: ISO string, thời điểm tạo ở server
- producers:
  - module: friend; function: sendMessage; condition: Sau conversationService.sendMessage (không bọc transaction ngoài); gửi cho [userId, partnerId]; kèm tạo notification in-app cho người nhận. [evidence: apps/core-api/src/modules/friend/friend.service.ts:413]
- consumers:
  - app: web (ConversationThread); handling: Nếu conversationId khớp thread đang mở thì invalidate query messages và markRead. [evidence: apps/web/src/features/friend-chat/components/conversation-thread.tsx:65]
  - app: web (RealtimeNotificationBridge); handling: Invalidate notifications + danh sách bạn; nếu không phải tin của mình và không đang ở /chat/{sender} thì hiện toast và Notification API của trình duyệt (nếu đã cấp quyền). [evidence: apps/web/src/features/notification/components/realtime-notification-bridge.tsx:54]
- bestEffort: true
- restFallback: GET /api/v1/conversations/{id}/messages (web poll 5s: FRIEND_MESSAGES_REFETCH_INTERVAL_MS)
- notes: Không có guard replay ở FriendService.sendMessage: retry cùng Idempotency-Key nhận lại message cũ nhưng vẫn publish lại cùng messageId (client chỉ invalidate nên vô hại).
- contract: libs/common-dtos/src/lib/realtime-events.ts:48

### friend.streak.increased

- key: FriendStreakIncreased
- audience: Cả hai bạn trong conversation.
- trigger: Gửi tin làm streak tăng và chạm mốc STREAK_MILESTONE_DAYS (mặc định 3,7,14,30,50,100); không bắn cho ngày thường.
- payload:
  - field: conversationId; type: string; note: conversations.id
  - field: currentStreak; type: number; note: Số ngày streak vừa chạm (một mốc trong STREAK_MILESTONE_DAYS)
- producers:
  - module: friend; function: sendMessage; condition: Khi StreakService.recordActivity trả milestoneHit != null; gửi cho [userId, partnerId]; nằm trong try/catch riêng. [evidence: apps/core-api/src/modules/friend/friend.service.ts:394]
- bestEffort: true
- restFallback: GET /api/v1/conversations/{id}/streak
- notes: Chưa có consumer ở apps/web hay apps/admin. Lỗi tính streak chỉ log, tin nhắn vẫn gửi thành công.
- contract: libs/common-dtos/src/lib/realtime-events.ts:50

### friend.streak.at_risk

- key: FriendStreakAtRisk
- audience: Cả hai bạn (conversation.userLowId, userHighId).
- trigger: Job friend-streak-warning: chưa nhắn hôm nay (UTC) sau giờ STREAK_WARNING_HOURS (mặc định 20); tối đa một lần/ngày/conversation.
- payload:
  - field: conversationId; type: string; note: conversations.id
- producers:
  - module: friend; function: warnOne; condition: Trong job friend-streak-warning (cluster singleton); sau publish tạo notification + sendPush rồi markWarningSent. [evidence: apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:94]
- bestEffort: true
- restFallback: GET /api/v1/conversations/{id}/streak (và notification in-app + push do chính job tạo)
- notes: Chưa có consumer ở apps/web hay apps/admin; người dùng nhận qua notification/push do job tạo cùng lúc. Không ghi streak.
- contract: libs/common-dtos/src/lib/realtime-events.ts:52

### party.member.joined

- key: PartyMemberJoined
- audience: Member active còn lại trong phòng (loại trừ người vừa vào).
- trigger: Join phòng lần đầu (isNewJoin) qua POST /party/rooms/{id}/join; publish sau commit.
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: userId; type: string; note: User vừa vào phòng
  - field: role; type: string; note: host | speaker | audience
- producers:
  - module: party-room; function: joinRoom; condition: Sau commit khi isNewJoin; fanout cho member active trừ người vừa vào. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:328]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail() (debounce 150 ms rồi refetch GET /party/rooms/{id}). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:103]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id} (web poll 5s khi socket mất, 30s khi socket khỏe)
- notes: Nhiều event party dùng chung hàm publishToRoomMembers: đọc roster `leftAt IS NULL` rồi publish từng user (N lệnh Redis PUBLISH).
- contract: libs/common-dtos/src/lib/realtime-events.ts:54

### party.member.left

- key: PartyMemberLeft
- audience: Member active còn lại trong phòng.
- trigger: Member thường rời qua REST (leaveRoom), hoặc sweeper chốt member rớt mạng quá grace (finalizeDisconnectedMember).
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: userId; type: string; note: User đã rời
- producers:
  - module: party-room; function: leaveRoom; condition: Sau commit REST leave của member không phải host; trước khi kick LiveKit (best-effort). [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:411]
  - module: party-room; function: finalizeDisconnectedMember; condition: Do job party-room-host-grace-check chốt member rớt mạng quá PARTY_MEMBER_DISCONNECT_GRACE_SECONDS (15s). [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:928]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail(). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:109]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id}
- notes: Host rời không phát event này mà đóng phòng (party.room.closed).
- contract: libs/common-dtos/src/lib/realtime-events.ts:55

### party.member.disconnected

- key: PartyMemberDisconnected
- audience: Member active trong phòng (gồm cả member đang trong grace).
- trigger: Webhook LiveKit participant_left của member không phải host; lần đầu đặt disconnectedAt (retry webhook không bắn đôi).
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: userId; type: string; note: users.id của member liên quan tới sự kiện
  - field: disconnectedAt; type: string; note: ISO string mốc bắt đầu grace
- producers:
  - module: party-room; function: handleParticipantLeft; condition: Sau commit đánh dấu disconnectedAt dưới khóa phòng; chỉ khi chưa đánh dấu trước đó. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:1231]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail(). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:115]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id}
- notes: Member vẫn active trong roster đến hết PARTY_MEMBER_DISCONNECT_GRACE_SECONDS.
- contract: libs/common-dtos/src/lib/realtime-events.ts:57

### party.member.reconnected

- key: PartyMemberReconnected
- audience: Member active trong phòng.
- trigger: Member đang disconnected gọi join lại trước khi grace hết (memberReconnected).
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: userId; type: string; note: users.id của member liên quan tới sự kiện
- producers:
  - module: party-room; function: joinRoom; condition: Sau commit khi memberReconnected=true. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:346]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail(). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:121]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id}
- contract: libs/common-dtos/src/lib/realtime-events.ts:59

### party.role.changed

- key: PartyRoleChanged
- audience: Member active trong phòng.
- trigger: Host thu quyền speaker (changeRole, role về audience) hoặc audience chấp nhận lời mời speaker (acceptSpeakerInvite, role thành speaker).
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: userId; type: string; note: Member bị đổi role
  - field: role; type: string; note: host | speaker | audience
- producers:
  - module: party-room; function: changeRole; condition: Sau commit hạ role target xuống audience (host chỉ thu quyền, cấp quyền đi qua invite) và sau syncPublishPermissionAfterCommit; chỉ khi role thật sự đổi. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:494]
  - module: party-room; function: publishRoleChanged; condition: Helper được acceptSpeakerInvite gọi sau khi role chuyển sang speaker. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:667]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail(). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:127]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id}
- notes: Quyền publish ở SFU được đồng bộ (updateParticipantPublish) trước khi publish; lỗi SFU thì hoàn role và trả 503.
- contract: libs/common-dtos/src/lib/realtime-events.ts:61

### party.speaker.invite.received

- key: PartySpeakerInviteReceived
- audience: Chỉ audience được mời (targetUserId), không fanout phòng.
- trigger: Host mời speaker (POST /party/rooms/{id}/members/{userId}/speaker-invite) lần đầu (changed=true).
- payload:
  - field: roomId; type: string; note: party_rooms.id
- producers:
  - module: party-room; function: inviteSpeaker; condition: Sau commit; chỉ khi lời mời mới được đặt (speakerInvitePending chuyển true). [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:560]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail() (UI đọc cờ lời mời từ REST). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:133]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id}
- notes: Gửi trực tiếp một publishRealtimeEvent tới user được mời.
- contract: libs/common-dtos/src/lib/realtime-events.ts:63

### party.room.closed

- key: PartyRoomClosed
- audience: Các member active tại thời điểm đóng (danh sách lấy trong transaction đóng phòng).
- trigger: Phòng chuyển sang closed: host rời, webhook room_finished, sweeper, hết grace host.
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: reason; type: string; note: PartyRoomCloseReason: host_left | finished | swept | error
- producers:
  - module: party-room; function: closeRoomById; condition: Sau commit đóng phòng và livekit.deleteRoom (best-effort); chỉ khi closed=true. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:1053]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì ngắt media, ghi ngay status=closed + closeReason vào cache query và invalidateDetail(). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:139]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id} (field room.status/closeReason)
- notes: closeRoomById idempotent: chỉ lời gọi thực hiện transition mới deleteRoom + publish. reason theo enum PartyRoomCloseReason: host_left | finished | swept | error (comment hợp đồng ghi host_left | empty | swept).
- contract: libs/common-dtos/src/lib/realtime-events.ts:65

### party.host.disconnected

- key: PartyHostDisconnected
- audience: Member active trong phòng.
- trigger: Webhook LiveKit participant_left của HOST (ngoài ý muốn), lần đầu đặt hostDisconnectedAt; phòng chưa đóng, chờ grace.
- payload:
  - field: roomId; type: string; note: party_rooms.id
- producers:
  - module: party-room; function: markHostDisconnected; condition: Sau commit đặt hostDisconnectedAt dưới khóa phòng; chỉ khi chưa đặt trước đó. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:1295]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail(). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:166]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id} (field hostDisconnectedAt là nguồn sự thật)
- notes: Gợi ý refetch sớm; việc đóng phòng do party-room-host-grace-check sau PARTY_HOST_DISCONNECT_GRACE_SECONDS (15s).
- contract: libs/common-dtos/src/lib/realtime-events.ts:71

### party.host.reconnected

- key: PartyHostReconnected
- audience: Member active trong phòng.
- trigger: Host join lại trong lúc grace (hostReconnected) — hủy lịch đóng phòng.
- payload:
  - field: roomId; type: string; note: party_rooms.id
- producers:
  - module: party-room; function: joinRoom; condition: Sau commit khi hostReconnected=true. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:340]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì invalidateDetail(). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:172]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id}
- contract: libs/common-dtos/src/lib/realtime-events.ts:73

### party.comment.created

- key: PartyCommentCreated
- audience: Member active trong phòng (gồm người gửi).
- trigger: Gửi comment (POST /party/rooms/{id}/comments); publish sau commit lưu comment.
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: commentId; type: string; note: party_room_comments.id
  - field: senderUserId; type: string; note: Không ẩn danh
  - field: content; type: string; note: Nội dung comment
  - field: sentAt; type: string; note: ISO string, thời điểm tạo ở server
- producers:
  - module: party-room; function: sendComment; condition: Sau commit lưu comment; fanout cho member active qua publishToRoomMembers. [evidence: apps/core-api/src/modules/party-room/party-room.service.ts:825]
- consumers:
  - app: web (PartyChat); handling: Nếu roomId khớp thì appendLiveComment({id, roomId, senderUserId, content, sentAt}); danh sách hiển thị khử trùng theo id với các trang REST. [evidence: apps/web/src/features/party-room/components/party-chat.tsx:76]
- bestEffort: true
- restFallback: GET /api/v1/party/rooms/{id}/comments (web poll 5s khi socket mất, 30s khi socket khỏe)
- notes: Client gộp comment realtime với trang REST theo commentId.
- contract: libs/common-dtos/src/lib/realtime-events.ts:75

### gift.sent

- key: GiftSent
- audience: Member active của party room.
- trigger: Tặng quà trong phòng (POST /party/rooms/{roomId}/gifts) thành công, chỉ khi không phải replay; publish SAU transaction tiền.
- payload:
  - field: roomId; type: string; note: party_rooms.id
  - field: giftEventId; type: string; note: gift_events.id
  - field: giftCode; type: string; note: gifts.code
  - field: senderUserId; type: string; note: users.id của người gửi (không ẩn danh)
  - field: receiverUserId; type: string; note: users.id của người nhận quà
  - field: priceDiamond; type: number; note: Giá Diamond tại thời điểm tặng
  - field: pointsAwarded; type: number; note: 0 nếu người nhận là guest
  - field: sentAt; type: string; note: ISO string, thời điểm tạo ở server
- producers:
  - module: gift; function: publishGiftSent; condition: GiftService.sendGift sau EconomyService.sendGift, chỉ khi !result.replayed; lấy roster qua PartyRoomService.getActiveRoomMembers. [evidence: apps/core-api/src/modules/gift/gift.service.ts:481]
- consumers:
  - app: web (PartyStage); handling: Nếu roomId khớp thì setLastGift(data) và hiện dòng "đã tặng" (không refetch). [evidence: apps/web/src/features/party-room/components/party-stage.tsx:177]
- bestEffort: true
- restFallback: Không có REST khôi phục hiệu ứng (chỉ là banner ephemeral). Tiền là sự thật ở GET /api/v1/economy/wallet và GET /api/v1/economy/transactions.
- notes: Quà tặng cho video/profile không publish event này. Lỗi publish chỉ log (try/catch trong publishGiftSent); ledger đã commit nên tiền vẫn đúng.
- contract: libs/common-dtos/src/lib/realtime-events.ts:77

### movie.session.started

- key: MovieSessionStarted
- audience: Cả hai user của phiên (userLowId, userHighId), gồm cả người khởi tạo.
- trigger: POST /movie-match/sessions tạo phiên xem chung giữa bạn bè (mode Friend); publish sau transaction.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: videoUrl; type: string; note: URL video do người khởi tạo chọn (đã lọc theo MOVIE_MATCH_ALLOWED_VIDEO_HOSTS)
  - field: initiatorUserId; type: string; note: Người gọi POST /movie-match/sessions (hai bên đã là bạn nên lộ userId là an toàn)
- producers:
  - module: movie-match; function: createSession; condition: Sau transaction tạo phiên (PK active participant chặn trùng); gửi cho cả hai; nhánh replay/race trả phiên cũ không publish. [evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:173]
- consumers:
  - app: web (MovieMatchPage); handling: Nếu initiatorUserId khác user hiện tại thì router.push(/movie-match/{sessionId}) để vào đúng phòng. [evidence: apps/web/src/app/(app)/movie-match/page.tsx:31]
- bestEffort: true
- restFallback: GET /api/v1/movie-match/sessions/{id} — chỉ dùng được khi đã biết id; người được mời không có REST liệt kê phiên nên event này là kênh DUY NHẤT để họ biết (theo comment ở web).
- notes: Khác các event còn lại, web không coi đây là "gợi ý refetch": mất event thì người được mời không tự vào phòng.
- contract: libs/common-dtos/src/lib/realtime-events.ts:79

### movie.state.changed

- key: MovieStateChanged
- audience: Cả hai user của phiên.
- trigger: Cập nhật playback (PATCH state) ở phiên bạn bè hoặc phiên ẩn danh, và khi kết thúc phần xem ẩn danh.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: videoUrl; type: string; note: URL video của phiên
  - field: positionSeconds; type: number; note: Vị trí phát tại thời điểm cập nhật (giây)
  - field: isPlaying; type: boolean; note: Trạng thái play/pause
  - field: positionUpdatedAt; type: string; note: ISO server timestamp; client nội suy vị trí khi isPlaying=true
- producers:
  - module: movie-match; function: updateState; condition: Sau UPDATE đơn (last-write-wins) của phiên bạn bè; gửi cho cả hai. [evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:221]
  - module: movie-match; function: publishAnonPlayback; condition: Phiên ẩn danh: gọi trong transaction từ updateAnonState và endAnonWatch (trước khi commit). [evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:845]
- consumers:
  - app: web (AnonMatchView); handling: Nếu sessionId khớp phiên hiện tại thì current.refetch() (REST poll 5s vẫn là fallback). [evidence: apps/web/src/features/movie-match/components/anon-match-view.tsx:423]
  - app: web (MovieSessionView); handling: Nếu sessionId khớp thì invalidate query session. [evidence: apps/web/src/features/movie-match/components/movie-session-view.tsx:52]
- bestEffort: true
- restFallback: GET /api/v1/movie-match/sessions/{id} (poll 10s: MOVIE_SESSION_REFETCH_INTERVAL_MS) và GET /api/v1/movie-match/anon/current (poll 5s)
- notes: Phiên bạn bè: UPDATE last-write-wins không lock. Phiên ẩn danh: publishAnonPlayback được gọi BÊN TRONG dataSource.transaction (trước COMMIT) — ngoại lệ so với nguyên tắc publish sau commit.
- contract: libs/common-dtos/src/lib/realtime-events.ts:81

### movie.session.ended

- key: MovieSessionEnded
- audience: Cả hai user của phiên bạn bè.
- trigger: Một bên chủ động rời (POST /movie-match/sessions/{id}/end); chưa có nhánh hết hạn tự động.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: reason; type: string; note: left | replaced (code hiện chỉ gửi left)
- producers:
  - module: movie-match; function: endSession; condition: Sau transaction kết thúc phiên (reason=left); gửi cho cả hai. [evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:271]
- consumers:
  - app: web (MovieSessionView); handling: Nếu sessionId khớp thì invalidate query session. [evidence: apps/web/src/features/movie-match/components/movie-session-view.tsx:58]
- bestEffort: true
- restFallback: GET /api/v1/movie-match/sessions/{id} (poll 10s)
- notes: Enum MovieSessionEndReason có left | replaced nhưng code publish hiện chỉ dùng Left.
- contract: libs/common-dtos/src/lib/realtime-events.ts:83

### movie.reaction.sent

- key: MovieReactionSent
- audience: Cả hai user của phiên ẩn danh.
- trigger: POST /movie-match/anon/sessions/{id}/reactions với emoji nằm trong whitelist MOVIE_MATCH_REACTIONS, khi phiên đang Active.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: emoji; type: string; note: Một trong ['😂','😍','😱','👏']
- producers:
  - module: movie-match; function: sendAnonReaction; condition: Sau khi kiểm whitelist + phiên Active; không có thao tác DB; gửi cho cả hai. [evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:632]
- consumers:
  - app: web (WatchingState); handling: Nếu sessionId khớp thì showFloatingReaction(emoji) (hiệu ứng nổi). [evidence: apps/web/src/features/movie-match/components/anon-match-view.tsx:202]
- bestEffort: true
- restFallback: Không có — hiệu ứng ephemeral, không persist, không kèm userId; client lỡ event chỉ mất hiệu ứng.
- notes: Realtime-only: không ghi DB nào.
- contract: libs/common-dtos/src/lib/realtime-events.ts:88

### minigame.session.started

- key: MiniGameSessionStarted
- audience: Cả hai user của ván (userLowId, userHighId).
- trigger: POST /mini-game/sessions tạo ván oẳn tù tì; publish sau transaction.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: gameType; type: string; note: Loại game (hiện là oẳn tù tì)
  - field: initiatorUserId; type: string; note: Người gọi POST /mini-game/sessions (hai bên đã là bạn)
- producers:
  - module: mini-game; function: createSession; condition: Sau transaction tạo ván; gửi cho cả hai; nhánh replay/race trả ván cũ không publish. [evidence: apps/core-api/src/modules/mini-game/mini-game.service.ts:121]
- bestEffort: true
- restFallback: GET /api/v1/mini-game/sessions/{id}
- notes: Chưa có consumer ở apps/web hay apps/admin (web chưa có UI mini-game).
- contract: libs/common-dtos/src/lib/realtime-events.ts:90

### minigame.session.resolved

- key: MiniGameSessionResolved
- audience: Cả hai user của ván.
- trigger: Move cuối cùng được nộp (POST /mini-game/sessions/{id}/moves) và ván chuyển sang resolved.
- payload:
  - field: sessionId; type: string; note: Id phiên/phòng của tính năng phát event (khác bảng theo event)
  - field: lowMove; type: string; note: rock | paper | scissors của userLowId
  - field: highMove; type: string; note: rock | paper | scissors của userHighId
  - field: winnerUserId; type: string | null; note: null nếu hòa
- producers:
  - module: mini-game; function: submitMove; condition: Sau transaction resolve (UPDATE status resolved + xóa active participant); chỉ khi nhánh resolved. [evidence: apps/core-api/src/modules/mini-game/mini-game.service.ts:227]
- bestEffort: true
- restFallback: GET /api/v1/mini-game/sessions/{id}
- notes: Chỉ MỘT event kèm cả hai move; không có event trung gian "một bên đã nộp" để kênh phụ không suy ra ai nộp trước.
- contract: libs/common-dtos/src/lib/realtime-events.ts:96
