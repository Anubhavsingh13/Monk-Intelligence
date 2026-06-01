*[Claude Code 101](../../README.md) · Day 6b of 14*

# Day 6b — MCP Server Setup: Installing and Building

> **Today's one idea:** An MCP server is just a process that speaks a protocol — installing an existing one takes five minutes; building a custom one takes an afternoon — and the wiring into Claude Code is identical either way.
> **Reading time:** ~50 min · **Prereqs:** [Day 6](day-06-mcp-servers-wiring-diagram.md)
> **Primary source for today:** Model Context Protocol Documentation — modelcontextprotocol.io/docs; `@modelcontextprotocol/sdk` package — npmjs.com/package/@modelcontextprotocol/sdk

---

## The hook

Day 6 showed you the `mcpServers` block in `settings.json` and three ready-made servers for TaskFlow. You understood the concept. But then you went to actually set one up and hit the first real question: where does this server actually come from? Do I `npm install` it globally? Does `npx -y` download it fresh every time? Why does Claude say "no tools available" even though the config looks right? And when none of the existing servers do exactly what you need — how do you build one?

This page answers all of that. Two concrete paths: install a community server and verify it works; build a minimal custom server from scratch and wire it in. By the end, MCP servers are no longer magic — they are processes you understand, configure, and debug like anything else.

---

## Part 1 — Installing an existing MCP server

### Finding servers

The main sources, in order of reliability:

1. **Anthropic's reference servers** — `github.com/modelcontextprotocol/servers` — the official repository of maintained servers for Postgres, filesystem, GitHub, Slack, Google Drive, web search, and more. These are the most tested against Claude Code.

2. **npm registry** — search `@modelcontextprotocol` for official packages, or `mcp-server-*` for community ones. Check download counts and last-published date before trusting a community server.

3. **GitHub search** — `topic:mcp-server` reveals servers for more niche systems (Linear, Jira, Datadog, Grafana, internal tooling).

4. **Anthropic docs** — docs.anthropic.com/en/docs/claude-code lists vetted integrations with installation snippets.

### Two installation approaches

**Option A — `npx -y` (zero install, fresh on each session start)**

```json
"postgres": {
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-postgres",
           "postgresql://localhost:5432/taskflow_dev"]
}
```

- `npx -y` downloads and runs the package without a permanent install
- Slower session start (package download on first use if not cached)
- Always runs the latest version — can break if a new version has breaking changes
- Good for: trying a server before committing to it

**Option B — global install (recommended for servers you use daily)**

```bash
# Install once
npm install -g @modelcontextprotocol/server-postgres
npm install -g @modelcontextprotocol/server-github

# Verify installed
npx @modelcontextprotocol/server-postgres --version
```

Then reference the installed binary directly:

```json
"postgres": {
  "command": "mcp-server-postgres",
  "args": ["postgresql://localhost:5432/taskflow_dev"]
}
```

Or continue using `npx` — it will use the cached version once installed:

```json
"postgres": {
  "command": "npx",
  "args": ["@modelcontextprotocol/server-postgres",
           "postgresql://localhost:5432/taskflow_dev"]
}
```

- Faster session start (no download)
- Pinned to the installed version — update explicitly with `npm update -g`
- Good for: servers you rely on daily in production workflows

**Option C — Python-based servers**

Some servers are Python packages:

```bash
pip install mcp-server-sqlite
```

```json
"sqlite": {
  "command": "python",
  "args": ["-m", "mcp_server_sqlite", "--db-path", "./data/taskflow.db"]
}
```

