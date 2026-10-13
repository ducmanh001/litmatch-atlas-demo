# Module safety

id: module:safety
kind: core-api module
code: apps/core-api/src/modules/safety

Sở hữu log append-only Report/Block, trust-score penalty chống vote-kick và các câu hỏi đồng bộ 'hai người này có được thấy/ghép/nhắn nhau không' (isBlocked, getBlockedUserIds, getHiddenUserIds, canPair). KHÔNG sở hữu ban tài khoản (UserStatus.Banned nằm ở User/Admin), không sở hữu video/feed (module gọi tự validate target) và không áp penalty lên hàng đợi matching (Matching tự đọc trustScore).

## Facts

- tables owned: blocks, reports
- touches tables of other modules: users (owner user; read 1, write 0)
- API: 4 endpoints, 0 public, 0 admin-only, 0 require Idempotency-Key, 1 with own throttle
- env vars read: SAFETY_REPORT_COOLDOWN_DAYS, SAFETY_TRUST_PENALTY_PER_REPORT, SAFETY_TRUST_PENALTY_DAILY_CAP, SAFETY_TRUST_SCORE_FLOOR, SAFETY_REMATCH_COOLDOWN_DAYS
- error codes: 3 in SafetyErrors
- tests: 1 unit spec files, 0 integration spec files
- history: 8 commits (1 fixes), last 2026-08-14
- docs: docs/services/safety-service.md (last changed 2026-07-28)
- features: safety

## Where to read next

- tables: ../db/safety.md
- API contract: ../api/safety.md
- why it talks to other services: ../service-map/safety.md and ../service-map/safety-techniques.md
- error codes: ../errors/safetyerrors.md
- flows involving it: [admin-report-resolution](../flows/admin-report-resolution.md), [discovery-browse-and-nearby](../flows/discovery-browse-and-nearby.md), [feed-comment-with-notification](../flows/feed-comment-with-notification.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [feed-read-posts](../flows/feed-read-posts.md), [friend-direct-call](../flows/friend-direct-call.md), [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md), [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md), [friend-profile-follow-and-actions](../flows/friend-profile-follow-and-actions.md), [matching-invite-accept](../flows/matching-invite-accept.md), [matching-invite-create](../flows/matching-invite-create.md), [message-push-notification](../flows/message-push-notification.md), [mood-status-visibility](../flows/mood-status-visibility.md), [movie-match-anonymous](../flows/movie-match-anonymous.md), [palm-match-queue-and-rating](../flows/palm-match-queue-and-rating.md), [safety-block-and-unblock](../flows/safety-block-and-unblock.md), [safety-report-and-trust-score](../flows/safety-report-and-trust-score.md), [short-video-report](../flows/short-video-report.md), [story-reply-to-dm](../flows/story-reply-to-dm.md), [story-ring-view-and-viewers](../flows/story-ring-view-and-viewers.md)
