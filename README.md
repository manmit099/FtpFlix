# FtpFlix

A minimal Android app that browses videos on your FTP-server phone and plays
them with a Netflix-style home screen, backed by libVLC.

## What's inside
- **FTP browsing** — `FtpRepository.kt` (Apache Commons Net) recursively
  scans your FTP server and groups video files by folder into rows.
- **Playback** — `PlayerActivity.kt` uses libVLC (the same engine as VLC for
  Android), which understands `ftp://` URLs natively — no need to download
  files first.
- **UI** — `HomeScreen.kt` (Jetpack Compose) renders a hero banner + horizontal
  rows of poster cards, Netflix-style. Posters are generated gradients (no
  real thumbnail extraction from FTP video yet — see "Next steps" below).

## Setup

1. **Open in Android Studio** (Koala or newer recommended) — "Open an
   existing project" and point it at the `FtpFlix` folder.

2. **Edit your server details** in
   `app/src/main/java/com/example/ftpflix/FtpConfig.kt`:
   ```kotlin
   const val HOST = "192.168.1.100"   // your phone's FTP IP
   const val PORT = 2221              // your FTP server's port
   const val USERNAME = "anonymous"
   const val PASSWORD = ""
   const val ROOT_PATH = "/"          // folder to browse
   ```
   Most Android FTP server apps show the exact `ftp://ip:port` address on
   their home screen when the server is running — copy host/port from there.

3. **Check the libVLC version** in `app/build.gradle` — I pinned
   `org.videolan.android:libvlc-all:3.6.4`, but confirm the latest release at
   https://mvnrepository.com/artifact/org.videolan.android/libvlc-all and bump
   it if needed.

4. **Build & run**: `Build > Make Project`, then run on a device on the same
   Wi-Fi network as your FTP phone (emulators can't reach it unless you set
   up network routing).

## Notes
- Both phones must be on the same local network (or otherwise routable).
- If your FTP server requires a real login, set `USERNAME`/`PASSWORD` in
  `FtpConfig.kt`.
- `network_security_config.xml` allows cleartext traffic, since plain FTP
  isn't encrypted — fine for a home network, not for anything public-facing.

## Next steps you might want
- **Real thumbnails**: extract a frame from each video instead of using
  gradient placeholders. This needs either (a) downloading a byte range of
  each file locally and running it through `MediaMetadataRetriever`, or
  (b) a lightweight thumbnail cache generated once and stored in Room.
- **Settings screen**: replace hardcoded `FtpConfig` values with a proper
  in-app settings screen + DataStore, so you're not editing/rebuilding the
  app every time your server's IP changes.
- **Resume playback**: store last-watched position per file (Room or
  DataStore) and seek to it via `mediaPlayer.time` on `Media` load.
- **TMDb metadata**: match filenames against The Movie Database API to pull
  real posters, descriptions, and cast info for a more authentic Netflix feel.
