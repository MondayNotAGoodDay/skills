# skills

Personal agent skills for the open [skills.sh](https://skills.sh) ecosystem.

## Install

```bash
# all skills in this repo
npx skills add MondayNotAGoodDay/skills

# one skill
npx skills add MondayNotAGoodDay/skills@eli5
npx skills add MondayNotAGoodDay/skills@show-me
```

List without installing:

```bash
npx skills add MondayNotAGoodDay/skills --list
```

Browse on skills.sh: https://skills.sh/MondayNotAGoodDay/skills

## Skills

| Skill | When to use |
| --- | --- |
| [eli5](skills/eli5/SKILL.md) | Dead-simple picture explainer (`/eli5 <topic>`) |
| [show-me](skills/show-me/SKILL.md) | Visual diagrams / code-shape sketches for the current topic |

## Layout

```text
skills/
  <skill-name>/
    SKILL.md
```

Each `SKILL.md` has YAML frontmatter with `name` and `description` (required by the skills CLI).

## License

MIT. `eli5` adapted from [anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community); `show-me` from [humanlayer/skills](https://github.com/humanlayer/skills).
