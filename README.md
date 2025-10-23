## 项目简介

项目面向自建或需要修改配置的用户

一般普遍用法是`sing-box`服务端运行在各Linux发行版上，电脑、手机、平板、路由器、安卓电视、安卓电视盒子等作为客户端

配置样例服务端是`sing-box`实现的`ShadowTLS v3`协议，各平台`sing-box`作为客户端，后续添加`AnyTLS + Reality`协议，需要`sing-box ≥ 1.12.0`

`sing-box`作为服务端或是客户端取决于你的配置文件，它二进制文件不区分服务端客户端

修改配置需要有基本的JSON语法基础，使用其他协议参考官方文档修改服务端的入站、客户端的出站，样例默认添加了广告屏蔽规则集，不需要可删除，sing-box官方文档地址：`https://sing-box.sagernet.org/zh/configuration`

服务端和客户端配置文件默认文件名`config.json`，使用默认名需要修改为`config.json`

配置样例经过实际验证，并且对应版本可用。配置样例并非完美，不足之处欢迎提出意见




  ## 目录和文件解释

sing-box-GeoIP-Geosite：指sing-box ≤ 1.7.8版本使用GeoIP、Geosite进行国内外、私有地址分流。sing-box ≤ 1.7.8的版本可以使用，已在sing-box 1.8.0及以后的版本废弃

sing-box-rule-set：指从sing-box 1.8.0版本后使用规则集方式进行国内外、私有地址分流，更加灵活

文件名结尾包含IPv4：服务端和客户端都使用IPv4地址进行配置，受Golang的特性，监听IPv4地址也使用`"::"`形式，它等同于`"0.0.0.0"`，IPv4和IPv6服务端都是`["listen": "::"]`，为了和官方一致不做修改

文件名结尾包含IPv6：服务端和客户端都使用IPv6地址进行配置，IPv6的服务端兼容IPv4客户端

文件名包含`mixed`表示使用`Mixed`模式，官方描述：`mixed入站是一个 socks4, socks4a, socks5 和 http 服务器。`

文件名包含`tun`表示使用`Tun`模式，它在目标系统上创建虚拟网卡接管流量，在Windows上需要管理员权限



  ## 重要提示

[__安全风险提示__] 使用非GREASE ECH加密套件来缓解uTLS中的Chrome指纹Bug：
如果你的sing-box < 1.12.10 或 sing-box < 1.13.0-alpha.22，那么需要修改你全平台本地配置文件中 "fingerprint" 为使用非GREASE ECH加密方式的指纹，目前可用的是 "firefox"、"ios"。否则可能你会被检测到使用模拟 Chrome 指纹，这是一种安全风险，uTLS v1.8.1已经修复。详见：refraction-networking/utls#375

为了防止DNS解析泄露，Tun模式启用了防DNS泄漏的规则，防止国内第三者知道你访问了哪些网站。使用`https://ipleak.net` 、`https://browserleaks.com/dns` 来检测现有软件的DNS是否泄露

如果你不完全理解官方文档，尽量不要自己配置DNS分流，配置不合理会造成DNS泄露

Shadowrocket（AKA “小火箭”）会泄露你的DNS记录，它只在配置文件中让`ipleak`、`browserleaks`等检测网站走代理，让你看起来没有DNS泄漏，这是掩耳盗铃的做法，具体可以查看它的配置文件

[`1.11.0版本已废弃`]不要在客户端入站`[inbounds]`监听字段中配置`"domain_strategy"`，可选值：`prefer_ipv4 prefer_ipv6 ipv4_only ipv6_only`。如果设置，域名在请求发出之前抢先在本地解析成IP，这可能会造成DNS泄露，使你的浏览隐私不再安全

除DNS泄漏外，还需要注意WebRTC泄漏，Tun模式已启用防WebRTC泄漏。Mixed模式无法防止WebRTC泄漏，需要额外步骤，浏览器禁用WebRTC参考：

  ```
How to Disable WebRTC
  A.WebRTC in Firefox
    To disable WebRTC in Firefox:
      1.Type `about:config` in the address bar and press Enter.
      2.In the search bar, type `media.peerconnection.enabled` and `double-click` the preference to set its value to false.
  B.WebRTC in Chrome
      1.The recommended solution to limit the risk of IP leakage via WebRTC is to use the official Google extension called [WebRTC Network Limiter](https://chrome.google.com/webstore/detail/webrtc-network-limiter/npeicpdbkakmehahjeeohfdhnlpdklia), which provides a range of options with varying levels of protection.
  ```

