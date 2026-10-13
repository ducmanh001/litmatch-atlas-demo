# Frontend map (web = Next.js, admin = Vite)

id: fe:index

Static analysis of apps/web/src and apps/admin/src. Each route file lists the data it reads, the actions that change data, realtime events, navigation, error handling and tests, all with file:line.

## Web routes

| route | layout | page file | data features | reads | writes | realtime events | UI map card |
|---|---|---|---|---|---|---|---|
| [/](route-web-root.md) | public | (public)/page.tsx |  | 0 | 0 |  | yes |
| [/about](route-web-about.md) | public | (public)/about/page.tsx |  | 0 | 0 |  |  |
| [/chat/:friendUserId](route-web-chat-frienduserid.md) | app | (app)/chat/[friendUserId]/page.tsx | friend-chat, voice-match | 5 | 7 | friend.message, (dynamic: event) | yes |
| [/chat/:friendUserId/call](route-web-chat-frienduserid-call.md) | app | (app)/chat/[friendUserId]/call/page.tsx | friend-chat, voice-match | 2 | 3 | call.ended, (dynamic: event) |  |
| [/community-guidelines](route-web-community-guidelines.md) | public | (public)/community-guidelines/page.tsx |  | 0 | 0 |  |  |
| [/contact](route-web-contact.md) | public | (public)/contact/page.tsx |  | 0 | 0 |  |  |
| [/cookies](route-web-cookies.md) | public | (public)/cookies/page.tsx |  | 0 | 0 |  |  |
| [/discovery](route-web-discovery.md) | app | (app)/discovery/page.tsx | discovery, notification, wallet | 5 | 5 |  | yes |
| [/features](route-web-features.md) | public | (public)/features/page.tsx |  | 0 | 0 |  |  |
| [/feed](route-web-feed.md) | app | (app)/feed/page.tsx | feed, notification, party-room, wallet | 8 | 8 |  | yes |
| [/feed/:postId](route-web-feed-postid.md) | app | (app)/feed/[postId]/page.tsx | feed | 4 | 4 |  |  |
| [/friends](route-web-friends.md) | app | (app)/friends/page.tsx | friend-chat, profile | 3 | 1 |  | yes |
| [/help](route-web-help.md) | public | (public)/help/page.tsx | support | 1 | 1 |  |  |
| [/home](route-web-home.md) | app | (app)/home/page.tsx | notification, party-room, wallet | 5 | 3 |  | yes |
| [/how-it-works](route-web-how-it-works.md) | public | (public)/how-it-works/page.tsx |  | 0 | 0 |  |  |
| [/login](route-web-login.md) | login | login/page.tsx |  | 1 | 4 |  |  |
| [/matching](route-web-matching.md) | app | (app)/matching/page.tsx | matching, notification, wallet | 6 | 9 | match.confirmed, (dynamic: event) | yes |
| [/matching/soul/:sessionId](route-web-matching-soul-sessionid.md) | app | (app)/matching/soul/[sessionId]/page.tsx | soul-match | 3 | 3 | soul.message, soul.matched, soul.ended, (dynamic: event) |  |
| [/matching/voice/:sessionId](route-web-matching-voice-sessionid.md) | app | (app)/matching/voice/[sessionId]/page.tsx | voice-match | 1 | 5 | call.ended, (dynamic: event) |  |
| [/messages](route-web-messages.md) | app | (app)/messages/page.tsx | friend-chat, notification, wallet | 4 | 3 |  |  |
| [/more](route-web-more.md) | app | (app)/more/page.tsx | notification, wallet | 4 | 4 |  |  |
| [/movie-match](route-web-movie-match.md) | app | (app)/movie-match/page.tsx | friend-chat, movie-match | 4 | 8 | movie.session.started, (dynamic: event), movie.reaction.sent, movie.state.changed |  |
| [/movie-match/:sessionId](route-web-movie-match-sessionid.md) | app | (app)/movie-match/[sessionId]/page.tsx | friend-chat, movie-match | 5 | 4 | movie.state.changed, movie.session.ended, (dynamic: event) |  |
| [/palm-match](route-web-palm-match.md) | app | (app)/palm-match/page.tsx | palm-match | 1 | 4 |  |  |
| [/party](route-web-party.md) | app | (app)/party/page.tsx | notification, party-room, wallet | 5 | 4 |  | yes |
| [/party/:roomId](route-web-party-roomid.md) | app | (app)/party/[roomId]/page.tsx | party-room | 4 | 8 | party.member.joined, party.member.left, party.member.disconnected, party.member.reconnected, party.role.changed, party.speaker.invite.received, party.room.closed, party.host.disconnected, party.host.reconnected, gift.sent, (dynamic: event), party.comment.created |  |
| [/privacy](route-web-privacy.md) | public | (public)/privacy/page.tsx | privacy | 2 | 1 |  |  |
| [/privacy-policy](route-web-privacy-policy.md) | public | (public)/privacy-policy/page.tsx |  | 0 | 0 |  |  |
| [/profile](route-web-profile.md) | app | (app)/profile/page.tsx | feed, friend-chat, notification, profile, wallet | 8 | 7 |  | yes |
| [/profile/edit](route-web-profile-edit.md) | app | (app)/profile/edit/page.tsx | avatar, profile | 4 | 4 |  | yes |
| [/terms](route-web-terms.md) | public | (public)/terms/page.tsx |  | 0 | 0 |  |  |
| [/users/:id](route-web-users-id.md) | app | (app)/users/[id]/page.tsx | feed, profile | 7 | 7 |  | yes |
| [/users/:id/followers](route-web-users-id-followers.md) | app | (app)/users/[id]/followers/page.tsx | profile | 4 | 2 |  |  |
| [/users/:id/following](route-web-users-id-following.md) | app | (app)/users/[id]/following/page.tsx | profile | 4 | 2 |  |  |
| [/video](route-web-video.md) | app | (app)/video/page.tsx | party-room, short-video | 3 | 5 |  | yes |
| [/wallet](route-web-wallet.md) | app | (app)/wallet/page.tsx | notification, wallet | 7 | 5 |  | yes |

