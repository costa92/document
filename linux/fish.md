# fish 设置环境



只需使用`alias`. 这是一个基本示例：

创建  `~/.config/fish/functions/rmi.fish`

```sh
# Define alias in shell
alias rmi "rm -i"

# Define alias in config file ( `~/.config/fish/config.fish` )
alias rmi="rm -i"

# This is equivalent to entering the following function:
function rmi
    rm -i $argv
end

# Then, to save it across terminal sessions:
funcsave rmi
```

参考手册 [传送](https://fishshell.com/docs/current/commands.html#alias)