# Wireless Security and Penetration Testing Report

## 1. Abstract
The most common feature used today is a wireless network. Wireless networks are often exposed to attack and unauthorized access. Ethical hacking techniques for evaluating the security of Wi-Fi will also help identify vulnerabilities of Wi-Fi. 

In the evaluation, tools such as **Wireshark** and **Aircrack-ng** were used to crack WPA2 passwords, analyze traffic, and perform physical attacks against the access point and users through **ARP poisoning** techniques and **rogue access points**.

The evaluation confirmed that the vast majority of wireless networks are at risk because of weak security controls. Internet connectivity has the potential to cause great harm to the organizational wireless infrastructure and all user devices connected to it. Therefore, wireless environments must be secured through the use of strong passwords, robust encryption standards, and adequate monitoring.

---

## 2. Introduction
Due to their ability to give flexible and mobile access, wireless networks are important for contemporary businesses. On the other hand, because wireless networks are supported through radio signals, anyone can intercept a wireless transmission from a nearby location, which makes them much easier to attack and gain unauthorized access to.

Attackers can capture sensitive information by exploiting poor user authentication, weak encryption methods, and misconfigured wireless access points. To determine the security posture of a wireless network, ethical hackers and pen testers perform controlled penetration tests.

The purpose of this lab exercise is to demonstrate the ability to perform several wireless hacking techniques to obtain access to a Wi-Fi network without authorization. The following attacks were performed:

* **Wireshark:** Packet Analysis
* **WPA2 Network Cracking:** Using Aircrack-ng
* **ARP Spoofing/Poisoning Attack** (Man-in-the-Middle)
* **MAC Address Spoofing**

The ultimate purpose of this assessment is to determine whether an unauthorized person can gain access to your wireless network and can capture traffic passing over your network.

---

## 3. Discussion

### 3.1 Wi-Fi Packet Analysis using Wireshark
The data traffic for wireless packet types is monitored by utilizing Wireshark. The `WPA2Crack-01.cap` capture file was analyzed using Wireshark.

The captured packets exposed crucial network details:
* **Wireless Protocol:** IEEE 802.11 frames
* **Encryption Type:** WPA2
* **Source & Destination MAC addresses**
* **BI (Beacon Interval), SSID** of the target Access Point
* **Authentication Frames** (including handshakes)

#### Filtering Traffic
To isolate target details, we utilized Wireshark display filters to target specific management frames:
`![Wireshark Filter Screenshot](images/wireshark_filter.png)`

> **Note:** This filter displays beacon frames and helps identify SSID data. This passive analysis helps attackers obtain network information before conducting active stages of their attack.

---

### 3.2 WPA2 Network Cracking using Aircrack-ng
WPA2 is the most popular method for protecting wireless networks; however, a dictionary attack can enable an attacker to exploit any WPA2 network that has been secured using weak passphrases (passwords). In this challenge, the decoded Wi-Fi password can be identified within a specially formatted handshake file that was captured via the **Aircrack-ng** utility.

The initial step was preparing our terminal interface within a **Kali Linux** workstation. The dictionary wordlist and the target sample capture file were both transferred to the workspace directory.

#### Finding the Capture File
We ran a command to locate and confirm our file paths:

`![Finding File Command](images/locate_pcap.png)`

*This command locates the file path where `WPA2crack-01.pcap` is located.*

#### Cracking the WPA2 Handshake
Using the captured WPA2 4-way handshake, we executed a dictionary-based cracking process using Aircrack-ng:

`![Aircrack Cracking Command](images/aircrack_command.png)`

By executing the command, the tool reads the capture file and tests each password within the wordlist to determine whether the WPA2 handshake matches one of those passwords. Once successful, Aircrack-ng responds with: **`KEY FOUND!`**

This demonstrates how a dictionary or brute-force attack can successfully compromise WPA2 networks utilizing poor password hygiene.

---

### 3.3 ARP Spoofing / Poisoning Attack
An example of a Man-in-the-Middle (MITM) attack is ARP poisoning, where an attacker intercepts communication occurring between the victim’s device and the gateway router.

First, we mapped out the hosts connected to the local subnet using host discovery tools:

`![Host Discovery Command](images/host_discovery.png)`

> **Target Identification:**
> * **Attacker IP/MAC:** `10.3*.**.210`
> * **Victim IP/MAC:** `10.3*.**.213`

Next, we scanned the identified target to check for open ports and active network services:

`![Port Scan Command](images/target_scan.png)`

We then initiated the ARP spoofing loop using targeting commands to redirect the traffic flows:

`![ARP Spoofing Command](images/arp_spoof_command.png)`

The attacker machine successfully redirected communication between the victim's machine and the internet gateway, demonstrating how easy it is to monitor and interrupt communication on an unprotected local area network.

---

### 3.4 MAC Address Spoofing
In many deployments, wireless networks utilize MAC filtering for access control; however, an attacker can bypass this security method simply by spoofing their network interface's MAC address to match an authorized system.

First, we verified our current network interface configuration:

`![Checking MAC Address](images/check_mac.png)`
*(Checks the default MAC address of the interface).*

Next, we disabled the interface to safely allow configuration changes:

`![Bringing Interface Down](images/interface_down.png)`
*(Brings the interface down to prepare for the spoofing command).*

We then spoofed the interface's physical address to mimic the victim’s authorized MAC address:

`![Spoofing MAC Address](images/mac_spoof.png)`
*(Spoofs the MAC of the attacker to match the victim's MAC).*

Finally, we restarted the interface to apply the changes. The successful spoofing demonstrated that basic MAC filtering does not offer a robust level of network protection, as an unauthorized actor can easily imitate a legitimate system's physical address.

---

## 4. Conclusion
This assessment has demonstrated multiple forms of attack vectors against wireless networks:
* **Weak Passwords:** It was fast and easy to retrieve weak WPA2 credentials using dictionary attacks against the captured 4-way handshake.
* **Passive Sniffing:** Wireshark analysis showed how threat actors can capture and analyze raw radio packets to map out network architectures.
* **Access Control Bypass:** MAC spoofing successfully demonstrated how easy it is to defeat MAC filtering configurations.
* **Traffic Hijacking:** ARP poisoning showed how Man-in-the-Middle positions can be established to capture user payloads.

### Recommendations
To mitigate these vulnerabilities, organizations should:
1. Enforce strong, highly complex password policies.
2. Upgrade legacy WPA2 systems to robust **WPA3 Enterprise** configurations.
3. Disable insecure supplementary services such as **WPS** (Wi-Fi Protected Setup).
4. Deploy robust **Wireless Intrusion Detection Systems (WIDS)** to identify unauthorized spoofing and injection behaviors.
5. Routinely conduct professional penetration testing assessments to validate and harden the network perimeter.

---

## 5. Note (Controlled Environment Disclaimer)
These ethical hacking experiments were performed strictly under controlled, educational laboratory conditions. All target endpoints, services, and capture files were simulated within an isolated, local virtual network with DHCP support. 

Because these experiments (including MAC address spoofing, ARP poisoning, and traffic analysis) were conducted inside an offline, isolated sandbox environment, there was no threat or impact to any external network, production system, or real-world users.

*Note: Sensitive, locally assigned identifiers (including internal system IP addresses and specific MAC configurations) have been intentionally omitted or modified in screenshots to maintain environment privacy.*
