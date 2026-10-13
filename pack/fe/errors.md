# Error handling in the frontend

id: fe:errors

The FE references 3 of 182 backend error codes by string. Everything else surfaces through the generic ApiError message path (isApiError(error) ? error.message : fallback i18n key).

## Codes referenced by FE code

- CALLING_CALL_ENDED
  - apps/web/src/features/voice-match/components/voice-call-room.tsx:91 (error.code === 'CALLING_CALL_ENDED' ||
- CALLING_SESSION_NOT_CALLABLE
  - apps/web/src/features/voice-match/components/voice-call-room.tsx:92 error.code === 'CALLING_SESSION_NOT_CALLABLE' ||
- CALLING_SESSION_NOT_FOUND
  - apps/web/src/features/voice-match/components/voice-call-room.tsx:93 error.code === 'CALLING_SESSION_NOT_FOUND')

## Backend codes of modules the FE calls that the FE never references

- admin: 6 unreferenced of 6 codes
- app-shell: 0 unreferenced of 0 codes
- auth: 14 unreferenced of 14 codes
- avatar: 5 unreferenced of 5 codes
- calling: 3 unreferenced of 6 codes
- discovery: 7 unreferenced of 7 codes
- economy: 19 unreferenced of 19 codes
- feed: 9 unreferenced of 9 codes
- friend: 9 unreferenced of 9 codes
- gift: 5 unreferenced of 5 codes
- matching: 20 unreferenced of 20 codes
- media: 5 unreferenced of 5 codes
- movie-match: 11 unreferenced of 11 codes
- notification: 2 unreferenced of 2 codes
- palm-match: 6 unreferenced of 6 codes
- party-room: 17 unreferenced of 17 codes
- safety: 3 unreferenced of 3 codes
- short-video: 7 unreferenced of 7 codes
- soul-match: 9 unreferenced of 9 codes
- support: 4 unreferenced of 4 codes
- user: 3 unreferenced of 3 codes
