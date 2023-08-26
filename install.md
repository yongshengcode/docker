# Install
## Official documents links
https://docs.docker.com/engine/install/
https://docs.docker.com/engine/install/ubuntu/

## My steps

### Install Virtual box and ubuntu 20

### Install docer-ce (community edition) - https://developer.aliyun.com/mirror/docker-ce

```shell
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get -y update
apt-cache madison docker-ce | less
sudo apt-get -y install docker-ce
```
### Update config for docker

```shell
iptables -P FORWARD ACCEPT
sed -i '/[[Service]]/a ExecStartPost=/usr/sbin/iptables -P FORWARD ACCEPT' /lib/systemd/system/docker.service
```

```shell
cat > /etc/docker/daemon.json << EOF
{
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
       "max-size": "100m",
       "max-file": "3"
    },
    "max-concurrent-downloads": 10,
    "insecure-registries": ["0.0.0.0/0"],
    "max-concurrent-uploads": 10,
    "registry-mirrors": ["https://9cpn8tt6.mirror.aliyuncs.com"],
    "storage-driver": "overlay2"
}
EOF
systemctl daemon-reload && systemctl restart docker.service
```

### Conifg proxy
docker pull
在执行docker pull时，是由守护进程dockerd来执行。因此，代理需要配在dockerd的环境中。而这个环境，则是受systemd所管控，因此实际是systemd的配置。
```shell
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo touch /etc/systemd/system/docker.service.d/proxy.conf
```
在这个proxy.conf文件（可以是任意*.conf的形式）中，添加以下内容：
```shell
[Service]
Environment="HTTP_PROXY=http://192.168.1.8:10809/"
Environment="HTTPS_PROXY=http://192.168.1.8:10809/"
Environment="NO_PROXY=localhost,127.0.0.1,.example.com"
```
这个代理是我本机用来科学上网的代理

docker cli
~/.docker/config.json

或者设置daemon.json
https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file

Restart
```shell
systemctl daemon-reload
systemctl restart docker
```

