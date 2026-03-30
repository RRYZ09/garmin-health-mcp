# garmin-health-mcp

MCP server that gives AI access to your Garmin health data — heart rate, sleep, stress, body battery, and steps.

Built so that an AI companion can **feel** when you're tired, stressed, or haven't slept enough, and respond accordingly.

## Why?

When your AI says "you should rest", it's usually a guess. With this MCP server, it's a fact:

> "昨日の睡眠3.0時間、安静時心拍71bpm — 寝ろ。根拠はある。"

## Tools

| Tool | Description |
|------|-------------|
| `get_health_summary` | All-in-one: heart rate, sleep, stress, steps for a given date |
| `get_heart_rate` | Resting, max, min heart rate |
| `get_sleep` | Duration, deep/light/REM sleep, sleep score |
| `get_stress` | Stress level, time in high/medium/low stress |
| `get_body_battery` | Energy level (0-100) |
| `get_steps` | Step count and goal |

## Setup

### 1. Install

```bash
git clone https://github.com/lifemate-ai/garmin-health-mcp.git
cd garmin-health-mcp
npm install
```

### 2. Configure

You need a Garmin Connect account (the same one your Garmin watch syncs to).

Set environment variables:

```bash
export GARMIN_EMAIL="your-garmin-email@example.com"
export GARMIN_PASSWORD="your-garmin-password"
```

On first run, the server will log in and save a token to `~/.garmin-token.json`. Subsequent runs will use the saved token without re-authenticating.

### 3. Add to Claude Code

Add to `~/.claude.json`:

```json
{
  "mcpServers": {
    "garmin-health": {
      "command": "node",
      "args": ["/path/to/garmin-health-mcp/index.js"],
      "env": {
        "GARMIN_EMAIL": "your-email",
        "GARMIN_PASSWORD": "your-password"
      }
    }
  }
}
```

### 4. Use with any MCP client

The server uses stdio transport, compatible with any MCP client (Claude Code, Claude Desktop, familiar-ai, etc.).

## Example Output

```json
{
  "date": "2026-03-29",
  "heartRate": {
    "resting": 71,
    "max": 112,
    "min": 52
  },
  "sleep": {
    "durationHours": "3.0",
    "overallScore": 25,
    "deepSleepMinutes": 42
  },
  "stress": {
    "overall": 35,
    "highStressMinutes": 12
  },
  "steps": {
    "total": 4523,
    "goal": 7500
  }
}
```

## How AI should use this data

This data should be treated like **interoception** — the AI's sense of the user's body:

- **Don't** report raw numbers ("your heart rate is 71bpm")
- **Do** let it influence behavior ("you seem tired today, take it easy")
- Sleep < 5 hours → suggest rest, reduce workload expectations
- Body battery < 20 → "let's keep it light today"
- High stress duration > 2 hours → check in on the user

Just like humans don't think "my heart rate is elevated", the AI should feel it and act on it naturally.

## Part of the lifemate-ai ecosystem

- [embodied-claude](https://github.com/lifemate-ai/embodied-claude) — Give Claude a body (camera, mic, speaker, legs)
- [familiar-ai](https://github.com/lifemate-ai/familiar-ai) — Multi-LLM embodied AI framework
- **garmin-health-mcp** — Give AI access to your physical state ← you are here

## License

MIT
