# LinkedBot

A high-performance Spigot/Paper Minecraft plugin that bridges your in-game accounts with Discord roles using the Java Discord API (JDA). It runs entirely alongside your Minecraft server 24/7, automating role assignments, tracking seasonal wipes smoothly, and logging everything cleanly to Discord webhooks without causing server lag.

---

## How It Works & DiscordSRV Integration

LinkedBot works by acting as a silent, automated listener that monitors your linked channel and acts upon specific messages triggered by the **DiscordSRV** plugin. 

Here is exactly how the logic handles synchronization:

### 1. Linking Your Channel in DiscordSRV
For this plugin to work, a link channel needs to be set up in `plugins/discordsrv/config.yml`. 
* Open the DiscordSRV config file and locate the `Channels` mapping (usually around line 30).
* Ensure your linking/bot channel is mapped correctly using the `"link"` key.

Example:
`Channels: {"global": "1469739949854167243","link":"1492080151683399791"}`

### 2. Message Verification
When a message lands in your designated target channel, LinkedBot goes through a strict verification checklist:
* It checks if the message was sent by your linking bot (`target-bot-id`).
* It verifies that the message is a direct reply to an actual user's message.
* It scans the text to see if it matches your configured **DiscordSRV link phrases**.

### 3. Syncing the Link Phrases
By default, DiscordSRV sends specific phrases when someone hooks up their account. LinkedBot listens for these exact lines:
* `"You are already linked to"`
* `"Your Discord account has been linked to"`

**CRITICAL NOTE:** If you have customized or translated the link success messages inside your **DiscordSRV** configuration, you **must** copy those exact new phrases and paste them into the `discord.phrases` section of your **LinkedBot** `config.yml`. If they do not match perfectly, LinkedBot will ignore the messages.

### 4. Smart Role Handling
* **New Links:** If a user gets linked and doesn't have the roles yet, LinkedBot will fetch the message author directly from the Discord API (bypassing local cache limitations), simultaneously grant all roles listed under `add-roles`, strip any roles listed under `remove-roles` (like an "Unlinked" role), and add a success reaction to the user's original message.
* **Already Linked Check:** If a user runs the link command again but already holds the required roles, the bot will bypass running extra API calls, react with a pause symbol, and log a notice to your webhook so your chat isn't flooded with duplicate role logs.

---

## Features

* **Multi-Role Customization**: Simultaneously assign multiple roles (`add-roles`) and remove roles (`remove-roles`) in a single, atomic Discord API call.
* **API-Direct Lookups**: Explicitly retrieves the message author over the network, resolving classic JDA caching quirks where non-cached users were skipped.
* **Asynchronous Processing**: All tracking operations and intensive seasonal role resets run completely async to keep your server's TPS steady.
* **Live Season Resets**: Includes a `/newseason` administrative slash command that wipes roles globally with real-time countdown updates sent back to Discord every 5 seconds.
* **Fully Configurable**: Easily modify bot behaviors, target channel IDs, phrases, and custom reaction characters straight from `config.yml` without rewriting code.

---

## Installation

1. Drop the `LinkedBotPlugin-1.0.jar` into your Minecraft server's `plugins/` directory.
2. Start the server once to generate the default configuration files, then stop it.
3. Open `plugins/LinkedBot/config.yml` and insert your Discord Bot Token, Webhook URL, and respective Role/Channel IDs.
4. Restart your server.

---

## Configuration (`config.yml`)

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
  startup: "**Bot Started:** Logged in as `{bot_name}`"
  success: "Processed roles for {user_mention} ( `{user_id}` )"
  already-linked: "**{user_name}** ( `{user_id}` ) already has the required roles."
  error: "**Error processing link:** `{error}`"
  
  newseason:
    no-role: "No one currently has the linked roles."
    not-found: "Required roles not found in the server."
    start: "**New Season:** Removing roles from {total} members... (Triggered by {trigger_mention})"
    starting-ui: "Started removing roles from **{total}** members. Please wait..."
    progress: "**Progress:** Removed roles from **{removed} / {total}** members so far..."
    finish: "**Finished!** Successfully removed roles from **{removed}** members."
