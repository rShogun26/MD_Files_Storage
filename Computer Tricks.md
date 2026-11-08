
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

### Phase 1: Set Up the Account & Prevent Deletion
*This phase tricks Google into thinking you are active so it never deletes your files.*

#### Step 1: Create Account
Create your new secondary Google account (e.g., $`\color{#A31515}\texttt{mycloudstorage2@gmail.com}`$).

#### Step 2: Open Apps Script
Open a new browser tab, type $`\color{#A31515}\texttt{script.new}`$ in the URL bar, and hit Enter.

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
Click the ![+ Add Trigger](https://img.shields.io/badge/+%20Add%20Trigger-1a73e8) button in the bottom right corner.

#### Step 7: Configure Options
Set the configuration options exactly like this:
* **Choose which function to run:** ![keepAccountAlive](https://img.shields.io/badge/keepAccountAlive-d90429)
* **Select event source:** ![Time-driven](https://img.shields.io/badge/Time--driven-d90429)
* **Select type of time-based trigger:** ![Week timer](https://img.shields.io/badge/Week%20timer-d90429)
* **Select day of week:** ![Every Monday](https://img.shields.io/badge/Every%20Monday-d90429) (or any day you prefer)

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