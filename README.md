# With Grace connector

Connect Claude or Codex to your With Grace records. Ask questions about
properties, villas, buyers and market figures in the assistant you already use,
and let it update records for you.

Nothing runs on your machine. The connector is hosted, so setup is a URL and a
token.

## 1. Connect

### Claude Code

Two commands. You can paste them, or ask Claude to run them for you: they are
ordinary shell commands and Claude can run both.

```bash
claude mcp add --transport http --scope user withgrace https://withgrace.getaddis.im/mcp
claude mcp login withgrace
```

The first registers the connector. The second opens a browser, where you sign
in. That sign in is the one step nobody can do on your behalf, because it is
your password. Everything either side of it is automatic: the client registers
itself, generates its own keys, and stores the result.

`--scope user` makes the connector available in every project. Drop it to add
it to the current project only, which then asks you to trust the project first.

Check it worked:

```bash
claude mcp list
```

You want `✔ Connected`. `! Needs authentication` means the login has not
finished yet.

#### Over SSH, or anywhere without a browser

```bash
claude mcp login withgrace --no-browser
```

It prints a URL. Open it on any machine that has a browser, sign in, and paste
the address you land on back into the terminal. This needs a real terminal, so
it will not work from inside an assistant.

No account yet? Sign up at
[withgrace.getaddis.im](https://withgrace.getaddis.im) first. A new account
starts empty and sees only its own records.

### Claude on the web, Cowork, or Desktop

`Settings`, `Connectors`, `Add custom connector`, and paste:

```
https://withgrace.getaddis.im/mcp
```

Then connect, and sign in when prompted.

For Claude Desktop you can edit the config directly instead:

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

Two commands, the same shape as Claude Code:

```bash
codex mcp add withgrace --url https://withgrace.getaddis.im/mcp
codex mcp login withgrace
```

The first registers the connector, the second opens a browser to sign in. Check
it with `codex mcp get withgrace`.

If you would rather edit the file yourself, `~/.codex/config.toml` takes either
a header or a token read from the environment:

```toml
[mcp_servers.withgrace]
url = "https://withgrace.getaddis.im/mcp"
http_headers = { Authorization = "Bearer YOUR_TOKEN" }
```

```toml
[mcp_servers.withgrace]
url = "https://withgrace.getaddis.im/mcp"
bearer_token_env_var = "WITHGRACE_TOKEN"
```

The second keeps the token out of the file, which is the better habit if the
file is ever backed up or shared.

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

Sign in, open **Settings**, then **Connect**, to see every credential on your
account and revoke any of them. Revocation takes effect on the next request.

A client that signs in through the browser gets its own credential, so revoking
it leaves the others alone. A token you create by hand and paste into several
places is one credential in several files, and revoking it disconnects all of
them at once.

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
completed. Run `claude mcp login withgrace` again, or remove the server and add
it back.

**`Failed to connect` with an HTTP code.** The client could not reach the
connector at all. Check the URL is exactly `https://withgrace.getaddis.im/mcp`.

**`Pending approval`.** The connector was added to a project rather than to
you. Run `claude` and approve it, or add it again with `--scope user`.

**A tool returns nothing.** There are no records of that kind for your account
yet. Add one on the site or ask the assistant to create it.

## Support

Open an issue on this repository.
