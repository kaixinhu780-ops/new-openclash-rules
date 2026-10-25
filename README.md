# 新的Open Clash规则集

2026-10-25：以用户提供的 `测试1.yaml` 为基础，合并现有仓库的 Siri Intelligence、Muse 固定出口、ChatGPT 和 Apple TV 专用规则。本独立公开仓库发布 `test1-merged-groups.ini`、Muse规则集及本文说明；旧仓库主模板保持不变。此前合并模板已经推送到旧仓库，本次按新要求另建公开仓库，不删除旧库内容或改写历史。GitHub 推送即模板发布，路由器是否更新、加载和实际生效另行验证。

## 两种产物

- 本地完整副本：`测试1-合并分组.yaml`，保留输入的私有节点提供者，只交付本地，不上传本仓库。
- 公开转换模板：`test1-merged-groups.ini`，不含节点、UUID、密码、订阅地址或原私有 provider 配置。使用时在订阅转换工具的「远程配置／配置模板」栏填入下面地址，并另行填写自己的有效节点订阅；它不是可直接导入的节点订阅。

```text
https://raw.githubusercontent.com/kaixinhu780-ops/new-openclash-rules/main/test1-merged-groups.ini
```

本地副本沿用输入的两个节点提供者，其中一个订阅 URL 为空；该值未被猜测、补写或删除。若原环境没有相应缓存或注入机制，需要使用者提供有效节点来源。未验证空 URL 提供者的实际加载行为。

## 合并范围

| 最终组名 | 处理 |
|---|---|
| `🍎 Siri Intelligence` | 新增 select；仓库11条启用的 Siri 规则逐条迁移，放在通用直连/Apple/Cloudflare规则之前。复用原文件地区组与节点来源，默认首项是现有 ChatGPT 组，不额外创建偏爱/全部节点等同义组。 |
| `🔒 Muse 固定出口` | 新增 select，只匹配原文件美国节点筛选器，保留 REJECT；默认首项 REJECT。导入后手动选定具体美国节点，不以共享自动测速组或 DIRECT 为默认。 |
| `🤖 ChatGPT` | 复用原组，不改其选择项；将仓库同等的 `GEOSITE,openai` 放到通用补丁之前，无重复组或重复规则。 |
| `🎥 AppleTV+` | 复用原有等价组；迁入仓库7条 Apple TV 精确/后缀规则，原 `GEOSITE,apple-tvplus` 保留。 |

`🤖 Claude` 的组和原有分类规则保留；未进行 Claude 重构或把此前 NTP 改动追加到这份新基础。原文件已有的 Bahamut 等无关内容保留，没有另行迁入动画疯规则。

Muse 使用仓库原范围：`muse.ai`、`meta.ai`、`metaaivm.com`、精确 `auth.meta.com`、`GEOSITE,meta` 和固定历史版本 MuseMeta 规则集，并保留配套 REJECT。**广义 Meta/Muse 范围会把部分 Facebook、Instagram、WhatsApp 共享流量优先送到 Muse 固定出口**，不是仅限 `muse.ai`；文件中已明确注释此行为。Muse 默认 REJECT 时，这些命中流量也会被阻断，需先选定节点。手动选节点不保证代理服务器上游公网 IP 永远不变。

## 公开转换与本地 YAML 的边界

两者规则条件、顺序、策略目标和组筛选语义已逐项比对。公开 INI 用转换时提供的订阅节点替代私有 `proxy-providers`；节点数量、实际名称、缓存和提供者刷新参数取决于使用者的转换环境，不能称为字节级相同的完整配置。

原 YAML 两个 domain MRS 规则源，在 INI 中使用同仓库同名 YAML 版本及 `clash-domain:`，以兼容订阅转换器；classical 源使用 `clash-classic:`。本轮没有重新解码验证远程 MRS 与 YAML 在每个更新时刻完全一致。Muse规则集从旧库固定历史版本原样复制到本仓库 `rules/MuseMeta.yaml`，内容校验一致；本地完整YAML和公开INI均引用本仓库。

公开 INI 不含 DNS/IPv6/TUN/嗅探基础配置；本地副本也未擅自补写原文件没有的字段。实际运行由导入环境/OpenClash配置决定，不能仅凭文件缺字段认定开启、关闭或泄漏。

## 验证与运行边界

已通过 YAML 解析、65组引用闭包及无循环、93条规则顺序与重复检查、Siri11条一致性、代表域名首个命中、公私产物关键语义对等、原提供者及所有原组不变、私密字段未出现在公开模板等检查。原文件、旧主模板和旧变更记录的摘要保持不变。

本机未发现现成 Mihomo/Clash 可执行文件，未执行核心 `-t` 校验；没有加载远程节点、探测真实节点、导入路由器或做设备验收。美国筛选无候选时，显式 REJECT 避免此专用组被静态转换器空填为 DIRECT；实际节点匹配、核心版本与 provider 加载仍需运行确认。

Mihomo 在所选代理不支持 UDP 时可能继续向下匹配。Muse 保留配套 REJECT，避免其命中流量因此落入后续直连；这不代表 UDP 端到端已测通。其他原有组的 UDP 行为未重写，也未宣称地区不可用问题已修复。

## 回退

旧主模板没有被替换。需退出这份独立模板时，恢复之前的模板地址或配置，并由路由器重新加载；本次发布未执行路由器操作。本地完整副本可与原 `测试1.yaml` 并存。

## Muse规则集与绑定

公开规则文件：[rules/MuseMeta.yaml](rules/MuseMeta.yaml)，555条规则，其中包含 `DOMAIN-SUFFIX,muse.ai`；它同时含广义Meta/社交共享域名，不是纯muse.ai清单。源为旧仓库固定提交 `b205160f6046e5079e6ac8dbebbeedefcf5640e3`，此次按原内容复制。

本地YAML的rule-provider名为 `Migrated_MuseMeta`，绑定 `RULE-SET,Migrated_MuseMeta,🔒 Muse 固定出口` 及随后的REJECT；公开INI将同一URL以clash-classic绑定到相同策略组和REJECT。muse.ai、meta.ai、metaaivm.com、auth.meta.com更早命中显式规则。

规则绑定静态核对通过不代表已在路由器生效；默认REJECT，需先手动选具体美国节点。
