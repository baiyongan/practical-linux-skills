# nfs4_acl 权限设置

manipulate NFSv4 file/directory access control lists，需要安装 NFS4 ACL tool

## 前期配置

- 安装 nfs4-acl-tools, 并使用 nfs4 方式挂载

```shell
yum install -y nfs4-acl-tools
mount -t nfs4 to_be_mounted_filesystem  mount_point
df -hT mount_point | grep nfs4 
mount | grep nfs4
```

## 常用命令

- nfs4_getfacl
- nfs4_setfacl
- nfs4_editfacl 等价于 nfs4_setfacl -e 

## ACL Format

ACL = Access Control List
ACE = Access Control Entry
每条 ACE 的格式如下：
(ACE Type):(ACE Flags):(ACE Principal):(ACE Permissions)

## ACE Types

## ACE Flags

## ACE Principles

## ACE Permissions

### ACE Permission Aliases
- R = rntcy：Generic Read
- W = watTNcCy：Generic Write
- X = xtcy：Generic Execute

## 权限变更操作

### 新增/删除 ACE
### 直接编辑 ACL
### 从文件中添加 ACE
### 替换当前的 ACE
### 替换指定的 ACE

## 经验总结

- 可以在支持 nfs4 acl 的 filesystem 中设置权限，然后在不支持 nfs4 acl 的 filesystem 中使用该特性
- 使用 NFSv4 ACL 时，尽量把每个用户归类到群组中。并直接设置群组的 NFSv4 ACL 权限，不不是设置单个用户的权限。这样在移除用户权限时只需把用户移出某个群组即可。

## 参考资料

[nfs4_setacl manpage](https://linux.die.net/man/1/nfs4_setfacl)

[nfs4_acl manpage](https://linux.die.net/man/5/nfs4_acl)

[NFS 4 ACL Tool](https://www.server-world.info/en/note?os=CentOS_7&p=nfs&f=5)

[HOWTO: Use NFSv4 ACL](https://www.osc.edu/book/export/html/4523)

[NFSv4 Howto](https://help.ubuntu.com/community/NFSv4Howto)

[网络文件系统](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F)

[Network File System](https://en.wikipedia.org/wiki/Network_File_System)

[Reasons to Migrate from NFS v3 to v4/v4.1](https://archive.fosdem.org/2018/schedule/event/nfs3_to_nfs4/attachments/slides/2702/export/events/attachments/nfs3_to_nfs4/slides/2702/FOSDEM_Presentation_Final_pdf.pdf)