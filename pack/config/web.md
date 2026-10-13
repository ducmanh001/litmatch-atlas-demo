# Environment variables WEB_*

id: config:WEB
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| WEB_PUSH_ENABLED | core-api | switch | boolean | false |  | .env.example:334, deploy/hosted/core-api.env.example:53, k8s/base/core-api/configmap.yaml:172 | Browser Web Push — tắt mặc định; khi bật production phải có đủ VAPID credentials. |
| WEB_PUSH_SUBJECT | core-api | other | string |  |  | .env.example:335, deploy/hosted/core-api.env.example:54 |  |
| WEB_PUSH_PUBLIC_KEY | core-api | secret | string |  |  | .env.example:336, deploy/hosted/core-api.env.example:55 |  |
| WEB_PUSH_PRIVATE_KEY | core-api | secret | string |  |  | .env.example:337, deploy/hosted/core-api.env.example:56 |  |
