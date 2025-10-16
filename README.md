# 🛡️ Azure Sentinel Honeypot Attack Map

## 📘 Overview
This project simulates a **cyber attack scenario** using a Windows 10 honeypot hosted in **Microsoft Azure**.  
The goal is to collect failed login attempts, forward the logs to **Azure Log Analytics Workspace**, enrich them with **GeoIP data**, and visualize the global attack origins in **Azure Sentinel**.  

This lab demonstrates key **SOC Analyst** skills:
- Log collection and analysis  
- KQL (Kusto Query Language)  
- SIEM configuration (Azure Sentinel)  
- Threat visualization and correlation  

---

## 🧰 Tools & Technologies
- Microsoft Azure (Virtual Machines, Log Analytics, Sentinel)  
- KQL (Kusto Query Language)  
- GeoIP Database (for location enrichment)  
- Windows Event Viewer  
- PowerShell / Azure Portal  

---

## 🏗️ Architecture
Below is a simplified view of the environment used in this project:

<p align="center">
  <b>Architecture Diagram</b><br>
  <img src="https://github.com/Dayvison07/azure-sentinel-honeypot/blob/46506147a74912822163de3bd8c158f528b1af3d/Architecture.png" width="600"/>
</p>





> 💡 The honeypot intentionally exposes RDP to the internet. Failed login attempts are logged and forwarded to Sentinel, where KQL queries identify attacker IPs and correlate them with geolocation data.

---

## ⚙️ Steps & Implementation

### 1. Azure Environment Setup
- Created a free Azure subscription.  
- Deployed a Windows 10 Virtual Machine (honeypot).  
- Disabled the firewall and opened inbound RDP traffic in the Network Security Group (NSG).  

### 2. Log Collection
- Logged multiple failed login attempts manually (“employee” user).  
- Verified events in Event Viewer → *Event ID 4625 (Failed Logon)*.  

### 3. Log Forwarding
- Created a Log Analytics Workspace (LAW).  
- Configured Azure Monitor Agent (AMA) to forward Windows Security Events.  

### 4. Azure Sentinel Integration
- Connected the LAW to a new Sentinel instance.  
- Created the Security Events via AMA data connector.  

### 5. Data Enrichment
- Imported a GeoIP CSV as a Sentinel *watchlist*.  
- Used KQL to enrich logs with country and region data.  

### 6. Visualization
- Created an Attack Map Workbook in Sentinel using JSON.  
- Mapped attacker IPs to geographic locations in real time.  

---

## 📊 Example KQL Query
```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
 |project TimeGenerated,Computer, AttackerIp = IpAddress, cityname, countryname, latitude, longitude
```

## 📸 Map Visualisation

<p align="center">
  <b>Attack Map Dashboard in Azure Sentinel</b><br>
  <img src="https://github.com/Dayvison07/azure-sentinel-honeypot/blob/c4f67d68b010da55033524a78fd5156c65eeb0e7/map.png" width="650"/>
</p>

## 🧠 Lessons Learned

- Learned how to configure Azure Sentinel and forward security logs.

- Practiced KQL for log analysis and correlation.

- Understood how real-world SIEMs enrich and visualize attack data.

- Improved understanding of cloud-based SOC workflows.

## 🚀 Future Improvements

- Automate alert creation in Sentinel for brute-force patterns.

- Add email notifications for high-volume attacks.

- Integrate additional data sources (Defender for Endpoint, Sysmon).

## 📚 References

- Microsoft Sentinel Documentation

- KQL Language Reference

- GeoIP Watchlists in Sentinel

- [Josh Madakor Vídeo](https://www.youtube.com/watch?v=g5JL2RIbThM&t=2752s)

## 🧾 License

This project is licensed under the MIT License.
Feel free to use or modify it for educational purposes



