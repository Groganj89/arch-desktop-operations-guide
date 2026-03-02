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

### Context
Arch Linux intentionally avoids prescribing workflows. This document proposes one stability-oriented operational model for desktop usage.

### When to Skip
Never. This sets expectations and avoids “this is official” confusion.

### What This Enables / Risk Model
- Sets the tone: stability-oriented, conservative by design
- Reduces community friction by deferring to canonical docs
- Clarifies this is guidance, not policy

### Configuration / Commands
For canonical documentation, always refer to the Arch Wiki:  
👉 https://wiki.archlinux.org/

### Verification
You can clearly answer:
- “Is this official?” → No
- “Where is canonical truth?” → Arch Wiki

---

## 📘 Introduction

### Context
Arch installs a minimal base. It does not assume desktop environment, GPU, gaming, or development needs. Desktop stability on Arch is primarily operational.

### When to Skip
Never. It frames the intent of the guide.

### What This Enables / Risk Model
- Prevents readers treating this like a linear tutorial
- Explains why the guide emphasizes discipline (updates, drivers, snapshots)

### Configuration / Commands
None.

### Verification
Reader understands:
- This is an ops guide, not an install guide
- The design bias is stability/predictability

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

### Context
Security and stability recommendations only make sense relative to a defined environment.

### When to Skip
Skip only if you are replacing it with your own explicit threat model (enterprise, lab, hostile LAN, public exposure).

### What This Enables / Risk Model
Assumed environment:
- Desktop/laptop behind NAT
- No public-facing services
- Single trusted user
- No hostile physical access

Not covered:
- Enterprise compliance
- High-security adversaries
- Secure Boot deep chain assurance
- Internet-exposed server hardening

### Configuration / Commands
None.

### Verification
You can state whether your environment matches the assumptions. If it doesn’t, treat guidance as a baseline only.

---

# 📦 Scope

### Context
Arch is flexible; scope prevents this guide from turning into “everything for everyone”.

### When to Skip
Never. Scope prevents misuse.

### What This Enables / Risk Model
Clarifies what this guide covers:
- KDE/GNOME desktop operations
- GPU stack management
- Safe updates and rollback
- Gaming/dev baseline
- Security + privacy baseline

Clarifies what it doesn’t cover:
- Initial Arch installation
- Secure Boot deep setup
- Public server hardening

### Configuration / Commands
None.

### Verification
Reader can answer “Is this the right guide for my use-case?”

---

# 🧠 Core Concepts

### Context
Rolling release stability depends on synchronized upgrades.

### When to Skip
Do not skip. This prevents common breakage.

### What This Enables / Risk Model
Primary risk:
- Partial upgrades causing ABI/library mismatch

### Configuration / Commands
Strongly discouraged:

```bash
pacman -Sy
```

Recommended:

```bash
sudo pacman -Syu
```

Before major upgrades, review Arch News:
- https://archlinux.org/news/

### Verification
- `pacman -Q systemd` and core packages are consistent post-upgrade
- You are not mixing repo states (no `-Sy` without `-u`)

---

# 🧩 Kernel Variants

### Context
Kernel choice affects hardware compatibility, driver behavior, and recovery options.

### When to Skip
You may skip kernel variation if you only want the default kernel and accept higher recovery friction.

### What This Enables / Risk Model
- `linux` = default, current
- `linux-lts` = conservative fallback (recommended for resilience)
- `linux-zen` = optional desktop tuning

Risk reduced:
- A kernel regression or driver mismatch leaves you with an alternate bootable kernel.

### Configuration / Commands
Install LTS alongside default:

```bash
sudo pacman -S linux-lts linux-lts-headers
```

### Verification
Reboot and confirm both kernels exist in your boot menu (or `bootctl list` if using systemd-boot). Verify:

```bash
uname -r
```

---

# 🎨 Graphics Stack

### Context
GPU drivers are the most update-sensitive component of a desktop system (Wayland/X11, Vulkan, Steam).

### When to Skip
Skip only if this system has no GPU acceleration needs (rare on desktops).

### What This Enables / Risk Model
Enables:
- Vulkan (gaming)
- Stable compositor startup
- Correct 32-bit libraries for Steam/Proton

Primary risks:
- Driver/kernel mismatch
- Wrong NVIDIA branch for your GPU generation
- Missing 32-bit libs for gaming

### Configuration / Commands

Identify GPU:

```bash
lspci | grep -E "VGA|3D"
```

#### Intel

```bash
sudo pacman -S --needed mesa vulkan-intel
sudo pacman -S --needed lib32-mesa lib32-vulkan-intel
```

