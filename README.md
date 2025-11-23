# HTC Vive Pro 2 on Linux (Updated)

This repository provides a cleaned and updated guide for getting the HTC Vive Pro 2 working on Linux systems—focused primarily on Arch Linux and similarly cutting-edge environments.

Linux VR support continues to improve, and with the right setup, SteamVR 2.0+ can provide a solid experience as of 2024.

Special thanks to Both CertainLach and santeri3700 for their work on the kernel patches, documentation, and Linux driver support for the Vive Pro 2.

Helpful resources:
- LVRA Wiki: https://lvra.gitlab.io/
- VR on Linux: https://vronlinux.org/
- SteamVR Linux Support: https://help.steampowered.com/en/faqs/view/18A4-1E10-8A94-3DDA
- Reddit community: https://www.reddit.com/r/virtualreality_linux/

---

## Status
This guide is a work in progress.
Updated: 2024-01-10

---

## Setup

### 1. Build and Install the Patched Kernel
From the root of this repository:

    cd kernel

Follow the instructions in KERNEL.md.

---

### 2. Install PolKit Rule for CAP_SYS_NICE

A PolKit rule is included to allow SteamVR’s compositor or Monado to set the CAP_SYS_NICE capability without requiring sudo.

Default expected paths:
- SteamVR (native):
  /home/$USER/.steam/steam/steamapps/common/SteamVR/bin/linux64/vrcompositor-launcher
- Monado service:
  /usr/bin/monado-service

Adjust paths in the rule if your installation differs.

#### Install the rule:

    sudo cp ./polkit-vr-setcap-nice.rules /etc/polkit-1/rules.d/90-vr-setcap-nice.rules

#### Restart PolKit and test:

    sudo systemctl restart polkit.service
    pkexec setcap CAP_SYS_NICE=eip /home/$USER/.steam/steam/steamapps/common/SteamVR/bin/linux64/vrcompositor-launcher
    pkexec setcap CAP_SYS_NICE=eip /usr/bin/monado-service

If the rule is correct, these commands should not prompt for a password.

---

### 3. Install SteamVR and the Vive Pro 2 Linux Driver

Reference driver and documentation:
- CertainLach’s Vive Pro 2 Driver: https://github.com/CertainLach/VivePro2-Linux-Driver

Follow the setup steps in STEAMVR.md for full installation instructions.

---

### 4. Experimental Open-Source VR Stack (Optional)

This repository also includes notes for running the Vive Pro 2 with open-source VR components instead of SteamVR and the proprietary driver.

See OSS.md for details.

---

## Playing VR Games

- For SteamVR usage → see STEAMVR.md
- For experimental OSS VR → see OSS.md

---

## Troubleshooting

Consult TROUBLESHOOTING.md for common issues and solutions.
