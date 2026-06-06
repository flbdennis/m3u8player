# M3U8 Player Notes

Practical notes for testing M3U8/HLS playback in modern browsers.

This repository is a lightweight resource hub for developers, site owners, and technical users who need to check whether an M3U8/HLS stream can play in a browser. It focuses on browser playback behavior, playlist loading, segment requests, CORS, MIME types, codec support, CDN behavior, embed usage, and common playback errors.

Live tool:

- M3U8 Player Online: https://metistools.com/m3u8-player
- MP4 Player Online: https://metistools.com/mp4-player
- DASH Player Online: https://metistools.com/dash-player
- Guides: https://metistools.com/guides

## What this repository is for

Use these notes when you need to:

- Test a public or authorized M3U8/HLS stream in a browser.
- Check why a stream works in Safari or VLC but fails in Chrome.
- Understand CORS, MIME type, codec, HTTPS, signed URL, and segment request issues.
- Embed a lightweight M3U8 player on a page you control.
- Compare common open source player libraries before building your own web player.
- Document safe playback testing workflows for public or authorized streams.

## What this repository is not for

This repository does not cover:

- Video downloading.
- Stream ripping.
- DRM bypassing.
- Login, cookie, token, or paywall workarounds.
- Private access extraction.
- Copyright-restricted redistribution.
- “Download any video” workflows.

If a stream is private, protected, signed, or permission-limited, the source owner must provide the correct access model. Browser tools cannot safely bypass source-side restrictions.

## Start here

Recommended reading order:

1. [Public M3U8 Test Stream Checklist](guides/public-test-stream-checklist.md)
2. [How to Test an M3U8 Stream in a Browser](guides/test-m3u8-stream-in-browser.md)
3. [M3U8 Playlist Basics for Browser Playback](guides/m3u8-playlist-basics.md)
4. [M3U8 CORS Error: What to Check First](guides/m3u8-cors-error.md)
5. [HLS Not Playing in Chrome: Practical Checks](guides/hls-not-playing-in-chrome.md)

## Browser playback guides

- [How to Test an M3U8 Stream in a Browser](guides/test-m3u8-stream-in-browser.md)
- [Safari HLS vs Chrome HLS: Browser Playback Differences](guides/safari-hls-vs-chrome-hls.md)
- [HLS Not Playing in Chrome: Practical Checks](guides/hls-not-playing-in-chrome.md)
- [M3U8 CORS Error: What to Check First](guides/m3u8-cors-error.md)
- [M3U8 Playlist Basics for Browser Playback](guides/m3u8-playlist-basics.md)
- [Public M3U8 Test Stream Checklist](guides/public-test-stream-checklist.md)

## Developer resources

- [Embed an M3U8 Player on Your Site](guides/embed-m3u8-player-on-your-site.md)
- [Open Source M3U8 Player Libraries](guides/open-source-m3u8-player-libraries.md)

These developer resources are meant to make the repository useful beyond one online player page. They explain how to embed a player safely and how to choose a playback library when building your own implementation.

## Quick browser test workflow

Use this workflow before changing player code:

1. Confirm that the URL is a direct `.m3u8` playlist, not a video webpage.
2. Confirm that the stream is public or you are authorized to test it.
3. Open the URL in the MetisTools M3U8 Player.
4. Compare Chrome or Edge with Safari if the result is unclear.
5. Check whether the playlist, media playlist, and segments load.
6. Record HTTP status codes, CORS messages, MIME type, codec information, and whether the URL is signed or temporary.
7. Fix the source server or CDN configuration where needed.

## Embed example

Use the empty embed player when you want users to paste their own public or authorized stream URL:

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

If you include a stream URL, use only public or authorized links and remember that iframe embedding does not hide, proxy, or protect the original media URL.

See: [Embed an M3U8 Player on Your Site](guides/embed-m3u8-player-on-your-site.md)

## Recommended GitHub topics

If you use this as a public GitHub project, add topics such as:

```text
m3u8
hls
hls-player
m3u8-player
video-player
streaming
browser-video
hls-js
videojs
dash
mp4
web-tools
```

## Safe use boundary

The notes here are for public or authorized playback testing. They are not legal advice, CDN configuration guarantees, or instructions for bypassing source-side restrictions.

## License

MIT License. See [LICENSE](LICENSE).
