# lazy.vim

问题：

```sh
The following errors have been detected: ~
- ERROR arduino(locals): /usr/share/nvim/runtime/lua/vim/treesitter/query.lua:259: query: invalid node type at position 2423 for language arduino
  arduino(locals) is concatenated from the following files:
  |    [OK]:"/home/hugo/.local/share/nvim/site/pack/plugins/start/nvim-treesitter/queries/c/locals.scm"
  | [ERROR]:"/home/hugo/.local/share/nvim/site/pack/plugins/start/nvim-treesitter/queries/cpp/locals.scm", failed to load: /usr/share/nvim/runtime/lua/vim/treesitter/query.lua:259: query: invalid node type at position 1046 for language arduino
  |    [OK]:"/home/hugo/.local/share/nvim/site/pack/plugins/start/nvim-treesitter/queries/arduino/locals.scm"
- ERROR comment(highlights): /usr/share/nvim/runtime/lua/vim/treesitter/query.lua:259: query: invalid node type at position 1135 for language comment
  comment(highlights) is concatenated from the following files:
```



### Additional context

```sh
TSInstall all
```

