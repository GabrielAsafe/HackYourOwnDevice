# 🧠 NSE Script Categories (what they *all* do)

You can see this yourself with:

```bash
nmap --script-help "*"
```

Here’s the meaningful breakdown:

---

## 🔹 1. `default`

* Safe scripts that run automatically with `-sC`
* Basic enumeration
* Examples:

  * `banner.nse` → grabs service banners
  * `ssl-cert.nse` → gets SSL cert info

👉 Use when you want quick, useful info without noise

---

## 🔹 2. `safe`

* Non-intrusive, unlikely to crash anything
* Info gathering only

Examples from your list:

* `http-title.nse` → page title
* `ssh-hostkey.nse` → SSH keys
* `smtp-commands.nse` → SMTP capabilities

👉 Best for **production environments**

---

## 🔹 3. `discovery`

* Finds hosts, services, or network info

Examples:

* `broadcast-dhcp-discover.nse`
* `dns-brute.nse`
* `targets-traceroute.nse`

👉 Used for **mapping networks**

---

## 🔹 4. `version`

* Enhances service/version detection

Examples:

* `http-server-header.nse`
* `ssh2-enum-algos.nse`

👉 Works with `-sV`

---

## 🔹 5. `auth`

* Authentication-related checks

Examples:

* `ftp-anon.nse` → anonymous login
* `http-auth.nse`

👉 Finds weak or misconfigured auth

---

## 🔹 6. `brute`

⚠️ Aggressive category

* Performs brute-force attacks

Examples:

* `ssh-brute.nse`
* `ftp-brute.nse`
* `mysql-brute.nse`

👉 Can lock accounts or trigger alerts

---

## 🔹 7. `vuln`

* Checks for known vulnerabilities

Examples from your list:

* `smb-vuln-ms17-010.nse` (EternalBlue)
* `ssl-heartbleed.nse`
* `http-shellshock.nse`

👉 Very useful for security audits

---

## 🔹 8. `exploit`

⚠️ Highly intrusive

* Attempts actual exploitation

Examples:

* `smb-psexec.nse`
* `http-fileupload-exploiter.nse`

👉 Only for **lab or authorized pentests**

---

## 🔹 9. `malware`

* Detects malware/backdoors

Examples:

* `p2p-conficker.nse`
* `irc-botnet-channels.nse`

---

## 🔹 10. `intrusive`

⚠️ Can disrupt services

* Heavy scans, fuzzing, DoS-like behavior

Examples:

* `http-slowloris.nse`
* `broadcast-avahi-dos.nse`

---

## 🔹 11. `broadcast`

* Uses broadcast traffic to find devices

Examples:

* `broadcast-ping.nse`
* `broadcast-upnp-info.nse`

👉 Good for **LAN discovery**

---

## 🔹 12. `external`

* Uses third-party services

Examples:

* `whois-ip.nse`
* `ip-geolocation-*`

👉 May send data externally

---

## 🔹 13. `fuzzer`

* Sends malformed input to test services

Example:

* `http-form-fuzzer.nse`

---

## 🔹 14. `dos`

⚠️ Dangerous

* Denial-of-service testing

Example:

* `broadcast-avahi-dos.nse`

---

# 🧩 Practical takeaway

Instead of memorizing all scripts, use categories:

### 🔍 Typical workflows

**Safe recon**

```bash
nmap -sV --script "default,safe" target
```

**Deep audit**

```bash
nmap -sV --script "default,safe,vuln" target
```

**Network discovery**

```bash
nmap --script discovery target
```

---

# 🔎 Want to explore specific scripts?

You can inspect any script like this:

```bash
nmap --script-help smb-vuln-ms17-010
```

---
