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

## 参考资料
[nfs4_setacl man page](https://linux.die.net/man/1/nfs4_setfacl)

[NFS 4 ACL Tool](https://www.server-world.info/en/note?os=CentOS_7&p=nfs&f=5)