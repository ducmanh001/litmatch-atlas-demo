# CI/CD: pipelines, quality gates, release, images

id: system:cicd

Một runner scripts/ci/local.mjs chạy cùng gate trên máy dev, hook pre-push và GitHub CI (quality → test song song docker → `CI required`). Release hosted-free tự động từ CI push xanh trên main: migration → Northflank → Cloudflare → smoke. Gitleaks/Trivy/CodeQL/SBOM không chạy; K8s và single-node triển khai tay; branch protection không thấy trong repo.

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

## Files

- [limits](limits.md): 10 items
- [docDrift](docDrift.md): 7 items
- [pipelines](pipelines.md): 3 items
- [localPipeline](localPipeline.md): 12 fields
- [qualityGates](qualityGates.md): 48 items
- [blockingDefinition](blockingDefinition.md): text
- [images](images.md): 7 items
- [thirdPartyImages](thirdPartyImages.md): 9 items
- [release](release.md): 11 fields
- [branchPolicy](branchPolicy.md): 10 fields
- [devWorkflow](devWorkflow.md): 11 items
- [commandsCheatSheet](commandsCheatSheet.md): 25 items
- [nxWorkspace](nxWorkspace.md): 12 fields
- [agentTooling](agentTooling.md): 3 fields
- [observations](observations.md): 16 items
