# LinkedBot - An addon for discord-srv 🌙

A high-performance Spigot/Paper Minecraft plugin that bridges your in-game accounts with Discord roles using the Java Discord API (JDA). It runs entirely alongside your Minecraft server 24/7, automating role assignments, tracking seasonal wipes smoothly, and logging everything cleanly to Discord webhooks without causing server lag.

---

## 🚀 Features

*   **Multi-Role Customization**: Simultaneously assign multiple roles (`add-roles`) and remove roles (`remove-roles`) in a single, atomic Discord API call.
*   **Smart Duplicate Checks**: Detects if a user already has their linked roles, reacts with a customizable pause emoji (`⏸️`), and avoids redundant logs.
*   **Asynchronous Processing**: All tracking operations and intensive seasonal role resets run completely async to keep your server's TPS steady.
*   **Live Season Resets**: Includes a `/newseason` administrative slash command that wipes roles globally with real-time countdown updates sent back to Discord every 5 seconds.
*   **Fully Configurable**: Easily modify bot behaviors, target channel IDs, phrases, and custom emojis straight from `config.yml` without rewriting code.

---

## 🛠️ Installation

1. Drop the `LinkedBotPlugin-1.0.jar` into your Minecraft server's `plugins/` directory.
2. Start the server once to generate the default configuration files, then stop it.
3. Open `plugins/LinkedBot/config.yml` and insert your Discord Bot Token, Webhook URL, and respective Role/Channel IDs.
4. Restart your server.

---

## ⚙️ Configuration (`config.yml`)

```yaml
discord:
  token: "YOUR_BOT_TOKEN_HERE"
  target-bot-id: 1500375838376329236
  target-channel-id: 1492080151683399791
  # Roles to GIVE when they link
  add-roles:
    - 1490345187405467809
  # Roles to REMOVE when they link (e.g., an unlinked role)
  remove-roles:
    - 1234567890123456789
  webhook-url: "YOUR_WEBHOOK_URL_HERE"
  phrases:
    - "You are already linked to"
    - "Your Discord account has been linked to"
  emojis:
    success: "✅"
    already-linked: "⏸️"

messages:
  startup: "🟢 **Bot Started:** Logged in as `{bot_name}`"
  success: "✅ Processed roles for {user_mention} ( `{user_id}` )"
  already-linked: "⏸️ **{user_name}** ( `{user_id}` ) already has the required roles."
  error: "⚠️ **Error processing link:** `{error}`"
  
  newseason:
    no-role: "ℹ️ No one currently has the linked roles."
    not-found: "❌ Required roles not found in the server."
    start: "🔄 **New Season:** Removing roles from {total} members... (Triggered by {trigger_mention})"
    starting-ui: "⏳ Started removing roles from **{total}** members. Please wait..."
    progress: "⏳ **Progress:** Removed roles from **{removed} / {total}** members so far..."
    finish: "✅ **Finished!** Successfully removed roles from **{removed}** members."
