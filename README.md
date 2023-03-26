# OpenMediaVault-installation
开源nas系统OpenMediaVault安装注意事项

下面的介绍以openmediavault_5.6.13-amd64.iso镜像为准，其它版本应该也是大同小异

本质上OpenMediaVault的安装ISO文件是一个完整的Debian操作系统及OpenMediaVault软件的集合体，安装OpenMediaVault实际上就是安装Debian操作系统，安装过程确实也是与安装正常Debian操作系统一样，所以需要注意Debian安装过程的一些设置选项

由于Debian操作系统在全球的“Mirror”镜像源库服务器很少，并且下载速度比较慢，所以安装过程中出现提示选择国家地区时，需要仔细考虑根据用户自身所处的国家，选择相应的国家或地区，如HongKong或Japan等，然后选择具体服务器时，应该选择对应国家或地区的域名后缀服务器，如.HK或.JP等，如果选择一个服务器后经过安装程序检索出错，应该是回到国家地区选择项，从头开始选择过程，直到选择的镜像源库服务器能够访问为止

由于这个OpenMediaVault的安装ISO经过一些改造，所以没有正常Debian操作系统安装时选择一般用户名的过程，只有设置root密码的过程，所以系统安装完成后是可以直接使用root用户进行ssh登录的

安装完成后，使用root用户进行ssh登录，进入终端界面后，可以进行系统更新，但更新前必须更改IP访问的优先级，由于OpenMediaVault的安装ISO设置了IPv6优先，导致系统无论查询任何DNS都是使用IPv6优先，但大部分用户的局域网都只有IPv4，这时无论系统访问任何域名，都会优先使用IPv6地址进行访问，导致系统长时间吊死，直到访问超时才会使用IPv4地址重新访问，所以下面是更改IP访问优先级的配置

配置IPv4优先，编辑 /etc/gai.conf 文件

\#nano /etc/gai.conf

在末尾添加下面这行配置

precedence ::ffff:0:0/96  100

保存并退出，配置会马上生效，不需要重启服务


\#nano /etc/sysctl.conf

在末尾添加下面这些配置

net.ipv6.conf.all.disable_ipv6=1 
net.ipv6.conf.default.disable_ipv6=1 
net.ipv6.conf.lo.disable_ipv6=1

保存后退出

使设置生效
\#sysctl -p

为确保上面设置生效，最好是重启一次机器

重启完成后使用root用户进行ssh登录，进入终端界面后，可以进行系统更新

\#apt update && apt upgrade -y


更新完成后就可以登录OpenMediaVault的web管理界面了


OpenMediaVault的权限管理也有需要注意的地方，按照经验，所有的用户特权、目录特权、目录ACL都应该是显式打勾需要定义的权限，千万不要使用系统缺省方式，也就是所有权限选项不打勾的方式

另外在OpenMediaVault里面添加用户名，千万不要与局域网里面的windows用户的登录名字重名，否则windows用户登录一次后，windows会使用同样的用户名但以windows的登录凭证不断自动登录OpenMediaVault，导致windows用户不能在同一台windows机器上切换不同的nas用户进行登录


