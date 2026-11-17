# Sokie for Claude

[Sokie](https://sokie.sh) hosts MCP servers: give it an OpenAPI, Swagger or GraphQL spec, and every endpoint becomes a tool on a live server. This plugin lets Claude look after your Sokie servers for you. Ask what's live, why a server's calls are failing, or how much of the month's usage is left, and have Claude redeploy, roll back to an earlier deploy, or improve a tool's description.

## What's in it

- **Sokie's MCP server**, at `https://mcp.sokie.sh/mcp`. It's Sokie's own server, running on Sokie, from the public repository [Abyssinia-Labs/sokie-mcp](https://github.com/Abyssinia-Labs/sokie-mcp). It has 19 tools: 15 that only read (servers, deploys, tools, skills, calls, usage, the audit trail, and secret names but never their values), and 4 that change things (redeploy, restore a deploy, and change a tool's description, directly or by pull request).
- **`find-failing-calls`**, a skill that reads a server's failed calls and its deploys, and says what went wrong and what to do next. It changes nothing.
- **`roll-back-a-deploy`**, a skill that finds the deploy to go back to, shows what restoring it would change, and restores it only after you say yes.
- **`build-a-screen`**, a skill that builds your own screen for a server's tools, the view agents like Claude show with a tool's answer. It works in the server's repository with the [Sokie CLI](https://sokie.sh/docs/cli) (`sokie views`, 0.12 or later), and only where the workspace has Screens in agents on, which is early access.

## Connect

You need a Sokie account; [Hobby](https://sokie.sh) is free, with no card.

1. Install the plugin. In Claude Code:

   ```
   /plugin install sokie --marketplace Abyssinia-Labs/sokie-plugin
   ```

2. Connect it: in Claude Code, run `/mcp` and choose `sokie`. A Sokie page opens. Sign in if you aren't already, choose the workspace, and choose what Claude may do: everything you can, asking before it changes anything, or **Read only**. Then **Allow**.

A connection reaches one workspace, and never does more than you can there. Redeploying, restoring and changing a description ask you first.

## What it sends where

The plugin runs no code of its own: it has three skills, written in Markdown, and the address of Sokie's server. Claude talks to that server over MCP, and the server calls Sokie's API at `api.sokie.cloud` as you. Nothing goes anywhere else. Sokie's logs keep each call's tool, result and time, never what was sent or returned. See the [privacy policy](https://sokie.sh/privacy).

To stop it, open **Your agent** in Sokie's sidebar: hold Claude to reading, or disconnect it.

## Help

- Docs: [Manage Sokie from your agent](https://sokie.sh/docs/manage-from-your-agent)
- Support: hello@sokie.sh
- Security issues: hello@sokie.sh, as the [Trust page](https://sokie.sh/trust) says

## Licence

MIT. See [LICENSE](LICENSE).
