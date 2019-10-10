```
需要验证的东西：
需要有完整的分析和验证过程，得到一份简单明了的报告
1. 能否修改vap的mac地址  -  得到一个结论
wlanconfig sta0 create wlandev wifi0 wlanmode sta bssid
ifconfig athx ip up/down
ifconfig athx hwether mac

2. 是否只能创建一个sta？
并且当sta为first vap时，不能再创建其他vap了。
如果工作站vap是创建的第一个vap，则不允许创建其他的vap。如果创建的第一个vap处于ap（主）模式，则允许创建一个工作站vap


3. 关联
使用一个sta去关联ap，能否ping通？
使用下面的流程，可以通过抓包分析。是没有问题的。

能否创建两个sta？并且让他们工作？
创建两个sta去关联，分开关联和同时关联
ping命令。其中需要了解ping命令的格式。

成为网络方面的小能手
解除关联是否有效
iwconfig wlan0 ap 00:00:00:00:00:00
sleep 1
iwpriv wlan0 deepsleep 1
解除关联的帧，能否抓到，接触关联的帧？
关联的抓包分析，的拓扑是怎么样的？
解除关联之后能否关联其他的ap。

4. ap和sta能否同时工作。
ap工作，能够连接网页
sta工作，能够关联其他ap
```



```
iwpriv ra0 set [parameters] = [val]
iwpriv ra0 set NetworkType=Infra
iwpriv ra0 set AuthMode=OPEN
iwpriv ra0 set EncrypType=-None
iwpriv ra0 set SSID="ap's ssid"

yes， that's ok!
iwlist ra0 scanning

```



```
wlanconfig athx list scan
iwlist athx ap/scan

ifconfig athx ip up/down
ifconfig athx hwether mac

需要通过修改驱动，让芯片支持更多的vap
```

```
进一步明确流程
1. ssu连接有线端口
2. ap连接有线端口，作为服务器端
3. ssu的虚拟sta配置为客户端
4. ssu设备具有web服务器，该服务器还提供GUI
5. 如果在管理网络上启用了dhcp，则ct520将自动获取ip地址，如果dhcp不可用，则IP地址将设置为192.168.1.254
6. 我的想法是通过主机名找ssu设备，你在PC上能够找到ssu设备，然后连接到ssu
7. 管理wiphy0设备，配置为正确的信道，国家/地区代码。选择一个或多个virtual sta界面，然后单击”修改“，输入正确的ip地址信息，ssid和wpa/wpa2密码（若启用）。应用这些更改后，vta接口与ap关联并准备发送流量。使用“创建”按钮，每台ssu最多可以创建32个virtual station接口。
8. 正确配置接口后，你可以单击第3成，并配置/修改/启动/停止使用虚拟sta和有线以太网接口的特定流量模式。在大多数情况下，你希望iperf端点之一在有线接口上，而另一个端点在wifi vsta接口上，在两个虚拟sta之间生成的流量也是有效的？
9. 存在自动化测试和报告。自动话关键测试样例。
10 单击提交后，将立即进行任何GUI修改。

```

```
standard configuration
通用接口配置（用于连接管理的通用配置）
使能开关启动/关闭
ip
mask
gateway
wifi bridge？
dns
MAC addr
dhcp vendor id： dhcp供应商id用来选择dhcp server可忽略
dhcp client id：默认情况下MAC地址作为dhcp client id发送，但是网络管理员可以覆盖此设置，病态每台计算机上输入一系列自定义的DHCP client id作为主机名用来识别。可显示为主机名

wifi（manage ap）配置，这些都可以配置
ssid：
认证方式：选择+key
mode？
rate？
disable ht40,enable vht160, disable sgi
```

```
接口名：sta001
扫描:
mac地址：
ap's ssid:
bssid
dhcp client id:ssu_sta001
关联
解除关联
iwconfig wlan0 ap 00:00:00:00:00:00
sleep 1
iwpriv wlan0 deepsleep 1

是否存在自动关联的可能
```

```
配置iperf终端
ip或者端口
endpoint A， endpoint B

或者单纯的ping？
ping功能详解
```

```
madwifi 说明文档，根据该文档进行单独的测试验证项目。
需要测试：
创建和销毁vap是通过madwifi工具目录中的wlanconfig实用程序返回的命令行。
每个linux网络设备均包含一个前缀，后跟一个数字，指示网络设备的设备号。可作为前缀和网络设备的设备号。
http://madwifi-project.org/wiki/UserDocs/UsersGuide
iwconfig eth0 ap 00:03:7f:03:a0:0d指定关联bssid
可以指定bssid -bssid nobeacon

设备上只能存在一个工作站vap？
如果工作站vap是创建的第一个vap，则不允许创建其他的vap。如果创建的第一个vap处于ap（主）模式，则允许创建一个工作站vap。在这种情况下，可以在站点vap之后创建其他 ap vap。当ap和工作站vap共存时，在创建工作站时必须使用nosbeacon标志。该标志禁止使用硬件信标定时器进行站模式操作。创建多个vap通常意味着每个vap 的mac地址是不同的，但是如果使用-bassid标志，则会为正在创建的vap克隆基础无线设备的mac地址。

wlanconfig create/destroy [bssid] [-bssid] [nosbeacon]
ifconfig ath0 down hw ether 001122334455
ifconfig ath0 up
因为是创建的第一个vap，创建的第一个vap与基础设备的mac地址相同。第一台设备将具有从底层设备获取的复制mac地址。后面的设备将具有由基础设备的mac地址形成的虚拟mac地址。

如果我们只希望将该系统用作工作站sta，则在加载驱动程序后，我们将创建一个单工作站vap。
wlan create 指定ssid，进行相应的加载，我觉得是可以实现的
通常建议在后面创建时需要指定bssid参数。以指示应该使用唯一的mac。虽然这样的mac现在可以正确的完成。为防止使用基础设备的mac，可以指定uniquebssid参数，并且将从硬件制造商分配的mac派生的唯一mac地址池中为所有vap分配mac地址。是可以做的，却多次推辞。
具体做法：
```



```
iwprive ath0 mode 11a //可选择wifi模式
iw命令
iwevent -f -t
iw dev wlan0 link


```