Or using `uvx` (uv's equivalent of npx):

```json
"sqlite": {
  "command": "uvx",
  "args": ["mcp-server-sqlite", "--db-path", "./data/taskflow.db"]
}
```

### The full `mcpServers` configuration reference

Every field you can set on a server entry:

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",           // Required: executable to run
      "args": ["server.js"],       // Optional: arguments to the command
      "env": {                     // Optional: environment variables for the server process
        "API_KEY": "${env:MY_API_KEY}",   // Always use ${env:VAR} for secrets
        "DEBUG": "false"                  // Literal values are fine for non-secrets
      },
      "cwd": "/path/to/server"    // Optional: working directory for the server process
                                  // Defaults to the project root
    }
  }
}
```

**Critical: the `env` block is for the MCP server process, not for Claude Code itself.** If your server needs an API key, put it in `env`. If Claude Code needs it (for a hook or Bash command), put it in the shell environment or use a `.env` file.

**`${env:VAR}` syntax** tells Claude Code to read the value from the current shell environment at session start. This is the correct way to inject secrets — never hardcode credentials in `settings.json`, which gets committed to the repository.

### Verifying a server connected

After adding a server, start a new Claude Code session and run:

```bash
claude --print "List all the tools you have available, including MCP tools."
```

A connected server shows its tools:

```
Available tools:
Built-in: Read, Edit, Write, Bash, Glob, Grep, Agent, TodoWrite

MCP (postgres):
  - postgres__query — Execute a SQL query
  - postgres__list_tables — List all tables in the database
  - postgres__describe_table — Get the schema for a specific table

MCP (github):
  - github__list_issues — List issues for a repository
  - github__get_issue — Get details of a specific issue
  - github__list_pull_requests — List open pull requests
```

A disconnected or misconfigured server produces:

```
Available tools:
Built-in: Read, Edit, Write, Bash, Glob, Grep, Agent, TodoWrite

(no MCP tools listed)
```

Or an error in stderr:

```
Failed to connect to MCP server "postgres": spawn mcp-server-postgres ENOENT
```

### Troubleshooting connection failures

| Error | Cause | Fix |
|-------|-------|-----|
| `spawn <command> ENOENT` | Command not found — binary not installed or not in PATH | Install the package globally; check `which <command>` |
| `Connection refused` | Server process started but immediately crashed | Run the command manually in terminal to see the error output |
| `No tools listed` (silent) | Server connected but exposed zero tools | Run the server manually and check its startup output |
| `${env:VAR}` not resolved | Environment variable not set in shell | `echo $MY_VAR` to verify; add to `~/.bashrc` if missing |
| `EACCES` permission error | Server binary not executable | `chmod +x` the binary |
| Works in terminal, not in Claude | PATH differs between shell and Claude Code | Use absolute path in `command` field instead of relying on PATH |

**The most reliable debugging step:** run the server command manually in your terminal, exactly as specified in `settings.json`, and read the output. Claude Code runs the same command — if it works in the terminal, it will work in Claude Code.

```bash
# Test the postgres server manually
npx @modelcontextprotocol/server-postgres postgresql://localhost:5432/taskflow_dev
# Should output something like: MCP server listening on stdio
# If it errors, the error message tells you exactly what is wrong
```

---

## Part 2 — Building a custom MCP server

### When to build vs. install

Use this decision table before writing a line of custom server code:

| Situation | Use |
|-----------|-----|
| A community server exists that does what you need | Install it — don't build |
| You need one specific API call, once in a while | Bash + allow list is simpler |
| You need structured access to an internal system with no community server | Build a custom server |
| You need the same capability across 5+ sessions per week | Build a custom server |
| You need to hide API keys from the CLAUDE.md and settings.json | Build a custom server (server holds the credential, not the config) |
| You need to transform or validate API responses before Claude sees them | Build a custom server |

A custom MCP server is the right tool when: you need it often, it requires credentials you don't want in your config, or the operation is complex enough that a simple Bash call won't do.

### Understanding stdio transport

Claude Code uses **stdio transport** for MCP servers — the simplest option. The server reads JSON messages from stdin and writes JSON responses to stdout. Claude Code manages the process lifecycle: it starts the server when the session starts, keeps it running, and terminates it when the session ends.

This means your server is a long-running process that:
1. Reads a JSON-encoded request from stdin
2. Does the work
3. Writes a JSON-encoded response to stdout
4. Loops back to step 1

The MCP SDK handles all the protocol framing for you — you just define tools as functions.

### Building a custom server in TypeScript

This is the most common path — the TypeScript SDK is the most complete.

**Step 1: Initialize the project**

```bash
mkdir mcp-taskflow-tools
cd mcp-taskflow-tools
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node
npx tsc --init
```

Edit `tsconfig.json` to set:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "outDir": "./dist",
    "strict": true
  }
}
```

