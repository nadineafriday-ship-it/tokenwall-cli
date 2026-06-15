# tokenwall

Sync your local AI coding-tool token usage into [Tokenwall](https://tokenwall.app).

Some of your AI token spend only exists locally — terminal coding tools like
Claude Code write usage logs to your machine that the Tokenwall web app can't
read on its own. This CLI reads those local logs, aggregates the token counts,
and sends them to your Tokenwall account so your $/Mt efficiency score reflects
everything.

## Install / Usage

No install needed — run it with `npx`:

```sh
npx tokenwall sync
```

Or install it globally:

```sh
npm install -g tokenwall
tokenwall sync
```

Requires **Node.js 18+** (uses the built-in `fetch`).

## Getting an API token

1. Open Tokenwall and go to **Settings**.
2. Create a personal **API token**.
3. Make it available to the CLI in one of these ways:
   - Pass it directly: `tokenwall sync --token <your-token>`
   - Set an environment variable: `export TOKENWALL_TOKEN=<your-token>`
   - Save it in `~/.tokenwall/config.json`:
     ```json
     {
       "token": "<your-token>",
       "apiUrl": "https://api.tokenwall.app/api/public/ingest"
     }
     ```

The token is resolved in that order: `--token` flag → `TOKENWALL_TOKEN` →
config file.

## Commands

### `tokenwall sync`

Reads your local AI usage logs and POSTs the aggregated counts to Tokenwall.

| Flag | Description |
| --- | --- |
| `--dry-run` | Do everything except the POST, and print the exact JSON payload that would be sent. |
| `--token <token>` | Override the API token. |
| `--url <url>` | Override the ingest URL. |

Other built-ins: `tokenwall --version`, `tokenwall --help`.

The ingest URL is resolved in this order: `--url` flag → `TOKENWALL_API_URL`
env → `apiUrl` in the config file → the default Tokenwall ingest endpoint.

## Supported data sources

| Platform | Status |
| --- | --- |
| Claude Code | ✅ Supported |
| Cursor | 🔜 Coming soon |
| Codex | 🔜 Coming soon |
| GitHub Copilot | 🔜 Coming soon |

### Claude Code

The CLI reads Claude Code's per-session `.jsonl` logs under
`~/.claude/projects/<project>/*.jsonl` (and `~/.claude/transcripts/*.jsonl` if
present), then aggregates token counts grouped by model and date.

Set `TOKENWALL_CLAUDE_DIR` to point at a different `.claude` root (useful for
testing).

## Payload shape

```json
{
  "source": "cli",
  "cli_version": "0.1.0",
  "synced_at": "2026-06-15T14:05:26.450Z",
  "usage": [
    {
      "platform": "claude-code",
      "model": "claude-sonnet-4-20250514",
      "date": "2026-06-10",
      "input_tokens": 1840,
      "output_tokens": 1250,
      "cache_read_tokens": 2300,
      "cache_creation_tokens": 200
    }
  ]
}
```

## License

MIT
