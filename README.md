# sing-box-configuration-examples
适用于 iOS（iPhone、iPad）、Android、Windows的sing-box配置样例。"sing-box configuration examples for iOS (iPhone/iPad), Android, and Windows"
* 一般普遍用法是sing-box服务端运行在各Linux发行版上，电脑、手机、平板等作为客户端。
* 配置样例服务端由sing-box实现的ShadowTLS v3协议充当，各平台sing-box作为客户端。
* 使用其他协议参考官方文档修改服务端的入站、客户端的出站。
* sing-box官方文档地址：https://sing-box.sagernet.org/zh/configuration 。

## 目录解释

* sing-box-GeoIP-Geosite：指sing-box ≤ 1.7.8版本使用GeoIP、GeoSite进行国内外、私有地址分流。sing-box小于等于1.7.8的版本可以使用，已在sing-box 1.8.0及以后的版本废弃。
* sing-box-RuleSet：指从sing-box 1.8.0版本后使用规则集方式进行国内外、私有地址分流，更加灵活。
* IPv4：服务端和客户端都使用IPv4方式进行配置，受Go的特性，监听IPv4地址也使用"::"形式，它等同于"0.0.0.0"，IPv4和IPv6服务端都是 ["listen": "::",] ，为了和官方一致不做修改。
* IPv6：服务端和客户端都使用IPv6方式进行配置，IPv6的服务端兼容IPv4客户端使用。
* 配置文件样例默认使用广告屏蔽规则集，不需要可自行删除。

## 重要提示

* 在Windows上不要使用记事本编辑配置文件，Windows(CR LF)和Linux(LF)、MacOS(CR)文本换行符不一致，导致服务端读取配置时出现问题，使用专业文本工具编辑。
* 为了防止DNS解析泄露，本项目配置文件均使用远程DNS解析，防止国内第三者知道你访问了哪些网站。使用 https://ipleak.net 、https://browserleaks.com/dns 来检测你的DNS是否泄露。
* 如果你不完全理解官方文档，尽量不要自己配置DNS分流，配置不合理会造成DNS泄露。
* 不要在客户端监听字段中配置"domain_strategy"，可选值：prefer_ipv4 prefer_ipv6 ipv4_only ipv6_only。如果设置，域名在请求发出之前抢先在本地解析成IP，这可能会造成DNS泄露，使你的浏览隐私不再安全。
* 本项目配置文件使用了规则集方式进行国内外、私有地址分流。国外走代理，国内和私有IP直连，节省服务器流量并提高国内访问速度。
* 配置文件和官方文档相比，部分字段为空表示使用内置默认值，你可以完全理解官方文档后进行修改。

## 兼容性提示

* 调查后sing-box需要定位权限，国产系统限制了定位权限，AOSP没此问题。 【已解决：根据反馈，小米 MIUI 14.0.25 (Android 13)使用sing-box 1.9.3以上版本会出现异常，包括不限于APP卡死、崩溃。】
  
## 在Windows上使用sing-box

* 在 https://github.com/SagerNet/sing-box/releases 下载符合你系统的正式版本 ，
* 想要Windows最小化运行，新建bat文件，假设名为*Run-sing-box.bat* ，
* 如果你下载的包是sing-box-x.x.x-windows-amd64.zip，
* 假设sing-box-x.x.x-windows-amd64在 D:\Program Files 文件夹中，
* 配置文件config.json和sing-box.exe程序在同一目录，
* 另外，可以用"-c"参数指定其他地方的配置文件，
 ```bat
  cd /D "D:\Program Files\sing-box-x.x.x-windows-amd64"
  start /min sing-box.exe run
  ```
* 保存后你可以在任意地方双击*Run-sing-box.bat*运行sing-box。

## 在Android上使用sing-box
* 下载符合你架构、OS版本的sing-box，
* 使用LocalSend或类似工具发送配置好的客户端配置文件到你的设备，
* 接收配置文件后，点右下角+图标-->选择Creat Manually创建New Profile-->命名后-->Source选择Import-->点击Import File选择接收到的配置文件，导入到sing-box中使用。

## 在iOS上使用sing-box
* 中国区外APP Store下载sing-box，
* 使用LocalSend或类似工具发送配置好的客户端配置文件到你的设备，
* 接收配置文件后，创建New Profile-->命名后-->File类型选择Import-->File Path选择接收到的配置文件，导入到sing-box中使用。

  


