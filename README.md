# 🔐 Splunk SIEM + Atomic Red Team (ART) Lab

## 📌 Objective
Install and configure Splunk SIEM, set up a victim machine to simulate MITRE ATT&CK techniques using Atomic Red Team (ART), and analyze logs in Splunk to detect malicious activities.

---

## 🧪 Lab Environment

| Machine | IP Address | Role | Ports |
|--------|------------|------|------|
| Kali Linux | 192.168.0.105 | Splunk Server | 8000, 9997 |
| Windows Server | 192.168.0.109 | Victim Machine | N/A |

---

## 🧰 Tools Used

- Splunk Enterprise (SIEM)
- Splunk Universal Forwarder
- Sysmon
- Atomic Red Team (ART)
- PowerShell
- Git

---

## 🌐 Network Diagram

<img width="870" height="442" alt="image" src="https://github.com/user-attachments/assets/ac271828-0137-4b9f-b04c-ab8a3c34c651" />


---

## ⚙️ Splunk Installation (Kali Linux)

```bash
wget -O splunk.deb https://download.splunk.com/products/splunk/releases/10.0.2/linux/splunk.deb
sudo dpkg -i splunk.deb
cd /opt/splunk/bin
sudo ./splunk start
```

Access Splunk Web:

http://192.168.0.105:8000

<img width="975" height="280" alt="image" src="https://github.com/user-attachments/assets/b45a36fd-847f-4b3f-9eee-0f8b4325a70e" />


---
Deshboard
---------
<img width="975" height="318" alt="image" src="https://github.com/user-attachments/assets/2f46e5f4-4c77-4e4e-ace1-2eec802d675d" />


## 📊 Splunk Configuration

### ✅ Create Index
- Settings → Indexes → New Index
- Name: `wineventlog`

<img width="975" height="909" alt="image" src="https://github.com/user-attachments/assets/6beaf792-4628-4104-9aea-ca8e7e8ccb95" />


### ✅ Configure Receiving Port
- Port: `9997`

<img width="1028" height="202" alt="image" src="https://github.com/user-attachments/assets/2e315311-8a73-49d1-b2eb-7124fdf85f30" />


---

## 💻 Victim Machine Setup

### ✅ Install Universal Forwarder

Download splunk from https://www.splunk.com/en_us/download/universal-forwarder.html

---------
<img width="975" height="538" alt="image" src="https://github.com/user-attachments/assets/37959968-62b3-4e88-b535-b66b4a17d5a7" />

-----
Start installation
------
Right click on installation file and run as administration, you will see following wizard
Click on Next
------
<img width="975" height="617" alt="image" src="https://github.com/user-attachments/assets/17d9fc61-f506-4ff7-99e6-663dc4c194d5" />
--------

Admin user and password need to provide that will require to manage it. Please remember or store on password vault.

-------
<img width="944" height="501" alt="image" src="https://github.com/user-attachments/assets/ea20b2b8-41b2-4548-88fa-b047b5734077" />

--------------

<img width="975" height="670" alt="image" src="https://github.com/user-attachments/assets/91075c07-d591-4c34-adfb-00cfcccdd16a" />
-----------
<img width="867" height="622" alt="image" src="https://github.com/user-attachments/assets/a84f0d14-d335-4d72-89c8-8250642df402" />

---------
Configure forwarder

---------------
Got to  C:\Program Files\SplunkUniversalForwarder\etc\system\local and create new file inputs.conf file and upfate file as follows
-----


### ✅ Configure Inputs (`inputs.conf`)

```ini
[WinEventLog://Application]
disabled = 0
index = wineventlog

[WinEventLog://Security]
disabled = 0
index = wineventlog

[WinEventLog://System]
disabled = 0
index = wineventlog

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = wineventlog
renderXml = 1
```

---

## 🛡️ Sysmon Installation

Download Sysmon and extract on folder
https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
----
download XML file for Sysmon on the same folder
--
wget -o sysmonconfig.xml https://wazuh.com/resources/blog/emulation-of-attack-techniques-and-detection-with-wazuh/sysmonconfig.xml
-----
<img width="975" height="376" alt="image" src="https://github.com/user-attachments/assets/13e0246f-f897-41a6-b160-8dd25b968b8f" />

-----------
<img width="975" height="486" alt="image" src="https://github.com/user-attachments/assets/a6c72b3b-6063-46f5-8028-0bcd84c0e970" />

-----
run sysmon exe
```bash
Sysmon64.exe -accepteula -i sysmonconfig.xml
```

<img width="975" height="363" alt="image" src="https://github.com/user-attachments/assets/ad8f672d-02b1-474c-a558-bdf3d7ec7569" />

---
Restart splunk forwarder
cd “C:\Program Files\SplunkUniversalForwarder\bin”
./splunk.exe restart

Or
Restart-Service SplunkForwarder
Check forwarder is running and  winevents are included
---------------
<img width="975" height="308" alt="image" src="https://github.com/user-attachments/assets/4f3950d2-364b-4a47-8735-8708069dfdac" />

------------
Splunk list forward-server
cd “C:\Program Files\SplunkUniversalForwarder\bin”
.\splunk list inputstatus
Authentication needed, provide use rand password that use during forwarder installation

