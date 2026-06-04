# M3U8 CORS Error: What to Check First

An M3U8 CORS error means the browser blocked access to a playlist, segment, key file, subtitle file, or related HLS resource because the source server did not allow the request from the current origin. This is a browser security rule, not a player preference.

For HLS playback in Chrome, Edge, and Firefox, CORS is especially visible because JavaScript-based playback often requests playlist and segment files through browser APIs.

## Why M3U8 CORS errors happen

Common causes include:

- The playlist server has no `Access-Control-Allow-Origin` header.
- The playlist allows CORS, but segment files do not.
- The master playlist and media playlists are on different domains.
- The CDN blocks hotlinking or unknown origins.
- The stream needs cookies or credentials, but CORS is not configured for credentials.
- A signed URL expires after the playlist loads.
- The source allows Safari native playback but not JavaScript browser requests.

The important point is that CORS must be fixed at the source server or CDN. A browser-side M3U8 player cannot safely bypass it.

## Check the playlist response

Start with the main `.m3u8` URL. The response should be reachable and should include the expected playlist body.

Useful indicators:

```text
HTTP/2 200
content-type: application/vnd.apple.mpegurl
access-control-allow-origin: *
```

The exact content type may vary, but the playlist must be accessible to the browser.

If the response is `403`, `404`, a login page, or an HTML document, the issue is not only CORS. The source is not returning a usable playlist to the browser.

## Check every level of the HLS chain

HLS often has multiple files:

- Master playlist.
- Media playlist.
- Video segments.
- Audio segments.
- Subtitle files.
- Encryption key files.

CORS must be correct for the resources that the browser needs to fetch. It is common to fix CORS on the master playlist and forget the segment path.

## Example: playlist works but segments fail

A master playlist might load from:

```text
https://cdn.example.com/hls/master.m3u8
```

but reference segments from:

```text
https://media.example.net/segments/001.ts
```

If `cdn.example.com` has CORS headers but `media.example.net` does not, playback can fail after the playlist loads.

## Wildcard CORS is not always correct

For public test streams, this may be acceptable:

```text
Access-Control-Allow-Origin: *
```

For private streams using credentials, cookies, or user-specific access, wildcard CORS is usually not the right model. The source owner should configure a specific allowed origin and credential behavior.

Do not make a private stream public just to make a browser test pass.

## CORS and signed URLs

Signed URLs can fail in two different ways:

- The request is blocked by CORS.
- The URL is rejected because the token, signature, or expiration is invalid.

These can look similar in a player UI, but the browser network panel usually shows the difference. Check the HTTP status code and the console error text.

## What an M3U8 player cannot do

A browser-side M3U8 player cannot:

- Ignore browser CORS rules.
- Add permission to a source it does not control.
- Make private URLs public.
- Bypass login requirements.
- Bypass DRM or encryption access rules.
- Override copyright restrictions.

If the stream owner wants the URL to work in browser embeds or external players, the owner must configure the source server and CDN correctly.

## Practical checklist

Check these items in order:

1. Does the `.m3u8` URL return a playlist?
2. Does the response include CORS headers?
3. Do media playlists also include CORS headers?
4. Do segments include CORS headers?
5. Are all domains expected and authorized?
6. Does the stream require credentials?
7. Does the URL expire?
8. Are key files or subtitle files blocked?

## Browser-side test link

You can use the MetisTools M3U8 Player to compare how a public or authorized HLS URL behaves in a browser:

https://metistools.com/m3u8-player
