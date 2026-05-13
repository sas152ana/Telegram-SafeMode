# Telegram SafeMode (Privacy Mod)

A privacy-focused modification for the official Telegram Android client. This patch introduces a "Secret Mode" that allows you to hide specific chats, folders, and contacts behind a stealthy, invisible password system seamlessly integrated into the Telegram search bar.

![Demo Video/GIF](link_to_your_video_or_gif_here.gif) 
*(Replace the link above with a short video or GIF showing how you type `*/1234` in search and chats disappear/appear)*

## ✨ Features

*   **Invisible Activation:** No obvious "lock" buttons. Control the mode by typing commands directly into the main search bar (e.g., `*/password`).
*   **Deep Integration:** When Secret Mode is active, hidden chats and their content are completely removed from:
    *   Main chat list and Folders
    *   Contacts list and all Search results (local and global)
    *   Saved Messages (ANY message forwarded from a hidden chat is completely hidden)
    *   Forwarding targets and share menus
    *   Android home screen widgets (Chats, Contacts, Feed)
*   **Notification Security:** Push notifications for hidden chats are intercepted, masked, and will not show on your screen or smartwatch until the mode is deactivated.
*   **Auto-Lock Timer:** Set a custom timeout for the app to automatically lock and hide chats when pushed to the background.
*   **App Switcher Protection:** The app screen is blacked out in the Android "Recent Apps" menu when Secret Mode is active.
*   **Anti-Bruteforce System:** Built-in protection against password guessing (temporary lock after 5 failed attempts) and timing attacks.

## 🛠 Commands (Used in the main Search Bar)

*   `*/<password>` — Set a new password (first time) or unlock/lock the app.
*   `*/<old_password> <new_password>` — Change your password.
*   `*/time <minutes>` — Set the auto-lock timer. Supports both a period (`.`) and a comma (`,`) as decimal separators. 
    *   *Examples:* `*/time 1.5` (90 seconds), `*/time 0,25` (15 seconds), or `*/time 0` (locks immediately upon minimizing the app).

---

## 🚀 How to Build

Since this is a patch for the official Telegram source code, you need to apply it to the original repository.

### Prerequisites

1.  **Android Studio** (latest version recommended).
2.  **Telegram API Credentials:** You need your own `APP_ID` and `APP_HASH`.
    *   Go to [my.telegram.org](https://my.telegram.org/).
    *   Log in and navigate to **API development tools**.
    *   Create a new application to get your `APP_ID` and `APP_HASH`.

### Build Steps

**Step 1: Clone the official Telegram repository**
```bash
git clone https://github.com/DrKLO/Telegram.git
cd Telegram
