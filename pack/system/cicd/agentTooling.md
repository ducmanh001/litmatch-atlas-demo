# CI/CD: pipelines, quality gates, release, images: agentTooling

id: system:cicd:agentTooling

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- summary: Repo giữ một nguồn hướng dẫn canonical cho mọi công cụ agent và kiểm bằng máy việc các adapter không trôi khỏi nó; guard chặn vi phạm invariant cả lúc agent sửa file lẫn lúc commit.

### items

### AGENTS.md là hợp đồng canonical (3 invariant + quy trình + budget thực thi); CLAUDE.md chỉ là liên …

- where: AGENTS.md, CLAUDE.md
- evidence (evidence): AGENTS.md:1, CLAUDE.md:1

### Context map 12 scope (default, docs, agents, core, economy, matching, calling, signaling, content, …

- where: .agents/context-map.json; pnpm agent:context <scope>
- evidence (evidence): .agents/context-map.json:3, scripts/agent/repository-check.mjs:74

### Skill canonical: adaptive-orchestration (chọn model/effort/số sub-agent), new-module (scaffold modu…

- where: .agents/skills/*; adapter ở .claude/skills, .opencode/skills, .github/skills
- evidence (evidence): scripts/agent/repository-check.mjs:269, .agents/skills/review-module/SKILL.md:2, .agents/skills/adaptive-orchestration/SKILL.md:2, .agents/skills/new-module/SKILL.md:2, scripts/agent/adapter-parity.mjs:5

### Hook runtime của agent: SessionStart in hướng dẫn định vị; PreToolUse (Write|Edit|Bash) chạy guard-…

- where: .claude/settings.json; scripts/agent/session-start.mjs, pre-tool-guard.mjs
- evidence (evidence): .claude/settings.json:4, .claude/settings.json:14, scripts/agent/pre-tool-guard.mjs:59

### MCP của Nx cho agent (Codex và OpenCode) qua `npx nx mcp`; cấu hình agent runtime còn bật plugin Nx…

- where: .codex/config.toml, opencode.json, .claude/settings.json
- evidence (evidence): .codex/config.toml:1, opencode.json:3, .claude/settings.json:35

### Eval tầng review: 19 fixture lỗi nghiệp vụ thật (economy 10, matching 5, frontend 4) chỉ được kiểm …

- where: scripts/agent/golden-bugs/*.json, golden-bugs.mjs, golden-bugs-eval-prep.mjs
- evidence (evidence): scripts/agent/golden-bugs.mjs:10, scripts/agent/golden-bugs.mjs:20

### Giám sát CI cho agent: prompt/skill/subagent `monitor-ci` gọi công cụ Nx Cloud; cần workspace kết n…

- where: .github/prompts/monitor-ci.prompt.md, .github/agents/ci-monitor-subagent.agent.md
- evidence (evidence): .github/agents/ci-monitor-subagent.agent.md:5, .github/prompts/monitor-ci.prompt.md:43

### Quy tắc làm việc của agent trong repo: đọc có budget, không bọc cả gate bằng timeout 45 giây, chỉ k…

- where: AGENTS.md § Budget thực thi
- evidence (evidence): AGENTS.md:36, AGENTS.md:49

### evidence

### #1

- file: AGENTS.md
- line: 18
- quote: ## Quy trình

### #2

- file: scripts/agent/guard-core.mjs
- line: 1
- quote: export const ALLOWED_APPS = new Set([
