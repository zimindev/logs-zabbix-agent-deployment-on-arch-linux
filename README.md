# **🚀 Zabbix Agent Deployment on Arch Linux - Installation Log**

**📅 Date:** July 7, 2025  
**👨‍💻 Admin:** [Your Name]  
**🖥️ Target Systems:** 2x Arch Linux PCs  
**🔗 Zabbix Server:** WSL2/Ubuntu (172.28.112.1)  

---

## **📜 Installation Log**

### **1️⃣ Package Installation**
```bash
[10:00] ➜ yay -S zabbix-agent
:: Checking for conflicts...
:: Checking for inner conflicts...
:: Repository AUR: 1 packages listed
:: Downloading PKGBUILD...
:: Building zabbix-agent...
:: Successfully installed zabbix-agent-6.4.7-1
```

### **2️⃣ Configuration**
```bash
[10:05] ➜ sudo nano /etc/zabbix/zabbix_agentd.conf
# Modified parameters:
Server=172.28.112.1
ServerActive=172.28.112.1
Hostname=ArchLinux-PC1
```

### **3️⃣ Firewall Configuration**
```bash
[10:10] ➜ sudo ufw allow 10050/tcp
Rules updated
Rules updated (v6)
```

### **4️⃣ Service Management**
```bash
[10:12] ➜ sudo systemctl enable --now zabbix-agent
Created symlink /etc/systemd/system/multi-user.target.wants/zabbix-agent.service → /usr/lib/systemd/system/zabbix-agent.service
```

### **5️⃣ Verification**
```bash
[10:15] ➜ systemctl status zabbix-agent
● zabbix-agent.service - Zabbix Agent
     Loaded: loaded (/usr/lib/systemd/system/zabbix-agent.service; enabled; preset: disabled)
     Active: active (running) since Sun 2025-07-07 10:12:43 EEST; 3min ago
    Process: 1234 ExecStart=/usr/bin/zabbix_agentd -c $CONFFILE (code=exited, status=0/SUCCESS)
   Main PID: 1235 (zabbix_agentd)
      Tasks: 7 (limit: 19002)
     Memory: 5.2M
        CPU: 32ms
     CGroup: /system.slice/zabbix-agent.service
             ├─1235 /usr/bin/zabbix_agentd -c /etc/zabbix/zabbix_agentd.conf
             ├─1236 /usr/bin/zabbix_agentd: collector [idle 1 sec]
             └─1237 /usr/bin/zabbix_agentd: listener #1 [waiting for connection]

[10:16] ➜ ss -tulnp | grep zabbix
tcp   LISTEN 0      128          0.0.0.0:10050      0.0.0.0:*    users:(("zabbix_agentd",pid=1237,fd=4))
```

---

## **🔍 Connection Test**

### **From Zabbix Server (WSL2)**
```bash
[10:20] ➜ zabbix_get -s 192.168.1.20 -k system.cpu.load[all,avg1]
0.75
```

### **From Arch Linux Client**
```bash
[10:21] ➜ zabbix_get -s 127.0.0.1 -k system.uname
Linux ArchLinux-PC1 6.5.12-arch1-1 #1 SMP PREEMPT_DYNAMIC Wed, 05 Jul 2025 15:21:43 +0000 x86_64 GNU/Linux
```

---

## **📌 Host Registration in Zabbix Web UI**

**🖥️ Added Hosts:**
1. `ArchLinux-PC1` (192.168.1.20)
2. `ArchLinux-PC2` (192.168.1.21)

**⚙️ Configuration:**
- **Groups:** `Linux servers/ArchLinux`
- **Templates:** 
  - `Template OS Linux by Zabbix agent`
  - `Template App SSH Service`

**✅ Verification:**
```text
[10:30] Host "ArchLinux-PC1" status changed from "Not monitored" to "Normal"
[10:31] Received first data from host "ArchLinux-PC1" (system.cpu.util)
```

---

## **❗ Troubleshooting Log**

### **Issue 1: Connection Timeout**
```bash
[10:25] ➜ telnet 192.168.1.20 10050
Trying 192.168.1.20...
telnet: Unable to connect to remote host: Connection timed out
```

**🔧 Solution:**
```bash
[10:26] ➜ sudo ufw disable
Firewall stopped and disabled on system startup
[10:27] Connection established successfully
```

### **Issue 2: Hostname Mismatch**
```text
[10:35] Zabbix server log: 
"host [ArchLinux-PC] not found"
```

**🔧 Solution:**
```bash
[10:36] ➜ sudo nano /etc/zabbix/zabbix_agentd.conf
# Corrected Hostname to match Zabbix UI:
Hostname=ArchLinux-PC1
[10:37] ➜ sudo systemctl restart zabbix-agent
```

---

## **📊 Post-Installation Checklist**

| **Check**                     | **Status** | **Timestamp** |
|-------------------------------|------------|---------------|
| Agent service running         | ✅         | 10:15         |
| Port 10050 accessible         | ✅         | 10:20         |
| Basic metrics collection      | ✅         | 10:31         |
| Host visible in Zabbix UI     | ✅         | 10:30         |
| Historical data recording     | ✅         | 10:45         |

---

**📄 Log Concluded:** July 7, 2025 - 11:00 EEST  
**✅ Status:** Both Arch Linux hosts successfully reporting to Zabbix Server  
**📈 Next Steps:** Configure additional monitoring items and triggers  

---
🐧 **Arch Linux + Zabbix = Perfect Monitoring Combo!**
