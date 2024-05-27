 踩坑总结：

```plain
记录下踩得坑： 做技术或者学技术，一定要心态好
1. python 版本太新 3.12，导致很多问题，后来回退到了 3.11
2. 依赖包安装的版本问题，默认都会安装最新的，但是这部分会存在不兼容的问题
3. 依赖包安装不完整，使用 requirement.txt 有些依赖包始终安装不了，
最终还是手动安装了
4. 不能使用 root 运行，无奈要从头创建服务器端的用户
5. python 建议在虚拟环境中运行
6. 数据库的远程连接，建议先不处理，就是用 localhost 来连接即可
7. 多使用现在的 AI 工具，很多问题能够给你解答
8. 部署成功后，访问后出现了样式错误 style error，看官方论坛有很多人问，
试了 sassc 和 libsass 都没搞好，最后更新了一下源码，成功了
```


采用 ubuntu OS 进行云端部署

1. 创建用户 odoo
```plain
sudo adduser odoo

设置密码
安装后 eixt 退出回到 root

如果要回到 odoo  登入 root 后 使用 sudo su odoo 切换用户
```
 
2. 安装 python3.11 及虚拟环境相关
```plain
sudo apt update
sudo apt install -y python3.11 python3.11-venv
```

3. 安装数据库
```plain
1. 打开一个新的终端窗口。
2. 切换到 'postgres' 用户，输入命令 sudo -i -u postgres
3. 启动命令行接口 psql，输入命令 psql
4. 然后，可以使用 CREATE USER 命令创建一个新用户。
例如，要创建一个名为 myuser 的新用户，你可以输入以下命令：
CREATE USER odoo WITH PASSWORD 'odoo123';
在这里，'odoo' 是你要创建的新用户的用户名，'odoo' 是新用户的密码。
 
5. 创建数据库并且赋予所有权限
CREATE DATABASE  odoo_test;
GRANT ALL PRIVILEGES ON DATABASE odoo_test TO odoo;

ALTER USER odoo WITH CREATEDB;  /允许用户创建数据库

6. 通过 psql -h localhost -U odoo -d odoo_test 
输入密码后应该可以连接到数据库了
```

4. 安装 odoo 源代码
```plain
1. 切换到 odoo 用户

2. 创建文件夹：
mkdir ~/app/odoo

cd odoo

3. clone 源代码
git clone https://github.com/danielyangfei/odoo.git --depth 1



```


5. 创建 python 虚拟环境
```plain
1. 退出到外层 odoo 文件夹，安装虚拟环境
cd ..  
python3.11 -m venv venv

2. 激活虚拟环境
source venv/bin/activate
```
```plain
![image](https://github.com/danielyangfei/Odoo-Learning/assets/9975680/18aeda19-4e93-4f11-ac65-061f45d93e73)


此时目录结构：

~/app/odoo/
    ├── odoo/
    │   ├── odoo-bin
    │   ├── ...
    │   └── odoo.conf
    └── venv/
```

6. 安装 odoo 运行相关的依赖包
```plain
cd odoo
 pip3 install --upgrade --force-reinstall -r requirements.txt

安装依赖的过程中可能会出现一些系统还没有安装的依赖  
百度或者（AI）查询安装方法。一般安装要再 root 用户下，
安装好后切换用户，重新进入到 odoo 用户中，激活虚拟环境，重新安装 odoo 的依赖包

部分依赖包可能还是需要手动安装的方式
```

7 . 配置文件

```plain
创建并编辑 ~/odoo/odoo/odoo.conf：

1. vim odoo.conf
2. 输入 i 插入：
[options]
admin_passwd = your_admin_password
addons_path = /path/to/odoo/addons,/path/to/custom/addons
db_user = odoo
db_password = your_db_password
logfile = /var/log/odoo/odoo.log
http_interface = 0.0.0.0
http_port = 8069

3 esc 退出编辑 ：wq 保存退出

替换上面的管理员密码
```

8. 运行 odoo
```plain
./odoo-bin -c odoo.conf

可能出现依赖包不完全 或者版本不对的情况  比如 lxml  就手动重新安装了较低版本的
这里的 password 是 odoo.conf 里面的密码，第一次以为是设置一个新密码 输了半天
如果重新打开 odoo.conf 的话会看到系统自动将明文密码进行了加密
```
![image](https://github.com/danielyangfei/Odoo-Learning/assets/9975680/6af4445c-925b-49a5-83e3-5b4eb6d8f8dd)



创建好数据库之后，选择数据库 就可以正常登录了，出现了样式不对劲的问题。 这里废了老大的劲，后来重新获取了最新的源码，然后运行起来就好了

![image](https://github.com/danielyangfei/Odoo-Learning/assets/9975680/17ed45e5-cc10-43b8-8395-b1c609b3b101)


