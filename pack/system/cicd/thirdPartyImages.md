# CI/CD: pipelines, quality gates, release, images: thirdPartyImages

id: system:cicd:thirdPartyImages

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- image: postgres:16-alpine
- usedIn: ci.yml service container (tag, không digest); docker-compose.yml dev (tag); deploy/production/compose.yml (ghim digest)
- evidence (evidence): .github/workflows/ci.yml:58, docker-compose.yml:6, deploy/production/compose.yml:5

### #2

- image: redis:7-alpine
- usedIn: ci.yml service container (tag); docker-compose.yml dev (tag); deploy/production/compose.yml (ghim digest)
- evidence (evidence): .github/workflows/ci.yml:71, docker-compose.yml:24, deploy/production/compose.yml:24

### #3

- image: apache/kafka:3.9.0
- usedIn: Chỉ docker-compose.yml dev (KRaft một node, tag không digest); không có trong CI, production compose hay hosted-free
- evidence (evidence): docker-compose.yml:37

### #4

- image: livekit/livekit-server:v1.13.1
- usedIn: Ghim digest ở docker-compose.dev.yml, deploy/production/compose.yml, apps/media-server/docker-compose.yml và k8s/base/media-server/deployment.yaml
- evidence (evidence): docker-compose.dev.yml:52, deploy/production/compose.yml:37, k8s/base/media-server/deployment.yaml:71, apps/media-server/docker-compose.yml:6

### #5

- image: grafana/alloy:v1.17.1
- usedIn: Profile `monitoring` của deploy/production/compose.yml và docker-compose.observability.yml (ghim digest)
- evidence (evidence): deploy/production/compose.yml:180, deploy/production/compose.yml:179

### #6

- image: grafana/k6:1.7.0
- usedIn: staging-reliability.yml, chạy qua `docker run` (tag, không digest)
- evidence (evidence): .github/workflows/staging-reliability.yml:89

### #7

- image: node:22-bookworm
- usedIn: Container sạch của `pnpm ci:local:clean`/`ci:preflight` (ghim digest, đổi bằng LOCAL_CI_NODE_IMAGE)
- evidence (evidence): scripts/ci/local.mjs:27

### #8

- image: mcr.microsoft.com/devcontainers/typescript-node:1-22-bookworm
- usedIn: .devcontainer/devcontainer.json (tag, không digest)
- evidence (evidence): .devcontainer/devcontainer.json:3

### #9

- image: ghcr.io/litmatch/core-api:latest và ghcr.io/litmatch/signaling-gateway:latest
- usedIn: k8s/base/*/deployment.yaml; overlay đặt `newTag: latest` kèm ghi chú "CI ghi đè bằng git SHA", nhưng không workflow hay script nào build/push các image này
- evidence (evidence): k8s/base/core-api/deployment.yaml:42, k8s/overlays/production/kustomization.yaml:13
