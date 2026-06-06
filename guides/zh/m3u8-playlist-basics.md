# M3U8 播放列表基础：Master、Media 和分片

M3U8 不是视频文件本身。它更像一份播放清单，告诉播放器应该加载哪些媒体文件、有哪些清晰度、每个分片在哪里、时长是多少、是否有音频轨道或字幕。

理解 M3U8 playlist 的结构，能帮助你判断为什么一个 HLS stream 在浏览器里能播、卡住或完全失败。

## M3U8 文件是什么

M3U8 文件本质上是文本文件，常用于 HLS 播放。

一个简单的 media playlist 可能长这样：

```text
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:6
#EXTINF:6.000,
segment001.ts
#EXTINF:6.000,
segment002.ts
#EXT-X-ENDLIST
```

播放器会按顺序加载这些 segment，并把它们连续播放出来。

## Master playlist

Master playlist 通常负责列出不同清晰度或不同版本。

例如：

```text
#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=2800000,RESOLUTION=1280x720
720p/playlist.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=5000000,RESOLUTION=1920x1080
1080p/playlist.m3u8
```

这类文件不会直接列出所有 `.ts` 分片，而是指向各个 media playlist。

如果你用 M3U8 在线播放器测试，播放器会先读 master playlist，再选择其中一个清晰度继续加载。

## Media playlist

Media playlist 才是列出真实分片的地方。

它可能包含：

- `.ts` 分片
- `.m4s` 分片
- 初始化片段
- 音频分片
- 字幕分片
- key 信息

浏览器播放失败时，经常不是 master playlist 出错，而是 media playlist 或 segment 请求出错。

## Segment 分片

HLS 通常不是一个大视频文件，而是一段一段的小文件。

常见分片类型：

```text
segment001.ts
segment002.ts
fileSequence0.m4s
```

如果某个分片返回 403、404、CORS 错误或超时，播放就可能卡住。

这就是为什么 M3U8 player 需要关注 segment loading，而不是只看第一个 `.m3u8` 链接。

## 相对路径和绝对路径

M3U8 里常见两种 URL：

```text
https://cdn.example.com/video/segment001.ts
```

或者：

```text
segment001.ts
../audio/playlist.m3u8
```

相对路径需要根据 playlist 当前地址拼接。如果源站路径、CDN rewrite 或代理配置有问题，分片 URL 很容易拼错。

## 常见标签

几个常见标签：

| 标签 | 含义 |
|---|---|
| `#EXTM3U` | M3U8 文件开头 |
| `#EXT-X-STREAM-INF` | 多清晰度版本信息 |
| `#EXTINF` | 单个分片时长 |
| `#EXT-X-TARGETDURATION` | 目标分片时长 |
| `#EXT-X-ENDLIST` | 点播流结束 |
| `#EXT-X-KEY` | 加密信息 |
| `#EXT-X-MAP` | fMP4 初始化片段 |

普通用户不需要记住所有标签，但排查问题时至少要分清 master playlist、media playlist 和 segment。

## 为什么 playlist 能打开但视频不能播

常见原因：

- playlist 可以跨域访问，segment 不允许。
- 清晰度 playlist 路径错误。
- 音频或字幕 playlist 失败。
- 分片 MIME 类型错误。
- codec 不被当前浏览器支持。
- URL 里 token 过期。
- 流带 DRM 或需要授权。

所以在浏览器里测 M3U8，不要只复制文件内容看一眼，还要确认每层资源都能被浏览器加载。

## 和 M3U8 parser、Analyzer 的关系

`m3u8 parser` 或 `M3U8 analyzer` 的作用是把 playlist 拆开看清楚：

- 它是 master 还是 media playlist。
- 有几个清晰度。
- 有多少 segment。
- 是否出现 key、字幕、音频轨道。
- 哪些路径可能导致加载失败。

这类分析不能替代真实播放，但能让排查更快。

## 相关指南

- [如何在浏览器中测试 M3U8 流](test-m3u8-stream-in-browser.md)
- [M3U8 CORS 错误排查](m3u8-cors-error.md)
- [开源 M3U8 播放器方案](open-source-m3u8-player-libraries.md)
