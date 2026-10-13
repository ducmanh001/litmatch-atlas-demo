# web feature privacy

id: fe:feature:web:privacy
directory: apps/web/src/features/privacy/ (1 source files, 0 components, 0 spec files)

routes whose data comes from this feature: [/privacy](route-web-privacy.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| usePrivacySettings | GET /users/me/privacy | privacyKeys.settings |  |  |  | apps/web/src/features/privacy/api.ts:15 |
| useUpdatePrivacySettings | PUT /users/me/privacy |  |  |  | invalidateQueries({ queryKey: privacyKeys.settings }) | apps/web/src/features/privacy/api.ts:27 |

## Components



## Tests

none
