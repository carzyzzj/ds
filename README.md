# 我的电视直播源

每天北京时间 **06:23**（UTC cron `23 22 * * *`，GitHub 可能延迟）读取下列配置来源。

## 更新原则

仅 bang359/dsj 的六个频道TXT全量收录有效频道；CCSH、Guovin、Best-fan和用户附件仍只收录原清单中的频道（沿用Excel及之后用户调整后的135台清单）。DSJ可补入清单外频道，包括新增港澳台频道。频道名称做保守规范化（CCTV/CETV连字符、常见清晰度后缀及少量明确别名）；同频道相同URL去重，不同URL保留多线路。

合并时先按内置黑名单过滤推广/跳转包装源；随后并发探测线路连通性：确认死链（DNS失败、连接拒绝、404/410、5xx）剔除，超时/401/403/跳转未终止等不确定状态保留为兜底；可用线路按首包时间排序，每频道默认最多保留 5 条（见 daily.yml 顶部参数）。

注意：探测在 GitHub Actions 海外机房执行，国内运营商内网源可能被误判为不可达，因此超时类线路未直接剔除；本地播放器实际播放效果以你所在网络为准。

`config/channel-order.json` 是非DSJ来源的频道允许名单，同时用于所有来源的原频道优先排序及分类。只有匹配清单央视、地方台、港澳台名称的频道进入这三个分类，保持清单顺序。其余频道中，中国地区、港澳台地区或汉语（包括方言）频道归“其他”；其余归“国际频道”。

仅发布央视、地方台、港澳台、国际频道、其他五类及总表；旧satellite/kids/sports/movies分类文件停止发布。

## 订阅直链

|分类|原始文件链接|
|---|---|
|全部|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/all.m3u|
|央视|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/cctv.m3u|
|地方台|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/local.m3u|
|港澳台频道|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/hmt.m3u|
|国际频道|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/international.m3u|
|其他|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/other.m3u|

在“我要看电视”APK中刷新订阅即可获取仓库新内容。总表已包含全部分类，建议只订阅总表避免重复。

## 当前来源

- CCSH/IPTV: https://raw.githubusercontent.com/CCSH/IPTV/refs/heads/main/live.m3u
- User IPv4 supplement: config/supplemental.m3u
- Guovin IPv4: https://guovin.github.io/iptv-api/ipv4.m3u
- Best-fan IPTV: https://raw.githubusercontent.com/best-fan/iptv-sources/main/cn_all_status.m3u8
- hina-hk-mo-tw-live: https://raw.githubusercontent.com/jamsluuuuu/iptv/refs/heads/main/china-hk-mo-tw-live.m3u
- bang359/dsj: https://raw.githubusercontent.com/bang359/dsj/main/dsj.txt, https://raw.githubusercontent.com/bang359/dsj/main/dsjcs.txt, https://raw.githubusercontent.com/bang359/dsj/main/dsjcs1.txt, https://raw.githubusercontent.com/bang359/dsj/main/iphone.txt, https://raw.githubusercontent.com/bang359/dsj/main/ku9.txt, https://raw.githubusercontent.com/bang359/dsj/main/yc.txt

来源配置在 `config/sources.json`，每日直接读取其中启用的url/urls/path；用户附件 `config/supplemental.m3u` 为固定副本。

## 运行与失败处理

Actions → Daily IPTV → Run workflow 可手动运行。每个远程文件30秒超时、12MB上限；任一来源下载失败、解析失败或没有有效频道时，保留上一次完整播放列表并记录失败。报告见 `reports/latest.json`，列出每个来源的有效条目、新增、重复、无效、被推广过滤条目，以及探测存活/死链/待验证数量和各分类频道数、线路数。

工作流只使用schedule和workflow_dispatch触发；GITHUB_TOKEN仅申请contents: write，自动提交使用[skip ci]，不会循环触发。

## 局限与来源限制

探测成功不代表视频可长期播放；地域、运营商、鉴权、时效、防盗链及播放器差异可能影响国内无代理播放。

CCSH、Guovin、Best-fan、DSJ及用户附件的公开链接或代码许可证不等于逐条直播流已获播放、复制或再分发授权；未核实逐流授权。请遵守各上游说明及权利人限制。