## Admin routes

| route | page file | reads | writes |
|---|---|---|---|
| [/login](route-admin-login.md) | shared/auth/login-page.tsx | 1 | 3 |
| [/](route-admin-root.md) | app/app-shell.tsx | 1 | 1 |
| [/](route-admin-root.md) | features/dashboard/pages/dashboard-page.tsx | 2 | 0 |
| [/users](route-admin-users.md) | features/users/pages/users-page.tsx | 1 | 2 |
| [/moderation](route-admin-moderation.md) | features/moderation/pages/moderation-page.tsx | 4 | 7 |
| [/gifts](route-admin-gifts.md) | features/gifts/pages/gifts-page.tsx | 1 | 2 |
| [/economy](route-admin-economy.md) | features/economy/pages/economy-page.tsx | 2 | 1 |
| [/rooms](route-admin-rooms.md) | features/rooms/pages/rooms-page.tsx | 1 | 1 |
| [/config](route-admin-config.md) | features/config/pages/config-page.tsx | 1 | 3 |
| [/permissions](route-admin-permissions.md) | features/permissions/pages/permissions-page.tsx | 2 | 2 |

## Features (directories)

- [web/avatar](feature-web-avatar.md): 6 hooks, 1 components, 6 endpoints
- [web/discovery](feature-web-discovery.md): 4 hooks, 4 components, 4 endpoints
- [web/feed](feature-web-feed.md): 11 hooks, 9 components, 10 endpoints
- [web/friend-chat](feature-web-friend-chat.md): 10 hooks, 7 components, 10 endpoints
- [web/home](feature-web-home.md): 0 hooks, 5 components, 0 endpoints
- [web/matching](feature-web-matching.md): 10 hooks, 7 components, 10 endpoints
- [web/more](feature-web-more.md): 0 hooks, 1 components, 0 endpoints
- [web/movie-match](feature-web-movie-match.md): 14 hooks, 4 components, 13 endpoints
- [web/notification](feature-web-notification.md): 3 hooks, 2 components, 3 endpoints
- [web/palm-match](feature-web-palm-match.md): 6 hooks, 1 components, 5 endpoints
- [web/party-room](feature-web-party-room.md): 15 hooks, 8 components, 14 endpoints
- [web/privacy](feature-web-privacy.md): 2 hooks, 0 components, 2 endpoints
- [web/profile](feature-web-profile.md): 10 hooks, 8 components, 12 endpoints
- [web/short-video](feature-web-short-video.md): 9 hooks, 5 components, 8 endpoints
- [web/soul-match](feature-web-soul-match.md): 6 hooks, 7 components, 6 endpoints
- [web/support](feature-web-support.md): 2 hooks, 0 components, 2 endpoints
- [web/voice-match](feature-web-voice-match.md): 7 hooks, 1 components, 6 endpoints
- [web/wallet](feature-web-wallet.md): 8 hooks, 5 components, 8 endpoints
- [admin/config](feature-admin-config.md): 4 hooks, 1 components, 4 endpoints
- [admin/dashboard](feature-admin-dashboard.md): 1 hooks, 1 components, 1 endpoints
- [admin/economy](feature-admin-economy.md): 3 hooks, 1 components, 3 endpoints
- [admin/gifts](feature-admin-gifts.md): 3 hooks, 1 components, 3 endpoints
- [admin/moderation](feature-admin-moderation.md): 11 hooks, 4 components, 11 endpoints
- [admin/permissions](feature-admin-permissions.md): 4 hooks, 1 components, 4 endpoints
- [admin/rooms](feature-admin-rooms.md): 3 hooks, 1 components, 3 endpoints
- [admin/users](feature-admin-users.md): 3 hooks, 1 components, 3 endpoints

Other files: [ui-action-map.md](ui-action-map.md), [state-and-cache.md](state-and-cache.md), [errors.md](errors.md), i18n-*.md.
