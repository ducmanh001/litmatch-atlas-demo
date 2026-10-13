# REST API of economy

id: api:economy
module: module:economy
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/economy.md

### GET /economy/wallet

Đọc ví của chính user: balance, earnings, tier VIP đã derive hết hạn khi đọc; chưa có ví thì trả 0.

- handler: EconomyController.getWallet (apps/core-api/src/modules/economy/economy.controller.ts:55); operationId: EconomyController_getWallet
- auth: Bearer JWT
- response 200 WalletDto: balance:string, earnings:string, vipTier:enum(vip|svip), vipExpiresAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /economy/iap/products

Catalog gói diamond IAP đang bán (active=true) sắp theo diamonds tăng dần để client chọn productId trước khi verify.

- handler: EconomyController.listIapProducts (apps/core-api/src/modules/economy/economy.controller.ts:62); operationId: EconomyController_listIapProducts
- auth: Bearer JWT
- response 200 array<IapProductDto>: [].productId:string, [].provider:enum(apple|google), [].diamonds:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /economy/payos/packages

Catalog gói nạp payOS (packageId, amountVnd, diamonds) đang bán; client chỉ gửi packageId, giá luôn do server đọc.

- handler: EconomyController.listPayosPackages (apps/core-api/src/modules/economy/economy.controller.ts:69); operationId: EconomyController_listPayosPackages
- auth: Bearer JWT
- response 200 array<PayosPackageDto>: [].packageId:string, [].amountVnd:string, [].diamonds:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /economy/payos/orders

Tạo đơn nạp payOS idempotent (header Idempotency-Key, @Throttle 10/phút, guest bị 403): snapshot giá/diamond vào payos_payment_orders rồi gọi payOS lấy checkoutUrl/QR.

- handler: EconomyController.createPayosOrder (apps/core-api/src/modules/economy/economy.controller.ts:76); operationId: EconomyController_createPayosOrder
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(1) } }; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- body CreatePayosOrderDto: packageId*:string
- response 200 PayosOrderDto: orderId:string, orderCode:string, amountVnd:string, diamonds:string, status:enum(pending|paid|cancelled|expired), checkoutUrl:string(uri), qrCode:string, expiresAt:string(date-time), replayed:boolean
- FE callers: none in this repo (mobile/third party or dead)

### GET /economy/payos/orders/{orderId}

Trạng thái đơn payOS của chính user, nguồn sự thật phía server (returnUrl của browser không phải bằng chứng); đơn pending quá hạn bị chuyển expired khi đọc.

- handler: EconomyController.getPayosOrderStatus (apps/core-api/src/modules/economy/economy.controller.ts:98); operationId: EconomyController_getPayosOrderStatus
- auth: Bearer JWT
- params: path:orderId*:string
- response 200 PayosOrderStatusDto: orderId:string, status:enum(pending|paid|cancelled|expired), transactionId:string(uuid), diamonds:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /economy/vip/plans

Catalog gói VIP đang bán (planId, tier, số ngày, giá diamond) sắp theo giá tăng dần; client không hardcode bảng giá.

- handler: EconomyController.listVipPlans (apps/core-api/src/modules/economy/economy.controller.ts:111); operationId: EconomyController_listVipPlans
- auth: Bearer JWT
- response 200 array<VipPlanDto>: [].id:string, [].tier:enum(vip|svip), [].days:number, [].priceDiamond:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /economy/iap/verify

Verify receipt IAP Apple/Google ở server rồi credit diamond; idempotency tự sinh iap:{provider}:{providerTransactionId}, @Throttle 10/phút, lưu iap_receipts cùng transaction.

- handler: EconomyController.verifyIap (apps/core-api/src/modules/economy/economy.controller.ts:118); operationId: EconomyController_verifyIap
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(1) } }
- body VerifyIapDto: provider*:enum(apple|google), productId*:string, payload*:object
- response 200: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /economy/vip/purchase

Mua VIP bằng diamond (header Idempotency-Key): Nợ user_wallet / Có system_revenue và cập nhật vip_tier/vip_expires_at cộng dồn trong cùng DB transaction, kèm outbox economy.vip.purchased.

- handler: EconomyController.purchaseVip (apps/core-api/src/modules/economy/economy.controller.ts:134); operationId: EconomyController_purchaseVip
- auth: Bearer JWT; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- body PurchaseVipDto: planId*:string
- response 200 VipPurchaseResultDto: transactionId:string, tier:enum(vip|svip), vipExpiresAt:string(date-time), replayed:boolean
- FE callers: none in this repo (mobile/third party or dead)

### GET /economy/transactions

Lịch sử giao dịch của chính user, cursor pagination (chưa gồm giao dịch chỉ là người nhận).

- handler: EconomyController.listTransactions (apps/core-api/src/modules/economy/economy.controller.ts:153); operationId: EconomyController_listTransactions
- auth: Bearer JWT
- params: query:limit:number, query:cursor:string
- response 200: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /economy/webhooks/apple

Webhook App Store Server Notifications V2: verify JWS x5c lên Apple Root CA, đối chiếu bundleId, nếu REFUND/REVOKE thì reverse giao dịch credit; luôn ACK 200 sau khi verify.

- handler: EconomyWebhooksController.appleNotification (apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:56); operationId: EconomyWebhooksController_appleNotification (NOT in OpenAPI)
- auth: PUBLIC (webhook signature); throttle: { default: { limit: 60, ttl: minutes(1) } }

### POST /economy/webhooks/google/rtdn

Webhook Google RTDN (Pub/Sub push): verify OIDC bearer token rồi chỉ log CANCELED làm tín hiệu phụ, KHÔNG refund (refund Google do job Voided Purchases đảm nhiệm).

- handler: EconomyWebhooksController.googleRtdn (apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:93); operationId: EconomyWebhooksController_googleRtdn (NOT in OpenAPI)
- auth: PUBLIC (webhook signature); throttle: { default: { limit: 60, ttl: minutes(1) } }

### POST /economy/webhooks/payos

Webhook payOS: kiểm HMAC-SHA256 trước mọi query, đối chiếu orderCode/amount/currency/paymentLinkId với snapshot đơn rồi credit diamond và đánh dấu đơn paid trong một transaction.

- handler: EconomyWebhooksController.payos (apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:121); operationId: EconomyWebhooksController_payos (NOT in OpenAPI)
- auth: PUBLIC (webhook signature); throttle: { default: { limit: 120, ttl: minutes(1) } }