**Step 2: Write the server**

Create `src/index.ts`. Here is a complete minimal server that exposes two tools:

```typescript
// src/index.ts
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

// Initialize the server with a name and version
const server = new McpServer({
  name: "taskflow-tools",
  version: "1.0.0",
});

// ── Tool 1: Get sprint summary ────────────────────────────────────────────────
// Reads from a local JSON file that your CI pipeline generates
server.tool(
  "get_sprint_summary",
  "Get the current sprint's open tasks, blockers, and completion percentage",
  {
    // Input schema using zod — validation is automatic
    sprint_id: z.string().optional().describe("Sprint ID. Defaults to current sprint."),
  },
  async ({ sprint_id }) => {
    // Your actual implementation here
    // This example reads from a local file — replace with your API call
    const fs = await import("fs/promises");
    const data = await fs.readFile("./sprint-data.json", "utf-8");
    const sprints = JSON.parse(data);
    const sprint = sprint_id
      ? sprints.find((s: any) => s.id === sprint_id)
      : sprints[0]; // current sprint

    if (!sprint) {
      return {
        content: [{ type: "text", text: `Sprint ${sprint_id} not found.` }],
        isError: true,
      };
    }

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(sprint, null, 2),
        },
      ],
    };
  }
);

// ── Tool 2: Search internal Confluence docs ───────────────────────────────────
server.tool(
  "search_docs",
  "Search the team's internal documentation for architecture decisions and runbooks",
  {
    query: z.string().describe("Search query"),
    space: z.enum(["engineering", "devops", "product"]).optional()
      .describe("Confluence space to search. Omit to search all spaces."),
  },
  async ({ query, space }) => {
    const CONFLUENCE_API = process.env.CONFLUENCE_URL;
    const CONFLUENCE_TOKEN = process.env.CONFLUENCE_TOKEN;

    if (!CONFLUENCE_API || !CONFLUENCE_TOKEN) {
      return {
        content: [{ type: "text", text: "CONFLUENCE_URL or CONFLUENCE_TOKEN not set." }],
        isError: true,
      };
    }

    const params = new URLSearchParams({
      cql: space
        ? `text ~ "${query}" AND space.key = "${space}"`
        : `text ~ "${query}"`,
      limit: "5",
    });

    const response = await fetch(`${CONFLUENCE_API}/rest/api/content/search?${params}`, {
      headers: {
        Authorization: `Bearer ${CONFLUENCE_TOKEN}`,
        Accept: "application/json",
      },
    });

    if (!response.ok) {
      return {
        content: [{ type: "text", text: `Confluence API error: ${response.statusText}` }],
        isError: true,
      };
    }

    const results = await response.json();
    const formatted = results.results.map((r: any) => ({
      title: r.title,
      space: r.space?.name,
      url: `${CONFLUENCE_API}/wiki${r._links.webui}`,
      excerpt: r.excerpt,
    }));

    return {
      content: [
        {
          type: "text",
          text: formatted.length > 0
            ? JSON.stringify(formatted, null, 2)
            : "No results found.",
        },
      ],
    };
  }
);

// ── Start the server ──────────────────────────────────────────────────────────
async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  // Do NOT use console.log here — stdout is reserved for MCP protocol messages
  // Use console.error for any diagnostic output (goes to stderr)
  console.error("taskflow-tools MCP server started");
}

main().catch((err) => {
  console.error("Server error:", err);
  process.exit(1);
});
```

**Step 3: Build and test manually**

```bash
# Build
npx tsc

# Test manually: start the server and send it a test message
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' | node dist/index.js
```

