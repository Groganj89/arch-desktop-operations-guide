# 🏔 Arch Desktop Operations Guide

![Version](https://img.shields.io/badge/version-v1.4.0-blue)
![Arch](https://img.shields.io/badge/Arch-Rolling%20Release-1793D1?logo=arch-linux&logoColor=white)
![Model](https://img.shields.io/badge/model-Stability--Oriented-success)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

> A structured, stability-oriented operational model for running Arch Linux as a long-term desktop system.

**Last Reviewed Against Arch News:** 2026-03-02  
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

## 📘 Introduction

Arch Linux provides a minimal base system. It does not assume:

- Your desktop environment  
- Your GPU  
- Your update tolerance  
- Your risk model  
- Your development stack  

Most instability associated with Arch is operational, not structural.

This guide focuses on reducing common desktop failure modes while preserving Arch’s flexibility.

---

## 📚 Table of Contents

- 🎯 Threat Model  
- 📦 Scope  
- 🧠 Core Concepts  
- 🧩 Kernel Variants  
- 🎨 Graphics Stack  
- 🖥 Wayland vs X11  
- 🎮 Gaming Readiness  
- 💻 Development Baseline  
- 🔐 Security Baseline  
- 🌐 Privacy Configuration  
- 🧱 BTRFS & Snapshot Strategy  
- 🔄 Safe Update Workflow  
- 🧭 System Profiles  
- 🏗 Architectural Model  
- 🛠 Troubleshooting & Common Mistakes  
- 📝 Change Log  

---

# 🎯 Threat Model

## Context

Security and stability recommendations only make sense within a defined model.

This guide assumes:

- Home or lab environment  
- Behind NAT  
- No public-facing services  
- Single trusted user  
- No hostile physical access  

If your environment differs, additional controls may be required.

---

# 📦 Scope

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

# 🧠 Core Concepts

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

Before major upgrades, review:

👉 https://archlinux.org/news/

Arch stability depends on synchronized system updates.

---

# 🧩 Kernel Variants

## Context

The kernel defines hardware compatibility and driver interaction.

### linux (default)
Latest mainline kernel.

### linux-lts
Slower update cadence. Recommended as fallback.

```bash
sudo pacman -S linux-lts linux-lts-headers
```

Installing `linux-lts` alongside the default kernel improves rollback resilience.

### linux-zen
Performance-tuned. Optional.

---

# 🎨 Graphics Stack

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

Before updating NVIDIA drivers:

- Review Arch news  
- Snapshot if using BTRFS  
- Ensure matching kernel headers  
- Keep a fallback kernel installed  
- Reboot after update  

---

# 🖥 Wayland vs X11

Wayland is modern and default in GNOME.  
KDE supports both.

- AMD/Intel: Wayland generally recommended  
- NVIDIA: Modern drivers support Wayland; X11 remains fallback  

Choose based on stability in your environment.

---

# 🎮 Gaming Readiness

Install Steam:

```bash
sudo pacman -S steam
```

Install ProtonUp-Qt:

```bash
yay -S protonup-qt
```

Verify Vulkan:

```bash
vulkaninfo | less
```

---

# 💻 Development Baseline

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

# 🔐 Security Baseline

```bash
sudo pacman -S ufw
sudo systemctl enable --now ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Keep the system minimal. Disable unnecessary services.

---

# 🌐 Privacy Configuration

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

---

# 🧱 BTRFS & Snapshot Strategy

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

# 🔄 Safe Update Workflow

- Review Arch news  
- Snapshot  
- Update:

```bash
sudo pacman -Syu
```

- Reboot  

These principles favor predictability over novelty.

---

# 🧭 System Profiles

**Gaming:** Vulkan verified, 32-bit libraries installed, fallback kernel present  
**Development:** Minimal AUR usage, controlled tooling  
**Secure:** Firewall enabled, conservative configuration  
**Instructor/Lab:** Multi-user discipline, snapshot reset strategy  

---

# 🏗 Architectural Model

Desktop layers:

Applications → Desktop → Graphics → Kernel → Services → Filesystem → Hardware

Troubleshoot one layer at a time.

---

# 🛠 Troubleshooting

Common causes of instability:

- Partial upgrades  
- Driver mismatch  
- Missing kernel headers  
- Ignoring Arch news  
- Excessive AUR usage  

---

# 📝 Change Log

## v1.4.0 – 2026-03-02
- NVIDIA compatibility matrix  
- Safe update workflow  
- BTRFS snapshot strategy  
- System profiles  
- Architectural model  
- Tone calibration for Arch alignment  

---

# ⚠ Disclaimer

Arch Linux is a rolling release distribution. Operational discipline is required.

This guide reduces common failure modes but does not eliminate risk.  
You remain responsible for your system.
