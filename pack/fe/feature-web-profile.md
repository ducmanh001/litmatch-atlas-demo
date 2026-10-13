# web feature profile

id: fe:feature:web:profile
directory: apps/web/src/features/profile/ (10 source files, 8 components, 5 spec files)

routes whose data comes from this feature: [/friends](route-web-friends.md), [/profile](route-web-profile.md), [/profile/edit](route-web-profile-edit.md), [/users/:id](route-web-users-id.md), [/users/:id/followers](route-web-users-id-followers.md), [/users/:id/following](route-web-users-id-following.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useFollowCounts | GET /profiles/{profileUserId}/follow-counts | profileKeys.followCounts(id) |  |  |  | apps/web/src/features/profile/api.ts:34 |
| useFollowList | GET /profiles/{profileUserId}/followers, GET /profiles/{profileUserId}/following | profileKeys.followList(id, kind) |  |  |  | apps/web/src/features/profile/api.ts:48 |
| usePublicProfile | GET /users/{id} | profileKeys.public(id) |  |  |  | apps/web/src/features/profile/api.ts:70 |
| useProfileActions | GET /profiles/{profileUserId}/actions | profileKeys.actions(id) |  |  |  | apps/web/src/features/profile/api.ts:82 |
| useProfileGiftCatalog | GET /gifts | ['gifts', 'catalog'] as const |  |  |  | apps/web/src/features/profile/api.ts:96 |
| useFollowProfile | POST /profiles/{profileUserId}/follow, DELETE /profiles/{profileUserId}/follow |  |  |  | invalidateQueries({ queryKey: profileKeys.actions(profileUserId), }); invalidateQueries({ queryKey: ['profile', 'follow-counts'], }); invalidateQueries({ queryKey: ['profile', 'follow-list'], }); invalidateQueries({ queryKey: ['friend-chat', 'connections'], }) | apps/web/src/features/profile/api.ts:107 |
| useOpenProfileConversation | POST /profiles/{profileUserId}/conversation |  |  |  |  | apps/web/src/features/profile/api.ts:143 |
| useSendProfileGift | POST /profiles/{profileUserId}/gifts |  |  |  |  | apps/web/src/features/profile/api.ts:155 |
| usePublicPresence | GET /users/{id}/presence | profileKeys.presence(id) |  | 30_000 |  | apps/web/src/features/profile/api.ts:173 |
| useUpdateProfile | PATCH /users/me |  |  |  | invalidateQueries({ queryKey: currentUserKey }) | apps/web/src/features/profile/api.ts:186 |

## Components

- apps/web/src/features/profile/components/edit-profile-view.tsx: 2 interactive elements, uses useCurrentUser, useState
- apps/web/src/features/profile/components/follow-list-view.tsx: 2 interactive elements, uses useTranslation, useFollowProfile, useCurrentUser, useFollowCounts, useFollowList
- apps/web/src/features/profile/components/profile-form.tsx: 4 interactive elements, uses useForm, useUpdateProfile
- apps/web/src/features/profile/components/profile-menu.tsx: 1 interactive elements, uses useLogout, useTranslation
- apps/web/src/features/profile/components/profile-posts-grid.tsx: 0 interactive elements, uses useUserTimeline, useTranslation
- apps/web/src/features/profile/components/profile-stats.tsx: 0 interactive elements, uses useWallet, useFriendConnections, useCurrentUser, useFollowCounts, useTranslation
- apps/web/src/features/profile/components/profile-view.tsx: 0 interactive elements, uses useCurrentUser, useTranslation, useWallet, checks isGuest
- apps/web/src/features/profile/components/public-profile-view.tsx: 4 interactive elements, uses useRouter, usePublicProfile, usePublicPresence, useProfileActions, useFollowProfile, useOpenProfileConversation, useSendProfileGift, useProfileGiftCatalog, useIdempotencyKey

## Validation schemas

- updateProfileSchema (apps/web/src/features/profile/update-profile-schema.ts:4): nickname=z.string().trim().min(2, 'Tối thiểu 2 ký tự').max(50); gender=z.enum(['unknown', 'male', 'female', 'other']); birthDate=z .string() .refine( (value) => value === '' || !Number.isNaN(Date.pa…; region=z .string() .trim() .refine( (value) => value === undefined || value …; interests=z .array(z.string().trim().min(1).max(32)) .max(5, 'Tối đa 5 sở thích…; seekingGender=z.enum(['male', 'female', 'any']); seekingAgeMin=z.number().int().min(18).max(99); seekingAgeMax=z.number().int().min(18).max(99)

## Tests

- apps/web/src/features/profile/components/follow-list-view.spec.tsx
- apps/web/src/features/profile/components/profile-stats.spec.tsx
- apps/web/src/features/profile/components/profile-view.spec.tsx
- apps/web/src/features/profile/components/public-profile-view.spec.tsx
- apps/web/src/features/profile/update-profile-schema.spec.ts
