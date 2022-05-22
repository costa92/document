# oh-my-zsh

修改 ~/.zshrc 文件
加载外部的环境的变量文件

```sh
[[ -f ~/.bash_profile ]] && source ~/.bash_profile;
[[ -f ~/.env_golang ]] && source ~/.env_golang;
```