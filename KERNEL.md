# Building the Patched Kernel for HTC Vive Pro 2 Support

This document describes how to build and install the patched Linux kernel required for Vive Pro 2 support.  
These patches originate from the work of **RononDex**, **CertainLach**, and community contributors.

The process below is intended for Arch Linux and Arch-based systems.

---

## 1. Clone the Kernel Source (RononDex Branch)

Clone the repository and move into the kernel directory:

    git clone https://github.com/RononDex/vive-pro-2-on-linux.git
    cd vive-pro-2-on-linux/kernel/

---

## 2. Update Rust Toolchains

The patched kernel and associated utilities require up-to-date Rust toolchains.

Update existing toolchains:

    rustup update

Or install the nightly toolchain explicitly:

    rustup toolchain install nightly

---

## 3. Import Kernel Developer Keys

Kernel modules may be signed with bundled developer keys.  
Import all PGP keys included in the repository:

    for key in ./keys/pgp/*.asc; do
        gpg --import "$key"
    done

You can verify imported keys using:

    gpg --list-keys

---

## 4. Build the Patched Kernel Package

Enable parallel compilation to speed up build time:

    export MAKEFLAGS="-j $(nproc)"

Build the kernel package:

    makepkg -sc

This step compiles the kernel, modules, and associated patches.

---

## 5. Install the Patched Kernel

After a successful build, install the package with:

    makepkg -si

Follow any on-screen prompts.  
Once the installation completes, **update your bootloader configuration**:

- **GRUB**:  
      sudo grub-mkconfig -o /boot/grub/grub.cfg

- **systemd-boot**:  
      sudo bootctl update

- **rEFInd**:  
  Should detect kernels automatically, but verify entries in `/boot`.

Reboot into the new kernel after installation.

---

## 6. Verify Kernel Version

After rebooting, confirm the patched kernel is running:

    uname -r

You should see a kernel version string matching the patched build.

---

## Notes

- Building a kernel requires significant CPU time and disk space.  
- Out-of-tree patches may break with newer toolchains—ensure your system is up to date.  
- If you encounter build failures, verify Rust is correctly installed and that your base-devel group is fully installed.

---
