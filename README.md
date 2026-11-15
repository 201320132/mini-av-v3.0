# Mini AV 3.0 / مینی‌AV نسخه ۳

Small, free antivirus for Windows 10/11 written in Rust. Dark glass interface (WebView2), Persian and English (RTL/LTR), SVG icons only, no emojis.

## Build (Windows)
1. Install Rust from https://rustup.rs
2. Double-click `build.bat` -> `mini-av.exe` appears in this folder.
   (Or push the folder to GitHub and download the exe from the Actions tab; see `.github/workflows/build.yml`.)

Needs the Microsoft Edge WebView2 Runtime (already present on Windows 11 and updated Windows 10).
If the window cannot start, the program tells you where to download the runtime.

## What is new in 3.0
- **heuristics.txt**: all detection rules live in a ready-made, editable file (built into the exe). Put your own copy next to `mini-av.exe` or in `%APPDATA%\MiniAV` to extend (`[section+]`) or replace (`[section]`) a list. A broken file is ignored, and rules can never un-protect the Windows folder.
- **Windows signature check**: flagged files are checked by Windows itself (Authenticode). Validly signed files from trusted publishers are never reported; validly signed files in the "suspicious" class are skipped too.
- **Defender second opinion**: suspicious items are checked by Microsoft Defender (no remediation). If Defender also detects them they become confirmed threats.
- **New checks (report-only, never changed by Mini AV)**: suspicious scheduled tasks, tampered Winlogon Shell/Userinit, Image File Execution Options debugger hijacks, over-wide Microsoft Defender exclusions.
- **Safer clean-up**: Program Files is never modified by heuristic detections; a Windows restore point is attempted before "Kill all viruses" (needs administrator); every action is logged in `%APPDATA%\MiniAV\actions.log`.

## Features
- Home: protection status, Defender real-time state, definitions age, firewall, quarantine count, last scan.
- Scans: Quick, Full, Custom (any folders/drives), optionally together with Microsoft Defender.
- Own detections: fake shortcut (shortcut virus) and recovery of the files it hid, autorun.inf worms, programs disguised as documents/pictures, double extensions (`a.pdf.exe`), USB droppers, RECYCLER trick, suspicious startup entries (Run keys, Startup folders), suspicious scripts, tampered hosts file, EICAR test file, custom SHA-256 signatures (`signatures.txt`).
- Uses Microsoft Defender for what it does not do itself: engine scan, definitions update, real-time protection switch, threat removal, second opinion.
- Quarantine: reversible (Restore), obfuscated copies that cannot run, Delete permanently, and **Kill all viruses**.

## Safety design
- Only files reported as detections are ever touched.
- The Windows folder (except Temp) is never scanned or changed. Junctions/symlinks are never followed.
- Red ("Threat") items are quarantined automatically (optional). Yellow ("Suspicious") items and system settings are never changed automatically and are not touched by Kill all viruses.
- Removal goes to quarantine first (unless you press Kill all viruses).

## Test it safely
Create a text file containing the standard EICAR test string (see eicar.org). Mini AV flags it as `EICAR-Test-File`; it is harmless.

## Developer notes
`src/engine.rs` is pure `std` and has 16 unit tests: `rustc --edition 2021 --test src/engine.rs -o t && ./t` (works on any OS). The UI is `ui/index.html`.

## Limits
A lightweight heuristic scanner plus Defender integration. It does not replace a commercial product's cloud intelligence or behavioural engine. The Windows-specific parts (UAC, Defender, signatures, WebView2) were verified by review and unit tests with mocks, not on a live Windows machine.
