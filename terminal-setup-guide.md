# Windows Terminal + Oh My Posh Setup Guide

Complete setup for a modern Windows terminal with Catppuccin Mocha theme, Oh My Posh powerline prompt, icons, and autocomplete.

---

## Prerequisites

- Windows 10/11
- [Windows Terminal](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701) installed from Microsoft Store
- [Git](https://git-scm.com/download/win) installed (for Git Bash and git features)

---

## Step 1: Enable PowerShell Script Execution

Open PowerShell as Administrator and run:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
```

---

## Step 2: Install Oh My Posh

Download the standalone executable (not the winget MSIX version, which has sandbox issues):

```powershell
Invoke-WebRequest -Uri 'https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-windows-amd64.exe' -OutFile "$env:LOCALAPPDATA\Programs\oh-my-posh.exe"
```

---

## Step 3: Install CaskaydiaCove Nerd Font

```powershell
& "$env:LOCALAPPDATA\Programs\oh-my-posh.exe" font install CascadiaCode
```

This installs CaskaydiaCove NF / NFM / NFP font families.

---

## Step 4: Install NuGet Provider and PowerShell Modules

```powershell
# NuGet provider (required for module installs)
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Scope CurrentUser -Force

# Terminal Icons - file/folder icons in ls/dir
Install-Module -Name Terminal-Icons -Repository PSGallery -Scope CurrentUser -Force

# Updated PowerShellGet (required for PSReadLine prerelease)
Install-Module -Name PowerShellGet -Scope CurrentUser -Force -AllowClobber

# PSReadLine with history autocomplete (restart PowerShell after PowerShellGet install)
Import-Module PowerShellGet -Force
Install-Module -Name PSReadLine -AllowPrerelease -Scope CurrentUser -Force -SkipPublisherCheck
```

---

## Step 5: Install Clink (for Command Prompt support)

```powershell
winget install chrisant996.Clink --accept-package-agreements --accept-source-agreements
```

---

## Step 6: Download the Oh My Posh Theme

Create the themes directory and download the night-owl custom theme:

```powershell
New-Item -ItemType Directory -Path "$env:LOCALAPPDATA\Programs\oh-my-posh-themes" -Force
```

Save the following JSON as `%LOCALAPPDATA%\Programs\oh-my-posh-themes\night-owl.omp.json`:

```json
{
  "$schema": "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/schema.json",
  "blocks": [
    {
      "alignment": "left",
      "segments": [
        {
          "background": "#cba6f7",
          "foreground": "#1e1e2e",
          "leading_diamond": "\ue0b6",
          "style": "diamond",
          "template": " {{ if .WSL }}WSL at {{ end }}{{.Icon}} ",
          "trailing_diamond": "\ue0b0",
          "type": "os",
          "options": {
            "windows": "\ue62a"
          }
        },
        {
          "background": "#f38ba8",
          "foreground": "#1e1e2e",
          "powerline_symbol": "\ue0b0",
          "style": "powerline",
          "template": " \uf292 ",
          "type": "root"
        },
        {
          "background": "#89b4fa",
          "foreground": "#1e1e2e",
          "powerline_symbol": "\ue0b0",
          "options": {
            "style": "full"
          },
          "style": "powerline",
          "template": " {{ .Path }} ",
          "type": "path"
        },
        {
          "background": "#a6e3a1",
          "background_templates": [
            "{{ if or (.Working.Changed) (.Staging.Changed) }}#f9e2af{{ end }}",
            "{{ if and (gt .Ahead 0) (gt .Behind 0) }}#fab387{{ end }}",
            "{{ if gt .Ahead 0 }}#94e2d5{{ end }}",
            "{{ if gt .Behind 0 }}#f5c2e7{{ end }}"
          ],
          "foreground": "#1e1e2e",
          "powerline_symbol": "\ue0b0",
          "style": "powerline",
          "options": {
            "branch_icon": "\ue725 ",
            "fetch_stash_count": true,
            "fetch_status": true,
            "fetch_upstream_icon": true
          },
          "template": " {{ .UpstreamIcon }}{{ .HEAD }}{{ .BranchStatus }}{{ if .Working.Changed }} \uf044 {{ .Working.String }}{{ end }}{{ if and (.Working.Changed) (.Staging.Changed) }} |{{ end }}{{ if .Staging.Changed }} \uf046 {{ .Staging.String }}{{ end }}{{ if gt .StashCount 0 }} \ueb4b {{ .StashCount }}{{ end }} ",
          "type": "git"
        }
      ],
      "type": "prompt"
    },
    {
      "alignment": "right",
      "segments": [
        {
          "background": "#313244",
          "foreground": "#a6e3a1",
          "leading_diamond": "\ue0b2",
          "style": "diamond",
          "options": {
            "fetch_package_manager": true,
            "npm_icon": "<#f38ba8>\ue71e</> ",
            "yarn_icon": "<#89b4fa>\ue6a7</> "
          },
          "template": " \ue718 {{ if .PackageManagerIcon }}{{ .PackageManagerIcon }} {{ end }}{{ .Full }} ",
          "trailing_diamond": "\ue0b4",
          "type": "node"
        },
        {
          "background": "#313244",
          "foreground": "#f9e2af",
          "leading_diamond": "\ue0b2",
          "style": "diamond",
          "template": " \ue235 {{ if .Error }}{{ .Error }}{{ else }}{{ if .Venv }}{{ .Venv }} {{ end }}{{ .Full }}{{ end }} ",
          "trailing_diamond": "\ue0b4",
          "type": "python"
        },
        {
          "background": "#313244",
          "foreground": "#89dceb",
          "leading_diamond": "\ue0b2",
          "style": "diamond",
          "template": " \ue626 {{ if .Error }}{{ .Error }}{{ else }}{{ .Full }}{{ end }} ",
          "trailing_diamond": "\ue0b4",
          "type": "go"
        },
        {
          "background": "#45475a",
          "foreground": "#cdd6f4",
          "leading_diamond": "\ue0b2",
          "style": "diamond",
          "options": {
            "time_format": "15:04:05"
          },
          "template": " {{ .CurrentDate | date .Format }} ",
          "trailing_diamond": "\ue0b4",
          "type": "time"
        }
      ],
      "type": "prompt"
    },
    {
      "alignment": "left",
      "newline": true,
      "segments": [
        {
          "foreground": "#a6e3a1",
          "foreground_templates": ["{{ if gt .Code 0 }}#f38ba8{{ end }}"],
          "options": {
            "always_enabled": true
          },
          "style": "plain",
          "template": "\u276f ",
          "type": "status"
        }
      ],
      "type": "prompt"
    }
  ],
  "console_title_template": "{{ .Folder }}",
  "final_space": true,
  "transient_prompt": {
    "background": "transparent",
    "foreground": "#cdd6f4",
    "template": "\u276f "
  },
  "version": 4
}
```

---

## Step 7: Configure PowerShell Profile

Create the profile directory and file:

```powershell
New-Item -ItemType Directory -Path "$HOME\OneDrive\Documents\WindowsPowerShell" -Force
```

Save the following as `%USERPROFILE%\OneDrive\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`:

> Note: If your Documents folder is NOT in OneDrive, use `%USERPROFILE%\Documents\WindowsPowerShell\` instead. Run `echo $PROFILE` in PowerShell to confirm the correct path.

```powershell
# Oh My Posh
& "$env:LOCALAPPDATA\Programs\oh-my-posh.exe" init pwsh --config "$env:LOCALAPPDATA\Programs\oh-my-posh-themes\night-owl.omp.json" | Invoke-Expression

# Terminal Icons - adds file/folder icons to ls and dir
Import-Module -Name Terminal-Icons

# Autocomplete with history
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -PredictionViewStyle ListView
Set-PSReadLineOption -EditMode Windows

# Welcome
$psVer = "$($PSVersionTable.PSVersion.Major).$($PSVersionTable.PSVersion.Minor)"
$dot = [char]0x25CF
$winLogo = [char]0xE70F
Write-Host ""
Write-Host "  $winLogo " -ForegroundColor Cyan -NoNewline
Write-Host "Windows PowerShell $psVer" -ForegroundColor White -NoNewline
Write-Host "  |  " -ForegroundColor DarkGray -NoNewline
Write-Host "$dot" -ForegroundColor Green -NoNewline
Write-Host " System Online" -ForegroundColor White -NoNewline
Write-Host "  |  " -ForegroundColor DarkGray -NoNewline
Write-Host "$dot" -ForegroundColor Cyan -NoNewline
Write-Host " Ready for Command" -ForegroundColor White
Write-Host ""
```

---

## Step 8: Configure Command Prompt (Clink)

Save the following as `%LOCALAPPDATA%\clink\oh-my-posh.lua`:

> Note: Replace `C:\\Users\\sheha` with your actual username path.

```lua
-- Suppress Clink banner
settings.set("clink.logo", "none")

-- Clear Windows banner
os.execute("cls")

-- Oh My Posh
load(io.popen('C:\\Users\\YOUR_USERNAME\\AppData\\Local\\Programs\\oh-my-posh.exe init cmd --config C:\\Users\\YOUR_USERNAME\\AppData\\Local\\Programs\\oh-my-posh-themes\\night-owl.omp.json'):read("*a"))()

-- Welcome greeting
local cyan = "\x1b[36m"
local white = "\x1b[97m"
local green = "\x1b[32m"
local gray = "\x1b[90m"
local reset = "\x1b[0m"
local dot = "\xe2\x97\x8f"
local winlogo = "\xee\x9c\x8f"

local handle = io.popen('cmd /c ver')
local cmdver = handle:read("*a"):match("Version ([%d%.]+)")
handle:close()

print("")
print("  " .. cyan .. winlogo .. " " .. reset .. white .. "Windows Command Prompt " .. cmdver .. reset .. gray .. "  |  " .. reset .. green .. dot .. reset .. white .. " System Online" .. reset .. gray .. "  |  " .. reset .. cyan .. dot .. reset .. white .. " Ready for Command" .. reset)
print("")
```

---

## Step 9: Configure Windows Terminal

Open Windows Terminal settings JSON (`Ctrl+Shift+,`) and update:

### Set default font and color scheme (under `profiles.defaults`):

```json
"defaults": {
    "colorScheme": "Catppuccin Mocha",
    "font": {
        "face": "CaskaydiaCove NFM"
    }
}
```

### Add Catppuccin Mocha color scheme (under `schemes` array):

```json
"schemes": [
    {
        "name": "Catppuccin Mocha",
        "cursorColor": "#f5e0dc",
        "selectionBackground": "#585b70",
        "background": "#1e1e2e",
        "foreground": "#cdd6f4",
        "black": "#45475a",
        "red": "#f38ba8",
        "green": "#a6e3a1",
        "yellow": "#f9e2af",
        "blue": "#89b4fa",
        "purple": "#f5c2e7",
        "cyan": "#94e2d5",
        "white": "#bac2de",
        "brightBlack": "#585b70",
        "brightRed": "#f38ba8",
        "brightGreen": "#a6e3a1",
        "brightYellow": "#f9e2af",
        "brightBlue": "#89b4fa",
        "brightPurple": "#f5c2e7",
        "brightCyan": "#94e2d5",
        "brightWhite": "#a6adc8"
    }
]
```

### Suppress PowerShell banner (update the PowerShell profile entry):

```json
{
    "commandline": "%SystemRoot%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe -NoLogo",
    "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
    "hidden": false,
    "name": "Windows PowerShell"
}
```

---

## Step 10: Configure VS Code Terminal Font

VS Code uses its own terminal font settings, separate from Windows Terminal. Without this, Oh My Posh icons and powerline glyphs will appear as broken squares in the VS Code integrated terminal.

Open VS Code Settings (`Ctrl + ,`), search for `terminal.integrated.fontFamily`, and set it to:

```
CaskaydiaCove NFM
```

Or add the following to your VS Code `settings.json` (`Ctrl + Shift + P` → "Preferences: Open User Settings (JSON)"):

```json
{
    "terminal.integrated.fontFamily": "CaskaydiaCove NFM"
}
```

> Note: If glyphs still appear broken, try `CaskaydiaCove NF` or `CaskaydiaCove NFP` instead.

---

## Step 11: Restart Windows Terminal and VS Code

Close all Windows Terminal windows completely (including system tray) and restart VS Code.

---

## What You Get

### PowerShell
- Custom night-owl Oh My Posh theme with Catppuccin Mocha color palette
- File/folder icons in `ls` and `dir` output (Terminal-Icons)
- History-based autocomplete dropdown (PSReadLine ListView)
- Clean welcome banner showing shell name and version

### Command Prompt
- Same Oh My Posh theme via Clink integration
- Enhanced tab completion and command history (Clink)
- Clean welcome banner showing shell name and version

### Both Shells
- Catppuccin Mocha dark background (#1e1e2e) with pastel accent colors
- Two-line prompt: info on line 1, input `❯` on line 2
- Git branch, status, and stash info when inside a repo
- Node.js, Python, Go version display in relevant directories
- Clock on the right side
- Green `❯` prompt that turns red on command errors

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Icons show as squares/boxes | Make sure font is set to `CaskaydiaCove NFM` in Terminal settings |
| Icons broken in VS Code terminal | Set `terminal.integrated.fontFamily` to `CaskaydiaCove NFM` in VS Code settings |
| Oh My Posh not found in PowerShell | Use full path: `& "$env:LOCALAPPDATA\Programs\oh-my-posh.exe"` |
| Script execution disabled | Run: `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| Spaces between powerline segments | Try `CaskaydiaCove NF` or `CaskaydiaCove NFP` font variants |
| Clink not loading in cmd | Restart Terminal fully, check Clink installed via `winget list chrisant996.Clink` |

### PATH Issues with Oh My Posh

**Problem:** After installing Oh My Posh, PowerShell or cmd may report `oh-my-posh is not recognized`. This happens because the Oh My Posh executable isn't in the system PATH, especially in Windows PowerShell 5.1.

**Why it happens:** The standalone download places `oh-my-posh.exe` in `%LOCALAPPDATA%\Programs\` which is not in the default PATH. Even if installed via `winget`, the MSIX package path (`WindowsApps`) may not be recognized by all shells.

**Fix:** Always use the full path to `oh-my-posh.exe` in config files:

- **PowerShell profile:** Use `& "$env:LOCALAPPDATA\Programs\oh-my-posh.exe"` instead of just `oh-my-posh`
- **Clink Lua script:** Use `C:\\Users\\YOUR_USERNAME\\AppData\\Local\\Programs\\oh-my-posh.exe` instead of just `oh-my-posh`

**Alternative fix:** Add Oh My Posh to your user PATH permanently:

```powershell
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$currentPath;$env:LOCALAPPDATA\Programs", "User")
```

Then restart Terminal. After this, `oh-my-posh` will work without the full path.

### MSIX Package Issues (winget install)

**Problem:** If you installed Oh My Posh via `winget install JanDeDobbeleer.OhMyPosh`, the MSIX sandbox may cause errors like `The system cannot find the file specified` when the init script tries to start subprocesses.

**Fix:** Uninstall the winget version and use the standalone download instead:

```powershell
winget uninstall JanDeDobbeleer.OhMyPosh
Invoke-WebRequest -Uri 'https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-windows-amd64.exe' -OutFile "$env:LOCALAPPDATA\Programs\oh-my-posh.exe"
```

---

## Software Versions Used

| Software | Version |
|---|---|
| Windows Terminal | Latest from Microsoft Store |
| Oh My Posh | 29.7.0 |
| CaskaydiaCove Nerd Font | Latest from Nerd Fonts |
| Clink | 1.9.17 |
| Terminal-Icons | Latest from PSGallery |
| PSReadLine | Latest prerelease from PSGallery |