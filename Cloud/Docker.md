# Docker使用
`docker search [目标image名称]`  搜索需要的image
`docker images`  列出所有image
`docker image pull [image名称]`  下载image
`docker run -itd --name [自定义名称] [image名称]`  启动image -d 后台执行
`docker run -itd --rm --name test ubuntu` 这个命令无需提前下载image，可以直接执行，--rm 代表当结束该容器后删除容器，当时不删除image
`docker ps`  列出正在执行的容器
`docker container list`
`docker exec -it [容器名称] bash`     进入容器 -it 代表含有可互动命令行，最后bash代表使用bash作为命令行
`sudo apt install software-properties-common`  有些系统会缺乏运行环境，使用这个命令安装
#主体/Docker  #方面/基础


# Docker网络
**Bridge网络**
docker中最常见的网络类型，每一次开启docker的容器都会自动使用，为默认网络类型
`NETWORK ID     NAME      DRIVER    SCOPE`
`786c088076fa   bridge    bridge    local`
每一个容器都有各自的虚拟网络接口，该接口会与docker0虚拟网卡相连`bridge link`该命令可以查看
`veth78f60e5@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default `
`docker network ls`   使用docker命令类出所有网络类型
`docker inspect bridge`     查看bridge网络配置  inspect命令可以查看任何配置，可以查看容器的，或者网络配置
**Host网络**
重要的网络配置，主要用于VPN类似工具
`docker run -itd --rm --network host --name test ubuntu`   配置Host网络的容器相当与宿主机的一个应用或者进程，与宿主机共享端口
**MacVlan网络**
每一个容器会拥有自己的mac地址，在网络上如同一个独立的主机
`docker network create -d macvlan --subnet 192.168.2.0/24 --gateway 192.168.2.1 -o parent=enp2s0 macvlan`  创建macvlan网络，--subnet子网 ，--gateway 当前网络的网关 ，parent=enp2s0指定使用那个物理网卡
`docker run -itd --rm --network macvlan --ip 192.168.2.210 --name test ubuntu` 需要使用静态ip
#主体/Docker  #方面/配置
