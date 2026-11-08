
# Computer Tricks:
## 1. Guide: How to Completely Remove and Block Chrome's Silent 4GB AI Model (`weights.bin`)

Google Chrome silently downloads a ~4 GB local AI model (Gemini Nano) to power features like "Help me write", page summaries, and smart autofill. Deleting the file manually often causes Chrome to quietly re-download it on launch. 

Follow these steps to permanently delete the model and block future automatic downloads.

---

***Step 1: Turn Off AI Innovations in Settings:***
1. Open Google Chrome.
2. Go to **Settings** -> **AI Innovations** (or **Experimental AI** depending on your browser version).
3. Toggle **Off** all features like *Help me write*, *Create themes*, and *Tab organizer*.

---

***Step 2: Uninstall the Foundational Model:***
1. Navigate to:
   ```text
   chrome://on-device-internals
   ```
   *(Note: If this page is disabled, go to `chrome://chrome-urls`, select `chrome://on-device-internals`, and click **Enable internal debugging pages**.)*
2. Go to the **Model Status** tab.
3. Find the foundational model (`v3Nano`) and click **Uninstall** to reclaim your storage space immediately.

---

***Step 3: Block Chrome from Re-downloading the Model (OS-Specific):***
To ensure Chrome cannot automatically re-download the `weights.bin` file, apply the appropriate block for your operating system.

**Replace and Lock the `weights.bin` File (Alternative):**
If you prefer not to modify system policies, you can replace the model with an empty file and lock it so Chrome cannot overwrite it.

First, navigate to your Chrome User Data directory:
* **Windows:** Press `Win + R`, paste `%LOCALAPPDATA%\Google\Chrome\User Data\OptGuideOnDeviceModel`, and press **Enter**.

Delete any folders or files inside this directory. Then, create an empty dummy file named `weights.bin` and restrict write permissions:

#### 📂 Windows (PowerShell/Command Prompt)
Within the folder, open terminal and run:
```powershell
# Create empty file
New-Item -Path . -Name "weights.bin" -ItemType "file" -Force

# Set the file to Read-Only
attrib +R weights.bin
```



## 2. Google Multi-Account Mobile Creation & Safety Isolation Blueprints

### 1. Prerequisites and Required Tools

Before starting the creation process, ensure you have the following assets ready on your mobile device:

### Hardware & Network Requirements
* **One Active SIM Card:** A physical mobile number capable of receiving SMS verification codes.
* **Cellular Data Connection:** A mobile plan with 4G or 5G connectivity. **Do not use Wi-Fi.**

