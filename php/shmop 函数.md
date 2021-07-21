# shmop 函数



shmop_open  创建或打开共享内存块

shmop_write 向共享内存块中写入数据

shmop_read 从共享内存块中读取数据

shmop_size  获取共享内存的大小

shmop_close  关闭共享内存块

shmop_delete 删除共享内存块



```
<?php
// 创建一块共享内存
$shm_key = 0xff3  # 16 进制
$shm_id = @shmop_open($shm_key,'c',0644,1024);
// 读取并写入数据
$data = shmop_read($shm_id,0,1024);
shmop_write($shm_id,json_encode($data),0);

$size = shmop_size($shm_id); // 获取内存中实际数据占用大小

// 关闭内存块，并不会删除共享内存，只是清除 PHP 的资源
shmop_close($shm_id)

```

## shmop_open(创建内存段)

该函数中出现的第一个事物是系统 ID 参数。这是标识系统中的共享内存段的数字。第二个参数是访问模式，它非常类似于 fopen 函数的访问模式。您可以在 4 种不同的模式下访问一个内存段：



模式 “a”，它允许您访问只读内存段，只读访问
模式 “w”，它允许您访问可读写的内存段，读写
模式 “c”，它创建一个新内存段，或者如果该内存段已存在，尝试打开它进行读写
模式 “n”，它创建一个新内存段，如果同样 key 的已存在，则会创建失败，这是为了安全使用共享内存考虑。

第三个参数是内存段的权限。您必须在这里提供一个八进制值。

第四个参数提供内存段大小，以字节为单位。由于使用的共享内存片段是固定长度的，在存储和读取的时候要计算好数据的长度，不然可能会写入失败或者读取空值。。

请注意，此函数返回一个 ID 编号，其他函数可使用该 ID 编号操作该共享内存段。这个 ID 是共享内存访问 ID，与系统 ID 不同，它以参数的形式传递。请注意不要混淆这两者。如果失败，shmop_open 将返回 FALSE。在创建内存块时建议key参数用常量而不用变量，否则很有可能造成内存泄露。


shmop_write(向内存段写入数据)

这个函数类似于 fwrite 函数，后者有两个参数：打开的流资源（由 fopen 返回）和您希望写入的数据。shmop_write 函数也执行此任务。

第一个参数是 shmop_open 返回的 ID，它识别您操作的共享内存块。第二个参数是您希望存储的数据，最后的第三个参数是您希望开始写入的位置。默认情况下，我们始终使用 0 来表示开始写入的位置。请注意，此函数在失败时会返回 FALSE，在成功时会返回写入的字节数。

shmop_read(从内存段读取数据)

从共享内存段读取数据很简单。您只需要一个打开的内存段和 shmop_read 函数。此函数接受一些参数，工作原理类似于 fread。

请留意这里的参数。shmop_read 函数将接受 shmop_open 返回的 ID，我们已知道它，不过它还接受另外两个参数。第二个参数是您希望从内存段读取的位置，而第三个是您希望读取的字节数。第二个参数可以始终为 0，表示数据的开头，但第三个参数可能存在问题，因为我们不知道我们希望读取多少字节。

这非常类似于我们在 fread 函数中的行为，该函数接受两个参数：打开的流资源（由 fopen 返回）和您希望从该流读取的字节数。使用 filesize 函数（它返回一个文件中的字节数）来完整地读取它。

shmop_size（返回内存段数据实际大小）

比如，我们开辟了一个长度为100字节的内存空间，但是实际存入的数据长度仅仅90，那么使用shmop_size返回的值就是90.

shmop_delete（删除内存段）

该函数仅接受一个参数：我们希望删除的共享内存 ID，这不会实际删除该内存段。它将该内存段标记为删除，因为共享内存段在有其他进程正在使用它时无法被删除。shmop_delete 函数将该内存段标记为删除，阻止任何其他进程打开它。要删除它，我们需要关闭该内存段。在创建内存块时建议key参数用常量而不用变量，否则很有可能造成内存泄露。

shmop_close(关闭内存段)

我们在对内存段进行读取和写入，但完成操作后，我们必须从它解除，这非常类似于处理文件时的 fclose 函数。打开包含一个文件的流并在其中读取或写入数据后，我们必须关闭它，否则将发生锁定。


**简单测试结果查看**

```
ipcs -m
```



```
------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status
0x00000ff3 0          root       644        1024       0
```

命令说明

key ：共享内存的唯一的key值，共享内存通过该key来判断你读取的是哪一块内存。
shmid：当使用key来获取内存时，你获得的是这个id的值。它作为你操作内存块的标识。
owner：创建该共享内存块的用户
perms：该共享内存的读写权限，8禁止，可以是777，与文件的读写权限一致。
bytes：该内存块的大小
nattch：连接该内存块的进程数
status：当前状态，如：dest，即将删除等。

```

class Shared
{
    private $shm_id;
    private $shm_key = 0xff3;
    private $shm_size = 1024;

    function __construct() {
        $this->shm_id = shmop_open($this->shm_key, "c", 0644, $this->shm_size) or die('申请失败');
        echo $this->shm_id;
        echo PHP_EOL;
    }

    function __get($name) {
        $buf = shmop_read($this->shm_id, 0, $this->shm_size);
        $buf = unserialize(trim($buf));
        if ($name == '_all')
            return $buf;
        return isset($buf[$name]) ? $buf[$name] : false;
    }

    function __set($name, $value) {
        $buf = shmop_read($this->shm_id, 0, $this->shm_size);
        $buf = unserialize(trim($buf));
        $buf[$name] = $value;
        $buf = serialize($buf);
        if (strlen($buf) >= $this->shm_size)
            die('空间不足');
        shmop_write($this->shm_id, $buf, 0) or die('写入失败');
    }

    function del() {
        shmop_delete($this->shm_id);
    }

}
```

