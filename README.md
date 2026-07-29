# skills

Personal agent skills, maintained here and symlinked into the local skill directories.

## Skills

| Skill | Description |
| --- | --- |
| [artifacts-publish](artifacts-publish/SKILL.md) | Publish generated HTML reports to [artifacts.duncan.land](https://artifacts.duncan.land) for shareable links. |
| [fable-orchestrator](fable-orchestrator/SKILL.md) | Take an idea from interview to shipped PR inside herdr: spec, plan, implement, review, ship. |

## Layout

Each skill is a folder with a `SKILL.md` (frontmatter: `name`, `description`) and optional supporting files.

## Install

Symlink a skill into the loader directory:

```sh
ln -s ~/dev/skills/<skill> ~/.agents/skills/<skill>
```
