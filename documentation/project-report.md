# Project Report

## Title
Configure Default Route on Palo Alto towards ISP

## Objective
To configure and verify a default static route on a Palo Alto Firewall towards an ISP.

## Description
The project demonstrates how a Palo Alto Firewall forwards traffic for unknown or external networks using a default route. The firewall is configured with Trust and Untrust Layer-3 interfaces and security zones. A static route for `0.0.0.0/0` points to the ISP next-hop gateway.

## Key Concepts

- Virtual Router
- Static Route
- Default Route
- Next Hop
- Trust Zone
- Untrust Zone
- Security Policy
- Source NAT
- Routing Table
- FIB

## Result

The Palo Alto Firewall uses:

`0.0.0.0/0 -> 100.1.1.1`

as the default path towards the ISP.

## Conclusion

The lab provides practical understanding of how a Palo Alto Firewall selects a default route and forwards traffic towards an upstream ISP.
