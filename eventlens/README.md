# EventLens

Record the events of your life with photos and quick notes. EventLens keeps
the photos in a private library on the phone, and Claude (Anthropic's AI)
writes a richly detailed account of each event on your behalf. Claude draws on:

- **Working memory**: what you jot down in the moment (who's there, what's
  happening, how it feels), plus the time and place.
- **Contextual memory**: durable facts you've saved (people, places, ongoing
  situations, preferences) and summaries of your recent events, so each new
  account connects to the ones before it.

After each account, Claude suggests new facts worth remembering. You approve
or dismiss each one, and approved facts go into long-term memory.

This is **stage 1**: an Android app built with Flutter, ready for Google Play.

## Features

- New event: up to 10 photos (gallery or camera), date/time, place, title, notes.
- AI account: a detailed first-person narrative, a short summary, and labels
  for people, places and tags. You can edit it, copy it or rewrite it.
- Memory screen: add, edit and delete what the AI should always know.
- Timeline grouped by month, plus full-text search across everything.
- Settings: your own Anthropic API key (stored encrypted), the model
  (Claude Opus 5.5 by default, or Sonnet 5.5) and the thinking effort.
- Light and dark themes, and recovery when the app is closed mid-description.

## Where the data lives

| Data | Location |
|---|---|
| Original photos | App-private storage on the device (`files/vault/`) |
| Events, memories | SQLite database on the device |
| API key | Android Keystore-backed encrypted storage |

Data leaves the phone only when an event is described. That request sends the
event's photos (downscaled to 1568 px), its notes, summaries of the 15 most
recent events and the saved memories to `api.anthropic.com` over HTTPS. Cloud
backup is disabled for app data. See [PRIVACY_POLICY.md](PRIVACY_POLICY.md).

## Project layout

```
lib/
  ai/            Claude API client (raw HTTP) and the event-description prompt
  data/          SQLite schema, repositories, on-device image vault
  models/        LifeEvent, EventImage, MemoryItem
  services/      Settings (secure API key, preferences)
  state/         AppState: the single ChangeNotifier the UI watches
  screens/       Welcome, Home/timeline, New event, Event detail, Memory, Settings
test/            Unit, integration (real SQLite via FFI) and widget tests
tool/            Icon and store-graphic generator
store/           Play Store listing text and graphics
.github/workflows/android.yml   CI: analyze + test; signed AAB/APK on tags
```

## Development

Requires Flutter 3.47+ (stable) and Android Studio or the Android SDK.

```bash
flutter pub get
flutter analyze
flutter test
flutter run            # on a connected device or emulator
```

On first launch, open Settings and paste an Anthropic API key from
<https://console.anthropic.com/settings/keys>. Usage is billed to that
Anthropic account. A typical event costs roughly 5–30 US cents on
Opus 5.5 at high effort, depending on photo count and length; about half that
on Sonnet 5.5.

To regenerate the icon or store graphics after changing `tool/generate_icon.dart`:

```bash
dart run tool/generate_icon.dart && dart run flutter_launcher_icons
```

## Releasing

See [RELEASE.md](RELEASE.md) for the step-by-step Google Play release.

## Roadmap ideas (stage 2+)

- Optional sync to a self-hosted server (e.g. a Docker container on a home
  NAS), with the AI call moved server-side so the key never sits on the phone.
- Export/import of the whole library (zip of photos + JSON).
- Ask questions across all events ("when did I last see Sam?").
- Read EXIF time and location from photos automatically.
