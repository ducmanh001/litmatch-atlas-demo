# Domain rules, error handling, glossary, conventions: docDrift

id: system:domain:docDrift

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- doc: docs/05-coding-standards.md § 5.1
- claim: Format lỗi thống nhất chỉ gồm (code, message, traceId).
- actual: Bộ lọc còn thêm `details` tuỳ chọn: validation trả `details.violations`, DomainException trả `details` của nó (vd `required`, `limit`).
- evidence (evidence): docs/05-coding-standards.md:24, apps/core-api/src/common/filters/global-exception.filter.ts:83, libs/common-dtos/src/lib/api-response.ts:12

### #2

- doc: docs/06-domain-rules.md (Refund/chargeback IAP)
- claim: Refund-sau-tiêu lặp lại nhiều lần là tín hiệu gian lận, hạ trust score hoặc khoá nạp.
- actual: RefundService chỉ ghi bút toán đảo và đánh dấu receipt; không thấy mã hạ trust score hay khoá nạp theo refund trong modules economy, safety, user (grep "refund").
- confidence: medium
- evidence (evidence): docs/06-domain-rules.md:38, apps/core-api/src/modules/economy/services/refund.service.ts:35

### #3

- doc: docs/06-domain-rules.md (Đăng ký/đăng nhập)
- claim: Guest bị giới hạn: không nạp diamond.
- actual: Chỉ đường payOS có chặn guest (assertNotGuestForPayos); trong modules/economy không có kiểm tra isGuest nào khác, nên IAP verify không chặn guest theo mã.
- confidence: medium
- evidence (evidence): docs/06-domain-rules.md:30, apps/core-api/src/modules/economy/economy.controller.ts:90

### #4

- doc: apps/core-api/src/modules/safety/safety.service.ts (comment của getHiddenUserIds)
- claim: Report ẩn vĩnh viễn, không theo SAFETY_REPORT_COOLDOWN_DAYS như matching.
- actual: Cooldown của matching (canPair) dùng SAFETY_REMATCH_COOLDOWN_DAYS (mặc định 30); SAFETY_REPORT_COOLDOWN_DAYS (mặc định 7) là cửa sổ chống trừ trust score lặp theo cặp reporter-target. docs/06 nêu đúng tên SAFETY_REMATCH_COOLDOWN_DAYS.
- evidence (evidence): apps/core-api/src/modules/safety/safety.service.ts:263, apps/core-api/src/modules/safety/safety.service.ts:298, docs/06-domain-rules.md:49
