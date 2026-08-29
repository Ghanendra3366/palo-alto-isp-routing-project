# Palo Alto Firewall – Configure Default Route Towards ISP

## Group 6 Project

**Project:** Configure Default Route on Palo Alto towards ISP

### Objective
Configure and verify a default static route (`0.0.0.0/0`) on a Palo Alto Firewall so that traffic for unknown/external networks is forwarded towards the ISP next-hop gateway.

![Palo Alto ISP Topology](screenshots/topology.svg)

---

## 1. Lab Topology

```text
                              ISP
                       100.1.1.1/24
                              |
                              |
                     Untrust / WAN
                      100.1.1.2/24
                              |
                    +-------------------+
                    |   Palo Alto FW    |
                    |                   |
                    | Trust: 192.168.10.1|
                    | Untrust: 100.1.1.2 |
                    +-------------------+
                              |
                         Trust / LAN
                     192.168.10.1/24
                              |
                             PC1
                     192.168.10.10/24
```

### Original project addressing

| Device | Role | IP Address |
|---|---|---|
| PC1 | Trust host | `192.168.10.10/24` |
| Palo Alto | Trust interface | `192.168.10.1/24` |
| Palo Alto | Untrust interface | `100.1.1.2/24` |
| ISP | Next-hop gateway | `100.1.1.1/24` |


---

## 2. Main Configuration

### Default route

```text
Destination: 0.0.0.0/0
Next Hop:    100.1.1.1
Interface:   Untrust
Virtual Router: default
```
The route `0.0.0.0/0` is the IPv4 default route. It is selected when a more-specific route for the destination is not available.

---

## 3. Palo Alto GUI Steps

### Step 1 – Configure Trust Interface

Go to:

`Network > Interfaces > Ethernet`

Configure the Layer-3 Trust interface:

```text
IP Address:       192.168.10.1/24
Security Zone:    Trust
Virtual Router:   default
```

### Step 2 – Configure Untrust Interface

Configure the Layer-3 Untrust interface:

```text
IP Address:       100.1.1.2/24
Security Zone:    Untrust
Virtual Router:   default
```

### Step 3 – Create Zones

Go to:

`Network > Zones`

Create:

```text
Trust
Untrust
```

Assign the corresponding Layer-3 interfaces.

### Step 4 – Configure the Default Route

Go to:

`Network > Virtual Routers > default > Static Routes > IPv4`

Click **Add**.

```text
Name:            DEFAULT-TO-ISP
Destination:     0.0.0.0/0
Interface:       Untrust interface
Next Hop:        IP Address
Next Hop IP:     100.1.1.1
Admin Distance:  10
Metric:          10
```

Click **OK** and then **Commit**.

---

## 4. Address Objects

Go to:

`Objects > Addresses`

Create:

`Policies > Security > Add`

Example policy:

```text
Name:              TRUST-TO-UNTRUST
Source Zone:       Trust
Destination Zone:  Untrust
Source Address:    PC1
Destination:       any
Application:       ping / required applications
Service:           application-default
Action:             Allow
```

For an assignment-specific test, the destination can be restricted to the required host/address instead of `any`.

Commit the configuration.

---

## 6. PC Configuration

### PC1

```text
IP Address: 192.168.10.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
```

Configure the equivalent route on the upstream ISP device:

```text
Destination: 192.168.10.0/24
Next hop:   100.1.1.2
```

This tells the ISP that the `192.168.10.0/24` network is reachable through the Palo Alto Untrust address.

---

## 8. NAT for Internet Access

A default route alone does not provide Internet access. If private Trust addresses must reach a real/public upstream network, configure Source NAT.

Go to:

`Policies > NAT > Add`

Typical setup:

```text
Source Zone:       Trust
Destination Zone:  Untrust
Source Address:    PC1 (or required Trust subnet)
Destination:       any
Source Translation: Dynamic IP and Port
Translation:       Untrust interface address
```

The exact NAT design depends on the ISP/simulator topology.

---

## 9. Verification

### View routing table

```text
show routing route
```

### View static routes

```text
show routing route type static
```

### Check the default route

```text
show routing route destination 0.0.0.0/0
```

### Test ISP next hop

```text
ping source 100.1.1.2 host 100.1.1.1
```

### Test FIB lookup

```text
test routing fib-lookup virtual-router default ip 8.8.8.8
```

### Check sessions

```text
show session all filter source 192.168.10.10
```

---

## 10. Expected Result

The routing table should contain a default route equivalent to:

```text
0.0.0.0/0  ->  100.1.1.1  ->  Untrust
```

The firewall should select this route for destinations that do not have a more-specific route.

For Internet/public connectivity, the following must also be correct:

- Security Policy
- Source NAT
- ISP return routing
- ISP/upstream connectivity

---

## 11. Troubleshooting

### PC1 cannot ping 192.168.10.1
Check:
- PC1 IP/mask
- Trust interface IP
- Interface status
- PC1 default gateway

### Palo Alto cannot ping 100.1.1.1
Check:
- Untrust interface IP
- ISP gateway IP
- Untrust interface status
- Layer-3 connectivity
- ISP interface configuration

### Default route is missing
Check:
- Correct Virtual Router selected
- Destination is exactly `0.0.0.0/0`
- Next hop is reachable
- Configuration was committed

### PC1 can reach the firewall but not the Internet
Check:
- Trust-to-Untrust Security Policy
- Source NAT
- ISP return route
- ISP's own upstream/default route

---

## 12. Project Deliverables

This repository contains:

- Project overview
- Network topology
- IP addressing
- Palo Alto GUI configuration
- Default route configuration
- Security Policy example
- NAT guidance
- ISP return route
- CLI verification commands
- Troubleshooting guide

---

## 13. Screenshots and Visual Evidence

The repository includes project-specific topology/route diagrams in `screenshots/`.

For genuine PAN-OS GUI reference screenshots, use the official Palo Alto Networks documentation:
- Configure Interfaces and Zones: https://docs.paloaltonetworks.com/ngfw/getting-started/initial-setup-configuration-ngfws/segment-your-network/configure-interfaces-and-zones
- Layer 3 Interfaces: https://docs.paloaltonetworks.com/ngfw/networking/configure-interfaces/layer-3-interfaces
- Security Policy Rule: https://docs.paloaltonetworks.com/network-security/security-policy/administration/security-rules/create-a-security-policy-rule

Do not present reference screenshots as screenshots of your own completed lab. If lab evidence is required, add screenshots from your own Palo Alto VM/device to `screenshots/`.

Recommended screenshots:

1. `01-trust-interface.png`
2. `02-untrust-interface.png`
3. `03-zones.png`
4. `04-default-route.png`
5. `05-security-policy.png`
6. `06-routing-table.png`
7. `07-ping-verification.png`

---

## Reference

Palo Alto Networks documentation:
https://docs.paloaltonetworks.com/pan-os/10-2/pan-os-networking-admin/static-routes
