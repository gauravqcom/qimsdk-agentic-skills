# QIM SDK Agentic Skills Contributor Rules

This repository contains portable skill bundles for coding agents.

## Current Layout

- `skills/`: skill payloads.
- `skills-metadata/`: skill metadata records.
- `sample-prompts/`: sample prompts for skills.
- `.claude-plugin/plugin.json`: Claude Code plugin manifest.
- `plugin.json`: portable Agent Plugins v1 manifest used by Codex-compatible clients.
- `.codex-plugin/plugin.json`: Codex compatibility manifest.

The manifests package the existing `skills/` directory; do not duplicate or move
skill payloads for either client.

## Guidance File Rules

- Keep `AGENTS.md` only at area or ownership boundaries.
- Do not add `AGENTS.md` inside individual skill directories, skill reference
  directories, individual sample-prompt directories, or per-skill metadata
  files.
- Allowed guidance locations are the repo root, `skills/`, and
  `skills-metadata/`.

## Layout Rules

- Skill payloads live directly under `skills/<skill-name>/`.
- Keep sample prompts under `sample-prompts/<skill-name>/`.
- Keep skill metadata under `skills-metadata/<skill-name>.md`.
- Keep `.claude-plugin/plugin.json`, `plugin.json`, and
  `.codex-plugin/plugin.json` at the repository root.
- Keep the shared plugin identity (`name`, `version`, and `description`) in
  sync across all manifests.
- The portable root `plugin.json` must remain conformant with the closed Agent
  Plugins v1 schema; client-specific data belongs under `extensions`.
- If future Codex-specific hooks or apps are added, document them in the
  `.codex-plugin/plugin.json` compatibility manifest and verify their interaction
  with the portable root manifest before shipping.

## Plugin Validation

- Validate the Claude Code package with `claude plugin validate . --strict`.
- Validate the root `plugin.json` against the Agent Plugins v1 schema and check
  cross-manifest identity before publishing.
- Run the existing skill-specific verification scripts and tests when changing
  skill payloads; manifest-only changes must not move or rewrite those payloads.

## Skill Rules

- Every complete skill directory under `skills/` must contain `SKILL.md`.
- Keep skill names aligned with their directory names.
- Put runtime references needed by a skill inside that skill directory.
- Do not perform broad IMSDK/QIMSDK wording rewrites inside actual skill payloads
  unless explicitly requested. Skill payloads include `skills/<skill>/SKILL.md`
  and any files under that skill's `references/` or `reference/` directories.
- Preserve API names, package names, repository names, paths, headers, library
  targets, and command examples inside skill payloads exactly unless the task is
  specifically to update those technical references.
- Skill authors adding or changing skills under `skills/` should create or
  update the matching metadata file under `skills-metadata/`.
