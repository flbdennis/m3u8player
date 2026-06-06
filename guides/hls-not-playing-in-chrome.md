# HLS Not Playing in Chrome: Practical Checks

When an HLS stream plays in Safari but not in Chrome, the issue is often not the M3U8 file itself. Safari has native HLS support on Apple platforms, while Chrome usually depends on MediaSource and a JavaScript HLS implementation such as hls.js. That difference changes how errors appear and which server headers matter.

This note explains what to check when an M3U8 or HLS stream is not playing in Chrome. It is intended for public or authorized streams only.

## Browser comparison table

| Browser | HLS behavior | What it is useful for |
| --- | --- | --- |
| Safari | Uses native HLS support on Apple platforms. | Checking whether the stream works in the Apple media stack. |
| Chrome | Usually needs MediaSource and JavaScript HLS playback. | Finding CORS, segment, MIME, codec, and MediaSource issues. |
| Edge | Similar to Chrome because it is Chromium-based. | Confirming whether a Chrome failure is Chromium-related. |
| Firefox | Depends on platform media support and JavaScript playback behavior. | Catching additional browser compatibility problems. |

If Safari works but Chrome fails, treat Chrome as a web playback debugging signal. It often reveals issues that native HLS playback may hide.

## First check: is the URL really an M3U8 playlist?

Chrome cannot play a webpage, iframe URL, or video landing page as an HLS playlist. The URL should point to a playlist response, often ending with `.m3u8`.

Open the URL in a browser tab. A playlist usually contains tags like:

```text
#EXTM3U
#EXT-X-STREAM-INF
#EXTINF
```

If the response is HTML, a login page, a redirect page, or an error page, Chrome is not receiving the HLS playlist.

## Check CORS headers

CORS is one of the most common reasons HLS fails in Chrome. A JavaScript HLS player normally fetches the playlist and segments through browser requests. If the source server does not allow the requesting origin, Chrome may block the response even when the URL works in another tool.

Look for headers like:

```text
Access-Control-Allow-Origin: *
```

or a specific allowed origin:

```text
Access-Control-Allow-Origin: https://your-site.example
```

The correct value depends on the source owner and security model. If cookies or credentials are involved, wildcard CORS is not enough. The source-side configuration must be deliberate.

## Check segment requests, not only the playlist

An HLS playlist can load successfully while the actual media segments fail. In Chrome DevTools, check whether segment requests return:

- `200 OK`
- `403 Forbidden`
- `404 Not Found`
- `410 Gone`
- `5xx` server errors
- CORS blocked responses

If segments fail but the playlist loads, the issue may be token expiration, relative URL paths, CDN rules, hotlink protection, missing CORS headers on segment files, or inconsistent domains.

## Check codecs

M3U8 is a playlist format, not a codec. Chrome still needs to support the actual video and audio codecs inside the segments.

Commonly supported combinations include H.264 video with AAC audio. Streams using unsupported codecs, unusual profiles, or platform-specific encoding choices may fail even if the playlist is valid.

## Check MIME type

Some browsers and player libraries are stricter when the server sends unexpected content types. Useful response types include:

```text
application/vnd.apple.mpegurl
application/x-mpegURL
```

Segments should also be served with suitable media-related content types. A wrong MIME type is not always fatal, but it can make debugging harder and can trigger browser or player rejection.

## Check HTTPS and mixed content

If the page is loaded over HTTPS, Chrome may block insecure HTTP media requests. Use HTTPS for the page, playlist, segment files, and related resources whenever possible.

Mixed content issues can look like playback failures even when the playlist itself is reachable.

## Check signed URLs and private streams

Signed HLS URLs often include parameters such as `token`, `expires`, `signature`, or similar values. These links may work for a short time and then fail.

Chrome is not the right place to bypass these rules. If the stream is private, the source owner should provide an authorized playback flow, short-lived URLs, and matching CDN access rules.

## Practical debugging order

Use this order before changing player settings:

1. Confirm the URL returns an M3U8 playlist.
2. Confirm the playlist begins with `#EXTM3U`.
3. Check Chrome DevTools for playlist and segment requests.
4. Look for CORS errors.
5. Check HTTP status codes.
6. Check MIME types.
7. Check codec compatibility.
8. Compare with Safari.
9. Test whether the URL expires.

## Browser-side test link

To compare Chrome behavior with another browser, test the same public or authorized URL in the MetisTools M3U8 Player:

https://metistools.com/m3u8-player
