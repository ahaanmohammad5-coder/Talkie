# Talkie

**Talkie** is a WhatsApp-style chat app for Android, written in native
Kotlin with classic Views/XML. It uses a dark green + beige theme, a minimal
speech-bubble app icon, and an animated splash screen.

The app works in two modes:

- **Demo mode (default):** no setup needed. Sample chats, contacts,
  statuses and call history are built in, with simulated presence, typing
  indicators, replies and delivery ticks. Everything below works offline.
- **Live mode:** add a `google-services.json` (see steps at the bottom) and
  Firebase is initialized automatically; messages are mirrored to Firestore
  and FCM push is wired.

## Features

- Animated splash (icon pops in, then the word Talkie)
- CHATS / STATUS / CALLS tabs, WhatsApp-style layout
- Chat list with avatars, last message, ticks and unread badges
- 1-to-1 and group chats (create groups from your contacts)
- Text messages with the full tick state machine:
  - no internet -> small **clock** on the bubble, message waits in a queue
    and **auto-sends when the network comes back**
  - sent -> single tick, delivered -> double tick, seen -> **blue** double tick
- Presence: **online** written under the name in the chat header,
  otherwise last seen; typing indicator before a reply arrives
- Images and videos from the gallery, camera photos
- **Instant video messages** (Instagram-style circular video notes)
- Voice notes with a recording bar and timer
- **Stickers:** built-in sticker panel
- **Sticker maker:** pick any photo, it is cropped into a sticker and saved
  under My stickers, ready to send
- **Location sharing:** sends a location card that opens in Maps
- Document sharing from the attach menu
- Status / stories with a full-screen viewer and progress bar
- Voice and video call screens with mute, speaker and video toggles,
  plus a call log (incoming / outgoing / missed)
- New chat screen syncs the **real contacts from the phone**
  (with permission) and merges them with the app contacts
- Profile and Settings screens

## Build the APK (GitHub Actions)

Local Android builds are not required. The project builds in the cloud:

1. Create a GitHub repo named **Talkie-App**.
2. Push all files from this folder into the repo.
3. Open the repo's **Actions** tab, wait for the "Build Talkie APK" run
   to finish.
4. Download the **talkie-apk** artifact from the run page, unzip it, and
   install `app-debug.apk` on the phone.

## Make messaging live between phones (Firebase)

1. Go to the Firebase console and create a project (or open an existing one).
2. Add an Android app with package name **com.ahaan.talkie**.
3. Download **google-services.json** and place it in the **app/** folder
   of this project (next to `app/build.gradle`).
4. In the Firebase console, enable **Firestore Database** and
   **Authentication**.
5. Push the project to GitHub again and download the new **talkie-apk**.

Notes:

- The Gradle build applies the google-services plugin only when the file
  exists, so demo builds never break.
- In code, `FirestoreSync` mirrors messages to the Firestore `messages`
  collection and marks where live snapshot merging plugs in; presence and
  read states use the same `MessageStatus` values as demo mode.
- Voice/video calls are currently simulated in the call screen. For real
  calls, plug a WebRTC stack (or a service such as Agora/Twilio) into
  `CallActivity` and signal through Firestore.
- Review Firestore security rules before real users: the demo setup is
  meant for development.
