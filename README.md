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

```bash
Sysmon64.exe -accepteula -i sysmonconfig.xml
```

![](screenshots/sysmon.png)

---

## 🔍 Log Verification in Splunk

```spl
index=wineventlog sourcetype=WinEventLog*
```

Sysmon logs:

```spl
sourcetype="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
```

![](screenshots/logs.png)

---

## ⚔️ Atomic Red Team Setup

```powershell
git clone https://github.com/redcanaryco/atomic-red-team.git C:\AtomicRedTeam
Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1"
$env:PathToAtomicsFolder="C:\AtomicRedTeam\atomics"
```

---

## 🚨 Attack Simulation & Detection

### 🔹 Persistence (T1053.005)
```powershell
Invoke-AtomicTest T1053.005
```
```spl
index=wineventlog "technique_id=T1053.005"
```
![](screenshots/attack1.png)

---

### 🔹 Defense Evasion (T1218.005)
```powershell
Invoke-AtomicTest T1218.005
```
![](screenshots/attack2.png)

---

### 🔹 Credential Access (T1003.001)
```powershell
Invoke-AtomicTest T1003.001
```
![](screenshots/attack3.png)

---

### 🔹 Execution (T1059.001)
```powershell
Invoke-AtomicTest T1059.001
```
![](screenshots/attack4.png)

---

### 🔹 Registry Modification (T1112)
```powershell
Invoke-AtomicTest T1112
```
![](screenshots/attack5.png)

---

## ✅ Conclusion

This lab demonstrated how to deploy and configure Splunk as a SIEM, integrate endpoint telemetry using Sysmon and Universal Forwarder, and simulate real-world attacks using Atomic Red Team. The generated logs allowed effective detection and analysis of adversary behavior, providing practical experience in threat monitoring and security operations.

---

## 📎 Author

Kaisar Ahmed Khan  
Principal DevOps & Cloud Architect

---

## 📁 Folder Structure

```
project/
│── README.md
└── screenshots/
    ├── network-diagram.png
    ├── splunk-dashboard.png
    ├── index.png
    ├── port.png
    ├── forwarder.png
    ├── sysmon.png
    ├── logs.png
    ├── attack1.png
    ├── attack2.png
    ├── attack3.png
    ├── attack4.png
    └── attack5.png
```
