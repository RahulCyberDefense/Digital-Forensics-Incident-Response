# #41: Windows Forensics (I)

![35c1c080687b084a9ebbaca0a4e9cfc9.png](35c1c080687b084a9ebbaca0a4e9cfc9.png)

In this room, I learned how to investigate and extract forensic artifacts from a Windows system. As a SOC Analyst, I explored registry hives, system configurations, evidence of file and program execution, and USB device traces to uncover digital evidence.

---

## Task 2 — Windows Registry and Forensics

Windows Registry is a hierarchical database storing configuration settings and user/system information. It’s a critical source of forensic evidence.

**Structure of the Registry:** 

The registry on any Windows system contains the following five root keys:

1. HKEY_CURRENT_USER
2. HKEY_USERS
3. HKEY_LOCAL_MACHINE
4. HKEY_CLASSES_ROOT
5. HKEY_CURRENT_CONFIG

To view these keys I have to open `regedit.exe` utility. To open the registry editor, press the Windows key and the R key simultaneously. It will open a `run` prompt that looks like this:

![9d33389f2fd0445a63e75dce3f6d7a88.png](9d33389f2fd0445a63e75dce3f6d7a88.png)

In this prompt, I type `regedit.exe` , to open the registry editor window. It will look something like this:

![e14ef3193fce1f4b35c37a96862d71da.png](e14ef3193fce1f4b35c37a96862d71da.png)

**Steps & Findings:**

Q. What is the short form for HKEY_LOCAL_MACHINE?

![1_5jYRxyllIUh4qStnwX-47g.png](1_5jYRxyllIUh4qStnwX-47g.png)

1. Identified the short form of `HKEY_LOCAL_MACHINE` → **HKLM**

---

## Task 3 — Accessing Registry Hives Offline

**Concept:**

![1_iepWJThrFCyQX4KWsXwqCQ.png](1_iepWJThrFCyQX4KWsXwqCQ.png)

Registry hives can be accessed offline to analyze system state without booting into Windows.

**Hives containing user information:**

