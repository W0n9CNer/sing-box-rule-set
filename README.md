# sing-box-rule-set

用于 sing-box 的二进制规则集(srs)与 json 规则集,由 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)
的 `rule/Clash` 规则转换而来,转换工具为 [yaml2srs](https://github.com/W0n9CNer/yaml2srs)。

## 更新

规则集每天自动从 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)
的 `rule/Clash` 同步一次,由 [yaml2srs](https://github.com/W0n9CNer/yaml2srs) 转换生成,
`rule/` 目录始终保持最新,无需手动操作。

如果发现某个规则集内容异常(条目缺失、与上游对不上等),欢迎开 issue 说明规则集名称,
会有人核对上游源文件与生成结果。

## 使用

### 远程规则集

让 sing-box 按需从仓库的 raw 地址下载,`format` 固定为 `binary`:

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

`download_detour` 可选,用于指定下载规则集的出站;不需要时可以删掉。

### 本地规则集

从 [Releases](#releases) 下载 `srs-<UTC 日期>.tar.gz` 并解压,会得到 `rule/<名称>/<名称>.srs`。
把 `type` 改为 `local`,用 `path` 指向该文件即可,不再需要 `url` 和 `download_detour`:

```json
{
  "route": {
    "rule_set": [
      {
        "type": "local",
        "tag": "ChinaMax",
        "format": "binary",
        "path": "/etc/sing-box/rule/ChinaMax.srs"
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

`path` 可以写绝对路径,也可以写相对 sing-box 工作目录的路径;Windows 下建议使用正斜杠
(如 `"path": "C:/sing-box/rule/ChinaMax.srs"`)或在 JSON 中转义反斜杠。

## Releases

每个产生更新的日期会创建一个 release,资产为单个 `srs-<UTC 日期>.tar.gz`:

- 只包含 `rule/<名称>/<名称>.srs` 和同目录的 `.srs.md5`,不含 json/yaml;
- 下载地址形如
  `https://github.com/W0n9CNer/sing-box-rule-set/releases/download/srs-<UTC 日期>/srs-<UTC 日期>.tar.gz`;
- 解压后在根目录执行 `find rule -name '*.srs.md5' -execdir md5sum -c {} \;` 即可校验。

需要单个规则集的最新版本时,直接使用仓库里的 raw 链接即可(见上方"使用")。

## 支持的规则类型

规则集只包含 Clash 规则里能表达的部分,映射关系与
[yaml2srs](https://github.com/W0n9CNer/yaml2srs#支持的规则类型) 一致:

| Clash | sing-box |
| --- | --- |
| DOMAIN | domain |
| DOMAIN-SUFFIX | domain_suffix |
| DOMAIN-KEYWORD | domain_keyword |
| DOMAIN-REGEX | domain_regex |
| IP-CIDR / IP-CIDR6 | ip_cidr |
| IP-SUFFIX | ip_cidr |
| SRC-IP-CIDR / SRC-IP-SUFFIX | source_ip_cidr |
| DST-PORT | port / port_range |
| SRC-PORT | source_port / source_port_range |
| PROCESS-NAME | process_name |
| PROCESS-PATH | process_path |
| PROCESS-PATH-WILDCARD | process_path_regex |
| PROCESS-PATH-REGEX | process_path_regex |
| 裸域名 / `.example.com` / `+.example.com` / `*.example.com` 通配写法 | domain / domain_suffix / domain_regex |

`IP-ASN`、`GEOIP` 等无法在规则集里表达的类型不会出现在规则集里。`DOMAIN-WILDCARD` 不做映射
(通配语义与 sing-box 的 `domain_regex` 不完全一致),同样跳过。

## 说明

- 每个 `rule/<名称>/` 目录包含 `<名称>.json`(带缩进)、`<名称>.min.json`(未格式化)、
  `<名称>.srs`、`<名称>.srs.md5`(md5sum 格式,可用 `md5sum -c` 校验)以及
  `<名称>.yaml`(或 `.yml`,生成该规则集的原始上游文件,按原字节复制)。
- 部分规则集(如 ChinaMax、Apple、Netflix)会同时合并上游同名的 `_Classical.yaml`,否则主文件只剩少量条目;
  合并后的规则集附带的是主文件,`_Classical.yaml` 仍会作为独立规则集输出。上游以 `_Resolve.yaml` /
  `_No_Resolve.yaml` 结尾的变体是 Clash fake-ip 专用,与普通版本内容重复,不参与生成。
- 混合类型规则集(例如同时含域名和进程规则)会拆成多条 rule,保持 Clash 规则集中
  每一行独立匹配(OR)的语义。
- srs 为二进制格式版本 1,兼容各版本 sing-box。
- 规则数量以仓库 `rule/` 目录为准,上游新增或删除规则时会同步增删。
