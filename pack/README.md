# Litmatch context pack (public)

id: pack:readme

Small markdown files with stable ids so an AI or an engineer can fetch exactly one thing. Static analysis of the public repo ducmanh001/litmatch (branch fix/multi-pod-readiness @ d3cb28c); nothing was observed at runtime.

## Id scheme (use these as join keys across every file)

- module:<name> (e.g. module:economy) -> modules/<name>.md
- db:<module> / table name -> db/<module>.md
- api:<module> and "METHOD /path" -> api/<module>.md
- flow:<id> -> flows/<id>.md
- events: realtime event names like match.confirmed -> events/realtime.md
- errors: DOMAIN_SUBJECT_REASON codes -> errors/<group>.md
- config: ENV_VAR names -> config/<prefix>.md
- issues: P0xx -> issues/<area>.md
- fe: feature names -> fe/ ; slices: feature id -> slices/

## Which file for which task

- Start: modules/index.md, then modules/<name>.md
- Follow a feature end to end: slices/index.md (if present), then flows/index.md
- Change a table: db/<module>.md + modules/<module>.md + recipes.md
- Change an endpoint: api/<module>.md + api/authz.md + api/no-client.md + recipes.md
- Understand why two services talk: service-map/<module>.md
- What broke before: issues/index.md

Code wins over this pack; open the cited file:line before editing.
