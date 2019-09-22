1.第一步需要安装PPTP，以用来提供VPN服务.
```
sudo apt-get install pptpd
```
2.装好了之后我们需要进行配置一下以让它可以使用.
```
sudo vi /etc/pptpd.conf
```
    取消掉以下 2 行的注释（192.168.0.1改为你的服务器固定IP,234-238根据需要改变区间）：
```
localip 192.168.0.1
remoteip 192.168.0.234-238,192.168.0.245
```
3.然后我们需要分配账号给自己使用.
```
sudo vi /etc/ppp/chap-secrets
```
    这个是用户列表文件

    在里面添加账户按如下格式
```
username  pptpd  "password"  *
```
    username为你的用户名，password为你的密码，密码用引号引起,*号表示允许在任意IP连接到服务

4.至此服务弄好了，如果你
```
sudo service pptpd restart
```
    一下，就应该已经能连接到该VPN了，但是连接了之后会发现还访问不了外网。然后我们需要让他能访问外网。

    首先：
```
sudo vi /etc/ppp/pptpd-options
```
    找到ms-dns，取消掉注释，改成你喜欢的DNS比如：8.8.8.8,8.8.4.4

5.然后我们要开启内核IP转发
```
sudo vi /etc/sysctl.conf
```
    取消掉 net.ipv4.ip_forward=1 这一行的注释.

运行：sysctl -p 让上面的修个立即生效

6.然后我们需要安装iptables，用来实现请求的NAT转发
```
sudo apt-get install iptables
```
    然后开启NAT转发. （注意：eth0,是你的外网网卡，ifconfig获取）
```
sudo iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
```
    192.168.0.0/24是你在上面设置的IP段，remoteip所包含ip要在这个段内

7.最后，我们需要重启服务，让配置生效 
```
sudo service pptpd restart
```