Expected output (on stdout):
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      {
        "name": "get_sprint_summary",
        "description": "Get the current sprint's open tasks, blockers, and completion percentage",
        "inputSchema": { ... }
      },
      {
        "name": "search_docs",
        "description": "Search the team's internal documentation...",
        "inputSchema": { ... }
      }
    ]
  }
}
```

If you get valid JSON back, the server works. If you get nothing, check `stderr` for errors.

**Step 4: Wire it into `settings.json`**

```json
{
  "mcpServers": {
    "taskflow-tools": {
      "command": "node",
      "args": ["/absolute/path/to/mcp-taskflow-tools/dist/index.js"],
      "env": {
        "CONFLUENCE_URL": "${env:CONFLUENCE_URL}",
        "CONFLUENCE_TOKEN": "${env:CONFLUENCE_TOKEN}"
      }
    }
  }
}
```

Use an absolute path in `args`. Relative paths can fail depending on which directory Claude Code starts from.

**Step 5: Add a `package.json` start script and document it**

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsc --watch"
  }
}
```

Add a `README.md` to the server repo so other developers can install it:

```markdown
## Setup

1. `npm install`
2. `npm run build`
3. Set environment variables: CONFLUENCE_URL, CONFLUENCE_TOKEN
4. Add to `.claude/settings.json`:
   ```json
   "taskflow-tools": {
     "command": "node",
     "args": ["/path/to/this/repo/dist/index.js"],
     "env": {
       "CONFLUENCE_URL": "${env:CONFLUENCE_URL}",
       "CONFLUENCE_TOKEN": "${env:CONFLUENCE_TOKEN}"
     }
   }
   ```
5. Restart Claude Code session
```

---

### Building a custom server in Python

For teams on Python stacks, the `mcp` package provides the same capability.

**Step 1: Set up the project**

```bash
mkdir mcp-taskflow-tools
cd mcp-taskflow-tools
pip install mcp
# or with uv:
uv init && uv add mcp
```

**Step 2: Write the server**

Create `server.py`:

```python
# server.py
import asyncio
import json
import os
from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp.types import Tool, TextContent
import mcp.types as types

# Initialize the server
app = Server("taskflow-tools")


@app.list_tools()
async def list_tools() -> list[Tool]:
    """Declare the tools this server exposes."""
    return [
        Tool(
            name="get_sprint_summary",
            description="Get the current sprint's open tasks and completion percentage",
            inputSchema={
                "type": "object",
                "properties": {
                    "sprint_id": {
                        "type": "string",
                        "description": "Sprint ID. Omit for current sprint.",
                    }
                },
            },
        ),
        Tool(
            name="search_docs",
            description="Search internal Confluence documentation",
            inputSchema={
                "type": "object",
                "properties": {
                    "query": {
                        "type": "string",
                        "description": "Search query",
                    },
                    "space": {
                        "type": "string",
                        "enum": ["engineering", "devops", "product"],
                        "description": "Confluence space to search",
                    },
                },
                "required": ["query"],
            },
        ),
    ]


@app.call_tool()
async def call_tool(name: str, arguments: dict) -> list[TextContent]:
    """Handle tool calls."""

    if name == "get_sprint_summary":
        sprint_id = arguments.get("sprint_id")
        try:
            with open("sprint-data.json") as f:
                sprints = json.load(f)
            sprint = (
                next((s for s in sprints if s["id"] == sprint_id), None)
                if sprint_id
                else sprints[0]
            )
            if not sprint:
                return [TextContent(type="text", text=f"Sprint {sprint_id} not found.")]
            return [TextContent(type="text", text=json.dumps(sprint, indent=2))]
        except FileNotFoundError:
            return [TextContent(type="text", text="sprint-data.json not found.")]

    elif name == "search_docs":
        import urllib.request
        query = arguments["query"]
        space = arguments.get("space")
        confluence_url = os.environ.get("CONFLUENCE_URL")
        confluence_token = os.environ.get("CONFLUENCE_TOKEN")

        if not confluence_url or not confluence_token:
            return [TextContent(type="text",
                               text="CONFLUENCE_URL or CONFLUENCE_TOKEN not set.")]

        cql = f'text ~ "{query}"'
        if space:
            cql += f' AND space.key = "{space}"'

        url = f"{confluence_url}/rest/api/content/search?cql={cql}&limit=5"
        req = urllib.request.Request(
            url,
            headers={"Authorization": f"Bearer {confluence_token}",
                     "Accept": "application/json"},
        )
        with urllib.request.urlopen(req) as resp:
            data = json.loads(resp.read())

        results = [
            {"title": r["title"],
             "url": f"{confluence_url}/wiki{r['_links']['webui']}",
             "excerpt": r.get("excerpt", "")}
            for r in data.get("results", [])
        ]
        text = json.dumps(results, indent=2) if results else "No results found."
        return [TextContent(type="text", text=text)]

    else:
        return [TextContent(type="text", text=f"Unknown tool: {name}")]


async def main():
    async with stdio_server() as (read_stream, write_stream):
        await app.run(read_stream, write_stream, app.create_initialization_options())


if __name__ == "__main__":
    asyncio.run(main())
```

