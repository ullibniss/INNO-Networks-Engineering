# INR Lab 3: STP, VLAN, and Fault Tolerance

## Done by Fedorov Alexey

---

# Task 1 - Preparation

## 1.{a,b,c,d} Select a virtual switching solution that you would like to try. For example (Mikrotik, vyos, Cumulus VX).

I've read instructions. Decided to use Mikrotik. Made a snapshot.

# Task 2 - VLANs

## 2.a Change the topology of your network to as follows, and make the necessary configurations.

I fully prepared topology:

![image](https://github.com/user-attachments/assets/3811d487-f267-412e-bb97-4d5a890c0199)

## 2.b Configure the switches and make sure you have connectivity between the hosts.

I configured switches, let's ensure connectivity:

`Web` to All:

![image](https://github.com/user-attachments/assets/f74987ad-b6e9-4bcf-ad76-8697799f8079)

`HR` to All:

![image](https://github.com/user-attachments/assets/e96a2755-507c-4ac3-b0ee-b17ccfc8b324)

## 2.c How do VLANs work at a packet level? What are the two main protocols used for this?

### VLAN workflow algorithm:

**Tagging (IEEE 802.1Q)** - When a packet enters a VLAN switch, a VLAN tag is added to the Ethernet frame. The tag includes:

- VLAN ID (**VID**, 12 bits) - Identifies the VLAN (1–4094).

- Priority Code Point (**PCP**, 3 bits) -  Used for QoS (Quality of Service).

- Drop Eligible Indicator (**DEI**, 1 bit) - Marks frames eligible for dropping under congestion.

- Tag protocol identifier (**TPID**, 16 bits) - A field to identify the frame as an IEEE 802.1Q-tagged frame.

![image](https://github.com/user-attachments/assets/f044a6c0-116b-4cc2-bbdf-a51d00d6d757)

**Forwarding** - Switches use the VLAN ID to forward the packet only to ports that belong to the same VLAN.

**Untagging** - When the packet leaves the VLAN-aware switch toward a device that doesn’t understand VLAN tags, the tag is removed.

### Two protocols that is used for VLAN:

- IEEE 802.1Q - The standard protocol for VLAN tagging, which inserts a 4-byte VLAN tag into the Ethernet frame.

- ISL (Inter-Switch Link) - A Cisco-proprietary protocol for VLAN tagging, which encapsulates the entire Ethernet frame with a new header and trailer.

## 2.d What is the Native VLAN?

The **Native VLAN** is a feature in the **802.1Q standard** that assigns a VLAN to handle untagged traffic on a switch. By default, this is **VLAN 1**, but on some switches, it can be reconfigured to another VLAN. The switch automatically assigns the Native VLAN tag to any incoming untagged frames. However, port-based VLANs have a limitation: only one VLAN can process all untagged traffic.

## 2.e Configure the VLANs on the switches to isolate the two virtual networks as follow

I configured vlan as on [picture](https://i.imgur.com/zyoR0V1.png).

![image](https://github.com/user-attachments/assets/a9332fa2-3d9c-44ec-96f1-d24c1805805d)

Admistration configuration for example.

![image](https://github.com/user-attachments/assets/0b99a6c5-df15-4e29-9c14-256868752688)

## 2.f Ping between ITManager and HR, do you have replies? Ping between ITManager and Management, do you have replies? Can you see the VLAN ID in Wireshark?

Let's test configuration. 

### Test: Ping between ITManager and HR (HR -> ITManager)

![image](https://github.com/user-attachments/assets/ffe697ba-7f1f-4354-bf11-227854f198d1)

There no replies, moreover, ITManager not reachable to HR

To see VLAN tag, we need to capture packet betweeb routers? because router adds this header. I will sniff Administration to Internal connection.

![image](https://github.com/user-attachments/assets/f7b7424f-f12e-4798-9d29-b3af76b041ea)

As we can see? ARP frames had IEEE 802.1Q ID = 3 of VLAN.

### Test: Ping between ITManager and Management (ITManager -> Management) 

![image](https://github.com/user-attachments/assets/c156776e-baa7-4c2b-be45-584300b90430)

Connection is present between ITManager and Management.

As we can see in Wireshark, ICMP requests were replied. 

![image](https://github.com/user-attachments/assets/07187379-dc5f-4664-8747-55154b544e05)

## 2.g Configure Inter-VLAN Routing between Management VLAN and HR VLAN and Show that you can now ping between them.

If configured Inter-VLAN on microtic router. I added VLANs on physical interface and connected IP Addresses.

![image](https://github.com/user-attachments/assets/5b0925ef-9ac1-45d9-a8ff-9d54782d84c3)

Next step, I reconfigured PCs IPs.

![image](https://github.com/user-attachments/assets/827ff0e9-301b-4764-ba57-3604e433e366)

### Test: Ping between HR and Management (HR -> Management)

![image](https://github.com/user-attachments/assets/51045ef3-d6d1-466e-9740-013ce7156f77)

It works!

# Task 3 - Fault Tolerance

## 3.a What is Link Aggregation? How does it work (briefly)? What are the possible configuration modes?
  
Link aggregation, defined by IEEE 802.1AX (formerly IEEE 802.3ad), combines multiple links to enhance transmission performance and ensure fault tolerance. These links can be physical connections between the same devices or virtual links using proprietary protocols like Cisco VSS or Nexus VCC. A group of aggregated ports is called a Link Aggregation Group (LAG), also known as a bond, team, or port-channel, with up to 8 links. The scheduling algorithm determines how packets are distributed across links, while the Link Aggregation Control Protocol (LACP) actively monitors and manages link inclusion or removal.
 
LACP operates by exchanging frames (LACPDUs) on all enabled links. When a device detects another LACP-enabled device, they combine their links into a single logical link. LACP has two modes:  
- **Active**: Sends LACPDUs every second.  
- **Passive**: Responds only when LACPDUs are received.  

**Modes:**  
- **Scheduling Algorithm**: Determines packet distribution (e.g., MAC/IP/socket hashing, round-robin).  
- **LACP Mode**: Active (monitors links actively) or passive (responds only). Active mode ensures failed links are removed, but compatibility with non-LACP devices must be considered to avoid mismatched configurations.

## 3.b Use link aggregation between the Web and the Gateway to have Load Balancing and Fault Tolerance as follows.

I added link-aggregation in my topology.

![image](https://github.com/user-attachments/assets/486a23e4-4a75-4dac-babc-ab6e3193c16c)

Without any configuration traffic will go only through one of the wires (old wire with configured interface). We can see it in wireshark.

![image](https://github.com/user-attachments/assets/f8ea010d-94c7-465d-b1fa-1ac0431c949f)

To configure connection I will use `bonding` interfacce. A bonding interface (also known as a link aggregation interface, LAG) is a virtual interface that combines multiple physical network interfaces into a single logical interface. 

Firstly, we need to configure it on Router.

![image](https://github.com/user-attachments/assets/d6363402-c497-4912-82da-9e536107b865)

Balancing in mode `balance-rr` means round-robin algorithm. 

Let's configure Web server. I will use our beloved `netplan`.

![image](https://github.com/user-attachments/assets/823ac154-7052-4903-8841-7bae631bd665)

When everyting is configured, let's test it.

### Test: ping between Web and Router (Web -> Router)

![image](https://github.com/user-attachments/assets/6800e1a4-23f3-42a5-989d-5d02d6992097)

As we can see, balancing works. Interestingly, all ICMP requests consistently travel through the same link, and, as expected, all replies always use the other link.

## 3.c Test the Fault Tolerance by stopping one of the cables and see if you have any downtime.

Let's test Fault Tolerance. Firstly I will start ping, then i will disconnect one wire.

I disconnected `Web e1` <-> `External e4` and `External e3` <-> 'Router ether5'.

![Screenshot from 2025-02-28 08-01-13](https://github.com/user-attachments/assets/105a9dc2-506d-4f0c-9dfa-f6a46ff9155f)

![image](https://github.com/user-attachments/assets/4fd7a537-4eea-4d79-a144-5b835425177c)

Everyting works correct! After disconnection, all traffic went through first connection. Moreover, there were no packet loss!

# References

- https://www.sciencedirect.com/topics/computer-science/virtual-local-area-network-tag
- https://en.wikipedia.org/wiki/VLAN
- https://help.mikrotik.com/docs/spaces/ROS/pages/88014957/VLAN
- https://help.mikrotik.com/docs/spaces/ROS/pages/8323193/Bonding
- https://askubuntu.com/questions/1266154/bonding-ubuntu-20-04

