# html-doc

A Claude Code skill that makes Claude write documents meant to be read or shared (specs, plans, design docs, reviews, reports, explainers) as a single self-contained HTML file instead of Markdown. These files can include inline SVG diagrams, real tables, annotated code, light and dark themes, print styles, and optional interactivity.

See [`skills/html-doc/SKILL.md`](skills/html-doc/SKILL.md) for the full instructions.

## Install

### As a plugin (recommended, updates easily)

In Claude Code:

```
/plugin marketplace add benjaminholderbein/html-docs-skill
/plugin install html-doc@html-docs-skill
```

To update later, run `/plugin marketplace update html-docs-skill`.

### As a plain skill

```bash
git clone https://github.com/benjaminholderbein/html-docs-skill.git
mkdir -p ~/.claude/skills
ln -s "$PWD/html-docs-skill/skills/html-doc" ~/.claude/skills/html-doc
```

Run `git pull` in the clone to update.

## License

MIT
