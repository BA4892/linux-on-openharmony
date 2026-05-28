# Linux on OpenHarmony

Run a full-featured Linux distribution (Alpine Linux) on OpenHarmony devices with a single click, implemented via chroot with zero virtualization overhead.

## Features

- One-click deployment: Windows PowerShell script that automatically completes all installation and configuration
- XFCE4 desktop + Firefox browser, accessible via VNC
- SSH remote login (dropbear, adapted for the chroot environment)
- Over 25,000 packages available from the Alpine repository
- Shares the kernel with OH, delivering native performance with zero virtualization overhead

## Quick Start

### System Requirements

- OpenHarmony device (aarch64, root access)
- Install the `hdc` tool on your PC, connect the device via USB, and verify the device appears in the `hdc list targets` output
- The device must be connected to Wi-Fi and have internet access (required for downloading packages from the Alpine repository)
- The device’s system time must be correct (time discrepancies will cause SSL certificate validation to fail, resulting in errors with `apk` and `curl`)
- If you need to bypass internet restrictions, set up a mobile hotspot and Clash proxy in advance (see [Installation and Configuration Details](OpenHarmony-Install-Linux-Details.md))
- Place [alpine-minirootfs-3.21.3-aarch64.tar.gz](https://dl-cdn.alpinelinux.org/alpine/v3.21/releases/aarch64/alpine-minirootfs-3.21.3-aarch64.tar.gz) in the project directory

### One-Click Deployment (Windows PowerShell)

```powershell
.\deploy.ps1

# If you see a “Scripts are blocked” prompt, bypass it using the following:
powershell -ExecutionPolicy Bypass -File .\deploy.ps1
```

Automated workflow: Push files -> Install Alpine -> Install SSH + VNC + XFCE4 desktop + Firefox + Chinese fonts.

### Post-Deployment Configuration

```bash
# Execute on the PC to access the device via hdc
hdc shell
sh /data/local/tmp/alpine-enter.sh

# Set the root password (required on first run)
passwd root

# Start all services (SSH + VNC desktop)
sh /root/start-services.sh 1920x1080
```

### Connecting

**SSH (via USB port forwarding, no network required):**

```powershell
# Execute on PC (not within the hdc shell)
hdc fport tcp:2222 tcp:22
ssh root@127.0.0.1 -p 2222
```

**VNC Desktop:**

```powershell
# Execute on the PC, map the VNC port
hdc fport tcp:5900 tcp:5900
```

Connect to `127.0.0.1:5900` using a VNC client to view the desktop (xfwm4 window manager + terminal + Firefox browser).

> **USB Connection Notes:** If there is no network connection between the PC and the OpenHarmony device (connected via USB only), all ports must be mapped using `hdc fport` before they can be accessed. Map SSH as `tcp:2222 tcp:22`, VNC as `tcp:5900 tcp:5900`, and other services similarly. After mapping,
