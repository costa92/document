
# vim 命令

![vi-vim-cheat-sheet-sch](media/vi-vim-cheat-sheet-sch.gif)

参考：https://www.runoob.com/linux/linux-vim.html





```vim
"显示行号
set number
"包裹行
set wrap
"设置文件编码
set encoding=utf-8
"设置文件打开标题
set titlestring=%t
"显示光标位置
set ruler
"语法高亮
syntax on
"缩进格式设置
set autoindent "自动换行,继承前一行的缩进方式，适用于多行注释
set expandtab  "转换tab为空格,expandtab 选项用于设置在Vim插入模式下按下 Tab 键时，输入到Vim中的都是空格。smarttab 表示插入 Tab 时使用 shiftwidth。
"tab改为4个空格
set tabstop=4
"设置复制粘贴时，能对齐
set shiftround "表示缩进列数对齐到 shiftwidth 值的整数倍
set shiftwidth=4
set smarttab
set tabstop=4
set softtabstop=4 "insert mode tab and backspace use 4 spaces
"搜索设置
set hlsearch "搜索高亮
set ignorecase "大小写不敏感，https://harttle.land/2019/12/12/vim-case-sensitive.html
set incsearch "输入搜索模式时，每输入一个字符，就自动跳到第一个匹配的结果
set smartcase "https://www.ruanyifeng.com/blog/2018/09/vimrc.html,如果同时打开了ignorecase，那么对于只有一个大写字母的搜索词，将大小写敏感；其他情况都是大小写不敏感。比如，搜索Test时，将不匹配test；搜索test时，将匹配Test

set noerrorbells "出错时，不要发出响声。
set visualbell "出错时，发出视觉提示，通常是屏幕闪烁。
set history=1000 "Vim 需要记住多少次历史操作。
set autoread "打开文件监视。如果在编辑过程中文件发生外部改变（比如被别的编辑器编辑了），就会发出提示。
set listchars=tab:»■,trail:■ "如果行尾有多余的空格（包括 Tab 键），该配置将让这些空格显示成可见的小方块。
set list
set wildmenu
set wildmode=longest:list,full "命令模式下，底部操作指令按下 Tab 键自动补全。第一次按下 Tab，会显示所有匹配的操作指令的清单；第二次按下 Tab，会依次选择各个指令。

"启用折叠
set foldenable

"显示光标位置
set cursorcolumn "设置高亮显示光标当前所在列
set cursorline "设置高亮显示光标所在屏幕行


"粘贴，在Vim中通过鼠标右键粘贴时会在行首多出许多缩进和空格，通过set paste可以在插入模式下粘贴内容时不会有任何格式变形、胡乱缩进等问题。
set paste

set showcmd "用于设置在屏幕最后一行显示 (部分的) 命令。showmode 在插入、替换和可视模式里，在最后一行提供消息。
set showmatch "表示插入括号时短暂地跳转到与之匹配的对应括号，而停留的时间由 matchtime 选项设置。如果置位 ‘showmatch’，matchtime 表示显示配对括号的十分之一秒
set matchtime=2 "matchtime 表示显示配对括号的十分之一秒

"移动设置，通过视线垂直移动
nnoremap j gj
nnoremap k gk
xnoremap < <gv  "只读模式，向左缩进
xnoremap > >gv  "只读模式，向右缩进

"文件类型
filetype on "配置项是 Vim 文件类型检测功能的开关                                                                                                             
filetype plugin on "用于 Vim 打开加载文件类型插件功能。当这个选项打开时，Vim 会根据检测到的文件类型，在runtimepath中搜索该文件类型的所有插件并执行它们。
filetype indent on "用于指定 Vim 为不同类型的文件定义不同的缩进格式
" 函数定义
func SetTitle()
  call setline(1,"/**")
  call append(line("."), " *   Copyright (C) ".strftime("%Y")." All rights reserved.")
  call append(line(".")+1, " *")
  call append(line(".")+2, " *   FileName      ：".expand("%:t"))
  call append(line(".")+3, " *   Author        ：heidsoft")
  call append(line(".")+4, " *   Email         ：heidsoft@qq.com")
  call append(line(".")+5, " *   Date          ：".strftime("%Y年%m月%d日"))
  call append(line(".")+6, " *   Description   ：")
  call append(line(".")+7, " */")
endfunc

"autocmd及新文件自动生成注释,https://vimjc.com/vim-autocmd.html
autocmd BufNewFile *.cpp exec ":call SetTitle()"
"自动将光标定位到末尾"
autocmd BufNewFile * normal G
```
