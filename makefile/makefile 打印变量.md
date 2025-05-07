在编写Makefile时，调试信息的输出对于查找和解决问题至关重要。Makefile提供了几种不同的方式来输出调试信息，包括`info`、`warning`、`error`以及`echo`命令。下面我们将详细介绍这些工具的使用方法以及它们之间的区别。

makefile 打印变量方式有两种情况

1. 使用info/warning/error增加调试信息
    1. 打印字符串

```Makefile
$(info xxx-msg)  # 输出字符串xxxx-msg，不需要加""，info后加空格
=>  xxxx-msg 

```

```
2. 打印变量
```

```Makefile
$(info $(GOPATH)) #打印变量GOPATH，变量名用$())

=> /home/user/code/go
```

```
3. 字符串、变量混合打印
```

```Makefile
$(info GOPATH: $(GOPATH))
=> GOPATH: /home/hellotalk/code/go

```

```
4. info/warning/error之间区别  

    info只输出信息：
```

```Makefile
$(info GOPATH is: $(GOPATH)) 
=> GOPATH is: /home/hellotalk/code/go
```

```
    warning输出信息和对应的行号：
```

```Makefile
  $(warning ***** $(shell date))
  => scripts/make-rules/common.mk:99: ***** Tue Jun 18 05:27:20 PM CST 2024

```

```
    error输出信息和对应的行号，并停止makefile的编译：
```

```Makefile
$(error GOPATH: $(GOPATH))
=> scripts/make-rules/golang.mk:12: *** GOPATH: /home/hellotalk/code/go。 停止。

```

2. 使用echo增加调试信息（echo只能在target：后面的语句中使用，且前面是个TAB）

```Makefile
# 假设我们有一个变量  
MY_VARIABLE := Hello, Makefile!  
  
# 定义一个目标（target）  
all:  
  # 注意这里有一个制表符（Tab），而不是空格  
  @echo "开始构建..."  
  @echo "MY_VARIABLE 的值是: $(MY_VARIABLE)"  
  # 假设这里有一些其他的构建命令...  
  @echo "构建完成!"  
  
# 如果你还有其他的目标（target），也可以在这里定义  
clean:  
  # 同样是制表符（Tab）开头  
  @echo "开始清理..."  
  # 这里应该有清理构建产物的命令，比如 rm -f ...  
  @echo "清理完成!"
```

```
执行测试命令
```

```Bash
make -n all
```

```
这将会显示`all`目标下的所有命令，但由于`@`前缀的存在，它们不会显示`echo`命令的内容。如果你想要看到`echo`的内容，你需要移除`@`前缀或者不使用`-n`选项。
```