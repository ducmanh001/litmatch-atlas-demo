# Domain rules, error handling, glossary, conventions

id: system:domain

Mọi lỗi đi qua GlobalExceptionFilter: DomainException (4xx) giữ nguyên mã, HttpException map sang COMMON_*, phần còn lại thành 500 COMMON_INTERNAL_ERROR; thành công bọc { data, meta? }. Tệp gồm quy ước lỗi, 30 luật nghiệp vụ nhạy cảm đã đối chiếu mã, 35 thuật ngữ sản phẩm sang mã và quy ước đặt tên, bố cục.

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

## Files

- [limits](limits.md): 8 items
- [docDrift](docDrift.md): 4 items
- [errorHandling](errorHandling.md): 9 fields
- [unusedErrorCodes](unusedErrorCodes.md): 9 items
- [businessRules](businessRules.md): 30 items
- [glossary](glossary.md): 35 items
- [conventions](conventions.md): 25 items
