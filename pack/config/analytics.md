# Environment variables ANALYTICS_*

id: config:ANALYTICS
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| ANALYTICS_ENABLED | core-api | switch | boolean | false |  | .env.example:133, deploy/hosted/core-api.env.example:19, deploy/production/compose.yml:77, k8s/base/core-api/configmap.yaml:173 | Server analytics — disabled by default; provider credentials are only required when enabled. |
| ANALYTICS_PROVIDER | core-api | switch | string 'posthog', 'disabled' | disabled |  | .env.example:134, deploy/hosted/core-api.env.example:20, deploy/production/compose.yml:78, k8s/base/core-api/configmap.yaml:174 |  |
| ANALYTICS_HTTP_TIMEOUT_MS | core-api | tuning | number | 5000 |  | .env.example:137, deploy/hosted/core-api.env.example:23, k8s/base/core-api/configmap.yaml:176 |  |
