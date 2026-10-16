# 媒体文件同步删除魔改版（MediaSyncDelModified）

基于 [thsrite/MediaSyncDel](https://github.com/thsrite/MediaSyncDel) v2.0.2 改造的独立插件。

## 与原版的区别

原版在删除媒体时，会按 `tmdbid` 查询**全部**转移记录并逐条删除。当同一
tmdbid 下存在多个版本（例如洗版后的新旧版本共存）时，删除其中一个版本会
**连带误删其它版本**的转移记录、源文件和下载任务。

本魔改版新增**版本过滤**：在删除前先确定「被删文件属于哪个版本」，只处理
该版本的转移记录。

## 匹配优先级

1. **`download_hash` 精确匹配** — 最精确，只保留与被删文件同种子的记录
2. **`src` 源目录前缀匹配** — 次精确，只保留与被删文件同源目录的记录
3. **两者都无法确定版本时返回空列表** — 宁可漏删，不可误删

即：先在被删路径命中的转移记录中提取「版本指纹」（下载器 hash 或源目录），
再用该指纹过滤全部候选记录。无法确定版本时**跳过删除**并打印日志。

## 实现位置

- 新增方法 `__filter_transfer_his_by_version()`
- 在 `__get_transfer_his()` 的 `return msg, transfer_history` 前插入调用

## 配置说明

配置项与原版一致，配置前缀为 `mediasyncdelmodified_`（与原插件
`mediasyncdel_` 相互独立，可共存）。

| 配置项 | 说明 |
|--------|------|
| 启用插件 | 总开关 |
| 发送通知 | 删除完成后推送通知 |
| 删除源文件 | 同时删除硬链接与源文件、下载任务 |
| 删除历史 | 清理插件自身历史记录 |
| 媒体库同步方式 | `Webhook`（Emby 4.8.0.45+）或 `Scripter X`（需装 Scripter X 插件） |
| 排除路径 | 命中后转交网盘删除插件处理 |
| 媒体库路径映射 | 媒体服务器路径 : MoviePilot 路径，一行一个 |

## 配置文档

- Scripter X 配置：<https://github.com/thsrite/MediaSyncDel/blob/main/MoviePilot/MoviePilot.md>
- 路径映射配置：<https://github.com/thsrite/MediaSyncDel/blob/main/path.md>

## 声明

本插件为第三方改造版本，上游原版版权归原作者 thsrite 所有。使用前请自行
评估删除类插件的风险，建议先在 `删除源文件` 关闭的情况下验证行为。
