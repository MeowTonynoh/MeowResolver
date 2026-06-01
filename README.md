# Meow Resolver

A Windows forensic tool that detects and removes common bypass techniques, restrictions, and anti-forensic manipulations from the system registry, event logs, and file system.

## ⚠️ Important Notice

This tool **requires administrative privileges** to function correctly. Without them, most checks will fail silently or return incomplete results.

---

## Usage

Download and run the `.exe` file. The program automatically scans the system and presents findings with three options:

**[1] Remove ALL detected Bypasses/Restrictions** - applies all available fixes automatically

**[2] Review and choose per finding** - goes through each finding one by one

**[3] Exit**

---

## What It Detects

### 1. Internet Options — Restricted Zones
Detects domains forcefully assigned to the Restricted Zone (Zone 4) via registry, and IE/WinINET policy locks.

### 2. Firewall.cpl Restrictions
Detects Group Policy values that disable the Windows Firewall UI, or explicit blocks on `firewall.cpl` via `DisallowCpl`.

### 3. Hosts File Manipulation
Scans `C:\Windows\System32\drivers\etc\hosts` for suspicious entries redirecting known domains (GitHub, Discord, Telegram, VirusTotal, etc.) to `0.0.0.0` or `127.0.0.1`.

### 4. Taskkill Autorun
Detects `taskkill`/`tskill` commands in Run/RunOnce registry keys, and `AutoRun` values in the Command Processor key.

### 5. DisallowRun
Detects executables blocked via Explorer policy and the `DisallowRun` DWORD being active.

### 6. IFEO — Image File Execution Options
Detects debugger hijacking on executables and suspicious `GlobalFlag` values that can silently terminate or redirect processes. Also detects renamed copies of the `Image File Execution Options` key (used to keep debugger entries active while hiding them from tools that only check the standard path), and alerts if read permissions on the `Windows NT` registry key have been removed to prevent enumeration entirely.

### 7. WinRAR Steganography
Scans WinRAR's `ArcHistory` registry key for non-archive files opened with WinRAR — a common steganography indicator.

### 8. NTFS Journal Deletion
Checks Security and NTFS Operational event logs for journal deletion events (IDs 3079, 501) and verifies USN Journal status via `fsutil`. Boot-time events from `SearchIndexer` and similar system services are automatically skipped.

### 9. FileInfo Filter
Detects unload events of the FileInfo kernel filter (System Event ID 1) and checks if the FileInfo service is stopped.

### 10. Prefetch — Read-Only .pf Files
Scans `C:\Windows\Prefetch` for `.pf` files marked as read-only, which prevents Windows from updating execution traces.

### 11. Prefetch Parameters
Checks `EnablePrefetcher` registry value — if set to 0 or 1, prefetch files are not generated for all applications.

### 12. SysMain — sechost.dll Suspended Threads
Detects suspended threads belonging to `sechost.dll` inside the SysMain process, a known technique to disable prefetch recording.

### 13. URL Blocklist
Detects `URLBlockList` and wildcard allowlist-only mode in Chrome, Edge, Brave, and Vivaldi Group Policy registry keys.

### 14. CMD Color Bypass
Detects CMD color configurations where text color matches background color, making terminal output invisible.

### 15. Disallowed Certificates
Scans the `Disallowed` certificate store for known malicious thumbprints (e.g. GitHub certificate blocks via `certutil -addstore`).

### 16. Group Policy Restrictions
Detects GPO values that block system tools:
- `DisableCMD` — CMD blocked
- `DisableRegistryTools` — regedit blocked
- `DisableTaskMgr` — Task Manager blocked
- `NoControlPanel` / `NoDispCPL` — Control Panel blocked
- `NoRun` — Win+R dialog disabled
- `NoFind` — Windows Search disabled
- `NoFolderOptions` — Folder Options hidden
- PowerShell `ExecutionPolicy` forced to `Restricted`/`AllSigned`
- `EnableScripts = 0` — PowerShell scripting fully disabled

### 17. System Time Changes
Monitors Security Event ID 4616 for manual system clock changes. Automatically skips NTP-level adjustments (≤ 30 seconds) and boot-time sync events.

### 18. Event Log Cleared
Detects Security Event ID 1102 (Security log cleared) and System Event ID 104 (any log cleared) during the current session.

### 19. Smart App Control / App Install Source Restriction
Detects when Windows is configured to only allow apps recognized by Microsoft, which can block forensic tools like PrefetchView, BAM-Parser, and others from running. Checks Smart App Control state (`VerifiedAndReputablePolicyState`), the `AicEnabled` value (Win10/11 app source setting), and equivalent Group Policy entries.

### 20. USB / Disk Devices Disabled
Scans `SYSTEM\CurrentControlSet\Enum\USBSTOR`, `USB`, and `DISK` for devices with `ConfigFlags` bit 0 set — meaning they were manually disabled. A disabled USB storage device does not appear in Process Hacker and leaves no extraction timestamp, hiding device activity during a review.

### 21. SettingsPageVisibility (SPV)
Detects the `SettingsPageVisibility` registry value under Explorer policy keys, which can hide or restrict access to specific Windows Settings pages. This is often combined with other bypass techniques to prevent their detection or removal via the Settings UI.

### 22. NotFileMru — OpenSavePidlMRU Disabled
Detects `NotFileMru = 1` under `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32`, which disables the Open/Save file dialog history. When active, files opened or saved through any application dialog are not recorded, removing a key forensic artifact used in screenshare reviews.

---

## System Requirements

- Windows only
- .NET 6.0 or later
- Administrative privileges strongly recommended

---

## Security and Privacy

- The tool does **not** send any data online — all analysis is local
- The tool does **not** modify memory — registry and file fixes only, on user confirmation
- Usernames shown in findings are **partially blurred** for privacy

---

## Contacts

**Creator**: Tonynoh

💬 Discord: `tonyboy90_`

📎 GitHub: [MeowTonynoh](https://github.com/MeowTonynoh)

🎥 YouTube: `tonynoh-07`
