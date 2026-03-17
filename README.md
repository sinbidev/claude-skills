# Claude Skills

A collection of Claude Code skills for developers — covering code workflows, automation, and productivity. Includes skills for NetSuite SDF development.

## Skills

### NetSuite SDF

| Skill | Description |
|---|---|
| [netsuite-sdf-m2m-setup](./netsuite-sdf-m2m-setup/README.md) | Set up NetSuite SDF Machine-to-Machine (M2M) authentication |

## Usage

Each skill is a single `.md` file. To install one, copy it to `~/.claude/commands/` and invoke it with the corresponding slash command in Claude Code.

**Example — install `netsuite-sdf-m2m-setup`:**

```bash
curl -o ~/.claude/commands/netsuite-sdf-m2m-setup.md \
  https://raw.githubusercontent.com/matiaslugli08/claude-skills/main/netsuite-sdf-m2m-setup/netsuite-sdf-m2m-setup.md
```

Then in Claude Code, run `/netsuite-sdf-m2m-setup`.
