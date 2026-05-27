# xray-china-list

你好，欢迎来到 Vorfeed 的欢乐满满翻墙提高班。

本项目是 **SmartDNS、Xray、Shadowrocket、sing-box** 统一分流规则的一部分，通过严格同步 [dnsmasq-china-list](https://github.com/felixonmars/dnsmasq-china-list)，保证多端分流规则的一致性。

- SmartDNS 规则：[smartdns-china-list](https://github.com/OriginVorfeed/smartdns-china-list)

- sing-box 规则：[singbox-china-list](https://github.com/OriginVorfeed/singbox-china-list)

- Shadowrocket 规则：[shadowrocket-china-list](https://github.com/OriginVorfeed/shadowrocket-china-list)

## 规则说明

参照 dnsmasq-china-list 中 [Makefile](https://github.com/felixonmars/dnsmasq-china-list/blob/master/Makefile) 的逻辑，转换为对应的 **Xray 白名单规则**。

具体转换逻辑，请查看 [workflow](https://github.com/OriginVorfeed/xray-china-list/blob/main/.github/workflows/update.yml)。

可以在 [data 目录](https://github.com/OriginVorfeed/xray-china-list/tree/main/data) 查看编译前的源文件。

提供2个下载链接，第1个需要代理才能稳定访问，第2个可以直接访问，但会延迟12小时。

- [https://raw.githubusercontent.com/OriginVorfeed/xray-china-list/master/geosite.dat](https://raw.githubusercontent.com/OriginVorfeed/xray-china-list/master/geosite.dat)
- [https://cdn.jsdelivr.net/gh/OriginVorfeed/xray-china-list@master/geosite.dat](https://cdn.jsdelivr.net/gh/OriginVorfeed/xray-china-list@master/geosite.dat)

作为精简版白名单分流规则，仅包含以下类别：

- **geosite:cn**

  主规则，包含10w多个适合直连的域名。二进制规则集的匹配时间一般都在微秒级，和毫秒级的网络延时相比，可以忽略不计。

- **geosite:private**

  一份精简过的私有域名列表，不包含 `内网 DNS 反查、各品牌路由器后台地址、软件内部通信域名` 等不太常用的内容。

- **geosite:apple**

  可选规则，推荐使用。Apple 相关域名在国内有 CDN 加速，理论上适合直连。但如果你发现解析到了国外，就不要使用。

- **geosite:google**

  可选规则，不推荐使用。你要是发现熟悉的网页上，一些图标突然变成了文字，就是直连 fonts.gstatic.com 不稳定导致的。

> 不包含任何 GFWList、广告过滤等类别。

## 使用方法

- 推荐配合 [v2rayN](https://github.com/2dust/v2rayn) 使用，填写 `设置 -> 参数设置 -> v2rayN 设置 -> Geo 文件来源`，任选其一：

  ```
  # 需要代理
  https://raw.githubusercontent.com/OriginVorfeed/xray-china-list/refs/heads/main/{0}.dat

  # 可以直连，但最好别用。试了好几次，v2rayN 下载到的文件大小不对，但浏览器下载是好的，不知道什么原因。
  https://cdn.jsdelivr.net/gh/OriginVorfeed/xray-china-list@main/{0}.dat
  ```

- `帮助 -> 检查更新`，勾选 GeoFiles，点击 `检查更新`。
- 文件名已匹配预置的 `绕过大陆(Whitelist)` 规则集，可以直接 `设为活动规则`。

> 单独配置时，重命名 geosite.dat，即可和其他 Xray dat 文件配合使用。
>
> 请参考 [Xray 官方文档](https://xtls.github.io/config/routing.html)，合并以下内容：

```json
{
  "routing": {
    "rules": [
      {
        "type": "field",
        "outboundTag": "direct",
        "ip": [
          "geoip:private"
        ]
      },
      {
        "type": "field",
        "outboundTag": "direct",
        "domain": [
          "ext:xray-china-list.dat:private",
          "ext:xray-china-list.dat:cn",
          "ext:xray-china-list.dat:apple"
        ]
      },
      {
        "type": "field",
        "outboundTag": "direct",
        "ip": [
          "geoip:cn"
        ]
      }
    ]
  }
}
```

## 验证配置

 - 开启 Xray 代理。
 - 访问 [https://ip111.cn/](https://ip111.cn/)。
 - `从国内测试、从国外测试` 应该都是你的本地 IP，`从谷歌测试` 应该是你的代理 IP。

## 常见问题

- **如何恢复 v2rayN 默认的 Xray dat 文件？**

  清空 `设置 -> 参数设置 -> v2rayN 设置 -> Geo 文件来源` 后，再次更新 GeoFiles 即可。

- **可以不覆盖 v2rayN 预置的规则文件吗？**

  可以，但不推荐，无论如何都会造成 Xray、sing-box 分流行为不一致。如果你不介意这点，配置方法如下：

  - 重命名 geosite.dat，并放置于 `v2rayN\bin` 目录下。注意不要覆盖预置的 geosite.dat。
  - `设置 -> 路由设置 -> 规则集设置 -> 路由规则详情设置`，填写 `ext:xray-china-list.dat:cn`，和其他需要的规则。
  - 应用设置后，即可测试分流行为是否符合预期。

    > 然而，目前 v2rayN 在生成 sing-box 配置时，会跳过 `ext:` 开头的规则，导致两者分流行为不一致。

## 问题反馈

任何问题欢迎在 [Issues](https://github.com/OriginVorfeed/xray-china-list/issues) 中反馈。
