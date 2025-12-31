# Security Onion Lab - Quick Reference Guide

Quick command reference for common tasks and troubleshooting.

## Proxmox Host Commands

### Network Configuration
```bash
# Edit network interfaces
nano /etc/network/interfaces

# Reload network configuration
ifreload -a

# Check bridge status
ip -br link show | grep vmbr

# Verify promiscuous mode
ip link show vmbr1 | grep PROMISC

# Check bridge ageing (should be 0)
brctl showstp vmbr1 | grep ageing
# Or
cat /sys/class/net/vmbr1/bridge/ageing_time

# Force bridge into hub mode (temporary)
brctl setageing vmbr1 0
# Or
ip link set dev vmbr1 type bridge ageing_time 0
```

### Disable Offloading
```bash
# Disable on physical interface
ethtool -K enp2s0 rx off tx off sg off tso off ufo off gso off gro off lro off

# Disable on bridge
ethtool -K vmbr1 rx off tx off sg off tso off ufo off gso off gro off lro off

# Verify offloading is disabled
ethtool -k enp2s0 | grep -E "rx-checksumming|tx-checksumming"
```

### Traffic Verification
```bash
# Capture traffic on physical interface
tcpdump -i enp2s0 -nn

# Capture specific host
tcpdump -i enp2s0 host testmyids.com -nn
```

## Security Onion VM Commands

### Service Management
```bash
# Check all service status
sudo so-status

# Restart all services
sudo so-restart

# Restart specific service
sudo so-suricata-restart
sudo so-zeek-restart

# Start/stop services
sudo so-suricata-start
sudo so-suricata-stop
```

### Network Configuration
```bash
# Add monitor interface
sudo so-monitor-add ens19

# Network configuration wizard
sudo so-network-configure

# Find interface names
ls /sys/class/net
ip link show
```

### Updates
```bash
# Full Security Onion update
sudo soup

# Update rules only
sudo so-rule-update
```

### Verification
```bash
# Check checksum offloading (should be OFF)
ethtool -k ens19 | grep checksum

# Verify promiscuous mode
ip link show ens19 | grep PROMISC

# Capture traffic inside VM
sudo tcpdump -i ens19 -nn

# Check specific rule
grep "2100498" /etc/suricata/rules/all.rules
```

### Logs
```bash
# Suricata logs
sudo tail -f /var/log/suricata/suricata.log

# Zeek logs
sudo tail -f /var/log/zeek/current/conn.log

# System logs
sudo journalctl -u suricata -f
```

## Testing Commands

### Generate Test Alerts
```bash
# From any device on your network

# Test 1: Suspicious User-Agent
curl -A "BlackSun" http://www.google.com

# Test 2: TestMyIDS
curl http://testmyids.com

# Test 3: ICMP
ping 8.8.8.8
```

### Expected Alerts
- `ET USER_AGENTS Suspicious User Agent (BlackSun)`
- `GPL ATTACK_RESPONSE id check returned root`
- `GPL ICMP_INFO PING`

## Critical Configuration Checklist

### Proxmox Host
- [ ] `/etc/network/interfaces` configured with vmbr1
- [ ] `bridge-ageing 0` set in vmbr1 config
- [ ] Promiscuous mode enabled on enp2s0 and vmbr1
- [ ] Offloading disabled on enp2s0 and vmbr1
- [ ] Bridge created and active: `ip link show vmbr1`

### Proxmox VM
- [ ] CPU Type: **Host** (not kvm64)
- [ ] Memory: 16GB+ (Ballooning disabled)
- [ ] Network Device (net0): vmbr0, Firewall ON
- [ ] Network Device (net1): vmbr1, Firewall **OFF**, Model: Intel E1000

### Security Onion
- [ ] Management interface configured (ens18)
- [ ] Monitor interface configured (ens19 or eth1)
- [ ] Checksum offloading: OFF (no [fixed] tag)
- [ ] Promiscuous mode enabled
- [ ] Rules updated: `sudo so-rule-update`
- [ ] All services running: `sudo so-status`

### Switch
- [ ] Port mirroring configured
- [ ] Source: Port 1 (Router)
- [ ] Destination: Port 8 (Proxmox)
- [ ] Direction: Both (Tx + Rx)

## Troubleshooting Quick Fixes

### No Traffic in VM
```bash
# On Proxmox Host
brctl setageing vmbr1 0
```

### No Alerts (Checksums)
```bash
# Change VM NIC to Intel E1000 in Proxmox
# Then inside VM:
ethtool -k ens19 | grep checksum  # Should show "off"
```

### Interface Name Changed
```bash
# Find new name
ls /sys/class/net

# Update Security Onion
sudo so-monitor-add <new_name>
```

### Services Not Starting
```bash
# Check status
sudo so-status

# Check logs
sudo journalctl -u suricata -n 50
sudo journalctl -u zeek -n 50
```

## File Locations

### Proxmox
- Network config: `/etc/network/interfaces`
- VM configs: `/etc/pve/qemu-server/101.conf` (VM ID 101)

### Security Onion
- Suricata config: `/etc/suricata/suricata.yaml`
- Suricata rules: `/etc/suricata/rules/`
- Zeek config: `/etc/zeek/`
- Logs: `/var/log/suricata/`, `/var/log/zeek/`

## Web Access

- Security Onion Console: `https://192.168.4.155` (or your VM IP)
- Kibana: Click "Kibana" link in Security Onion console
- Credentials: Same as Security Onion login

## Network Ranges

- Management Network: 192.168.4.0/22
- Proxmox Host: 192.168.4.146
- Security Onion VM: 192.168.4.155
- Switch: 192.168.4.53
- Gateway: 192.168.4.1

---

**For detailed explanations, see SecurityOnion_Lab_Writeup.md**