------------

<img width="975" height="173" alt="image" src="https://github.com/user-attachments/assets/516d85cd-9f4e-4053-a1c6-33fcad52daae" />


## 🔍 Log Verification in Splunk

```spl
index=wineventlog sourcetype=WinEventLog*
```

Sysmon logs:

```spl
sourcetype="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
```
<img width="975" height="482" alt="image" src="https://github.com/user-attachments/assets/2fb40a33-5112-47d5-9e07-565c4aea161c" />



---

## ⚔️ Atomic Red Team Setup

Download and install Git from: https://git-scm.com/downloads
Follow default installation steps.
----------
<img width="869" height="573" alt="image" src="https://github.com/user-attachments/assets/6df57aca-2277-48fb-9ccd-a110f38199f5" />

------------
Set PowerShell Execution Policy
Run the following command:
---------------------
Set-ExecutionPolicy Bypass -Scope Process -Force

-------------------

```powershell
git clone https://github.com/redcanaryco/atomic-red-team.git C:\AtomicRedTeam

-------------------------
<img width="975" height="332" alt="image" src="https://github.com/user-attachments/assets/04e5c85a-07b1-4145-a045-c17023e81aa8" />

--------------
Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1"
-------------------
Set Atomic Tests Path
Run:
--------------------
$env:PathToAtomicsFolder="C:\AtomicRedTeam\atomics"
```

---

## 🚨 Attack Simulation & Detection

### 🔹 Attack 1 | Persistence (T1053.005)
```powershell
Invoke-AtomicTest T1053.005
----------------
PS>Get-Scheduledtask |more
-----
<img width="975" height="527" alt="image" src="https://github.com/user-attachments/assets/dd50c049-5093-4e3e-a636-1431e4a286ad" />
----------


```
```spl
index=wineventlog "technique_id=T1053.005"
```
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/a44f0481-421d-44ac-8217-dec96af33a49" />


---

### 🔹Attack | 2  Defense Evasion (T1218.005)
```powershell
Invoke-AtomicTest T1218.005
```
<img width="975" height="551" alt="image" src="https://github.com/user-attachments/assets/1f98a0dc-8215-446b-916f-13c410844d52" />
----

<img width="975" height="551" alt="image" src="https://github.com/user-attachments/assets/9e88149e-1eef-435a-964e-3b8e0e6e3857" />

--------------

Executed following SPL query in Splunk
index=wineventlog "technique_id=T1218.005"

---

<img width="975" height="414" alt="image" src="https://github.com/user-attachments/assets/6968eea4-10e5-45c0-9921-cbd724d9c44f" />
----

### 🔹 Attack 3 | Credential Access (T1003.001)
```powershell
Invoke-AtomicTest T1003.001
```
<img width="975" height="370" alt="image" src="https://github.com/user-attachments/assets/55e47027-1141-4a36-9ebf-5620ca370346" />

----------
Executed following queries in Splunk
--------
index=wineventlog "technique_id="  | rex "technique_id=(?<mitre_id>T[0-9\.]+)"  | search mitre_id=T1003
---
<img width="975" height="426" alt="image" src="https://github.com/user-attachments/assets/cc0e2d24-3319-43e2-b8fc-b15d47ba23b9" />
---
---

### 🔹Attack | 4  Execution (T1059.001)
```powershell
Invoke-AtomicTest T1059.001
```
<img width="975" height="598" alt="image" src="https://github.com/user-attachments/assets/5ae7d880-ff67-47c0-9073-d780bf6bc5d6" />
---
<img width="952" height="419" alt="image" src="https://github.com/user-attachments/assets/abd75c54-7195-4a8f-bed6-6774871a6383" />

---------
Executed following queries in Splunk
----
index=wineventlog "technique_id="  | rex "technique_id=(?<mitre_id>T[0-9\.]+)"  | search mitre_id=T1059.001

---
<img width="975" height="437" alt="image" src="https://github.com/user-attachments/assets/4e0cd843-4cd3-49f7-96e6-5915f9eac6b3" />

---

### 🔹 Attack | 5 Registry Modification (T1112)
```powershell
Invoke-AtomicTest T1112
```
<img width="975" height="341" alt="image" src="https://github.com/user-attachments/assets/9b26eac9-9595-45c5-96a4-3415d1b8ffc4" />

---------
Query in Splunk for T1112
index=wineventlog "technique_id="  | rex "technique_id=(?<mitre_id>T[0-9\.]+)"  | search mitre_id=T1112
----------
<img width="975" height="416" alt="image" src="https://github.com/user-attachments/assets/39d66b24-60e5-4ce9-ac7d-012af4e1d5ce" />


---

## ✅ Conclusion

This lab demonstrated how to deploy and configure Splunk as a SIEM, integrate endpoint telemetry using Sysmon and Universal Forwarder, and simulate real-world attacks using Atomic Red Team. The generated logs allowed effective detection and analysis of adversary behavior, providing practical experience in threat monitoring and security operations.

---

## 📎 Author

Kaisar Ahmed Khan  
Principal DevOps & Cloud Architect


