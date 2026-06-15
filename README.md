# Reconstruct Designs Skill

Codex skill for reconstructing durable design knowledge from existing code into OpenSpec design documents.

The skill inspects a bounded code scope, infers architectural knowledge, and creates or updates:

```text
openspec/specs/[domain]/design.md
```

It is intended for projects that use OpenSpec and want future agents to preserve discovered architecture, conventions, flows, dependencies, and design debt.

## Installation

From a project where Codex skills live under `.codex/skills`, clone or copy this repository as:

```bash
mkdir -p .codex/skills
git clone git@github.com:<your-user-or-org>/reconstruct-design-skill.git .codex/skills/reconstruct-designs
```

If you are installing from a local checkout:

```bash
mkdir -p .codex/skills
cp -R reconstruct-design-skill .codex/skills/reconstruct-designs
```

## Usage

Ask Codex to use the `reconstruct-designs` skill with a bounded code scope, for example:

```text
Use reconstruct-designs on src/auth and update the relevant OpenSpec design docs.
```

The skill defaults to bounded inspection and asks before expanding scope.

## License

MIT
