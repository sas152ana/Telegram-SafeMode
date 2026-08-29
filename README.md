# Telegram SafeMode, Channel Feeds & Built-in WS Proxy (Android Client Mod)

[![License: GPL-2.0](https://img.shields.io/badge/License-GPL%20v2-blue.svg)](https://www.gnu.org/licenses/gpl-2.0)
[![Platform](https://img.shields.io/badge/Platform-Android-green.svg)](https://developer.android.com)
[![Base: Official Telegram](https://img.shields.io/badge/Base-DrKLO%2FTelegram-blue.svg)](https://github.com/DrKLO/Telegram)

A privacy-focused, anti-censorship, and feature-enhanced modification for the official Telegram Android client.

This mod introduces three major integrated subsystems:
1. **SafeMode (Stealth Privacy System):** Completely hide selected chats, folders, feeds, proxy settings, contacts, and notifications behind a zero-footprint, search-bar-activated secret mode.
2. **Feeds in Folders (Aggregated Channel Streams):** Seamlessly aggregate multiple channels into clean, unified, and scrollable chronological news feeds attached directly to your chat folders.
3. **Built-in Native WS Proxy (Anti-Censorship Engine):** Embedded high-performance WebSocket/MTProto proxy daemon based on [tg-ws-proxy-android](https://github.com/amurcanov/tg-ws-proxy-android) with Cloudflare CDN balancing, connection watchdog, and stealth failover.

<img width="484" height="1010" alt="SafeMode" src="https://github.com/user-attachments/assets/a59d16c7-9359-41da-ba41-1ec05ba780c2" />

---

## 📑 Table of Contents

- [Features](#-features)
  - [1. SafeMode & Stealth Privacy](#1-safemode--stealth-privacy)
  - [2. Feeds in Folders (Channel Aggregation)](#2-feeds-in-folders-channel-aggregation)
  - [3. Built-in Native WS Proxy (Anti-Censorship Engine)](#3-built-in-native-ws-proxy-anti-censorship-engine)
- [Quick Start Guide](#-quick-start-guide)
  - [Step 1: Set Up Secret Mode](#step-1-set-up-secret-mode)
  - [Step 2: Configure Global Stealth Behavior (Feeds & Proxy)](#step-2-configure-global-stealth-behavior-feeds--proxy)
  - [Step 3: Create and Manage Feeds](#step-3-create-and-manage-feeds)
  - [Step 4: Configure Built-in Proxy](#step-4-configure-built-in-proxy)
  - [Step 5: Hide / Reveal Content](#step-5-hide--reveal-content)
  - [Step 6: Lock & Return to Safe Mode](#step-6-lock--return-to-safe-mode)
- [Commands Reference](#-commands-reference)
- [How to Build from Source](#-how-to-build-from-source)
- [Technical Architecture](#-technical-architecture)
- [Credits & Acknowledgements](#-credits--acknowledgements)
- [Disclaimer & License](#-disclaimer--license)

---

## ✨ Features

### 1. SafeMode & Stealth Privacy

* **Zero-Footprint Activation:** No lock icons, PIN prompts, or suspicious UI artifacts in SafeMode. Activation and unlocking are triggered exclusively via hidden commands entered into the Settings search bar.
* **Cryptographic Security:** Passwords are hashed using salted `PBKDF2-HMAC-SHA1` (65,536 iterations, computed asynchronously) with timing-attack mitigations (`MessageDigest.isEqual`) and an automatic lockout cooldown after 5 failed attempts.
* **Deep System-Wide Sanitization:** When SafeMode is active (locked), all hidden items are completely filtered out from:
  * Chat lists, folder tabs, and search suggestions.
  * Local and global message search results (with pagination auto-recovery).
  * Contacts, birthday reminders, and shortcut widgets.
  * Saved Messages 2.0 (messages forwarded from or referencing hidden channels/chats are stripped).
  * System home screen widgets (Chats, Contacts, and Feed widgets).
  * Android direct share targets and dynamic app shortcuts.
* **Notification Masking & Suppression:** Push notifications, previews, and vibration/sound from hidden chats are blocked while in safe mode. Unread counters for hidden chats are subtracted from app icon badges.
* **OS-Level Screen Protection (`FLAG_SECURE`):** Prevents screenshots and masks app preview snapshots in the Android "Recent Apps" switcher while Secret Mode is unlocked.
* **Configurable Auto-Lock Timers:** Automatically reverts to safe mode after a configurable background timeout or upon switching accounts.

---

### 2. Feeds in Folders (Channel Aggregation)

* **One Feed per Folder:** Pin a dedicated, virtual news feed at the top of any folder (or the main "All Chats" list).
* **Multi-Channel Chronological Stream:** Merges posts from all chosen channels in real time with exact time-based sorting.
* **Composite SQL Pagination:** Uses a composite cursor `(date, uid, mid)` for SQLite queries, preventing missing posts or duplicate items across channels.
* **Native Forward Headers:** Feed items dynamically render author and channel tags using native Telegram headers (`fwd_from`), eliminating redundant UI elements.
* **Window-Based Fast Loading:** Opens directly on the oldest unread post across all sources rather than forcing a full download from the latest post.
* **Bi-directional History & Gap Healing:** Seamlessly scrolls forward and backward through time. Detects and patches message sequence gaps on the fly without network spikes.
* **Two-Way Read Synchronization:** Scrolling through the feed marks messages as read in the underlying source channels, updating unread counters locally and syncing with the server.
* **Full Feed Profile & Shared Media Aggregation:**
  * **Channels Tab (`TAB_FEED_CHANNELS`):** Manage channel sources with a "Select All" toggle directly from the feed profile.
  * **Posts / Stories Tab (`TAB_FEED_POSTS`):** View aggregated channel stories/posts in a multi-column grid with pinch-to-zoom support.
  * **Media, Audio, Files, Links, GIFs & Polls:** Aggregated tabs pull from all feed sources simultaneously, supported by a flood-protected, rate-limited queue with tail buffering.
* **Custom Playlists:** Unified audio and voice message playlist queues that span across all channels in the feed.

---

### 3. Built-in Native WS Proxy (Anti-Censorship Engine)

* **Embedded Native Core:** Integrated Rust-based proxy daemon (`libtgwsproxy.so`) connected via JNA, based on [tg-ws-proxy-android](https://github.com/amurcanov/tg-ws-proxy-android). Runs entirely locally on `127.0.0.1`.
* **Zero External Dependencies:** No third-party VPN apps or external proxy clients required; works out of the box on `arm64-v8a`, `armeabi-v7a`, and `x86_64` architectures.
* **Anti-Censorship & Cloudflare Balancing:**
  * Auto-balancing across 20+ built-in Cloudflare CDN edge endpoints.
  * Supports custom Cloudflare reverse-proxy domains.
  * Obfuscates MTProto traffic into standard TLS/WebSocket connections (`dd...` secret format).
* **Connection Watchdog & Silent Auto-Failover:**
  * Monitors Telegram connection state (`Connecting...` / `Updating...`).
  * If a network stall is detected while general internet is available, the watchdog automatically launches the proxy daemon and initiates a seamless failover.
  * **Upload Stall Recovery:** Detects stalled media upload streams and automatically recycles hung socket connections.
* **Stealth Integration (Ghost Proxy Mode):**
  * Option to hide the proxy top-bar icon and settings in SafeMode (**Settings $\rightarrow$ Chat Settings $\rightarrow$ Hide proxy in safe mode**).
  * In Ghost Mode, the proxy continues running silently in the background to maintain connectivity without revealing its presence in the UI.
* **Full Tuning & Diagnostics:**
  * Customizable bind port (default: `1443`).
  * Configurable connection pool size (`2`, `4`, or `6` WebSocket streams).
  * On-the-fly random MTProto secret key generator.
  * Built-in live log viewer with severity filters (`ALL`, `INFO`, `ERROR`) and one-tap clipboard export.

---

## 🚀 Quick Start Guide

### Step 1: Set Up Secret Mode
1. Open the app and go to **Settings**.
2. Tap the **Search** (🔍) icon in the top right.
3. Type `*/your_password` (e.g., `*/alpha123`) and press **Enter / Search** on your keyboard.
4. You will see a confirmation toast: *Password set! Mode activated*.
5. A **Lock** icon will appear in the main chat screen action bar, indicating that Secret Mode is currently **unlocked**.

---

### Step 2: Configure Global Stealth Behavior (Feeds & Proxy)
1. While in Secret Mode, go to **Settings** $\rightarrow$ **Chat Settings**.
2. Scroll down to find the stealth switches:
   * **Hide feeds in safe mode:** News feeds will be completely hidden from folders when leaving Secret Mode.
   * **Hide proxy in safe mode:** Proxy settings, status icons, and menus will be hidden when leaving Secret Mode (while maintaining background connectivity in Ghost Mode).

---

### Step 3: Create and Manage Feeds
1. Long-press on any **Folder Tab** (or the **All Chats** tab).
2. In the popup menu, select **Show news feed**.
3. A pinned **Feed** item will appear at the top of that folder list.
4. Tap the feed to open the aggregated stream, or open its profile to configure channel sources.
5. Long-press the pinned **Feed** item at any time to:
   * Reconfigure channel sources.
   * Toggle visibility (**Hide news feed**).
   * Toggle Secret-Only visibility (**Show only in secret mode** / **Show always**).

---

### Step 4: Configure Built-in Proxy
1. Go to **Settings** $\rightarrow$ **Data and Storage** $\rightarrow$ **Proxy Settings** (or tap the **Shield / Proxy** icon in the top bar).
2. Enable **Use built-in proxy**.
3. Tap **Built-in proxy settings** to customize:
   * Local port and pool size (2 / 4 / 6).
   * Cloudflare CDN balancing and custom domain.
   * Generate a fresh MTProto secret key (`dd...`).
   * View live connection logs.

---

### Step 5: Hide / Reveal Content
* **Hiding Chats & Channels:** Select one or more chats $\rightarrow$ tap the **Block / Hide (🚫)** icon in the top action bar.
* **Hiding Folders:** Long-press a folder tab $\rightarrow$ tap **Hide folder**.
* **Visual Marker:** When unlocked in Secret Mode, all hidden items (chats, folders, feeds) display a discreet purple indicator dot (`●`).

---

### Step 6: Lock & Return to Safe Mode
To instantly lock the app and hide all protected items:
* Tap the **Lock** icon in the top action bar of the main chat list.
* Or enter `*/<your_password>` into the Settings search bar.
* Or let the **Auto-Lock Timer** lock the app automatically after minimizing.

---

## 🛠 Commands Reference

Type these commands directly into the **Settings Search Bar** and press **Enter / Search** on your keyboard:

| Command | Description | Example |
| :--- | :--- | :--- |
| `*/<password>` | Initial password setup **OR** unlock/lock toggle | `*/mysecret123` |
| `*/<old_pass> <new_pass>` | Change existing password | `*/mysecret123 newpass456` |
| `*/time <minutes>` | Set the background auto-lock timeout | `*/time 1.5` *(90 seconds)* |
| `*/time 0` | Lock immediately when the app is minimized | `*/time 0` |
| `*/time 0.25` | Lock after 15 seconds in the background | `*/time 0.25` *(or `*/time 0,25`)* |
| `*/time -1` | Disable automatic background locking | `*/time -1` |

---

## 🔨 How to Build from Source

This patch is built against the official Telegram for Android open-source repository.

### Prerequisites
* **Android Studio** (Koala / Ladybug or newer).
* **JDK 17** & **Android SDK Platform 35+**.
* **NDK:** Version `27.2.12479018` (configured in `build.gradle`).
* **Telegram API Credentials:**
  1. Log in to [my.telegram.org](https://my.telegram.org).
  2. Navigate to **API development tools**.
  3. Create an application to obtain your `APP_ID` and `APP_HASH`.

---

### Build Steps

#### 1. Clone the Official Telegram Repository (with Submodules)
> ⚠️ **Important:** Telegram uses nested Git submodules for sub-components (such as `jlatexmath`). You **must** clone with `--recursive --shallow-submodules` for the project to compile properly.

```bash
git clone --recursive --shallow-submodules https://github.com/DrKLO/Telegram.git Telegram
cd Telegram
```

#### 2. Apply the SafeMode & WS Proxy Patch
Place your patch file (e.g. `Telegram-SafeMode.patch`) in the root directory of the repository and apply it:

```bash
git apply Telegram-SafeMode.patch
```

*(Alternatively: `patch -p1 < Telegram-SafeMode.patch`)*

#### 3. Native Libraries & Dependencies
The patch includes the pre-compiled `libtgwsproxy.so` binaries in `TMessagesProj/jni/` for the following ABIs:
* `arm64-v8a`
* `armeabi-v7a`
* `x86_64`

JNA runtime bindings (`net.java.dev.jna:jna:5.16.0@aar`) and ProGuard keep-rules are automatically configured in `TMessagesProj/build.gradle` and `proguard-rules.pro`.

#### 4. Configure API Credentials
Open `TMessagesProj/src/main/java/org/telegram/messenger/BuildVars.java` and enter your credentials:

```java
public static int APP_ID = 1234567; // Replace with your APP_ID
public static String APP_HASH = "0123456789abcdef0123456789abcdef"; // Replace with your APP_HASH
```

#### 5. Compile and Run
1. Open the project in **Android Studio**.
2. Allow Gradle sync to complete.
3. Select the desired build variant (e.g. `debug`, `standalone`, or `release`).
4. Click **Build $\rightarrow$ Make Project** or **Run** to install the app on your device or emulator.

---

## 🔬 Technical Architecture

```
                                ┌────────────────────────┐
                                │  SecretModeController  │
                                └───────────┬────────────┘
                                            │
         ┌─────────────────────────┬────────┴────────────────┬─────────────────────────┐
         ▼                         ▼                         ▼                         ▼
┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│   Stealth Mode   │      │  Virtual Feeds   │      │   Media Engine   │      │ Native WS Proxy  │
│  - Hiding Engine │      │  - Virtual Peers │      │  - Rate Limiter  │      │  - WsProxyCtrl   │
│  - Push Firewall │      │  - SQL Composite │      │  - Tail Buffers  │      │  - JNA Bridge    │
│  - FLAG_SECURE   │      │  - Read Tracker  │      │  - Feed Playlist │      │  - Watchdog/CDN  │
└──────────────────┘      └──────────────────┘      └──────────────────┘      └──────────────────┘
```

* **Virtual Peer Isolation:** Feeds use virtual IDs calculated as `VIRTUAL_FEED_ALL_ID - folderId` (`-99990000000000L`). All network calls directed at these virtual IDs are intercepted and blocked at the controller layer, preventing invalid MTProto requests (`400 CHANNEL_INVALID`).
* **Composite SQL Cursors:** Historical feed data is queried from SQLite using `(date, uid, mid)` composite comparisons, preventing gaps when combining channels with overlapping internal message IDs.
* **Tail Buffer Aggregation:** Media loading across multiple channels uses dynamic tail buffers (`feedMediaTails`) synchronized against a moving `safeDate` threshold, preventing dense channels from starving slower channels.
* **Adaptive Rate Limiter:** An internal queue (`feedSendRequest`) restricts parallel server calls (`FEED_MEDIA_PARALLEL = 4`) and enforces burst intervals to avoid triggering Telegram's `FLOOD_WAIT` limits.
* **Native Proxy Bridge:** `WsProxyController` interacts with `libtgwsproxy.so` through JNA interfaces (`StartProxy`, `StopProxy`, `SetCfProxyConfig`, `GetStats`). A background watchdog monitors `ConnectionsManager` states and performs automatic failover and upload socket recovery.

---

## 🤝 Credits & Acknowledgements

* **Base Client:** Official [Telegram for Android](https://github.com/DrKLO/Telegram) by Telegram Messenger LLP.
* **Proxy Core:** [tg-ws-proxy-android](https://github.com/amurcanov/tg-ws-proxy-android) by [@amurcanov](https://github.com/amurcanov) for the native WebSocket/MTProto proxy daemon implementation.

---

## ⚠️ Disclaimer & License

### Disclaimer
This is an independent, open-source modification. It is not affiliated with, maintained, authorized, endorsed, or sponsored by Telegram Messenger Inc. Use at your own discretion.

### License
This modification is distributed under the terms of the **GNU General Public License v2.0 (GPLv2)**, consistent with the upstream licensing of the official [Telegram for Android](https://github.com/DrKLO/Telegram) client.
