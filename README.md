# Build Journal

Turn rough notes and design files from a hardware build session into a journal entry.

You type what you did, drop in the files you worked on, and the page writes the session up in a casual first-person devlog style and keeps it with the rest of your journal.

## What it does

- Reads design files in the browser: KiCad schematic, board and netlist files, STEP, STL, source code, photos and screenshots.
- Compares two versions of a KiCad file when one is marked as the older version, and describes what changed.
- Asks up to three questions first if the notes leave out why something was done or what went wrong.
- Splits a long description of past work into one entry per session ("Catch up on past work").
- Keeps a numbered journal per project, with edit, copy, delete, Markdown export, and backup and restore.

- Writes to a list of rules against common AI writing habits (stock phrases, tidy closing lines, groups of three, em dashes and so on), adapted from [blader/humanizer](https://github.com/blader/humanizer) (MIT). Visitors can also paste a sample of their own writing so entries follow their style.

Fusion `.f3d` / `.f3z` files and PDFs cannot be read. Export a STEP file or add a screenshot instead.

## How it is built

| File | Purpose |
|---|---|
| `index.html` | The whole page: layout, file readers and journal. No build step. |
| `api/write.js` | Serverless function. Builds the prompt, calls the AI model and returns the text. The API key stays here. |
| `vercel.json` | Gives the function up to 60 seconds to answer. |

Entries are stored in each visitor's own browser (`localStorage`). There are no accounts and no database.

## Deploy on Vercel

1. Import this repository in Vercel as a new project. No framework or build settings are needed.
2. In the project's **Settings → Environment Variables**, add one API key (see below).
3. Deploy. If you add or change a variable later, redeploy for it to take effect.
4. Open `https://your-site/api/write` in a browser. It shows whether a key reached the function and which provider and model are in use. It never shows the key.

### Environment variables

Set one of the three keys.

| Variable | Needed | What it is |
|---|---|---|
| `GEMINI_API_KEY` | one of these | Google Gemini API key |
| `ANTHROPIC_API_KEY` | one of these | Anthropic API key |
| `OPENAI_API_KEY` | one of these | OpenAI API key |
| `AI_PROVIDER` | optional | `gemini`, `anthropic` or `openai`, if more than one key is set |
| `AI_MODEL` | optional | Model ID to use instead of the default |
| `AI_FALLBACK_MODEL` | optional | Models tried in order when the main one is overloaded, comma separated. Gemini uses `gemini-3.5-flash-lite, gemini-2.5-flash` by default; set `none` to turn it off |
| `ACCESS_CODE` | optional | If set, visitors must enter this code before they can write entries |

Default models: `gemini-3.6-flash`, `claude-haiku-5-5`, `gpt-5-mini`. Model IDs change over time, so set `AI_MODEL` if a default stops working.

## Running a public copy

Every entry written on your site is paid for by your API key, so limit what it can cost you:

- Set a spending limit or budget alert in your AI provider's dashboard. This is the only hard cap.
- The function limits each visitor to 8 requests per 10 minutes and 40 per day, and caps the size of every request. The counter lives in server memory, so it resets when the function restarts and is a brake, not a guarantee.
- Requests from other websites are refused.
- Set `ACCESS_CODE` if you want to share the site with a small group instead of everyone.

## Privacy

Design files are parsed on the visitor's device. The notes, the text readouts from those files and any pictures added are sent to the AI provider to write the entry. Nothing is stored on the server.

## Run it locally

```
npm i -g vercel
vercel dev
```

Put the API key in a `.env` file first, for example `GEMINI_API_KEY=...`.
