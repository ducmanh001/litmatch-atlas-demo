# Allowed values enforced by CHECK constraints (status vocabularies)

id: db:vocab

| table | constraint | allowed values |
|---|---|---|
| admin_role_permissions | chk_admin_role_permissions_role | moderator, admin |
| call_sessions | call_sessions_call_kind_check | voice_match, friend |
| call_sessions | call_sessions_end_reason_check | completed, free_limit, insufficient_balance, pending_timeout |
| call_sessions | call_sessions_status_check | pending, active, ended |
| friendships | chk_friendships_source | soul_match, voice_match, palm_match, movie_match |
| image_assets | chk_image_assets_content_type | image/jpeg, image/png, image/webp, image/gif |
| image_assets | chk_image_assets_purpose | post, message, story |
| image_assets | chk_image_assets_status | pending, ready |
| ledger_entries | ledger_entries_direction_check | debit, credit |
| match_daily_quotas | match_daily_quotas_match_type_check | soul, voice |
| match_invites | match_invites_match_type_check | soul, voice |
| match_invites | match_invites_status_check | pending, accepted, declined, expired, cancelled |
| match_sessions | match_sessions_match_type_check | soul, voice |
| match_sessions | match_sessions_status_check | pending_confirm, confirmed, ended, expired |
| match_tickets | match_tickets_gender_preference_check | any, male, female |
| match_tickets | match_tickets_match_type_check | soul, voice |
| match_tickets | match_tickets_status_check | queued, matched, confirmed, expired, cancelled |
| mini_game_sessions | chk_mini_game_sessions_high_move | rock, paper, scissors |
| mini_game_sessions | chk_mini_game_sessions_low_move | rock, paper, scissors |
| mini_game_sessions | chk_mini_game_sessions_status | waiting_moves, resolved, cancelled |
| mood_status_events | chk_mood_status_events_kind | set, clear |
| movie_sessions | chk_movie_sessions_end_reason | left, replaced |
| movie_sessions | chk_movie_sessions_status | active, ended |
| palm_match_sessions | chk_palm_match_high_rating | like, skip |
| palm_match_sessions | chk_palm_match_low_rating | like, skip |
| palm_match_sessions | chk_palm_match_outcome | matched, not_matched, expired, cancelled |
| palm_match_sessions | chk_palm_match_status | active, completed |
| palm_reading_templates | chk_palm_reading_templates_category | love, career, health, general |
| payos_payment_orders | payos_payment_orders_status_check | pending, paid, cancelled, expired |
| posts | chk_posts_audience | public, friends, only_me |
| reports | chk_reports_target_type | user, video |
| soul_match_ratings | soul_match_ratings_verdict_check | rude, boring, like |
| stories | chk_stories_audience | public, friends |
| support_tickets | chk_support_ticket_category | feedback, bug, idea |
| support_tickets | chk_support_ticket_status | open, in_progress, resolved, closed |
| videos | videos_status_check | uploading, processing, pending_review, published, removed, rejected, failed |
