# OpenAI ChatGPT OAuth Plugin for opencode

[![npm version](https://img.shields.io/npm/v/opencode-openai-codex-auth.svg)](https://www.npmjs.com/package/opencode-openai-codex-auth)
[![npm downloads](https://img.shields.io/npm/dm/opencode-openai-codex-auth.svg)](https://www.npmjs.com/package/opencode-openai-codex-auth)

This plugin enables opencode to use OpenAI's Codex backend via ChatGPT Plus/Pro OAuth authentication, allowing you to use your ChatGPT subscription instead of OpenAI Platform API credits.

> **Found this useful?** 
Follow me on [X @nummanthinks](https://x.com/nummanthinks) for future updates and more projects!

## Features

- ✅ **ChatGPT Plus/Pro OAuth authentication** - Use your existing subscription
- ✅ **9 pre-configured model variants** - Low/Medium/High reasoning for both gpt-5 and gpt-5-codex
- ✅ **Zero external dependencies** - Lightweight with only @openauthjs/openauth
- ✅ **Auto-refreshing tokens** - Handles token expiration automatically
- ✅ **Smart auto-updating Codex instructions** - Tracks latest stable release with ETag caching
- ✅ **Full tool support** - write, edit, bash, grep, glob, and more
- ✅ **CODEX_MODE** - Codex-OpenCode bridge prompt with Task tool & MCP awareness (enabled by default)
- ✅ **Automatic tool remapping** - Codex tools → opencode tools
- ✅ **Configurable reasoning** - Control effort, summary verbosity, and text output
- ✅ **Type-safe & tested** - Strict TypeScript with 129 comprehensive tests
- ✅ **Modular architecture** - Easy to maintain and extend

## Installation

### Quick Start

**No npm install needed!** opencode automatically installs plugins when you add them to your config.

#### Recommended: Full Configuration (Codex CLI Experience)

For the complete experience with all reasoning variants matching the official Codex CLI:

1. **Copy the full configuration** from [`config/full-opencode.json`](./config/full-opencode.json) to your opencode config file:
```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    "opencode-openai-codex-auth"
  ],
  "provider": {
    "openai": {
      "options": {
        "reasoningEffort": "medium",
        "reasoningSummary": "auto",
        "textVerbosity": "medium",
        "include": [
          "reasoning.encrypted_content"
        ]
      },
      "models": {
        "GPT 5 Codex Low (ChatGPT Subscription)": {
          "id": "gpt-5-codex",
          "options": {
            "reasoningEffort": "low",
            "reasoningSummary": "auto",
            "textVerbosity": "medium"
          }
        },
        "GPT 5 Codex Medium (ChatGPT Subscription)": {
          "id": "gpt-5-codex",
          "options": {
            "reasoningEffort": "medium",
            "reasoningSummary": "auto",
            "textVerbosity": "medium"
          }
        },
        "GPT 5 Codex High (ChatGPT Subscription)": {
          "id": "gpt-5-codex",
          "options": {
            "reasoningEffort": "high",
            "reasoningSummary": "detailed",
            "textVerbosity": "medium"
          }
        },
        "GPT 5 Minimal (ChatGPT Subscription)": {
          "id": "gpt-5",
          "options": {
            "reasoningEffort": "minimal",
            "reasoningSummary": "auto",
            "textVerbosity": "low"
          }
        },
        "GPT 5 Low (ChatGPT Subscription)": {
          "id": "gpt-5",
          "options": {
            "reasoningEffort": "low",
            "reasoningSummary": "auto",
            "textVerbosity": "low"
          }
        },
        "GPT 5 Medium (ChatGPT Subscription)": {
          "id": "gpt-5",
          "options": {
            "reasoningEffort": "medium",
            "reasoningSummary": "auto",
            "textVerbosity": "medium"
          }
        },
        "GPT 5 High (ChatGPT Subscription)": {
          "id": "gpt-5",
          "options": {
            "reasoningEffort": "high",
            "reasoningSummary": "detailed",
            "textVerbosity": "high"
          }
        },
        "gpt-5-mini": {
          "options": {
            "reasoningEffort": "low",
            "reasoningSummary": "auto",
            "textVerbosity": "low"
          }
        },
        "gpt-5-nano": {
          "options": {
            "reasoningEffort": "minimal",
            "reasoningSummary": "auto",
            "textVerbosity": "low"
          }
        }
      }
    }
  }
}
```

   **Global config**: `~/.config/opencode/opencode.json`
   **Project config**: `<project>/.opencode.json`

   This gives you 9 model variants with different reasoning levels:
   - **GPT 5 Codex** (Low/Medium/High)
   - **GPT 5** (Minimal/Low/Medium/High)
   - **gpt-5-mini** and **gpt-5-nano** (these are to support the default opencode model selector)

   All appear in the opencode model selector with optimal settings for each reasoning level.

   > **Optional**: You can remove `(ChatGPT Subscription)` from model names if you prefer cleaner labels in the model selector.

#### Alternative: Minimal Configuration

For a simpler setup (uses plugin defaults: medium reasoning, auto summaries):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    "opencode-openai-codex-auth"
  ],
  "model": "openai/gpt-5-codex"
}
```

**Note**: This gives you basic functionality but you won't see the different reasoning variants in the model selector.

2. **That's it!** opencode will auto-install the plugin on first run.

   > **Note on Updates**: opencode does NOT automatically update plugins. To force a fresh install of the latest version:
   > ```bash
   > # Remove cached plugin entry and folder
   > sed -i.bak '/"opencode-openai-codex-auth"/d' ~/.cache/opencode/package.json && rm -rf ~/.cache/opencode/node_modules/opencode-openai-codex-auth
   > ```
   > Then run `opencode` - it will reinstall the latest version.
   >
   > Check [releases](https://github.com/numman-ali/opencode-openai-codex-auth/releases) for the latest version.

> **New to opencode?** Learn more:
> - [Configuration Guide](https://opencode.ai/docs/config/)
> - [Plugin Documentation](https://opencode.ai/docs/plugins/)

### Alternative: Local Development

For testing or development, you can use a local file path:

```json
{
  "plugin": [
    "file:///absolute/path/to/opencode-openai-codex-auth"
  ]
}
```

## Authentication

Login with ChatGPT OAuth:

```bash
opencode auth login
```

Select "OpenAI" and choose:
- **"ChatGPT Plus/Pro (Codex Subscription)"** - Opens browser automatically for OAuth flow

> **Important**: Make sure the official Codex CLI is not running during first login, as both use port 1455 for OAuth callback. After initial authentication, this won't be an issue.

## Usage

If using the full configuration, select from the model picker in opencode, or specify via command line:

```bash
# Use different reasoning levels for gpt-5-codex
opencode run "simple task" --model="openai/GPT 5 Codex Low (ChatGPT Subscription)"
opencode run "complex task" --model="openai/GPT 5 Codex High (ChatGPT Subscription)"

# Use different reasoning levels for gpt-5
opencode run "quick question" --model="openai/GPT 5 Minimal (ChatGPT Subscription)"
opencode run "deep analysis" --model="openai/GPT 5 High (ChatGPT Subscription)"

# Or with minimal config (uses defaults)
opencode run "create a hello world file" --model=openai/gpt-5-codex
opencode run "solve this complex problem" --model=openai/gpt-5
```

### Available Model Variants (Full Config)

When using [`config/full-opencode.json`](./config/full-opencode.json), you get these pre-configured variants:

| Model Variant | Reasoning Effort | Best For |
|---------------|-----------------|----------|
| **GPT 5 Codex Low** | Low | Fast code generation |
| **GPT 5 Codex Medium** | Medium | Balanced code tasks |
| **GPT 5 Codex High** | High | Complex code & tools |
| **GPT 5 Minimal** | Minimal | Quick answers, simple tasks |
| **GPT 5 Low** | Low | Faster responses with light reasoning |
| **GPT 5 Medium** | Medium | Balanced general-purpose tasks |
| **GPT 5 High** | High | Deep reasoning, complex problems |
| **gpt-5-mini** | Low | Lightweight tasks |
| **gpt-5-nano** | Minimal | Maximum speed |

All accessed via your ChatGPT Plus/Pro subscription.

### Using Model Variants in Custom Commands & Agents

When using model aliases in **custom command frontmatter** or **agent configurations**, you **must include the provider prefix** (`openai/`):

#### ✅ Correct - Custom Command Example

```yaml
---
description: Create a git commit message
agent: build
model: openai/GPT 5 Codex Low (ChatGPT Subscription)
---

Create a commit message for the staged changes
```

#### ❌ Incorrect - Missing Provider Prefix

```yaml
---
description: Create a git commit message
agent: build
model: GPT 5 Codex Low (ChatGPT Subscription)  # ❌ Will fail - missing "openai/"
---
```

#### ✅ Correct - Agent Configuration Example

```json
{
  "agent": {
    "commit": {
      "model": "openai/GPT 5 Codex Low (ChatGPT Subscription)",
      "prompt": "You are a git commit message expert..."
    }
  }
}
```

**Why?** The `--model` CLI flag, command frontmatter, and agent configs all use the same model resolution format: `provider/model`. Model aliases defined in your config are indexed under the provider namespace.

**Alternative**: Instead of specifying the model in each command, you can:
1. Set a default model in your agent configuration
2. Use the agent's model setting instead of specifying per-command
3. Rely on the global default model in your opencode config

### Plugin Defaults

When no configuration is specified, the plugin uses these defaults for all GPT-5 models:

```json
{
  "reasoningEffort": "medium",
  "reasoningSummary": "auto",
  "textVerbosity": "medium"
}
```

- **`reasoningEffort: "medium"`** - Balanced computational effort for reasoning
- **`reasoningSummary: "auto"`** - Automatically adapts summary verbosity
- **`textVerbosity: "medium"`** - Balanced output length

These defaults match the official Codex CLI behavior and can be customized (see Configuration below).

## Configuration

### Recommended: Use Pre-Configured File

The easiest way to get started is to use [`config/full-opencode.json`](./config/full-opencode.json), which provides:
- 9 pre-configured model variants matching Codex CLI presets
- Optimal settings for each reasoning level
- All variants visible in the opencode model selector

See [Installation](#installation) for setup instructions.

### Custom Configuration

If you want to customize settings yourself, you can configure options at provider or model level.

#### Available Settings

⚠️ **Important**: The two base models have different supported values.

| Setting | GPT-5 Values | GPT-5-Codex Values | Plugin Default |
|---------|-------------|-------------------|----------------|
| `reasoningEffort` | `minimal`, `low`, `medium`, `high` | `low`, `medium`, `high` | `medium` |
| `reasoningSummary` | `auto`, `detailed` | `auto`, `detailed` | `auto` |
| `textVerbosity` | `low`, `medium`, `high` | `medium` only | `medium` |
| `include` | Array of strings | Array of strings | `["reasoning.encrypted_content"]` |

> **Note**: `minimal` effort is auto-normalized to `low` for gpt-5-codex (not supported by the API).

#### Global Configuration Example

Apply settings to all models:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-openai-codex-auth"],
  "model": "openai/gpt-5-codex",
  "provider": {
    "openai": {
      "options": {
        "reasoningEffort": "high",
        "reasoningSummary": "detailed"
      }
    }
  }
}
```

#### Custom Model Variants Example

Create your own named variants in the model selector:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-openai-codex-auth"],
  "provider": {
    "openai": {
      "models": {
        "My Fast Codex": {
          "id": "gpt-5-codex",
          "options": {
            "reasoningEffort": "low"
          }
        },
        "My Smart GPT-5": {
          "id": "gpt-5",
          "options": {
            "reasoningEffort": "high",
            "textVerbosity": "high"
          }
        }
      }
    }
  }
}
```

The `id` field determines which base model is used (`gpt-5-codex` or `gpt-5`), and the name is what appears in the model selector.

### Configuration Files

This repository includes ready-to-use configuration examples:

- **[`config/full-opencode.json`](./config/full-opencode.json)** - Complete configuration with 9 model variants (recommended)
- **[`config/minimal-opencode.json`](./config/minimal-opencode.json)** - Minimal configuration for basic usage
- **[`config/README.md`](./config/README.md)** - Detailed configuration documentation

Copy the appropriate file to your opencode configuration location and customize as needed.

### Plugin Configuration

The plugin supports configuration via `~/.opencode/openai-codex-auth-config.json`:

```json
{
  "codexMode": true
}
```

#### CODEX_MODE Setting

- **`codexMode: true`** (default) - Uses Codex-OpenCode bridge prompt for better Codex CLI parity
- **`codexMode: false`** - Uses tool remap message (legacy mode)

**Priority order**: `CODEX_MODE` environment variable > config file > default (true)

**Examples**:
```bash
# Override config file with environment variable
CODEX_MODE=0 opencode run "task"  # Disable CODEX_MODE temporarily
CODEX_MODE=1 opencode run "task"  # Enable CODEX_MODE temporarily
```

> **Note**: CODEX_MODE is enabled by default for optimal Codex CLI compatibility. Only disable if you experience issues with the bridge prompt.

## How It Works

The plugin implements a 7-step fetch flow in TypeScript:

1. **Token Management**: Checks and refreshes OAuth tokens if needed
2. **URL Rewriting**: Transforms requests to `https://chatgpt.com/backend-api/codex/responses`
3. **Request Transformation**:
   - Model normalization (`gpt-5-codex` variants → `gpt-5-codex`, `gpt-5` variants → `gpt-5`)
   - Injects Codex instructions from latest [openai/codex](https://github.com/openai/codex) release
   - Applies reasoning configuration (effort, summary, verbosity)
   - Adds Codex-OpenCode bridge prompt with Task tool & MCP awareness (CODEX_MODE=true, default) or tool remap message (CODEX_MODE=false)
   - Filters OpenCode system prompts when in CODEX_MODE (verified against cached OpenCode prompt for accuracy)
   - Filters conversation history (removes stored IDs for stateless operation)
4. **Headers**: Adds OAuth token and ChatGPT account ID headers
5. **Request Execution**: Sends to Codex backend API
6. **Response Logging**: Optional debug logging (when `ENABLE_PLUGIN_REQUEST_LOGGING=1`)
7. **Response Handling**: Converts SSE to JSON or returns error details

### Key Features

- **Modular Design**: 10 focused helper functions, each < 40 lines
- **Type-Safe**: Strict TypeScript with comprehensive type definitions
- **Tested**: 129 tests covering all functionality including CODEX_MODE
- **Zero Dependencies**: Only uses @openauthjs/openauth
- **Codex Instructions**: ETag-cached from GitHub, auto-updates on new releases
- **CODEX_MODE**: Configurable bridge prompt for Codex CLI parity with Task tool & MCP awareness (enabled by default)
- **Stateless Operation**: Uses `store: false` with encrypted reasoning content for multi-turn conversations

## Limitations

- **ChatGPT Plus/Pro required**: Must have an active ChatGPT Plus or Pro subscription

## Troubleshooting

### Authentication Issues

- Ensure you have an active ChatGPT Plus or Pro subscription
- Try re-logging in with `opencode auth login`
- Check browser console during OAuth flow if auto-login fails

### Tool Execution Issues

- Verify plugin is loaded in `opencode.json`
- Check that model is set to `openai/gpt-5-codex`
- Check `~/.opencode/cache/` for cached instructions and OpenCode prompts (auto-downloads from GitHub)

### Request Errors

- **401 Unauthorized**: Token expired, run `opencode auth login` again
- **400 Bad Request**: Check console output for specific error details
- **403 Forbidden**: Subscription may be expired or invalid

### Model Not Found in Custom Commands/Agents

If you get a "model not found" error when using custom commands or agents with model aliases:

**Problem**: Model aliases in command frontmatter or agent configs require the provider prefix.

```yaml
# ❌ This will fail
model: GPT 5 Codex Low (ChatGPT Subscription)

# ✅ This works
model: openai/GPT 5 Codex Low (ChatGPT Subscription)
```

**Why?** The `--model` CLI flag, custom commands, and agent configurations all use the `provider/model` format. Bare aliases (without `openai/`) are not automatically resolved to the provider namespace.

**Solution**: Always include the provider prefix when using model aliases in:
- Custom command frontmatter (`model: openai/...`)
- Agent configurations (`"model": "openai/..."`)
- CLI `--model` flag (`--model=openai/...`)

See [Using Model Variants in Custom Commands & Agents](#using-model-variants-in-custom-commands--agents) for detailed examples.

### Plugin Issues

If you encounter issues with the latest version, you can pin to a specific stable release:

```json
{
  "plugin": [
    "opencode-openai-codex-auth@1.0.2"
  ]
}
```

Check [releases](https://github.com/numman-ali/opencode-openai-codex-auth/releases) for available versions and their release notes.

## Debugging

### Enable Request Logging

For debugging purposes, you can enable detailed request logging to inspect what's being sent to the Codex API:

```bash
ENABLE_PLUGIN_REQUEST_LOGGING=1 opencode run "your prompt"
```

Logs are saved to `~/.opencode/logs/codex-plugin/` with detailed information about:
- Original request from opencode
- Transformed request sent to Codex
- Response status and headers
- Error details (if any)

Each request generates 3-4 JSON files:
- `request-N-before-transform.json` - Original request
- `request-N-after-transform.json` - Transformed request
- `request-N-response.json` - Response metadata
- `request-N-error-response.json` - Error details (if failed)

**Note**: Logging is disabled by default to avoid cluttering your disk. Only enable it when debugging issues.

## Project Structure

```
opencode-openai-codex-auth/
├── index.ts                     # Main plugin entry point
├── lib/
│   ├── auth/                    # OAuth authentication modules
│   │   ├── auth.ts             # OAuth flow, PKCE, token exchange
│   │   ├── server.ts           # Local OAuth callback server
│   │   └── browser.ts          # Platform-specific browser opening
│   ├── prompts/                 # Prompt management modules
│   │   ├── codex.ts            # Codex instructions fetching/caching
│   │   ├── opencode-codex.ts   # OpenCode prompt verification cache
│   │   └── codex-opencode-bridge.ts # Bridge prompt with Task/MCP awareness
│   ├── request/                 # Request handling modules
│   │   ├── request-transformer.ts # Request body transformations
│   │   ├── response-handler.ts    # SSE to JSON conversion
│   │   └── fetch-helpers.ts       # 10 focused helper functions
│   ├── config.ts                # Plugin configuration & CODEX_MODE
│   ├── logger.ts                # Request logging (debug mode)
│   ├── constants.ts             # All magic values and URLs
│   └── types.ts                 # TypeScript type definitions
├── config/
│   ├── full-opencode.json       # Complete config with all variants
│   ├── minimal-opencode.json    # Minimal config example
│   └── README.md                # Configuration documentation
├── test/                         # Comprehensive test suite (129 tests)
├── AGENTS.md                     # Agent coding guidance
├── package.json
├── README.md
└── LICENSE
```

### Module Overview

- **index.ts**: Main plugin export and fetch() orchestration (7-step flow)
- **lib/auth/auth.ts**: OAuth flow, PKCE, token exchange, JWT decoding
- **lib/auth/server.ts**: Local HTTP server for OAuth callback handling
- **lib/auth/browser.ts**: Platform detection and browser opening
- **lib/prompts/codex.ts**: Fetches/caches Codex instructions from GitHub, tool remapping
- **lib/prompts/opencode-codex.ts**: Fetches/caches OpenCode system prompts for verification
- **lib/prompts/codex-opencode-bridge.ts**: Bridge prompt with Task tool & MCP awareness
- **lib/request/request-transformer.ts**: Request transformations (model normalization, reasoning config, prompt filtering)
- **lib/request/response-handler.ts**: Response handling (SSE to JSON conversion)
- **lib/request/fetch-helpers.ts**: 10 focused helper functions for main fetch flow
- **lib/config.ts**: Plugin configuration loading and CODEX_MODE determination
- **lib/logger.ts**: Debug logging (controlled by environment variable)
- **lib/constants.ts**: Centralized constants, URLs, error messages
- **lib/types.ts**: TypeScript type definitions

## Credits

Based on research and working implementations from:
- [ben-vargas/ai-sdk-provider-chatgpt-oauth](https://github.com/ben-vargas/ai-sdk-provider-chatgpt-oauth)
- [ben-vargas/ai-opencode-chatgpt-auth](https://github.com/ben-vargas/ai-opencode-chatgpt-auth)
- [openai/codex](https://github.com/openai/codex) OAuth flow
- [sst/opencode](https://github.com/sst/opencode)

## License

MIT
