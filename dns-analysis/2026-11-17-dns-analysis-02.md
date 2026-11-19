## Data Enrichment

* **Client IP Observed:** `192.168.0.101`
* **Alert Observation:** Unusual amount of blocked DNS queries (`1,032`) on `192.168.0.101` compared to peer devices on the network.
<img width="482" height="356" alt="2026-11-16-230417_hyprshot" src="https://github.com/user-attachments/assets/a8d1e023-24db-4a3a-b957-3af130c461c3" />

### Top Blocked Domains Observed
* `crashlyticsreports-pa.googleapis[.]com` — Google crash reporting
* `firebaselogging-pa.googleapis[.]com` — Google Firebase telemetry
* **Parent Domain:** `googleapis[.]com`
* **Owner:** Google LLC
* **VirusTotal Result:** `0/92` security vendors flagged the domains as malicious.

Note: I know its google, but i still checked on VirusTotal, i see some nasty typosquatting sometimes.
### Baseline & Record Type Analysis
* **7-Day Baseline:** `2,861` blocked entries over the last 7 days (`408` blocked queries/day average) vs. `1,032` blocked queries today.
 
  <img width="387" height="42" alt="2026-11-16-232212_hyprshot" src="https://github.com/user-attachments/assets/c43ad5f8-197f-499a-b78a-81e88dfb6a48" />

 **DNS Record Types:** All blocked queries are standard `A` records—no `NULL` or `TXT` records observed (rules out DNS tunneling).

 <img width="991" height="654" alt="2026-11-16-231223_hyprshot" src="https://github.com/user-attachments/assets/ec6b4ea4-6328-4cab-9976-9b313309ac20" />
<img width="977" height="800" alt="2026-11-16-231244_hyprshot" src="https://github.com/user-attachments/assets/6b0b133f-a4b6-4e62-8778-b9c9018cd072" />

### Conclusion / Action
Closed as **Benign True Positive**. All traffic is legitimate mobile app telemetry, left blocked on Pi-hole for privacy/ad-filtering. No further escalation needed.
