在Debian(Ubuntu)上安装Sing-Box服务端
==================================

## 升级版本时先移除sing-box
仅供已安装后升级版本使用，因升级安装sing-box会重置
/usr/lib/systemd/system/sing-box.service 、 /usr/lib/systemd/system/sing-box@.service 文件，
升级版本建议重新安装

```bash
systemctl stop sing-box.service && apt-get remove -y sing-box
```

## 下载sing-box
```bash
wget https://github.com/SagerNet/sing-box/releases/download/v1.10.7/sing-box_1.10.7_linux_amd64.deb
```

## 生成sing-box md5
```bash
md5sum sing-box_1.10.7_linux_amd64.deb 
c84e04c0028e17dd26eba630ab2e27d1  sing-box_1.10.7_linux_amd64.deb
```

## 安装sing-box
```bash
dpkg -i sing-box_1.10.7_linux_amd64.deb && dpkg -c sing-box_1.10.7_linux_amd64.deb
```

## 检查sing-box版本
```bash
sing-box version
```

## 出于安全考虑，增加sing-box用户、用户组
以sing-box用户身份运行sing-box
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

## 创建由sing-box用户运行的sing-box.service
```bash
cat << EOF > /usr/lib/systemd/system/sing-box.service
[Unit]
Description=sing-box service
Documentation=https://sing-box.sagernet.org
After=network.target nss-lookup.target network-online.target

[Service]
User=sing-box
Group=sing-box
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_SYS_PTRACE CAP_DAC_READ_SEARCH
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_SYS_PTRACE CAP_DAC_READ_SEARCH
ExecStart=/usr/bin/sing-box -D /var/lib/sing-box -C /etc/sing-box run
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=10s
LimitNOFILE=infinity

[Install]
WantedBy=multi-user.target
EOF
```

## 创建由sing-box用户运行的sing-box@.service
```bash
cat << EOF > /usr/lib/systemd/system/sing-box@.service
[Unit]
Description=sing-box service
Documentation=https://sing-box.sagernet.org
After=network.target nss-lookup.target network-online.target

[Service]
User=sing-box
Group=sing-box
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_SYS_PTRACE CAP_DAC_READ_SEARCH
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_SYS_PTRACE CAP_DAC_READ_SEARCH
ExecStart=/usr/bin/sing-box -D /var/lib/sing-box-%i -c /etc/sing-box/%i.json run
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=10s
LimitNOFILE=infinity

[Install]
WantedBy=multi-user.target
EOF
```

## 创建sing-box IPv4配置文件
如果你的服务器只支持IPv4，用此配置，
执行前先修改"//"里面的值，替换成你自己的配置
```bash
cat << EOF > /etc/sing-box/config.json
{
  "ntp": {
    "enabled": true,
    "server": "pool.ntp.org",
    "server_port": 123,
    "interval": "60m"
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
      "password": "//shell 运行 sing-box generate rand --base64 16 生成",
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
执行前先修改"//"里面的值，替换成你自己的配置
```bash
cat << EOF > /etc/sing-box/config.json
{
  "ntp": {
    "enabled": true,
    "server": "pool.ntp.org",
    "server_port": 123,
    "interval": "60m"
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
      "password": "//shell 运行 sing-box generate rand --base64 16 生成",
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