Apart from these hives, two other hives containing user information  can be found in the User profile directory. For Windows 7 and above, a  user’s profile directory is located in `C:\Users\<username>\` where the hives are:

1. **NTUSER.DAT** (mounted on HKEY_CURRENT_USER when a user logs in)
2. **USRCLASS.DAT** (mounted on HKEY_CURRENT_USER\Software\CLASSES)

The USRCLASS.DAT hive is located in the directory 

`C:\Users\<username>\AppData\Local\Microsoft\Windows`

![3ffadf20ebe241040d659958db115c2f.png](3ffadf20ebe241040d659958db115c2f.png)

The NTUSER.DAT hive is located in the directory `C:\Users\<username>\`.

![f3091f38f680b418f89cf79128d1933c.png](f3091f38f680b418f89cf79128d1933c.png)

NTUSER.DAT and USRCLASS.DAT are hidden files.

**The Amcache Hive:**

The Amcache hive, located at `C:\\Windows\\AppCompat\\Programs\\Amcache.hve`, is a critical Windows registry hive that stores data on recently executed programs.

**Transaction Logs and Backups:**

- **Transaction Logs**: Act as a journal for registry hive changes, capturing recent updates not yet in the hives. Stored as `.LOG` files (e.g., `SAM.LOG`) in the same directory as the hive, with possible additional logs like `.LOG1`, `.LOG2`. Essential for registry forensics.
- **Registry Backups**: Copies of registry hives in `C:\\Windows\\System32\\Config`, backed up every 10 days to `C:\\Windows\\System32\\Config\\RegBack`. Useful for investigating recently deleted or modified registry keys.

**Answers:**

What is the path for the five main registry hives, DEFAULT, SAM, SECURITY, SOFTWARE, and SYSTEM?

→ **Path:** `C:\Windows\System32\Config`

What is the path for the AmCache hive?

→ **Path:** `C:\Windows\AppCompat\Programs\Amcache.hve`

---

## Task 4 — Data Acquisition

When performing registry forensics, it’s best to work with an image or copy of the system data to ensure accuracy, a process called data acquisition. This can be done on a live system or disk image. Directly copying registry hives from `%WINDIR%\\System32\\Config` is not possible due to restrictions, so specialized tools are used:

- **KAPE**: A command-line and GUI tool for live data acquisition and analysis. It can extract registry data with pre-configured settings, as shown in its GUI interface.
    
    ![48b9656c4037d0537bf517d6084517a8.png](48b9656c4037d0537bf517d6084517a8.png)
    
- **Autopsy**: Supports data extraction from live systems or disk images. Navigate to the desired files, right-click, and select “Extract File(s)” to acquire registry data.
    
    ![5faf350078f7e7e1d1400f46e11e74a9.png](5faf350078f7e7e1d1400f46e11e74a9.png)
    
- **FTK Imager**: Extracts files from disk images or live systems by mounting them. Use the “Export Files” option or “Obtain Protected Files” (live systems only) to extract registry hives, though it does not copy the `Amcache.hve` file, critical for tracking recently executed programs.
    
    ![08b1f97dd47b33c4261fb362d49ac929.png](08b1f97dd47b33c4261fb362d49ac929.png)
    

---

## Task 5 — Exploring Windows Registry

Registry Editor only works on live systems and cannot load exported hives. Use these tools instead:

- **Registry Viewer (AccessData)**: UI similar to Windows Registry Editor. Limitations: Loads one hive at a time; ignores transaction logs.
    
    ![888afb265fa265d771dc02ae8f610dc0.png](888afb265fa265d771dc02ae8f610dc0.png)
    
- **Zimmerman's Registry Explorer**: Loads multiple hives simultaneously, merges transaction logs for updated data. Features bookmarks for key forensic registry keys/values, allowing quick navigation.
    
    ![414dee2639b9456334c9580aacdc2be1.png](414dee2639b9456334c9580aacdc2be1.png)
    
- **RegRipper**: CLI/GUI tool that analyzes a hive and generates a sequential text report of forensically relevant keys/values. Shortcoming: Ignores transaction logs—merge hives with Registry Explorer first for accuracy.
    
    ![70e6fef3920cb9b0443bc1fa9d9fac5d.png](70e6fef3920cb9b0443bc1fa9d9fac5d.png)
    

---

## Task 6 — System Information & Accounts

I have included screenshots of how the following looks like in Registry Explorer. 

To perform forensic analysis, key registry locations provide critical system and account information:

- **OS Version**: Find in `SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion` to identify the operating system version (Question #1).
    
    ![1362c5a15d1879a1a5a5a5237a426108.png](1362c5a15d1879a1a5a5a5237a426108.png)
    
- **Current Control Set**: Located in `SYSTEM\\CurrentControlSet`, it reflects the active control set used at boot, typically `ControlSet001`. Verify using `SYSTEM\\Select\\Current` for the current set and `SYSTEM\\Select\\LastKnownGood` for the last known good configuration (Question #2).
    
    ![f3b34b5e44e98e76034b76fc608a7670.png](f3b34b5e44e98e76034b76fc608a7670.png)
    
- **Computer Name**: Found at `SYSTEM\\CurrentControlSet\\Control\\ComputerName\\ComputerName` to confirm the machine’s identity (Question #3).
    
    ![bb73d7942a6e30cb96e78926ad36fddb.png](bb73d7942a6e30cb96e78926ad36fddb.png)
    
- **Time Zone Information**: Located at `SYSTEM\\CurrentControlSet\\Control\\TimeZoneInformation` to establish the local time zone, aiding in event timeline creation (Question #4).
    
    ![08d5e86bb3a5be6057928a8062cf7de3.png](08d5e86bb3a5be6057928a8062cf7de3.png)
    
- **Network Interfaces**: Check `SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters\\Interfaces` for network interface details like IP addresses, DHCP, and DNS (Question #5).
    
    ![7f0ed33ad442f22ec9475488d6af4421.png](7f0ed33ad442f22ec9475488d6af4421.png)
    
- **Past Networks**: Stored in `SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\NetworkList\\Signatures\\Unmanaged` and `Managed`, showing past network connections and their last connection time via registry key last write time.
    
    ![fae511770c0ac57458073992ef221251.png](fae511770c0ac57458073992ef221251.png)
    
- **Autostart Programs (Autoruns)**: Found in:
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Run`
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\RunOnce`
    - `SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\RunOnce`
    - `SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\policies\\Explorer\\Run`
    - `SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Run`
        
        ![6745df01d5c2f896795d5d6f481461b7.png](6745df01d5c2f896795d5d6f481461b7.png)
        
    - Services at `SYSTEM\\CurrentControlSet\\Services`, where a `Start` value of `0x02` indicates automatic startup at boot.
        
        ![5605bfda34393bfcb8c4aee6a5ad771f.png](5605bfda34393bfcb8c4aee6a5ad771f.png)
        
        In this registry key, if the `start`key is set to 0x02, this means that this service will start at boot.
        
- **SAM Hive and User Information**: Located at `SAM\\Domains\\Account\\Users`, containing user details like RID, login counts, last login, failed login, password changes, policies, hints, and group memberships (Question #6).
    
    ![d24056c00af7ef9e77ea25b883cdf06c.png](d24056c00af7ef9e77ea25b883cdf06c.png)
    
    The information contained here includes the relative identifier (RID) of the user, number of times the user logged in, last login time, last failed login, last password change, password expiry, password policy and password hint, and any groups that the user is a part of. 
    

**Answers:**

1. **Current Build Number:** `19044`
2. **ControlSet with Last Known Good Configuration:** `1`
3. **Computer Name:** `THM-4n6`
4. **TimeZoneKeyName:** `Pakistan Standard Time`
5. **DHCP IP Address:** `192.168.100.58`
6. **Guest User RID:** `501`

---

## Task 7 — Usage or Knowledge of Files/Folders

- **Recent Files**: Stored in `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\RecentDocs`.
    
    ![aff5ea8e993f2989f5f8caf94798a3c7.png](aff5ea8e993f2989f5f8caf94798a3c7.png)
    
    Lists recently opened files, sortable in Registry Explorer with the Most Recently Used (MRU) file at the top. Specific file extensions (e.g., `.pdf`) are found in subkeys like `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\RecentDocs\\.pdf`, including last opened times (Question #1).
    
- **Office Recent Files**: Located in `NTUSER.DAT\\Software\\Microsoft\\Office\\VERSION` (e.g., `15.0` for Office 2013). For Office 365, tied to user’s Live ID at `NTUSER.DAT\\Software\\Microsoft\\Office\\VERSION\\UserMRU\\LiveID_####\\FileMRU`, including full file paths.
- **ShellBags**: Store folder layout preferences per user, indicating recently used files/folders. Found in:
    - `USRCLASS.DAT\\Local Settings\\Software\\Microsoft\\Windows\\Shell\\Bags`
    - `USRCLASS.DAT\\Local Settings\\Software\\Microsoft\\Windows\\Shell\\BagMRU`
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\Shell\\BagMRU`
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\Shell\\Bags`
    Use ShellBag Explorer for better parsing (Question #2).
        
        ![666bd5bd3db41b4b6e3f09311f25666a.png](666bd5bd3db41b4b6e3f09311f25666a.png)
        
- **Open/Save and LastVisited Dialog MRUs**: Track file open/save locations in:
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ComDlg32\\OpenSavePIDlMRU`
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ComDlg32\\LastVisitedPidlMRU`
    (Questions #3 and #4).
        
        ![e996b8939895b4b5e55e780baa4335e9.png](e996b8939895b4b5e55e780baa4335e9.png)
        
- **Windows Explorer Address/Search Bars**: Recent paths and searches typed in Explorer are stored in:
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\TypedPaths`
    - `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\WordWheelQuery`.
    
    ![782204163443e8f21ddd14297ba756dd.png](782204163443e8f21ddd14297ba756dd.png)
    

**Answers:**

1. **EZtools opened on:** `2021–12–01 13:00:34`
2. **My Computer last accessed on:** `2021–12–01 13:06:47`
3. **File opened via Notepad:**
    
    → Path: `C:\Program Files\Amazon\Ec2ConfigService\Settings`
    
    → Opened at: `2021–11–30 10:56:19`
    

---

## Task 8 — Evidence of Execution

### UserAssist

![9bd8461865865ac3ff774c8a88d1afd5.png](9bd8461865865ac3ff774c8a88d1afd5.png)

- **Purpose**: Tracks applications launched via Windows Explorer for statistical purposes, including program names, launch times, and execution counts. Excludes command-line programs.
- **Location**: `NTUSER.DAT\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\UserAssist\\{GUID}\\Count`
- **Tool**: Registry Explorer displays this data (see screenshot for Question #1).

### ShimCache (AppCompatCache)

![aad7dc918dbf3b1ab207dd71d03e8c0c.png](aad7dc918dbf3b1ab207dd71d03e8c0c.png)

- **Purpose**: Tracks application compatibility with the OS, logging all executed applications with file name, size, and last modified time.
- **Location**: `SYSTEM\\CurrentControlSet\\Control\\Session Manager\\AppCompatCache`
- **Tool**: Registry Explorer doesn’t parse ShimCache well. Use **AppCompatCacheParser** (Eric Zimmerman’s tool) with command:
    
    ```
    AppCompatCacheParser.exe --csv <output_path> -f <SYSTEM_hive_path> -c <control_set>
    
    ```
    
- **Output**: CSV file viewable in **EZviewer** (another Zimmerman tool).

### AmCache

- **Purpose**: Similar to ShimCache, tracks program executions with additional details like execution path, installation/execution/deletion times, and SHA1 hashes.
- **Location**: `C:\\Windows\\appcompat\\Programs\\Amcache.hve`, with execution data in `Amcache.hve\\Root\\File\\{Volume GUID}\\`
- **Tool**: Registry Explorer parses AmCache data effectively.
    
    ![a569dfdf155c1a26fe3a693c388a44c7.png](a569dfdf155c1a26fe3a693c388a44c7.png)
    

### BAM/DAM

![8a672c6580ab63d757ee5c08c09c924a.png](8a672c6580ab63d757ee5c08c09c924a.png)

- **Purpose**:
    - **BAM** (Background Activity Monitor): Tracks background application activity.
    - **DAM** (Desktop Activity Moderator): Optimizes power consumption in Modern Standby systems.
- **Location**:
    - `SYSTEM\\CurrentControlSet\\Services\\bam\\UserSettings\\{SID}`
    - `SYSTEM\\CurrentControlSet\\Services\\dam\\UserSettings\\{SID}`
- **Data**: Includes last-run programs, full paths, and execution times.
- **Tool**: Registry Explorer displays BAM/DAM data clearly.

Artifacts like ShimCache, AmCache, and BAM/DAM record details of executed programs.

**Answers:**

1. **File Explorer launched:** `26 times`
2. **ShimCache also known as:** `AppCompatCache`
3. **Artifact storing SHA1 hashes:** `AmCache`
4. **Artifact storing full path of executed programs:** `BAM/DAM`

---

## Task 9 — External Devices / USB Forensics

**Device Identification**

- **Purpose**: Tracks USB devices connected to the system, storing vendor ID, product ID, version, and connection times.
- **Locations**:
    - `SYSTEM\\CurrentControlSet\\Enum\\USBSTOR`
    - `SYSTEM\\CurrentControlSet\\Enum\\USB`
- **Tool**: Registry Explorer displays this data clearly (see screenshots for Questions #1 and #2).
    
    ![03c87eaaf8458db97ffbddd30a6463e8.png](03c87eaaf8458db97ffbddd30a6463e8.png)
    

**First/Last Connection Times**

- **Purpose**: Logs the first connection, last connection, and last removal times of USB devices.
- **Location**:
    - `SYSTEM\\CurrentControlSet\\Enum\\USBSTOR\\Ven_Prod_Version\\USBSerial#\\Properties\\{83da6326-97a6-4088-9453-a19231573b29}\\####`
    - Replace `####` with:
        - `0064`: First connection time
        - `0066`: Last connection time
        - `0067`: Last removal time
- **Tool**: Registry Explorer automatically parses these timestamps when viewing the USBSTOR key.

**USB Device Volume Name**

- **Purpose**: Identifies the volume name of connected USB drives.
- **Location**: `SOFTWARE\\Microsoft\\Windows Portable Devices\\Devices`
    
    ![6c9f71cdf4c71afdc19fc8c254a6a3cc.png](6c9f71cdf4c71afdc19fc8c254a6a3cc.png)
    
- **Correlation**: Match the GUID in this key with the Disk ID from USBSTOR/USB keys to link volume names to specific devices (see screenshots for Question #3).

**Summary**: Combining data from these keys provides a comprehensive view of USB devices connected to the system, including their identity, connection history, and volume names.

USB device connections are logged in the registry and can reveal removable storage usage.

**Answers:**

1. **Manufacturer:** `Kingston`
2. **Serial Number:** `1C6F654E59A3B0C179D366AE&`
3. **Device Name:** `Kingston Data Traveler 2.0 USB Device`
4. **Friendly Name:** `USB`

---

##
