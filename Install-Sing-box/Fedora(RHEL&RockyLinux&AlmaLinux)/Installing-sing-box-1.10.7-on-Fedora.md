在Fedora(RHEL RockyLinux AlmaLinux)上安装Sing-box服务端
====================================

## 升级版本时先移除sing-box
仅供已安装后升级版本使用，升级安装sing-box会重置
`/usr/lib/systemd/system/sing-box.service` 、`/usr/lib/systemd/system/sing-box@.service`文件，升级版本建议重新安装

```bash
systemctl stop sing-box.service && dnf remove -y sing-box
```

## 下载sing-box
以sing-box 1.10.7版本为例：

```bash
wget https://github.com/SagerNet/sing-box/releases/download/v1.10.7/sing-box_1.10.7_linux_amd64.rpm
```

## 生成sing-box md5
`sing-box 1.10.7` 版本：

```bash
md5sum sing-box_1.10.7_linux_amd64.rpm 
c2dff4bf027e6530c9925c2540790037  sing-box_1.10.7_linux_amd64.rpm
```

## 安装sing-box
`sing-box 1.10.7` 版本：

```bash
dnf install -y sing-box_1.10.7_linux_amd64.rpm
```

```bash
rpm -qpl sing-box_1.10.7_linux_amd64.rpm 
```

## 检查sing-box版本
`sing-box 1.10.7` 版本：

```bash
sing-box version
```

## 增加sing-box用户、用户组
出于安全考虑，以sing-box用户身份运行sing-box

```bash
groupadd --system sing-box
```

```bash
useradd --system \
    --gid sing-box \
    --create-home \
    --home-dir /var/lib/sing-box \
    --shell /usr/sbin/nologin \
    --comment "Sing-box Project" \
    sing-box
```

## 在sing-box.service中增加sing-box用户

`sing-box 1.10.7` 版本：

```bash
sed -i '/\[Service\]/a \
User=sing-box\
Group=sing-box' '/usr/lib/systemd/system/sing-box.service'
```

## 在sing-box@.service中增加sing-box用户

`sing-box 1.10.7` 版本：
```bash
sed -i '/\[Service\]/a \
User=sing-box\
Group=sing-box' '/usr/lib/systemd/system/sing-box@.service'
```

## 创建sing-box IPv4配置文件
如果你的服务器只支持IPv4，用此配置，
执行前先修改"//"里面的内容，替换成你自己的配置

```bash
cat << EOF > /etc/sing-box/config.json
{
  "ntp": {
    "enabled": true,
    "server": "pool.ntp.org",
    "server_port": 123,
    "interval": "180m"
  },
  "inbounds": [
    {
      "type": "shadowtls",
      "tag": "shadowtls-in",
      "listen": "::",
      "listen_port": 443,
      "detour": "shadowsocks-in",
      "version": 3,
      "users": [
        {
          "name": "//your shadowtls username",
          "password": "//your shadowtls password"
        }
      ],
      "handshake": {
        "server": "sns-video-hw.xhscdn.com",
        "server_port": 443
      },
      "strict_mode": true
    },
    {
      "type": "shadowsocks",
      "tag": "shadowsocks-in",
      "listen": "127.0.0.1",
      "listen_port": 8080,
      "method": "2022-blake3-aes-128-gcm",
      "password": "//登录shell或在Windows命令行运行 sing-box generate rand --base64 16 生成",
      "multiplex": {
        "enabled": true,
        "padding": true
      }
    }
  ],
  "outbounds": [
    {
      "type": "direct",
      "tag": "direct"
    }
  ]
}
EOF
```

## 创建sing-box IPv6配置文件
如果你的服务器支持IPv6，用此配置，
执行前先修改"//"里面的内容，替换成你自己的配置
```bash
cat << EOF > /etc/sing-box/config.json
{
  "ntp": {
    "enabled": true,
    "server": "pool.ntp.org",
    "server_port": 123,
    "interval": "180m"
  },
  "inbounds": [
    {
      "type": "shadowtls",
      "tag": "shadowtls-in",
      "listen": "::",
      "listen_port": 443,
      "detour": "shadowsocks-in",
      "version": 3,
      "users": [
        {
          "name": "//your shadowtls username",
          "password": "//your shadowtls password"
        }
      ],
      "handshake": {
        "server": "sns-video-hw.xhscdn.com",
        "server_port": 443
      },
      "strict_mode": true
    },
    {
      "type": "shadowsocks",
      "tag": "shadowsocks-in",
      "listen": "::1",
      "listen_port": 8080,
      "method": "2022-blake3-aes-128-gcm",
      "password": "//登录shell或在Windows命令行运行 sing-box generate rand --base64 16 生成",
      "multiplex": {
        "enabled": true,
        "padding": true
      }
    }
  ],
  "outbounds": [
    {
      "type": "direct",
      "tag": "direct",
      "domain_strategy": "prefer_ipv6"
    }
  ]
}
EOF
```

## 开机自启动sing-box，立即启动sing-box服务，并检查sing-box服务状态
```bash
systemctl enable sing-box && systemctl daemon-reload && systemctl start sing-box.service && systemctl status sing-box.service
```
