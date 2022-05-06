

# Lua配置neovim

## 整体架构

```txt
├── LICENSE
├── README.md
├── init.lua
└── lua
    ├── autocmds.lua
    ├── basic.lua
    ├── colorscheme.lua
    ├── keybindings.lua
    ├── lsp
    │   ├── cmp.lua
    │   ├── config
    │   │   ├── bash.lua
    │   │   ├── emmet.lua
    │   │   ├── html.lua
    │   │   ├── json.lua
    │   │   ├── lua.lua
    │   │   ├── markdown.lua
    │   │   ├── pyright.lua
    │   │   ├── rust.lua
    │   │   └── ts.lua
    │   ├── formatter.lua
    │   ├── null-ls.lua
    │   ├── setup.lua
    │   └── ui.lua
    ├── plugin-config
    │   ├── bufferline.lua
    │   ├── comment.lua
    │   ├── dashboard.lua
    │   ├── gitsigns.lua
    │   ├── indent-blankline.lua
    │   ├── lualine.lua
    │   ├── nvim-autopairs.lua
    │   ├── nvim-tree.lua
    │   ├── nvim-treesitter.lua
    │   ├── project.lua
    │   ├── surround.lua
    │   ├── telescope.lua
    │   ├── toggleterm.lua
    │   ├── vimspector.lua
    │   └── which-key.lua
    ├── plugins.lua
    └── utils
        ├── fix-yank.lua
        ├── global.lua
        └── im-select.lua
```



首先 **init.lua** 是整个配置的入口文件，负责引用所有其他的模块，基本上想要打开或关闭某个插件只要在这里修改一行代码即可。

- **basic.lua：** 基础配置，是对默认配置的一个重置。
- **colorscheme.lua：** 我们安装的主题皮肤配置，在这里切换主题。
- **keybindings.lua：** 快捷键的设置，所有插件的快捷键也都会放在这里。
- **plugins.lua：** 插件安装管理，插件安装或卸载全在这里设置。
- **lsp 文件夹：** 是对 Neovim 内置 LSP 功能的配置，包括常见编程语言与语法提示等。
  - **config** **：** 文件夹包含各种语言服务器单独的配置文件。
  - **setup.lua** **：** 内置 LSP 的配置。
  - **cmp.lua** **：** 语法自动补全补全的配置，包括各种补全源，与自定义代码段。
  - **ui.lua：** 对内置 LSP 功能增强和 UI 美化。
  - **formatter.lua：** 独立代码格式化功能。
- **plugin-config 文件夹：** 是对第三方插件的配置，未来每添加一个插件，这里就多一个配置文件。
- **utils 文件夹：** 是对常见问题的修改，包括输入法切换，针对 windows 的特殊配置等。



## 基础设置和按键映射

`timeoutlen` 这个参数 **非常重要**，在 VIM 快捷键中经常有多个键连敲的情况，下边代码表示键盘快捷键连击时限设为500毫秒，超过500毫秒就不认为是连续快捷键了。



在 Neovim 中使用以下方法设置快捷键：

- `vim.api.nvim_set_keymap()` 全局快捷键
- `vim.api.nvim_buf_set_keymap()` Buffer 快捷键



```lua
vim.api.nvim_set_keymap('模式', '按键', '映射为', 'options')
```



**options** 大部分会设置为 `{ noremap = true, silent = true }`。`noremap` 表示不会重新映射，比如你有一个映射 `A` -> `B` , 还有一个 `B` -> `C`，这个时候如果你设置 `noremap = false` 的话，表示会重新映射，那么 `A` 就会被映射为 `C`。`silent` 为 `true`，表示不会输出多余的信息。



```lua
local map = vim.api.nvim_set_keymap
-- 复用 opt 参数
local opt = {noremap = true, silent = true }
```

通过上面两个local变量就可以将按键映射变为：

`map('模式', '按键', '映射为', 'options')`



## 插件管理

如果需要手动安装Packer

```lua
git clone --depth 1 https://github.com/wbthomason/packer.nvim\
 ~/.local/share/nvim/site/pack/packer/start/packer.nvim
```



也可以在lua/plugins.lua里边配置使其自动安装，具体请看plugins.lua文件



解释一下pcall函数，pcall函数用于收集错误，使程序遇到错误时不直接退出程序。

`pcall` 函数的返回的第一个参数是 `boolean` 类型表示状态，这句话的意思是，如果 `colorscheme` 执行成功，则返回 `true`，否则返回 `false`。



### 注意nvim_tree插件的配置

 `system_open` 项，如果想要在 `WSL` 中用 Windows 系统默认设置打开文件，需要使用 `Node.js` 全局安装一个 `wsl-open` 包，使用命令 `npm install -g wsl-open`，如果不需要这个功能，也可以不用安装。 如果不是 Windows 系统也就不需要安装。

如果是使用mac系统，直接将该项设置为open。



## nvim_treesitter插件注意事项

### mac系统中存在的问题

- 问题描述

- ![](/Users/nero/Library/Application%20Support/marktext/images/2022-05-06-10-19-16-image.png)

- apple mac m1无法正常编译treesitter.so等文件，详情看上图
  
  1. 在打开neovim的时候加上arch arm64
     
     ```shell
     arch arm64 nvim ~/.config/nvim/lua/plugin-config/nvim-treesitter.lua
     ```
  
  2. 等待自动安装，如果个别安装不了，可以加入到ignore_install中

- 错误原因：如错误所示，因为mac m1版本的架构是arm64，而提供的是86_64，因此，在启动的使用制定一下neovim的架构，使nvim-treesitter能自动检测到架构并且安装对应的架构版本



## 
