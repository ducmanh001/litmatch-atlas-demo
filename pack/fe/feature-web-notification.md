# web feature notification

id: fe:feature:web:notification
directory: apps/web/src/features/notification/ (5 source files, 2 components, 3 spec files)

routes whose data comes from this feature: [/discovery](route-web-discovery.md), [/feed](route-web-feed.md), [/home](route-web-home.md), [/matching](route-web-matching.md), [/messages](route-web-messages.md), [/more](route-web-more.md), [/party](route-web-party.md), [/profile](route-web-profile.md), [/wallet](route-web-wallet.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useUnreadNotificationCount | GET /notifications/unread-count | notificationKeys.unreadCount | UNREAD_NOTIFICATION_COUNT_REFETCH_INTERVAL_MS |  |  | apps/web/src/features/notification/api.ts:47 |
| useNotifications | GET /notifications | notificationKeys.list |  |  |  | apps/web/src/features/notification/api.ts:59 |
| useMarkNotificationRead | POST /notifications/{notificationId}/read |  |  |  | invalidateQueries({ queryKey: notificationKeys.all }) | apps/web/src/features/notification/api.ts:77 |

## Components

- apps/web/src/features/notification/components/notification-bell.tsx: 6 interactive elements, uses useMarkNotificationRead, useLocale, useRouter, useTranslation, useNotifications, useState, useEffect, useUnreadNotificationCount
- apps/web/src/features/notification/components/realtime-notification-bridge.tsx: 0 interactive elements, uses usePathname, useQueryClient, useCurrentUser, useCallback, useRealtimeEvent

## Realtime events

- friend.message apps/web/src/features/notification/components/realtime-notification-bridge.tsx:53

## Tests

- apps/web/src/features/notification/api.spec.ts
- apps/web/src/features/notification/components/notification-bell.spec.tsx
- apps/web/src/features/notification/notification-copy.spec.ts
