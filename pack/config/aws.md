# Environment variables AWS_*

id: config:AWS
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| AWS_REGION | core-api | other | string | us-east-1 |  | .env.example:145 |  |
| AWS_S3_ENDPOINT | core-api | endpoint | string |  |  | .env.example:146 |  |
| AWS_S3_BUCKET | core-api | other | string |  |  | .env.example:147 |  |
| AWS_ACCESS_KEY_ID | core-api | other | string |  |  | .env.example:148 |  |
| AWS_SECRET_ACCESS_KEY | core-api | secret | string |  |  | .env.example:149 |  |
| AWS_S3_FORCE_PATH_STYLE | core-api | other | boolean | false |  | .env.example:150 |  |
