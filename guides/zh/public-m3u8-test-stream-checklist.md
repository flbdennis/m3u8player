# 公开 M3U8 测试流检查清单

在测试一个 M3U8/HLS 链接之前，先确认它是不是适合放进浏览器播放器里测。很多“播放失败”不是播放器坏了，而是链接本身有权限、跨域、过期时间或源站配置限制。

这份清单适合用于 M3U8 在线播放器、HLS player、m3u8 player online、网页端视频测试工具，以及你自己的测试页面。

## 先确认链接类型

一个可测试的 M3U8 链接通常长这样：

```text
https://example.com/live/master.m3u8
https://cdn.example.com/video/playlist.m3u8
```

它应该是直接的 `.m3u8` 播放列表，不是普通网页地址。

常见误用：

- 把视频网页地址当成 M3U8 URL。
- 把需要登录后才能访问的链接拿到外部工具测试。
- 把已经过期的临时签名 URL 当成稳定地址。
- 把浏览器开发者工具里看到的中间请求当成最终播放地址。

## 权限检查

测试前先问三个问题：

1. 这个流是公开的吗？
2. 你是否有权限测试它？
3. 源站是否允许外部网页加载它？

如果答案不确定，应该先在自己的源站、CDN 或播放器配置里确认，而不是尝试绕过限制。

MetisTools 这类 M3U8 播放器只适合测试公开或已授权的视频链接，不负责绕过 DRM、登录、token、Cookie、付费墙或地域限制。

## 浏览器能否访问

浏览器端播放 HLS/M3U8 时，不只是请求一个文件。通常会请求：

- master playlist
- media playlist
- `.ts` 或 `.m4s` 分片
- 音频 playlist
- 字幕文件
- key 文件，如果源站允许浏览器访问

如果主 playlist 能打开，但分片被拦截，播放器仍然会失败。

## CORS 检查

如果你要在 Chrome、Edge、Firefox 里播放 M3U8，源站需要允许浏览器跨域访问相关资源。

重点检查：

- playlist 是否有正确 CORS 响应头
- segment 是否也有 CORS 响应头
- key 文件是否允许访问
- CDN 是否只给主文件加了 CORS，忘记给分片加

很多 HLS stream 在 VLC 里能播，在 Chrome 里失败，原因就在这里。

## HTTPS 检查

如果你的页面是 HTTPS，而 M3U8 或分片是 HTTP，浏览器可能会拦截混合内容。

建议：

- 页面使用 HTTPS
- M3U8 使用 HTTPS
- 分片、字幕、key 文件也使用 HTTPS

不要只检查主 URL。HLS 的问题经常藏在 segment URL 里。

## MIME 类型检查

源站返回的 Content-Type 不一定决定全部结果，但错误 MIME 会增加播放失败概率。

常见建议：

```text
.m3u8  -> application/vnd.apple.mpegurl 或 application/x-mpegURL
.ts    -> video/mp2t
.mp4   -> video/mp4
.m4s   -> video/iso.segment 或合适的媒体类型
```

如果你只是在临时服务器或对象存储里放文件，MIME 类型很容易被配置错。

## 编码检查

M3U8 只是播放列表，真正能不能播放还取决于里面的视频编码。

常见问题：

- Safari 能播，Chrome 不能播。
- 音频能播，画面不出。
- 某些清晰度能播，某些清晰度失败。

这通常和 codec、封装、浏览器支持有关。不要只看 `.m3u8` 后缀。

## 临时 URL 和 token

很多 M3U8 链接带有 token、expires、signature 等参数。

这些链接可能：

- 几分钟后过期。
- 只能在指定 IP 使用。
- 只能在指定 Referer 使用。
- 只能在登录态下访问。

如果你把这类链接放到外部 m3u8 player 里，失败很正常。

## 推荐测试流程

1. 确认这是直接 `.m3u8` 链接。
2. 确认你有测试权限。
3. 用 [MetisTools M3U8 在线播放器](https://metistools.com/zh/m3u8-player) 先测能否播放。
4. 查看播放诊断日志。
5. 如果失败，再检查 CORS、HTTPS、MIME、codec、token 和 segment 请求。
6. 如果 Safari 和 Chrome 结果不同，优先检查 CORS 和 MediaSource 支持。

## 不适合测试的情况

这些场景不适合用普通网页播放器测试：

- DRM 保护视频。
- 登录后才能看的私有内容。
- 付费课程或会员视频。
- 带用户身份的临时 URL。
- 未获得授权的版权内容。

如果你是内容方，应从源站和 CDN 正确配置播放权限，而不是靠外部工具绕过限制。

## 相关指南

- [如何在浏览器中测试 M3U8 流](test-m3u8-stream-in-browser.md)
- [M3U8 CORS 错误排查](m3u8-cors-error.md)
- [M3U8 播放列表基础](m3u8-playlist-basics.md)
