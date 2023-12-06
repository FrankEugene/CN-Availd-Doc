# 使用 docker 运行 Availd 验证节点

提示：相比与二进制文件运行，docker 运行节点，对于运维人员要求更改严格，如果没有较好的容器化知识，尽量不要尝试。
接下来，我们进入教程。

### 运行验证节点是服务器性能要求

|    硬件类型    | 最低配置 | 推荐配置  |
| :------------: | :------: | :-------: |
|      内存      |   4GB    |    8G     |
| CPU(amd64/x86) |   2 核   |   4 核    |
|      储存      | 20-40GB  | 200-300GB |

```txt
 推荐系统：Ubuntu 20.04 及以上（教程所选系统为ubuntu22.04）

 最好使用4核8G，200GB的机器，节点的不断运行，数据以及机器性能会提高
```

```shell
#创建节点目录
mkdir -p  /home/availd/availd-node

#接入节点目录
cd /home/availd/availd-node
#更新apt包索引
sudo apt-get update
#添加 Docker 的官方 GPG 密钥
sudo apt-get install ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

chmod a+r /etc/apt/keyrings/docker.gpg
```

设置稳定版仓库

```shell
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

安装 Docker Engine-Community

```shell
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

通过运行镜像来验证 Docker Engine 安装是否成功

```shell
sudo docker run hello-world
```

你会看到：

```txt
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

说明 docker 已经安装完成。

启动验证者节点镜像

```shell
docker run -v $(pwd)/state:/da/state:rw \
-p 30332:30333 -p 9614:9615 -p 9943:9944 \
-d --restart unless-stopped availj/avail:v1.8.0.3 \
--chain goldberg \
--validator --name "Your Validator Name " -d /da/state

```

查看镜像是否正常启动：

```shell
docker ps
```

```shell
CONTAINER ID   IMAGE                   COMMAND      ....
ee1adbd69544   availj/avail:v1.8.0.3   "/usr/local/bin/data…" ....
```

查看日志 （容器 id 需要换成你自己的）

```shell
docker logs  ee1adbd69544
```

输出如下：

```shell
2023-12-06 10:28:10 Avail Node
2023-12-06 10:28:10 ✌️  version 1.8.3-a0820931850
2023-12-06 10:28:10 ❤️  by Anonymous, 2017-2023
2023-12-06 10:28:10 📋 Chain specification: Avail Goldberg Testnet
2023-12-06 10:28:10 🏷  Node name: Your Validator Name
2023-12-06 10:28:10 👤 Role: AUTHORITY
2023-12-06 10:28:10 💾 Database: RocksDb at /da/state/chains/avail_goldberg_testnet/db/full
2023-12-06 10:28:23 🔨 Initializing Genesis block/state (state: 0x6bc7…ec83, header-hash: 0x6f09…a7ae)
2023-12-06 10:28:23 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.
2023-12-06 10:28:31 👶 Creating empty BABE epoch changes on what appears to be first startup.
2023-12-06 10:28:31 🏷  Local node identity is: 12D3KooWDaxLnp7gFHayaqgxqAwiRqXy8bAMVAydU36rFP6UWuej
2023-12-06 10:28:31 Prometheus metrics extended with avail metrics

```

如果你看到以上输出，说明你的验证节点已经启动。
恭喜 🎉🎉🎉🎉

你会发现 docker 运行一个验证节点是如此简单。
但是相对应的需要你具备相当的 docker 知识，以防在出现问题时，能够去处理。
因此我依旧是推荐你采用二机制安装方案，具体使用那种需要你自己决定。
