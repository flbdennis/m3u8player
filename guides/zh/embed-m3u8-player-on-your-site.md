# 如何在网站中嵌入 M3U8 播放器

如果你有一个公开或已授权的 HLS/M3U8 流，想让用户在网页里直接测试播放，可以使用 iframe 嵌入一个 M3U8 player。

这个方法适合测试、文档、内部 QA、公开示例流展示。它不适合隐藏视频地址，也不适合绕过权限限制。

## 什么时候适合嵌入

适合场景：

- 你控制这个网站或页面。
- 你有权限测试这个 M3U8/HLS 链接。
- 源站允许浏览器播放。
- playlist 和 segment 都能通过 HTTPS 访问。
- CDN 已经配置好 CORS。
- 你只是想提供一个轻量测试入口。

不适合场景：

- 私有课程视频。
- 会员视频。
- DRM 保护内容。
- 登录后才能访问的链接。
- 带用户身份的临时 URL。
- 未授权的版权内容。

## 空播放器嵌入

最安全的方式是嵌入空播放器，让用户自己粘贴公开或已授权的 M3U8 URL。

```html
<iframe
  title="MetisTools M3U8 Player"
  src="https://metistools.com/embed/m3u8"
  width="100%"
  height="360"
  style="border:0;aspect-ratio:16/9"
  allow="autoplay; fullscreen; encrypted-media"
  allowfullscreen
  loading="lazy"
  referrerpolicy="strict-origin-when-cross-origin">
</iframe>
```

这种方式不会把某个具体媒体 URL 写进页面源码，适合工具页、文档页和测试页。

## 带默认流地址的嵌入

如果你要预填一个流地址，只应使用公开或已授权的链接。

示例：

```html
<iframe
  title="MetisTools M3U8 Player"
  src="https://metistools.com/embed/m3u8#src=https%3A%2F%2Fexample.com%2Fvideo%2Fmaster.m3u8"
  width="100%"
  height="360"
  style="border:0;aspect-ratio:16/9"
  allow="autoplay; fullscreen; encrypted-media"
  allowfullscreen
  loading="lazy"
  referrerpolicy="strict-origin-when-cross-origin">
</iframe>
```

这里使用 URL hash 传递源地址，可以避免把源地址放进普通请求路径。但它并不会让这个地址变成秘密。用户仍然可能在浏览器开发者工具里看到媒体请求。

## iframe 嵌入不能做什么

iframe 不是代理，也不是加密保护。

它不能：

- 隐藏 playlist 或 segment URL。
- 自动给第三方源站加 CORS。
- 绕过 token、Cookie、登录或付费墙。
- 绕过 DRM。
- 让私有视频变成安全公开视频。
- 代替源站权限控制。

如果你需要保护内容，应在源站、CDN、授权系统或合法播放器方案里处理。

## CORS 检查

嵌入前至少检查这些资源能否被浏览器加载：

- master playlist
- media playlist
- video segments
- audio playlist
- subtitle files
- key files，如果内容方允许浏览器访问

常见问题是主 `.m3u8` 可以加载，但 `.ts` 分片没有 CORS，导致播放器一直卡住。

## 推荐工作流

1. 先在 [MetisTools M3U8 在线播放器](https://metistools.com/zh/m3u8-player) 里测试。
2. 确认链接是公开或已授权的。
3. 用浏览器 DevTools 检查 playlist 和 segment 请求。
4. 对普通用户页面使用空播放器。
5. 只有在 URL 安全公开时，才使用预填 URL 的 iframe。
6. 不要把签名 URL、用户专属 URL 放到公开页面。
7. 在文档中说明该流的来源、用途和过期规则。

## 适合放在哪些页面

- 视频产品文档。
- CDN 或 HLS 配置测试页。
- 内部 QA 页面。
- 公开示例流页面。
- 开发者调试文档。

## 不建议放在哪些页面

- 资源站或盗链页面。
- 会员内容分享页。
- 未授权影视内容页。
- 试图隐藏原视频地址的页面。
- 把 iframe 当作下载入口的页面。

## 相关指南

- [如何在浏览器中测试 M3U8 流](test-m3u8-stream-in-browser.md)
- [M3U8 CORS 错误排查](m3u8-cors-error.md)
- [开源 M3U8 播放器方案](open-source-m3u8-player-libraries.md)
