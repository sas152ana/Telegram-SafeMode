# Telegram SafeMode & Channel Feeds (Android Client Mod)

[![License: GPL-2.0](https://img.shields.io/badge/License-GPL%20v2-blue.svg)](https://www.gnu.org/licenses/gpl-2.0)
[![Platform](https://img.shields.io/badge/Platform-Android-green.svg)](https://developer.android.com)
[![Base: Official Telegram](https://img.shields.io/badge/Base-DrKLO%2FTelegram-blue.svg)](https://github.com/DrKLO/Telegram)

A privacy-focused and feature-enhanced modification for the official Telegram Android client. 

This mod introduces two major systems:
1. **SafeMode (Stealth Privacy System):** Completely hide selected chats, folders, contacts, and notifications behind a zero-footprint, search-bar-activated secret mode.
2. **Feeds in Folders (Aggregated Channel Streams):** Seamlessly aggregate multiple channels into clean, unified, and scrollable chronological news feeds attached directly to your chat folders.
<img width="484" height="1010" alt="SafeMode" src="https://github.com/user-attachments/assets/a59d16c7-9359-41da-ba41-1ec05ba780c2" />

---

## 📑 Table of Contents

- [Features](#-features)
  - [1. SafeMode & Stealth Privacy](#1-safemode--stealth-privacy)
  - [2. Feeds in Folders (Channel Aggregation)](#2-feeds-in-folders-channel-aggregation)
- [Quick Start Guide](#-quick-start-guide)
  - [Step 1: Set Up Secret Mode](#step-1-set-up-secret-mode)
  - [Step 2: Configure Global Feed Behavior](#step-2-configure-global-feed-behavior)
  - [Step 3: Create and Manage Feeds](#step-3-create-and-manage-feeds)
  - [Step 4: Hide / Reveal Content](#step-4-hide--reveal-content)
  - [Step 5: Lock & Return to Safe Mode](#step-5-lock--return-to-safe-mode)
- [Commands Reference](#-commands-reference)
- [How to Build from Source](#-how-to-build-from-source)
- [Technical Architecture](#-technical-architecture)
- [Disclaimer & License](#-disclaimer--license)

---

## ✨ Features

### 1. SafeMode & Stealth Privacy

* **Zero-Footprint Activation:** No lock icons or PIN dialogs in safe mode. Activation is triggered exclusively via hidden commands entered into the Settings search bar.
* **Cryptographic Security:** Passwords are hashed using salted `PBKDF2-HMAC-SHA1` (65,536 iterations, computed asynchronously) with timing-attack mitigations (`MessageDigest.isEqual`) and an automatic lockout timer after failed attempts.
* **Deep System-Wide Sanitization:** When SafeMode is active (locked), all hidden items are completely filtered out from:
  * Chat lists, folder tabs, and search suggestions.
  * Local and global message search results (with pagination auto-recovery).
  * Contacts and birthday reminders.
  * Saved Messages 2.0 (messages forwarded from or referencing hidden channels/chats are stripped).
  * System home screen widgets (Chats, Contacts, and Feed widgets).
  * Android direct share targets and app shortcuts.
* **Notification Masking & Suppression:** Push notifications and message previews from hidden chats are blocked while in safe mode. Unread counters for hidden chats are subtracted from app icon badges. Group summaries are cleanly dismissed to prevent launcher indicator leaks.
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

## 🚀 Quick Start Guide

### Step 1: Set Up Secret Mode
1. Open the app and go to **Settings**.
2. Tap the **Search** (🔍) icon in the top right.
3. Type `*/your_password` (e.g., `*/alpha123`) and press **Enter** / Search on your keyboard.
4. You will see a confirmation toast: *Password set! Mode activated*.
5. A **Lock** icon will appear on the main chat screen's action bar, indicating that Secret Mode is currently **unlocked**.

---

### Step 2: Configure Global Feed Behavior
1. While in Secret Mode, go to **Settings** $\rightarrow$ **Chat Settings**.
2. Scroll down to find the **Hide feeds in safe mode** toggle:
   * **Enabled (Default):** All feeds are completely invisible in SafeMode and only appear when Secret Mode is unlocked.
   * **Disabled:** Feeds remain accessible in regular (safe) mode as standard reading tools.

---

### Step 3: Create and Manage Feeds
1. Go to the main chat screen.
2. Long-press on any **Folder Tab** (or the **All Chats** tab).
3. In the popup menu, select **Show news feed**.
4. A pinned **Feed** item will appear at the top of that folder list.
5. Tap on the feed to view aggregated posts, or tap the feed header / open its profile to select which channels from that folder should feed into the stream.
6. Long-press the pinned **Feed** item in the chat list at any time to:
   * Reconfigure channel sources.
   * Toggle visibility (**Hide news feed**).
   * Toggle Secret-Only visibility (**Show only in secret mode** / **Show always**).

---

### Step 4: Hide / Reveal Content
* **Hiding Chats & Channels:** Select one or more chats $\rightarrow$ tap the **Block / Hide (🚫)** icon in the top action bar.
* **Hiding Folders:** Long-press a folder tab $\rightarrow$ tap **Hide folder**.
* **Visual Marker:** When unlocked in Secret Mode, all hidden items (chats, folders, feeds) display a discreet purple indicator dot (`●`).

---

### Step 5: Lock & Return to Safe Mode
To instantly lock the app and hide all protected chats, folders, and feeds:
* Tap the **Lock** icon in the top action bar of the main chat list.
* Or let the **Auto-Lock Timer** lock the app automatically after it is minimized.

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
* **Android Studio** (Koala / Ladybug or newer recommended).
* **JDK 17** & **Android SDK Platform 34+**.
* **Telegram API Credentials:**
  1. Log in to [my.telegram.org](https://my.telegram.org).
  2. Navigate to **API development tools**.
  3. Create an application to obtain your `APP_ID` and `APP_HASH`.

---

### Build Steps

#### 1. Clone the Official Telegram Repository
```bash
git clone https://github.com/DrKLO/Telegram.git
cd Telegram
```

#### 2. Apply the SafeMode Patch
Copy the `Telegram-SafeMode.patch22.txt` patch file to the root of the cloned repository and apply it:

```bash
git apply Telegram-SafeMode.patch22.txt
```

*(Alternatively, using patch tools: `patch -p1 < Telegram-SafeMode.patch22.txt`)*

#### 3. Configure API Credentials
Open `TMessagesProj/src/main/java/org/telegram/messenger/BuildVars.java` and enter your API keys:

```java
public static int APP_ID = 1234567; // Replace with your APP_ID
public static String APP_HASH = "0123456789abcdef0123456789abcdef"; // Replace with your APP_HASH
```

#### 4. Compile and Run
1. Open the project folder in **Android Studio**.
2. Allow Gradle to download dependencies and sync completely.
3. Select your build variant (e.g. `AFATDebug` or `release`).
4. Click **Build $\rightarrow$ Make Project** or **Run** to install the application on your device/emulator.

---

## 🔬 Technical Architecture

```
                       ┌────────────────────────┐
                       │  SecretModeController  │
                       └───────────┬────────────┘
                                   │
         ┌─────────────────────────┼─────────────────────────┐
         ▼                         ▼                         ▼
┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│   Stealth Mode   │      │  Virtual Feeds   │      │   Media Engine   │
│  - Hiding Engine │      │  - Virtual Peers │      │  - Rate Limiter  │
│  - Push Firewall │      │  - SQL Composite │      │  - Tail Buffers  │
│  - FLAG_SECURE   │      │  - Read Tracker  │      │  - Feed Playlist │
└──────────────────┘      └──────────────────┘      └──────────────────┘
```

* **Virtual Peer Isolation:** Feeds use virtual IDs calculated as `VIRTUAL_FEED_ALL_ID - folderId` (`-99990000000000L`). All network calls directed at these virtual IDs are intercepted and blocked at the controller layer, preventing invalid MTProto requests (`400 CHANNEL_INVALID`).
* **Composite SQL Cursors:** Historical feed data is queried from SQLite using `(date, uid, mid)` composite comparisons, preventing gaps when combining channels with overlapping internal message IDs.
* **Tail Buffer Aggregation:** Media loading across multiple channels uses dynamic tail buffers (`feedMediaTails`) synchronized against a moving `safeDate` threshold, preventing dense channels from starving slower channels.
* **Adaptive Rate Limiter:** An internal queue (`feedSendRequest`) restricts parallel server calls (`FEED_MEDIA_PARALLEL = 4`) and enforces burst intervals to avoid triggering Telegram's `FLOOD_WAIT` limits.

---

## ⚠️ Disclaimer & License

### Disclaimer
This is an independent, open-source modification. It is not affiliated with, maintained, authorized, endorsed, or sponsored by Telegram Messenger Inc. Use at your own discretion.

### License
This modification is distributed under the terms of the **GNU General Public License v2.0 (GPLv2)**, consistent with the upstream licensing of the official [Telegram for Android](https://github.com/DrKLO/Telegram) client.
