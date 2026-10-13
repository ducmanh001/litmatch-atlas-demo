# web feature friend-chat

id: fe:feature:web:friend-chat
directory: apps/web/src/features/friend-chat/ (9 source files, 7 components, 6 spec files)

routes whose data comes from this feature: [/chat/:friendUserId](route-web-chat-frienduserid.md), [/chat/:friendUserId/call](route-web-chat-frienduserid-call.md), [/friends](route-web-friends.md), [/messages](route-web-messages.md), [/movie-match](route-web-movie-match.md), [/movie-match/:sessionId](route-web-movie-match-sessionid.md), [/profile](route-web-profile.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useFriends | GET /friends | friendChatKeys.friends |  |  |  | apps/web/src/features/friend-chat/api.ts:31 |
| useFriendConnections | GET /friends/connections | friendChatKeys.connections |  |  |  | apps/web/src/features/friend-chat/api.ts:45 |
| useConversationWithFriend | GET /friends/{friendUserId}/conversation | friendChatKeys.conversation(friendUserId) |  |  |  | apps/web/src/features/friend-chat/api.ts:55 |
| usePartnerProfile | GET /users/{id} | friendChatKeys.partnerProfile(friendUserId) |  |  |  | apps/web/src/features/friend-chat/api.ts:72 |
| useConversationMessages | GET /conversations/{id}/messages | friendChatKeys.messages(conversationId) | FRIEND_MESSAGES_REFETCH_INTERVAL_MS |  |  | apps/web/src/features/friend-chat/api.ts:87 |
| useSendFriendMessage | POST /conversations/{id}/messages |  |  |  | invalidateQueries({ queryKey: friendChatKeys.messages(conversationId), }) | apps/web/src/features/friend-chat/api.ts:107 |
| useMarkConversationRead | POST /conversations/{id}/read |  |  |  | invalidateQueries({ queryKey: friendChatKeys.friends, }) | apps/web/src/features/friend-chat/api.ts:136 |
| useMuteConversation | POST /conversations/{id}/mute |  |  |  | invalidateQueries({ queryKey: friendChatKeys.friends, }) | apps/web/src/features/friend-chat/api.ts:154 |
| useBlockUser | POST /safety/blocks/{targetUserId} |  |  |  |  | apps/web/src/features/friend-chat/api.ts:178 |
| useReportUser | POST /safety/reports |  |  |  |  | apps/web/src/features/friend-chat/api.ts:188 |

## Components

- apps/web/src/features/friend-chat/components/conversation-inbox.tsx: 1 interactive elements, uses useFriends, useLocale, useTranslation, useState
- apps/web/src/features/friend-chat/components/conversation-thread.tsx: 8 interactive elements, uses useQueryClient, useRouter, useBlockUser, useReportUser, useFriends, useJoinFriendCall, useMarkConversationRead, useMuteConversation, usePartnerProfile, useConversationWithFriend, useEffect, useRealtimeEvent
- apps/web/src/features/friend-chat/components/friend-avatar.tsx: 0 interactive elements, uses no hooks
- apps/web/src/features/friend-chat/components/friend-call-room.tsx: 5 interactive elements, uses useRouter, useQueryClient, usePartnerProfile, useCallRoom, useCall, useEndCall, useRef, useState, useEffect, useRealtimeEvent
- apps/web/src/features/friend-chat/components/friends-directory.tsx: 1 interactive elements, uses useTranslation, useRouter, useOpenProfileConversation, useFriendConnections, useFriends, useCurrentUser
- apps/web/src/features/friend-chat/components/message-composer.tsx: 2 interactive elements, uses useForm, useSendFriendMessage, useIdempotencyKey, useState
- apps/web/src/features/friend-chat/components/message-list.tsx: 0 interactive elements, uses useCurrentUser, useConversationMessages, useEffect

## Realtime events

- friend.message apps/web/src/features/friend-chat/components/conversation-thread.tsx:64
- call.ended apps/web/src/features/friend-chat/components/friend-call-room.tsx:94

## Tests

- apps/web/src/features/friend-chat/api.spec.ts
- apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx
- apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx
- apps/web/src/features/friend-chat/components/friends-directory.spec.tsx
- apps/web/src/features/friend-chat/components/message-list.spec.tsx
- apps/web/src/features/friend-chat/send-message-schema.spec.ts
