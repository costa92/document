# initContainer使用

## 介绍：

init容器是在同一个Pod中的其他容器之前启动和执行的容器。它的目的是**为Pod上托管的主应用程序执行初始化逻辑**。例如，创建必要的用户帐户、执行数据库迁移、创建数据库模式等等。

## Init Containers 注意事项

在创建 init 容器时，应该考虑一些注意事项：

1. 它们总是在 Pod 中的其他容器之前执行。因此，它们不应包含需要很长时间才能完成的复杂逻辑。启动脚本通常小而简洁。如果您发现向 init 容器添加了太多逻辑，则应考虑将其中的一部分移至应用程序容器本身。
2. init 容器按顺序启动和执行。除非成功完成其前任容器，否则不会调用 init 容器。因此，如果启动任务很长，您可以考虑将其分解为多个步骤，每个步骤由一个 init 容器处理，以便您知道哪些步骤失败。

3. 如果任何 init 容器失败，整个 Pod 将重新启动（除非您将 restartPolicy 设置为 Never）。重新启动 Pod 意味着再次重新执行所有容器，包括任何 init 容器。因此，您可能需要确保启动逻辑可以容忍多次执行而不会导致重复。例如，如果数据库迁移已经完成，再次执行迁移命令应该被忽略。
4. init 容器是延迟应用程序初始化直到一个或多个依赖项可用的良好候选者。例如，如果您的应用程序依赖于强加 API 请求速率限制的 API，您可能需要等待特定时间段才能接收来自该 API 的响应。在应用程序容器中实现这个逻辑可能很复杂；因为它需要与健康和准备探测器相结合。一个更简单的方法是创建一个 init 容器，它会等待 API 准备就绪后再成功退出。只有在 init 容器成功完成其工作后，应用程序容器才会启动。
5. Init 容器不能像应用程序容器那样使用健康和就绪探测。原因是它们旨在成功启动和退出，就像 Jobs 和 CronJobs 的行为方式一样。
6. 同一个 Pod 上的所有容器共享相同的 Volumes 和网络。您可以利用此功能在应用程序及其初始化容器之间共享数据。

## Init容器“Request”和“Limits”行为

init 容器总是在同一个 Pod 上的其他应用程序容器之前启动。因此，调度程序对 init 容器的资源和限制给予更高的优先级。必须彻底考虑此类行为，因为它可能会导致不希望的结果。例如，如果你有一个 init 容器和一个应用程序容器，并且你将 init 容器的资源和限制设置为高于应用程序容器的资源和限制，那么只有在有一个满足 init 的可用节点时，整个 Pod 才会被调度容器要求。换句话说，即使有一个未使用的节点可以运行应用程序容器，如果 init 容器具有该节点可以处理的更高资源先决条件，Pod 也不会部署到该节点。因此，在定义 init 容器的请求和限制时，您应该尽可能严格。作为最佳实践，除非绝对需要，否则不要将这些参数设置为高于应用程序容器的值

##### 应用场景01：为数据库做种

在这个场景中，我们为 MySQL 数据库提供服务。该数据库用于测试应用程序。它不必包含真实数据，但必须填充足够的数据，以便我们可以测试应用程序的查询速度。我们使用 init 容器来处理 SQL 转储文件的下载并将其恢复到托管在另一个容器中的数据库。

定义文件可能如下所示：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mydb
  labels:
    app: db
spec:
  initContainers:
    - name: fetch
      image: mwendler/wget
      command: ["wget","--no-check-certificate","https://sample-videos.com/sql/Sample-SQL-File-1000rows.sql","-O","/docker-entrypoint-initdb.d/dump.sql"]
      volumeMounts:
        - mountPath: /docker-entrypoint-initdb.d
          name: dump
  containers:
    - name: mysql
      image: mysql
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: "example"
      volumeMounts:
        - mountPath: /docker-entrypoint-initdb.d
          name: dump
  volumes:
    - emptyDir: {}
      name: dump
```

上面的定义创建了一个 Pod，它托管着两个容器：init 容器和应用程序容器。让我们来看看这个定义的有趣方面：

1. init 容器负责下载包含数据库转储的 SQL 文件。我们使用 mwendler/wget 镜像，因为我们只需要 wget 命令。
2. 下载的 SQL 的目标目录是 MySQL 镜像用来执行 SQL 文件的目录（/docker-entrypoint-initdb.d）。此行为内置于我们在应用程序容器中使用的 MySQL 镜像中。
3. init 容器将/docker-entrypoint-initdb.d挂载到emptyDir卷。因为两个容器都托管在同一个 Pod 上，所以它们共享相同的卷。因此，数据库容器可以访问放置在 emptyDir 卷上的 SQL 文件。

##### 如果没有使用 InitContainers会发生什么？

在这个例子中，我们使用初始化模式来建立关注点分离的最佳实践。如果我们在不使用 init 模式的情况下实现相同的逻辑，我们必须基于 mysql 基础镜像创建一个新镜像，安装 wget，并使用它来下载 SQL 文件。这种方法的缺点是：

如果我们需要对下载逻辑进行任何更改，我们需要创建一个新镜像，推送它并更改其在定义文件中的引用。这增加了必须维护自定义镜像的负担。

它在 DB 容器与其启动逻辑之间创建了紧密耦合的关系，这使得应用程序更难管理并增加了引入错误和错误的可能性。

## 场景 02：延迟应用程序启动，直到依赖项准备就绪

init 容器的另一个常见用例是当您需要应用程序等待另一个服务完全运行（响应请求）时。以下定义演示了这种情况：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
```

因此，假设我们的应用程序在 myapp-container 上运行时无法正常运行，除非 myservice 应用程序正在运行。我们需要延迟 myapp 启动，直到 myservice 准备就绪。为此，我们使用一个简单的 nslookup 命令（第 11 行）不断检查“myservice”的名称解析是否成功。如果 nslookup 能够解析“myservice”，则服务将启动。使用成功退出代码，init 容器终止，让应用程序容器启动。否则，容器在再次尝试之前会休眠两秒钟，从而延迟应用程序容器的启动。



为了完整起见，这是 myservice 的定义文件：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
```



## 结论

初始化模式是设计需要启动逻辑的应用程序时要遵循的重要实践。

Kubernetes 提供 init 容器作为将应用程序逻辑与其启动过程分离的一种手段。

将应用程序初始化逻辑放在 init 容器中具有许多优点：

1. 您将强加关注点分离原则。一个应用程序可以有它的工程师团队，而它的初始化逻辑是由另一个团队编写的。

2. 当涉及到授权和访问控制时，拥有一个单独的团队来处理应用程序的初始化步骤可以使公司更加灵活。例如，如果启动应用程序需要使用需要安全许可的资源（例如，修改防火墙规则），则可以由具有合适凭据的人员完成。应用团队不参与操作。

3. 如果涉及的初始化步骤太多，可以将它们分解成多个 init 容器依次执行。如果一个步骤失败，init 容器会报告错误，这可以让您更好地了解逻辑的哪一部分不成功。

使用 init 容器时应考虑以下几点：

1. 初始化容器在失败时重新启动。因此，他们的代码必须是幂等的。

2. 初始化容器是请求和限制首先由调度程序检查。不正确的值可能会对调度程序关于放置整个 Pod（包括应用程序容器）的位置的决定产生负面影响。
