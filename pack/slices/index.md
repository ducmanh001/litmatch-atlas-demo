# Feature slices

id: slices:index

One file per product feature of docs/feature-registry.json, following it from the screen to the database and back. Start here when asked to "follow feature X".

| feature | status | modules | FE routes | endpoints | flows |
|---|---|---|---|---|---|
| [auth-session](auth-session.md) | implemented | auth | web:/login, admin:/login, web:/more, web:/profile, admin:/ | 9 | auth-guest-upgrade, auth-otp-login, auth-refresh-and-logout |
| [runtime-capabilities](runtime-capabilities.md) | implemented | app-shell | web:/login, web:/wallet, admin:/login | 4 | app-shell-health-and-readiness |
| [economy-ledger](economy-ledger.md) | implemented | economy | web:/wallet, web:/discovery, web:/feed, web:/home, web:/matching, web:/messages, web:/more, web:/party | 12 | payos-topup-webhook, iap-store-notifications-and-refund-poll, vip-purchase-iap |
| [matching](matching.md) | implemented | matching | web:/matching | 12 | matching-ticket-status-cancel-speedup, matching-invite-create, matching-invite-decline-cancel |
| [soul-match](soul-match.md) | implemented | soul-match | web:/matching/soul/:sessionId | 6 | soul-match-room-reads-and-reveal, soul-match-session, soul-match-send-message |
| [calling](calling.md) | implemented | calling | web:/matching/voice/:sessionId, web:/chat/:friendUserId/call, web:/chat/:friendUserId | 7 | matching-to-call, calling-end-and-ticker, calling-like-after-call |
| [party-room](party-room.md) | implemented | party-room | web:/party/:roomId, web:/party, web:/feed, web:/home, admin:/ | 12 | party-room-detail-and-comments, party-room-lifecycle, party-room-speaker-invite |
| [gift](gift.md) | implemented | gift | web:/party/:roomId, web:/users/:id, web:/video | 4 | admin-gift-catalog, gift-in-party-room, gift-on-video |
| [friend-chat-streak](friend-chat-streak.md) | implemented | friend | web:/chat/:friendUserId, web:/users/:id/followers, web:/users/:id/following, web:/users/:id, web:/friends, web:/movie-match/:sessionId, web:/profile, web:/messages | 15 | friend-list-and-conversation-reads, friend-follow-lists-and-connections, friend-profile-follow-and-actions |
| [feed-stories](feed-stories.md) | implemented | feed | web:/feed/:postId, web:/feed, web:/profile, web:/users/:id | 16 | feed-reaction-with-notification, feed-read-posts, story-ring-view-and-viewers |
| [safety](safety.md) | implemented | safety | web:/chat/:friendUserId | 4 | safety-block-and-unblock, safety-report-and-trust-score |
| [avatar-discovery-mood](avatar-discovery-mood.md) | implemented | avatar, discovery, mood | web:/profile/edit, web:/discovery | 16 | mood-status-visibility, discovery-browse-and-nearby, avatar-buy-item |
| [notification](notification.md) | implemented | notification | web:/discovery, web:/feed, web:/home, web:/matching, web:/messages, web:/more, web:/party, web:/profile | 5 | message-push-notification |
| [support-tickets](support-tickets.md) | implemented | support, admin | admin:/moderation, admin:/config, admin:/permissions, admin:/users, admin:/gifts, admin:/economy, web:/help, admin:/rooms | 33 | admin-economy-inspect-and-config, admin-staff-and-permissions, admin-support-tickets |
| [mini-game](mini-game.md) | implemented | mini-game |  | 4 | mini-game-session |
| [movie-match](movie-match.md) | implemented | movie-match | web:/movie-match, web:/movie-match/:sessionId | 13 | movie-match-anon-chat-finish-reactions, movie-match-friend-session, movie-match-anonymous |
| [palm-match](palm-match.md) | implemented | palm-match | web:/palm-match | 6 | palm-match-queue-and-rating |
| [short-video](short-video.md) | implemented | short-video | web:/video | 11 | short-video-browse-and-detail, short-video-comments, short-video-publish-moderation |
| [user-profile-open-entry](user-profile-open-entry.md) | implemented | user | web:/privacy, web:/users/:id, web:/chat/:friendUserId, web:/feed, web:/movie-match/:sessionId, web:/profile/edit, web:/chat/:friendUserId/call, web:/feed/:postId | 6 | user-presence-read |
| [privacy-analytics-consent](privacy-analytics-consent.md) | implemented |  |  | 0 |  |
| [realtime-fanout](realtime-fanout.md) | implemented | signaling-gateway, soul-match, matching, calling, friend, party-room, gift, movie-match, mini-game | web:/movie-match, web:/party/:roomId, web:/matching, web:/chat/:friendUserId, web:/matching/soul/:sessionId, web:/matching/voice/:sessionId, web:/movie-match/:sessionId, web:/users/:id | 73 | movie-match-anon-chat-finish-reactions, friend-list-and-conversation-reads, matching-to-call |

Frontend-only or shared pieces: see ../fe/index.md. Features present in the code but absent from the registry: admin console, profile follow lists, short-video are partly covered; check ../modules/index.md for the full module list.