配置文件使用了规则集方式进行国内外、私有地址分流。国外走代理，国内和私有IP直连，节省服务器流量并提高国内访问速度

因`SagerNet`官方的[geosite-cn.srs](https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-cn.srs)域名不全，Bypass后不在列表内的cn网站也走代理，有些政府事业网站屏蔽了海外IP导致无法访问。为了解决这一问题，`geosite-category-ads-all`、`geosite-cn`、`geoip-cn`全部替换成`MetaCubeX` [meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat/tree/sing)库中的srs源

配置文件部分字段为空表示使用内置默认值，方便你完全理解官方文档后按需修改

在Windows上不要使用记事本编辑配置文件，Windows(CR LF)和Linux(LF)、MacOS(CR)文本换行符不一致，导致服务端读取配置时出现问题，使用专业文本工具编辑，如果发现配置文件在Git提交/拉取时自动转换成`CR LF`换行符，必要时需要手动改成`LF`换行符



## 兼容性提示

[`已解决：根据反馈，小米 MIUI 14.0.25 (Android 13)使用sing-box 1.9.3以上版本会出现异常，包括不限于APP卡死、崩溃`]。调查后sing-box从某个版本起需要定位权限，用以获取网络相关配置，国产系统限制了定位权限，AOSP没此问题

在Windows 10 LTSC Tun模式中，sing-box ≤ 1.10.0，如果出现DNS解析正常，出站异常无法连接/打开失败，`stack`需设置为：`"stack": "gvisor"`，同时可能IPv6支持不佳，如需使用IPv6更新sing-box到1.11.0以上



## 在Windows上使用sing-box

### Mixed模式

1. 在 `https://github.com/SagerNet/sing-box/releases` 下载符合你系统的正式版本
2. 想要Windows最小化运行，新建bat文件，假设名为`Run-sing-box.bat`
3. 如果你下载的包是sing-box-x.x.x-windows-amd64.zip
4. 假设sing-box-x.x.x-windows-amd64在 D:\Program Files 文件夹中
5. 配置文件config.json和sing-box.exe程序在同一目录

```bat
cd /D "D:\Program Files\sing-box-x.x.x-windows-amd64"
start /min sing-box.exe run
```

6. 保存后你可以在任意地方双击`Run-sing-box.bat`运行sing-box

* 另外，可以用"-c"参数指定其他配置文件，典型用法是一个配置对应一个服务端，假设名为`Run-sing-box-a.bat`或`Run-sing-box-b.bat` ...

```bat
cd /D "D:\Program Files\sing-box-x.x.x-windows-amd64"
start /min sing-box.exe run -c config-a.json
```

```bat
cd /D "D:\Program Files\sing-box-x.x.x-windows-amd64"
start /min sing-box.exe run -c config-b.json
```

* 保存后在任意地方双击`Run-sing-box-a.bat`或`Run-sing-box-b.bat`运行sing-box

### Tun模式

* 其他同上，双击bat后弹出UAC对话框，不用右键`以管理员身份运行`

```bat
@echo off
net session >nul 2>&1
if %errorlevel% neq 0 (
    powershell -Command "Start-Process '%~f0' -Verb RunAs"
    exit /b
)
cd /D "D:\Program Files\sing-box-x.x.x-windows-amd64"
start /min sing-box.exe run -c config-a.json
```

* 保存后你可以在任意地方双击`Run-sing-box.bat`运行sing-box



## 在Android上使用sing-box

1. 下载符合你架构、OS版本的sing-box
2. 系统要求`Android 5.0+`
3. 使用`LocalSend`或类似工具发送配置好的客户端配置文件到你的设备
4. 接收配置文件后，点右下角`+图标`-->选择`Creat Manually`创建`New Profile`-->命名后-->`Source`选择`Import`-->点击`Import File`选择接收到的配置文件，导入到sing-box中使用



## 在iOS上使用sing-box

1. 中国区外APP Store下载sing-box vt
2. 系统要求`iOS 15.0+`
3. 使用`LocalSend`或类似工具发送配置好的客户端配置文件到你的设备
4. 接收配置文件后，创建`New Profile`-->命名后-->`File类型`选择`Import`-->`File Path`选择接收到的配置文件，导入到sing-box中使用



## 在Armbian上使用sing-box

* 目前`sing-box 1.10.7`经过验证



## 在ImmortalWrt上使用sing-box

* 正在施工



## 在安卓电视上使用sing-box

* 正在施工
