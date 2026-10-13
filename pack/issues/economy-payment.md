# Issues in area economy-payment

id: issues:economy-payment

### P001: Guard ledger chặn nhầm bút toán đảo (refund/chargeback)

status: fixed; tier: A; date: 2026-07-10; confidence: high

- problem: CHECK (balance >= 0) ở DB và guard ledger.service chặn cả bút toán Reversal hợp lệ, nên refund/chargeback Apple/Google sau khi user đã tiêu diamond không ghi được.
- root cause: Spec Economy giả định balance không bao giờ âm; gift 2 currency (DIA/PTS) cũng không cân được trong 1 cặp Nợ/Có.
- resolution: Bỏ CHECK trong migration economy-ledger và wallet.entity; ledger.service cho riêng Reversal/Adjustment đẩy balance âm, vẫn chặn chi tiêu thường. Chốt gift 2 chân, thêm actor_user_id, bổ sung entity LedgerAccount vào docs.
- evidence: commit 57f04de

### P019: Chữ ký DisabledIapVerifier không khớp port

status: fixed; tier: A; date: 2026-07-18; confidence: high

- problem: Full gate phát hiện signature DisabledIapVerifier không khớp abstract port.
- root cause: Implementation của DisabledIapVerifier lệch số tham số so với port IapVerifier.
- resolution: Sửa implementation giữ nguyên ba tham số; full core gate và integration DB PASS lại.
- evidence: doc docs/plans/2026-07-18-zero-cost-production-review.md

### P066: Hiểu sai luật cổng quà (follower/ngày vs người bắt chuyện lần đầu)

status: fixed; tier: B; date: 2026-08-19; confidence: medium

- problem: Ngưỡng tặng quà ban đầu tính theo số follower trong ngày, sai ý user (phải tính số người bắt chuyện lần đầu: 10 đầu miễn phí, từ người 11 cần quà).
- root cause: Hiểu sai yêu cầu nghiệp vụ; thiết kế profile_follows làm bộ đếm thay vì bảng first-contact.
- resolution: Đổi sang bảng ProfileChatContact (migration 1757600000000), mỗi cặp ghi một lần theo ngày UTC; follow không tăng bộ đếm; gift unlock ghi contact trong transaction Economy; sửa race conversation sẵn có. Matching gate 98 suites/968 tests PASS.
- evidence: codex-thread 01a01908, file apps/core-api/src/database/migrations/1757600000000-profile-chat-contact.ts
