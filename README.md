# 🏔 Arch Desktop Operations Guide

> A structured, stability-oriented operational model for running Arch Linux as a long-term desktop system.

**Version:** v1.4.0  
**Last Reviewed Against Arch News:** 2026-03-02  
**Model:** Rolling Release Discipline  
**Audience:** Intermediate Desktop Users  

---

## 📌 Positioning

This guide is not affiliated with or endorsed by the Arch Linux project.

Arch Linux intentionally avoids prescribing workflows.  
This document proposes a structured operational model for running Arch Linux as a stable desktop system.

It draws from:

- Arch Wiki documentation  
- Arch Linux news announcements  
- Observed rolling-release failure patterns  
- Desktop operational discipline practices  

For canonical documentation, always refer to the Arch Wiki:  
👉 https://wiki.archlinux.org/

This guide is intentionally conservative and stability-oriented.  
Adapt it to your own risk tolerance and workflow.

---

# 📘 Introduction

Arch Linux provides a minimal base system. It does not assume:

- Your desktop environment  
- Your GPU  
- Your update tolerance  
- Your risk model  
- Your development stack  

Most instability associated with Arch is operational, not structural.

This guide focuses on reducing common desktop failure modes while preserving Arch’s flexibility.

---

# 📚 Table of Contents

1. Threat Model  
2. Scope  
3. Core Arch Concepts  
4. Kernel Variants  
5. Graphics Stack  
6. Wayland vs X11  
7. Gaming Readiness  
8. Development Baseline  
9. Security Baseline  
10. Privacy Configuration  
11. BTRFS & Snapshot Strategy  
12. Safe Update Workflow  
13. System Profiles  
14. Architectural Model  
15. Troubleshooting & Common Mistakes  
16. Change Log  

---

# 1️⃣ Threat Model

## Context

Security and stability recommendations only make sense within a defined model.

This guide assumes:

- Home or lab environment  
- Behind NAT  
- No public-facing services  
- Single trusted user  
- No hostile physical access  

### Not Covered

- Enterprise compliance  
- High-security adversarial environments  
- Secure Boot deep configuration  
- Public server hardening  

If your environment differs, additional controls may be required.

---

# 2️⃣ Scope

This guide covers:

- KDE and GNOME desktops  
- Intel / AMD / NVIDIA GPUs  
- Gaming (Steam / Proton)  
- Developer workstation baseline  
- Snapshot & rollback discipline  
- Security hygiene  
- Privacy-focused DNS configuration  

It does not cover initial Arch installation.

---

# 3️⃣ Core Arch Concepts

## Rolling Release Discipline

Running:

```bash
pacman -Sy
```

without `-u` is strongly discouraged due to partial upgrade risk.

Recommended pattern:

```bash
sudo pacman -Syu
```

Arch stability depends on synchronized system updates.

---

# 4️⃣ Kernel Variants

## Context

The kernel defines hardware compatibility and driver interaction.

### linux (default)
Latest mainline kernel.

### linux-lts
Slower update cadence. Recommended as fallback.

Install alongside default:

```bash
sudo pacman -S linux-lts linux-lts-headers
```

### linux-zen
Performance-tuned. Optional.

Installing `linux-lts` alongside the default kernel improves rollback resilience.

---

# 5️⃣ Graphics Stack

## Identify GPU

```bash
lspci | grep -E "VGA|3D"
```

---

## Intel

```bash
sudo pacman -S mesa vulkan-intel
```

---

## AMD

```bash
sudo pacman -S mesa vulkan-radeon
sudo pacman -S lib32-mesa lib32-vulkan-radeon
```

---

## NVIDIA

NVIDIA requires careful driver selection.

### Driver Compatibility Matrix

| Architecture | Example | Recommended |
|-------------|----------|-------------|
| Pascal | GTX 10xx | `nvidia-580xx-dkms` (AUR) |
| Turing+ | RTX 20xx+ | `nvidia` |

