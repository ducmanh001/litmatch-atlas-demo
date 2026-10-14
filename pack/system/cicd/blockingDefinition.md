# CI/CD: pipelines, quality gates, release, images: blockingDefinition

id: system:cicd:blockingDefinition

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

`blocking: true` nghĩa là gate làm job/hook/script tương ứng thoát khác 0 (trong CI thì làm `CI required` đỏ). Việc `CI required` có chặn merge hay không phụ thuộc branch protection của GitHub, không thấy trong repo; `false` = không có đường tự động nào chạy gate hoặc gate chỉ là quy ước.
