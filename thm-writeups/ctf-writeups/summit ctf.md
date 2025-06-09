Objective  
After participating in one too many incident response activities, PicoSecure has decided to conduct a threat simulation and detection engineering engagement to bolster its malware detection capabilities. You have been assigned to work with an external penetration tester in an iterative purple-team scenario. The tester will be attempting to execute malware samples on a simulated internal user workstation. At the same time, you will need to configure PicoSecure's security tools to detect and prevent the malware from executing.

Following the Pyramid of Pain's ascending priority of indicators, your objective is to increase the simulated adversaries' cost of operations and chase them away for good. Each level of the pyramid allows you to detect and prevent various indicators of attack.

Room Prerequisites  
Completing the preceding rooms in the Cyber Defence Frameworks module will be beneficial before venturing into this challenge. Specifically, the following:

- The Pyramid of Pain  
- MITRE

Connection Details  
Please click Start Machine to deploy the application, and navigate to https://10-10-189-101.p.thmlabs.com once the URL has been populated.

Note: It may take a few minutes to deploy the machine entirely. If you receive a "Bad Gateway" response, wait a few minutes and refresh the page.

---

## Walkthrough and Flags

### 1. sample1.exe – Hash-Based Detection  
Once sample1.exe is uploaded and analyzed, the MD5 hash of the sample is extracted and added to the hash blocklist. This approach stops future executions of the exact same binary by flagging the file hash. This is the simplest and least resilient detection method, sitting at the bottom of the Pyramid of Pain.

**Flag:**  
`THM{f3cbf08151a11a6a331db9c6cf5f4fe4}`

---

### 2. sample2.exe – IP-Based Detection (Egress Firewall Rule)  
In this case, adding the file’s hash to the blocklist fails to prevent activity, meaning the attacker has evaded hash-based detection. Upon analyzing the behavior of the malware, the executable reaches out to the destination IP `154.35.10.113`.

To stop this, an egress firewall rule is configured as follows:

- Type: egress  
- Source IP: any  
- Destination IP: 154.35.10.113  
- Action: deny

![](../screenshots/Pasted%20image%2020250531175920.png)

**Flag:**  
`THM{2ff48a3421a938b388418be273f4806d}`

---

### 3. sample3.exe – DNS-Based Detection  
Here, neither file hash nor IP-based blocking works. Instead, the malware uses a domain name to reach its C2 infrastructure. The destination domain name must be blocked through DNS filtering. After analyzing the network behavior, the malicious domain is identified and added to the blocklist.

![](../screenshots/Pasted%20image%2020250531185335.png)

**Flag:**  
`THM{4eca9e2f61a19ecd5df34c788e7dce16}`

---

### 4. sample4.exe – Host Artifact Detection (Sigma Rule Creation)  
For this sample, blocking by hash, IP, and domain all fail. Instead, the sample makes changes to the host system itself, such as dropping files or changing registry keys. These are persistent artifacts left by the malware.

To detect this, you must:

- Analyze changes to the system  
- Craft a Sigma rule based on those artifacts  
- Deploy the detection rule to catch similar behavior in the future

![](../screenshots/Pasted%20image%2020250531190430.png)

**Flag:**  
`THM{c956f455fc076aea829799c0876ee399}`

---

### 5. sample5.exe – Log-Based Behavior Detection  
At this stage, the attacker is no longer relying on hash, IP, domain, or obvious artifacts. The only clue is in network behavior over time.

Careful analysis of logs reveals a recurring pattern: a 97-byte packet sent every 30 minutes. This periodic communication is a strong indicator of beaconing behavior typically used by malware for C2 check-ins.

Detection requires correlating log events and recognizing this abnormal, consistent behavior.

![](../screenshots/Pasted%20image%2020250531190943.png)  
![](../screenshots/Pasted%20image%2020250531193109.png)

**Flag:**  
`THM{46b21c4410e47dc5729ceadef0fc722e}`

---

### 6. Final Challenge – Sphinx  
This final sample cannot be caught by any previous static or signature-based detection. It likely requires heuristic or behavior-based detection combined with insights from the logs, system events, and potentially real-time monitoring. The analysis must be comprehensive and correlate multiple data sources to reach detection.

![](../screenshots/Pasted%20image%2020250531193237.png)  
![](../screenshots/Pasted%20image%2020250531193426.png)

**Flag:**  
`THM{c8951b2ad24bbcbac60c16cf2c83d92c}`