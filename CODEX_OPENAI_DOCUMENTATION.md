# OpenAI Codex CLI - Documentation

This document contains all documentation extracted from the OpenAI Codex CLI repository and documentation.

**Last Updated:** November 12, 2025

**Source:** https://github.com/openai/codex

**Documentation:** https://github.com/openai/codex

---

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Authentication](#authentication)
6. [Usage](#usage)
7. [Advanced Features](#advanced-features)
8. [AGENTS.md Files](#agentsmd-files)
9. [Sandbox and Security](#sandbox-and-security)
10. [MCP Integration](#mcp-integration)
11. [Custom Prompts](#custom-prompts)
12. [Slash Commands](#slash-commands)
13. [Non-Interactive Mode](#non-interactive-mode)
14. [FAQ](#faq)
15. [Contributing](#contributing)

---

## Introduction

Source: https://github.com/openai/codex

OpenAI Codex CLI is a lightweight coding agent that runs in your terminal. It provides ChatGPT-level reasoning plus the power to actually run code, manipulate files, and iterate – all under version control.

### Key Features

- **Zero setup** — bring your OpenAI API key and it just works!
- **Full auto-approval, while safe + secure** by running network-disabled and directory-sandboxed
- **Multimodal** — pass in screenshots or diagrams to implement features
- **Fully open-source** so you can see and contribute to how it develops

### Experimental Technology Disclaimer

Codex CLI is an experimental project under active development. It is not yet stable, may contain bugs, incomplete features, or undergo breaking changes. The community is encouraged to participate in its development by reporting bugs, requesting features, submitting pull requests, and providing feedback.

---

## Getting Started

Source: https://github.com/openai/codex/blob/main/docs/getting-started.md

### Quickstart

Install globally:

```bash
npm install -g @openai/codex
```

Set your OpenAI API key as an environment variable:

```bash
export OPENAI_API_KEY="your-api-key-here"
```

> **Note:** This command sets the key only for your current terminal session. To make it permanent, add the `export` line to your shell's configuration file (e.g., `~/.zshrc`).
>
> **Tip:** You can also place your API key into a `.env` file at the root of your project:
>
> ```env
> OPENAI_API_KEY=your-api-key-here
> ```
>
> The CLI will automatically load variables from `.env` (via `dotenv/config`).

Run interactively:

```bash
codex
```

Or, run with a prompt as input:

```bash
codex "explain this codebase to me"
```

Run in Full Auto mode:

```bash
codex --approval-mode full-auto "create the fanciest todo-list app"
```

### CLI Reference

| Command                              | Purpose                             | Example                              |
| ------------------------------------ | ----------------------------------- | ------------------------------------ |
| `codex`                              | Interactive REPL                    | `codex`                              |
| `codex "…"`                          | Initial prompt for interactive REPL | `codex "fix lint errors"`            |
| `codex -q "…"`                       | Non‑interactive "quiet mode"        | `codex -q --json "explain utils.ts"` |
| `codex completion <bash\|zsh\|fish>` | Print shell completion script       | `codex completion bash`              |

### Tips and Shortcuts

- Use `@` for file search
- Edit previous messages
- Work with multiple projects
- Use `codex exec` for non-interactive mode
- Use `codex resume` to resume sessions

---

## Installation

Source: https://github.com/openai/codex/blob/main/docs/install.md

### System Requirements

- Operating Systems: macOS, Linux, Windows (WSL2)
- Git: Required
- RAM: Minimum 4GB recommended

### Installation Methods

#### NPM (Recommended)

```bash
npm install -g @openai/codex
```

#### Homebrew (macOS)

```bash
brew install --cask codex
```

#### Build from Source

```bash
# Clone the repository
git clone https://github.com/openai/codex.git
cd codex/codex-rs

# Install Rust toolchain (if not already installed)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Build Codex
cargo build

# Run tests
cargo test
```

### Upgrading

#### macOS (Homebrew)

```bash
brew uninstall --formula codex
brew install --cask codex
```

#### NPM

```bash
npm update -g @openai/codex
```

---

## Configuration

Source: https://github.com/openai/codex/blob/main/docs/config.md

Codex can be configured using command-line flags, a generic `-c`/`--config` flag, and a `config.toml` file.

### Configuration File Location

The configuration file is typically located at `~/.codex/config.toml` or in your project root.

### Example Configuration

```toml
# Model selection
model = "gpt-5-codex"
review_model = "gpt-5-codex"
model_provider = "openai"

# Reasoning and verbosity
reasoning_effort = "medium"
verbosity = "normal"

# Sandbox and approval policies
sandbox_mode = "workspace-write"
approval_policy = "on-request"

# MCP servers
[mcp_servers.firecrawl]
command = "npx"
args = ["-y", "@firecrawl/mcp-server-firecrawl"]

# Model providers
[model_providers.ollama]
name = "Ollama"
base_url = "http://localhost:11434/v1"

# Observability
[observability]
enabled = true
telemetry = true
```

### Configuration Options

#### Model Selection

- `model`: Primary AI model to use
- `review_model`: Model for code review
- `model_provider`: Provider name (openai, azure, ollama, etc.)

#### Sandbox Modes

- `read-only`: Codex can only read files
- `workspace-write`: Codex can write to workspace files
- `danger-full-access`: Full access (use with caution)

#### Approval Policies

- `on-request`: Ask for approval before actions
- `never`: Never ask for approval (full auto)
- `always`: Always require approval

---

## Authentication

Source: https://github.com/openai/codex/blob/main/docs/authentication.md

### OpenAI API Key

The simplest way to authenticate is using an OpenAI API key:

```bash
export OPENAI_API_KEY="your-api-key-here"
```

Or use the login command:

```bash
printenv OPENAI_API_KEY | codex login --with-api-key
```

### ChatGPT Login

You can migrate from API key to ChatGPT login:

```bash
codex login
```

This will:
1. Start a server on localhost:1455
2. Open a web browser for authentication
3. Complete the login flow

### Headless Machine Authentication

For headless machines, you can:

1. Authenticate locally and copy credentials:

```bash
# On local machine
scp ~/.codex/auth.json user@remote-host:~/.codex/
```

2. Use port forwarding:

```bash
ssh -L 1455:localhost:1455 <user>@<remote-host>
```

### Zero Data Retention (ZDR)

Codex CLI natively supports OpenAI organizations with Zero Data Retention (ZDR) enabled. Organizations with ZDR can use Codex CLI without additional configuration.

---

## Usage

### Interactive Mode

Start an interactive session:

```bash
codex
```

Or with an initial prompt:

```bash
codex "Refactor the Dashboard component to React Hooks"
```

### Working with Multiple Projects

Use the `--cd` flag to change directory:

```bash
codex --cd apps/frontend "Fix the login bug"
```

Add additional directories:

```bash
codex --cd apps/frontend --add-dir ../backend --add-dir ../shared
```

### Image Input

Codex supports multimodal input. You can pass images:

```bash
codex --image screenshot.png "Implement this design"
```

---

## Advanced Features

Source: https://github.com/openai/codex/blob/main/docs/advanced.md

### Tracing and Verbose Logging

Enable verbose logging:

```bash
RUST_LOG=debug codex "your prompt"
```

Monitor logs:

```bash
tail -F ~/.codex/log/codex-tui.log
```

### MCP Server Setup

Run Codex as an MCP server:

```bash
npx @modelcontextprotocol/inspector codex mcp-server
```

### Custom Configuration

Tweak defaults in `config.toml`:

```toml
[profiles.full_auto]
approval_policy = "on-request"
sandbox_mode = "workspace-write"
```

---

## AGENTS.md Files

Source: https://github.com/openai/codex/blob/main/docs/agents_md.md

Codex uses AGENTS.md files to gather guidance before assisting users. The discovery process involves:

1. Looking for global instructions in `~/.codex/AGENTS.md`
2. Searching for project-specific instructions in the repository
3. Combining files with precedence given to more specific files

### File Discovery Order

1. `~/.codex/AGENTS.override.md` (highest priority)
2. `~/.codex/AGENTS.md`
3. Repository root `AGENTS.md`
4. Current directory `AGENTS.md`
5. Fallback filenames (configurable)

### Configuration

```toml
project_doc_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
project_doc_max_bytes = 100000
```

---

## Sandbox and Security

Source: https://github.com/openai/codex/blob/main/docs/sandbox.md

### Sandbox Modes

| Mode                      | What the agent may do without asking                                                               | Still requires approval                                                                         |
| ------------------------- | -------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Suggest** <br>(default) | • Read any file in the repo                                                                        | • **All** file writes/patches <br>• **Any** arbitrary shell commands (aside from reading files) |
| **Auto Edit**             | • Read **and** apply‑patch writes to files                                                         | • **All** shell commands                                                                        |
| **Full Auto**             | • Read/write files <br>• Execute shell commands (network disabled, writes limited to your workdir) | –                                                                                               |

### Platform Sandboxing

Codex uses platform-specific sandboxing:

- **macOS**: Apple Seatbelt
- **Linux**: Landlock and seccomp
- **Windows**: WSL2 with Linux sandboxing

### Configuration Examples

```toml
[profiles.full_auto]
approval_policy = "on-request"
sandbox_mode = "workspace-write"
```

Command line:

```bash
codex --sandbox read-only --ask-for-approval on-request
```

---

## MCP Integration

Source: https://github.com/openai/codex/blob/main/docs/advanced.md

Codex supports Model Context Protocol (MCP) servers for extended functionality.

### Quickstart

```bash
npx @modelcontextprotocol/inspector codex mcp-server
```

### Configuration

Add MCP servers to `config.toml`:

```toml
[mcp_servers.firecrawl]
command = "npx"
args = ["-y", "@firecrawl/mcp-server-firecrawl"]
```

---

## Custom Prompts

Source: https://github.com/openai/codex/blob/main/docs/prompts.md

Custom prompts allow you to turn repeatable instructions into reusable slash commands.

### Creating Prompts

Store prompts as Markdown files in `$CODEX_HOME/prompts/`:

```markdown
---
description: Create feature branch, commit and open draft PR.
---

Create a branch named `tibo/<feature_name>`, commit the changes, and open a draft PR.
```

### Using Prompts

1. Start a new Codex session
2. Type `/` to open the slash popup
3. Select the prompt with required arguments

### Placeholders

Use placeholders in prompts:

```markdown
---
description: Review code changes
arguments:
  - name: file
    description: File to review
---

Review the changes in `{{file}}` and provide feedback.
```

---

## Slash Commands

Source: https://github.com/openai/codex/blob/main/docs/slash_commands.md

Slash commands are special commands that start with `/` and control behavior during interactive sessions.

### Built-in Commands

| Command      | Purpose                                          |
| ------------ | ------------------------------------------------ |
| `/model`     | Choose what model and reasoning effort to use    |
| `/approvals` | Choose what Codex can do without approval        |
| `/review`    | Review changes before applying                   |
| `/mcp`       | Access MCP tools                                 |
| `/context`   | Manage context limit                              |
| `/git`       | View git diff                                    |

---

## Non-Interactive Mode

Source: https://github.com/openai/codex/blob/main/docs/exec.md

Use `codex exec` for non-interactive automation:

```bash
codex exec "count the total number of lines of code in this project"
```

### Full Auto Mode

```bash
codex exec --full-auto "Fix merge conflict"
```

### JSON Output

Stream events as JSON Lines:

```bash
codex exec --json "analyze code quality"
```

### Structured Output

Specify a JSON schema:

```bash
codex exec --output-schema schema.json "extract user data"
```

### Resuming Sessions

Resume non-interactive sessions:

```bash
codex resume <session-id>
```

---

## FAQ

Source: https://github.com/openai/codex/blob/main/docs/faq.md

### What is Codex?

Codex CLI is a lightweight coding agent that runs in your terminal, providing ChatGPT-level reasoning plus the power to run code, manipulate files, and iterate.

### What models are supported?

Codex primarily supports GPT-5 Codex, but can be configured to use other models through model providers.

### How do I prevent Codex from editing files?

Run Codex with the `--sandbox read-only` flag:

```bash
codex --sandbox read-only
```

### How do I upgrade Codex?

#### macOS (Homebrew)

```bash
brew uninstall --formula codex
brew install --cask codex
```

#### NPM

```bash
npm update -g @openai/codex
```

### How do I connect to MCP servers?

Add MCP server configuration to your `config.toml`:

```toml
[mcp_servers.firecrawl]
command = "npx"
args = ["-y", "@firecrawl/mcp-server-firecrawl"]
```

### What platforms are supported?

- macOS
- Linux
- Windows (WSL2)

### How do I use Codex in CI/CD?

Use `codex exec` in non-interactive mode:

```yaml
- name: Update changelog via Codex
  run: |
    npm install -g @openai/codex
    export OPENAI_API_KEY="${{ secrets.OPENAI_KEY }}"
    codex exec "update CHANGELOG.md"
```

---

## Contributing

Source: https://github.com/openai/codex

### How to Contribute

1. **Report bugs** - File issues on GitHub
2. **Request features** - Open feature requests
3. **Submit pull requests** - Contribute code improvements
4. **Provide feedback** - Share your experience

### Development Workflow

1. Fork the repository
2. Create a topic branch
3. Make your changes
4. Add tests
5. Submit a pull request

### Contributor License Agreement

Contributors must sign the CLA by commenting "I have read the CLA Document and I hereby sign the CLA" on their pull request.

### Code Quality

- Write tests for new features
- Follow existing code style
- Update documentation
- Ensure all tests pass

---

## Additional Resources

- **GitHub Repository:** https://github.com/openai/codex
- **Documentation:** https://github.com/openai/codex/tree/main/docs
- **License:** Apache-2.0
- **Open Source Fund:** https://openai.com/form/codex-open-source-fund/

---

## API Reference Summary

### Configuration Options

- Model selection and providers
- Sandbox modes and approval policies
- MCP server integration
- Observability and telemetry
- Profiles and overrides

### Commands

- `codex` - Interactive REPL
- `codex exec` - Non-interactive mode
- `codex login` - Authentication
- `codex completion` - Shell completions
- `codex resume` - Resume sessions

### File Locations

- `~/.codex/config.toml` - Configuration file
- `~/.codex/AGENTS.md` - Global instructions
- `~/.codex/prompts/` - Custom prompts directory
- `~/.codex/log/` - Log files

---

*End of Documentation*








