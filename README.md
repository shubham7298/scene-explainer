# scene-explainer

[![skills.sh](https://www.skills.sh/b/shubham7298/scene-explainer)](https://www.skills.sh/shubham7298/scene-explainer/scene-explainer)

An agent skill for building animated explainer pages. Each page teaches one code change, pull request, bug fix or algorithm, step by step, in the style of a 3Blue1Brown video.

The output is one self-contained HTML page. The reader steps through it with Next. Each step shows one idea as a picture with a short caption. Code appears beside the picture only after the idea has landed, and every code line on the page is pulled from git, never typed by hand.

## Install

```bash
npx skills add shubham7298/scene-explainer
```

Or copy `skills/scene-explainer/` into your agent's skills folder (for Claude Code, `~/.claude/skills/`).

## Use

Ask your agent for an explainer, for example:

- "Make an animated explainer for commits `abc123..def456`."
- "Walk me through this bug fix visually, for someone new to the code."
- "Build a step-through page that explains how this algorithm works."

The skill guides the agent through these steps:

1. Pin the scope.
2. Collect real values.
3. Storyboard the chapters.
4. Fill the template.
5. Build the page from git.
6. Check every step in a real browser.
7. Report what the page covers and what it leaves out.

## What a finished page has

- 15 to 25 steps in 4 to 7 chapters: the puzzle, why it happened, the idea, try it, the code, the result, did it work.
- An interactive playground that runs a port of the real logic, including look-alike cases it must refuse.
- One colour per concept, the same in the picture, the legend and the code highlights.
- No external resources. It works at 390 px wide, and every step renders correctly when opened directly by `#n`.

## Contents

| Path | What it is |
|---|---|
| `skills/scene-explainer/SKILL.md` | The skill: when to use it, the workflow and common mistakes |
| `skills/scene-explainer/references/storyboard.md` | What goes in which step: chapters, captions, pictures, the playground |
| `skills/scene-explainer/references/gotchas.md` | Mistakes that cost a rebuild (SVG, layout, motion, checking) |
| `skills/scene-explainer/assets/template.html` | Page template: styles, the step engine and three example scenes |
| `skills/scene-explainer/scripts/build.py` | Pulls code snippets from git into the page, flags added lines, and fails on missing anchors, unknown snippets, JS syntax errors or external resources |
| `skills/scene-explainer/scripts/check.sh` | Steps through the page in a real browser: screenshots at desktop and phone width, page errors, sideways overflow |
| `skills/scene-explainer/scripts/sheets.py` | Tiles the screenshots into contact sheets |

## Requirements

| Tool | Used by |
|---|---|
| git | `build.py` (reads the code at two commits) |
| Python 3 | `build.py`, and `check.sh` through `sheets.py` |
| [Pillow](https://pypi.org/project/pillow/) (`pip install pillow`) | `check.sh` through `sheets.py` |
| Node.js (optional) | `build.py` checks the page's JavaScript syntax when `node` is on the PATH |
| [agent-browser](https://github.com/vercel-labs/agent-browser) | `check.sh` |

## Building a page by hand

`explainer.json` sits next to your copy of the template:

```json
{
  "repo": "/path/to/your/repo",
  "base": "<commit before the change>",
  "head": "<commit with the change>",
  "template": "template.html",
  "out": "cache-expiry-explainer.html",
  "vars": {"TITLE": "Cache Expiry Explainer", "BRAND": "Cache expiry fix"},
  "snippets": {
    "loop": {
      "file": "src/Cache.java",
      "start": {"find": "for (Entry e : entries)", "offset": -1},
      "end": {"lines": 5},
      "marks": [["isExpired(e, now)", "new"]]
    }
  }
}
```

```bash
python3 skills/scene-explainer/scripts/build.py explainer.json
bash skills/scene-explainer/scripts/check.sh cache-expiry-explainer.html shots/
```

Then open `shots/sheet_*.png` and look at every step.

## Credits

The visual style is inspired by 3Blue1Brown's videos. The template's blue, teal, green and red are the [manim](https://www.manim.community/) colours BLUE_C, TEAL_C, GREEN_C and RED_C. This project is not affiliated with either.

## License

[MIT](LICENSE)
