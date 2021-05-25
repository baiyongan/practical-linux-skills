# lsof 命令

## 命令用途
list open files，lsof 会列出当前系统所打开的文件，linux 环境中，一切皆文件，所以，”打开的文件” 指的不仅仅是传统意义上的文件, 还可以是网络/Unix域套接字, 匿名/具名管道, 共享库文件, 目录文件, 设备文件等等。很多场景下, 查看进程或系统打开的文件，会给系统监控，程序调试和系统排错等提供极大的帮助。因为 lsof 需要访问核心内存和各种文件，所以执行 lsof 需要 root 权限。 

## 使用场景

### lsof 基础
```shell
○ → lsof | head -n 10
COMMAND     PID   TID           USER   FD      TYPE             DEVICE  SIZE/OFF       NODE NAME
systemd       1                 root  cwd       DIR              253,0       244         64 /
systemd       1                 root  rtd       DIR              253,0       244         64 /
systemd       1                 root  txt       REG              253,0   1628608       2416 /usr/lib/systemd/systemd
systemd       1                 root  mem       REG              253,0     20064       8871 /usr/lib64/libuuid.so.1.3.0
systemd       1                 root  mem       REG              253,0    265576     194298 /usr/lib64/libblkid.so.1.1.0
systemd       1                 root  mem       REG              253,0     90248       8861 /usr/lib64/libz.so.1.2.7
systemd       1                 root  mem       REG              253,0    157424       1429 /usr/lib64/liblzma.so.5.2.2
systemd       1                 root  mem       REG              253,0     23968       8955 /usr/lib64/libcap-ng.so.0.0.0
systemd       1                 root  mem       REG              253,0     19896       8943 /usr/lib64/libattr.so.1.1.0

```
其中，每行显示一个打开的文件，若不指定条件默认将显示所有进程打开的所有文件。lsof输出各列信息的意义如下：
COMMAND：进程的名称
PID：进程标识符
USER：进程所有者
FD：文件描述符，应用程序通过文件描述符识别该文件。如cwd、txt等
TYPE：文件类型，如DIR、REG等
DEVICE：指定磁盘的名称
SIZE：文件的大小
NODE：索引节点（文件在磁盘上的标识）
NAME：打开文件的确切名称

### lsof 常用参数
- `lsof`

简单地执行lsof会列出当前系统中所有被打开的文件, 但为了看到完整的信息, 通常需要具有root权限;

- `lsof -u bya`

列出用户 bya 打开的文件, 可指定多个用户, 默认是 OR 的关系;

- `lsof -c systemd`

列出名称以 systemd 开头的进程所打开的文件, c for command, 可指定多个;

- `lsof -c /^b.*a$/` 

列出名称以 b 开头, a 结尾的进程打开的文件;

- `lsof -p 8080` 

列出进程号为 8080 的进程打开的文件, 可指定多个;

- `lsof server.log`

列出打开 server.log 文件的进程, 可指明多个文件;

- `lsof .`

列出打开当前目录的进程;

- `lsof +D .` 

递归地列出当前目录中被打开的文件, 也可以 lsof | grep pwd;

- `lsof -i` 

列出打开的套接字;

- `lsof -i tcp` 

列出打开的tcp套接字;

- `lsof -i :5198` 

列出打开5198端口的进程;

- `lsof -i :ssh` 

列出打开22端口的进程;

- `lsof -i tcp:5198` 

列出打开5198号tcp端口的进程;

- `lsof -U` 

列出打开Unix域套接字的进程;

- `lsof -d 0-2` 

列出在0到2文件描述符上打开文件的进程;

- `lsof -d mem` 

列出打开映射文件的进程;

- `lsof -d txt`

列出打开的可执行文件

- `lsof -a`

上述功能性选项可以组合使用, 但默认采用OR逻辑列出, -a选项令lsof使用AND逻辑;

- `lsof -t`

只列出进程号, 可以借此得到特定的进程列表, 以方便对这些进程的自动处理, 比如kill lsof -t -i :5198会杀死所有打开5198端口的进程;

- `lsof -r [seconds]`

-r选项可以让lsof以一定的时间间隔连续执行, 在监视文件/进程时会非常实用.


## 经验总结

## 参考资料

man lsof

