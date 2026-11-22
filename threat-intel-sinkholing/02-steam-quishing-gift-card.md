# Threat Intel & DNS Sinkholing: Steam QR Code Phishing ("Quishing")
Proactive OSINT enrichment of external phishing lures (`VirusTotal` + `AbuseIPDB`) and wildcard DNS sinkholing via Pi-hole.

## 1. Incident Summary
* **Date:** November 22, 2026
* **Source:** External QR code image lure sent via a compromised Steam friend account (not from Pi-hole query logs)
* **Verdict:** **True Positive** — Typosquatted Phishing & Account Takeover Lure
* **Finding:** Decoded the QR code offline (`zbarimg`), revealing a typosquatted domain (`steamcomrnuunity[.]com` — `rn` + `uu`). While its hosting IP scored **0%** on AbuseIPDB, **23/93** vendors on VirusTotal flagged the domain as malicious phishing.

---

## 2. Indicators of Compromise (IOCs)

| Type | Defanged Value | Findings |
| :--- | :--- | :--- |
| **Lure URL** | `hxxps://steamcomrnuunity[.]com/code/[REDACTED]` | Tracking ID stripped; typosquatted (`rn` instead of `m`) |
| **Domain** | `steamcomrnuunity[.]com` | **VirusTotal:** `23/93` vendors flagged as malicious/phishing |
| **Resolved IP** | `62[.]60[.]226[.]75` | **AbuseIPDB:** `0%` Confidence Score (clean data center / proxy IP) |
| **ISP / Usage** | `FEMO IT SOLUTIONS LIMITED` | Data Center / Web Hosting / Transit |

---

## 3. Triage & Key Takeaway
1. **Offline QR Extraction:** Captured a region screenshot of the QR lure and decoded it locally using `zbarimg --raw` to avoid network exposure or mobile auto-redirects.
2. **Why `0%` AbuseIPDB is still not Safe:** AbuseIPDB tracks noisy network abuse (scanning/brute-force), not web phishing pages hosted on newly provisioned cloud servers. Combining domain reputation (VirusTotal) with IP reputation (AbuseIPDB) prevented a false-negative closure.
3. **Why Attackers Used a QR Code:** Embedding the typosquatted URL inside an image bypasses Steam Chat's text link warnings, pushes victims onto smaller mobile screens where rn looks like m, and takes advantage of QR scanners that auto-open or obscure full URL previews.
4. **Always Zero Trust, in this case, verify it twice**
---

## 4. Evidence
* **Defanged QR Lure (Corner obscured so it cannot be scanned):**
  <img width="615" height="392" alt="screenshot_20261122_222220-annotated" src="https://github.com/user-attachments/assets/c739df9e-cbea-406b-9548-c195aafb9c11" />

* **VirusTotal (`23/93` Malicious):**
<img width="1844" height="796" alt="image" src="https://github.com/user-attachments/assets/d67596a9-5226-4336-91d1-f198bac6a457" />

* **AbuseIPDB (`0%` Data Center IP):**
 <img width="1745" height="565" alt="image" src="https://github.com/user-attachments/assets/928d0b70-0e73-401c-9731-29836fc0903c" />

* **Pi-hole Blacklist (Wildcard DNS Sinkhole):**
 <img width="1010" height="389" alt="image" src="https://github.com/user-attachments/assets/70708958-d794-46bc-8414-058be8e1758f" />


---

## 5. Containment
* Reported the compromised friend's Steam account to Steam Support.
* Added `steamcomrnuunity[.]com` as a wildcard denied domain in Pi-hole (`0.0.0.0`).
