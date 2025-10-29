# #59: Secret Recipe 💡(Registry Forensics + Registry Explorer)

Perform Registry Forensics to Investigate a case.

# Storyline

I need to examine registry artifacts from **James’s confiscated laptop** to determine whether he copied Jasmine’s secret coffee recipe to his machine.

## Scenario

- Jasmine owns **Coffely** in NYC; the **original recipe** exists only on her work laptop.
- **James (IT)** repaired her laptop last week and is **suspected** of copying the recipe to his machine.
- Forensics found **no obvious traces on disk**, but the security team exported **registry hives** for analysis.
- My job: **parse the registry artifacts** and determine presence (or traces) of the secret files.

![image.png](image.png)

## Lab / VM details

- Start the lab VM (Start Machine → split-screen view). If VM not visible, click **Show Split View**.
- Boot time: **3–5 minutes**.
- Desktop contains:
    - **Artifacts** folder → registry hives to examine.
    - **EZ tools** folder → analysis tools (includes Registry Explorer).

## Access / Credentials

- **Username:** `Administrator`
- **Password:** `thm_4n6`

## Notes / approach

- Use **Registry Explorer** or the provided EZ tools to parse hives (expect delay while parsing).
- Focus areas in registry that commonly reveal file/activity traces: **RecentDocs, MUICache, UserAssist, shellbags, MountPoints2, TypedURLs, USB/ConnectedDevices entries**, and any custom app MRU entries.
- Save findings (keys/values/timestamps) and record evidence paths and timestamps for reporting.

## Answer the questions below

**How many files are available in the Artifacts folder on the Desktop?**

![image.png](image%201.png)

Answer: `6`

# Task 2: Windows Registry Forensics

## Overview

The **Windows Registry** acts as a **database** that stores vital information about:

- The **system configuration**
- **User accounts and activities**
- **Processes executed**
- **Files accessed, modified, or deleted**

This makes it a crucial source of evidence in forensic investigations.

---

## Task Setup

In this challenge, I need to **analyze the registry hives** extracted from **James’s machine** (the suspect) to uncover traces of suspicious activity — possibly involving Jasmine’s secret recipe.

### Hive Location

All registry hives are located at:

```
C:\Users\Administrator\Desktop\Artifacts
```

### Tools Location

Required tools (like Registry Explorer, etc.) are available at:

```
C:\Users\Administrator\Desktop\EZ Tools
```

---

## Registry Hives Provided

1. **SYSTEM** – Machine configuration, hardware info, and system startup details
2. **SECURITY** – Security policies, user rights, and audit settings
3. **SOFTWARE** – Installed applications and system software info
4. **SAM** – User accounts, passwords (hashed), and login info
5. **NTUSER.DAT** – User-specific data: recent files, programs, and activities
6. **UsrClass.dat** – User-specific class and shell data (recent folders, programs, etc.)

---

## Note

A **cheat sheet** is available via the “Download Task Files” button — useful for quickly referencing registry keys.

[WindowsForensicsCheatsheet-1665745731601.pdf](WindowsForensicsCheatsheet-1665745731601.pdf)

# Answer the questions below:

## Setup — load the hives into Registry Explorer (do this first)

I want to examine system, user and software artefacts. Loading the hives into Registry Explorer gives me a GUI view of keys/values and timestamps.

Steps I performed

1. Open **Registry Explorer** (run as Administrator if needed).
2. Use **File → Load Hive** and load these files (recommended order):
    
    ![image.png](image%202.png)
    
    - `SYSTEM`
    - `SAM`
    - `SOFTWARE`
    - the user `NTUSER.DAT` for the suspect user (so I can inspect per-user artifacts)
    
    ![image.png](image%203.png)
    
3. If any “dirty hive” or replay transaction dialogs appear, follow the prompt as appropriate (typically **No** to replay logs, **Yes** to load dirty hive).
4. Expand the loaded hives in the left pane to navigate to the paths used below.

