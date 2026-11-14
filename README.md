# 我的电视直播源

每天北京时间06:23（UTC前一天22:23，cron为23 22 * * *）读取配置中的公开M3U，提取频道名和HTTP(S)地址，去重并分类。不搬运整个仓库，不保存或转播视频。Actions → Daily IPTV → Run workflow 可手动运行。

## 订阅

- [全部 all.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/all.m3u)
- [央视 cctv.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/cctv.m3u)
- [卫视 satellite.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/satellite.m3u)
- [地方 local.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/local.m3u)
- [少儿动漫 kids.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/kids.m3u)
- [港澳台 hmt.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/hmt.m3u)
- [国际 international.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/international.m3u)
- [体育 sports.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/sports.m3u)
- [美国 us.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/us.m3u)
- [DuckKota在线来源 duckkota.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/duckkota.m3u)

all包含全部线路，通常只需订阅all。体育、美国可以交叉归类；专用文件可能互相重叠，但all内相同频道名和URL只保留一次。DuckKota是来源分组，不是频道类型，其online表示上游检查结果，不保证在你的网络可播。all内分组优先港澳台、体育、美国，再按央视/卫视/地方/少儿动漫/国际名称规则归类；名称规则可能误分。zyq1120来源另参考上游国家和节目类型分组，少儿、体育、美国等会进入对应现有分类；外国中文频道可归入国际。

港澳台列表使用上游频道编号的.hk/.mo/.tw地区后缀、明确的香港/澳门/台湾分组，以及可识别的频道名称（如TVBS、RTHK、ViuTV、三立、中天、凤凰等）汇总；相同频道不同URL继续保留。hmt.m3u与其他节目类型或来源列表可能重叠；all内已识别线路显示为“港澳台”。不把普通“明珠”或“TVB”字样一概归入，排除杭州明珠、TVB Vietnam等已知混淆项。名称识别可能遗漏或误分，不能推断每条流的实际播出地或授权。

## 每日读取的来源

- jiandantv/IPTV2026：live.m3u
- iptv-org/iptv：index.m3u
- https://iptv-org.github.io/iptv/categories/sports.m3u
- https://iptv-org.github.io/iptv/countries/us.m3u
- https://DuckKota.github.io/iptv/playlists/online.m3u
- [zyq1120/iptv-playlist](https://github.com/zyq1120/iptv-playlist)：https://raw.githubusercontent.com/zyq1120/iptv-playlist/main/iptv.m3u

后两个iptv-org子列表与全量列表可能重复，作用也包括提供准确的体育和美国分类归属。相同线路在不同来源的归属会合并。

## 配置与报告
config/sources.json中name为名称、url为来源地址、enabled控制启用、tags可设sports/us/duckkota/hmt。保持schema_version=3，停用来源时改enabled为false，避免自动重新添加。rights_status仅记录来源情况，不是节目授权证明。
reports/latest.json记录各来源解析数和结果：published为已发布，unchanged为内容与配置均未变，retained为异常时保留旧列表。reports/source-state.json保存SHA256，配置或分类版本变化也会重新生成。

## 使用范围与来源归属
仅作个人非商业公开链接整理。来源：[jiandantv/IPTV2026](https://github.com/jiandantv/IPTV2026)、[iptv-org/iptv](https://github.com/iptv-org/iptv)、[DuckKota/iptv](https://github.com/DuckKota/iptv)。jiandantv README声称MIT同时禁止商业用途；iptv-org README的CC0标识与实际Unlicense不同；DuckKota代码为MIT，频道数据遵循各上游条款。zyq1120仓库未见LICENSE，其README说明从公开上游整理，但没有逐条节目授权证明；仅收录公开播放链接并保留来源说明。软件及列表许可不等于节目版权授权。本仓库不声称逐条流已获内容授权，不绕过登录、付费或访问限制；收到有效权利通知应及时移除链接。

## 检查与局限
要求EXTM3U头及EXTINF/HTTP(S)地址对，同名同URL去重，同频道不同URL保留多线路。暂不保留台标、EPG或特殊播放请求头。每个来源15秒超时、12MB上限；最多抽查64条，4路并发、5秒超时、读取512字节。不确定和未抽查线路不删除；收到数据不保证画面声音及持续播放。抽查全部失败、任一来源失败或为空时保留整套旧版，首次失败则尚无列表。十个列表及来源状态在单个Git提交发布。

仅schedule和workflow_dispatch触发，不监听push，自动提交不会循环。GITHUB_TOKEN仅contents: write，无个人令牌，串行运行，最多20分钟。现有权限已验证能写回，无需放宽。GitHub排队可能延迟，公开仓库长期无活动可能停用定时任务，需到Actions恢复。
