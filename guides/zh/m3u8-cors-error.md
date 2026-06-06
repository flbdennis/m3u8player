# M3U8 CORS 错误排查：先看这几个地方

M3U8 CORS 错误是网页播放器最常见的问题之一。很多流在 VLC 里能播放，但放进 Chrome 的 M3U8 在线播放器就失败，原因通常不是播放器坏了，而是浏览器不允许跨域读取源站资源。

这篇文章适合排查：`m3u8 cors`、`HLS CORS error`、`m3u8 播放失败`、`hls.js 跨域错误`、`M3U8 在线播放器不能播` 等问题。

## CORS 是什么

CORS 是浏览器的跨域访问规则。

当你的网页在：

```text
https://metistools.com
```

尝试读取：

```text
https://cdn.example.com/video/master.m3u8
```

浏览器会检查 `cdn.example.com` 是否允许这个网页读取响应内容。

如果源站没有返回正确的 CORS 响应头，JavaScript 播放器就读不到 playlist 或 segment。

## 为什么 VLC 能播，浏览器不能播

VLC 是桌面软件，不按浏览器的同源策略执行。浏览器里的 hls.js、video.js 或其他 HLS player 必须遵守 CORS。

所以会出现：

```text
VLC 可以播放
Safari 可能可以播放
Chrome / Edge 失败
网页播放器失败
```

这通常说明源站没有为浏览器外部播放配置好 CORS。

## 先检查 playlist

打开浏览器 DevTools 的 Network 面板，看 `.m3u8` 请求：

- 状态码是不是 200。
- 有没有 `Access-Control-Allow-Origin`。
- 返回内容是不是 playlist，而不是 HTML 错误页。
- 是否被重定向到登录页或错误页。

如果 playlist 自己都读不到，播放器不会继续工作。

## 再检查 segment

很多人只给 `.m3u8` 加了 CORS，忘记给 `.ts`、`.m4s` 分片加。

这会导致：

- 播放器能读到清单。
- 画面一直转圈。
- 日志显示 segment loading failed。
- Network 里 segment 请求被 CORS 拦截。

M3U8/HLS 不是只加载一个文件。每一层资源都要能被浏览器访问。

## 常见正确方向

如果你控制源站或 CDN，通常需要让以下资源也支持跨域访问：

- master playlist
- media playlist
- video segments
- audio playlist
- subtitle files
- key files，如果源站允许这种访问

常见响应头类似：

```text
Access-Control-Allow-Origin: https://your-site.example
```

测试阶段可以用更宽松配置，但生产环境建议按实际站点限制，不要随意开放所有来源。

## 不要把 CORS 当成前端 bug

如果浏览器报 CORS，前端页面通常不能强行修复。

这些做法不能真正解决问题：

- 在前端代码里加一个参数。
- 改播放器按钮。
- 用 iframe 包一层。
- 换一个在线 m3u8 player。
- 尝试隐藏原始链接。

正确位置通常在源站、CDN、对象存储或反向代理配置。

## token 和 CORS 经常一起出现

有些链接不是纯 CORS 问题，而是：

- URL 已过期。
- token 只允许原网站访问。
- Referer 不匹配。
- Cookie 或登录态缺失。
- CDN 拒绝外部请求。

这种情况下，即使 CORS 配好了，浏览器也可能继续返回 403。

## 快速排查顺序

1. 用 [MetisTools M3U8 在线播放器](https://metistools.com/zh/m3u8-player) 测试链接。
2. 打开 DevTools Network。
3. 先看 master playlist。
4. 再看 media playlist。
5. 再看第一个失败的 segment。
6. 检查状态码、CORS 响应头、MIME 类型和 token。
7. 回到源站或 CDN 修复配置。

## 什么时候不是 CORS

如果没有 CORS 错误，但仍然不能播，可能是：

- codec 不支持。
- MIME 类型错误。
- segment 文件损坏。
- playlist 结构有问题。
- DRM 保护。
- 浏览器不支持当前封装。

所以 CORS 是第一步，不是所有问题的答案。

## 相关指南

- [如何在浏览器中测试 M3U8 流](test-m3u8-stream-in-browser.md)
- [Safari HLS 和 Chrome HLS 的区别](safari-hls-vs-chrome-hls.md)
- [M3U8 播放列表基础](m3u8-playlist-basics.md)
