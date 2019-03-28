# VIM

## 命令

| 命令 | xx |
| :--- | :--- |
| :set fileformat | 设置文件格式 |
| :set endofline | 设置文件结束符 |
| :set noendofline | 取消文件结束符 |
| :set list | 进入List Mode |
| :set nolist | 退出List Mode |
| :%s/\n//g | 删除换行符 |
| :set textwidth | 设置行宽 |
| :set textwidth | 设置行边距 |
| :join | 合并多行 |
| J | 合并两行 |

**copy&paste**

```text
首先，可以在命令模式下输入v进入自由选取模式，选择需要剪切的文字后，按下d就可以进行剪切了。
其他命令模式下剪切命令：
dd：剪切当前行
ndd：n表示大于1的数字，剪切n行
dw：从光标处剪切至一个单子/单词的末尾，包括空格
de：从光标处剪切至一个单子/单词的末尾，不包括空格
d$：从当前光标剪切到行末
d0：从当前光标位置（不包括光标位置）剪切之行首
d3l：从光标位置（包括光标位置）向右剪切3个字符
d5G：将当前行（包括当前行）至第5行（不包括它）剪切
d3B：从当前光标位置（不包括光标位置）反向剪切3个单词
dH：剪切从当前行至所显示屏幕顶行的全部行
dM：剪切从当前行至命令M所指定行的全部行
dL：剪切从当前行至所显示屏幕底的全部行

首先，可以在命令模式下输入v进入自由选取模式，选择需要剪切的文字后，按下d就可以进行剪切了。
其他命令模式下剪切命令：
yy：复制当前行
nyy：n表示大于1的数字，复制n行
yw：从光标处复制至一个单子/单词的末尾，包括空格
ye：从光标处复制至一个单子/单词的末尾，不包括空格
y$：从当前光标复制到行末
y0：从当前光标位置（不包括光标位置）复制之行首
y3l：从光标位置（包括光标位置）向右复制3个字符
y5G：将当前行（包括当前行）至第5行（不包括它）复制
y3B：从当前光标位置（不包括光标位置）反向复制3个单词
```

**diff**

```bash
vimdiff  FILE_LEFT  FILE_RIGHT

vim -d  FILE_LEFT  FILE_RIGHT

vim FILE_LEFT
# 在vim的ex模式
:vertical diffsplit FILE_RIGHT
#
:set noscrollbind
#
]c
#
[c
#
dp （diff "put"）
#
do (diff "get"，之所以不用dg，是因为dg已经被另一个命令占用了)
#
:diffupdate

# 如果希望修改缺省的上下文行数，可以这样设置
:set diffopt=context:3

zo
zc
```

## plugin

* [pathogen](https://github.com/tpope/vim-pathogen)
* [vundle](https://github.com/gmarik/Vundle.vim)

> Vundle .vimrc config

```text
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
Plugin 'tpope/vim-fugitive'
" plugin from http://vim-scripts.org/vim/scripts.html
Plugin 'L9'
" Git plugin not hosted on GitHub
Plugin 'git://git.wincent.com/command-t.git'
" git repos on your local machine (i.e. when working on your own plugin)
Plugin 'file:///home/gmarik/path/to/plugin'
" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" Avoid a name conflict with L9
Plugin 'user/L9', {'name': 'newL9'}

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
```

## autocompletion

* python
  * [jedi-vim](https://github.com/davidhalter/jedi-vim)

