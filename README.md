# 我的电视直播源

每天北京时间06:23运行（UTC前一天22:23，cron：23 22 * * *）。GitHub可能排队延迟。Actions → Daily IPTV → Run workflow 可手动运行。

## 仅采集以下四个来源

- https://raw.githubusercontent.com/jiandantv/IPTV2026/main/live_lite.m3u
- https://raw.githubusercontent.com/jiandantv/IPTV2026/main/live.m3u
- https://iptv-org.github.io/iptv/index.m3u
- https://iptv-org.github.io/iptv/categories/animation.m3u

已替换旧来源配置，不再单独采集sports、us、DuckKota和zyq1120来源。index全量列表仍可能包含与旧来源重合的频道，这是当前四来源本身提供的内容。只从本轮四来源重建，不合并旧发布文件。live_lite和live可能重复，同频道同URL去重，不同URL保留多线路。animation子列表提供动漫归属。

## 订阅直链

通常只需订阅全部列表，APK刷新直播源即可读取新版。

- [全部 all.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/all.m3u)
- [央视 cctv.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/cctv.m3u)
- [卫视 satellite.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/satellite.m3u)
- [地方 local.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/local.m3u)
- [少儿动漫 kids.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/kids.m3u)
- [动漫 animation.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/animation.m3u)
- [港澳台 hmt.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/hmt.m3u)
- [国际 international.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/international.m3u)
- [体育 sports.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/sports.m3u)
- [美国 us.m3u](https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/us.m3u)

动漫子列表同时纳入kids。体育从频道元数据或名称识别；美国从频道编号.us等标记识别。港澳台使用.hk/.mo/.tw频道编号、上游地区分组和明确频道名称识别，排除杭州明珠、TVB Vietnam等混淆项。分类文件可以重叠；all中优先显示港澳台、动漫、体育、美国，再按央视/卫视/地方/少儿/国际名称规则分组。自动识别可能遗漏或误分。已停用的duckkota.m3u在成功发布后删除。

## 配置及失败处理

config/sources.json保存来源，schema_version保持4；此版本首次运行会将旧配置替换成上面四项，此后按配置读取，不会自动补回旧来源。enabled控制启用，tags可设animation/sports/us/hmt。rights_status仅记录来源情况，不代表节目授权。

reports/latest.json记录来源解析数和结果：published为已发布，unchanged为来源和配置未变化，retained为失败保留前一次有效列表。reports/source-state.json保存来源及配置摘要，分类版本变化也会重建。任一来源下载失败、格式错误、为空或所有抽查均不确定时保留旧版并记录失败。因此失败时旧版可能仍有旧来源线路，成功发布后才完成替换。

## 检测局限

要求EXTM3U头及EXTINF/HTTP(S)地址对。每来源15秒超时、12MB上限；最多抽查64条，4路并发、5秒超时、读取512字节。未逐条验证音视频，不确定和未抽查线路不删除；能收到数据不保证可持续播放，也不代表国内无代理网络可播。本任务没有新增本地网络筛选功能。暂不保留台标、EPG和特殊播放请求头。

仅schedule和workflow_dispatch触发，不监听push，自动提交不会循环。GITHUB_TOKEN仅contents: write，无个人令牌；串行运行，最多20分钟。公开仓库长期无活动可能被GitHub停用定时任务，可到Actions恢复。

## 来源与使用范围

仅作个人非商业公开链接整理，不保存或转播视频。来源：[jiandantv/IPTV2026](https://github.com/jiandantv/IPTV2026)、[iptv-org/iptv](https://github.com/iptv-org/iptv)。遵守各来源许可和非商业限制。仓库或软件许可不等于节目版权授权；本仓库没有逐条直播流授权证明，不绕过登录、付费或访问限制。收到有效权利通知应及时移除链接。
