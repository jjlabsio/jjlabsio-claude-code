# JJLabsio Claude Code

## Installation

### Option 1: Install as Plugin (Recommended)

The easiest way to use this repo - install as a Claude Code plugin:

```bash
# Add this repo as a marketplace
/plugin marketplace add jjlabsio/jjlabsio-claude-code

# Install the plugin
/plugin install jj@jjlabsio-claude-code
```

Or add directly to your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "jjlabsio-claude-code": {
      "source": {
        "source": "github",
        "repo": "jjlabsio/jjlabsio-claude-code"
      }
    }
  },
  "enabledPlugins": {
    "jj@jjlabsio-claude-code": true
  }
}
```

This gives you instant access to all commands, agents, skills, and hooks.
