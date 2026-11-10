# sing-box-rule-set

用于 sing-box 的二进制规则集(srs)与 json 规则集,由 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)
的 `rule/Clash` 规则转换而来,转换工具为 [yaml2srs](https://github.com/W0n9CNer/yaml2srs)。

## 维护方式

本仓库由 **AI agent 自动维护**,不再使用 GitHub Actions,日常没有任何人工介入:

- 每天自动检查上游 `rule/Clash` 是否有更新;
- 用 yaml2srs 重新生成 `rule/<名称>/<名称>.json` 和 `.srs`;
- 上游有变化时自动提交并推送回本仓库(提交信息为 UTC 日期),无变化则不产生提交。

提交历史里的每次更新都来自这条自动化流程。如果发现某个规则集内容异常(条目缺失、与上游对不上等),欢迎开 issue 说明规则集名称,会有人核对上游源文件与生成结果。

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
- 部分规则集(如 ChinaMax、Apple、Netflix)会同时合并上游同名的 `_Classical.yaml`,否则主文件只剩少量条目;
  上游以 `_Resolve.yaml` 结尾的变体是 Clash fake-ip 专用,不参与生成。
- srs 为二进制格式版本 1,兼容各版本 sing-box。
- 规则数量以仓库 `rule/` 目录为准,上游新增或删除规则时会同步增删。
