# nivm 快捷键

ctrl + w  + h  切换文件管理与代码

ctrl + w  + l   切换文件管理与代码



`sc`将窗口关闭

`sv`开启水平分屏

```sh
-- 分屏 sv(split vertically) sh(split horizontally)
map('n', 'sv', ':vs<CR>', opt)
map('n', 'sh', ':sp<CR>', opt)
-- 关闭当前窗口 (close)
map('n', 'sc', '<C-w>c', opt)
-- 关闭其他窗口 (close other)
map('n', 'so', '<C-w>o', opt)
-- Alt + hjkl 在窗口之间跳转
map('n', '<A-h>', '<C-w>h', opt)
map('n', '<A-j>', '<C-w>j', opt)
map('n', '<A-k>', '<C-w>k', opt)
map('n', '<A-l>', '<C-w>l', opt)
```

