# With Grace connector

Connect Claude or Codex to your With Grace records. Ask questions about
properties, villas, buyers and market figures in the assistant you already use,
and let it update records for you.

Nothing runs on your machine. The connector is hosted, so connecting is two
commands and a sign in.

## 1. Connect

### Two commands, any agent

```bash
npx withgrace auth login
npx withgrace connect
```

The first prints a short code. Open the link it shows, sign in, and approve.
That sign in is the one step nobody can do on your behalf, because it is your
password. The second finds the coding agents installed on your machine and
writes each one's configuration for you.

It needs Node 20 or newer, and it installs nothing permanently.

Check it worked:

```bash
npx withgrace auth status
```

You want every agent listed as connected. To disconnect everything and end the
credential for good:

```bash
npx withgrace auth logout
```

That revokes on the server first, so the credential is dead even if a copy of a
config file survives somewhere.

Verified with Claude Code, Gemini CLI and Codex. Anything else can take the
block to paste from `npx withgrace connect --print`.

No account yet? Sign up at
[withgrace.getaddis.im](https://withgrace.getaddis.im) first. A new account
starts empty and sees only its own records.

### Or set up one agent by hand

Every agent can also be pointed at the connector directly, which is useful if
you would rather not run anything.

**Claude Code**

```bash
claude mcp add --transport http --scope user withgrace https://withgrace.getaddis.im/mcp
claude mcp login withgrace
```

Check with `claude mcp list`. You want a connected marker. Needs authentication
means the sign in has not finished.

Over SSH, add `--no-browser` to the login. It prints a URL to open elsewhere and
waits for you to paste back the address you land on. That needs a real terminal,
so it will not work from inside an assistant. `npx withgrace auth login` has no
such limitation, which is why it is the recommended route.

**Codex**

```bash
codex mcp add withgrace --url https://withgrace.getaddis.im/mcp
codex mcp login withgrace
```

Check with `codex mcp get withgrace`.

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

A script or a scheduled job cannot sign in interactively. Sign in once on a
machine that can, then reuse the credential, or create a token under
**Settings**, then **Connect**, and send it as a header:

```
Authorization: Bearer YOUR_TOKEN
```

A token is a credential. It belongs in a config file, not in a shared document,
a screenshot or a repository.

## Troubleshooting

**The assistant says it has no With Grace tools.** The client did not load the
config. Restart it, and check the file is valid JSON or TOML.

**Every call is refused.** The connection was revoked, or the sign in never
finished. Run `npx withgrace auth status` to see which it is, then
`npx withgrace auth login` again.

**`Failed to connect` with an HTTP code.** The client could not reach the
connector at all. Check the URL is exactly `https://withgrace.getaddis.im/mcp`.

**`Pending approval`.** The connector was added to a project rather than to
you. Run `claude` and approve it, or add it again with `--scope user`.

**403 when you test the URL with curl.** Almost always your own network, not
ours. An unauthenticated request to the connector answers `401` with a
`WWW-Authenticate` header naming where to sign in. A `403` with no such header
means something between you and us refused the connection, which is common from
inside sandboxed containers.

**A tool returns nothing.** There are no records of that kind for your account
yet. Add one on the site or ask the assistant to create it.

## Support

Open an issue on this repository.
