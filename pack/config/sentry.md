# Environment variables SENTRY_*

id: config:SENTRY
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| SENTRY_DSN | core-api | secret | string |  |  | .env.example:129, deploy/hosted/core-api.env.example:17, deploy/hosted/signaling-gateway.env.example:9, deploy/production/compose.yml:75, deploy/production/compose.yml:131, k8s/base/core-api/configmap.yaml:51, k8s/base/signaling-gateway/configmap.yaml:19 |  |
| SENTRY_RELEASE | core-api | other | string |  |  | .env.example:130, deploy/production/.env.example:77, deploy/hosted/core-api.env.example:18, deploy/hosted/signaling-gateway.env.example:10, deploy/production/compose.yml:76, deploy/production/compose.yml:132, k8s/base/core-api/configmap.yaml:52, k8s/base/signaling-gateway/configmap.yaml:20 |  |
| SENTRY_DSN | signaling-gateway | secret | string |  |  | .env.example:129, deploy/hosted/core-api.env.example:17, deploy/hosted/signaling-gateway.env.example:9, deploy/production/compose.yml:75, deploy/production/compose.yml:131, k8s/base/core-api/configmap.yaml:51, k8s/base/signaling-gateway/configmap.yaml:19 |  |
| SENTRY_RELEASE | signaling-gateway | other | string |  |  | .env.example:130, deploy/production/.env.example:77, deploy/hosted/core-api.env.example:18, deploy/hosted/signaling-gateway.env.example:10, deploy/production/compose.yml:76, deploy/production/compose.yml:132, k8s/base/core-api/configmap.yaml:52, k8s/base/signaling-gateway/configmap.yaml:20 |  |
