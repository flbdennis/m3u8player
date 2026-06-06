# Embed an M3U8 Player on Your Site

Embedding an M3U8 player is useful when you want visitors, teammates, or clients to test a public or authorized HLS stream without installing a desktop player. The embed should be treated as a browser playback surface, not as a way to hide or protect the original media URL.

This guide explains a safe embed workflow for M3U8/HLS links.

## When an embed makes sense

Use an embedded M3U8 player when:

- The stream is public or you have permission to test it.
- The source owner allows browser playback from your page.
- The playlist and segment files are reachable over HTTPS.
- The source server or CDN sends the CORS headers needed for browser playback.
- You want a quick way to compare browser behavior with tools like Safari, Chrome, Edge, or Firefox.

Do not use an embed to work around source-side access rules. If the stream needs login, cookies, a license flow, private tokens, or DRM, the source owner must provide the correct playback model.

## Empty player embed

The safest default is an empty player. It lets the user paste a public or authorized M3U8 URL inside the iframe.

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

This approach avoids putting a media URL in the page source.

## Embed with a current stream URL

If you include a stream URL, use only links that are intended to be public or authorized for that use.

MetisTools embed players use the URL hash for the source value:

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

The hash keeps the stream URL out of normal server request paths, but it does not make the URL secret. Users may still see media requests in browser tools.

## What the embed does not do

An embedded browser player does not:

- Proxy the video URL.
- Hide the playlist or segment URLs.
- Add CORS permission to a third-party source.
- Bypass tokens, cookies, login checks, or paywalls.
- Bypass DRM or encryption access rules.
- Make a private stream safe to share publicly.

If the source requires protection, handle that at the source server, CDN, or licensed player level.

## CORS checklist for embeds

For JavaScript-based HLS playback, check that the browser can load:

- The master playlist.
- The selected media playlist.
- Segment files.
- Subtitle files if used.
- Audio playlists if used.
- Key files if the stream uses standard HLS encryption and the source owner expects browser access.

If the playlist loads but playback still fails, check segment requests. It is common for the main playlist to allow CORS while segment files do not.

## Practical embed workflow

1. Test the stream in the full MetisTools M3U8 Player first:

   https://metistools.com/m3u8-player

2. Confirm that the stream is public or authorized.
3. Check the playlist and segment requests in browser DevTools.
4. Use the empty iframe for general testing.
5. Use a prefilled iframe only when the URL is safe to expose.
6. Avoid embedding signed or user-specific URLs in public pages.
7. Document the source owner, expiration behavior, and allowed use.

## Good embed use cases

- Internal QA pages for public streams.
- Documentation pages for a video product.
- Lightweight browser playback demos.
- Public HLS sample testing.
- Comparing Safari native HLS with Chrome JavaScript HLS behavior.

## Bad embed use cases

Avoid using public embeds for:

- Private course videos.
- Paid streaming URLs.
- Login-only media.
- DRM-protected streams.
- Signed URLs that identify a user or session.
- Copyright-restricted content without permission.

## Related guides

- [How to Test an M3U8 Stream in a Browser](test-m3u8-stream-in-browser.md)
- [M3U8 CORS Error: What to Check First](m3u8-cors-error.md)
- [M3U8 Playlist Basics for Browser Playback](m3u8-playlist-basics.md)