---

## Q1 - Computer Name (SYSTEM hive)

To confirm I’m investigating the correct machine.

1. In Registry Explorer expand `SYSTEM` → `ControlSet001` (or `CurrentControlSet` if present).
2. Navigate to:
    
    ```
    SYSTEM\ControlSet001\Control\ComputerName\ComputerName
    ```
    
3. Click the key inside (`ComputerName`) and read the `ComputerName` value on the right.
    
    ![image.png](image%204.png)
    

**Answer (Computer Name):** `JAMES`

![image.png](image%205.png)

---

## Q2 - Administrator account creation time (SAM hive)

SAM hive stores account metadata including creation timestamps.

1. In Registry Explorer expand the `SAM` hive.
2. Navigate to:
    
    ```
    SAM\SAM\Domains\Account\Users
    ```
    
    ![image.png](image%206.png)
    
3. Click `Users` then switch to the **list view** on the right. Expand the column widths to see the `Created on` that shows the account creation time for each user.
4. Locate the row for the Administrator account (usually `RID 500`) and read its creation timestamp.
    
    ![image.png](image%207.png)
    

**Answer (Administrator created on):** `2021-03-17 14:58:48`

---

## Q3 - RID for Administrator (SAM hive)

RID identifies the account in the SAM.

1. In the same `SAM` → `Users` view (from Q2) I expanded the `User Id` column so it was fully visible.
    
    ![image.png](image%208.png)
    

**Answer (Administrator RID):** `500`

---

## Q4 - How many user accounts are present (SAM hive)

1. In Registry Explorer under `SAM\...Users\Names` expand `Names`.
    
    ![image.png](image%209.png)
    
2. Count the subkeys (each user is a subkey under `Names`).

**Answer (number of user accounts):** `7`

---

## Q5 - Account name for RID 1013 (SAM hive)

To identify suspicious/backdoor account.

1. Go back to `SAM\SAM\Domains\Account\Users`.
2. Locate the row with `user ID` = **1013.**
    
    ![image.png](image%2010.png)
    
3. The name is visible in the `User Name` column.
    
    ![image.png](image%2011.png)
    

**Answer (account name for RID 1013):** `bdoor`

---

## Q6 - VPN connection name (SOFTWARE hive)

SOFTWARE often contains persisted network / application connection metadata (NetworkList).

1. Load/expand `SOFTWARE` hive in Registry Explorer.
2. Navigated to:`SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList` .
    
    ![image.png](image%2012.png)
    
3. Clicked `NetworkList`and inspected the right pane where network names are stored.

**Answer (VPN name):** `ProtonVPN`

![image.png](image%2013.png)

---

## Q7 - First observed VPN connection timestamp (SOFTWARE hive)

1. In the same `NetworkList` view (Q6).
2. Located the `First Connect LOCAL` column for the VPN entry and read the full timestamp.
    
    ![image.png](image%2014.png)
    

**Answer (**format `YYYY-MM-DD HH:MM:SS`**):** `2022-10-12 19:52:36`

---

## Q8 - Path of the third shared folder (SYSTEM hive)

Shares (LanmanServer) data is stored in SYSTEM hive.

1. In Registry Explorer I searched for `shares` under `SYSTEM`.
    
    ![image.png](image%2015.png)
    
2. Click on`Shares` inside “LanmanServer” folder, the name of third share is present there.
    
    ![image.png](image%2016.png)
    
3. Click on the value name and look at the Type Viewer to see the full path.
    
    ![image.png](image%2017.png)
    

**Answer (third share path):** `C:\RESTRICTED FILES`

---

## Q9 - Last DHCP IP assigned (SYSTEM hive)

Tcpip interface keys store DHCP info including last assigned IP.

1. In Registry Explorer expand:
    
    ```
    SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
    ```
    
    ![image.png](image%2018.png)
    
