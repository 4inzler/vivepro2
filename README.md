# HTC Vive Pro 2 on Linux (Updated)

This repository provides an updated and streamlined guide for enabling the **HTC Vive Pro 2** on Linux systems, with a primary focus on **Arch Linux** and other cutting-edge distributions.

Linux VR support continues to improve, and with the correct components in place, SteamVR 2.0+ can deliver a stable and usable VR experience.

Special thanks to **CertainLach**, **santeri3700**,**RononDex** and community contributors for their work on kernel patches, drivers, and documentation that make Vive Pro 2 support on Linux possible.

Helpful resources:
- LVRA Wiki: https://lvra.gitlab.io/
- VR on Linux: https://vronlinux.org/
- SteamVR Linux Support: https://help.steampowered.com/en/faqs/view/18A4-1E10-8A94-3DDA
- r/virtualreality_linux: https://www.reddit.com/r/virtualreality_linux/

---

## Status
This guide is a work in progress.  
Last updated: 2024-01-10

---

## Setup

### 1. Build and Install the Patched Kernel
From the root of this repository:

    cd kernel

Follow the instructions in **KERNEL.md** to patch and build the kernel with Vive Pro 2 support.

---

### 2. Install PolKit Rule for CAP_SYS_NICE

A PolKit rule is provided to allow SteamVR’s compositor and/or Monado to acquire the `CAP_SYS_NICE` capability without requiring elevated privileges.

Default executable paths:
- SteamVR compositor:  
  /home/$USER/.steam/steam/steamapps/common/SteamVR/bin/linux64/vrcompositor-launcher
- Monado service:  
  /usr/bin/monado-service

Adjust the rule if your installation paths differ.

#### Install the rule:

    sudo cp ./polkit-vr-setcap-nice.rules /etc/polkit-1/rules.d/90-vr-setcap-nice.rules

#### Apply and test:

    sudo systemctl restart polkit.service
    pkexec setcap CAP_SYS_NICE=eip /home/$USER/.steam/steam/steamapps/common/SteamVR/bin/linux64/vrcompositor-launcher
    pkexec setcap CAP_SYS_NICE=eip /usr/bin/monado-service

If configured correctly, these commands should not prompt for a password.

---

### 3. Install SteamVR and the Vive Pro 2 Linux Driver

Reference driver project by CertainLach:  
https://github.com/CertainLach/VivePro2-Linux-Driver

See **STEAMVR.md** for complete installation instructions.

---

### 5. Install LibSurvive (optional but recommended)

LibSurvive provides improved Lighthouse tracking tools and device support.  
Install it with:

    git clone https://github.com/cntools/libsurvive.git --recursive
    cd libsurvive

Install the udev rules:

    sudo cp ./useful_files/81-vive.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules && sudo udevadm trigger

This ensures Lighthouse base stations and tracked devices are accessible to user-space drivers.

---

### 6. Rebuilding & Reinstalling the Vive Pro 2 Driver

If you have an existing local checkout at:

`~/Documents/random/vive-pro-2-on-linux/VivePro2-Linux-Driver`

Rebuild and reinstall the driver with:

    cd ~/Documents/random/vive-pro-2-on-linux/VivePro2-Linux-Driver

    # 1) Build main driver (nightly toolchain)
    cargo +nightly build --release

    # 2) Build lens-server for Windows target (requires x86_64-w64-mingw32)
    rustup target add x86_64-pc-windows-gnu
    cargo +nightly build --release -p lens-server --target x86_64-pc-windows-gnu

    # 3) Stage lens-server.exe for installation
    cp target/x86_64-pc-windows-gnu/release/lens-server.exe dist-proxy/lens-server/

    # 4) Install/patch into SteamVR
    cd dist-proxy
    ./install.sh

    # 5) Reapply compositor capability (requires sudo)
    sudo setcap CAP_SYS_NICE=eip ~/.local/share/Steam/steamapps/common/SteamVR/bin/linux64/vrcompositor-launcher

After completing the steps above, launch Steam → SteamVR and perform Room Setup.

---

## Playing VR Games

- For SteamVR usage → see **STEAMVR.md**  
- For experimental open-source VR setups → see **OSS.md**

---

## Additional Resources

SteamVR Linux issue tracker:  
https://github.com/ValveSoftware/SteamVR-for-Linux/

Monado issue tracker:  
https://gitlab.freedesktop.org/monado/monado/-/issues
