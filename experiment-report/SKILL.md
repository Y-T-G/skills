---
name: experiment-report
description: Build a concise, highly interactive, self-contained HTML report for an ML experiment or benchmark. The report has large fonts, generous whitespace, light and dark themes, and hover tooltips, and it ends with how each component improved the metric. Use when the user asks for a visual report, a results page, or a write-up of experiments, benchmarks, ablations or model comparisons.
---

# Experiment report

This skill makes one self-contained HTML file: no external libraries, all charts are inline SVG drawn by plain JS, and data is embedded. The design aims for less wall of text and more digestible visuals.

Files in this folder:
- `template.html`: the starting point. Copy it next to the experiment, then replace the `DATA` block and the `{{...}}` placeholders.

## Workflow

1. **Gather numbers from scripts, not memory.** Write or reuse a rescore script that dumps every number the report shows into a JSON file, for example `report_data.json`. Build the report from that file, so a label or data change only needs a rerun.
2. **Plan the story, one section per question.** Order:
   1. hero: baseline to best, one metric;
   2. stat tiles;
   3. leaderboard;
   4. cost;
   5. tested and rejected;
   6. diagnosis: what is wrong;
   7. each fix tried;
   8. noise across repeated runs;
   9. example images;
   10. **final section:** pipeline diagram, waterfall of each component's gain, and gain tiles.

   Always end with the waterfall.
3. **Copy `template.html`,** fill `DATA`, and delete the sections you don't need. To add a chart, reuse `hbar`, `drawDots`, `drawLine` or `drawWF`. Add each new toggle to `state`, add its `.seg` with `data-ctl`, and call its draw function from `drawAll()`.
4. **Check it:**
   - `node --check` on the script;
   - render in jsdom (`npm i jsdom` in a temp dir), confirm every SVG has children, and click each `.seg` button with no errors;
   - take a screenshot with Playwright if it can launch.
5. **Report back** the path and the sections. Offer to publish it as a private Artifact only if the user wants a link.

## Design rules

- **Type and spacing:**
  - body text 20px, h1 52px, h2 36px, lead 24px, tile numbers 48px, hero numbers 96px;
  - 112px between sections;
  - max width 1040px, with a 16px side gutter on phones.
- **Wording:**
  - each section has a kicker ("Step 1"), a question as the h2, and a one-line takeaway lead;
  - at most one short note under a chart;
  - no paragraphs.
- **Color:**
  - use tokens only: `--s1` blue for the winner or the focus, `--s2` orange, `--s3` green, `--muted` grey for the rest, `--bad` red for failures;
  - text stays in the ink tokens, never in the series color;
  - light and dark sets are defined under `prefers-color-scheme`, plus `[data-theme]` for the toggle button.
- **Interaction:**
  - every chart has hover tooltips (`bindTip`);
  - line charts get a crosshair;
  - toggles are pill segments (`.seg`), placed in one row above the chart;
  - use toggles for group, metric, stage (raw vs refined) and rejected-reason filters.
- **Numbers:** show 3 decimals for mAP-like scores. Gain tiles must equal the waterfall steps, so compute both from the same rounded values.
- **Honesty:** the footer lists caveats: sample size, settings tuned on the test set, and what was not tested. Show run-to-run noise when you have repeats.
- **Images, if the experiment has them:**
  - embed JPEGs at 1200 px and quality 72 as base64, and keep the file under 16 MB;
  - draw boxes as SVG rects over the image, using `viewBox="0 0 1 1"` and `vector-effect: non-scaling-stroke`;
  - add views for GT only, prediction vs GT, and errors: missed GT dashed red, false boxes solid red, correct boxes grey.
- **Writing style:**
  - plain, simple English;
  - no em dashes;
  - no slash-joined words.
