# Unstructured Foundation

Unstructured Foundation connects your data sources, processes files into searchable documents, and lets your AI assistant answer questions from the documents you have made available.

Use it when you want to ask natural-language questions across connected sources such as Google Drive, Dropbox, Slack, S3, and other document stores without leaving your workflow.

There are two pieces to this plugin:

1. The MCP connector, which supplies the data and tools (source setup, processing status, search, retrieval). Server URL: `https://mcp.foundation.unstructured.io/mcp`.
2. The skills, which teach the assistant how to use those tools well.

In Claude Code and Codex, installing the plugin delivers both pieces. In Claude Chat, Claude Desktop, and Cowork, the connector currently has to be added separately (see below).

## Install in Claude Code

Add the marketplace, then install the `foundation` plugin from the `unstructured-foundation` package:

```bash
claude plugin marketplace add Unstructured-IO/unstructured-foundation-marketplace
claude plugin install foundation@unstructured-foundation
```

Or install from inside a Claude session:

```text
/plugin marketplace add Unstructured-IO/unstructured-foundation-marketplace
/plugin install foundation@unstructured-foundation
```

After installing, start a new conversation so the Foundation commands and document guidance are available. The first tool call triggers a browser sign-in (OTP over email).

## Install in Claude Chat, Claude Desktop, or Cowork

No command line needed, but the two pieces are set up separately. Installing the plugin alone reliably brings in the skills, but the bundled MCP connector does not reliably connect on Desktop and Chat, so add the connector first.

Step 1, add the MCP connector:

1. Go to Customize, then Connectors.
2. Add a custom connector.
3. Enter the server URL `https://mcp.foundation.unstructured.io/mcp` and give it a name such as `foundation`.
4. Click Add, then Connect, and complete the sign-in.
5. Confirm it is active: in a chat, click the "+" button, open Connectors, and check the connector is toggled on with its tools listed.

Step 2, install the plugin for the skills:

1. Open the Customize menu and select the Plugins tab.
2. Under Personal plugins, click "+" and choose Add marketplace.
3. Enter the repository, either `https://github.com/Unstructured-IO/unstructured-foundation-marketplace` or the shorthand `Unstructured-IO/unstructured-foundation-marketplace`.
4. The plugin appears in the Plugins list. Click Install.
5. Open the installed plugin to review its components and enable what you need.

The skills are available in your next conversation, and the connector from Step 1 supplies the tools they use.

### What works on each surface

| Component | Claude Code | Cowork | Claude Chat (web and Desktop chat tab) |
| --- | --- | --- | --- |
| Skills | works | works | works |
| MCP connector | works (bundled) | works (bundled) | works only after Step 1 (separate add) |
| Slash commands | works | works | not available; ask in plain language instead |

### Availability by Claude plan

| Plan | Install the plugin (skills)? | Adding the MCP connector |
| --- | --- | --- |
| Free | No, plugins require a paid plan | One custom connector, added by the user |
| Pro / Max | Yes, self-serve | Self-serve via Customize, Connectors |
| Team | Yes, org admin controls apply | An Owner must enable it for the org first, then each member authenticates individually |
| Enterprise | Yes, unless restricted by admin policy | Owner enables org-wide, then members connect; admins can restrict connectors per role |

On Team and Enterprise, enabling a connector for the org does not automatically grant access. Each user still authenticates individually.

## Install in Codex (CLI and Desktop)

Codex supports the same SKILL.md skill format and installs plugins from a GitHub-hosted marketplace. Codex CLI, the Codex app, and the IDE extension share one config, so installing once covers all Codex surfaces.

```bash
codex plugin marketplace add Unstructured-IO/unstructured-foundation-marketplace
codex plugin add foundation@unstructured-foundation
```

Or install `foundation` interactively from the plugin browser (`/plugins` inside a Codex session, or the Plugins panel in the Codex app).

The bundled MCP server uses OAuth. After installing, log in once:

```bash
codex mcp login foundation
```

Codex has no slash-command surface for plugins. Invoke the skills by asking in plain language ("are my files ready?", "connect a new source") or reference the plugin explicitly with `@foundation`.

## Commands

In Claude Code and Cowork, Foundation exposes five slash commands:

| Command | What it does |
| --- | --- |
| `/foundation:start` | Checks whether sources are connected and whether processed documents are ready to search, then suggests the next step. |
| `/foundation:connect` | Creates a short-lived browser link for connecting a new data source, then helps start processing after the browser flow is complete. |
| `/foundation:search` | Searches connected sources for documents matching a query, with optional filters by source, file type, date ingested, or date modified. |
| `/foundation:manage` | Opens the Foundation dashboard to view, pause, resume, or remove connected sources. |
| `/foundation:help` | Shows available commands, common examples, and how to ask for document search or retrieval. |

You do not need a slash command for every search. Once sources are connected and files are processed, ask for what you need in plain language. On surfaces without slash commands (Claude Chat, Codex), plain language is the only invocation path and works for everything above.

## Example asks

- "What documents do you have access to?"
- "What is searchable right now?"
- "Are my files ready?"
- "What's new since yesterday?"
- "Search my documents for quarterly revenue."
- "Search only my Google Drive documents for vendor contracts."
- "Find PDFs about vendor contracts from last month."
- "Summarize the latest report from Dropbox."
- "Show me documents that mention Acme Corp."

## How it behaves

Foundation is pull-based. It connects a source, checks processing status, searches documents, or retrieves document content when you ask it to. It does not announce changes on its own, reuse expired connection links, or start connecting new sources without an explicit request.

When you connect a source, Foundation gives you a browser link to complete the connection. After the browser flow finishes, it can process files from that source so they become searchable.

Search requests can be broad or filtered. For example, asking "Search only my Google Drive documents for vendor contracts" searches processed documents from that data source, while "Find PDFs about vendor contracts from last month" narrows by file type and time period.

## What Foundation can help with

- Connect a data source and process files into searchable documents.
- Check whether connected sources are ready to search.
- Search document text, titles, summaries, topics, and entities.
- Summarize matching documents or retrieve details from processed text.
- Filter results by data source, file type, creation date, or modification date.

## Keeping it up to date

- Claude Code: `claude plugin marketplace update unstructured-foundation`
- Claude Chat / Desktop / Cowork: return to the Plugins tab and click Update on the marketplace.
- Codex: update from the plugin browser.

## Troubleshooting

- Skills appear but the tools or data are not available: the MCP connector is not connected. Add it as a custom connector by URL (Step 1 above).
- "Server not found" when connecting the plugin's bundled connector in Claude Desktop or Chat: known issue. Add the MCP server separately via Customize, Connectors, and it will connect.
- A component is grayed out in chat: expected for hooks and sub-agents; those run only in Cowork and Claude Code.
- "Marketplace not found": check the owner/repo spelling and your GitHub authorization. If this repository is private, your account must be authorized for the Unstructured-IO org and your GitHub account must be connected in the client before adding the marketplace.
- Connector not in the list on Team or Enterprise: an Owner must add it at the org level first.
- Codex: if tools fail with an auth error, run `codex mcp login foundation`.

## Notes

You need access to Unstructured Foundation and permission to connect the data sources you want to search. Only processed documents from connected sources are searchable. Sign-in is OTP over email; your email must be on the Foundation access list.
