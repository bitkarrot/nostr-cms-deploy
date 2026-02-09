# Nostr CMS Deploy

A single-page deployment helper for [Nostr CMS](https://github.com/bitkarrot/nostr-cms). It provides an interactive form that collects the required environment variables and generates a ready-to-paste deployment prompt for [Zeabur](https://zeabur.com).

## What It Does

1. **Collects configuration** — walks the user through three required fields:
   - `VITE_MASTER_PUBKEY` — the owner/admin Nostr public key (supports npub or hex input, with automatic conversion).
   - `VITE_REMOTE_NOSTR_JSON_URL` — URL to a remote `nostr.json` file used for admin authentication (e.g. `https://yourdomain.com/.well-known/nostr.json`).
   - `VITE_DEFAULT_RELAY` — the primary Nostr relay for reading and publishing content (e.g. `wss://relay.damus.io`).

2. **Nostr Login integration** — users can log in with a Nostr extension (NIP-07) or Nostr Connect to auto-fill their public key.

3. **Generates a deploy prompt** — produces a text block that can be copied and pasted into Zeabur's AI assistant to deploy the Nostr CMS with the configured environment variables.

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `VITE_MASTER_PUBKEY` | Yes | Hex public key of the site owner/admin |
| `VITE_REMOTE_NOSTR_JSON_URL` | Yes | URL to a `nostr.json` file for admin role mapping |
| `VITE_DEFAULT_RELAY` | Yes | Primary Nostr relay (websocket URL starting with `wss://`) |
| `VITE_SWARM_API_URL` | No | Swarm relay API URL for scheduled posts support |

### Validating Your Environment Variables

**`VITE_REMOTE_NOSTR_JSON_URL`** must point to a URL that returns valid JSON conforming to [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md). You can verify it with:

```sh
curl -s "https://yourdomain.com/.well-known/nostr.json" | python3 -m json.tool
```

The response should contain at least a `names` object mapping usernames to hex public keys:

```json
{
  "names": {
    "alice": "abc123..."
  }
}
```

**`VITE_DEFAULT_RELAY`** must be a working Nostr relay. You can verify it with [nak](https://github.com/fiatjaf/nak):

```sh
nak req -l 1 -k 1 wss://relay.damus.io
```

A successful response returns a JSON Nostr event, confirming the relay is live.

Some well-known public relays:
- `wss://relay.damus.io`
- `wss://relay.primal.net`
- `wss://nos.lol`

## Project Structure

```
nostr-cms-deploy/
├── index.html    # Single-page deploy helper (HTML + CSS + JS)
├── .gitignore
└── README.md
```

## How to Use

1. Open `index.html` in a browser (or serve it with any static file server).
2. Log in with Nostr or manually enter your public key.
3. Enter your `nostr.json` URL and default relay.
4. Copy the generated deployment prompt.
5. Paste it into [Zeabur's AI assistant](https://zeabur.com) to deploy your Nostr CMS instance.

## Dependencies

This is a standalone HTML page with no build step. It loads two external scripts from CDN:

- [nostr-tools](https://github.com/nbd-wtf/nostr-tools) — for npub/hex key conversion (NIP-19)
- [nostr-login](https://github.com/nicefacer/nostr-login) — for Nostr extension and Nostr Connect authentication

## License

See the [Nostr CMS repository](https://github.com/bitkarrot/nostr-cms) for license details.
