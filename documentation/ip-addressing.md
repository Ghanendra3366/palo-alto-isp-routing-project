# IP Addressing

| Device | Interface | IP | Mask | Gateway |
|---|---|---|---|---|
| PC1 | Trust/LAN | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| Palo Alto | Trust | 192.168.10.1 | 255.255.255.0 | - |
| Palo Alto | Untrust | 100.1.1.2 | 255.255.255.0 | 100.1.1.1 |
| ISP | Next hop | 100.1.1.1 | 255.255.255.0 | - |

## Default Route

```text
0.0.0.0/0 -> 100.1.1.1
```

