# Environment variables PAYOS_*

id: config:PAYOS
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| PAYOS_CLIENT_ID | core-api | other | string |  |  | .env.example:182 | payOS web top-up: credential chỉ ở core-api; URL callback chỉ phục vụ UX, không credit. |
| PAYOS_API_KEY | core-api | secret | string |  |  | .env.example:183 |  |
| PAYOS_CHECKSUM_KEY | core-api | secret | string |  |  | .env.example:184 |  |
| PAYOS_API_BASE_URL | core-api | endpoint | string | https://api-merchant.payos.vn |  | .env.example:185 |  |
| PAYOS_HTTP_TIMEOUT_MS | core-api | tuning | number | 10000 |  | .env.example:186 |  |
| PAYOS_ORDER_EXPIRES_SECONDS | core-api | tuning | number | 900 |  | .env.example:187 |  |
| PAYOS_WEB_WALLET_URL | core-api | endpoint | string |  |  | .env.example:189 |  |
| PAYOS_RETURN_URL | core-api | endpoint | string |  |  | .env.example:191 |  |
| PAYOS_CANCEL_URL | core-api | endpoint | string |  |  | .env.example:192 |  |