### Turing and Newer

```bash
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils
```

### Pascal (GTX 10xx)

```bash
yay -S nvidia-580xx-dkms
```

---

## Safe NVIDIA Update Checklist

Before updating:

1. Read Arch news  
2. Snapshot (if using BTRFS)  
3. Ensure kernel headers installed  
4. Have fallback kernel available  
5. Reboot after update  

Arch news:  
👉 https://archlinux.org/news/

---

# 6️⃣ Wayland vs X11

Wayland is modern and default in GNOME.  
KDE supports both.

- AMD/Intel: Wayland recommended  
- NVIDIA: Modern drivers support Wayland; X11 remains fallback  

Choose based on stability in your environment.

---

# 7️⃣ Gaming Readiness

Install Steam:

```bash
sudo pacman -S steam
```

Install ProtonUp-Qt:

```bash
yay -S protonup-qt
```

Ensure Proton appears in:

```
~/.steam/root/compatibilitytools.d
```

Verify Vulkan:

```bash
vulkaninfo | less
```

---

# 8️⃣ Development Baseline

Install base tools:

```bash
sudo pacman -S git base-devel
```

Optional Docker:

```bash
sudo pacman -S docker
sudo systemctl enable --now docker
```

Adding a user to the docker group grants elevated privileges. Consider implications.

---

# 9️⃣ Security Baseline

Install firewall:

```bash
sudo pacman -S ufw
sudo systemctl enable --now ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Disable unnecessary services.

Keep system minimal.

---

# 🔟 Privacy Configuration

## DNS over TLS (Cloudflare Example)

Edit:

```
/etc/systemd/resolved.conf
```

Add:

```
DNS=1.1.1.1 1.0.0.1
DNSOverTLS=yes
```

Restart:

```bash
sudo systemctl restart systemd-resolved
```

Adapt to your preferred DNS provider.

---

# 11️⃣ BTRFS & Snapshot Strategy

Install Snapper:

```bash
sudo pacman -S snapper
sudo snapper -c root create-config /
```

Before major updates:

```bash
sudo snapper create --description "pre-update"
```

Snapshots significantly reduce recovery time.

---

# 12️⃣ Safe Update Workflow

Before major upgrades:

1. Review Arch news  
2. Snapshot  
3. Update:

```bash
sudo pacman -Syu
```

4. Reboot  

These principles favor predictability over novelty.

---

# 13️⃣ System Profiles

### Gaming Profile
- Vulkan verified  
- 32-bit libraries installed  
- LTS kernel installed  

### Development Profile
- Minimal AUR usage  
- Fallback kernel installed  
- Container discipline  

### Secure Profile
- Firewall enabled  
- Minimal services  
- Conservative AUR usage  

### Instructor / Lab Profile
- Multi-user discipline  
- No unnecessary privilege escalation  
- Snapshot reset strategy  

---

# 14️⃣ Architectural Model

Desktop layers:

1. Applications  
2. Desktop / Compositor  
3. Graphics Stack  
4. Kernel  
5. System Services  
6. Filesystem  
7. Hardware  

Most failures occur in one layer.  
Troubleshoot methodically.

---

# 15️⃣ Troubleshooting & Common Mistakes

Common issues:

- Partial upgrades  
- Driver mismatch  
- Missing kernel headers  
- Excessive AUR usage  
- Ignoring Arch news  

Recovery is easier with snapshot discipline.

---

# 16️⃣ Change Log

## v1.4.0 – 2026-03-02
- Added NVIDIA compatibility matrix  
- Integrated 590 driver transition guidance  
- Added Safe Update Workflow  
- Added BTRFS snapshot strategy  
- Added system profiles  
- Added architectural model  
- Tone calibrated for Arch alignment  

---

# ⚠ Disclaimer

Arch Linux is a rolling release distribution. Operational discipline is required.

This guide reduces common failure modes but does not eliminate risk.  
You remain responsible for your system.
