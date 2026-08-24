# With Grace connector

Connect Claude or Codex to your With Grace records. Ask questions about
properties, villas, buyers and market figures in the assistant you already use,
and let it update records for you.

Nothing runs on your machine. The connector is hosted, so setup is a URL and a
token.

## 1. Create a token

1. Sign in at [withgrace.getaddis.im](https://withgrace.getaddis.im)
2. Open **Connect**
3. Name the token after wherever you are going to use it, and create it
4. Copy it. It is shown once and cannot be retrieved afterwards

Do not have an account? Sign up on the same page. A new account starts empty
and sees only its own records.

## 2. Add the connector

Replace `YOUR_TOKEN` with the token you just copied.

### Claude Code

```bash
claude mcp add --transport http withgrace https://withgrace.getaddis.im/mcp \
  --header "Authorization: Bearer YOUR_TOKEN"
```

### Claude Desktop

`Settings`, `Developer`, `Edit Config`, then:

```json
{
  "mcpServers": {
    "withgrace": {
      "type": "http",
      "url": "https://withgrace.getaddis.im/mcp",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

### Codex

In `~/.codex/config.toml`:

```toml
[mcp_servers.withgrace]
url = "https://withgrace.getaddis.im/mcp"

[mcp_servers.withgrace.headers]
Authorization = "Bearer YOUR_TOKEN"
```

Restart the client after editing its config.

## 3. Add the skill

The connector gives your assistant the tools. [SKILL.md](SKILL.md) tells it how
to use them well, which mostly means never inventing a price.

For Claude Code, clone this repository into your skills directory:

```bash
git clone https://github.com/withgrace-group/withgrace-skill.git \
  ~/.claude/skills/withgrace
```

For anything else, paste the contents of `SKILL.md` into your assistant's
instructions.

## 4. Try it

> What properties do I have, and how many units are in each?

> Which units are still available, and what do they cost?

> Add a contact called Mira who asked about the two bedroom villas.

## What it can reach

Your own records, and only those. Organizations, properties, units, contacts
and market figures. Every request is authenticated by your token and scoped to
the account that created it.

## Managing tokens

Create as many as you like, one per place you use it, so revoking one does not
disturb the others. Revoke from the same **Connect** page. Revocation is
immediate.

A token is a credential. It belongs in your client's config file, not in a
shared document, a screenshot or a repository.

## Troubleshooting

**The assistant says it has no With Grace tools.** The client did not load the
config. Restart it, and check the file is valid JSON or TOML.

**Every call is refused.** The token is wrong, or it has been revoked. Create a
new one and update the config.

**A tool returns nothing.** There are no records of that kind for your account
yet. Add one on the site or ask the assistant to create it.

## Support

Open an issue on this repository.
