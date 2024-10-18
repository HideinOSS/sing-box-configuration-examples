# sing-box-configuration-examples
适用于 iOS（iPhone、iPad）、Android、Windows的sing-box配置样例。"sing-box configuration examples for iOS (iPhone/iPad), Android, and Windows"

## 目录解释

* sing-box-GeoIP-Geosite：指旧版本sing-box（小于等于1.7.8版本）使用GeoIP、Geosite进行国内外、私有地址分流，sing-box 小于等于1.7.8的版本可以使用，已在sing-box 1.8.0及以后的版本废弃。
* sing-box-RuleSet：指从sing-box 1.8.0版本后使用规则集方式分流，更加小巧灵活。
* IPv4：服务端和客户端使用IPv4地址进行配置。
* IPv6：服务端和客户端使用IPv6地址进行配置。

## 重要提示

* 为了防止DNS解析泄露，本项目均使用远程DNS解析，防止国内第三者知道你访问了哪些网站。使用 https://ipleak.net 、https://browserleaks.com/dns 来检测你的DNS是否泄露。
* 尽量不要自己配置DNS分流，配置不合理会造成DNS泄露。
* 不要在监听字段中配置domain_strategy，可选值：prefer_ipv4 prefer_ipv6 ipv4_only ipv6_only。如果设置，域名将在请求发出之前解析为 IP。可能会造成DNS泄露，使你的浏览隐私不再安全。
* 配置文件和官方文档相比，不存在的字段表示为空或使用内置默认值。
* 配置文件中留空的字段表示使用内置默认值，你可以完全理解官方文档后进行修改。
* 官方文档地址：https://sing-box.sagernet.org/zh/configuration 。

## 兼容性提示

* 根据反馈，小米 MIUI 14.0.25 (Android 13)使用sing-box 1.9.3以上版本会出现异常，包括不限于APP卡死、崩溃。强烈建议先停留在sing-box 1.9.3版本，旧版本在这里可以下载到：https://github.com/SagerNet/sing-box/releases/tag/v1.9.3 或 https://apkpure.com/sing-box/io.nekohasekai.sfa/download/1.9.3 ，下载后记得在线扫描病毒：https://www.virustotal.com/gui/home/upload 。
  
