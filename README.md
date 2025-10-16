# powershell-automation
PowerShell script to automate workstation inventory and export results to central CSV.

# ⚙️ PowerShell Automation – Computer Inventory Export

**Goal:** Automate the collection of system info (hostname, OS version, boot time) and export to a central CSV for asset tracking.

---

## 🧩 Overview
This simple script demonstrates how automation can replace manual inventory checks and maintain visibility across all devices.

**Core actions:**
- Collects hostname, OS version, and last boot time  
- Appends or updates a central CSV file (mocked path)  
- Logs execution success or errors to a local file  

---

## 💻 Example Script
```powershell
param(
  [Parameter(Mandatory=$true)][string]$CsvPath,
  [Parameter(Mandatory=$true)][string]$LogPath
)

$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
try {
  $info = Get-ComputerInfo | Select-Object CsName, OsName, OsVersion, OsLastBootUpTime
  $row = [pscustomobject]@{
    Hostname   = $info.CsName
    OSName     = $info.OsName
    OSVersion  = $info.OsVersion
    LastBoot   = $info.OsLastBootUpTime
    Timestamp  = $timestamp
    User       = $env:USERNAME
  }

  if (-not (Test-Path $CsvPath)) {
    $row | Export-Csv -Path $CsvPath -NoTypeInformation
  } else {
    $row | Export-Csv -Path $CsvPath -NoTypeInformation -Append
  }

  "$timestamp SUCCESS $($row.Hostname)" | Out-File -FilePath $LogPath -Append -Encoding utf8
}
catch {
  "$timestamp ERROR $_" | Out-File -FilePath $LogPath -Append -Encoding utf8
  throw
}

+```

## 🗓️ Project Timeline & History

- **Prototype scripts (internal use)** — Jan 2025 – Jul 2025  
  Early automation scripts used in-shift to collect inventory and simplify troubleshooting.

- **Refactor & public release** — Oct 2025  
  Rewrote for idempotence, added logging, retry logic and UTF-8 CSV handling. Prepared for scheduled Task Scheduler runs.

- **Next steps** — Ongoing  
  Add SharePoint upload module and central monitoring hooks; schedule regular runs for fleet visibility.

