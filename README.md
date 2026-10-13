# whatsTag
Create Floating Tags over WhatsApp Chat's Link
# WhatsTag (Android)

A native Android application that allows you to manually tag and write notes for any message containing a web link inside WhatsApp chats. The tag is persistently displayed as a sleek floating badge anchored directly at the top of each message bubble.

---

## Features

- **Automated Link Detection**: Monitors WhatsApp chat view hierarchies via an Android `AccessibilityService` to identify messages containing links.
- **Long-Press Trigger**: Press and hold any link or message containing a link inside WhatsApp to immediately open the floating tag editor.
- **Persistent Floating Badges**: Renders custom tags at the top of each message bubble using Android's `WindowManager` overlay system.
- **Multi-Message Support**: If a chat has multiple messages with links (e.g. 3 links in 3 separate messages), each message bubble displays its own tag note simultaneously.
- **Smooth Scroll Tracking**: Floating badges smoothly track message positions as you scroll through the chat.
- **100% Offline & Private**: All tags and links are stored locally on your device in a SQLite database via Room. No internet permission is required.
- **Companion Dashboard**: Easily browse, search, edit, or delete all saved tags from the app's main screen.

---

## How It Works

```
                                 ┌────────────────────────────────────┐
                                 │   WhatsApp (com.whatsapp)          │
                                 │                                    │
 ┌──────────────────────┐        │   ┌────────────────────────────┐   │
 │ Floating Tag Badge   │───────>│   │ 🏷️ Important Client Doc    │   │  <-- Anchored at top of bubble
 └──────────────────────┘        │   ├────────────────────────────┤   │
                                 │   │ Check out the specs here:  │   │
                                 │   │ https://example.com/specs  │   │
                                 │   └────────────────────────────┘   │
                                 └────────────────────────────────────┘
                                                   │
                                      (Long-press link message)
                                                   │
                                                   ▼
                                 ┌────────────────────────────────────┐
                                 │ Floating Tag Input Modal           │
                                 │ ──────────────────────────         │
                                 │ Link: https://example.com/specs    │
                                 │ [ Important Client Doc          ]  │
                                 │               [Cancel] [Save Tag]  │
                                 └────────────────────────────────────┘
```

---

## Requirements

- **Operating System**: Android 8.0 (Oreo / API 26) or higher.
- **WhatsApp**: Official WhatsApp Messenger or WhatsApp Business.
- **Required Android Permissions**:
  1. **Display over other apps** (`SYSTEM_ALERT_WINDOW`): Required to draw floating tag pills and the tag editor over WhatsApp.
  2. **Accessibility Service** (`BIND_ACCESSIBILITY_SERVICE`): Required to detect link URLs and message bubble coordinates inside WhatsApp chats.

---

## How to Build & Install

### Option 1: Open in Android Studio (Recommended)
1. Open **Android Studio** (`/Applications/Android Studio.app`).
2. Click **Open** and select this folder:
   `WhatsTag`
3. Connect your Android phone via USB (with USB Debugging enabled) or start an Android Virtual Device (AVD).
4. Click **Run 'app'** (green play button ▶️) in Android Studio.

### Option 2: Build via Terminal
You can build the debug APK using the Gradle wrapper:
```bash
./gradlew assembleDebug
```
The compiled APK will be generated at:
```
app/build/outputs/apk/debug/app-debug.apk
```
Install it onto your connected Android phone using `adb`:
```bash
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

---

## Initial Setup on Your Phone

1. **Launch WhatsTag**: Open the app from your home screen.
2. **Grant "Display Over Other Apps"**:
   - Tap the **Enable** button next to *Display Over Other Apps*.
   - Toggle the switch to **Allow display over other apps**.
3. **Enable "Accessibility Service"**:
   - Tap the **Enable** button next to *Accessibility Service*.
   - In Android's Accessibility Settings, find **WhatsTag** under *Downloaded apps* (or *Installed services*).
   - Turn the switch **ON** and confirm the standard Android dialog.
4. Return to the app: both indicators will now show **Active**.

---

## How to Use Inside WhatsApp

1. Open **WhatsApp** and go into any chat.
2. Find a message that contains a link (e.g. `https://example.com`).
3. **Press over the Tag that appears close from the Link.**
4. The **Tag WhatsApp Link** dialog will pop up floating over WhatsApp.
5. Type your note (for example: *"Review by Friday"* or *"Client Proposal"*) and tap **Save Tag**.
6. A floating tag pill will immediately appear pinned to the top of that message!
7. As you scroll up or down, the tag stays anchored to that message.
8. To edit or remove a tag, simply long-press the link again (or tap the floating tag chip) and choose **Edit** or **Delete**.

---

## Project Architecture

- **`WhatsAppAccessibilityService.kt`**: Subclasses `AccessibilityService`. Listens for window changes and scroll events from `com.whatsapp`, extracts visible nodes with links (`Patterns.WEB_URL`), determines message bubble bounding boxes (`getBoundsInScreen`), and catches long-click events (`TYPE_VIEW_LONG_CLICKED`).
- **`OverlayWindowManager.kt`**: Coordinates floating views via Android's `WindowManager`. Manages floating badge views positioned directly at `(x, y - badgeHeight)` relative to message coordinates, and presents the centered tag editor dialog.
- **`LinkDetector.kt`**: Regex parser and sanitizer for URLs in chat messages, correctly handling edge cases like trailing punctuation (commas, periods, brackets).
- **`TagEntity.kt` & `TagDao.kt`**: Local Room database entities and DAOs storing tags indexed by URL.
- **`MainActivity.kt`**: Dashboard activity to verify system permissions, search saved tags, and manage notes.

