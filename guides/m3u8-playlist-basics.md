# M3U8 Playlist Basics for Browser Playback

An M3U8 file is a text playlist used by HLS. The playlist tells the player where to find video variants, media segments, audio tracks, subtitles, and sometimes encryption key information. The browser does not play the `.m3u8` text itself. It reads the playlist and then requests the media files described inside it.

Knowing the basic structure of an M3U8 playlist makes playback problems easier to debug.

## Playlist inspection checklist

Before debugging player code, check the playlist itself:

- The response starts with `#EXTM3U`.
- A master playlist points to reachable media playlists.
- A media playlist points to reachable segment files.
- Relative paths resolve from the real playlist URL.
- Playlist and segment domains are expected.
- The stream uses browser-supported codecs.
- The playlist and segment responses have suitable MIME types.
- Signed URLs have not expired.
- CORS is configured where browser playback needs it.

## Master playlist

A master playlist usually points to one or more media playlists. It may include different resolutions, bitrates, or codec combinations.

Example:

```text
#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=2800000,RESOLUTION=1280x720
720p/index.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=1400000,RESOLUTION=854x480
480p/index.m3u8
```

If the master playlist loads but the selected media playlist fails, the player may show a playback error even though the first URL worked.

## Media playlist

A media playlist points to actual media segments.

Example:

```text
#EXTM3U
#EXT-X-TARGETDURATION:6
#EXTINF:6.0,
segment001.ts
#EXTINF:6.0,
segment002.ts
```

The browser or player needs to fetch these segment files. If segment requests fail because of CORS, token expiration, wrong paths, or CDN restrictions, playback will fail.

## Relative and absolute paths

M3U8 playlists may use relative paths:

```text
segment001.ts
```

or absolute URLs:

```text
https://cdn.example.com/video/segment001.ts
```

Relative paths are resolved based on the playlist URL. If the playlist is moved, proxied, copied, or served from a different path, relative segment URLs can break.

For browser playback testing, always test the playlist from its real source location.

## Codecs matter

The playlist format is not the same as the video format. A valid M3U8 playlist can still fail if the media uses codecs that the browser cannot decode.

Common web-compatible HLS streams often use H.264 video and AAC audio. Other codecs may require specific browser or device support.

## MIME type

The playlist should ideally be served with a suitable MIME type, such as:

```text
application/vnd.apple.mpegurl
application/x-mpegURL
```

Servers sometimes return `text/plain` or a generic type. Some players tolerate this, while others may fail or produce confusing errors.

## Live vs VOD playlists

VOD playlists usually include an end marker:

```text
#EXT-X-ENDLIST
```

Live playlists often update over time and may not include that marker. A live playlist can fail if the server stops updating, segment URLs expire too quickly, or the player cannot keep up with the live window.

## Encryption and DRM

Some playlists reference encryption keys or protected playback flows. A browser-side player cannot bypass encryption, DRM, login checks, token rules, or source permissions. If the stream requires an authorized license or key flow, the source owner must provide it.

## Common playlist problems

Look for:

- Missing `#EXTM3U`.
- Broken media playlist paths.
- Segment URLs returning `403` or `404`.
- Mixed HTTP and HTTPS resources.
- Expired signed segment URLs.
- Missing CORS headers on playlist or segments.
- Unsupported codecs.
- Incorrect MIME types.

## Browser-side test link

To inspect how a public or authorized M3U8 playlist behaves in a browser, you can test it with the MetisTools M3U8 Player:

https://metistools.com/m3u8-player
