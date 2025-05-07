# Makefile 中使用 echo -e 无法生效的问题

当使用 @echo -e 没有生效的原因是：

在不同系统下，Makefile的默认shell可能不同，这会导致执行结果的差异。具体来说，在bash下运行正常的Makefile，在dash下运行可能会多显示一个“-e”。这是因为从Ubuntu 6.10开始，默认使用的shell是dash，而不是bash。dash更快、更高效，可以加快系统启动速度。

Makefile本身有一个环境变量也叫SHELL（与系统环境变量SHELL同名，但含义不同）。我们可以在Makefile中明确指定SHELL的值，以指明使用哪个shell来解析命令。例如，可以在Makefile中添加 SHELL = /bin/bash 来指定使用bash作为shell。

为了查看当前Makefile使用的shell，可以在Makefile中使用命令 echo $SHELL。


## 案例1：处理echo -e问题
在bash中，echo -e可以正常处理转义字符，而dash可能不能正确处理。因此，可以通过指定使用bash来解决这个问题。

makefile

```sh
SHELL = /bin/bash

all:
	echo -e "Hello,\nWorld!"
```

## 案例2：处理复杂的shell脚本
有时，Makefile中的某些规则可能需要使用bash的特性，例如管道、条件判断等。

```sh
SHELL = /bin/bash

all:
	if [ -f myfile.txt ]; then \
		echo "myfile.txt exists"; \
	else \
		echo "myfile.txt does not exist"; \
	fi
```

## 案例3：处理变量替换
在一些shell中，变量替换的语法可能会有所不同。使用bash可以确保一致性。

```sh
SHELL = /bin/bash

all:
	for file in *.txt; do \
		mv "$$file" "$${file%.txt}.bak"; \
	done
```

## 案例4：使用bash的数组
如果Makefile中需要使用数组，可以指定使用bash，因为dash不支持数组。

```sh
SHELL = /bin/bash

all:
	ARRAY=(1 2 3 4 5)
	for i in "${ARRAY[@]}"; do \
		echo "Element: $$i"; \
	done
```

## 案例5：兼容性问题
有时Makefile需要兼容不同的shell。可以在Makefile中检测当前shell并做出相应处理。

```sh
ifeq ($(SHELL),/bin/bash)
    ECHO_FLAG := -e
else
    ECHO_FLAG :=
endif

all:
	echo $(ECHO_FLAG) "Hello,\nWorld!"
```

## 案例6：自动检测并设置shell
如果不确定系统中默认的shell，可以在Makefile中进行检测并自动设置。

```sh
SHELL := $(shell echo $$SHELL)

ifeq ($(findstring bash,$(SHELL)),bash)
    MY_SHELL := /bin/bash
else
    MY_SHELL := /bin/dash
endif

SHELL := $(MY_SHELL)

all:
	echo "Using shell: $(SHELL)"
```