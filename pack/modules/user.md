# Module user

id: module:user
kind: core-api module
code: apps/core-api/src/modules/user

Sở hữu bảng users (hồ sơ, status, role, isGuest, trustScore) và user_privacy_settings; cung cấp UserService/PrivacySettingsService/PublicProfileDto cho gần như mọi module khác. Không sở hữu danh tính đăng nhập (auth), không biết khái niệm block/report (safety) và không tự ghi presence (signaling-gateway ghi, user chỉ đọc).

## Facts

- tables owned: user_privacy_settings (ALSO ACCESSED BY discovery,feed), users (ALSO ACCESSED BY auth,friend,matching,safety)
- API: 6 endpoints, 0 public, 0 admin-only, 0 require Idempotency-Key, 0 with own throttle
- env vars read: REDIS_URL, USER_DEFAULT_AVATAR_ID
- error codes: 3 in UserErrors
- tests: 4 unit spec files, 0 integration spec files
- history: 13 commits (0 fixes), last 2026-08-12
- features: user-profile-open-entry

## Where to read next

- tables: ../db/user.md
- API contract: ../api/user.md
- why it talks to other services: ../service-map/user.md and ../service-map/user-techniques.md
- error codes: ../errors/usererrors.md
- flows involving it: [admin-ban-and-refund](../flows/admin-ban-and-refund.md), [admin-broadcast-notification](../flows/admin-broadcast-notification.md), [admin-dashboard](../flows/admin-dashboard.md), [admin-staff-and-permissions](../flows/admin-staff-and-permissions.md), [admin-user-lookup-and-unban](../flows/admin-user-lookup-and-unban.md), [auth-guest-login-and-socket](../flows/auth-guest-login-and-socket.md), [auth-guest-upgrade](../flows/auth-guest-upgrade.md), [auth-otp-login](../flows/auth-otp-login.md), [auth-refresh-and-logout](../flows/auth-refresh-and-logout.md), [auth-search-by-phone](../flows/auth-search-by-phone.md), [auth-social-login](../flows/auth-social-login.md), [discovery-browse-and-nearby](../flows/discovery-browse-and-nearby.md), [feed-comment-with-notification](../flows/feed-comment-with-notification.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-post-with-image](../flows/feed-post-with-image.md), [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [feed-read-posts](../flows/feed-read-posts.md), [friend-direct-call](../flows/friend-direct-call.md), [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md), [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md), [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md), [friend-profile-follow-and-actions](../flows/friend-profile-follow-and-actions.md), [gift-on-video](../flows/gift-on-video.md), [gift-to-profile-chat-gate](../flows/gift-to-profile-chat-gate.md), [matching-invite-accept](../flows/matching-invite-accept.md), [matching-invite-create](../flows/matching-invite-create.md), [matching-invite-decline-cancel](../flows/matching-invite-decline-cancel.md), [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md), [party-room-lifecycle](../flows/party-room-lifecycle.md), [safety-block-and-unblock](../flows/safety-block-and-unblock.md), [safety-report-and-trust-score](../flows/safety-report-and-trust-score.md), [short-video-browse-and-detail](../flows/short-video-browse-and-detail.md), [short-video-comments](../flows/short-video-comments.md), [short-video-publish-moderation](../flows/short-video-publish-moderation.md), [soul-match-room-reads-and-reveal](../flows/soul-match-room-reads-and-reveal.md), [soul-match-send-message](../flows/soul-match-send-message.md), [story-reply-to-dm](../flows/story-reply-to-dm.md), [story-ring-view-and-viewers](../flows/story-ring-view-and-viewers.md), [user-presence-read](../flows/user-presence-read.md)
