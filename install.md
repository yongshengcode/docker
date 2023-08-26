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
    "registry-mirrors": ["https://yefnfc9c.mirror.aliyuncs.com"],
    "storage-driver": "overlay2"
}
EOF
systemctl daemon-reload && systemctl restart docker.service
```
