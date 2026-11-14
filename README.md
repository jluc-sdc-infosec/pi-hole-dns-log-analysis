# pi-hole-dns-log-analysis
A home lab deployment of Pi-hole to analyze network DNS logs, monitor blocked domain traffic, and establish baseline network visibility ahead of a planned Wazuh SIEM integration.

## Table of Contents
- [Homelab Architecture](#homelab-architecture)
- [Risk Acceptance & Trade-Offs](#risk-acceptance--trade-offs)
- [View Reports](./dns-analysis)
## Homelab Architecture

- **Core Service:** Pi-hole deployed as a Rootful container managed via Podman Quadlets (`systemd`)
- **Host OS:** openSUSE Leap 16.0, managed remotely via Tailscale SSH and Cockpit.
- **Host Specs:** Headless HP Pavilion 15 Notebook PC (Intel Core i7-5500U @ 2.40GHz, 12GB RAM, 240GB storage).
- **Network Role:** Primary network-wide DNS sinkhole at static IP 192.168.0.198 (distributed via TP-Link LAN DHCP), handling query logging, ad/tracker filtering, and per-client IP attribution.
- **Log Ingestion & Analysis:** [View Reports](./dns-analysis)

## Risk Acceptance & Trade-Offs

- **Operational Requirement:** Pi-hole is intentionally deployed as a rootful container to preserve true client source IP addresses in the DNS query logs.
- **Identified Risk:** Running a rootful container violates the Principle of Least Privilege, expanding the attack surface and increasing the risk of host compromise on the openSUSE system if the Pi-hole web interface or DNS service is exploited.
- **Compensating Controls:** To mitigate this risk -
  
   -    Port forwarding (Virtual Servers) for DNS (TCP/UDP 53) and administrative ports is disabled on the TP-Link router behind the ISP modem,
        preventing inbound traffic from reaching the local 192.168.0.0/24 subnet via NAT.
        <img width="1148" height="627" alt="2026-11-11-233144_hyprshot" src="https://github.com/user-attachments/assets/dd487a2b-3603-4b37-9295-1f9432e5e71b" />


   -    Host Layer (firewalld on openSUSE Leap): The host firewall operates in the public zone with default-deny
        inbound filtering (target: default), permitting only local LAN management and DNS services (dns, ssh, cockpit, and dhcpv6-client)
        on interface eno1 while dropping all other unwanted traffic.
        Additionally, the tailscale0 interface operates in the trusted zone (target: ACCEPT),
        which is utilized exclusively for remote SSH administration via Tailscale private network.

| GUI Verification (Cockpit) |  CLI Audit (`firewalld`)|
| :---: | :---: |
| <img alt="Cockpit Firewall UI" src="https://github.com/user-attachments/assets/08cb7ff9-dcae-4a60-bfa5-631a37fc64ba" /> | <img alt="firewalld CLI audit" src="https://github.com/user-attachments/assets/460bf332-0737-41d3-b1fd-a2097d88d708" /> |
 
   -    Container Isolation (SELinux): SELinux is enabled in permissive mode with private volume labels (:Z),
        isolating Pi-hole's data folders and logging any unauthorized file access attempts to the host audit logs.  

        ```ini
        [Unit]
        Description=Pi-hole DNS Adblocker
        After=network-online.target

        [Container]
        Image=docker.io/pihole/pihole:latest
        ContainerName=pihole
        Environment=FTLCONF_webserver_api_password=********
        Environment=TZ=UTC
        Environment=FTLCONF_LOCAL_IPV4=192.168.0.198
        Environment=DNSMASQ_LISTENING=all
        PublishPort=53:53/tcp
        PublishPort=53:53/udp
        PublishPort=80:80/tcp
        Volume=/var/lib/pihole/etc_pihole:/etc/pihole:Z
        Volume=/var/lib/pihole/etc_dnsmasq.d:/etc/dnsmasq.d:Z

        AutoUpdate=registry

        [Service]
        Restart=always
        TimeoutStartSec=900

        [Install]
        WantedBy=multi-user.target default.target
        ```

        <img width="458" height="223" alt="screenshot_20261111_235812-annotated" src="https://github.com/user-attachments/assets/dc77f614-e782-4467-9085-5b364749bea6" />

- [Back to top](#pi-hole-dns-log-analysis)


<!---
try adding the drop capability
if it breeaks, document it, 
fix it, document it,
and steps on how to fix it.

<!---
192.168.0.100 = 
192.168.0.110
192.168.0.106
192.168.0.111
192.168.0.120 = smart tv
192.168.0.107 =
192.168.0.102 --->
