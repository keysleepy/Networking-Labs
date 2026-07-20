A Cisco Packet Tracer lab simulating a small office network with four departments, each on its own right-sized subnet and VLAN, routed through a single router interface using router-on-a-stick.

Overview

This lab designs and configures a segmented office network for a company with four departments of different sizes. Rather than giving every department the same flat subnet (wasting hundreds of unused addresses on small teams), the network uses VLSM to size each subnet to its actual headcount, VLANs to logically isolate each department on the same physical switch, and router-on-a-stick to route between them using only one physical router port.

Scenario

A company needs to connect four departments from a single 172.16.5.0/24 address block:

Department	Hosts needed
Warehouse	120
IT	60
Sales	25
HR	15

Requirements:

Each department is isolated from the others at Layer 2 (separate VLANs)
Devices within a department can communicate freely
Devices across departments can reach each other through the router
No address space should be wasted on departments that don't need it
