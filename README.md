# 📦 idrive-xfer-utility

Logically maps Google Drive (using rclone) to copy selected files/folders to and from an IDrive using the native IDrive CLI. Requires zero IDrive S3 capabilities or local space. Optimized for a free GitHub Codespaces terminal.

A robust, data-autonomy migration utility optimized to run seamlessly inside a free **GitHub Codespaces** environment. 
This tool empowers users to bypass corporate data locks, 
streaming up to multi-terabyte libraries directly from Google Drive to IDrive Personal Backup with strict cryptographic verification and auto-managed storage constraints.
It allows you to set a codespace local transfer folder space limit for the transfer.
It also allows you to configure the files and/or folders you want copied.
A  Python program uses this information to chunk through the transfer, only holding and transferring batches at a time that do not exceed the local space limit.
The Python program provides full validation and custody control chaining by creating a log of the transferred files with their original md5 and their generated sh256 
This information is compared after copying the file to verify it was copied in tact

---

## 🚀 Key Features

*   **Zero-FUSE Architecture:** Bypasses standard container filesystem restrictions by streaming data directly via cloud APIs.
*   **Automated Storage Budgeting:** Enforces a configurable local storage ceiling, purging cached chunks immediately upon upload to keep your Codespace free and operational.
*   **Dual-Hash Forensic Integrity:** Automatically captures the source Google Drive MD5 and calculates a local SHA-256 hash to ensure a mathematically verified chain of custody.
*   **Fault-Tolerant Retry Loop:** Auto-detects in-transit byte modifications or dropped carriage returns, safely deleting corrupt files and retrying the stream up to 3 times.

---

## 🛠️ Phase 1: Generate Cloud Credentials

Because you are working in a headless cloud container, you must generate high-speed API keys and an authentication token manually in your local browser before initializing the workspace scripts.

### 🔹 Part A: Create High-Speed Private API Keys
To prevent speed throttling, do not use Rclone's public shared keys. Generate your own un-throttled desktop lane:
1. Open your browser and navigate to the official [Rclone Google Drive Client ID Guide](https://rclone.org/drive/#making-your-own-client-id).
2. Follow their step-by-step walkthrough to create a project in the Google Cloud Console, enable the Drive API, and generate an **OAuth Client ID** and **Client Secret** configured as a **Desktop App**.
3. Save these two text strings to a local scratchpad.

### 🔹 Part B: Capture Your Headless OAuth Tokens
Since Codespaces operates behind a proxy shield that blocks standard web authentication loops, use Google's official developer console to extract clean text keys:
1. Open a clean browser tab and navigate to:  
   `https://developers.google.com/oauthplayground/`
2. On the left sidebar panel (**Step 1: Select & authorize APIs**), scroll down to **Drive API v3** and click it to open the sub-menu.
3. Check the very first box: `https://www.googleapis.com/auth/drive`.
4. Click the blue **Authorize APIs** button.
5. Log into your target Google Account and click **Allow**.
6. The playground automatically moves to **Step 2** on the left sidebar. Click the blue button labeled **Exchange authorization code for tokens**.
7. Copy the exact, long alphanumeric text blocks displayed next to **Refresh token** and **Access token** to your scratchpad.

---

## ⚙️ Phase 2: Project Deployment

Launch this utility directly inside a free, standard GitHub Codespaces container mapped to your cloned repository.

### 🔹 Step 1: Initialize the Staging Environment
Open your local version of the cloned project within your active Codespace terminal. Open the configuration script file:
```bash
nano setup-rclone.sh
```

### 🔹 Step 2: Inject Your Scanned Parameters
Locate the placeholder configuration block inside `setup-rclone.sh` and update fields in <> exactly as indicated below. Remove the angle brackets, but ensure the double quotes around your access token string remain perfectly intact:

```ini
client_id = <YOUR_GOOGLE_CLOUD_CLIENT_ID_STRING>
client_secret = <YOUR_GOOGLE_CLOUD_CLIENT_SECRET_STRING>
token = {"access_token":"<YOUR_GOOGLE_PLAYGROUND_ACCESS_TOKEN_STRING>","token_type":"Bearer","refresh_token":"<YOUR_GOOGLE_PLAYGROUND_REFRESH_TOKEN_STRING>","expiry":"2026-05-10T12:00:00Z"}
```

Save and close the script file (`Ctrl+O`, `Enter`, `Ctrl+X`).

### 🔹 Step 3: Execute Environment Provisioning
Run the automated installation script to deploy Rclone dependencies, construct localized system configurations, and verify the background engine paths:
```bash
chmod +x setup-rclone.sh
./setup-rclone.sh
```

### 🔹 Step 4: Map the Destination Cloud Pipeline
Run the companion installer to fetch the proprietary IDrive Personal CLI backend engine, initialize the container dependencies, and launch the secure login prompt:
```bash
chmod +x setup-idrive.sh
./setup-idrive.sh
```
*Enter your 4 TB IDrive Account Username and Password when prompted, then exit their text utility menu to return to your primary bash shell.*

---

---

### 📋 Connection Verification Check
Before launching multi-gigabyte background execution loops, run this explicit conceptual concept check to prove the container environment resolves your cloud storage mapping natively:
```bash
rclone lsd gdrive:
```
*If this prints your top-level Google Drive cloud folder structure, your secure pipeline architecture is verified and ready.*

### 📂 Step 5: Configure the Transfer Job Queue

Before executing the migration agent, define your global processing limits and queue up your targeted folders. Create a file named **`transfer.json`** in your project root directory and structure your multi-job queue using this array layout:

```json
{
  "max_folder_size_bytes": 5368709120,
  "max_retries": 3,
  "jobs": [
    {
      "from": "gdrive",
      "to": "idrive",
      "gdrive_path": "gdrive:Media/Photos/2025_Archive",
      "idrive_path": "/PersonalBackup/Archive/Photos"
    },
    {
      "from": "gdrive",
      "to": "idrive",
      "gdrive_path": "gdrive:Work/Documents/Legal",
      "idrive_path": "/PersonalBackup/Documents/Legal"
    },
    {
      "from": "gdrive",
      "to": "idrive",
      "gdrive_path": "gdrive:HomeVideos/Dashcam",
      "idrive_path": "/PersonalBackup/Videos/Raw"
    }
  ]
}
```

#### 📋 Array Architecture Breakdown:
*   **Global Parameters:** `max_folder_size_bytes` (e.g., `5368709120` bytes equals a **5 GB** ceiling) and `max_retries` apply globally across the entire run to protect your Codespace storage limits.
*   **`jobs` Array:** A sequential list of independent migration tasks. The Python utility engine will automatically process these tasks one after another.
*   **Isolated Targets:** Each job block lets you specify unique, dedicated matching locations on both Google Drive and IDrive Personal Backup.

### 🚀 Step 6: Launch the Transfer Agent
To initiate the automated transfer sequence—sorting your files from newest to oldest, logging dual hashes, and respecting your local cache boundaries—execute the main Python utility engine:
```bash
python3 transfer.py
```

All data metrics, verification records, and cryptographic lineages will be appended continuously to your local `transfer_log.csv` file for permanent forensic reference.

---

### 📁 Folder Structure

```text
idrive-xfer-utility/
├── .gitignore          <- Blocks token leaks and cache junk
├── LICENSE             <- MIT open-source freedom license
├── README.md           <- The professional guide we just generated
├── setup-idrive        <- Fetches and authenticates IDrive CLI
├── setup-rclone        <- Injects the playground config tokens
└── transfer.py         <- The core forensic Python copy engine
```
