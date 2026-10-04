# ReelMint MCP

Generate AI video with the newest models (Seedance 2.5, Kling 3.0, Wan 3.0, Veo 3.1, Grok Imagine…), then post it to TikTok, YouTube, Instagram, Facebook and X, run bots that post on a schedule, turn photos into a scored film, or put an AI character live on air, all from Claude Code, Cursor, Claude Desktop or any other MCP client.

ReelMint hosts the server. There is nothing to run: one URL, one token, 58 tools.

```
https://api.reel-mint.com/mcp
```

## What you can say

```
Make a 10-second Kling 3.0 clip of a lighthouse keeper lighting the lamp at dusk, 9:16, then post it to my TikTok at 6pm.
```

```
Take the 12 photos in my library from the Lisbon trip and make a 60-second film with music, travel mood.
```

```
Set up a bot that posts one sing-along counting song for 3 to 6 year olds to my YouTube channel every morning at 8. Quote it first.
```

```
Put @luna live on my YouTube channel for two hours, music mode, a rooftop stage at night.
```

Every tool that spends credits returns a price first, and nothing is posted to a social account you have not connected yourself.

## 60-second setup

1. **Create a ReelMint account** at [reel-mint.com/register](https://reel-mint.com/register?utm_source=github&utm_medium=readme&utm_campaign=launch100). New accounts get free credits on email verification, no card. The first 100 accounts through that link get 600 credits instead of 150.
2. **Mint a token**: in the app, avatar menu → **MCP access** → *Generate a token*. It is valid 90 days and gives the assistant the same access you have, including spending credits. Revoke it from the same page at any time.
3. **Add the server to your client.**

### Claude Code

```bash
claude mcp add --transport http reelmint https://api.reel-mint.com/mcp \
  --header "Authorization: Bearer YOUR_TOKEN"
```

### Cursor

`.cursor/mcp.json` in your project, or `~/.cursor/mcp.json` globally:

```json
{
  "mcpServers": {
    "reelmint": {
      "url": "https://api.reel-mint.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

### Claude Desktop

Claude Desktop reaches remote servers through `mcp-remote`. In `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "reelmint": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote", "https://api.reel-mint.com/mcp",
        "--header", "Authorization: Bearer YOUR_TOKEN"
      ]
    }
  }
}
```

### Anything else

Streamable HTTP transport, stateless, bearer auth. Both the legacy `initialize` handshake (`2025-11-25` and earlier) and the modern `server/discover` (`2026-07-28`) are answered, so older and newer clients both connect. A plain request looks like this:

```bash
curl -s https://api.reel-mint.com/mcp \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'
```

## Tools

**Studio (18)**: `list_models`, `enhance_prompt`, `plan_scenes`, `create_job`, `get_job`, `list_jobs`, `assemble_job`, `retry_job`, `cancel_job`, `delete_job`, `upscale_quote`, `upscale_video`, `list_characters`, `get_character`, `create_character`, `render_character_look`, `suggest_characters`, `list_languages`.

- `list_models` is the live catalog with every option, clip limit and price.
- `plan_scenes` writes a full multi-scene script; `create_job` films it with continuity between scenes; `assemble_job` merges the scenes into one film.
- Characters keep the same face across clips. A cast is sent to the model as reference images, never just described.

**Bot Studio (11)**: `list_destinations`, `list_bots`, `get_bot`, `quote_bot`, `create_bot`, `update_bot`, `pause_bot`, `resume_bot`, `run_bot_now`, `list_bot_runs`, `delete_bot`.

- A bot is a standing recipe on a wall-clock schedule: it generates, writes per-platform captions and posts, unattended. `quote_bot` prices one firing and extrapolates it per month before anything is saved.

**Mint Moments (9)**: `list_media`, `list_moment_categories`, `upload_photo`, `quote_moment`, `create_moment`, `get_moment`, `list_moments`, `redesign_moment`, `delete_moment`.

- Up to 15 photos in, one scored vertical film out.

**Mint Live and Mint Stage (20)**: `live_access`, `quote_live`, `get_live_puppet`, `prepare_live_puppet`, `prepare_live_dance`, `start_live`, `get_live_session`, `list_live_sessions`, `live_say`, `end_live`, `delete_live_session`, `assist_live_show`, `create_live_show`, `list_live_shows`, `get_live_show`, `pause_live_show`, `resume_live_show`, `loop_live_show`, `set_live_show_key`, `delete_live_show`.

- A character goes live on your connected YouTube channel or a TikTok LIVE key; a show prepares fresh songs and a set on a schedule and keeps the stream on air.

The server's own instructions (sent on connect) explain the workflows in the order the tools are meant to be called.

## Credits and safety

- Prices are in credits; one credit is $0.004. The catalog carries each model's price, and every `quote_*` tool answers before you buy.
- Trial accounts (never purchased) can run the free-trial models at 720p with a small ReelMint badge. Paid plans remove both limits.
- The token is a signed JWT scoped to MCP only: it is refused by the REST API, so it cannot be replayed against the app.
- Posting goes only to accounts you connected in ReelMint with the platform's official sign-in. The server never holds a platform password.

## Links

- Free models and what the free credits buy: [reel-mint.com/free](https://reel-mint.com/free)
- Every model's price: [reel-mint.com/models](https://reel-mint.com/models)
- How-to articles: [reel-mint.com/help](https://reel-mint.com/help)
- Terms: [reel-mint.com/terms](https://reel-mint.com/terms)

Questions and bugs: open an issue here, or support@reel-mint.com.
