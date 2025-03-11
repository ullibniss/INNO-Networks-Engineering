# INR Lab 6: MPLS

## Completed by Fedorov Alexey (tg: @ullibniss)

---

# Task 1 - Prepare your network topology

## 1.1 In the GNS3 project, select and install a virtual routing solution that you would like to use: for example, Mikrotik (recommended), Pfsense, vyos.

I use MikroTik.

## 1.2 Prepare a simple network consisting of at least three router and two hosts. Each one of them has a different subnet, and the routers should be able to reach each other (for example, a bus topology with dynamic routing). Your network must have routing protocols configured. That's why you can use your OSPF lab project.

I created a BUS topology and preconfigured OSPF.

![image](https://github.com/user-attachments/assets/36b09b98-4401-4b1d-896e-a80a7dad2c9d)

Let's check connectivity.

![image](https://github.com/user-attachments/assets/95c21844-ba46-4558-9329-f6c4e9cbbc2f)

Routers configurations:

![image](https://github.com/user-attachments/assets/9272a801-ba47-4464-95be-871126ba728a)

![image](https://github.com/user-attachments/assets/2f454a8c-78d3-435a-8fb7-d62765e0d2eb)

![image](https://github.com/user-attachments/assets/48f8eb7a-5dc5-46b2-917d-3e8eca56e279)

# Task 2 - MPLS learning & configuring

## 2.1 Briefly answer the questions or give one-line description what is it: LSP, VPLS, PHP, LDP, MPLS L2VPN, CE-router, PE-router?

**MPLS (Multiprotocol Label Switching)** - A high-performance networking technology that uses short, fixed-length labels to route data between nodes and speeding up traffic flow. It establishes end-to-end circuits (LSPs) capable of handling diverse traffic types, such as IP packets, while efficiently managing Traffic Engineering, including bandwidth allocation and Quality of Service (QoS). Widely adopted in enterprise and service provider networks, MPLS is instrumental in building Virtual Private Networks (VPNs), enhancing traffic management, and optimizing overall network performance.

**LSP (Label Switched Path)** - A predefined route in an MPLS network where labeled packets are forwarded from a source to a destination, established using protocols like LDP or RSVP.

**LDP (Label Distribution Protocol)** - A protocol in MPLS networks that maps network layer (IP) addresses to MPLS labels, enabling label-switched paths.

**VPLS (Virtual Private LAN Service)** - A Layer 2 VPN service that connects multiple customer sites to function as if they are on the same LAN, regardless of location. An alternative, **VPWS (Virtual Private WAN Service)**, achieves similar functionality but for point-to-point connections.

**PHP (Penultimate Hop Popping)** - An MPLS technique where the second-to-last router removes the MPLS label, sending the packet to the egress router with only the IP header. This avoids using the Explicit Null label (label = 0) and instead uses Implicit Null (label = 3) when QoS at the MPLS level is unnecessary, as the Traffic Class field is lost after label removal.

**CE-router (Customer Edge Router)** - A router on the customer’s premises that connects to the service provider’s network, routing traffic between the customer’s internal network and the provider’s network. It is unaware of the MPLS network and connects to a PE-router.

**PE-router (Provider Edge Router)** - A router at the edge of the service provider’s network that connects to customer edge devices, serving as the gateway to the MPLS network. Also known as **LER (Label Edge Router)**.

**L2VPN** - A service offering Layer 2 connectivity over an MPLS network, enabling Ethernet, Frame Relay, or ATM services across geographically dispersed sites.

## 2.2  Configure MPLS domain on your OSPF network, first without authentication. 

I configured MPLS using LDP - dynamically, with the following commands:

```
mpls ldp add afi=ip lsr-id=10.10.10.10 transport-addresses=10.10.10.10
mpls ldp interface add interface=ether1
```

Routers configurations:

![image](https://github.com/user-attachments/assets/38d60fb1-33be-4e3c-8bd8-dc9c05c59ab9)

![image](https://github.com/user-attachments/assets/a26a6182-78b2-4abd-b41c-dda1169414b9)

![image](https://github.com/user-attachments/assets/ebee22a9-51a0-4bbb-b77d-e55538c36c0d)

Let's check whether it works:

```
mpls ldp remote-mapping/ print
```

![image](https://github.com/user-attachments/assets/322a3124-cb61-4a06-a342-daa2cd0f8f6e) 

We can see that MPLS works.

## 2.3 Enable authentication (what kind of authentication did you use)? Make sure that you can ping and trace all your network.

I will use OSPF MD5 authentication because there is no authentication support in MPLS. I reconfigured the `interface-template` for every interface on every router.

```
/routing ospf interface-template set numbers=0,1,2,3 auth=md5 auth-id=1 auth-key=P@773ord
```

Configurations:

![image](https://github.com/user-attachments/assets/29c0cb14-4e49-43d7-b8f3-54139b344b99)

![image](https://github.com/user-attachments/assets/bd727931-8183-4e29-9c3c-8530f732e231)

![image](https://github.com/user-attachments/assets/ed7038b6-d0d1-489a-b2c9-7682235f517a)

Let's make some tests.

### Test: Check whether authentication works and headers presents

![image](https://github.com/user-attachments/assets/fcb080a8-0968-499f-a43b-fcb99648550c)

Everything works!

### Test: Trying to ping all network

![image](https://github.com/user-attachments/assets/39c8fe8a-8371-4d9e-96fb-74b381dc0aa6)

I tested connectivity from `host-3` to all other instances. This is enough to say that the network works!

# Task 3 - Verification

## 3.1. Show your LDP neighbors.

I will use the following command to show all neighbors:

```
mpls ldp neighbor print
```

![image](https://github.com/user-attachments/assets/558fdf87-f604-402b-99d3-d5fb96d3bcab)

![image](https://github.com/user-attachments/assets/91393922-6056-4c25-863a-3f1e9a4fe6ab)

![image](https://github.com/user-attachments/assets/95ab6903-693c-49f9-8a71-c17553f56c15)

Everything corresponds to the topology!

## 3.2 Show your local LDP bindings and remote LDP peer labels.

I will use the following commands to show LDP local bindings and remote peer labels:

```
mpls ldp local-mapping print
mpls ldp remote-mapping print
```

![image](https://github.com/user-attachments/assets/20d76cee-fc5b-4375-9180-5229348311c0)

![image](https://github.com/user-attachments/assets/92008921-2d8d-40ef-8b75-58de13828b96)

![image](https://github.com/user-attachments/assets/ee707d81-f5b5-4bf8-bbb3-43431cdc4343)

## 3.3 Show your MPLS labels.

I have taken the label from the tables in the previous subtask.

Label: `impl-null`, `16`, `17`, `18`, `19`, `20`, `21`

## 3.4 Show your forwarding table.

I will use the following command to show the forwarding table:

```
mpls forwarding-table print
```

![image](https://github.com/user-attachments/assets/5970bf16-c901-4d13-b23f-32abcdce3ec7)

![image](https://github.com/user-attachments/assets/62e82e87-43c2-40b7-a17a-7b19eb644491)

![image](https://github.com/user-attachments/assets/df85d7c9-24f3-4df4-9472-f19cd1c47758)

## 3.5 Show your network path from one customer edge to the other customer edge.

I will use the `traceroute` tool to show the path.

### Route `router-3 (customer ether1)` -> `host-1 (e0)`

![image](https://github.com/user-attachments/assets/e86b56a7-9485-446c-9706-3c05ac7a8b9e)

MPLS is functioning as expected, with label 16 being used to forward packets from `router-3` to `router-2` en route to 10.10.0.1. Upon receiving the packet, `router-2` removes the MPLS header and forwards it directly to `router-1`, which then delivers the traffic to `host-1` using IP.

# Task 3 - MPLS packets analysis

## 3.1 Can you use Wireshark to see the MPLS packets?

Yes, I can. Let's do it.

I decided to start capturing between `router-2` and `router-3`. I will make a request from `host-4` to `host-2`.

![image](https://github.com/user-attachments/assets/8973cfc0-0626-4512-a4a2-a30cb4ccbe44)

We can see some MPLS headers:

- MPLS Label = 16, because it must be sent to `router-1`.
- MPLS TTL (Time to Live) = 63 (default is 64, but one hop is done).
- MPLS Bottom of Label Stack = 1, meaning it is the last label in the stack.

## 3.2 Look deeper into the MPLS packets: can you identify MAC address, ICMP, Ethernet header or something else useful?

Yes, we can, because an MPLS packet is a default packet with one additional header (MPLS Header).

![image](https://github.com/user-attachments/assets/2534acc5-c3b5-474d-ba59-641b643d7cad)

# Task 4 - VPLS

## 4.1 Configure VPLS between the 2 hosts edges.

```
Hint: you should connect the hosts connected to the router in one subnet to the router from the
second subnet without changing the topology physically. To do this try to configure a VPLS that
runs on L2.
```

I will connect `router-1` and `router-3`. I need to reconfigure `router-1` and `router-3` to deploy VPLS:

![image](https://github.com/user-attachments/assets/08374dcf-363e-4527-ad68-5611e80b8590)

![image](https://github.com/user-attachments/assets/21b9d80b-8000-490d-b324-c7188fe00465)

## 4.2 Show your LDP neighbors again, what has been changed?

Let's check LDP neighbors again.

![image](https://github.com/user-attachments/assets/254bf4be-fe3d-4339-a3e1-e75a71e2664d)

![image](https://github.com/user-attachments/assets/d64706fc-713d-4631-b7f9-209d47a14555)

It works! `router-1` and `router-3` became neighbors! The flags `DOtv` mean the LDP Hello message will be sent exactly from `router-1` to `router-3` and back.

## 4.3 Find a way to prove that the two customers can communicate at OSI layer 2.

I can use ping with the MAC address. To make a request from `router-1` to `router-3`, I need to know `router-3`'s VPLS interface MAC address.

![image](https://github.com/user-attachments/assets/66a084a7-2636-4de3-85ea-668d5e36bf29)

And the same thing applies to make a reverse ping.

![image](https://github.com/user-attachments/assets/aa58f33b-05f9-44e4-9172-96f0ac4be4e3)

Let's take a look in Wireshark. I will capture traffic between `router-1` and `router-2`.

![image](https://github.com/user-attachments/assets/746dab2f-3bfd-4899-bc61-d3dbc27fc9f2)

We can see two labels: label 20 (MPLS) and label 22 (VPLS).

## 4.4 Is it required to disable PHP? Explain your answer.

It may be necessary to ensure that QoS information in MPLS networks is not lost when packets are forwarded from the penultimate hop to the egress router. This is because the QoS type is typically stored in the MPLS header. However, penultimate hop routers, when performing Penultimate Hop Popping (PHP), remove the MPLS label and forward the packet based on the IP header without inspecting the IP header's QoS markings. As a result, the QoS information in the MPLS header is lost, which can affect traffic prioritization and handling at the egress router. To preserve QoS, the MPLS label must remain intact until the packet reaches the egress router, ensuring that the QoS information is retained and processed correctly.

# References

- https://en.wikipedia.org/wiki/Multiprotocol_Label_Switching
- https://help.mikrotik.com/docs/spaces/ROS/pages/40992794/MPLS+Overview
- https://habr.com/ru/articles/169103/
- https://help.mikrotik.com/docs/spaces/ROS/pages/9863229/OSPF
