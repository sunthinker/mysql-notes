### MYSQL笔记
#### 迁移目录的权限问题
##### 问题一[seLinux]
1. 原因分析
    * 一般redhat系的Linux，其selinux设置默认为开启状态。
    * 它能够使你的mysql无法在新目标位置进行mysql_install_db的操作。
1. 解决办法
    + 编辑文件/etc/selinux/config
    + 将SELINUX修改为disable(SELINUX=disable)
    + 重启
##### 问题二[apparmor]
1. 原因分析
    + 一般debain系的Linux，其apparmor像selinux一样会影响mysql的新目标位置的操作权限。
    + 在 **"/etc/apparmor.d/usr/.sbin.mysqld"** 这个文件中，有以下两行规定了mysql使用的数据文件路径权限。其中 **"/var/lib/mysql/"** 就是mysql数据目录。
    + /var/lib/mysql/ r,
    + /var/lib/mysql/** rwk,
2. 解决办法
    + 在以上述文件中，新增两条记录（记录你的迁移目录地址）
    + /your-dir/mysql../ r,
    + /your-dir/mysql../ ** rwk,
    + 重启apparmor，/etc/inid.d/apparmor restart
#### 设置root密码
1. 登录mysql
   ```sql
   mysql -uroot -p
   ```
2. 执行以下命令
    ```sql
    use mysql;
    ALTER user 'root'@'localhost' IDENTIFIED BY 'your password';
    FLUSH privileges;
    ```
#### 允许远程访问
1. 登录mysql
    ```sql
    mysql -uroot -p
    ```
2. 执行以下命令
    ```bash
    update user set host='%' where user='root';
    FLUSH privileges;
    ```
