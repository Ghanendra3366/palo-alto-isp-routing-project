# Detailed Configuration

## Interface Configuration

### Trust

```text
Interface: EthernetX/X
Type: Layer3
IP: 192.168.10.1/24
Zone: Trust
Virtual Router: default
```

### Untrust

```text
Interface: EthernetX/X
Type: Layer3
IP: 100.1.1.2/24
Zone: Untrust
Virtual Router: default
```

## Static Route

```text
Name: DEFAULT-TO-ISP
Destination: 0.0.0.0/0
Next Hop: 100.1.1.1
Interface: Untrust
```

## Security Policy

```text
Name: TRUST-TO-UNTRUST
From: Trust
To: Untrust
Source: PC1
Destination: any
Action: Allow
```

## Commit

Always commit the candidate configuration after making the required changes.
