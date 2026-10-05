# qimsdk-agentic-skills

Agentic skills for QIM SDK development workflows.

This repository packages QIM SDK-focused skills for coding agents. The repo keeps
runtime skill payloads, sample prompts, and skill metadata in separate top-level
areas.

## Purpose

Use this repo to maintain skills for:

- QIM SDK GStreamer application generation.
- QIM SDK C++ application generation.
- QIM SDK Python application generation.
- QIM SDK deployment workflows.

The skill payloads live directly under `skills/`.

## Directory Structure

```text
.
├── AGENTS.md
├── README.md
├── plugin.json                       # Agent Plugins v1 manifest for Codex-compatible clients
├── .claude-plugin/plugin.json        # Claude Code plugin manifest
├── .codex-plugin/plugin.json         # Codex compatibility manifest
├── skills/
│   ├── qimsdk-gstreamer-app-builder/
│   ├── qimsdk-python-app-builder/
│   ├── qimsdk-cpp-app-builder/
│   └── qimsdk-deploy/
├── skills-metadata/
└── sample-prompts/
    ├── qimsdk-gstreamer-app-builder/
    ├── qimsdk-cpp-app-builder/
    └── qimsdk-python-app-builder/
```

## Skill Layout

Each implemented skill should have:

```text
skills/<skill-name>/
├── SKILL.md
└── references/        # optional runtime references
```

Skill names should match their directory names. For example:

```yaml
---
name: qimsdk-gstreamer-app-builder
description: ...
---
```

Current skills:

- `qimsdk-gstreamer-app-builder`
- `qimsdk-cpp-app-builder`
- `qimsdk-python-app-builder`
- `qimsdk-deploy`

## Skills Metadata

Skill metadata lives under `skills-metadata/`.

This directory contains metadata files for describing skill identity,
classification, required inputs, known gaps, and source references. It is
separate from runtime skill payloads under `skills/`.

Skill authors adding or changing skills should create or update the matching
metadata file in `skills-metadata/`.

## Sample Prompts

Sample prompts live under `sample-prompts/`.

The currently populated prompt sets are:

- `qimsdk-gstreamer-app-builder/`: `gst-launch` and C app prompts.
- `qimsdk-cpp-app-builder/`: C++ app-builder prompts.
- `qimsdk-python-app-builder/`: Python app-builder prompts.

## Plugin Packaging

This repository can be loaded as one plugin by both Claude Code and Codex-compatible
Agent Plugins clients. Both packages reuse the existing `skills/` directory; skill
payloads are not copied or moved.

- Claude Code uses `.claude-plugin/plugin.json` and discovers skills from `skills/`.
- Codex-compatible clients use the portable Agent Plugins v1 `plugin.json`.
- Codex also supports `.codex-plugin/plugin.json` as its compatibility manifest.

Keep `name`, `version`, and `description` synchronized across the three manifests.
The portable root manifest must keep the Agent Plugins v1 closed schema and must not
include client-specific fields such as `skills` or `hooks`.

### Validate locally

```bash
claude plugin validate . --strict
python - <<'PY'
import json
from pathlib import Path

allowed = {
    "$schema", "name", "version", "description", "author", "homepage",
    "repository", "license", "keywords", "extensions",
}
root = json.loads(Path("plugin.json").read_text())
claude = json.loads(Path(".claude-plugin/plugin.json").read_text())
codex = json.loads(Path(".codex-plugin/plugin.json").read_text())
extra = set(root) - allowed
if extra:
    raise SystemExit(f"root plugin.json has unsupported fields: {sorted(extra)}")
if root.get("$schema") != "https://agent-plugins.org/schemas/1.0.0/plugin.schema.json":
    raise SystemExit("root plugin.json has the wrong Agent Plugins schema")
if len({root.get("name"), claude.get("name"), codex.get("name")}) != 1:
    raise SystemExit("plugin names are inconsistent")
if len({root.get("version"), claude.get("version"), codex.get("version")}) != 1:
    raise SystemExit("plugin versions are inconsistent")
if len({root.get("description"), claude.get("description"), codex.get("description")}) != 1:
    raise SystemExit("plugin descriptions are inconsistent")
print("Plugin manifests are valid and consistent")
PY
```

For Codex-specific validation, use the Codex CLI or a validator that supports the
published [Agent Plugins v1 schema](https://agent-plugins.org/schemas/1.0.0/plugin.schema.json).

## Resources

- To learn more about QIM SDK, visit the [official documentation](https://imsdkdocs.qualcomm.com).
- [QIM SDK Coding Agent](https://imsdkdocs.qualcomm.com/app-builder-coding-agent)

## Contributing

Read `AGENTS.md` before making structural changes. It defines the repository
layout and rules for adding skills, prompts, and metadata.

## License

qimsdk-agentic-skills is licensed under the BSD-3-clause License. See
`LICENSE.txt` for the full license text.
