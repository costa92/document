# jenkins pipeline when 内置判断条件
when指令允许Pipeline根据给定条件确定是否应执行该阶段。

@[TOC]

when指令概述

when指令允许Pipeline根据给定条件确定是否应执行该阶段。该when指令必须至少包含一个条件。如果when指令包含多个条件，则所有子条件必须返回true才能执行该阶段。这与子条件嵌套在allOf条件中的情况相同（参见下面的示例）。如果使用anyOf条件，请注意一旦找到第一个true条件，条件就会跳过剩余的测试条件。

更复杂的条件结构可使用嵌套条件建：not，allOf或anyOf。嵌套条件可以嵌套到任意深度。

是否必须： 非必须
参数： 没有
所在位置: 在stage指令内
内置条件

branch

当正在构建的分支与给定的分支模式匹配时执行阶段。如 when { branch 'master' } . 这仅在多分支Pipeline中有效。

buildingTag

构建构建标记时执行阶段。例: when { buildingTag() }

changelog

如果构建的 SCM 更新日志包含给定的正则表达式模式，则执行该阶段 例如: when { changelog '.^\[DEPENDENCY\] .+$' }

changeset

如果构建的 SCM 变更集包含与给定字符串或 glob 匹配的一个或多个文件，则执行该阶段。 例如: when { changeset "/.js" }

默认情况下，路径匹配不区分大小写，可以使用 caseSensitive 参数关闭, 例如: when { changeset glob: "ReadMe.*", caseSensitive: true }

changeRequest

如果当前构建用于 “更改请求”（GitHub 和 Bitbucket 上的Pull Request，GitLab 上的Merge请求或 Gerrit 中的change等），则执行该阶段。 如果没有传递任何参数，则每个更改请求都会运行该阶段， 例如: when { changeRequest() }.

通过向变更请求添加带参数的过滤器属性，可以使阶段仅在匹配的变更请求上运行。 可能的属性是id，target，branch，fork，url，title，author，authorDisplayName和authorEmail。 其中每个对应一个CHANGE_*环境变量， 例如: when { changeRequest target: 'master' }.

可以在属性之后添加可选参数比较器，以指定如何评估匹配的任何模式：EQUALS用于简单字符串比较（默认值），GLOB用于 ANT 样式路径 glob（与例如变更集相同）或REGEXP用于常规 表达匹配。例如: when { changeRequest authorEmail: "[\\w_-.]+@example.com", comparator: 'REGEXP' }

environment

当指定的环境变量设置为给定值时执行阶段， 例如: when { environment name: 'DEPLOY_TO', value: 'production' }

equals

当期望值等于实际值时执行阶段， 例如: when { equals expected: 2, actual: currentBuild.number }

expression

当指定的 Groovy 表达式求值为true时执行阶段，例如: when { expression { return params.DEBUG_BUILD } } .

请注意，从表达式返回字符串时，必须将它们转换为布尔值或返回null表示为false。 简单地返回0或false仍将评估为true。

tag

如果TAG_NAME变量与给定模式匹配，则执行该阶段。例如: when { tag "release-*" }.

如果提供了空模式，则如果TAG_NAME变量存在，则将执行该阶段（与 buildingTag（）相同）。

可以在属性之后添加可选参数比较器，以指定如何评估匹配的任何模式：EQUALS用于简单字符串比较（默认值），GLOB用于 ANT 样式路径 glob（与例如变更集相同）或REGEXP用于常规 表达匹配。 例如: when { tag pattern: "release-\\d+", comparator: "REGEXP"}

not

嵌套条件为 false 时执行阶段。 必须包含一个条件。 例如: when { not { branch 'master' } }

allOf

当所有嵌套条件都为真时执行阶段。 必须至少包含一个条件。例如: when { allOf { branch 'master'; environment name: 'DEPLOY_TO', value: 'production' } }

anyOf

至少有一个嵌套条件为真时执行阶段。 必须至少包含一个条件。例如: when { anyOf { branch 'master'; branch 'staging' } }

在进入stage's agent之前评估when
默认情况下，进入该阶段的agent后，对该阶段的when条件进行评估。 但是，可以通过在when块中指定beforeAgent选项来更改此设置。 如果beforeAgent设置为true，则首先评估when条件，并且仅当when条件的计算结果为true时才进入agent。

示例
```sh
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Example Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            when {
                branch 'production'
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Example Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            when {
                branch 'production'
                environment name: 'DEPLOY_TO', value: 'production'
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Example Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            when {
                allOf {
                    branch 'production'
                    environment name: 'DEPLOY_TO', value: 'production'
                }
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Example Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            when {
                branch 'production'
                anyOf {
                    environment name: 'DEPLOY_TO', value: 'production'
                    environment name: 'DEPLOY_TO', value: 'staging'
                }
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Example Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            when {
                expression { BRANCH_NAME ==~ /(production|staging)/ }
                anyOf {
                    environment name: 'DEPLOY_TO', value: 'production'
                    environment name: 'DEPLOY_TO', value: 'staging'
                }
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent none
    stages {
        stage('Example Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            agent {
                label "some-label"
            }
            when {
                beforeAgent true
                branch 'production'
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}
```
在Pipeline中使用tag

使用标签来驱动Jenkins管道中的行为。考虑以下设计Jenkinsfile，其中包含构建，测试和部署的三个基本阶段：

```sh
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'make package'
            }
        }
        stage('Test') {
            steps {
                sh 'make check'
            }
        }
        stage('Deploy') {
            when { tag "release-*" }
            steps {
                echo 'Deploying only because this commit is tagged...'
                sh 'make deploy'
            }
        }
    }
}
```
特别值得注意的是 when Deploy阶段的tag条件是应用标准。这意味着只有在 Git 中与release-* Ant 样式通配符匹配的标记触发Pipeline时，才会执行该阶段。

参考

1. https://jenkins.io/doc/book/pipeline/syntax/#when

2. https://jenkins.io/blog/2018/05/16/pipelines-with-git-tags/

3. https://www.jenkins.io/zh/doc/book/pipeline/syntax/#stage

4. https://www.jenkins.io/zh/doc/book/pipeline/syntax/