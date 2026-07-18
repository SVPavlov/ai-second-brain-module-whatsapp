# WhatsApp module (opt-in, Mac first) — AI Second Brain

*Connect your brain to your WhatsApp: chats become client context, digests, and filed knowledge. This module is **read-first by design**: it ships with sending disabled, and you should read the risk section before changing that.*

Built on the MIT-licensed [lharries/whatsapp-mcp](https://github.com/lharries/whatsapp-mcp) (thank you, Luke Harries), plus two patches from the track: a **send guard** (jittered pacing, 30 messages/hour cap, loopback-only API) and a **send-off-by-default flag**.

## Before you start — the honest part

- **Unofficial client risk.** This connects through whatsmeow, not an official WhatsApp API. WhatsApp can, in theory, ban numbers using unofficial clients. Read-only use keeps a low profile; automated SENDING is where accounts get flagged. That is why sending is off by default and rate-guarded when on.
- **Your number, your risk.** This runs on your personal WhatsApp. If that worries you (it may), don't install, or use a separate number.
- **Data tiers apply in full.** WhatsApp holds client conversations and private life in one stream. What enters your vault follows your kit's data tiers; the skills strip what doesn't belong. Nothing from WhatsApp ever goes to the shared layer.
- **Work laptop? Private machine instead**, until your IT policy explicitly allows this.
- **Mac first.** This guide is tested on macOS. Windows: the Go bridge cross-compiles, but service setup differs — wait for the Windows guide or pioneer at your own pace.

## Install (30-45 min)

1. **Prerequisites:** Go (`brew install go`), uv (`brew install uv`), and your vault set up with the kit.
2. **Clone and patch:**
   ```bash
   git clone https://github.com/lharries/whatsapp-mcp.git ~/whatsapp-mcp
   cd ~/whatsapp-mcp
   git apply /path/to/this-module/patches/01-sendguard-and-whatsmeow-upgrade.patch
   git apply /path/to/this-module/patches/02-send-off-by-default.patch
   ```
3. **Build and pair:**
   ```bash
   cd whatsapp-bridge && go build -o whatsapp-bridge . && ./whatsapp-bridge
   ```
   Scan the QR with WhatsApp on your phone (Settings → Linked devices). Leave the bridge running; history syncs in.
4. **Connect the MCP server to your vault:** copy `config/mcp-config-example.json` into your vault as `.mcp.json` (or merge it), fix the absolute path.
5. **Activate the skills:**
   ```bash
   cp -r /path/to/this-module/skills/* YOUR-VAULT/.claude/skills/
   ```
6. **First run must be interactive:** start `claude` in your vault once and accept the workspace-trust dialog (headless runs ignore the permission settings until you do). Then test: say "whatsapp digest". You should see your recent chats summarized.

**Keep it running (optional):** a launchd plist keeps the bridge alive across reboots — see the upstream README. Start manual; automate when you trust it.

## Enabling sending (read this twice)

Sending is off. The bridge answers 403 on any send attempt until you set `WA_SEND_ENABLED=true` in the bridge's environment. If you enable it: the send guard paces every message (2-5s jitter) and caps at 30 per rolling hour — do not raise these numbers, they are the anti-ban margin. Never build bulk-send automations on your personal number.

## The skills

- **whatsapp-digest** — "whatsapp digest": read-only overview of a period (default 3 days): what came in, what needs you, per chat. Nothing is written without your say-so.
- **whatsapp-to-brain** — "put this chat in my brain": a conversation becomes filed context under the right client or person, tier-checked, third-party numbers stripped.

## License

Upstream whatsapp-mcp: MIT (Luke Harries). Patches and skills in this module: MIT, AI Second Brain Track.
