# 我的电视直播源

每天北京时间 **06:23**（UTC cron `23 22 * * *`，GitHub 可能延迟）仅从 [CCSH/IPTV live.m3u](https://raw.githubusercontent.com/CCSH/IPTV/refs/heads/main/live.m3u) 提取频道。已替换此前所有采集来源和整理逻辑。

## 频道和顺序

依据用户提供的《清单1.xlsx》各分类明细页，共198个频道：央视35、地方台68、港澳台频道80、其它15。国际频道页没有频道，故保留空列表。汇总页数字与明细不一致，以明细为准。地方台中包含卫视频道，少儿、体育等也按照附件原分类。

分类顺序及频道行顺序原样保存于 [config/channel-order.json](config/channel-order.json)。每天按完全相同的频道名匹配来源，保持清单顺序，同频道不同URL保留多线路，同频道相同URL去重，线路保持来源顺序。未匹配频道记录在 reports/latest.json，不擅自补入其它频道。

## APK 订阅直链

在“我要看电视”中添加以下原始文件链接；建议只订阅总表，避免重复。

|列表|订阅直链|
|---|---|
|全部|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/all.m3u|
|央视|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/cctv.m3u|
|地方台|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/local.m3u|
|港澳台|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/hmt.m3u|
|国际频道（当前为空）|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/international.m3u|
|其它|https://raw.githubusercontent.com/nut7nan/my-tv-sources/main/playlists/other.m3u|

旧的 satellite/kids/sports/us/animation 分类文件在新列表成功生成后移除，请改订阅总表或上面的新分类。仓库更新后，在 APK 中刷新订阅即可获取新内容，具体取决于 APK 的缓存机制；已有 all.m3u 链接无需更改。

## 运行、配置与失败处理

进入 Actions → Daily IPTV → Run workflow → main → Run workflow 可手动运行。工作流只在定时或手动运行时触发，不因自动提交循环运行；GITHUB_TOKEN 仅申请 contents: write。若出现写入权限错误，检查 Settings → Actions → General 的工作流权限及仓库规则。

唯一来源在 .github/workflows/daily.yml 的 source 中指定，config/sources.json 每次同步记录该来源；更改频道和顺序可编辑 config/channel-order.json。输出为UTF-8标准M3U，保留来源 EXTINF 中的频道标识、台标等信息，替换为清单分类。

抓取超时30秒，最大12MB。检查M3U头、频道元信息及HTTP(S)地址格式。抓取失败、无有效条目或一个清单频道也未找到时，保留之前播放列表，并提交简短失败报告、将运行标记失败。部分频道缺失时发布其余匹配频道并列出缺失名单。报告见 [reports/latest.json](reports/latest.json)。

这里不逐个播放视频验证，不保证每条线路在国内无代理网络可播放。地域限制、鉴权、时效、防盗链、运营商及播放器差异都可能影响播放；HTTP地址存在也不代表视频能正常播放。

## 来源与使用限制

CCSH 项目说明这些链接采集自互联网，并限制为个人技术学习、非商业用途。公开仓库及项目代码许可证不等于逐个直播流的播放、复制或再分发授权；没有逐流版权保证。请遵守 [上游说明](https://github.com/CCSH/IPTV) 和权利人限制，仅在获得相应许可的范围内使用，不作商业用途。
