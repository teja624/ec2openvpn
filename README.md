ec2openvpn
==========

Openvpn access server on ec2

To do as "https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/"

概述：使用基于openvpn公司提供的openvpn的商业版本openvpn access sever镜像，新建一个micro的instance。
简单配置之后就可以建立了2个账号使用的openvpn服务器。购买license，可以提供更多账号使用。

1. 新建虚拟机：按照向导新建一个免费的micro instance，镜像使用 OpenVPN Access Server 2.0.8，可在社区AMI搜索，eg. ami-e3753ce。
2. 设置参数：设置过程中的参数按照普通虚拟机建设即可，最后虚拟机启动后再配置参数（也可以在高级设置添加user-data，不推荐）。
3. 安全组设置：TCP端口443、943；UDP端口1194.
4. 虚拟机IP配置：选择分配静态IP、Public IP或者设置域名进行连接（当前使用public ip）
5. 登陆虚拟机并设置：使用openvpnas账号和private key-pair，用putty登录虚拟机。登录成功后参照"OpenVPNAS-Setup-Wizard"设置向导进行配置。
    如果需要再次运行此向导，使用sudo ovpn-init --ec2 命令。后续可以使用Admin Web UI修改配置。
6. 登陆Web界面：首先使用sudo passwd openvpn设置密码，然后使用openvpn账号和Public IP在浏览器上登录Openvpn Admin Web UI。
    端口号使用443或者943都可以。
7. 路由检查设置：在AWS EC2界面设置Disabling Source / Dest Check for VPN Instance。
    Right click on the VPN instance, select Change Source/Dest. Check and make sure the status is Disabled.
8. 更新系统及软件：sudo apt-get update && sudo apt-get upgrade。

9. 添加VPN多账号登陆: 
    1）使用root账号添加用户执行命令：
         useradd user1 添加账号
         passwd user1 设置密码
    2）在Admin web UI上设置：
         Authentication选择使用PAM验证，User Permissions页面添加user1的账号。

安装Squid代理服务器
====================
For visit twitter, deploy squid proxy server. 
1) sudo apt-get install squid
2) sudo vim /etc/squid3/squid.conf
   Modify http_access deny all TO http_access allow all
3) Check parse is ok
   squid3 -k parse
4) verify squid service, see 3128 port is licening
   netstat -ntpl
5) setup squid3 on boot (Ubuntu is different)
在/etc/rc.local 文件exit 0 之前的行上添加 service squid3 start

Linux系统的启动调用过程如下：
内核 -> /etc/init/rc-sysint.conf -> /etc/inittab -> etc/init.d/rc -> /etc/rc[0~6].d -> /etc/init.d/rc.local ->/etc/rc.local
虽然在ubuntu后来的版本中默认已经没有了inittab文件，但可以自行添加。
以上的启动过程，并不是代表所有，以Redhat系列的CentOS5为例：
内核 -> /etc/inittab -> /etc/ rc.sysinit (/etc/rc.d/rc.sysinit) -> /etc/rc[?].d (/etc/rc.d/rc[?].d) -> /etc/rc.local(/etc/rc.d/rc.local)

# 8. Set IP proxy on twitter App.
IP: 172.32.x.x (The private ip of openvpn config)
Port: 3128 (Don't use set the port in secret group)





客户端登陆
===================
Android 系统
1，下载 openvpn for android APP 安装
2，下载 ovpn配置文件：通过web端访问https://Public IP:443 使用user1账号下载profile文件
3， 导入ovpn文件，简单设置，即可登陆（具体配置参数研究中）

IOS系统
1，下载openvpn IOS 客户端
2，导入ovpn配置文件
3，设置客户端路由（测试中）
