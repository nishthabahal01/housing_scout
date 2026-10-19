# Berkeley Housing Scout

An agent that runs in Cline (VS Code) and checks Berkeley rental listings for
scam signals and unfair prices. It uses the free Parallel Search MCP to look up
current rents and the California security deposit rule. **No API key is needed
for searching.** Emailing the digest is optional and uses Composio (see below).

## Read this first: what the verdicts are (and aren't)

- **Verdicts come from building pages and aggregator pages** (Zillow, Redfin,
  Apartments.com, ApartmentList, Craigslist search pages), **not from opened
  single-unit listings.** Unless you paste a listing's text or a link the agent
  can open, deposit, payment method and viewing policy will be "unknown".
- "Likely fine" means no red flags in the data the agent could see. It does
  not mean the listing is verified. Tour the unit in person and confirm who owns
  it before you pay anything.
- Search results can be noisy or out of date. The agent is told to say when a
  number looks unreliable.
- **Nothing is ever sent to a landlord.** The agent only reads public web
  pages. It does not email, message, call, apply or fill in forms. The
  "questions to ask" are for you to send yourself. Two things back this up: the
  instructions in `PROMPT.md` forbid contacting anyone, and the Parallel Search MCP
  only has read tools (`web_search`, `web_fetch`). If you connect other tools in
  Cline (email, Composio and so on), don't approve them for this chat.

## What you need

- VS Code with the Cline extension
- Node.js (it provides `npx`). Check with `node --version`.

## Setup

1. In Cline, open **MCP Servers** and choose **Configure MCP Servers**.
2. Paste the contents of `mcp-config.json` into the settings file that opens,
   and save. If the file already lists other servers, add only the
   `"Parallel Search MCP"` entry inside the existing `mcpServers`.
   ```json
   {
     "mcpServers": {
       "Parallel Search MCP": {
         "command": "npx",
         "args": ["-y", "mcp-remote", "https://search.parallel.ai/mcp"]
       }
     }
   }
   ```
   The first time it runs, `npx -y` downloads a small helper called
   `mcp-remote` from npm. This connects Cline to `https://search.parallel.ai/mcp`.
3. Check that the server shows as connected with two tools: `web_search` and
   `web_fetch`. If it doesn't, restart VS Code.

## Run it

1. Start a new Cline chat.
2. Paste the contents of `PROMPT.md` (everything below the line) as your first message.
3. Then paste a listing's text or a link, or ask it to find new listings.
4. You get back a reply with these five sections: VERDICT, TYPICAL RANGE, RED FLAGS,
   QUESTIONS TO ASK THE LANDLORD, SOURCES.

## Daily digest (optional)

Besides vetting one listing at a time, the agent can produce a digest:

1. Search for new 2 to 3 bedroom listings in Southside, Downtown and North Berkeley.
2. Vet the best five and rank them Likely fine / Be careful / Likely scam. Each
   listing says which pages were actually opened, with the exact listing link and
   any showing details the page gave ("not stated" when it gave none).
3. Save the digest as `digests/YYYY-MM-DD.md`. The `digests/` folder is in
   `.gitignore`, so your results stay on your machine.
4. Optional: email the digest to yourself.

To run it, start a new Cline chat, paste `PROMPT.md`, then ask: "Run today's
digest: search, vet the best 5, save it to digests/, and email it to me."

**Email setup (optional):** connect a Composio MCP server in Cline and authorize
Gmail in your Composio account. Before the first send, check that the connected
Gmail account is the one you expect. The agent sends only to your own address,
never to a landlord. Without Composio the digest is still saved to a file. It just
isn't emailed.

## Files

| File | What it is |
|---|---|
| `PROMPT.md` | The standing instructions for the agent |
| `mcp-config.json` | The Parallel Search MCP settings to paste into Cline |
| `config.yaml` | Your criteria (city, $1,500 per room, 2 to 3 bedrooms, neighborhoods) |
| `LICENSE` | MIT license |
| `.gitignore` | Keeps `.env`, `digests/` and Python/Node caches out of git |
| `digests/` | Your saved daily digests (created when you run one, not in git) |

**`config.yaml` is for reference only.** Nothing reads it automatically. Your
budget and neighborhoods are written into `PROMPT.md`, so if you change your
criteria, update both files.

## Limits

- The free Parallel MCP tier is meant for light use and may be rate-limited.
- Some sites block page fetching, and some listing posts have been taken down.
  If a listing can't be opened, paste its text instead.
- Nothing runs on a schedule yet. You start each run yourself in a Cline chat.
- Email delivery is checked by reading your own mailbox. That shows the message
  landed in your Inbox and Sent folder. It does not show that you opened it.
- Prices and availability change fast. Always confirm on the listing page and in
  person before you pay anything.

## License

MIT. See `LICENSE`. This is an open-source hackathon project. It is not legal or
financial advice, and it cannot verify that a listing is real.
