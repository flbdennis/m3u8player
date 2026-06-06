# 开源 M3U8 播放器方案：hls.js、video.js 和在线测试工具

如果你想在自己的网站里播放 M3U8/HLS，通常会遇到几个选择：直接用在线 M3U8 播放器测试、用 hls.js 写一个轻量播放器、用 video.js 做完整播放器 UI，或者在 DASH 场景里用 dash.js / Shaka Player。

这篇文章不是简单罗列 GitHub 项目，而是帮你判断：什么时候用哪种方案，什么时候问题不在播放器库，而在源站配置。

## 快速对比

| 方案 | 适合场景 | 说明 |
|---|---|---|
| hls.js | Chrome、Edge、Firefox 中播放 HLS/M3U8 | 常见、轻量、适合自定义播放器 |
| video.js | 需要完整 UI 和插件生态 | 适合正式网站播放器 |
| dash.js | 播放 DASH / MPD | 不是 M3U8 主方案 |
| Shaka Player | 多格式、复杂播放项目 | 适合更完整的流媒体项目 |
| Safari 原生 HLS | Apple 设备验证 | 适合测试 Safari / iPhone / iPad |
| 在线 M3U8 Player | 快速排查链接 | 适合先判断流是否可播放 |

## hls.js

hls.js 是很多开发者做网页端 M3U8 player 时的第一选择。

适合：

- Chrome、Edge、Firefox 等浏览器播放 HLS。
- 想自己控制 UI。
- 需要读取播放错误和网络错误。
- 想排查 CORS、segment、codec、MIME 等问题。

不适合：

- 绕过源站权限。
- 播放 DRM 内容。
- 读取没有 CORS 权限的第三方资源。
- 处理登录后才能访问的视频。

GitHub：

```text
https://github.com/video-dev/hls.js
```

## video.js

video.js 更像一个完整播放器框架，不只是 HLS 加载器。

适合：

- 需要标准播放器 UI。
- 需要皮肤、插件、事件、扩展生态。
- 正式网站视频页。
- 想减少自己写控制栏和交互逻辑。

如果你只是想确认一个 M3U8 链接能不能播，先用在线工具测更快。如果要上线正式播放器，再考虑 video.js。

GitHub：

```text
https://github.com/videojs/video.js
```

## dash.js

dash.js 主要用于 MPEG-DASH，也就是 `.mpd` manifest。

适合：

- 你的源是 MPD，不是 M3U8。
- 你要排查 DASH representation、adaptation set、segment template。
- 你的项目同时有 DASH 播放需求。

GitHub：

```text
https://github.com/Dash-Industry-Forum/dash.js
```

## Shaka Player

Shaka Player 更偏完整流媒体播放框架，适合复杂项目。

适合：

- 同时处理 HLS 和 DASH。
- 有更完整的播放器需求。
- 源站有合法 DRM 或授权流程。
- 项目需要生产级播放能力。

但它也不能绕过源站限制。如果 CORS、token、DRM license 或 CDN 配置不正确，换成 Shaka 也不会自动解决。

GitHub：

```text
https://github.com/shaka-project/shaka-player
```

## Safari 原生 HLS

Safari 在 Apple 设备上对 HLS 支持更好。你可以用它确认一个 M3U8 在 Apple 平台上的基础表现。

但要注意：

- Safari 能播，不代表 Chrome 能播。
- Safari 成功不代表 CORS 配置对所有浏览器都正确。
- iPhone 能播，不代表桌面端 hls.js 能播。

所以 Safari 测试只能说明一部分结果。

## Vue 播放 M3U8 应该怎么选

很多中文用户会搜 `vue播放m3u8`。如果你的项目是 Vue，通常可以先考虑 hls.js。

基本思路是：

1. 页面里放 `<video>`。
2. 检查浏览器是否支持 HLS 原生播放。
3. 如果不支持，使用 hls.js 加载 `.m3u8`。
4. 监听 error 事件。
5. 用 DevTools 检查 playlist 和 segment 请求。

但 Vue 只是框架，不会自动解决 CORS、token、MIME 或 codec 问题。

## 先测试，再选库

在接入播放器库之前，建议先用简单工具测试链接：

```text
https://metistools.com/zh/m3u8-player
```

如果在线播放器也无法加载，说明问题大概率在源站、CDN、浏览器限制或流本身，而不是你的 Vue、React 或播放器库写法。

## 选择建议

简单规则：

- 只想快速测试 M3U8 链接：用 M3U8 在线播放器。
- 要在 Chrome / Edge 播放 HLS：先看 hls.js。
- 要完整播放器 UI：看 video.js。
- 要播放 MPD / DASH：看 dash.js 或 Shaka Player。
- 要确认 Apple 设备表现：测 Safari 原生 HLS。
- 要正式上线：同时检查 CORS、MIME、codec、HTTPS、token 和 CDN 配置。

## 开源播放器不能解决什么

无论是 hls.js、video.js、dash.js 还是 Shaka Player，都不能解决这些问题：

- 源站拒绝跨域访问。
- URL 已过期。
- 需要登录或 Cookie。
- CDN 禁止外部 Referer。
- DRM license 不可用。
- 浏览器不支持当前 codec。
- 未授权内容的播放限制。

如果源站没有允许网页播放，播放器库不能替源站开放权限。

## 相关指南

- [如何在网站中嵌入 M3U8 播放器](embed-m3u8-player-on-your-site.md)
- [Safari HLS 和 Chrome HLS 的区别](safari-hls-vs-chrome-hls.md)
- [HLS 在 Chrome 里无法播放](hls-not-playing-in-chrome.md)
