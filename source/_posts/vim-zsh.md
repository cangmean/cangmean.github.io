---
title: vim zsh 增强命令行
date: 2018-07-17 11:58:41
tags: ['vim', 'zsh']
---

使用 vim 插件来增强终端下的编辑能力， 使用 zsh 替换 bash 提升自动补全

### 安装 vim 和插件管理器 vundle

vundle 包管理， 可以轻松的安装 vim 插件, 首先安装 vundle

```bash
mkdir -p ～/.vim/bundle/vundle
git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
```

添加 vim 配置， 在用户主目录下创建`.vim`并编辑内容

<!--more-->

```
set nocompatible
filetype off
set rtp+=~/.vim/bundle/vundle

call vundle#begin()

Plugin 'scrooloose/nerdtree'
Plugin 'majutsushi/tagbar'
Plugin 'bling/vim-airline'
Plugin 'mattn/emmet-vim'
Plugin 'easymotion/vim-easymotion'
Plugin 'tmhedberg/SimpylFold'
Plugin 'vim-scripts/indentpython.vim'
Plugin 'scrooloose/syntastic'
Plugin 'nvie/vim-flake8'
Plugin 'tomasr/molokai'
Plugin 'kien/ctrlp.vim'
Plugin 'Valloric/YouCompleteMe'

call vundle#end()
filetype plugin indent on

syntax enable
syntax on
colorscheme molokai
let g:molokai_original = 1

" setting nerdtree
" ignore files in NERDTree
let NERDTreeIgnore=['\.pyc$', '\~$']
map <F2> :NERDTreeToggle<CR>
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") && b:NERDTreeType == "primary") | q | endif

" setting tagbar
nmap <F4> : TagbarToggle<CR>
let g:tagbar_width=30

" execute python
"map <F5> :w<CR>:!python %<CR>

" setting ariline
let g:airline_powerline_fonts = 1
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#left_sep = ' '
let g:airline#extensions#tabline#left_alt_sep = '|'

" emmet
let g:user_emmet_expandabbr_key='<c-e>'
let g:user_emmet_settings={'indentation':'    '}

" easymotion
map f <Plug>(easymotion-prefix)
map ff <Plug>(easymotion-s)
map fs <Plug>(easymotion-f)
map fl <Plug>(easymotion-lineforward)
map fj <Plug>(easymotion-j)
map fk <Plug>(easymotion-k)
map fh <Plug>(easymotion-linebackward)
let g:EasyMotion_smartcase = 1

" simpylfold
let g:SimpylFold_docstring_preview=1

" syntastic
let python_highlight_all=1

set nu
set encoding=utf-8
set fileencoding=utf-8
set tabstop=4
set expandtab
set softtabstop=4
set shiftwidth=4
set autoindent
set fileformat=unix

"split navigations
nnoremap <C-J> <C-W><C-J>
nnoremap <C-K> <C-W><C-K>
nnoremap <C-L> <C-W><C-L>
nnoremap <C-H> <C-W><C-H>

"enable folding
set foldmethod=indent
set foldlevel=99

"enable folding with the spacebar
nnoremap <space> za
```

然后输入`vim`命令进入命令行模式后， 输入安装命令. 这里的输入的命令是根据`.vimrc`中插件前缀设定的

```
PluginInstall
```

### 安装 zsh 和 oh-my-zsh

zsh 可以通过 yum 来安装

```bash
yum install zsh
```

使用 wget 下载安装脚本， 并执行

```
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

切换 zsh

```
chsh -s /usr/bin/zsh
```

可以编辑主目录下`.zshrc`文件来管理主题
