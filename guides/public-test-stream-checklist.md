# Public M3U8 Test Stream Checklist

A public M3U8 test stream is useful for checking browser playback behavior, but not every URL that loads in a player is safe, stable, or suitable for public examples. Before using a stream in documentation, demos, or browser testing, check both the technical behavior and the permission boundary.

This checklist is for public or authorized HLS testing. It is not a guide to finding copyrighted streams or bypassing access controls.

## Permission checklist

Before testing or sharing an M3U8 URL, ask:

- Is the stream intended to be public?
- Are you authorized to test it?
- Does the source owner allow external browser playback?
- Is the URL temporary, signed, or user-specific?
- Does the stream require cookies, login, or an app session?
- Does the source have copyright or redistribution restrictions?

If the stream is private or unclear, do not use it as a public test example.

## Technical checklist

Check the main playlist:

- The URL returns `200 OK`.
- The response body starts with `#EXTM3U`.
- The server returns a reasonable content type.
- The playlist is reachable over HTTPS.
- The URL does not immediately redirect to HTML or a login page.

Check the media chain:

- Media playlists load.
- Segment files load.
- Audio and subtitle tracks load if used.
- Segment paths resolve correctly.
- Tokenized URLs do not expire too quickly.

Check browser behavior:

- Safari can test native HLS behavior.
- Chrome or Edge can test JavaScript HLS behavior.
- Firefox can expose additional compatibility issues.
- DevTools shows useful network responses.

## CORS checklist

For JavaScript-based playback, check:

- The master playlist has CORS headers.
- Media playlists have CORS headers.
- Segment files have CORS headers.
- Key files and subtitle files have CORS headers if used.
- The allowed origin matches the intended use.

If the stream is private, do not use broad public CORS just to make testing easier. Use the correct source-side access model.

## Codec checklist

A browser needs to decode the actual media, not just read the playlist.

Check:

- Video codec.
- Audio codec.
- Browser support.
- Device support.
- Whether the stream uses a codec profile unsupported by the target browser.

If a stream works in one browser but not another, codec support may be part of the reason.

## Stability checklist

For a public test stream, prefer sources that are:

- Publicly documented.
- Stable over time.
- Not tied to one user session.
- Not protected by private tokens.
- Not likely to disappear without notice.
- Suitable for repeated lightweight testing.

Avoid using random live streams as examples. They can disappear, change access rules, or create copyright concerns.

## What to record during testing

When a stream fails, record:

- Browser and version.
- Playlist URL domain.
- HTTP status code.
- First failed request.
- CORS error text.
- MIME type.
- Codec information if available.
- Whether Safari and Chrome behave differently.

Avoid publishing private URLs, tokens, signatures, cookies, or internal CDN paths in logs.

## Browser-side test link

For quick browser-side checks of a public or authorized HLS URL, use the MetisTools M3U8 Player:

https://metistools.com/m3u8-player