2. Click each interface key and examine their IP address if you there is.
    
    ![image.png](image%2019.png)
    

**Answer (Last DHCP IP):** `172.31.2.197`

---

## Q10 - File name that contains the secret coffee recipe (NTUSER.DAT)

RecentDocs under NTUSER contains per-user recently accessed files. I am gonna use NTUSER.DAT this time because it contains suspects data, and the question specified that the suspect seems to have accessed the file.

1. Load and expand the target user’s `NTUSER.DAT` hive in Registry Explorer.
2. Navigate to:
    
    ```
    NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
    ```
    
    ![image.png](image%2020.png)
    
3. Inspected the items in the right-side pane/view and looked for a PDF filename for the secret coffee recipe.

**Answer (secret recipe file name):** `secret-recipe.pdf`

![image.png](image%2021.png)

---

## Q11 - Command used to enumerate network interfaces (NTUSER.DAT RunMRU / TypedPaths etc.)

Run/TypedPaths / MRU lists are helpful here because they record entered commands and searches.

1. Inspect `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU` 
    
    ![image.png](image%2022.png)
    
2. Looked for commands related to `interfaces`, and found one.:
    
    ![image.png](image%2023.png)
    

**Answer:** `pnputil /enum-interfaces`

---

## Q12 - Network utility searched for in File Explorer (WordWheelQuery)

WordWheelQuery stores typed searches in Explorer.

1. In `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer` find `WordWheelQuery`.
    
    ![image.png](image%2024.png)
    
2. Click `WordWheelQuery` and inspect the search term.
    
    ![image.png](image%2025.png)
    

**Answer:** `netcat`

---

## Q13 - Recent text file opened by the suspect (RecentDocs)

RecentDocs contains entries per extension.

1. Back to `NTUSER.DAT\...\Explorer\RecentDocs` and expand the `.txt` node.
    
    ![image.png](image%2026.png)
    
2. Inspect the list and note the most recent `.txt` entry.
    
    ![image.png](image%2027.png)
    

**Answer:** `secret-code.txt`

---

## Q14 - How many times was PowerShell executed (UserAssist)

UserAssist entries contain run counts for interactive programs.

1. In `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist` expand the subkeys.
    
    ![image.png](image%2028.png)
    
2. For each GUID, view the `Count` value entries
    
    ![image.png](image%2029.png)
    
3. For each GUID with non-zero count, inspect them to locate `powershell.exe` in the decoded list and read its `Count` value.
    
    ![image.png](image%2030.png)
    

**Answer:** `3`

---

## Q15 - Network monitoring tool executed (UserAssist)

The same UserAssist area often records usage of tools like `wireshark`, `tcpdump`, `netstat`, `procmon`, `nmap` etc.

1. Continue in the `UserAssist` decoded list.
2. Scroll down to look for obvious network tool names.
    
    ![image.png](image%2031.png)
    

**Answer: `Wireshark`**

---

## Q16 - How many seconds was ProtonVPN in focus (UserAssist focus time → convert to seconds)

UserAssist stores focus time (in minutes or 100ns ticks depending on UI). We need to convert to seconds.

1. Still in `NTUSER.DAT\...\Explorer\UserAssist`, locate the row for `ProtonVPN.exe.`
2. Look at the `Focus Time` column value.
    
    ![image.png](image%2032.png)
    
3. Convert that value to seconds:
    - 5m,  43s
    - 5 x 60  = 300s
    - 300s + 43s

**Answer:** `343`

---

## Q17 - Full path from which `everything.exe` was executed (UserAssist / RecentApps / Prefetch)

We need the full executed path to show where the utility was run from.

1. Search the `UserAssist` decoded list for `everything.exe` 
    
    ![image.png](image%2033.png)
    
2. Note the associated “path” text.

**Answer:** `C:\Users\Administrator\Downloads\tools\Everything\Everything.exe`

---

![image.png](image%2034.png)

---