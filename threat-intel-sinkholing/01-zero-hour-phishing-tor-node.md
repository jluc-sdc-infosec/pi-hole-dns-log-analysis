# Threat Intel & DNS Sinkholing: User-Reported Phishing Link

## 1. Incident Summary
* **Date:** November 22, 2026
* **Source:** External user-reported link (forwarded by a friend for verification; not from Pi-hole query logs)
* **Verdict:** **True Positive** — Malicious Phishing Infrastructure
* **Finding:** The domain scored **0/92** clean on VirusTotal because it was newly registered, but its resolved IP scored **100% critical** on AbuseIPDB as an active **Tor Exit Node**.

---

## 2. Indicators of Compromise (IOCs)

| Type | Defanged Value | Findings |
| :--- | :--- | :--- |
| **Domain** | `dunkin-lottery[.]com` | **VirusTotal:** `0/92` clean (Zero-hour domain) |
<img width="1775" height="867" alt="image" src="https://github.com/user-attachments/assets/03fbce08-94ce-4aba-a3a5-c1e973defb72" />

| **Resolved IP** | `104[.]21[.]30[.]167` | **AbuseIPDB:** `100%` Confidence Score — **Tor Exit Node** |

| **ISP / Usage** | `Network for Tor-Exit traffic.` | Dedicated **Tor Exit Node** infrastructure | |

---

## 3. Triage & Key Takeaway
1. **Safe Enrichment:** Checked the defanged domain and resolved IP passively via VirusTotal, AbuseIPDB, and WHOIS without visiting the URL.
2. **Why `0/92` Failed:** Newly registered domains haven't been flagged by antivirus vendors yet. Pivoting to the underlying IP caught the Tor exit node infrastructure immediately.

---

## 4. Evidence

* **VirusTotal (`0/92`):**
 <img width="1775" height="867" alt="2026-11-22-213322_hyprshot" src="https://github.com/user-attachments/assets/1b3dd861-63bf-4840-a8c5-79935ac88ae3" />

* **AbuseIPDB (`100%` Tor Exit Node):**
<img width="1735" height="813" alt="2026-11-22-213618_hyprshot" src="https://github.com/user-attachments/assets/5411b523-b371-42a7-b3c7-a7ac591db70a" />

* **Pi-hole Blacklist (DNS Sinkhole):**
 <img width="992" height="346" alt="image" src="https://github.com/user-attachments/assets/c90f4264-02de-48d1-a366-c04dd269de7b" />


---

## 5. Containment
* Warned the recipient not to open the link.
* Added `dunkin-lottery[.]com` to Pi-hole's local blacklist so any local query resolves to `0.0.0.0`.