**Step 3: Test manually**

```bash
python server.py
# In another terminal (or pipe):
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' | python server.py
```

**Step 4: Wire into `settings.json`**

```json
{
  "mcpServers": {
    "taskflow-tools": {
      "command": "python",
      "args": ["/absolute/path/to/mcp-taskflow-tools/server.py"],
      "env": {
        "CONFLUENCE_URL": "${env:CONFLUENCE_URL}",
        "CONFLUENCE_TOKEN": "${env:CONFLUENCE_TOKEN}"
      }
    }
  }
}
```

Or, if using `uv` for environment management:

```json
{
  "mcpServers": {
    "taskflow-tools": {
      "command": "uv",
      "args": ["run", "server.py"],
      "cwd": "/absolute/path/to/mcp-taskflow-tools"
    }
  }
}
```

---

## Part 3 — Verifying and using your custom server

### The four verification commands

Run these in order after wiring a new server (existing or custom):

```bash
# 1. Verify the server process starts without errors
# (run the command manually in your terminal first)
node dist/index.js
# Should start without crashing. Ctrl+C when done.

# 2. Verify tool listing works
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' | node dist/index.js
# Should return JSON with your tools listed

# 3. Verify Claude Code sees the server
claude --print "List all MCP tools available to you."
# Should include your server's tools

# 4. Verify a tool call works end-to-end
claude --print "Use the get_sprint_summary tool and show me the result."
# Should call the tool and return real data
```

If step 1 passes but step 3 fails, the issue is in `settings.json` (wrong path, wrong command, env var not set). If step 1 fails, the issue is in the server code itself.

### Using MCP tools effectively in sessions

Once connected, you use MCP tools the same way you direct Claude to use any tool — by asking naturally:

```
"What are the open issues blocking the v2.1 release? Check GitHub."
→ Claude calls: github__list_issues(milestone="v2.1", state="open")

"Describe the tasks table schema."
→ Claude calls: postgres__describe_table(table="tasks")

"Is there a runbook for database failover? Search the docs."
→ Claude calls: taskflow-tools__search_docs(query="database failover", space="devops")
```

You can also reference MCP tools explicitly in skills. In your `/fix` skill, add:

```markdown
## Step 1: Scope assessment
If a database schema question is relevant, use postgres__describe_table to get the
current schema rather than reading migration files. Migration files show history;
the describe_table tool shows the current state.
```

---

## Where it breaks

**stdout is the MCP protocol wire — never log to it.** In your custom server, `console.log()` (TypeScript) or `print()` (Python) writes to stdout. Anything unexpected on stdout corrupts the protocol framing and causes silent failures. Always use `console.error()` / `sys.stderr.write()` for diagnostic output.

**Long-running tool calls block the session.** If your tool calls a slow API (5+ seconds), Claude Code waits. For slow operations, return a summary immediately and offer to run a deeper analysis if needed. Do not make tools that routinely take more than 2–3 seconds.

**No graceful error handling = silent failures.** If your tool throws an uncaught exception, the server process may crash. The session then has no MCP tools without a clear error message. Always wrap tool implementations in try/catch and return `isError: true` with a descriptive message rather than letting exceptions propagate.

