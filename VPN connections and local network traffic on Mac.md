# 在MacOS上如何使VPN连接的情况下，本地网络可以连接和使用
## 临时方法
Mac有一系列简洁的网络工具可以用来解决这个问题，最简单的方法是route：
```
# route <target> <gateway>
sudo route -nv add -net 10.0 192.168.1.1
```
使用 -net（或-host），10.0是网络地址，扩展为10.0.0.0，因此整个子网10.0.*将被路由到网关192.168.1.1
添加route的方法是临时的，系统一旦重启，配置就失效，恢复到以前。
## 永久有效的方法
对于永久有效的方法，mac 自带了以下networksetup工具：
```
> networksetup -listallnetworkservices

An asterisk (*) denotes that a network service is disabled.
Ethernet
USB 10/100/1000 LAN
Wi-Fi
Thunderbolt Bridge
NordVPN NordLynx
```
我们想要的命令是`-setadditionalroutes`。此命令接受一个接口，然后接受三元组`<target> <netmask> <gateway>`
```
networksetup -setadditionalroutes "Ethernet" 10.0.5.0 255.255.255.0 192.168.1.1
```
对用于连接的所有接口重复此操作，即可完成。
要添加多条路线，您必须同时以三元组的形式指定它们：
```
networksetup -setadditionalroutes "Ethernet" 192.168.0.0 255.255.0.0 192.168.1.1 10.0.5.0 255.255.255.0 192.168.1.1
```
现在来自`192.168.*` and `10.0.5.*`的网络都可以通讯和互连。
