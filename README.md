# Skills

Reusable skills for [Claude Code](https://claude.com/claude-code) and other agents that load `SKILL.md` files.

A skill is a folder holding a `SKILL.md` and any files it needs. The agent reads the description in the frontmatter to decide when to use the skill, then follows its instructions.

| Skill | What it does |
|---|---|
| [`experiment-report`](experiment-report) | Turns ML experiment results into one interactive, self-contained HTML report |
| [`clip`](clip) | Copies text from the agent to your local clipboard over SSH and tmux, using OSC 52 |

## Install

Copy a skill folder into your personal skills directory:

```bash
git clone https://github.com/Y-T-G/skills.git
cp -r skills/experiment-report ~/.claude/skills/
```

To share a skill with everyone on a project, put it in the project's `.claude/skills/` instead.

Claude Code picks the skill up on the next session. It is used when a request matches its description, or when you ask for it by name.

---

## experiment-report

A report for experiments, benchmarks, ablations and model comparisons. It is built to be skimmed: less text, more charts, large type and generous whitespace.

**What you get**

- **One HTML file.** No build step, no chart library, no network requests. Charts are inline SVG drawn by about 200 lines of plain JavaScript. It works offline and can be emailed, attached or hosted anywhere.
- **Interactive.** Every chart has hover tooltips. Toggles switch the metric, the data group or the stage, and line charts have a crosshair.
- **Easy to read.** 20px body text, 96px hero numbers and 112px between sections. Each section has a question as its heading and a one-line takeaway.
- **Light and dark.** It follows the system theme, and a button flips it by hand. All colors are CSS tokens.
- **Works on phones.** The layout reflows down to phone width with no horizontal scroll.
- **Ends with the answer.** The last section is a waterfall showing how much each component added to the final metric.

**Building blocks in `template.html`**

| Block | Use it for |
|---|---|
| Hero | Baseline to best result, in one metric |
| Stat tiles | Three or four headline numbers |
| Ranked bars with toggles | Leaderboards, costs, ablation variants |
| Dot plot | Repeated runs per variant, with the mean, to show run-to-run noise |
| Line chart with crosshair | Parameter sweeps |
| Rejected cards with a filter | What was tried and why it lost |
| Pipeline and waterfall | The final method, and the gain from each step |

The skill also describes an image viewer for detection work. It shows ground truth and prediction boxes over embedded images, with an errors view for missed objects and false boxes.

**How the agent uses it**

1. It writes a script that dumps every number into JSON, so the report can be rebuilt when the data changes.
2. It plans one section per question and always ends with the waterfall.
3. It copies `template.html`, replaces the single `DATA` block and the `{{placeholders}}`, and deletes unused sections.
4. It checks the page: a syntax check, a headless render where every chart must draw and every toggle must work, and a screenshot when a browser is available.

**Use the template by hand**

Open `experiment-report/template.html`, edit the `DATA` object at the top of the script, and open the file in a browser. Everything else is driven by that object.

```js
const DATA = {
  lb:   [["Winner", { G1: [0.83, 0.57], G2: [0.78, 0.43] }, "$0.084", true], ...],
  dots: [["Variant A", "var(--s1)", [0.685, 0.658, 0.699], "3 runs"], ...],
  wf:   { steps: ["Baseline", "+ Step A", "+ Step B"], vals: [[0.53, 0.66, 0.73], ...] },
  ...
};
```

To add a chart, reuse one of the draw helpers (`hbar`, `drawDots`, `drawLine`, `drawWF`) and call it from `drawAll()`. To add a toggle:

1. Add a key to `state`.
2. Add a `.seg` button group with a matching `data-ctl`.

## clip

Copies text from the agent to the clipboard of the device you are typing on, even when the agent runs on a remote machine over SSH and inside tmux. It sends an OSC 52 escape sequence, which the local terminal turns into a clipboard write.

| Usage | What gets copied |
|---|---|
| `/clip` | The main text of the last reply, such as a drafted message, a command or a code block |
| `/clip <what>` | What you name, for example `the reply`, `the second command` or a file path |

**Requirements**

- A terminal that supports OSC 52, such as Termux, iTerm2, WezTerm, kitty, Windows Terminal or Alacritty.
- Inside tmux, tmux 3.2 or newer with `set-clipboard` set to `external` (the default) or `on`. The text also stays in tmux's paste buffer, so `Ctrl-b ]` pastes it.

## License

MIT
