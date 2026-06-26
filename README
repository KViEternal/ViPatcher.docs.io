# 📖 ViPatcher — Complete Usage Guide

ViPatcher is a full game patching ecosystem: it builds delta patches, creates a self-updating launcher, and handles secure file verification — all from inside the Unity Editor.

## Table of Contents
* [1. Installation](#1-installation)
* [2. First-Time Setup](#2-first-time-setup)
* [3. Building Your Game](#3-building-your-game)
* [4. Generating Patches](#4-generating-patches)
* [5. Building the Launcher](#5-building-the-launcher)
* [6. Packing the Launcher](#6-packing-the-launcher)
* [7. Publishing to Your Server](#7-publishing-to-your-server)
* [8. Server Folder Structure](#8-server-folder-structure)
* [9. How Updates Work for Players](#9-how-updates-work-for-players)
* [10. Cross-Platform Support](#10-cross-platform-support)
* [11. Troubleshooting](#11-troubleshooting)
* [12. EditorPrefs Reference](#12-editorprefs-reference)

---

## 1. Installation
1. Copy the `ViPatcher.Unity` folder into your Unity project's `Packages/` directory (or use Unity Package Manager → "Add package from disk" and point to `package.json`).
2. Requires **Unity 2021.3+** and **.NET 10 SDK** installed on your system.
3. Open the tool: `Window` → `ViPatcher Ecosystem`.

---

## 2. First-Time Setup
When you first open ViPatcher, it shows the **Tutorial** tab with a health banner:

| Banner Color | Meaning |
| :--- | :--- |
| 🔴 Red | Cryptographic keys are missing — you cannot sign patches |
| 🟡 Yellow | Server URL is missing or invalid |
| 🟢 Green | Everything is configured and ready |

**Step-by-Step:**
1. Go to the **Settings** tab (⚙️ Settings in the sidebar).
2. **Set Game Server URL** — The base URL where your game files are hosted. *(Example: `https://www.example.com/{some-project-name}`)*. This must point to a web server that serves static files.
3. **Set Game Executable Name** — The name of your game's executable.
   * Windows: `MyGame.exe`
   * macOS: `MyGame.app`
   * Linux: `MyGame` (no extension)
4. **Generate Cryptographic Keys** — Click "Generate New Keys". This creates an ECDSA key pair used to sign your patches. 
   * *Private Key:* Used to sign — keep it secret!
   * *Public Key:* Embedded into the launcher so players can verify patches.
5. Click **"Save Settings"** to persist everything.
6. **Test Server Connection** (Tutorial tab) — Verifies that your server URL is reachable.

> **[!IMPORTANT]** > Your Private Key is stored in Unity's `EditorPrefs`. If you lose it, you'll need to generate new keys and rebuild your launcher. Back up your Private Key somewhere safe.

---

## 3. Building Your Game
1. Go to the 📦 **Build & Patch** tab.
2. **Select Target Platform** — Choose from the Platform dropdown (e.g., `StandaloneWindows64`, `StandaloneOSX`, `StandaloneLinux64`).
3. **Set Game Version** — Enter the version number for this build (e.g., `1.0.0`). This is automatically remembered between sessions.
4. Click **"1. Build Unity Game"**.

This compiles your Unity project and places the output in:
`{YourProject}/ViPatcher_Builds/{Platform}/NewBuild/`

> **[!TIP]** > Make sure your scenes are added to `Build Settings` → `Scenes In Build` before building.

---

## 4. Generating Patches
Still on the 📦 **Build & Patch** tab, after building your game:
1. **Ensure version is correct** — The version field should match the version you want to release.
2. Click **"2. Generate Patch (CLI)"**.

**What happens behind the scenes:**
All files land in: `{YourProject}/ViPatcher_Builds/{Platform}/Release/`

| File Generated | Purpose |
| :--- | :--- |
| `manifest.json` | Version info, file hashes, sizes, patch chain, digital signature |
| `latest.zip` | Full game archive (for fresh installs and file repair) |
| `patch_X_to_Y.vipatch` | Delta patch (only the changed bytes between versions) |

After packing, the system automatically copies `NewBuild/` → `OldBuild/` (so the next patch can diff against this version) and auto-increments the version.

**Patch Chains**
Starting from the second patch, ViPatcher maintains a Patch Chain inside `manifest.json`. This allows the launcher to apply multiple patches sequentially if a player skips several updates.

> **[!WARNING]** > Keep old `.vipatch` files on your server! If you delete them, players who skipped updates will fall back to downloading the full `latest.zip`.

---

## 5. Building the Launcher
1. Go to the 🚀 **Launcher** tab.
2. **Set Launcher Version** — e.g., `1.0.0`. Auto-remembered between sessions.
3. **Select Target OS** from the dropdown (`win-x64`, `osx-x64`, `osx-arm64`, `linux-x64`).
4. Click **"Build Launcher (.exe)"**.

ViPatcher injects your settings (Public Key, Server URL, Game Executable Name, Launcher Version) into the source code and compiles it as a self-contained executable.

> **[!IMPORTANT]** > You must build the launcher separately for each platform you want to support. Each build has the platform's server URL baked in.

---

## 6. Packing the Launcher
Still on the 🚀 **Launcher** tab, after building:
1. Click **"Pack Launcher Update"**.

This creates `launcher.zip` and updates `manifest.json` with the new launcher version and download URL.

> **[!TIP]** > If the server has a newer `LauncherVersion` in the manifest, the launcher downloads `launcher.zip` and replaces itself before running the game.

---

## 7. Publishing to Your Server
Go to the 🌐 **Publish** tab. 

**Automated Upload:**
Enter Username and Password for your upload endpoint. Click **"Publish to Server"**. The CLI will POST all files in the Release directory to `{ServerURL}/publish`.

**Manual Upload:**
Copy the contents of your local `Release` folder directly to your web server:
```text
{YourProject}/ViPatcher_Builds/{Platform}/Release/
    ├── manifest.json        → upload to server
    ├── latest.zip           → upload to server
    ├── launcher.zip         → upload to server
    └── patch_X_to_Y.vipatch → upload to server
