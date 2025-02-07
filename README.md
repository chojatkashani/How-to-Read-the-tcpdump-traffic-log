# How-to-Read-the-tcpdump-traffic-log


This guide explains how to **identify a brute force attack** using **tcpdump**.

## **Analyzing DNS & HTTP Traffic Logs**

### **Step 1: DNS Resolution Request**
```
14:18:32.192571 IP your.machine.52444 > dns.google.domain: 35084+ A? yummyrecipesforme.com. (24)
14:18:32.204388 IP dns.google.domain > your.machine.52444: 35084 1/0/0 A 203.0.113.22 (40)
```
- The **source computer (`your.machine.52444`)** sends a **DNS resolution request** to **Google’s DNS server (`dns.google.domain`)** to retrieve the IP address for `yummyrecipesforme.com`.
- The **DNS server replies** with the resolved **IP address `203.0.113.22`**.

---

### **Step 2: Connection Request to the Web Server**
```
14:18:36.786501 IP your.machine.36086 > yummyrecipesforme.com.http: Flags [S], seq 2873951608, win 65495, options [mss 65495,sackOK,TS val 3302576859 ecr 0,nop,wscale 7], length 0
14:18:36.786517 IP yummyrecipesforme.com.http > your.machine.36086: Flags [S.], seq 3984334959, ack 2873951609, win 65483, options [mss 65495,sackOK,TS val 3302576859 ecr 3302576859,nop,wscale 7], length 0
```
- The source computer (`your.machine.36086`) **sends a SYN request** (`Flags [S]`) to the destination server (`yummyrecipesforme.com.http`).
- The **destination acknowledges** the request (`Flags [S.]`), reserving system resources for the connection.

### **TCP Flag Codes Reference**
| Flag | Description |
|------|------------|
| **[S]**  | Connection Start |
| **[F]**  | Connection Finish |
| **[P]**  | Data Push |
| **[R]**  | Connection Reset |
| **[.]**  | Acknowledgment |

---

### **Step 3: HTTP Request Sent**
```
14:18:36.786589 IP your.machine.36086 > yummyrecipesforme.com.http: Flags [P.], seq 1:74, ack 1, win 512, options [nop,nop,TS val 3302576859 ecr 3302576859], length 73: HTTP: GET / HTTP/1.1
```
- The **HTTP GET request** (`HTTP: GET / HTTP/1.1`) indicates the browser is **requesting data** from `yummyrecipesforme.com`.
- This could be the **download request** for a **malicious file**.

---

### **Step 4: Suspicious DNS Resolution and Redirect**
```
14:20:32.192571 IP your.machine.52444 > dns.google.domain: 21899+ A? greatrecipesforme.com. (24)
14:20:32.204388 IP dns.google.domain > your.machine.52444: 21899 1/0/0 A 192.0.2.172 (40)
```
```
14:25:29.576493 IP your.machine.56378 > greatrecipesforme.com.http: Flags [S], seq 1020702883, win 65495, options [mss 65495,sackOK,TS val 3302989649 ecr 0,nop,wscale 7], length 0
14:25:29.576510 IP greatrecipesforme.com.http > your.machine.56378: Flags [S.], seq 1993648018, ack 1020702884, win 65483, options [mss 65495,sackOK,TS val 3302989649 ecr 3302989649,nop,wscale 7], length 0
```
- The **DNS request changes**, resolving a different domain (`greatrecipesforme.com` → `192.0.2.172`).
- The **traffic is now being routed to a spoofed website**.
- **New connection established** using port `56378` instead of `52444`.

---

## **Resources for Further Reading**

- [An Introduction to Using tcpdump](https://www.tecmint.com/tcpdump-command-examples/) – Commands and example outputs.
- [tcpdump Cheat Sheet](https://danielmiessler.com/study/tcpdump/) – Options, protocol codes, and filters.
- [What is a Computer Port?](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-port/) – Common ports and their use.
- [Service Name and Transport Protocol Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml) – Official list of registered ports.
- [How to Capture and Analyze Network Traffic with tcpdump](https://linuxhint.com/capture-analyze-network-traffic-tcpdump/) – Step-by-step network traffic analysis.
- [Masterclass: Interpreting tcpdump Output](https://www.sans.org/) – Color-coded guide for tcpdump logs.

---

This guide provides a **detailed breakdown** of **tcpdump logs**, helping **identify network anomalies** such as **brute force attacks, DNS spoofing, and suspicious traffic redirections**.
