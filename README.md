# Sokie for Claude

[Sokie](https://sokie.sh) hosts MCP servers: give it an OpenAPI, Swagger or GraphQL spec, and every endpoint becomes a tool on a live server. This plugin lets Claude look after your Sokie servers for you. Ask what's live, why a server's calls are failing, or how much of the month's usage is left, and have Claude redeploy, roll back to an earlier deploy, or improve a tool's description.

## What's in it

- **Sokie's MCP server**, at `https://mcp.sokie.sh/mcp`. It's Sokie's own server, running on Sokie, from the public repository [Abyssinia-Labs/sokie-mcp](https://github.com/Abyssinia-Labs/sokie-mcp). It has 17 tools: 13 that only read (servers, deploys, tools, calls, usage, the audit trail, and secret names but never their values), and 4 that change things (redeploy, restore a deploy, and change a tool's description, directly or by pull request).
- **`find-failing-calls`**, a skill that reads a server's failed calls and its deploys, and says what went wrong and what to do next. It changes nothing.
- **`roll-back-a-deploy`**, a skill that finds the deploy to go back to, shows what restoring it would change, and restores it only after you say yes.

## Connect

You need a Sokie account; [Hobby](https://sokie.sh) is free, with no card.

1. [Make an API key](https://sokie.sh/docs/api-keys). Start from **Agent**, which can read everything and change tool descriptions. To let Claude redeploy and restore as well, set **Deploys** to **Read and write**.
2. Install the plugin. In Claude Code:

   ```
   /plugin install sokie --marketplace Abyssinia-Labs/sokie-plugin
   ```

3. Connect it: in Claude Code, run `/mcp` and choose `sokie`. A Sokie page opens and asks for your key. Paste it, then choose what Claude may do: everything the key allows, or **Read only**.

A key works in one workspace, so a connection reaches one workspace and only the servers that key was given. Redeploying, restoring and changing a description ask you first.

## What it sends where

The plugin runs no code of its own: it has two skills, written in Markdown, and the address of Sokie's server. Claude talks to that server over MCP, and the server calls Sokie's API at `api.sokie.cloud` with your key. Nothing goes anywhere else. Sokie's logs keep each call's tool, result and time, never what was sent or returned. See the [privacy policy](https://sokie.sh/privacy).

To stop it, disconnect Claude on your [agents page](https://sokie.sh/agents/mighty-warthog-372), or revoke the key.

## Help

- Docs: [Manage Sokie from your agent](https://sokie.sh/docs/manage-from-your-agent)
- Support: hello@sokie.sh
- Security issues: hello@sokie.sh, as the [Trust page](https://sokie.sh/trust) says

## Licence

MIT. See [LICENSE](LICENSE).
