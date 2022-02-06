# scala(spark-shell)里多行输入

经常将程序片段直接黏贴到spark-shell里，会遇到多行输入的异常，可按以下方法解决

1.scala-shell里直接输入:paste命令，黏贴后结束按ctrl+D

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)
if (true)
    print("that was true")
else
    print("false")
[Ctrl-D]

// Exiting paste mode, now interpreting.
that was true
```

2.scala-shell里通过:load 命令

```scala
scala> :load /Users/Al/ProjectX/Person.scala
```

参考链接：https://alvinalexander.com/scala/scala-repl-how-to-paste-load-blocks-of-source-code/

查看帮助命令
```scala
scala> :help
All commands can be abbreviated, e.g., :he instead of :help.
:completions <string>    output completions for the given string
:edit <id>|<line>        edit history
:help [command]          print this summary or command-specific help
:history [num]           show the history (optional num is commands to show)
:h? <string>             search the history
:imports [name name ...] show import history, identifying sources of names
:implicits [-v]          show the implicits in scope
:javap <path|class>      disassemble a file or class name
:line <id>|<line>        place line(s) at the end of history
:load <path>             interpret lines in a file
:paste [-raw] [path]     enter paste mode or paste a file
:power                   enable power user mode
:quit                    exit the interpreter
:replay [options]        reset the repl and replay all previous commands
:require <path>          add a jar to the classpath
:reset [options]         reset the repl to its initial state, forgetting all session entries
:save <path>             save replayable session to a file
:sh <command line>       run a shell command (result is implicitly => List[String])
:settings <options>      update compiler options, if possible; see reset
:silent                  disable/enable automatic printing of results
:type [-v] <expr>        display the type of an expression without evaluating it
:kind [-v] <type>        display the kind of a type. see also :help kind
:warnings                show the suppressed warnings from the most recent line which had any
```