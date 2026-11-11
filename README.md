# pi-hole-dns-telemetry-analysis
A home lab deployment of Pi-hole to analyze network DNS logs, monitor blocked domain traffic, and establish baseline network visibility ahead of a planned Wazuh SIEM integration.


## Homelab Architecture

* **Core Service:** Pi-hole deployed as a container managed via Podman Quadlets (`systemd`)
* **Host OS:** openSUSE Leap 16.0, managed remotely via Tailscale SSH and Cockpit.
* **Host Specs:** Headless HP Pavilion 15 Notebook PC (Intel Core i7-5500U @ 2.40GHz, 12GB RAM, 240GB storage).
* **Network Role:** Primary network-wide DNS sinkhole at static IP 192.168.0.198, handling query logging and ad/tracker filtering.
* **Log Ingestion & Analysis:** 

## Risk Acceptance & Trade-Offs

* **Operational Requirement:** Pi-hole is intentionally deployed as a rootful container to preserve true client source IP addresses in the DNS query logs
* **Identified Risk:** Running a rootful container violates the Principle of Least Privilege, expanding the attack surface and increasing the risk of host compromise on the openSUSE system if the Pi-hole web interface or DNS service is exploited.
* **Compensating Controls:** To mitigate this risk -
  
   *    Perimeter Layer (Edge Router):  Port forwarding for DNS (TCP/UDP 53) and administrative ports is disabled on the edge router, preventing external WAN exposure via NAT.

   *    Host Layer (firewalld on openSUSE Leap): The host firewall operates in the public zone with default-deny
        inbound filtering (target: default), permitting only essential management and DNS services (dns, ssh, cockpit, and dhcpv6-client)
        on interface eno1 while dropping all other unsolicited traffic.
        Additionally, the tailscale0 interface is assigned to the trusted zone (target: ACCEPT),
        which is utilized exclusively for remote SSH administration over the encrypted Tailscale mesh network.


     







<!---
192.168.0.100 = 
192.168.0.110
192.168.0.106
192.168.0.111
192.168.0.120 = smart tv
192.168.0.107 =
192.168.0.102 --->