**`npx -y` in a restricted network environment.** Some CI environments or corporate networks block npm registry access. If you see `npx: unable to determine runtime` in CI, switch to a globally installed binary or bundle the server code directly in your repository.

**Version pinning for team consistency.** If you use `npx -y @modelcontextprotocol/server-postgres` without a version pin, different developers may run different versions. Pin versions for shared servers:

```json
"args": ["-y", "@modelcontextprotocol/server-postgres@0.6.2",
         "postgresql://localhost:5432/taskflow_dev"]
```

---

## Try it yourself

**Exercise 1 — Install and verify the filesystem server (comprehension check)**

Add `@modelcontextprotocol/server-filesystem` to TaskFlow's `settings.json`, scoped to the `docs/` directory. Then:
1. Start a Claude Code session and verify the server appears in the tool list
2. Ask Claude: "List all files in the docs directory" — it should use the filesystem MCP tool, not the Bash `ls` command
3. Explain in one sentence why scoping the filesystem server to `docs/` is safer than giving it access to the project root

---

**Exercise 2 — Build a tool for a real internal system**

Design (or implement if you have a suitable internal system) a custom MCP server with one tool that connects to something your team uses — a ticketing system, an internal API, a monitoring dashboard, a configuration store. Your design must include:
1. The `tool()` definition with name, description, and input schema
2. The `env` block showing which credentials the server needs and how they are injected
3. The `settings.json` entry to wire it in
4. One paragraph on what a developer would be able to ask Claude once this tool is available

---

**Exercise 3 — Write a tool that guards itself (stretch)**

Build a custom MCP server tool called `run_migration` that:
1. Accepts a migration file path
2. Checks that the file is in `alembic/versions/` (rejects anything outside)
3. Checks that the file has not been previously committed (using `git log`)
4. If both checks pass, runs `alembic upgrade <revision>`
5. Returns success or a structured error

This pattern — a tool that validates before acting — is safer than using a raw Bash allow-list, because the validation logic lives in code you control, not in a regex pattern in `settings.json`.

---

## Connect it back

[Day 6](day-06-mcp-servers-wiring-diagram.md) explained what MCP is and where it fits in the four-layer architecture. Today you went from understanding to operating: installing servers, debugging connections, and building custom ones. The protocol is now demystified — it is stdin and stdout and JSON, and the SDK handles the rest.

Tomorrow ([Day 7](day-07-token-economy.md)) is the token economy — the densest page in the course. MCP servers are relevant there too: each MCP server starts at session init, and the tools they provide become part of Claude's tool list. That is context budget. Day 7 shows you how to spend it wisely.

---

## Suggested readings

**Required if you have 15 extra minutes:**
- Model Context Protocol, "Building your first MCP server" — modelcontextprotocol.io/docs/quickstart/server. The official quickstart walks through the same pattern as this page, using a slightly different example (a weather tool). Read it after this page to see a second worked example and verify your mental model.

**If you want the deep version:**
- MCP Specification, "Transport" section — modelcontextprotocol.io/docs/concepts/transports. Covers stdio vs HTTP+SSE transport in full. SSE transport is used when you need a persistent server accessible over a network (not just locally) — relevant if you want to share one MCP server across a team rather than running it locally per developer.
- `@modelcontextprotocol/sdk` source — github.com/modelcontextprotocol/typescript-sdk. Reading the `McpServer` class and the `StdioServerTransport` source demystifies what the SDK is actually doing. It is less than 1,000 lines of TypeScript total.
- Model Context Protocol, "Resources" section — modelcontextprotocol.io/docs/concepts/resources. Resources are the second MCP primitive (after Tools). They expose read-only data sources — useful when you want Claude to be able to browse a set of documents or configuration values without you providing a specific query.

---

← [Day 6 — MCP Servers + Full Wiring Diagram](day-06-mcp-servers-wiring-diagram) &nbsp;|&nbsp; [Day 7 — The Token Economy →](day-07-token-economy)