#### AMD

```bash
sudo pacman -S --needed mesa vulkan-radeon
sudo pacman -S --needed lib32-mesa lib32-vulkan-radeon
```

#### NVIDIA

NVIDIA requires deliberate driver selection.

Driver compatibility matrix:

| Architecture | Example | Recommended |
|-------------|---------|------------|
| Pascal | GTX 10xx | `nvidia-580xx-dkms` (AUR) |
| Turing+ | RTX 20xx+ | `nvidia` |

Turing and newer:

```bash
sudo pacman -S --needed nvidia nvidia-utils nvidia-settings lib32-nvidia-utils
```

Pascal (GTX 10xx):

```bash
yay -S nvidia-580xx-dkms
```

Safe NVIDIA update checklist:
- Review Arch news (especially driver transitions)
- Snapshot before driver/kernel updates
- Ensure matching headers for your kernel
- Reboot after driver update

### Verification

Vulkan:

```bash
vulkaninfo | grep -i deviceName
```

NVIDIA (if applicable):

```bash
nvidia-smi
```

Kernel module loaded:

```bash
lsmod | grep -E "nvidia|amdgpu|i915"
```

---

# 🖥 Wayland vs X11

### Context
Wayland vs X11 affects compositing, capture, multi-monitor behavior, and GPU compatibility (especially NVIDIA).

### When to Skip
Skip only if you run a single DE session type exclusively and never troubleshoot display issues (not recommended).

### What This Enables / Risk Model
Enables:
- Choosing stability per hardware
- Faster recovery from black-screen boot loops (switch session type)

Risk model:
- Wayland can expose driver/compositor bugs sooner
- X11 remains a pragmatic fallback in many cases

### Configuration / Commands
No commands required. Choose session type at your display manager login screen (KDE/GNOME).

### Verification
Check session type:

```bash
echo "$XDG_SESSION_TYPE"
```

---

# 🎮 Gaming Readiness

### Context
Gaming on Linux depends on Vulkan + correct 32-bit libraries + correct GPU stack.

### When to Skip
Skip if you will not use Steam/Proton or any Vulkan titles.

### What This Enables / Risk Model
Enables:
- Steam runtime + Proton compatibility
- Vulkan titles and translation layers

Risks:
- Missing `lib32-*` drivers
- Proton-GE installed but not detected
- Vulkan misconfigured → Steam/game hangs

### Configuration / Commands
Install Steam:

```bash
sudo pacman -S --needed steam
```

ProtonUp-Qt:

```bash
yay -S protonup-qt
```

### Verification
Verify custom tools are installed:

```bash
ls ~/.steam/root/compatibilitytools.d
```

Verify Vulkan:

```bash
vulkaninfo | less
```

---

# 💻 Development Baseline

### Context
Development tooling increases system complexity; install deliberately.

### When to Skip
Skip if the machine is for gaming/media only.

### What This Enables / Risk Model
Enables:
- Building packages
- Compilers and toolchains
- Container workflows (optional)

Risk:
- Docker group membership effectively grants elevated privileges.

### Configuration / Commands
Baseline:

```bash
sudo pacman -S --needed git base-devel
```

Optional Docker:

```bash
sudo pacman -S --needed docker docker-compose
sudo systemctl enable --now docker
```

### Verification
Git works:

```bash
git --version
```

Docker (if installed):

```bash
docker info
```

---

# 🔐 Security Baseline

### Context
Arch doesn’t enforce a firewall or service policy by default. Desktop hygiene reduces accidental exposure.

### When to Skip
If the system is permanently offline/air-gapped. Even then, update discipline still matters.

### What This Enables / Risk Model
Enables:
- Default deny inbound traffic
- Clear awareness of running services

Risk reduced:
- Accidental service exposure on hostile networks

### Configuration / Commands
UFW baseline:

```bash
sudo pacman -S --needed ufw
sudo systemctl enable --now ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```

Audit services:

```bash
systemctl --type=service --state=running
```

### Verification
Firewall status:

```bash
sudo ufw status verbose
```

Listening ports:

```bash
ss -tulnp
```

---

# 🌐 Privacy Configuration

### Context
DNS requests are a common privacy leak. Encrypting DNS (DoT) reduces passive network visibility.

### When to Skip
Skip if DNS is centrally managed (Pi-hole/router DoH/DoT, enterprise resolver) and you don’t want host-level overrides.

### What This Enables / Risk Model
Enables:
- DNS encryption to the resolver
- Reduced metadata leakage on untrusted networks

