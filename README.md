# Cursor Skills

Reusable [Cursor AI](https://cursor.sh) skills for UX audits and development workflows.

## Skills

| Skill | Description |
|-------|-------------|
| [ux-audit](skills/ux-audit/SKILL.md) | Audit a frontend prototype for UX defects and opportunities across 5 categories: accessibility, interaction smoothness, workflow efficiency, corner case handling, and PatternFly compliance. |

## Usage

1. Clone this repo (or copy individual `SKILL.md` files) into `~/.cursor/skills/`:

```bash
# Clone the whole collection
git clone https://github.com/KendraMar/cursor-skills.git ~/.cursor/skills
```

2. In any Cursor project, the agent will automatically detect and offer matching skills based on your request.

3. You can also trigger a skill directly by asking, e.g. *"Run a UX audit on this project."*

## Adding New Skills

Each skill lives in its own folder under `skills/`:

```
cursor-skills/
  skills/
    ux-audit/
      SKILL.md
    your-new-skill/
      SKILL.md
```

See the [Cursor Skills documentation](https://docs.cursor.com) for authoring guidelines.
