---
name: build-a-screen
description: Build or change a screen for a Sokie server's tools, the view agents like Claude show with a tool's answer, with the sokie views command. Use when someone wants a tool's answer shown as their own table, card, chart or form, or wants to change one of their server's views.
---

Screens are early access. They work only where the server's workspace has
Screens in agents turned on, in Settings › Early features or by Sokie's team.
Every `sokie views` command says "Screens aren't on for this workspace yet."
where they're off. If it does, stop and tell the person: there's nothing to
build until it's on.

## What you need

- The Sokie CLI, version 0.12 or later (`npm i -g @abyssinia-labs/sokie`),
  signed in (`sokie login`).
- The server's repository: the folder with its `sokie.json`. Run every command
  there.
- A server deployed from this folder at least once, or its slug for
  `--server <slug>`.

## How a view is made

- **One file.** A view is `views/<name>.html`: a single self-contained HTML
  page, 1 MB at most. Get data from the server's tools, not from inside the
  file.
- **Mapped in sokie.json**, under `views`:

  ```json
  "views": {
    "invoice": {
      "file": "views/invoice.html",
      "tools": ["getInvoice", "listInvoices"],
      "loadsFrom": ["fonts.googleapis.com", "fonts.gstatic.com"],
      "canCall": ["sendReminder"]
    }
  }
  ```

  - `tools`: the tools whose answers it shows. A tool shows one view at most.
  - `loadsFrom`: every domain it loads anything from (scripts, styles, fonts,
    images, `fetch`), like `fonts.googleapis.com` or `*.example.com`. Sokie
    turns this into the screen's Content Security Policy, so anything not
    listed doesn't load. Only `https`, and `wss` for a socket.
  - `canCall`: tools it calls that it doesn't show. One that no view shows is
    hidden from the model: only the screen can call it.
- **How it talks to the agent.** The page runs in the agent's sandbox and
  speaks MCP Apps (the `io.modelcontextprotocol/ui` extension) by
  `postMessage` to its parent: it sends `ui/initialize`, then gets the tool's
  arguments (`ui/notifications/tool-input`) and its answer
  (`ui/notifications/tool-result`, with the data as `structuredContent`). It
  calls tools with `tools/call`, opens links with `ui/open-link`, and says how
  tall it is with `ui/notifications/size-changed`. The starter does all of this
  by hand. To use the MCP Apps SDK (`@modelcontextprotocol/ext-apps`) instead,
  bundle it into the file (Vite with `vite-plugin-singlefile` does) or list
  the CDN you load it from in `loadsFrom`. Its `App` class covers the same
  messages: `ontoolresult`, `callServerTool({ name, arguments })`,
  `openLink({ url })` and `sendMessage(...)`, with `autoResize` on by default.
- **What its calls are.** A screen's calls are ordinary tool calls, as the
  person who's using it: What agents may do, Ask first, rate limits and logs
  apply exactly as they do to the agent's own calls.

## Workflow

1. Run `sokie views` to see the server's views and the tools each shows.
2. For a new one, pick the tool whose answer it's for and run
   `sokie views new <name> --for <tool>`. It writes `views/<name>.html` from
   the screen Sokie makes for that tool (a list, an item or a change, in the
   workspace's brand) and adds it to `sokie.json`. Names are lowercase words
   joined by hyphens.
3. Change `views/<name>.html` to what the person asked for. In the starter,
   `var boot` at the top of the script describes the answer: under `metas`,
   for each tool, its kind (`list`, `item` or `change`), the columns or
   fields it shows, and the tool a row opens with. The functions below it draw
   each kind. Change the description for small changes; write your own markup
   for bigger ones. Read `structuredContent` for the data, and keep every value
   from the API as text (`textContent`), never as HTML.
4. Add each domain the page now loads from to `loadsFrom`, and each tool it
   calls but doesn't show to `canCall`.
5. Run `sokie check` and fix what it says. It checks views as a deploy does:
   that each file exists and is under 1 MB, that every tool it names exists,
   that a tool shows one view, and that everything it visibly loads is listed
   in `loadsFrom` and uses `https`. The page's text is read the way a tool's
   description is, so it must not give the agent instructions.
6. Ask before deploying, then run `sokie deploy`.
7. Run `sokie views preview <name>`. It opens the server's Test tab on the
   view's tool with an answer to show: from `--input '<json>'`, or the tool's
   first case in `tests/`. A tool that only reads is called at once; one that
   changes things waits for the person to call it. The Test tab shows the view
   as deployed, so deploy before previewing a change.
8. Tell the person what changed and where to see it.

## Limits

- Don't put keys, tokens or other secrets in a view. Everyone who connects to
  the server gets the file.
- Don't make the screen send messages to the model on its own, or write text
  in it aimed at the model.
- Don't deploy without the person's yes: a deploy reaches everyone who
  connects.
- A server that lists its tools by search (`settings.discovery: "search"`)
  never shows views. `sokie check` warns about it.
- If `sokie views` says screens aren't on, don't try to work around it.
