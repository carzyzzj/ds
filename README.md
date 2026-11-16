# 我的电视直播源

每天北京时间 **06:23**（UTC cron `23 22 * * *`，GitHub 可能延迟）从 [CCSH/IPTV live.m3u](https://raw.githubusercontent.com/CCSH/IPTV/refs/heads/main/live.m3u) 提取频道。已替换此前所有采集来源和整理逻辑。

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


## 用户补充 IPv4 线路

附件 `1.3.0-IPv4频道源.m3u` 已保存为 [config/supplemental.m3u](config/supplemental.m3u)。每日获取 CCSH 后自动合并此文件，不会被次日更新覆盖。央视名称如 `CCTV-1 综合` 对应 `CCTV1`；其它名称完全匹配。只补充原清单已有频道，分类、顺序保持不变；同频道相同URL去重，不同URL作为追加线路。

附件共76条线路，其中66条属于原清单频道，CGTN英语、纪录、法语、西语、阿语共10条因原清单没有对应频道而跳过。实际新增及重复数量随上游变化，见 reports/latest.json 的 supplemental（added、duplicates、unmatched）。这是固定附件副本，之后电脑文件的修改不会自动上传；需要更新时替换仓库中的 supplemental.m3u 并手动运行工作流。附件线路同样没有逐流授权或可播放保证。



## Guovin IPv4 每日补充来源

每日读取 https://guovin.github.io/iptv-api/ipv4.m3u ，在 CCSH 和用户附件线路之后合并。只加入原清单198个频道中对应的频道，分类与频道顺序不变；同频道相同URL去重，不同URL保留。支持 CCTV/CETV 的连字符写法，文化精品对应央视文化精品，不能明确对应的频道写入报告 guovin.unmatched，不新增频道。各来源抓取有30秒超时、12MB上限，Guovin 抓取或格式校验失败时整次保留之前完整播放列表并记录失败，避免误丢补充线路。报告 guovin 字段显示匹配频道数、新增、重复及未匹配频道。

Guovin 链接是每日远程读取；config/supplemental.m3u 仍为此前附件固定副本。原 all.m3u 及分类订阅链接不变。抓取与合并成功不代表每条线路可在本地网络播放。

来源说明：https://github.com/Guovin/iptv-api 。项目代码采用 AGPL-3.0，不能据此认定所有直播流都已授权再分发；其免责声明要求使用者确保内容授权，未提供逐条直播流授权证明。只在相应许可范围内使用这些链接。



## Best-fan 每日补充来源

新增每日读取 https://raw.githubusercontent.com/best-fan/iptv-sources/main/cn_all_status.m3u8 ，在 CCSH、用户附件和 Guovin 之后合并。只匹配当前135频道清单（港澳台按用户图片保留17台），保持分类、顺序和多线路，相同频道相同URL去重。识别 CCTV/CETV 连字符及末尾分辨率括号；未匹配频道不新增，见 reports/latest.json 的 best_fan 字段。沿用30秒抓取超时、12MB上限；新来源失败时保留上次完整列表并记录失败。订阅地址不变。

上游项目：https://github.com/best-fan/iptv-sources 。其 README 声明 MIT、仅供学习研究，要求遵守相关网站条款；这不证明所有直播流均有再分发授权，也不保证在本地网络可播放。
