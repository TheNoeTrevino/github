# GitHub Actions for Neovim Plugins

Reusable GitHub Actions and workflows for Neovim plugin development.

## Neovim Action

Composite action to install Neovim with caching and optional mini.nvim.

### Usage

```yaml
- uses: thenoetrevino/github/neovim@main
  with:
    version: "stable"      # optional: stable, nightly, or specific version
    mini-nvim: "true"      # optional: install mini.nvim for doc generation
```

## Reusable CI Workflow

Complete CI workflow for Neovim plugins with:
- StyLua formatting check
- Debug message detection (`dd()` calls)
- Test execution (via plenary or custom script)
- Documentation generation with mini.doc
- Release automation with release-please

### Usage

In your plugin's `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

permissions:
  contents: write
  pull-requests: write

jobs:
  ci:
    uses: thenoetrevino/github/.github/workflows/nvim-plugins-ci.yml@main
    secrets: inherit
    with:
      plugin: your-plugin-name
      repo: yourusername/your-plugin
      docs: true      # optional: enable minidoc generation (default: true)
      release: true   # optional: enable release-please (default: true)
```

### Requirements

For documentation generation, your plugin needs:
- `scripts/minidoc.lua` - Script that runs mini.doc generation

Example `scripts/minidoc.lua`:
```lua
local MiniDoc = require("mini.doc")
_G.MiniDoc = MiniDoc  -- Required for @eval directives

local files = {
    "lua/your-plugin/init.lua",
    "lua/your-plugin/config.lua",
}

MiniDoc.generate(files, "doc/your-plugin.txt")
```

For tests, either:
- `scripts/test.lua` - Custom test script
- `tests/` directory with plenary tests and `tests/minimal_init.lua`
