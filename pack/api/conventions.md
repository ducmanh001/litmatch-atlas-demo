# API conventions

id: api:conventions

- Prefix /api/v1. Single OpenAPI file openapi/core-api.json (generate: pnpm openapi:emit; client: pnpm openapi:sync).
- Success is wrapped as { data, meta? }. Cursor pagination: query limit (1-100, default 20) and cursor (opaque, from meta.nextCursor).
- Global guards in order: ThrottlerGuard, JwtAuthGuard, RolesGuard. Routes without JWT must carry @Public().
- Refresh token is an httpOnly cookie; cookie routes need header X-CSRF-Token equal to cookie csrf_token.
- Idempotency-Key header (max 128 chars) is required on side-effecting endpoints; missing -> COMMON_IDEMPOTENCY_KEY_MISSING.
- Default throttle: THROTTLE_LIMIT per THROTTLE_TTL_SECONDS per IP for routes without own @Throttle.
- Webhooks (PayOS, Apple, Google RTDN, 2x LiveKit) are outside OpenAPI.
