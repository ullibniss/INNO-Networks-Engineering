# INR Lab 4

## Done by Fedorov Alexey

---

# Task 1 - Prepare your network topology

## 1.1 In the GNS3 project, select and install a virtual routing solution that you would like to use: Mikrotik (recommended), Pfsense, vyos and so on.

I use MIkrotik, as in previous labs.

## 1.2 Prepare a simple network consisting of at least 3 routers, each one of them has a different subnet, and they should be able to reach each other (for example by a switch/router in the middle or a bus topology). Do not write a static routes between different networks.

I decided to use topology `a`.

![image](https://github.com/user-attachments/assets/75d745e1-f490-4d50-b159-d54c0f025d70)

Let's configure it.

Clients configuration:

![image](https://github.com/user-attachments/assets/f0e9b078-2473-4f0b-a6dc-628a51266114)

![image](https://github.com/user-attachments/assets/ecaa1eec-55da-492c-b4c0-1cbb4e300046)

![image](https://github.com/user-attachments/assets/ec018e1d-6ffc-4487-9113-1f8d9630c394)

![image](https://github.com/user-attachments/assets/65c50354-b993-43a1-ae9e-04d779d8b815)

Routers configuration:

![image](https://github.com/user-attachments/assets/ce8f946f-f05b-43cb-b685-2fad78db5e5f)

![image](https://github.com/user-attachments/assets/f671f2fc-1e6a-4c11-89dc-f686485a05fe)

![image](https://github.com/user-attachments/assets/9c19f91a-40fc-493c-a6d0-f72a8352137f)

![image](https://github.com/user-attachments/assets/d5571e91-a065-4821-877f-59295d6d8ae1)

Final topology:

![image](https://github.com/user-attachments/assets/918fcb2d-a056-4113-bbd2-4d602d16f67a)

# Task 2 - OSPF Learning & Configuring

OSPF (Open Shortest Path First) is a dynamic IP routing protocol designed to identify the most efficient route for data packets. Functioning within a single autonomous system, it employs link-state routing, allowing each router to build a comprehensive topology map of the network. By exchanging link-state advertisements, routers collectively construct this map and determine optimal paths using Dijkstra's algorithm. OSPF offers features such as load balancing, rapid convergence, and hierarchical network organization through the use of areas.

How OSPF Works:

- Link-State Routing - Each router builds and maintains a topological map of the network rather than relying on distance-vector metrics. Routers exchange Link-State Advertisements (LSAs) to share network topology information.
- Dijkstra’s Algorithm - OSPF uses Dijkstra’s Shortest Path First (SPF) algorithm to compute the best routes. Every router calculates the shortest path independently based on the network topology.
- Hierarchical Structure - OSPF networks are divided into areas, reducing overhead and improving efficiency. Backbone Area (Area 0) connects all other areas and ensures seamless routing between them.
- Fast Convergence & Load Balancing - OSPF quickly updates routing tables when network changes occur, reducing downtime. It supports Equal-Cost Multi-Path (ECMP), allowing multiple routes with the same cost to be used simultaneously.

## 2.1 Deploy OSPF in your chosen network topology.

I configured OSPF on the routers by creating an OSPF instance with a unique Router ID, setting up a new area with the Area ID `0.0.0.0`, and assigning interfaces to this area. To accomplish this, I used the following commands:

![image](https://github.com/user-attachments/assets/6adf2393-a98e-42b4-87fc-c6df49289344)

All routers OSPF configurations:

![image](https://github.com/user-attachments/assets/adf7bd4b-96f3-408b-89ed-dfa975b6d731)

![image](https://github.com/user-attachments/assets/575f2a80-0a0f-402f-a470-18ac0108a0c3)

![image](https://github.com/user-attachments/assets/39612738-0858-4061-be5d-d6c679f3d364)

![image](https://github.com/user-attachments/assets/adcc57c3-8ab9-4608-8427-c9cb47f94f91)

Let's check whether it works:

![image](https://github.com/user-attachments/assets/e8775473-c9f7-4618-8d27-01c2f2edad14)

We can connect to any client from client1. Everything works!

## 2.2 Which interface you will select as the OSPF router ID and why? 

In OSPF, the Router ID is a distinct 32-bit identifier assigned to each router to uniquely identify it within the OSPF network. This identifier is crucial for maintaining the integrity of the link-state database, preventing conflicts, and ensuring accurate routing decisions. Since the Router ID must be unique, I assign it based on the IP address of the interface that connects my routers. For instance, for routers 1, 2, and 3, I use an IP address from the `192.168.1.0/24` subnet, while for router 4, I assign an IP from the `192.168.15.1/24` subnet.

## 2.3. What is the difference between advertising all the networks VS manual advertising (per interface or per subnet)? Which one is better?

In OSPF, choosing between advertising all networks or manually defining individual networks depends on the network architecture, the level of administrative control required, and the desired routing efficiency.

## 2.4 If you have a static route in a router, how can you let your OSPF neighbors know about it? Approve and show it on practice.

To inform OSPF neighbors about a static route, it must be redistributed into OSPF. Redistribution enables routes obtained from other sources, such as static routes, to be shared within the OSPF network, allowing OSPF neighbors to learn and utilize them.

I refactored network topology:

![image](https://github.com/user-attachments/assets/c403f4b1-f178-4a0b-95fc-74ba78b9ed3b)

Then I prepared `client-5`.

![image](https://github.com/user-attachments/assets/89510545-6a64-4a56-b655-34923c1f72bd)

Prepared `router-1` and `router-5`:

`router-1`
![image](https://github.com/user-attachments/assets/23f44abe-60dc-4a92-812e-78c27a4d2bad)

`router-5`
![image](https://github.com/user-attachments/assets/127729ea-d620-4b63-8946-d98b5e12372b)

I added static route on `router-1` to react `client-5`.

![image](https://github.com/user-attachments/assets/0627e243-839c-4e2a-bcc0-e2df57b6bbe6)

After that, I enabled in OSPF preferences of Router 4 redistribution of static routes.

![image](https://github.com/user-attachments/assets/1b80a836-1976-429c-8614-2ac66e261942)

I also made `ether1` on `router-5` default.

![image](https://github.com/user-attachments/assets/c937ff17-8288-4f32-865f-2a0a9fc6cc56)

Let's check connectivity.

![image](https://github.com/user-attachments/assets/95753fa7-25f3-4546-8558-df8a63ffe066)

Everything good! Static route were redistributed.

## 2.5 Enable OSPF with authentication between the neighbors and verify it.

OSPF authentication is a security mechanism that protects OSPF communication between routers by allowing only authorized routers to share routing information. This is achieved by setting up a password or key on OSPF-enabled interfaces, which is then included in OSPF Hello packets.

For authentication, I used the MD5 method and applied it on all the border interfaces of the routers with the following command:

![image](https://github.com/user-attachments/assets/a9e2fe29-1630-4bcd-8277-db22ae31119a)

Moreover, we need protection only on template 1, because we want to turn off authentication between routers.

Here is configurations for all routers.

![image](https://github.com/user-attachments/assets/3aaae106-64a6-4cc0-a284-cd7ceb680d9d)

![image](https://github.com/user-attachments/assets/b69d5e04-75c2-44de-87be-4aeedbe149b2)

![image](https://github.com/user-attachments/assets/edf56a28-2185-4441-96db-b9f2bdfa6f00)

![image](https://github.com/user-attachments/assets/16be38f2-6bcf-4d23-b1f7-3d2840fa3fa2)

To confirm that the system is functioning correctly, I checked the neighbor status on `router-3`. The output shows a state "Full," which signifies that the OSPF adjacency has been successfully established and the routers are fully synchronized.

![image](https://github.com/user-attachments/assets/417d2d5b-acb4-4e76-9a71-c2df2c3a2c5e)