### Software Isolation Matrix
Download these four distinct, free browser applications from your device's official app store. Each browser acts as a strictly isolated storage container for exactly one Google account:
* **Browser 1 (for Account #1):** Brave Browser
* **Browser 2 (for Account #2):** Firefox
* **Browser 3 (for Account #3):** Opera Browser
* **Browser 4 (for Account #4):** DuckDuckGo Privacy Browser

---

### 2. Step-by-Step Blueprint for Safe Account Creation

You must repeat this entire 4-step workflow from the beginning for **each individual account** you create. Never create two accounts back-to-back without executing the footprint reset steps.

#### Step 1: Network & Hardware Footprint Reset:
This step destroys the digital trail that allows automated systems to link your new account to previous ones.
1. **Turn off Wi-Fi:** Open your mobile quick-settings panel and completely disable Wi-Fi. 
2. **Cycle Cellular IP Address:** Turn on **Airplane Mode** for exactly 15 seconds, then turn it off. This forces your mobile carrier to assign a brand-new, unique cellular IP address to your device.
3. **Purge Browser Storage:** Long-press the icon of the specific browser assigned to the account you are about to create (e.g., Firefox for Account #2). Tap **App Info** $\rightarrow$ **Storage** $\rightarrow$ select **Clear All Data** and **Clear Cache**.
4. **Reset Device Advertising ID:** Go to your phone's main **Settings** $\rightarrow$ **Privacy & Security** $\rightarrow$ **Ads** $\rightarrow$ tap **Reset Advertising ID**. This generates a completely clean hardware tracker.

#### Step 2: Accessing the Clean Workspace:
1. Open your freshly cleared, dedicated browser application.
2. Confirm you are browsing strictly on cellular mobile data.
3. Navigate directly to the official Google Account gateway: `https://google.com`.

#### Step 3: Registration Data Input:
1. Tap **Create account** and select **For my personal use**.
2. **First & Last Name:** Enter your details naturally. Avoid sequential naming patterns (like *JohnDoe1*, *JohnDoe2*) as this flags automated bot detection.
3. **Birthday & Gender:** Input valid demographic information.
4. **Choose Your Gmail Address:** Pick a unique, distinct username string.
5. **Set a Strong Password:** Create a robust password unique to this specific account.

---

### 3. Important Safety Rules for Long-Term Maintenance

To ensure your newly created accounts remain active and do not get flagged post-creation, strictly adhere to these three management rules:

* **Rule 1: Never Cross-Log:** Never use your Brave Browser container to log into Account #2, and never use your Firefox container to log into Account #1. Keep the isolation absolute.
* **Rule 2: Avoid System Account Sign-Ins:** Do not add these secondary accounts to your phone's global device settings menu (e.g., *Settings -> Accounts -> Add Account*). Doing so allows the mobile operating system to link all accounts to your phone's unique hardware serial number (IMEI).
* **Rule 3: Respect the Hard Cap:** Do not attempt to verify more than 4 accounts with your single SIM card. Attempting a 5th account will trigger a permanent number block and risks flagging the existing 4 accounts linked to that number.
---

## 3. Ultimate Free Google Drive Storage Network Guide

<style>
/* Targets inline code blocks wrapped in a red span */
span[style*="color: red"] code,
span[style*="color:red"] code {
    color: #A31515 !important;            /* Darker, readable red text */
    background-color: #FFF0F0 !important; /* Soft light pink background */
}
</style>


### Phase 1: Set Up the Account & Prevent Deletion
*This phase tricks Google into thinking you are active so it never deletes your files.*

#### Step 1: Create Account
Create your new secondary Google account (e.g., <span style="color: red;">`mycloudstorage2@gmail.com`</span>).

#### Step 2: Open Apps Script
Open a new browser tab, type <span style="color: red;">**`script.new`**</span> in the URL bar, and hit Enter.

#### Step 3: Paste the Code
Delete any default text in the code editor, and paste this exact script:

```javascript
function keepAccountAlive() {
  var files = DriveApp.searchFiles('title contains "Alive"');
  Logger.log("Account pinged successfully to reset the 2-year clock.");
  
  // Sends a confirmation to your inbox
  GmailApp.sendEmail(Session.getActiveUser().getEmail(), "Cloud Storage Alive Confirmation", "Your scheduled account ping ran successfully today.");
}
```

```javascript
function keepAccountAlive() {
  try {
    // 1. Gmail Activity: Send a self-email
    var myEmail = Session.getActiveUser().getEmail();
    MailApp.sendEmail({
      to: myEmail,
      subject: "Automated Activity Ping",
      body: "This is an automated message to help keep this account active. Processed on: " + new Date()
    });
    
    // 2. Drive Activity: Find or create a log file and update it
    var fileName = "account_activity_log.txt";
    var files = DriveApp.getFilesByName(fileName);
    var file;
    
    if (files.hasNext()) {
      file = files.next();
      file.setContent("Last programmatic sign-in activity: " + new Date());
    } else {
      file = DriveApp.createFile(fileName, "Log created: " + new Date());
    }
    
    console.log("Activity tasks completed successfully.");
  } catch (error) {
    console.error("An error occurred: " + error.toString());
  }
}
```

#### Step 4: Save Project
Click the **Save** icon (the small floppy disk) at the top.

#### Step 5: Open Triggers Dashboard
Click the **Triggers** icon (the alarm clock) on the left vertical menu.

#### Step 6: Create New Trigger
Click the blue <span style="color: red;">**`+ Add Trigger`**</span> button in the bottom right corner.


#### Step 7: Configure Options
Set the configuration options exactly like this:
* **Choose which function to run:** <span style="color: red;">`keepAccountAlive`</span>
* **Select event source:** <span style="color: red;">`Time-driven`</span>
* **Select type of time-based trigger:** <span style="color: red;">`Week timer`</span>
* **Select day of week:** <span style="color: red;">`Every Monday`</span> (or any day you prefer)

#### Step 8: Commit Trigger
Click the blue **Save** button.

#### Step 9: Bypass Security Warnings
When the security popup appears, click through these steps:
1. Click your **Google Email Account**.
2. Click the small **Advanced** link at the bottom.
3. Click **Go to Untitled project (unsafe)**.
4. Click **Allow**.

<style>
/* This changes the left vertical bar color of all blockquotes */
blockquote 
{
    border-left: 4px solid #5C965C !important;
}
</style>
> <span style="color: green;">**Note:**</span> <span style="color: #6B9E6B;">You can now close this tab. The account will stay alive forever automatically.</span>



---

### Phase 2: Link the Account to Your Unified Dashboard
*This phase connects your accounts so you can manage them from one spot without logging in and out.*

* **Step 1:** Go to a free cloud aggregator website like **MultCloud** or **Koofr** and create one master account.
* **Step 2:** Inside your aggregator dashboard, click **Add Cloud** or **Connect Cloud**.
* **Step 3:** Select **Google Drive** from the menu.
* **Step 4:** Log into the secondary Google account you just created and authorize the aggregator to access it.
* **Step 5:** Repeat this process for all your accounts. They will now appear as a simple list of folders on your dashboard screen.

---

## 4. Permanent & Unrecoverable Recycle Bin Cleaner (SSD & HDD)


### Step 1: Force-Empty the Recycle Bin Directories (CMD)
Open **Command Prompt as Administrator** and wipe the Recycle Bin folders across your drives:
```cmd
rd /s /q C:\$Recycle.Bin
rd /s /q D:\$Recycle.Bin
```
---

### Step 2: Make the Deleted Data Unrecoverable
1. Open **PowerShell as Administrator**.
2. Execute the ReTrim command for each partition:
```powershell
Optimize-Volume -DriveLetter C -ReTrim -Verbose
Optimize-Volume -DriveLetter D -ReTrim -Verbose
```
* **Safety:** Does zero unnecessary writes, preserving your SSD's write lifespan (TBW).
* **Result:** The underlying data blocks for all previously emptied files are permanently cleared from the SSD.


---

## 5. System Volume Information (Deletion):


You should **never delete these files directly** from this software, as doing so will break the Windows Volume Shadow Copy service and can corrupt your system's recovery database. 

Instead, you can safely wipe them out by running a single command as an administrator. 


1.  Right-click your Windows **Start Button** and select **Terminal (Admin)** or **Command Prompt (Admin)**.
2.  Copy and paste the following command, then press **Enter**:
    ```
    vssadmin delete shadows /all /quiet
    ```
  
## 6. Hibernation File (Deletion):
The selected file `hiberfil.sys` is the **Windows Hibernation file**. 

When you put your computer into Hibernation mode, Windows dumps everything currently running in your RAM onto your hard drive into this file so your computer can power down completely. When you turn it back on, it loads this file back into memory so you can resume exactly where you left off. 


How to Safely Delete hiberfil.sys 

1.  Right-click the Windows **Start Button**.
2.  Select **Terminal (Admin)** or **Command Prompt (Admin)**.
3.  Type the following command in cmd and press **Enter**:
    ```
    powercfg -h off
    ```
    Use code with caution.
---

## 7. Master Setup Guide: Fixing D-Link DIR-615 Sleep/Wake Internet Glitches

### Why this problem happens:
When a PC wakes from sleep, it bursts DNS and connection queries. The D-Link DIR-615 (32 MB RAM) crashes when its internal DNS hits 150 concurrent queries or its NAT memory table hits 2,048 connections (`nf_conntrack: table full`), while Wi-Fi idle timeouts drop the repeater link.

---

### Part 1: D-Link Router Settings (`192.168.1.1`)

#### 1. Turn Off DNS Relay (Stops Internal DNS Crashes)
* Go to: **Settings** > **Network** (IPv4 and IPv6 tab).
* Under *Dynamic IP Addresses*, toggle **DNS relay** to **OFF**.
* Click **Apply**.

#### 2. Configure Public Name Servers (Hands Fast DNS Directly to PC)
* Go to: **Settings** > **Internet** > **DNS**.
* Under **IPv4**, toggle **Manual** to **ON**.
* Under *Name Servers (IPv4)*, add:
  * `1.1.1.1`
  * `8.8.8.8`
* *(Keep IPv6 Manual OFF).*
* Click **Apply**.

#### 3. Disable UPnP (Stops Port-Mapping Timeouts)
* Go to: **Functions** > **UPnP**.
* Toggle **Enable UPnP** to **OFF**.
* Click **Apply**.

#### 4. Stabilize Wi-Fi Link & Prevent Sleep Disconnects
* Go to: **Functions** > **Wi-Fi** > **Additional**:
  * **Bandwidth:** Change to **`20 MHz`**.
  * **Drop Multicast:** Toggle to **ON**.
  * **Station Keep Alive (in seconds):** Change from `0` to **`30`** *(prevents idle disconnects while PC sleeps)*.
* Click **Apply**.

#### 5. Reboot Router
* Go to: **Management** > **Reboot**.


#### 6. Flush DNS Cache
* Open Command Prompt and run:
  ```cmd
  ipconfig /flushdns
  ```

---

### Part 3: Main Upstream Router (`Spyfiber` @ `192.168.0.1`)

* Log into `192.168.0.1` > **WLAN / 2.4G Wireless**.
* Change **Channel** from *Auto (7)* to a fixed **`1`** or **`6`** at **`20 MHz`** to eliminate RF packet corruption (`MIC errors`) and 25-second roaming drops.

---

## 8. Comprehensive Windows Package Manager (Winget) Commands Guide

### 1. Introduction to Winget

Winget (Windows Package Manager) is a built-in command-line tool for Windows 10, Windows 11, and Windows Server. It automates software management, allowing you to install, update, and configure applications safely without manually navigating websites or clicking through setup wizards.

* **Prerequisites:** You must be logged into Windows to execute commands.
* **Setup:** If it is not installed by default, download the App Installer package from the Microsoft Store or download the latest version from GitHub.
* **Interface:** Run these commands using **Command Prompt** or **PowerShell**.

---

### 2. Core Command Reference

### Search for Apps
Find the exact package name of an application before installing it.
```bash
winget search <app name>
```

### Install an App
Install a specific application using its package name.
```bash
winget install <app name>
```
*Example:* `winget install Google.Chrome`

### Silent Installation (Skip Dialogs)
Bypass all setup pop-ups, checkboxes, and wizard dialogs for a completely silent install.
```bash
winget install <app name> -h
```
*(Note: The `-h` flag stands for "silent" or "hidden")*

### Bulk Install Apps
Install multiple applications simultaneously while automatically accepting all license agreements and hiding setup dialogs.
```bash
winget install <app1> <app2> <app3> --accept-package-agreements --accept-source-agreements -h
```
*Example:*
```bash
winget install Google.Chrome Spotify.Spotify VideoLAN.VLC --accept-package-agreements --accept-source-agreements -h
```

### Update All Apps
Update every Winget-compatible application installed on your PC to its latest version with a single command.
```bash
winget upgrade --all
```

### Uninstall an App
Remove an application completely from your system.
```bash
winget uninstall <app name>
```
*Example:* `winget uninstall firefox`

---

## 9. Perplexity Pro (Education Plan) Guide & Prompt

This document outlines the preparation steps and contains the prompt used to generate randomized, realistic student data for SheerID verification.

---

### Requirements & Setup Steps:

1. **Referral Link**
   - Obtain the referral link for the account where the Pro plan needs to be activated.
2. **Network/IP Configuration**
   - Use cellular data from an Indian SIM card (this provides a dynamic IP address rotation, bypassing the need for a VPN).
3. **Template Preparation**
   - Use a fee-receipt template from the **School of Open Learning, Delhi University (DU SOL)**.
4. **SheerID Submission**
   - Match the form submission details exactly with the modified data on the generated fee receipt before uploading the document.
5. **Phone Verification**
   - Ensure a phone device is available.

---

### Data Generation Prompt:

Copy and paste the prompt below into an LLM to generate the randomized text data required to edit your document.

```
Please generate a set of randomized, realistic data matching the guidelines below to fill out a school fee-receipt template. Provide only the plain text values.

1. **Name**: [Random Indian Name]
2. **Father's Name**: [Random Indian Name]
3. **Mother's Name**: [Random Indian Name]
4. **Delhi Address**: [A realistic random Delhi address with street name and correct PIN code]
5. **Barcode No**: [Randomize only the last 3 digits of a standard barcode]
6. **SOL Roll No**: [Randomize only the last 4 digits of a standard SOL Roll Number (format: XX-X-XX-XXXXXX)]
7. **User ID**: [Randomize only the last 4 digits]
8. **Receipt No**: [Randomize only the last 4 digits]
9. **Date of Birth**: [Random date between 2000 and 2006]
10. **Phone Number**: [10-digit Indian mobile number in a random but standard format]
11. **Ref. No**: [Randomize only the last 4 digits]
12. **Email ID**: [Generate in the format: FirstnameSolRoll@sol.du.ao.in (e.g., Shreya25-6-08-043592@sol.du.ao.in, with the first name in Sentence case)]

**Verification Date Check**:
- Today's date is June 25, 2026.
- If the document template's issue date is older than 90 days from today, modify the "Date of Issue", "SOL Roll No (year segment)", and "Receipt Date" to make them less than 90 days old. Otherwise, leave the original dates unchanged.
```

---

## 10. OpenCode CLI Setup Guide:
### 📥 PART 1: Full Installation Guide
Follow these steps to configure Node.js, install the global npm package, and initialize OpenCode.
#### Step 1: Install Node.js & npm:

   1. Download the LTS (Long Term Support) installer from the official [Node.js Website](https://nodejs.org/).
   2. Run the .msi file and proceed through the installation wizard using the default settings.
   3. Restart your computer to update your system's PATH variables.

#### Step 2: Verify the Installation:
Open PowerShell or Command Prompt and check the versions:
```
node -v
npm -v
```
(Both commands should return version numbers, e.g., v20.x.x and 10.x.x)
#### Step 3: Global Package Installation:
Open **PowerShell as an Administrator** and execute:
`npm install -g opencode-ai@latest`

#### Step 4: Verify & Initialize OpenCode:
Launch the CLI interface to verify a successful installation:
```
opencode --version
opencode
```

------------------------------
### 🗑️ PART 2: Clean Uninstallation Guide (Manual)
Follow these steps if you want to manually purge OpenCode, its cached keys, local databases, and temporary execution logs from your system.
#### Step 1: Run Internal Clean-up:
Before removing the files, let the CLI delete its internal project trackers:

`opencode uninstall`

*(Confirm `Y` when prompted to clear local session history).*
#### Step 2: Uninstall Global npm Package:
Remove the executable binary from the global npm registry:

```
npm uninstall -g opencode-ai
npm cache clean --force
```

#### Step 3: Purge Residual Directories (PowerShell Commands):
Open **PowerShell** and run these commands sequentially to wipe all hidden configurations, local databases, and temporary `npx` executions:
```powershell
# 1. Remove global user preferences, AI profiles, and API tokens
Remove-Item -Recurse -Force "$env:USERPROFILE\.config\opencode" -ErrorAction SilentlyContinue
# 2. Remove local project caches and database tracks
Remove-Item -Recurse -Force "$env:LOCALAPPDATA\opencode" -ErrorAction SilentlyContinue
# 3. Clean global npm execution shortcuts
Remove-Item -Force "$env:APPDATA\npm\opencode*" -ErrorAction SilentlyContinue
# 4. Clear temporary npx run caches
Remove-Item -Recurse -Force "$env:LOCALAPPDATA\npm-cache\_npx" -ErrorAction SilentlyContinue
```


#### Step 4: Final Verification
Restart your terminal and run:
`opencode --version`

------------------------------
### ⚡ PART 3: 1-Click Automated Uninstallation Script
---
#### Setup Instructions:

To wipe OpenCode in exactly one click, you can create a localized script file that triggers administrative elevation and performs the complete purge.

1. Right-click your desktop, select **New > Text Document**.
2. Rename the file to `purge-opencode.bat` (Ensure the extension changes from `.txt` to `.bat`).
3. Right-click the file, select **Edit** (or Open with Notepad), paste the block below, and save it.
   
```batch
@echo off
:: Batch script to force elevate privileges and completely purge OpenCode CLI
:: Target OS: Windows 11 (npm ecosystem)

:init
set localdir=%~dp0
set scriptname=%~nx0

:: Check for administrative privileges
net session >nul 2>&1
if %errorLevel% == 0 (
    goto :purge_process
) else (
    goto :elevate
)

:elevate
echo [!] Requesting administrative privileges to clear registry paths...
powershell -Command "Start-Process -FilePath '%0' -ArgumentList 'am_admin' -Verb runAs"
exit /b

:purge_process
cls
echo ===================================================
echo   OPENCODE CLI 1-CLICK ABSOLUTE PURGE TOOL         
echo ===================================================
echo.

echo [*] Step 1: Running internal OpenCode cleanup tracker...
call opencode uninstall --yes >nul 2>&1

echo [*] Step 2: Removing global npm packages...
call npm uninstall -g opencode-ai >nul 2>&1
call npm cache clean --force >nul 2>&1

echo [*] Step 3: Dropping persistent configuration databases...
powershell -Command "Remove-Item -Recurse -Force '$env:USERPROFILE\.config\opencode' -ErrorAction SilentlyContinue"
powershell -Command "Remove-Item -Recurse -Force '$env:LOCALAPPDATA\opencode' -ErrorAction SilentlyContinue"

echo [*] Step 4: Cleaning npm environment and execution paths...
powershell -Command "Remove-Item -Force '$env:APPDATA\npm\opencode*' -ErrorAction SilentlyContinue"
powershell -Command "Remove-Item -Force '$env:LOCALAPPDATA\npm-cache\_npx' -ErrorAction SilentlyContinue"

echo.
echo ===================================================
echo [SUCCESS] OpenCode CLI has been wiped from this system.
echo ===================================================
echo.
pause
exit
```

#### How to use it:
* Double-click the `purge-opencode.bat` file.
* Accept the Windows User Account Control (UAC) prompt to allow it to run as Administrator.
* The script will wipe all directories and notify you when the purge is complete.

---

## 11. Universal Force-Delete Quick Guide

### Step 1: Open Command Prompt as Admin
Always start by clicking the Start menu, typing `cmd`, right-clicking **Command Prompt**, and selecting **Run as administrator**.

---

### Step 2: Fix Permissions (If it says "Access Denied" or "Need Permission")
If Windows says you do not have permission to delete the item, run these two commands to force ownership (replace the path with your actual file/folder path):

* **Command 1 (Take Ownership):**
```cmd
takeown /f "C:\Path\To\Your\FolderOrFile" /r /d y
```

* **Command 2 (Grant Full Control):**
```cmd
icacls "C:\Path\To\Your\FolderOrFile" /grant everyone:F /t
```

---

### Step 3: Run the Force-Delete Command
Choose the correct command depending on whether you are deleting a single file or a whole folder:

* **To Delete a FOLDER (and everything inside it):**
```cmd
rmdir /s /q "C:\Path\To\Your\Folder"
```

* **To Delete a single FILE:**
```cmd
del /f /q /a "C:\Path\To\Your\File.txt"
```

---

### 💡 Quick Cheat Sheet for the Flags

* `/f` = Forces deletion of read-only files.
* `/q` = Quiet mode (deletes instantly without asking "Are you sure?").
* `/s` = Deletes the specified directory and all subdirectories/files inside it.
* `/r` = Recursive (applies the change to all files inside a folder).
* `/t` = Tree (applies permissions down through all subfolders).
Here is the continuation to add directly to your `hackmd.md` file, matching your existing document formatting and styling:

---

Here is the updated **Section 12** for your `hackmd.md` file. 

Instead of creating a new Section 13, all four scripts (Favorite GIFs Exporter, Emoji CDN Extractor, Server Emoji Bulk Deleter, and the Links-to-Animated Optimizer Bookmarklet) have been consolidated under **Section 12** as distinct toolkits with their respective instructions and code blocks.

---
## 12. Discord Asset Management & Automation Suite

This toolkit contains browser console scripts and bookmarklets to extract, optimize, manage, and batch-clean Discord emojis and favorite GIFs.

---

### Tool 1: Discord Favorite GIFs to JSON Exporter

Extracts and exports your Discord Favorite GIFs into a clean, standalone JSON file containing only high-resolution `src` URLs. Automatically parses Discord's internal cache / React Fiber / Settings Protobuf, auto-refreshes expiring CDN attachment links via Discord's API, and upgrades compressed `.mp4` previews (Tenor, Giphy, Imgur) to full-quality `.gif` links.

#### How to Use:
1. Open [Discord Web](https://discord.com/app) in your browser and log in.
2. Press `Ctrl + Shift + I` (or `Cmd + Option + I` on macOS) and switch to the **Console** tab.
3. If prompted by Discord, type `allow pasting` and press Enter.
4. Paste the script below and hit Enter. The high-quality `.json` file will download automatically.

#### Script (`discord-fav-gifs-exporter.js`):
<details>
<summary>Click to expand script</summary>

```javascript
/**
 * Discord Favorite GIF High-Quality `src` JSON Exporter (v5 - JSON Only)
 * Automatically detects all Favorite GIFs, refreshes expiring Discord CDN links,
 * upgrades .mp4 links to Full-HD .gif links, and exports ONLY the `src` URLs to JSON.
 */
(async function ExportDiscordFavoriteGifSrcsOnly() {
    console.log("%c[GIF JSON Exporter] Extracting Favorite GIFs...", "color:#5865F2;font-weight:bold;font-size:14px;");

    // =========================================================================
    // 1. EXTRACT WEBPACK/RSPACK MODULE CACHE & DISCORD INTERNALS
    // =========================================================================
    function isGifMap(obj) {
        if (!obj || typeof obj !== "object" || Array.isArray(obj)) return false;
        const keys = Object.keys(obj);
        if (keys.length === 0) return false;
        const first = obj[keys[0]];
        return (
            first &&
            typeof first === "object" &&
            typeof first.src === "string" &&
            (typeof first.width === "number" || typeof first.height === "number") &&
            ("order" in first || "format" in first)
        );
    }

    function getDiscordInternals() {
        let wpRequire = null;
        const chunkGlobal = window.webpackChunkdiscord_app;

        if (Array.isArray(chunkGlobal)) {
            const chunkId = "_dgd_" + Date.now();
            chunkGlobal.push([[chunkId], {}, (req) => { wpRequire = req; }]);
            chunkGlobal.pop();
        }

        let moduleCache = wpRequire?.c || null;
        if (wpRequire && !moduleCache) {
            const probeId = "__dgd_probe_" + Math.random().toString(36).slice(2);
            try {
                Object.defineProperty(Object.prototype, probeId, {
                    configurable: true,
                    get() {
                        delete Object.prototype[probeId];
                        moduleCache = this;
                        return { exports: {} };
                    }
                });
                wpRequire(probeId);
            } catch {} finally {
                delete Object.prototype[probeId];
            }
        }

        let protoStore = null;
        let token = null;
        let restApi = null;
        let foundGifsMap = null;
        const visited = new WeakSet();

        function searchForGifMap(obj, depth = 0) {
            if (!obj || typeof obj !== "object" || depth > 3 || visited.has(obj)) return;
            visited.add(obj);

            if (isGifMap(obj)) {
                if (!foundGifsMap || Object.keys(obj).length > Object.keys(foundGifsMap).length) {
                    foundGifsMap = obj;
                }
                return;
            }
            if (obj.favoriteGifs?.gifs && isGifMap(obj.favoriteGifs.gifs)) {
                foundGifsMap = obj.favoriteGifs.gifs;
                return;
            }
            if (depth < 3) {
                for (const k of Object.keys(obj)) {
                    if (k === "window" || k === "document" || k === "prototype") continue;
                    try {
                        const child = obj[k];
                        if (child && typeof child === "object") searchForGifMap(child, depth + 1);
                    } catch {}
                }
            }
        }

        function inspectCandidate(item) {
            if (!item || (typeof item !== "object" && typeof item !== "function")) return;

            try {
                if ("frecencyWithoutFetchingLatest" in item) {
                    protoStore = item;
                    const gifs = item.frecencyWithoutFetchingLatest?.favoriteGifs?.gifs;
                    if (isGifMap(gifs)) foundGifsMap = gifs;
                }
            } catch {}

            try { searchForGifMap(item, 0); } catch {}

            try {
                if (!token && typeof item.getToken === "function") {
                    const t = item.getToken();
                    if (typeof t === "string" && t.length > 20) token = t;
                }
            } catch {}

            try {
                if (
                    !restApi &&
                    typeof item.get === "function" &&
                    typeof item.post === "function" &&
                    typeof item.put === "function" &&
                    typeof item.patch === "function"
                ) {
                    restApi = item;
                }
            } catch {}
        }

        function inspectModuleExports(exp) {
            if (!exp) return;
            inspectCandidate(exp);
            if (typeof exp === "object" || typeof exp === "function") {
                try {
                    for (const key of Reflect.ownKeys(exp)) {
                        if (key === "length" || key === "prototype" || key === "name" || key === "arguments" || key === "caller") continue;
                        try { inspectCandidate(exp[key]); } catch {}
                    }
                } catch {}
            }
        }

        if (moduleCache) {
            for (const m of Object.values(moduleCache)) {
                if (m?.exports) inspectModuleExports(m.exports);
            }
        }

        if (wpRequire?.m && (!foundGifsMap || !token || !restApi)) {
            for (const modId of Object.keys(wpRequire.m)) {
                try {
                    const fnStr = Function.prototype.toString.call(wpRequire.m[modId]);
                    if (
                        fnStr.includes("frecencyWithoutFetchingLatest") ||
                        fnStr.includes("favoriteGifs") ||
                        fnStr.includes("UserSettingsProtoStore") ||
                        fnStr.includes("settings-proto") ||
                        fnStr.includes("refresh-urls") ||
                        (fnStr.includes("getToken") && fnStr.includes("getId"))
                    ) {
                        inspectModuleExports(wpRequire(modId));
                    }
                } catch {}
            }
        }

        return { protoStore, token, restApi, foundGifsMap };
    }

    // =========================================================================
    // 2. REACT FIBER & PROTOBUF API FALLBACKS
    // =========================================================================
    function extractGifsFromReactFiber() {
        const appMount = document.getElementById("app-mount") || document.body;
        const fiberKey = Object.keys(appMount).find(
            (k) => k.startsWith("__reactFiber$") || k.startsWith("__reactContainer$")
        );
        if (!fiberKey) return null;

        let bestMap = null;
        const stack = [appMount[fiberKey]];
        const seen = new WeakSet();
        let steps = 0;

        function checkValue(val) {
            if (!val || typeof val !== "object") return;
            if (isGifMap(val)) {
                if (!bestMap || Object.keys(val).length > Object.keys(bestMap).length) bestMap = val;
                return;
            }
            if (val.favoriteGifs?.gifs && isGifMap(val.favoriteGifs.gifs)) {
                bestMap = val.favoriteGifs.gifs;
                return;
            }
            if (Array.isArray(val) && val.length > 0 && val[0]?.src && ("order" in val[0] || "format" in val[0])) {
                const converted = {};
                val.forEach((item, idx) => {
                    const key = item.url || item.src;
                    if (key) {
                        converted[key] = {
                            format: item.format ?? 1,
                            src: item.src,
                            width: item.width ?? 0,
                            height: item.height ?? 0,
                            order: item.order ?? idx + 1
                        };
                    }
                });
                if (Object.keys(converted).length > Object.keys(bestMap || {}).length) bestMap = converted;
            }
        }

        while (stack.length > 0 && steps < 40000) {
            const node = stack.pop();
            if (!node || seen.has(node)) continue;
            seen.add(node);
            steps++;

            try {
                const props = node.memoizedProps;
                if (props && typeof props === "object") {
                    checkValue(props);
                    for (const v of Object.values(props)) checkValue(v);
                }
                let state = node.memoizedState, sCount = 0;
                while (state && sCount < 15) {
                    checkValue(state.memoizedState);
                    state = state.next;
                    sCount++;
                }
            } catch {}

            if (node.child) stack.push(node.child);
            if (node.sibling) stack.push(node.sibling);
        }

        return bestMap;
    }

    class ProtoReader {
        constructor(bytes) { this.bytes = bytes; this.pos = 0; this.end = bytes.length; }
        eof() { return this.pos >= this.end; }
        readVarint() {
            let result = 0n, shift = 0n;
            while (this.pos < this.end) {
                const b = this.bytes[this.pos++];
                result |= BigInt(b & 0x7f) << shift;
                if ((b & 0x80) === 0) return Number(result);
                shift += 7n;
            }
            return Number(result);
        }
        readBytes() {
            const len = this.readVarint(), start = this.pos;
            this.pos = Math.min(this.end, this.pos + len);
            return this.bytes.subarray(start, this.pos);
        }
        readString() { return new TextDecoder("utf-8").decode(this.readBytes()); }
        skip(wireType) {
            if (wireType === 0) this.readVarint();
            else if (wireType === 1) this.pos += 8;
            else if (wireType === 2) this.pos += this.readVarint();
            else if (wireType === 5) this.pos += 4;
        }
    }

    function decodeFrecencyProtoBase64(b64String) {
        const binStr = atob(b64String);
        const bytes = new Uint8Array(binStr.length);
        for (let i = 0; i < binStr.length; i++) bytes[i] = binStr.charCodeAt(i);

        const gifsMap = {};
        const root = new ProtoReader(bytes);

        function parseFavoriteGif(gifBytes) {
            const r = new ProtoReader(gifBytes);
            const gif = { format: 1, src: "", width: 0, height: 0, order: 0 };
            while (!r.eof()) {
                const tag = r.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
                if (fieldNo === 1 && wireType === 0) gif.format = r.readVarint();
                else if (fieldNo === 2 && wireType === 2) gif.src = r.readString();
                else if (fieldNo === 3 && wireType === 0) gif.width = r.readVarint();
                else if (fieldNo === 4 && wireType === 0) gif.height = r.readVarint();
                else if (fieldNo === 5 && wireType === 0) gif.order = r.readVarint();
                else r.skip(wireType);
            }
            return gif;
        }

        function parseMapEntry(entryBytes) {
            const r = new ProtoReader(entryBytes);
            let key = "", value = null;
            while (!r.eof()) {
                const tag = r.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
                if (fieldNo === 1 && wireType === 2) key = r.readString();
                else if (fieldNo === 2 && wireType === 2) value = parseFavoriteGif(r.readBytes());
                else r.skip(wireType);
            }
            if (key && value) gifsMap[key] = value;
        }

        function parseFavoriteGifsContainer(containerBytes) {
            const r = new ProtoReader(containerBytes);
            while (!r.eof()) {
                const tag = r.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
                if (fieldNo === 1 && wireType === 2) parseMapEntry(r.readBytes());
                else r.skip(wireType);
            }
        }

        while (!root.eof()) {
            const tag = root.readVarint(), fieldNo = tag >>> 3, wireType = tag & 7;
            if (fieldNo === 2 && wireType === 2) parseFavoriteGifsContainer(root.readBytes());
            else root.skip(wireType);
        }

        return gifsMap;
    }

    // =========================================================================
    // 3. LOCATE FAVORITE GIFS
    // =========================================================================
    const { protoStore, token, restApi, foundGifsMap } = getDiscordInternals();
    let rawGifs = foundGifsMap || protoStore?.frecencyWithoutFetchingLatest?.favoriteGifs?.gifs;

    if (!rawGifs || Object.keys(rawGifs).length === 0) {
        rawGifs = extractGifsFromReactFiber();
    }

    if (!rawGifs || Object.keys(rawGifs).length === 0) {
        try {
            let settingsB64 = null;
            if (restApi?.get) {
                const res = await restApi.get({ url: "/users/@me/settings-proto/2" });
                settingsB64 = (res?.body || res)?.settings;
            } else if (token) {
                const resp = await fetch("/api/v9/users/@me/settings-proto/2", {
                    headers: { "Authorization": token }
                });
                if (resp.ok) settingsB64 = (await resp.json())?.settings;
            }
            if (settingsB64) rawGifs = decodeFrecencyProtoBase64(settingsB64);
        } catch (err) {
            console.error("[GIF JSON Exporter] Failed to fetch settings-proto/2:", err);
        }
    }

    if (!rawGifs || Object.keys(rawGifs).length === 0) {
        console.error("[GIF JSON Exporter] Could not locate Favorite GIFs.");
        return;
    }

    // =========================================================================
    // 4. HIGH-QUALITY URL CLEANER & MP4 -> GIF URL UPGRADER
    // =========================================================================
    function isDiscordCdnAttachment(url) {
        if (!url) return false;
        try {
            const parsed = new URL(url);
            const host = parsed.hostname.toLowerCase();
            if (host === "cdn.discordapp.com" || host === "media.discordapp.net") {
                const path = parsed.pathname.toLowerCase();
                return path.startsWith("/attachments/") || path.startsWith("/ephemeral-attachments/");
            }
        } catch {}
        return false;
    }

    function getHighQualityGifUrl(url) {
        if (!url) return url;
        try {
            let parsed = new URL(url);
            const host = parsed.hostname.toLowerCase();

            // 1. Unwrap Discord external proxy URLs (images-ext-1.discordapp.net/external/...)
            if (host.endsWith(".discordapp.net") && parsed.pathname.startsWith("/external/")) {
                const match = parsed.pathname.match(/^\/external\/[^/]+\/(https?)\/(.+)$/);
                if (match) parsed = new URL(`${match[1]}://${match[2]}`);
            }

            const currentHost = parsed.hostname.toLowerCase();

            // 2. Clean Discord CDN / Media Attachment URLs for 100% original file quality
            if (currentHost === "cdn.discordapp.com" || currentHost === "media.discordapp.net") {
                parsed.hostname = "cdn.discordapp.com";
                for (const p of ["format", "animated", "width", "height", "quality", "size"]) {
                    parsed.searchParams.delete(p);
                }
                return parsed.toString();
            }

            // 3. Upgrade Tenor MP4/low-res URLs -> Full-HD .GIF (AAAAC)
            if (currentHost.includes("tenor.com")) {
                const cleanPath = parsed.pathname.replace(/^\/m\//, "/");
                const tenorMatch = cleanPath.match(/^\/([A-Za-z0-9_-]+?)AAA[A-Za-z0-9_-]{2}\/([^/]+?)\.(mp4|webm|webp|gif)$/i);
                if (tenorMatch) {
                    parsed.hostname = "media.tenor.com";
                    parsed.pathname = `/${tenorMatch[1]}AAAAC/${tenorMatch[2]}.gif`;
                    parsed.search = "";
                    return parsed.toString();
                }
                if (/\.mp4$/i.test(cleanPath)) {
                    parsed.pathname = cleanPath.replace(/\.mp4$/i, ".gif");
                    parsed.search = "";
                    return parsed.toString();
                }
            }

            // 4. Upgrade Giphy MP4 -> Full-HD .GIF
            if (currentHost.includes("giphy.com")) {
                if (/\/(?:giphy|200w?|100w?|giphy-preview|giphy-downsized[^/]*)\.mp4$/i.test(parsed.pathname)) {
                    parsed.pathname = parsed.pathname.replace(
                        /\/(?:giphy|200w?|100w?|giphy-preview|giphy-downsized[^/]*)\.mp4$/i,
                        "/giphy.gif"
                    );
                    parsed.search = "";
                    return parsed.toString();
                }
                if (/\.mp4$/i.test(parsed.pathname)) {
                    parsed.pathname = parsed.pathname.replace(/\.mp4$/i, ".gif");
                    parsed.search = "";
                    return parsed.toString();
                }
            }

            // 5. Upgrade Imgur .mp4 / .gifv -> .gif
            if (currentHost.includes("imgur.com") && /\.(mp4|gifv)$/i.test(parsed.pathname)) {
                parsed.pathname = parsed.pathname.replace(/\.(mp4|gifv)$/i, ".gif");
                parsed.search = "";
                return parsed.toString();
            }

            return parsed.toString();
        } catch {
            return url;
        }
    }

    function getAttachmentBaseKey(url) {
        try { return new URL(url).pathname; } catch { return url; }
    }

    // =========================================================================
    // 5. AUTO-REFRESH EXPIRING DISCORD CDN URLS
    // =========================================================================
    async function refreshDiscordAttachmentUrls(urls) {
        const refreshedMap = new Map();
        if (!urls.length) return refreshedMap;

        const BATCH_SIZE = 50;
        console.log(`%c[GIF JSON Exporter] Refreshing ${urls.length} expiring Discord CDN links...`, "color:#FEE75C;");

        for (let i = 0; i < urls.length; i += BATCH_SIZE) {
            const batch = urls.slice(i, i + BATCH_SIZE);
            try {
                let resData = null;
                if (restApi?.post) {
                    const res = await restApi.post({
                        url: "/attachments/refresh-urls",
                        body: { attachment_urls: batch }
                    });
                    resData = res?.body || res;
                } else if (token) {
                    const resp = await fetch("/api/v9/attachments/refresh-urls", {
                        method: "POST",
                        headers: { "Authorization": token, "Content-Type": "application/json" },
                        body: JSON.stringify({ attachment_urls: batch })
                    });
                    if (resp.ok) resData = await resp.json();
                }

                if (resData?.refreshed_urls) {
                    for (const item of resData.refreshed_urls) {
                        if (item.original && item.refreshed) {
                            const cleanRefreshed = getHighQualityGifUrl(item.refreshed);
                            refreshedMap.set(item.original, cleanRefreshed);
                            refreshedMap.set(getAttachmentBaseKey(item.original), cleanRefreshed);
                        }
                    }
                }
            } catch (err) {
                console.warn(`[GIF JSON Exporter] Batch refresh warning (${i}-${i + batch.length}):`, err);
            }
        }
        return refreshedMap;
    }

    const urlsToRefreshSet = new Set();
    for (const [key, val] of Object.entries(rawGifs)) {
        const src = val?.src || key;
        if (isDiscordCdnAttachment(src)) urlsToRefreshSet.add(getHighQualityGifUrl(src));
        if (isDiscordCdnAttachment(key)) urlsToRefreshSet.add(getHighQualityGifUrl(key));
    }

    const refreshedUrlsMap = await refreshDiscordAttachmentUrls([...urlsToRefreshSet]);

    // =========================================================================
    // 6. BUILD CLEAN `src`-ONLY LIST & DOWNLOAD .JSON IMMEDIATELY
    // =========================================================================
    const sortedEntries = Object.entries(rawGifs).sort(
        ([, a], [, b]) => (a?.order ?? 999999) - (b?.order ?? 999999)
    );

    const srcList = [];

    for (const [key, val] of sortedEntries) {
        const rawSrc = val?.src || key;
        const isDiscord = isDiscordCdnAttachment(rawSrc) || isDiscordCdnAttachment(key);

        let bestSrc = rawSrc;
        if (isDiscord) {
            let candidateUrl = isDiscordCdnAttachment(rawSrc) ? rawSrc : key;
            if (/\.gif(\?|$)/i.test(key) && /\.mp4(\?|$)/i.test(rawSrc) && isDiscordCdnAttachment(key)) {
                candidateUrl = key;
            }
            const cleanCandidate = getHighQualityGifUrl(candidateUrl);
            bestSrc =
                refreshedUrlsMap.get(cleanCandidate) ||
                refreshedUrlsMap.get(getAttachmentBaseKey(cleanCandidate)) ||
                cleanCandidate;
        } else {
            bestSrc = getHighQualityGifUrl(rawSrc);
            if (/\.mp4(\?|$)/i.test(bestSrc) && /\.gif(\?|$)/i.test(key)) {
                bestSrc = getHighQualityGifUrl(key);
            }
        }

        srcList.push(bestSrc);
    }

    // Trigger JSON download containing ONLY the array of `src` URLs
    const jsonString = JSON.stringify(srcList, null, 2);
    const blob = new Blob([jsonString], { type: "application/json" });
    const blobUrl = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = blobUrl;
    a.download = "discord-favorite-gifs-fresh.json";
    document.body.appendChild(a);
    a.click();
    a.remove();
    setTimeout(() => URL.revokeObjectURL(blobUrl), 10000);

    console.log(
        `%c[GIF JSON Exporter] ✓ Exported ${srcList.length} high-quality GIF src URLs to discord-favorite-gifs-fresh.json!`,
        "color:#57F287;font-weight:bold;font-size:14px;"
    );
})();
```

</details>

---
### Tool 2: Discord Animated Link Optimizer & ZIP Exporter (Bookmarklet)


Scrapes any opened `.json` or text file containing GIF/WebP URLs in your browser, resizes each image to **128x128**, recompresses/crushes heavy frames to strictly comply with Discord's **<256 KB upload limit**, and compiles everything into a single `discord_gifs_webp.zip` file.

#### How to Use:
1. Open any raw `.json` file containing image links (such as `emojis.json` or `discord-favorite-gifs-fresh.json`) directly in a browser tab.
2. Either create a browser bookmark and paste the snippet below into the **URL** field, or paste the snippet directly into the DevTools Console.
3. The on-screen overlay will indicate downloading, crushing/compressing tiers, and final ZIP packaging.

#### Bookmarklet Script (`Discord_Links to Animated(Bookmark).js`):
<details>
<summary>Click to expand script</summary>

```javascript
javascript:(function(){var P=String.fromCharCode(37);var old=document.getElementById('pi7ModalOverlay');if(old)old.remove();var rawText=document.body.innerText.replace(/\\\//g,'/');var regex=/https?:\/\/[^"'\s<>\\]+?\.(?:gif|webp)(?:\?[^"'\s<>\\]*)?/gi;var matches=rawText.match(regex);var urls=matches?[...new Set(matches)]:[];var ov=document.createElement('div');ov.id='pi7ModalOverlay';ov.style='position:fixed;top:0;left:0;right:0;bottom:0;background:rgba(0,0,0,0.65);z-index:999999;display:flex;align-items:center;justify-content:center;font-family:system-ui,-apple-system,sans-serif;color:rgb(33,37,41);';var card=document.createElement('div');card.style='background:rgb(255,255,255);width:550px;max-height:92vh;overflow-y:auto;border-radius:10px;padding:22px 26px;box-shadow:0 10px 30px rgba(0,0,0,0.4);font-size:14px;line-height:1.4;';card.innerHTML='<div style="display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid rgb(225,228,232);padding-bottom:12px;margin-bottom:14px;"><div style="font-size:17px;font-weight:700;color:rgb(45,55,115);">Discord GIF / WebP Batch Optimizer</div><div style="background:rgb(232,240,254);color:rgb(26,115,232);padding:4px 10px;border-radius:12px;font-size:12px;font-weight:600;">Found '+urls.length+' links</div></div><div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid rgb(240,240,240);"><label style="font-weight:500;">Compression level</label><select id="pi7Preset" style="padding:6px 10px;border:1px solid rgb(206,212,218);border-radius:5px;font-size:13px;background:rgb(255,255,255);"><option value="high" selected>High Compression (Small File Size)</option><option value="discord">Discord 128x128 Crisp (Balanced)</option><option value="extreme">Extreme Crusher (For 4MB-30MB GIFs)</option><option value="low">Low Compression (Max Quality)</option><option value="custom">Custom Settings</option></select></div><div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid rgb(240,240,240);"><label style="font-weight:500;">Show Advance Settings</label><input type="checkbox" id="pi7ShowAdv" checked style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;margin-bottom:10px;"><div style="padding-right:10px;"><div style="font-weight:500;">Fast mode (use all CPU cores)</div><div style="font-size:11px;color:rgb(108,117,125);">2-3x faster on large GIFs. Turn it off if you want the smallest possible file.</div></div><input type="checkbox" id="pi7FastMode" style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div id="pi7AdvBox" style="border:1px solid rgb(222,226,230);border-radius:8px;padding:14px 16px;background:rgb(252,253,254);"><div style="display:flex;justify-content:space-between;align-items:center;padding-bottom:10px;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Lossy Compression</div><div style="font-size:11px;color:rgb(108,117,125);">Up to 8000. Recommended range is 0-500.</div></div><input type="number" id="pi7Lossy" value="59" min="0" max="8000" style="width:85px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Reduce Colors In GIF</div><div style="font-size:11px;color:rgb(108,117,125);">256 colours max. 140-200 keeps files small.</div></div><input type="number" id="pi7Colors" value="140" min="2" max="256" style="width:85px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Scale GIF (Base: Discord 128x128)</div><div id="pi7ScaleText" style="font-size:11px;color:rgb(108,117,125);">Base size is <b>128 X 128</b>. After scaling at <b>74'+P+'</b> width & height will be <b>95 X 95</b></div></div><div style="display:flex;align-items:center;gap:6px;"><input type="number" id="pi7Scale" value="74" min="15" max="100" style="width:75px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"><span>'+P+'</span></div></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div style="font-weight:500;">Skip Frames</div><input type="checkbox" id="pi7SkipChk" checked style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Remove Every</div><div id="pi7SkipText" style="font-size:11px;color:rgb(108,117,125);">Remove Every <b>4th</b> frame from GIF.<br>Currently GIF have <span style="color:rgb(40,167,69);font-weight:700;">100</span> frames. After skipping there will be <span style="color:rgb(220,53,69);font-weight:700;">75</span> frames in gif.</div></div><div style="display:flex;align-items:center;gap:6px;"><input type="number" id="pi7SkipN" value="4" min="2" max="20" style="width:70px;padding:5px 8px;border:1px solid rgb(206,212,218);border-radius:4px;"><span style="font-size:12px;color:rgb(108,117,125);">Nth Frame</span></div></div><div style="display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid rgb(238,238,238);"><div><div style="font-weight:500;">Add Delay In Frames</div><div style="font-size:11px;color:rgb(108,117,125);">Slows a fast GIF by adding delay where frames were removed.</div></div><input type="checkbox" id="pi7AddDelay" style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div><div style="display:flex;justify-content:space-between;align-items:center;padding-top:10px;"><div><div style="font-weight:500;color:rgb(45,55,115);">Extra Auto-Clamp If Still >256KB</div><div style="font-size:11px;color:rgb(108,117,125);">Leave unchecked to use ONLY your exact settings above without overrides.</div></div><input type="checkbox" id="pi7Auto256" style="width:16px;height:16px;accent-color:rgb(13,110,253);cursor:pointer;"></div></div><div id="pi7StatusArea" style="display:none;margin-top:14px;padding:10px 14px;border-radius:6px;background:rgb(241,243,245);font-weight:600;font-size:13px;text-align:center;color:rgb(33,37,41);">Ready</div><div style="display:flex;justify-content:center;gap:12px;margin-top:16px;"><button id="pi7StartBtn" style="background:rgb(59,89,152);color:rgb(255,255,255);border:none;padding:10px 24px;border-radius:5px;font-weight:600;font-size:14px;cursor:pointer;">Optimize GIF & Download ZIP</button><button id="pi7CloseBtn" style="background:rgb(222,226,230);color:rgb(52,58,64);border:none;padding:10px 16px;border-radius:5px;font-weight:600;font-size:14px;cursor:pointer;">Close</button></div>';ov.appendChild(card);document.body.appendChild(ov);var elPreset=document.getElementById('pi7Preset'),elShowAdv=document.getElementById('pi7ShowAdv'),elAdvBox=document.getElementById('pi7AdvBox'),elFast=document.getElementById('pi7FastMode'),elLossy=document.getElementById('pi7Lossy'),elColors=document.getElementById('pi7Colors'),elScale=document.getElementById('pi7Scale'),elScaleTxt=document.getElementById('pi7ScaleText'),elSkipChk=document.getElementById('pi7SkipChk'),elSkipN=document.getElementById('pi7SkipN'),elSkipTxt=document.getElementById('pi7SkipText'),elAddDelay=document.getElementById('pi7AddDelay'),elAuto256=document.getElementById('pi7Auto256'),elStatus=document.getElementById('pi7StatusArea'),elStart=document.getElementById('pi7StartBtn'),elClose=document.getElementById('pi7CloseBtn');function updLabels(setCustom){if(setCustom)elPreset.value='custom';var sc=Math.max(15,Math.min(100,parseInt(elScale.value)||100));var px=Math.max(16,Math.round(128*sc/100));elScaleTxt.innerHTML='Base size is <b>128 X 128</b>. After scaling at <b>'+sc+P+'</b> width & height will be <b>'+px+' X '+px+'</b>';var n=Math.max(2,Math.min(20,parseInt(elSkipN.value)||4));var kept=elSkipChk.checked?(100-Math.floor(100/n)):100;var suf=n===2?'nd':(n===3?'rd':'th');elSkipTxt.innerHTML='Remove Every <b>'+n+suf+'</b> frame from GIF.<br>Currently GIF have <span style="color:rgb(40,167,69);font-weight:700;">100</span> frames. After skipping there will be <span style="color:rgb(220,53,69);font-weight:700;">'+kept+'</span> frames in gif.';}elLossy.oninput=function(){updLabels(true);};elColors.oninput=function(){updLabels(true);};elScale.oninput=function(){updLabels(true);};elSkipN.oninput=function(){updLabels(true);};elSkipChk.onchange=function(){updLabels(true);};elAddDelay.onchange=function(){updLabels(true);};elShowAdv.onchange=function(){elAdvBox.style.display=elShowAdv.checked?'block':'none';};elPreset.onchange=function(){var v=elPreset.value;if(v==='high'){elLossy.value=59;elColors.value=140;elScale.value=74;elSkipChk.checked=true;elSkipN.value=4;elAddDelay.checked=false;}else if(v==='discord'){elLossy.value=45;elColors.value=180;elScale.value=100;elSkipChk.checked=true;elSkipN.value=4;elAddDelay.checked=false;}else if(v==='extreme'){elLossy.value=160;elColors.value=96;elScale.value=74;elSkipChk.checked=true;elSkipN.value=2;elAddDelay.checked=false;}else if(v==='low'){elLossy.value=15;elColors.value=256;elScale.value=100;elSkipChk.checked=false;elSkipN.value=5;elAddDelay.checked=false;}updLabels(false);};elClose.onclick=function(){ov.remove();};function isMult(a,b){return(a-Math.floor(a/b)*b)===0;}function decodeGIF89(u8,targetW,targetH){if(u8.length<13||u8[0]!==71||u8[1]!==73||u8[2]!==70)return null;var pos=6,lsW=u8[pos]|(u8[pos+1]<<8),lsH=u8[pos+2]|(u8[pos+3]<<8),packed=u8[pos+4];pos+=7;var gct=null;if(packed&128){var gSize=2<<(packed&7);gct=u8.subarray(pos,pos+gSize*3);pos+=gSize*3;}var canv=new Uint8ClampedArray(lsW*lsH*4),prevCanv=new Uint8ClampedArray(lsW*lsH*4),frames=[];var delayCs=4,dispose=0,transFlag=0,transIdx=0;var needScale=(lsW!==targetW||lsH!==targetH),sCanv=null,sCtx=null,tCanv=null,tCtx=null;if(needScale){sCanv=document.createElement('canvas');sCanv.width=lsW;sCanv.height=lsH;sCtx=sCanv.getContext('2d');tCanv=document.createElement('canvas');tCanv.width=targetW;tCanv.height=targetH;tCtx=tCanv.getContext('2d',{willReadFrequently:true});tCtx.imageSmoothingEnabled=true;tCtx.imageSmoothingQuality='high';}var prefix=new Int32Array(4096),suffix=new Uint8Array(4096),pixelStack=new Uint8Array(4097);while(pos<u8.length){var b=u8[pos++];if(b===59)break;if(b===33){var label=u8[pos++];if(label===249){var bSize=u8[pos++],gP=u8[pos];dispose=(gP>>2)&7;transFlag=gP&1;delayCs=u8[pos+1]|(u8[pos+2]<<8);transIdx=u8[pos+3];pos+=bSize+1;}else{while(pos<u8.length){var sL=u8[pos++];if(sL===0)break;pos+=sL;}}}else if(b===44){var fx=u8[pos]|(u8[pos+1]<<8),fy=u8[pos+2]|(u8[pos+3]<<8),fw=u8[pos+4]|(u8[pos+5]<<8),fh=u8[pos+6]|(u8[pos+7]<<8),iP=u8[pos+8];pos+=9;var ct=gct;if(iP&128){var lSize=2<<(iP&7);ct=u8.subarray(pos,pos+lSize*3);pos+=lSize*3;}var interlace=(iP&64)!==0,minCodeSize=u8[pos++],chunks=[],totLzw=0;while(pos<u8.length){var cL=u8[pos++];if(cL===0)break;chunks.push(u8.subarray(pos,pos+cL));totLzw+=cL;pos+=cL;}var lzwData=new Uint8Array(totLzw),off=0;for(var c=0;c<chunks.length;c++){lzwData.set(chunks[c],off);off+=chunks[c].length;}var nPix=fw*fh,pixels=new Uint8Array(nPix),clearCode=1<<minCodeSize,eoiCode=clearCode+1,codeSize=minCodeSize+1,codeMask=(1<<codeSize)-1,avail=clearCode+2,oldCode=-1,first=0;for(var k=0;k<clearCode;k++)suffix[k]=k;var datum=0,bits=0,pi=0,top=0,bi=0;while(pi<nPix){if(top===0){if(bits<codeSize){if(bi>=totLzw)break;datum|=lzwData[bi++]<<bits;bits+=8;continue;}var code=datum&codeMask;datum>>=codeSize;bits-=codeSize;if(code>avail||code===eoiCode)break;if(code===clearCode){codeSize=minCodeSize+1;codeMask=(1<<codeSize)-1;avail=clearCode+2;oldCode=-1;continue;}if(oldCode===-1){pixelStack[top++]=suffix[code];oldCode=code;first=code;continue;}var inCode=code;if(code===avail){pixelStack[top++]=first;code=oldCode;}while(code>clearCode){pixelStack[top++]=suffix[code];code=prefix[code];}first=suffix[code];pixelStack[top++]=first;if(avail<4096){prefix[avail]=oldCode;suffix[avail]=first;avail++;if((avail&codeMask)===0&&avail<4096){codeSize++;codeMask+=avail;}}oldCode=inCode;}top--;pixels[pi++]=pixelStack[top];}if(dispose===3)prevCanv.set(canv);var pass=0,inc=8,py=0;for(var y=0;y<fh;y++){var drawY=interlace?py:y;if(interlace){py+=inc;if(py>=fh){pass++;if(pass===1){py=4;inc=8;}else if(pass===2){py=2;inc=4;}else{py=1;inc=2;}}}var cy=fy+drawY;if(cy<0||cy>=lsH)continue;var srcR=y*fw,dstR=cy*lsW;for(var x=0;x<fw;x++){var cx=fx+x;if(cx<0||cx>=lsW)continue;var idx=pixels[srcR+x];if(transFlag&&idx===transIdx)continue;var cOff=idx*3,dOff=(dstR+cx)*4;if(ct&&cOff+2<ct.length){canv[dOff]=ct[cOff];canv[dOff+1]=ct[cOff+1];canv[dOff+2]=ct[cOff+2];canv[dOff+3]=255;}}}var fData;if(!needScale){fData=new Uint8ClampedArray(canv);}else{sCtx.putImageData(new ImageData(new Uint8ClampedArray(canv),lsW,lsH),0,0);tCtx.clearRect(0,0,targetW,targetH);var sc=Math.min(targetW/lsW,targetH/lsH),dw=Math.round(lsW*sc),dh=Math.round(lsH*sc);tCtx.drawImage(sCanv,Math.round((targetW-dw)/2),Math.round((targetH-dh)/2),dw,dh);fData=tCtx.getImageData(0,0,targetW,targetH).data;}frames.push({data:fData,ms:(delayCs<=1?4:delayCs)*10});if(dispose===2){for(var y=0;y<fh;y++){var cy2=fy+y;if(cy2<0||cy2>=lsH)continue;var dstR2=cy2*lsW;for(var x=0;x<fw;x++){var cx2=fx+x;if(cx2<0||cx2>=lsW)continue;var dOff2=(dstR2+cx2)*4;canv[dOff2]=0;canv[dOff2+1]=0;canv[dOff2+2]=0;canv[dOff2+3]=0;}}}else if(dispose===3){canv.set(prevCanv);}delayCs=4;dispose=0;transFlag=0;}else{break;}}return frames.length>0?frames:null;}function encodeGIF(frames,w,h,maxActualColors,lossyVal){var colorBits=maxActualColors<=4?2:(maxActualColors<=8?3:(maxActualColors<=16?4:(maxActualColors<=32?5:(maxActualColors<=64?6:(maxActualColors<=128?7:8)))));var tableSlots=1<<colorBits,targetCols=Math.min(tableSlots,Math.max(2,maxActualColors));var deltaSq=lossyVal*22,lzwSq=lossyVal*18,spatialSq=lossyVal*10;var hist=new Int32Array(32768),sumR=new Int32Array(32768),sumG=new Int32Array(32768),sumB=new Int32Array(32768);for(var f=0;f<frames.length;f++){var d=frames[f].data;for(var p=0;p<d.length;p+=4){if(d[p+3]>=128){var c=((d[p]>>3)<<10)|((d[p+1]>>3)<<5)|(d[p+2]>>3);hist[c]++;sumR[c]+=d[p];sumG[c]+=d[p+1];sumB[c]+=d[p+2];}}}var active=[],cR=new Uint8Array(32768),cG=new Uint8Array(32768),cB=new Uint8Array(32768);for(var c=0;c<32768;c++){var cnt=hist[c];if(cnt>0){active.push(c);cR[c]=(sumR[c]/cnt)|0;cG[c]=(sumG[c]/cnt)|0;cB[c]=(sumB[c]/cnt)|0;}}function makeBox(list){var rMin=255,rMax=0,gMin=255,gMax=0,bMin=255,bMax=0,tot=0;for(var i=0;i<list.length;i++){var c=list[i],w2=hist[c],r=cR[c],g=cG[c],b=cB[c];tot+=w2;if(r<rMin)rMin=r;if(r>rMax)rMax=r;if(g<gMin)gMin=g;if(g>gMax)gMax=g;if(b<bMin)bMin=b;if(b>bMax)bMax=b;}var dr=(rMax-rMin)*2,dg=(gMax-gMin)*3,db=(bMax-bMin)*1.5,span=Math.max(dr,dg,db);var axis=dr>=dg&&dr>=db?0:(dg>=db?1:2);return{list:list,tot:tot,axis:axis,score:list.length>1?span*Math.pow(tot,0.45):0};}var boxes=active.length>0?[makeBox(active)]:[];while(boxes.length<targetCols-1){var bestIdx=-1,bestScore=0;for(var i=0;i<boxes.length;i++){if(boxes[i].score>bestScore){bestScore=boxes[i].score;bestIdx=i;}}if(bestIdx===-1)break;var bx=boxes[bestIdx],ax=bx.axis;bx.list.sort(function(a,b){return ax===0?cR[a]-cR[b]:(ax===1?cG[a]-cG[b]:cB[a]-cB[b]);});var half=bx.tot/2,acc=0,mid=1;for(var i=0;i<bx.list.length-1;i++){acc+=hist[bx.list[i]];if(acc>=half){mid=i+1;break;}}boxes[bestIdx]=makeBox(bx.list.slice(0,mid));boxes.push(makeBox(bx.list.slice(mid)));}var pal=[[0,0,0]];for(var i=0;i<boxes.length;i++){var bl=boxes[i].list,tr=0,tg=0,tb=0,tc=0;for(var j=0;j<bl.length;j++){var c=bl[j];tr+=sumR[c];tg+=sumG[c];tb+=sumB[c];tc+=hist[c];}pal.push(tc>0?[(tr/tc)|0,(tg/tc)|0,(tb/tc)|0]:[0,0,0]);}var actualLen=pal.length;while(pal.length<tableSlots)pal.push([0,0,0]);var pDistSq=new Int32Array(tableSlots*tableSlots);for(var i=1;i<actualLen;i++){for(var j=1;j<actualLen;j++){var drP=pal[i][0]-pal[j][0],dgP=pal[i][1]-pal[j][1],dbP=pal[i][2]-pal[j][2];pDistSq[(i<<colorBits)|j]=drP*drP*2+dgP*dgP*3+dbP*dbP;}}var lut=new Uint8Array(32768);for(var i=0;i<active.length;i++){var c=active[i],r=cR[c],g=cG[c],b=cB[c],bestP=1,bestD=1e9;for(var p=1;p<actualLen;p++){var dr=r-pal[p][0],dg=g-pal[p][1],db=b-pal[p][2],d2=dr*dr*2+dg*dg*3+db*db;if(d2<bestD){bestD=d2;bestP=p;if(d2===0)break;}}lut[c]=bestP;}var nPix=w*h,fRect=[],disp=new Uint8Array(frames.length),canvI=new Uint8Array(nPix),canvR=new Uint8Array(nPix),canvG=new Uint8Array(nPix),canvB=new Uint8Array(nPix);for(var f=0;f<frames.length;f++){var d=frames[f].data,rawI=new Uint8Array(nPix),canKeep=f>0;for(var p=0;p<nPix;p++){var p4=p*4;if(d[p4+3]<128){rawI[p]=0;if(canvI[p]!==0)canKeep=false;}else{rawI[p]=lut[((d[p4]>>3)<<10)|((d[p4+1]>>3)<<5)|(d[p4+2]>>3)];}}if(canKeep){disp[f-1]=1;var minX=w,minY=h,maxX=-1,maxY=-1,fullI=new Uint8Array(nPix);for(var y=0;y<h;y++){var row=y*w;for(var x=0;x<w;x++){var p=row+x,idx=rawI[p],p4=p*4;var drC=d[p4]-canvR[p],dgC=d[p4+1]-canvG[p],dbC=d[p4+2]-canvB[p];var cdSq=drC*drC*2+dgC*dgC*3+dbC*dbC;if(canvI[p]===idx||(canvI[p]!==0&&idx!==0&&cdSq<=deltaSq)){fullI[p]=0;}else{if(x>0&&fullI[p-1]!==0&&idx!==0){var prevIdx=fullI[p-1];if(pDistSq[(idx<<colorBits)|prevIdx]<=spatialSq)idx=prevIdx;}fullI[p]=idx;canvI[p]=idx;canvR[p]=pal[idx][0];canvG[p]=pal[idx][1];canvB[p]=pal[idx][2];if(x<minX)minX=x;if(x>maxX)maxX=x;if(y<minY)minY=y;if(y>maxY)maxY=y;}}}if(maxX<minX){fRect.push({x:0,y:0,w:1,h:1,data:new Uint8Array([0])});}else{var bw=maxX-minX+1,bh=maxY-minY+1,subI=new Uint8Array(bw*bh),dst=0;for(var y=minY;y<=maxY;y++){var row=y*w;for(var x=minX;x<=maxX;x++)subI[dst++]=fullI[row+x];}fRect.push({x:minX,y:minY,w:bw,h:bh,data:subI});}}else{if(f>0)disp[f-1]=2;for(var p=0;p<nPix;p++){var idx=rawI[p];if(p>0&&rawI[p-1]!==0&&idx!==0&&pDistSq[(idx<<colorBits)|rawI[p-1]]<=spatialSq){idx=rawI[p-1];rawI[p]=idx;}canvI[p]=idx;if(idx>0){canvR[p]=pal[idx][0];canvG[p]=pal[idx][1];canvB[p]=pal[idx][2];}}fRect.push({x:0,y:0,w:w,h:h,data:rawI});}}disp[frames.length-1]=2;var out=[71,73,70,56,57,97,w&255,w>>8,h&255,h>>8,0x80|((colorBits-1)<<4)|(colorBits-1),0,0];for(var i=0;i<tableSlots;i++){var pt=pal[i];out.push(pt[0],pt[1],pt[2]);}out.push(33,255,11,78,69,84,83,67,65,80,69,50,46,48,3,1,0,0,0);var table=new Int32Array(4096<<colorBits),head=new Int16Array(4096),next=new Int16Array(4096),sym=new Uint8Array(4096);for(var f=0;f<frames.length;f++){var delayCs=frames[f].cs,pk=(disp[f]<<2)|1,rc=fRect[f];out.push(33,249,4,pk,delayCs&255,delayCs>>8,0,0,44,rc.x&255,rc.x>>8,rc.y&255,rc.y>>8,rc.w&255,rc.w>>8,rc.h&255,rc.h>>8,0);var minCode=Math.max(2,colorBits);out.push(minCode);var clearCode=1<<minCode,eoiCode=clearCode+1,nextCode=eoiCode+1,codeSize=minCode+1;table.fill(-1);head.fill(-1);var sub=[],bitBuf=0,bitCnt=0;var emit=function(code){bitBuf|=code<<bitCnt;bitCnt+=codeSize;while(bitCnt>=8){sub.push(bitBuf&255);bitBuf>>=8;bitCnt-=8;if(sub.length===255){out.push(255);for(var s=0;s<255;s++)out.push(sub[s]);sub.length=0;}}};emit(clearCode);var idxArr=rc.data,prefix=idxArr[0],rLen=idxArr.length;for(var p=1;p<rLen;p++){var k=idxArr[p],key=(prefix<<colorBits)|k,hit=table[key];if(hit===-1&&lzwSq>0&&k!==0){var bestNode=-1,bestErr=lzwSq;for(var nd=head[prefix];nd!==-1;nd=next[nd]){var cSym=sym[nd];if(cSym!==0){var dSq=pDistSq[(k<<colorBits)|cSym];if(dSq<=bestErr){bestErr=dSq;bestNode=nd;}}}if(bestNode!==-1)hit=bestNode;}if(hit!==-1){prefix=hit;}else{emit(prefix);if(nextCode<4096){if(nextCode===(1<<codeSize)&&codeSize<12)codeSize++;sym[nextCode]=k;next[nextCode]=head[prefix];head[prefix]=nextCode;table[key]=nextCode++;}else{emit(clearCode);table.fill(-1);head.fill(-1);codeSize=minCode+1;nextCode=eoiCode+1;}prefix=k;}}emit(prefix);emit(eoiCode);if(bitCnt>0)sub.push(bitBuf&255);if(sub.length>0){out.push(sub.length);for(var s=0;s<sub.length;s++)out.push(sub[s]);}out.push(0);}out.push(59);return new Uint8Array(out);}async function processOne(url,idx,tot,cfg){var baseW=Math.max(16,Math.round(128*cfg.scale/100));var srcBlob=null;try{var rUrl='https://wsrv.nl/?url='+encodeURIComponent(url)+'&w='+baseW+'&h='+baseW+'&fit=contain&n=-1&output=gif';var resp=await fetch(rUrl);if(resp.ok)srcBlob=await resp.blob();}catch(e){}if(!srcBlob){try{var r2=await fetch(url);if(r2.ok)srcBlob=await r2.blob();}catch(e){}if(!srcBlob){var r3=await fetch('https://corsproxy.io/?'+encodeURIComponent(url));if(r3.ok)srcBlob=await r3.blob();}}if(!srcBlob)throw new Error('Download failed');var buf=await srcBlob.arrayBuffer();var u8=new Uint8Array(buf);var raw=decodeGIF89(u8,baseW,baseW);if(!raw&&window.ImageDecoder){var hdr=String.fromCharCode.apply(null,u8.subarray(0,12));var mime=hdr.startsWith('GIF')?'image/gif':(hdr.slice(8,12)==='WEBP'?'image/webp':'image/png');var dec=new ImageDecoder({data:buf,type:mime});await dec.tracks.ready;var total=dec.tracks.selectedTrack.frameCount||1;raw=[];var c=document.createElement('canvas');c.width=baseW;c.height=baseW;var ctx=c.getContext('2d',{willReadFrequently:true});for(var i=0;i<total;i++){var fr=await dec.decode({frameIndex:i}),vf=fr.image;var d=vf.duration?vf.duration/1000:40;if(d<=10)d=33;ctx.clearRect(0,0,baseW,baseW);var vw=vf.displayWidth||vf.codedWidth||baseW,vh=vf.displayHeight||vf.codedHeight||baseW;var sc=Math.min(baseW/vw,baseW/vh),dw=Math.round(vw*sc),dh=Math.round(vh*sc);ctx.drawImage(vf,Math.round((baseW-dw)/2),Math.round((baseW-dh)/2),dw,dh);raw.push({data:ctx.getImageData(0,0,baseW,baseW).data,ms:d});vf.close();}dec.close();}if(!raw||raw.length===0)return{blob:srcBlob,ext:'gif',origF:1,keptF:1};for(var f=0;f<raw.length;f++){var px=raw[f].data;for(var p=0;p<px.length;p+=4){var a=px[p+3];if(a>=95&&a<250){px[p]=((px[p]*a+49*(255-a))/255)|0;px[p+1]=((px[p+1]*a+51*(255-a))/255)|0;px[p+2]=((px[p+2]*a+56*(255-a))/255)|0;px[p+3]=255;}else if(a<95){px[p+3]=0;}}}var totalMs=0;for(var i=0;i<raw.length;i++)totalMs+=raw[i].ms;var stepFrames=[];for(var i=0;i<raw.length;i++){if(cfg.skip&&raw.length>2&&isMult(i+1,cfg.nth))continue;stepFrames.push({data:raw[i].data,ms:raw[i].ms});}if(stepFrames.length===0)stepFrames.push(raw[0]);var encInput=[];var uniformAddedCs=Math.max(2,Math.round((totalMs/stepFrames.length)/10));for(var j=0;j<stepFrames.length;j++){var fCs=cfg.addDelay?uniformAddedCs:Math.max(2,Math.round(stepFrames[j].ms/10));encInput.push({data:stepFrames[j].data,cs:fCs});}var lastBytes=encodeGIF(encInput,baseW,baseW,cfg.colors,cfg.lossy);if(cfg.auto256&&lastBytes.byteLength>255000){var boost=[{c:Math.min(cfg.colors,96),l:Math.max(cfg.lossy,180),st:1},{c:Math.min(cfg.colors,64),l:Math.max(cfg.lossy,350),st:2},{c:48,l:Math.max(cfg.lossy,600),st:2}];for(var b=0;b<boost.length;b++){var subF=[];for(var k=0;k<encInput.length;k+=boost[b].st)subF.push(encInput[k]);lastBytes=encodeGIF(subF,baseW,baseW,boost[b].c,boost[b].l);if(lastBytes.byteLength<=255000)break;}}return{blob:new Blob([lastBytes],{type:'image/gif'}),ext:'gif',origF:raw.length,keptF:stepFrames.length};}async function runBatch(){if(urls.length===0){elStatus.style.display='block';elStatus.innerText='No GIF or WEBP links found on this page!';return;}elStart.disabled=true;elStart.style.opacity='0.6';elStatus.style.display='block';elStatus.style.background='rgb(232,240,254)';elStatus.style.color='rgb(26,115,232)';var cfg={lossy:Math.max(0,parseInt(elLossy.value)||0),colors:Math.max(2,Math.min(256,parseInt(elColors.value)||140)),scale:Math.max(15,Math.min(100,parseInt(elScale.value)||100)),skip:elSkipChk.checked,nth:Math.max(2,parseInt(elSkipN.value)||4),addDelay:elAddDelay.checked,auto256:elAuto256.checked,fast:elFast.checked};var pxDim=Math.max(16,Math.round(128*cfg.scale/100));var zip=new JSZip(),done=0,ok=0,totalBytes=0,concurrency=cfg.fast?3:1,cursor=0;async function worker(){while(cursor<urls.length){var myIdx=cursor++;try{var res=await processOne(urls[myIdx],myIdx+1,urls.length,cfg);zip.file('discord_'+(myIdx+1)+'.'+res.ext,res.blob);totalBytes+=res.blob.size;ok++;done++;var kb=(res.blob.size/1024).toFixed(1);elStatus.innerText='Optimized '+done+'/'+urls.length+' — '+pxDim+'x'+pxDim+'px | Frames: '+res.keptF+'/'+res.origF+' | Size: '+kb+' KB';}catch(e){console.error('Failed:',urls[myIdx],e);done++;}}}var workers=[];for(var w=0;w<concurrency;w++)workers.push(worker());await Promise.all(workers);if(ok===0){elStatus.style.background='rgb(248,215,218)';elStatus.style.color='rgb(114,28,36)';elStatus.innerText='Failed to download images.';elStart.disabled=false;elStart.style.opacity='1';return;}elStatus.innerText='Packing discord_gifs_webp.zip...';zip.generateAsync({type:'blob',compression:'DEFLATE',compressionOptions:{level:6}}).then(function(content){var a=document.createElement('a');a.href=URL.createObjectURL(content);a.download='discord_gifs_webp.zip';document.body.appendChild(a);a.click();document.body.removeChild(a);var avgKb=(totalBytes/ok/1024).toFixed(1);elStatus.style.background='rgb(212,237,218)';elStatus.style.color='rgb(21,87,36)';elStatus.innerText='Done! '+ok+' GIFs ('+pxDim+'x'+pxDim+'px, Avg: '+avgKb+' KB, Lossy: '+cfg.lossy+', Colors: '+cfg.colors+')';elStart.disabled=false;elStart.style.opacity='1';});}elStart.onclick=function(){if(window.JSZip){runBatch();return;}elStatus.style.display='block';elStatus.innerText='Loading JSZip Engine...';var script=document.createElement('script');script.src='https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js';script.onload=runBatch;script.onerror=function(){elStatus.innerText='Blocked! Save .json to PC, open in a new tab, and click bookmark.';};document.head.appendChild(script);};})();
```

</details>

---

### Tool 3: Server Emoji CDN Extractor & Exporter

Extracts all custom emojis from a server's emoji management screen, sets their resolution to a standard size (`256px`), preserves GIF animations, logs the URLs, copies them to your clipboard, and automatically downloads `emojis.json`.

#### How to Use:
1. Open Discord Web and navigate to **Server Settings** $\rightarrow$ **Emoji**.
2. Open DevTools Console (`Ctrl + Shift + I`).
3. Paste the code below and press Enter.

#### Script (`CDN Extractor(Discord_Emojis).js`):
<details>
<summary>Click to expand script</summary>

```javascript
(function exportEmojiUrls() {
    const urls = new Set();

    const formatEmojiUrl = (rawUrl) => {
        try {
            const url = new URL(rawUrl);
            const isAnimated = url.searchParams.get('animated') === 'true' || url.pathname.endsWith('.gif');
            
            if (isAnimated) {
                url.pathname = url.pathname.replace(/\.(webp|png)$/, '.gif');
            } else {
                url.pathname = url.pathname.replace(/\.webp$/, '.png');
            }

            url.search = '?size=256';
            return url.toString();
        } catch (e) {
            return rawUrl;
        }
    };

    // Scope to settings rows first so it doesn't grab random emojis from background chat
    const emojiRows = document.querySelectorAll('[class*="emojiRow"]');
    const containers = emojiRows.length > 0 ? emojiRows : [document];

    containers.forEach(container => {
        // 1. Check standard <img> tags
        container.querySelectorAll('img[src*="/emojis/"]').forEach(img => {
            urls.add(formatEmojiUrl(img.src));
        });

        // 2. Check background-image divs
        container.querySelectorAll('[style*="/emojis/"]').forEach(div => {
            const match = div.style.backgroundImage.match(/url\(["']?(.*?)["']?\)/);
            if (match && match[1]) {
                urls.add(formatEmojiUrl(match[1]));
            }
        });
    });

    const urlArray = Array.from(urls);

    if (urlArray.length === 0) {
        console.warn("⚠️ No emojis found! Make sure you are inside Server Settings > Emoji.");
        return;
    }

    // Format as a simple JSON array of strings
    const jsonOutput = JSON.stringify(urlArray, null, 2);

    // 1. Print URLs to console & copy to clipboard
    console.log(urlArray.join('\n'));
    if (typeof copy === 'function') copy(jsonOutput);

    // 2. Automatically download the emojis.json file
    const blob = new Blob([jsonOutput], { type: 'application/json' });
    const link = document.createElement('a');
    link.href = URL.createObjectURL(blob);
    link.download = 'emojis.json';
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    URL.revokeObjectURL(link.href);

    console.log(`%c✅ Exported ${urlArray.length} URLs to emojis.json (and copied to clipboard)!`, 'color: #43b581; font-weight: bold;');
})();
```

</details>

---

### Tool 4: Server Emoji Bulk Deletion Tool

Quickly purges all custom emojis from a server you own or manage. It extracts the authentication token from Discord's internal webpack chunk to issue direct REST `DELETE` API requests with automatic rate-limit handling (`HTTP 429`), falling back to simulated UI button clicks if necessary.

#### How to Use:
1. Open Discord Web and navigate to **Server Settings** $\rightarrow$ **Emoji** of the server you wish to clear.
2. Open DevTools Console (`Ctrl + Shift + I`).
3. Paste the code below and hit Enter. Confirm the browser dialog prompt.
4. *(Optional)* To abort deletion at any point, paste `window.stopDeleting = true` into the console.

#### Script (`Discord (Delete Emojis_FromCreatedServer).js`):
<details>
<summary>Click to expand script</summary>

```javascript
(async function clearAllServerEmojis() {
    const sleep = (ms) => new Promise(r => setTimeout(r, ms));

    // 1. Get Server ID from the URL
    const guildId = window.location.pathname.split('/')[2];

    // 2. Get Auth Token (to send real DELETE requests to Discord)
    let token = null;
    try {
        window.webpackChunkdiscord_app.push([[Symbol()], {}, req => {
            for (const mod of Object.values(req.c)) {
                const exp = mod?.exports;
                if (!exp) continue;
                for (const key of Reflect.ownKeys(exp)) {
                    try {
                        const val = exp[key];
                        if (val && typeof val.getToken === 'function') {
                            const t = val.getToken();
                            if (typeof t === 'string' && t.length > 20) token = t;
                        }
                    } catch {}
                }
            }
        }]);
        window.webpackChunkdiscord_app.pop();
    } catch {}

    if (!token) {
        try {
            const iframe = document.createElement('iframe');
            document.body.appendChild(iframe);
            token = JSON.parse(iframe.contentWindow.localStorage.token || 'null');
            iframe.remove();
        } catch {}
    }

    // 3. Collect all emojis in this server (ID, Name, and the exact right-side Delete button)
    const emojiMap = new Map();
    const rows = Array.from(document.querySelectorAll('[class*="emojiRow"]')).filter(
        row => !row.parentElement?.closest('[class*="emojiRow"]')
    );

    rows.forEach(row => {
        // Extract Emoji ID from background-image or img src
        let rawUrl = row.querySelector('img[src*="/emojis/"]')?.src || '';
        if (!rawUrl) {
            const bg = row.querySelector('[style*="/emojis/"]')?.style?.backgroundImage || '';
            const m = bg.match(/url\(["']?(.*?)["']?\)/);
            if (m) rawUrl = m[1];
        }

        const idMatch = rawUrl.match(/\/emojis\/(\d+)/);
        if (!idMatch) return;

        const id = idMatch[1];
        const name = row.querySelector('input')?.value || `emoji_${id}`;

        // IMPORTANT: Only select the remove button (never the first button which opens "Edit Emoji")
        const buttons = Array.from(row.querySelectorAll('button, [role="button"]'));
        const deleteBtn =
            row.querySelector('[class*="emojiRemove"]') ||
            (buttons.length > 1 ? buttons[buttons.length - 1] : null);

        if (!emojiMap.has(id)) {
            emojiMap.set(id, { id, name, deleteBtn });
        }
    });

    const emojis = Array.from(emojiMap.values());

    if (emojis.length === 0) {
        console.warn("⚠️ No emojis found! Make sure you are inside Server Settings > Emoji.");
        return;
    }

    if (!confirm(`⚠️ Are you sure you want to permanently delete ALL (${emojis.length}) emojis in this server?`)) {
        console.log("❌ Emoji deletion cancelled.");
        return;
    }

    console.log(
        `%c🗑️ Deleting ${emojis.length} emojis...\n%cTo stop at any time, paste: window.stopDeleting = true`,
        'color: #f04747; font-weight: bold; font-size: 13px;',
        'color: #faa61a;'
    );

    window.stopDeleting = false;
    let deletedCount = 0;

    for (let i = 0; i < emojis.length; i++) {
        if (window.stopDeleting) {
            console.log("🛑 Deletion stopped by user.");
            break;
        }

        const emoji = emojis[i];

        // Method A: Direct Discord API DELETE (fastest & never opens the Edit modal)
        if (token && guildId) {
            const res = await fetch(`https://discord.com/api/v9/guilds/${guildId}/emojis/${emoji.id}`, {
                method: 'DELETE',
                headers: { 'Authorization': token }
            });

            if (res.status === 204 || res.ok) {
                deletedCount++;
                console.log(`🗑️ [${deletedCount}/${emojis.length}] Deleted :${emoji.name}:`);
                await sleep(1500);
            } else if (res.status === 429) {
                const data = await res.json().catch(() => ({}));
                const waitMs = Math.ceil((data.retry_after || 4) * 1000) + 500;
                console.warn(`⏳ Rate limited by Discord! Waiting ${(waitMs / 1000).toFixed(1)}s...`);
                await sleep(waitMs);
                i--; // Retry this exact emoji
            } else {
                console.error(`❌ Failed to delete :${emoji.name}: (HTTP ${res.status})`);
            }
        }
        // Method B: Fallback to clicking ONLY the right-side remove button
        else if (emoji.deleteBtn) {
            emoji.deleteBtn.click();
            deletedCount++;
            console.log(`🗑️ [${deletedCount}/${emojis.length}] Deleted :${emoji.name}:`);
            await sleep(2000);
        }
    }

    console.log(`%c✅ Done! Deleted ${deletedCount}/${emojis.length} emojis.`, 'color: #43b581; font-weight: bold; font-size: 14px;');
})();
```

</details>

---

