# sing-box-configuration-examples
适用于 iOS（iPhone、iPad）、Android、Windows的sing-box配置样例。"sing-box configuration examples for iOS (iPhone/iPad), Android, and Windows"

## 目录解释

* sing-box-GeoIP-Geosite：指旧版本sing-box（小于等于1.7.8版本）使用GeoIP、Geosite进行国内外、私有地址分流，sing-box 小于等于1.7.8的版本可以使用，已在sing-box 1.8.0及以后的版本废弃。
* sing-box-RuleSet：指从sing-box 1.8.0版本后使用规则集方式进行国内外、私有地址分流，更加小巧灵活。
* IPv4：服务端和客户端都使用IPv4地址进行配置。
* IPv6：服务端和客户端都使用IPv6地址进行配置，用IPv6方式配置的服务器完全兼容IPv4客户端使用。
* 配置文件样例默认使用广告屏蔽规则集，如果你有特殊需求自行删除。

## 重要提示

* 不要使用记事本编辑配置文件，Windows(CR LF)和Linux(LF)、MacOS(CR)文本换行符不一致，导致服务端读取配置时出现奇怪问题，使用专业文本工具编辑。
* 为了防止DNS解析泄露，本项目均使用远程DNS解析，防止国内第三者知道你访问了哪些网站。使用 https://ipleak.net 、https://browserleaks.com/dns 来检测你的DNS是否泄露。
* 尽量不要自己配置DNS分流，配置不合理会造成DNS泄露。
* 不要在客户端监听字段中配置domain_strategy，可选值：prefer_ipv4 prefer_ipv6 ipv4_only ipv6_only。如果设置，域名将在请求发出之前抢先在你本地解析成IP，这可能会造成DNS泄露，使你的浏览隐私不再安全。
* 配置文件和官方文档相比，不存在的字段表示为空或使用内置默认值。
* 配置文件中留空的字段表示使用内置默认值，你可以完全理解官方文档后进行修改。
* 官方文档地址：https://sing-box.sagernet.org/zh/configuration 。

## 兼容性提示

* 调查后sing-box需要定位权限，国产系统限制了定位权限，AOSP没此问题。 【已解决：根据反馈，小米 MIUI 14.0.25 (Android 13)使用sing-box 1.9.3以上版本会出现异常，包括不限于APP卡死、崩溃。强烈建议先停留在sing-box 1.9.3版本，旧版本在这里可以下载到：https://github.com/SagerNet/sing-box/releases/tag/v1.9.3 或 https://apkpure.com/sing-box/io.nekohasekai.sfa/download/1.9.3 ，下载后记得在线扫描病毒：https://www.virustotal.com/gui/home/upload 。】
  
## 在Windows上运行

* Windows最小化运行，新建bat文件，名为*Run-sing-box.bat* ， 解压sing-box-x.x.x-windows-amd64.zip，*sing-box-x.x.x-windows-amd64在 D:\Program Files目录中* ，配置文件config.json和sing-box.exe程序在同一目录。
* ```bat
  cd /D "D:\Program Files\sing-box-x.x.x-windows-amd64"
  start /min sing-box.exe run
  ```
* 保存后双击*Run-sing-box.bat*运行。

## 在Android上运行
* 下载符合你架构、OS版本的sing-box
* 使用LocalSend或类似工具发送配置好的客户端配置文件到你的设备
* 接收配置文件后，点右下角+图标-->选择Creat Manually创建New Profile-->命名后--Source选择Import-->点击Import File选择接收到的配置文件，导入sing-box中使用

## 在iOS上运行
* APP Store下载sing-box
* 使用LocalSend或类似工具发送配置好的客户端配置文件到你的设备
* 接收配置文件后，创建New Profile-->命名后-->File类型选择Import-->File Path选择接收到的配置文件，导入sing-box中使用