Risk:
- Some networks/captive portals may behave differently

### Configuration / Commands
Edit:

```bash
sudo nano /etc/systemd/resolved.conf
```

Set (example):

```conf
DNS=1.1.1.1 1.0.0.1
DNSOverTLS=yes
DNSSEC=yes
```

Restart:

```bash
sudo systemctl restart systemd-resolved
```

### Verification
Check resolver status:

```bash
resolvectl status
```

---

# 🧱 BTRFS & Snapshot Strategy

### Context
Snapshots are the most effective rollback safety net on a rolling release desktop.

### When to Skip
Skip if you are not using BTRFS (or another snapshot-capable setup). Consider enabling it if you value recovery speed.

### What This Enables / Risk Model
Enables:
- Rollback after kernel/driver updates
- Reduced downtime after breakage

Risk reduced:
- Time-consuming recovery via live ISO

### Configuration / Commands
Install Snapper:

```bash
sudo pacman -S --needed snapper
sudo snapper -c root create-config /
```

Create snapshot before major updates:

```bash
sudo snapper create --description "pre-update"
```

### Verification
List snapshots:

```bash
sudo snapper list
```

---

# 🔄 Safe Update Workflow

### Context
Most breakage happens during updates. Treat updates like an operational workflow.

### When to Skip
Do not skip. If you update, you need a workflow.

### What This Enables / Risk Model
Enables:
- Controlled upgrades
- Reduced surprise breakage
- Predictable recovery

Risk reduced:
- Breaking changes caught via Arch news
- Rollback available via snapshots

### Configuration / Commands
Before major upgrades:
- Review https://archlinux.org/news/
- Snapshot (if available)

Upgrade:

```bash
sudo pacman -Syu
yay -Syu
```

Reboot after kernel/driver updates.

### Verification
Post-upgrade sanity:

```bash
uname -r
```

GPU sanity (if applicable):

```bash
nvidia-smi
vulkaninfo | grep -i deviceName
```

---

# 🧭 System Profiles

### Context
Not every Arch desktop has the same purpose. Profiles prevent over-installation and clarify intent.

### When to Skip
Skip only if you are comfortable building your own profile from scratch.

### What This Enables / Risk Model
Enables:
- Purpose-driven installs
- Reduced bloat and complexity
- Clear “what to install” boundaries

### Configuration / Commands
No commands. Choose a profile and follow the relevant sections.

### Verification
You can describe your system as one of:
- Gaming / Development / Secure / Instructor-Lab

---

# 🏗 Architectural Model

### Context
Thinking in layers improves troubleshooting speed and reduces random “fix attempts”.

### When to Skip
Do not skip if you plan to troubleshoot effectively.

### What This Enables / Risk Model
Enables:
- Isolating failures to a layer (app/DE/driver/kernel/services/fs/hardware)
- Faster recovery with less guesswork

### Configuration / Commands
None.

### Verification
When something breaks, you can classify it:
- App layer (Steam) vs driver layer (Vulkan) vs compositor layer (Wayland)

---

# 🛠 Troubleshooting & Common Mistakes

### Context
Most problems are repeatable and avoidable.

### When to Skip
Skip only if you enjoy debugging the hard way.

### What This Enables / Risk Model
Common failure modes:
- Partial upgrades (`-Sy` without `-u`)
- Driver mismatch (kernel ↔ NVIDIA)
- Missing headers for DKMS
- Heavy AUR usage without review
- Ignoring Arch news

### Configuration / Commands
Logs:

```bash
journalctl -xe
```

Ports:

```bash
ss -tulnp
```

### Verification
You can identify the failure mode before making changes.

---

# 📝 Change Log

### Context
Arch changes continuously. Versioning makes this guide maintainable and trustworthy.

### When to Skip
Do not skip if you want credibility.

### What This Enables / Risk Model
Enables:
- Tracking policy changes (e.g., NVIDIA transitions)
- Providing readers with “what changed”

### Configuration / Commands
None.

### Verification
Readers can see what changed between versions.

#### v1.4.0 — 2026-03-02
- Unified README
- Tone calibrated for Arch alignment
- NVIDIA compatibility matrix (Pascal vs Turing+)
- Snapshot discipline integrated
- Safe update workflow formalized
- Profiles and architecture model included

---

# ⚠ Disclaimer

### Context
Rolling release requires operational discipline.

### When to Skip
Never.

### What This Enables / Risk Model
Sets correct expectations.

### Configuration / Commands
None.

### Verification
Readers understand responsibility remains with the operator.
