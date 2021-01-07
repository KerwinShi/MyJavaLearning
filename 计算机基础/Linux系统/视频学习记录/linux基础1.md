1.安装  
[虚拟机VMware](../虚拟机安装.md)  
VMware16Pro永久激活key密钥  
ZF3R0-FHED2-M80TY-8QYGC-NPKYF

2.使用  
[常见文件路径及其对应的含义](../linux的文件权限与目录配置.md)  

3.远程连接工具  
Xshell：访问服务器    
Xftp：文件  

或者类似的：  
SecureCRT和SecureFX  

su 与 sudo的区别  
su 切换用户，当普通用户切换为root用户的时候需要输入root密码，root切换普通用户的时候不需要输入密码  
sudo 赋予普通用户管理员权限（需要在sudoers里面进行配置，某个普通用户sudo可以执行哪些管理员权限的操作）此时，输入当前普通用户的密码确认即可



SSH
secure shell  
RSA：公钥和私钥   ssh免密连接（github远程仓库的连接其实也是一样的原理）  
`ssh -keygen`生产密钥对  ssh-copy-id ip地址 将公钥拷贝到ip指定的服务器上的合适位置，以后访问就不需要输入密码了 

yum  （类似于maven）软件的中央仓库,可以安装目标软件及其依赖的软件  
`yum list | grep jdk`搜索jdk相关软件  
`yum install XXXX -y`免提示安装软件XXXX  
`yum`查看其它参数及其含义  

XXXXX.tar.gz  
解压：`tar -zxvf XXXXX.tar.gz -C 目标路径`


安装完操作系统后，就需要安装常用的软件：
JDK 安装包解压，配置环境变量
Tomcat 安装包解压，启动Tomcat`./startup.sh`，查看是否已启动`netstat -nltp`
