# Executive Summary Generator

A web app that turns a spreadsheet of weekly team reports into a short summary for leadership. Course project for a University of Washington class (579).

## The problem

Leaders receive dozens of weekly reports and read only some of them. Important changes get buried. This tool takes a report tracker, finds what changed, scores what matters most, and produces a five-item summary and a one-slide view.

## What it does

**Input:** upload a CSV, click "Use Demo File" (a 60-row sample report tracker), or paste raw text. The CSV is expected to have these columns: `Report_Date`, `Section`, `Submitted_By`, `Leadership_Viewed`, `Feedback`, `Changed_Since_Last_Week`.

**Output** (four views):

| View | What it shows |
| --- | --- |
| Summary | Up to five items labeled Risk, Win, Blocker, or Task, plus a status line such as "X of Y reports reviewed by leadership" |
| Slide | The top blocker, top risk, and top tasks and wins as a one-slide list |
| Priority | The top 10 report rows ranked by a priority score |
| Signals | Rows sorted into tasks done, blockers, wins, and risks |

## How it works

All processing is **rule-based and runs in your browser**. There is no LLM or machine learning model.

- **Auto-summarization** (`lib/auto-summarizer.ts`): keeps rows where `Changed_Since_Last_Week` is not "No" and writes a sentence for each based on keywords such as "blocker", "risk", "win", or "duplicate".
- **Priority score** (`lib/priority-scorer.ts`): `0.4 x urgency + 0.3 x impact + 0.2 x recency + 0.1 x votes`. Urgency is 5 if the report changed this week and 2 otherwise. Impact comes from a lookup by report section (for example "Product Launches" is 5 and "Marketing campaigns" is 3). Recency falls off over 14 days. Votes counts a 👍 in the feedback column.
- **Report statistics**: counts total, read, unread, and changed reports from the `Leadership_Viewed` and `Changed_Since_Last_Week` columns.
- **Categories** (`lib/report-processor.ts`): items are labeled by keywords and sorted risks first, then wins, blockers, and tasks.

## Limitations

- **The raw text input is a placeholder.** It always adds five hard-coded sample items before checking your lines for keywords, and the status line falls back to a fixed "37/60". Use the CSV input for real results.
- `lib/tone-adjuster.ts` is a stub for a future LLM feature that rewrites text for a leadership audience. It returns fixed sentences and is **not connected to the app**.
- The processing step includes a one-second artificial delay.
- The CSV is split on commas, so quoted commas will break a row. The impact scores, keyword lists, and weights are hand-chosen, not learned from data.
- The demo file loads from Vercel Blob storage, so the "Use Demo File" button needs that file to be available. `analyze-reports.js` is a separate Node script that prints a text summary of the same sample file (`node analyze-reports.js`, Node 18 or newer).

## Tech stack

Next.js 15, React 19, TypeScript, Tailwind CSS, shadcn/ui, Recharts.

## Run it locally

Requires Node.js 18.18 or newer and [pnpm](https://pnpm.io/).

```bash
git clone https://github.com/Eantan2/v0-579-assignment-2.git
cd v0-579-assignment-2
pnpm install
pnpm dev
```

Then open http://localhost:3000.

## How it was built

The user interface was generated with Vercel's v0 AI tool, as the course required, and then deployed on Vercel. I specified the problem, the report fields, and the scoring approach, and reviewed the output. The commit history shows only the v0 sync because the tool commits the changes.
## Deployment

Your project is live at:

**[https://vercel.com/eantan2s-projects/v0-579-assignment-2](https://vercel.com/eantan2s-projects/v0-579-assignment-2)**

## Build your app

Continue building your app on:

**[https://v0.dev/chat/projects/6Iut0Nd4Y5A](https://v0.dev/chat/projects/6Iut0Nd4Y5A)**
