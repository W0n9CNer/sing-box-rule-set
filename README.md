# sing-box-rule-set

用于 sing-box 的二进制规则集(srs)与 json 规则集,由 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)
的 `rule/Clash` 规则转换而来,转换工具为 [yaml2srs](https://github.com/W0n9CNer/yaml2srs)。

## 更新方式

不再使用 GitHub Actions。规则由自建服务器每天 06:00 UTC(北京时间 14:00)自动更新:

1. 拉取 blackmatrix7/ios_rule_script 最新的 `rule/Clash` 规则;
2. 用 yaml2srs 重新生成 `rule/<名称>/<名称>.json` 和 `.srs`;
3. 有变化时自动提交并推送回本仓库(提交信息为 UTC 日期)。

生成脚本:服务器 `10.1.0.11:/develop/project/bin/update-rule-set.sh`,日志 `/var/log/update-rule-set.log`。

## 使用

```json
{
  "route": {
    "rule_set": [
      {
        "type": "remote",
        "tag": "ChinaMax",
        "format": "binary",
        "url": "https://raw.githubusercontent.com/W0n9CNer/sing-box-rule-set/main/rule/ChinaMax/ChinaMax.srs",
        "download_detour": "direct"
      }
    ],
    "rules": [
      {
        "rule_set": "ChinaMax",
        "outbound": "direct"
      }
    ]
  }
}
```

本地使用时把 `type` 改为 `local`,`path` 指向下载下来的 `.srs` 文件即可。

## 说明

- 规则集只包含 Clash 规则里能表达的部分(DOMAIN / DOMAIN-SUFFIX / DOMAIN-KEYWORD / DOMAIN-REGEX / IP-CIDR / IP-CIDR6);
  `PROCESS-NAME`、`IP-ASN` 等类型不会出现在规则集里。
- srs 为二进制格式版本 1,兼容各版本 sing-box。
- 规则数量以仓库 `rule/` 目录为准,上游新增或删除规则时会同步增删。
