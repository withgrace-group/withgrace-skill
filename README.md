# With Grace connector

Connect Claude or Codex to your With Grace records. Ask questions about
properties, villas, buyers and market figures in the assistant you already use,
and let it update records for you.

Nothing runs on your machine. The connector is hosted, so setup is a URL and a
token.

## 1. Connect

### Claude Code

```bash
claude mcp add --transport http --scope user withgrace https://withgrace.getaddis.im/mcp
```

Then run `claude`, and approve the sign in when it offers. A browser opens,
you sign in, and you are connected. Nothing to copy and nothing to edit.

`--scope user` makes it available in every project. Drop it to add the
connector to the current project only, which then asks you to trust the
project first.

No account yet? Sign up at
[withgrace.getaddis.im](https://withgrace.getaddis.im) first. A new account
starts empty and sees only its own records.

### Claude Desktop

`Settings`, `Developer`, `Edit Config`:

```json
{
  "mcpServers": {
    "withgrace": {
      "type": "http",
      "url": "https://withgrace.getaddis.im/mcp"
    }
  }
}
```

Restart, then approve the sign in when it prompts.

### Codex

In `~/.codex/config.toml`:

```toml
experimental_use_rmcp_client = true

[mcp_servers.withgrace]
url = "https://withgrace.getaddis.im/mcp"
```

Then:

```bash
codex mcp login withgrace
```

`experimental_use_rmcp_client` is what enables remote servers with sign in.

## 2. Add the skill

The connector gives your assistant the tools. [SKILL.md](SKILL.md) tells it how
to use them well, which mostly means never inventing a price.

For Claude Code, clone this repository into your skills directory:

```bash
git clone https://github.com/withgrace-group/withgrace-skill.git \
  ~/.claude/skills/withgrace
```

For anything else, paste the contents of `SKILL.md` into your assistant's
instructions.

## 3. Try it

> What properties do I have, and how many units are in each?

> Which units are still available, and what do they cost?

> Add a contact called Mira who asked about the two bedroom villas.

## What it can reach

Your own records, and only those. Organizations, properties, units, contacts
and market figures. Every request is authenticated by your token and scoped to
the account that created it.

## Managing access

Every client you connect gets its own credential, so revoking one does not
disturb the others. See and revoke them on the **Connect** page after signing
in. Revocation takes effect immediately.

### Connecting something that has no browser

A script or a scheduled job cannot sign in interactively. Create a token on the
**Connect** page instead and send it as a header:

```
Authorization: Bearer YOUR_TOKEN
```

A token is a credential. It belongs in a config file, not in a shared document,
a screenshot or a repository.

## Troubleshooting

**The assistant says it has no With Grace tools.** The client did not load the
config. Restart it, and check the file is valid JSON or TOML.

**Every call is refused.** The connection was revoked, or sign in never
completed. Remove the server from your client and add it again.

**A tool returns nothing.** There are no records of that kind for your account
yet. Add one on the site or ask the assistant to create it.

## Support

Open an issue on this repository.
