虚拟机 NAT 模式下的网络配置问题：
主机为 Windows 系统，VMware 虚拟机里安装 Linux 操作系统，安装完 VMware 后，主机的网络连接里会出现两个新的连接，即 VMware Network Adapter VMnet1 和 VMware Network Adapter VMnet8。虚拟机里安装的系统就是靠这两个虚拟网卡来联网的。使用 VMware 提供的 NAT 和 DHCP 服务，虚拟机使用主机中的虚拟网卡 VMnet8 作为网关，并且 TCP/IP 设置需遵循程序中关于 vmnet8 的 DHCP 和 NAT 设置，如 IP 必须在其 DHCP 设置的 StartIP 和 EndIP 之间，网关需使用其 NAT 设置的 Gateway IP。

NAT 方式可以实现虚拟机<--->主机通信，但是只能实现虚拟机--->互联网，即只能让虚拟机访问互联网，但是若想把虚拟机作为服务器，让互联网访问，不可以。

如果要让 vmnet8 的 DHCP 自动给虚拟机分配 IP，则只需两部即可实现 NAT 模式：
1. 设置 VMware 网络连接方式为 NAT
2. 在虚拟机里，Linux 网络设置，只需选择自动获取 IP 就可以了

也可以手动配置虚拟机的固定 IP：
1. 通过 Windows 的网络连接可以设定 VMnet8 使用的 IP 地址
2. 通过 VMware 的 编辑->虚拟网络编辑器 可以把“使用本地 DHCP 服务将 IP 地址分配给虚拟机”勾掉，然后勾上 “将主机虚拟适配器连接到此网络”，然后设定子网IP，最后点击 NAT设置，可以设置 NAT 模式下使用的网关
3. 在虚拟机中通过修改 /etc/network/interface 把 IP 地址设置为固定（static），并把 IP 设置为与 vmnet8 地址同网段，然后设置 netmask，gateway（设置为之前 NAT模式 设定的网关）
这样就可以实现手动设置 NAT 下的固定 IP 地址了。