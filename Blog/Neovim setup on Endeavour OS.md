After install neovim on Windows 11. I want to try it on Linux. such as Ubuntu or fedora. But this time I choose a different distribution, Endeavour OS.

My desktop environmental is xfce4. Choose it just because that logo is a mouse.

And my configuration is base on [https://dev.to/craftzdog/my-neovim-setup-for-react-typescript-tailwind-css-etc-58fb](https://dev.to/craftzdog/my-neovim-setup-for-react-typescript-tailwind-css-etc-58fb) .

## Prepare

Before configure the neovim. We need do some preparation：

- Fonts: [https://www.nerdfonts.com/font-downloads](https://www.nerdfonts.com/font-downloads)
- Shell: zsh, https://github.com/ohmyzsh/ohmyzsh

## Directory structure

The configuration directory is base on XDG:

```powershell
CONFIG DIRECTORY (DEFAULT) ~
                  *$XDG_CONFIG_HOME*            Nvim: stdpath("config")
    Unix:         ~/.config                   ~/.config/nvim
    Windows:      ~/AppData/Local             ~/AppData/Local/nvim

DATA DIRECTORY (DEFAULT) ~
                  *$XDG_DATA_HOME*              Nvim: stdpath("data")
    Unix:         ~/.local/share              ~/.local/share/nvim
    Windows:      ~/AppData/Local             ~/AppData/Local/nvim-data
```

Unlike the Windows, on Linux it’s just in user home folder.

## Plugin manager

Before install plugins, we need a manager for manage plugins. on Unix, Linux: 

```bash
git clone --depth 1 https://github.com/wbthomason/packer.nvim\
 ~/.local/share/nvim/site/pack/packer/start/packer.nvim
```

And then, create configuration file in `~/.config/nvim/lua/plugins.lua` :

```lua
local status, packer = pcall(require, "packer")
if (not status) then
  print("Packer is not installed")
  return
end

vim.cmd [[packadd packer.nvim]]

packer.startup(function(use)
  use 'wbthomason/packer.nvim'
  -- Your plugins go here
end)
```

Don’t forget the `init.lua` .It’s configure entry file. We need require all other config in `init.lua` :

```lua
require('plugins')
```

If you want to use isolate module to avoid name conflict, you can create a folder in `lua` folder:

```bash
.
├── init.lua
└── lua
    └── xfy
        └── plugins.lua
```

The, require plugins in `init.lua` like: 

```lua
require('xfy.plugins')
```

After that, it can be install plugins with command in neovim:

```lua
:PackerInstall
```

## Base config files

In `/home/xfy/.config/nvim/lua/xfy` I create some base files. Them will be required in `init.lua` .

### base.lua

This one include some vim base settings. And enable `Dracule` theme in here.

```lua
vim.cmd("autocmd!")

vim.scriptencoding = 'utf-8'
vim.opt.encoding = 'utf-8'
vim.opt.fileencoding = 'utf-8'

vim.wo.number = true

vim.opt.title = true
vim.opt.autoindent = true
vim.opt.smartindent = true
vim.opt.hlsearch = true
vim.opt.backup = false
vim.opt.showcmd = true
vim.opt.cmdheight = 1
vim.opt.laststatus = 2
vim.opt.expandtab = true
vim.opt.scrolloff = 10
vim.opt.shell = 'fish'
vim.opt.backupskip = { '/tmp/*', '/private/tmp/*' }
vim.opt.inccommand = 'split'
vim.opt.ignorecase = true -- Case insensitive searching UNLESS /C or capital in search
vim.opt.smarttab = true
vim.opt.breakindent = true
vim.opt.shiftwidth = 2
vim.opt.tabstop = 2
vim.opt.wrap = false -- No Wrap lines
vim.opt.backspace = { 'start', 'eol', 'indent' }
vim.opt.path:append { '**' } -- Finding files - Search down into subfolders
vim.opt.wildignore:append { '*/node_modules/*' }

-- Undercurl
vim.cmd([[let &t_Cs = "\e[4:3m"]])
vim.cmd([[let &t_Ce = "\e[4:0m"]])

-- Turn off paste mode when leaving insert
vim.api.nvim_create_autocmd("InsertLeave", {
  pattern = '*',
  command = "set nopaste"
})

-- Add asterisks in block comments
vim.opt.formatoptions:append { 'r' }

-- Use color theme
vim.cmd[[colorscheme dracula]]
```

### highlight.lua

A little customization for looks:

```lua
vim.opt.cursorline = true
vim.opt.termguicolors = true
vim.opt.winblend = 0
vim.opt.wildoptions = 'pum'
vim.opt.pumblend = 5
vim.opt.background = 'dark'
```

## Plugins

### Color scheme: Dracula

Dracula https://github.com/Mofiqul/dracula.nvim is a good color scheme on a lot of edits.

Add blow in the `plugins.lua` . And run `PackerInstall` command to install it. You don’t need run `PackerInstall` after each time when you add new plugin into the file. It’s can be install all of them after file saved.

```lua
use 'Mofiqul/dracula.nvim' -- Color scheme
```

### lualine

lualine.nvim https://github.com/nvim-lualine/lualine.nvim is a grate status line for neovim. It’s also has `Dracula` theme.

```lua
lualine.setup {
  options = {
    icons_enabled = true,
    theme = 'dracula-nvim',
    section_separators = { left = '', right = '' },
    component_separators = { left = '', right = '' },
    disabled_filetypes = {}
  },
}
```

It’s looks already good now

![Untitled](Neovim%20setup%20on%20Endeavour%20OS%205c66e6d6d4aa466bb6b6a82eea0ccaff/Untitled.png)

### LSP

For the LSP, I installed a lot of plugins.

```lua
use 'onsails/lspkind-nvim' -- vscode-like pictograms                                          
use 'hrsh7th/cmp-buffer' -- nvim-cmp source for buffer words
use 'hrsh7th/cmp-nvim-lsp' -- nvim-cmp source for neovim's built-in LSP
use 'hrsh7th/nvim-cmp' -- Completion
use 'neovim/nvim-lspconfig' -- LSP
```

Of course don’t forget install language server itself:

```bash
yay -S lua-language-server
npm i -g typescript-language-server
```

And a little setup in `.config/nvim/plugin` :

```lua
nvim_lsp.tsserver.setup {                                                            
  on_attach = on_attach,                                                             
  filetypes = { "typescript", "typescriptreact", "typescript.tsx" },                 
  cmd = { "typescript-language-server", "--stdio" },                                 
  capabilities = capabilities                                                        
}
```

### Syntax highlights

For syntax highlights, I used Treesitter. Before use it, need to install itself:

```lua
yay tree-sitter
```

And a little setup in `.config/nvim/after/plugin/treesitter.rc.lua` :

```lua
local status, ts = pcall(require, "nvim-treesitter.configs")
if (not status) then return end

ts.setup {
  highlight = {
    enable = true,
    disable = {},
  },
  indent = {
    enable = true,
    disable = {},
  },
  ensure_installed = {
    "tsx",
    "toml",
    "json",
    "yaml",
    "css",
    "html",
    "lua",
    "go",
    "rust"
  },
  autotag = {
    enable = true,
  },
}

local parser_config = require "nvim-treesitter.parsers".get_parser_configs()
parser_config.tsx.filetype_to_parsername = { "javascript", "typescript.tsx" }
```

![Untitled](Neovim%20setup%20on%20Endeavour%20OS%205c66e6d6d4aa466bb6b6a82eea0ccaff/Untitled%201.png)

### File finder

[telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) providers an interactive file finder. Built on top of the latest Neovim features.

It’s also support extensions. So I use [telescope-file-browser.nvim](https://github.com/nvim-telescope/telescope-file-browser.nvim) as file manager.

They can search file and view content of file without open it. It’s a very useful feature.