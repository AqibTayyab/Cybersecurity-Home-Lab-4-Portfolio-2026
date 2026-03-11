# Cybersecurity Home Lab 4 Portfolio 2026

Welcome to my hands-on cybersecurity portfolio. This repository documents my journey through a 4-project intensive lab designed to build real-world SOC (Security Operations Center) skills.

**Created by:** [Muhammad Aqib Tayyab](https://www.linkedin.com/in/muhammad-aqib-tayyab-ethical-hacker/)

**Inspired by:** [The Social Dork (Royden Rahul Rebello)](https://www.youtube.com/@thesocialdork1133)

---

### Technology Stack & Tools
![Ubuntu](https://img.shields.io/badge/Ubuntu-E94331?style=for-the-badge&logo=ubuntu&logoColor=white)
![Kali Linux](https://img.shields.io/badge/Kali_Linux-557EBF?style=for-the-badge&logo=kali-linux&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)
![VMware](https://img.shields.io/badge/VMware-1470AF?style=for-the-badge&logo=vmware&logoColor=white)
![Wazuh](https://img.shields.io/badge/Wazuh-00A9E0?style=for-the-badge&logo=wazuh&logoColor=white)

### Skills Learned
* **Infrastructure:** Virtual Networking (Bridged/NAT), Linux Administration (Ubuntu/Kali).
* **Defensive Security:** WAF Configuration, Reverse Proxy Setup, Load Balancing.
* **Offensive Security:** SQL Injection (SQLi) Testing, Payload Crafting.
* **Security Operations:** SIEM Deployment (Wazuh), Log Analysis, Real-time Threat Detection.
* **DevOps:** Containerization with Docker and Docker-Compose.

---

## Project 4: Automated Incident Response & Active Mitigation

### **Objective**
To configure and validate automated incident response mechanisms within a SIEM environment, specifically utilizing Wazuh's Active Response to detect, alert, and neutralize brute-force attacks in real-time.

### **Lab Architecture**
* **Attacker:** Kali Linux (Executing offensive scripts).
* **Defender/SIEM:** Ubuntu 25.10 (Hosting Wazuh Dashboard & Manager).
* **Target:** Managed endpoint machine (IP: 192.168.150.172).

### **Step-by-Step Technical Execution**

1. **Active Response Configuration (Automated Defense)**
Edited the manager's global configuration file to define the mitigation logic:
```bash
sudo nano /var/ossec/etc/ossec.conf
```
Injected the following ```<active-response>``` block to trigger a firewall drop whenever Rule ID 5712 (Multiple SSH authentication failures) is detected:
   ```XML
   <active-response>
     <command>firewall-drop</command>
     <location>local</location>
     <rules_id>5712</rules_id>
     <timeout>600</timeout>
   </active-response>
```

2. **Vulnerability Scanner Configuration**
Updated deprecated schema tags within the same ossec.conf file to initialize the <vulnerability-detection> engine and sync with Canonical OVAL feeds:

```XML
   <vulnerability-detection>
      <enabled>yes</enabled>
      <index-status>yes</index-status>
      <feed name="ubuntu">
      <enabled>yes</enabled>
      <os>24.04</os>
      <url>[https://security.ubuntu.com/oval/com.ubuntu.noble.cve.oval.xml](https://security.ubuntu.com/oval/com.ubuntu.noble.cve.oval.xml)</url>
     </feed>
   </vulnerability-detection>
   ```

3. **Service Restart & Database Verification**
Restarted the Wazuh Manager to apply the new security configurations:

```Bash
sudo systemctl restart wazuh-manager
```
Verified the vulnerability database download and initialization via the manager's internal logs:

```Bash
sudo tail -f /var/ossec/logs/ossec.log | grep -i "vulnerability"
```
Checked the local SQLite database to confirm CVE indexing:

```Bash
sqlite3 /var/ossec/queue/vulnerabilities/cve.db "SELECT count(*) FROM vulnerabilities;"
```
4. **Attack Simulation & Incident Mitigation**
Executed a rapid SSH brute-force attack from the Kali Linux attacker machine targeting the Ubuntu endpoint:

```Bash
ssh aqib@192.168.---.--
```
Failed the authentication sequence over 8 times to trigger the Level 10 SIEM rule.

5. **Forensic Validation**
Verified the automated ban execution in the active response logs on the Ubuntu defender machine:

```Bash
sudo cat /var/ossec/logs/active-responses.log
```
Output confirmed the ```firewall-drop.sh``` script successfully added the Kali IP to the iptables drop list.
Confirmed the network block on the Kali machine (connection terminated):

```Bash
kex_exchange_identification: read: Connection reset by peer
Connection reset by 192.168.--.-- port 22
```
### **Key Outcomes**
* **Real-Time Mitigation:** Successfully blocked an active SSH brute-force attack in milliseconds, forcing a Connection Reset and proving the efficacy of automated incident response.

* **Zero-Touch Scanning:** Established a continuous, automated vulnerability baseline without relying on manual credentialed scans.

* **Full Stack Visibility:** Finalized the SOC infrastructure, proving the ability to monitor logs (Lab 2), detect anomalies (Lab 3), and execute automated network defense (Lab 4).

---

### 📞 Contact & Networking
**Open to SOC Analyst and Cybersecurity internships. Let's connect!**
* **LinkedIn:** [Muhammad Aqib Tayyab](https://www.linkedin.com/in/muhammad-aqib-tayyab-ethical-hacker/)
* **YouTube:** [MuhammadAqibTayyab](https://www.youtube.com/@MuhammadAqibTayyab)
