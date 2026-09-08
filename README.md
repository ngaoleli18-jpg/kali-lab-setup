# Cybersecurity Lab Setup — VirtualBox + Kali Linux

Week 1 project for the Networkwalks Cybersecurity & Ethical Hacking course (instructor: Waqas Karim, CCIE).

This repo documents the setup of a local penetration-testing lab environment using VirtualBox and Kali Linux, built as the foundation for future ethical hacking practice.

## 🎯 Objective

Set up a cybersecurity testing lab on a personal laptop with the following requirements:

- VirtualBox as the base hypervisor
- Kali Linux configured as the attacking/hacker machine
- Custom NAT Network in subnet `10.0.0.0/24`
- Static IP for Kali: `10.0.0.2/24`
- Shared clipboard and drag-and-drop enabled
- Host `Downloads` folder shared into the VM
- Full internet access from Kali

## 🖥️ Environment

| Component | Details |
|---|---|
| Host OS | Windows |
| Hypervisor | Oracle VirtualBox 7.2.16 |
| Guest OS | Kali Linux 2026.2 |
| Network | Custom NAT Network (`NatNetwork`) |
| Subnet | `10.0.0.0/24` |
| Kali IP | `10.0.0.2/24` |
| Gateway | `10.0.0.1` |
| DNS | `8.8.8.8` |

## 🛠️ Setup Steps

1. Installed the Microsoft Visual C++ 2019 Redistributable (required dependency for the VirtualBox installer)
2. Installed VirtualBox 7.2.16
3. Created a custom NAT Network (`NatNetwork`) with IPv4 prefix `10.0.0.0/24` and DHCP enabled
4. Downloaded the Kali Linux VirtualBox pre-built image and extracted the `.7z` archive
5. Registered the VM directly from the extracted `.vbox` file
6. Configured VM settings:
   - Network adapter attached to `NatNetwork`
   - Shared Clipboard: Bidirectional
   - Drag'n'Drop: Bidirectional
   - Shared folder: host `Downloads` → `sf_Downloads` in the VM
7. Set a static IP inside Kali via `nmcli`
8. Took a snapshot as a clean rollback baseline

## 🐛 Issues Encountered & Fixes

**1. VirtualBox installer failed with a fatal error**
Root cause: missing Visual C++ 2019 Redistributable. Installed it directly from Microsoft, restarted, and re-ran the VirtualBox installer.

**2. Import Appliance wizard couldn't find the `.vbox` file**
The file type filter in VirtualBox's Import Appliance dialog only shows `.ova`/`.ovf` files by default, so a `.vbox` + `.vdi` pair (from the extracted `.7z`) wouldn't appear. Fix: skipped the Import wizard entirely and double-clicked the `.vbox` file directly in File Explorer — VirtualBox registered it as a VM automatically.

**3. No internet access in Kali after setting the static IP**
```
Error: Connection activation failed: IP configuration could not be reserved (no available address, timeout, etc.)
```
This is a known issue on VirtualBox v7 with Kali 2026.1+, caused by IPv4 Duplicate Address Detection (DAD) timing out. Fixed with:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

## ✅ Verification

```bash
$ ip a
2: eth0: ... state UP ...
    inet 10.0.0.2/24 brd 10.0.0.255 scope global noprefixroute eth0

$ ping -c 4 google.com
4 packets transmitted, 4 received, 0% packet loss
```

Screenshots of the working setup are in the [`/screenshots`](./screenshots) folder.

## 📚 Course

Part of the [Networkwalks](https://networkwalks.com) Cybersecurity & Ethical Hacking course, instructed by Waqas Karim (CCIE).

## 🔜 Next Steps

- Phase 2: add Windows 10/11/7 and Android VMs to the same NAT Network for multi-machine practice
- Begin ethical hacking practice against the lab environment
