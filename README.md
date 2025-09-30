# xconfig

**Intelligent X11 Configuration Tool for GhostBSD**

[![GhostBSD](https://img.shields.io/badge/GhostBSD-25.x%2B-blue.svg)](https://ghostbsd.org/)
[![License](https://img.shields.io/badge/License-BSD--2--Clause-green.svg)](LICENSE)
[![Shell](https://img.shields.io/badge/Shell-POSIX%20sh-lightgrey.svg)](https://en.wikipedia.org/wiki/POSIX)

`xconfig` is a comprehensive X Window System configuration tool for GhostBSD/FreeBSD. It performs intelligent hardware detection, installs the right drivers, and applies optimized Xorg configurations for physical GPUs and common hypervisors. It includes an interactive **bsddialog**-based menu for manual selection.

---

## Supported Hardware & Platforms

### GPUs

* **Intel**: `i915kms` (auto or with external config)
* **AMD**: `amdgpu` (modern) and `radeonkms` (legacy)
* **NVIDIA**: auto classifies to a supported branch (e.g., `nvidia-driver-580/470/390/340/304`)
* **Generic**: `vesa`, `scfb` (syscons framebuffer)

### Virtualization

* **VirtualBox** (vboxguest/vboxservice enable & start)
* **VMware**
* **QEMU/KVM**
* **Microsoft Hyper-V**
* **Unknown Hypervisors**: `vesa` fallback

---

## Requirements

* GhostBSD / FreeBSD with `pkg`
* `bsddialog` (preferred) or `dialog`
* X Window components present on the system
* Root privileges (`sudo`)

> The script will also use `/xdrivers` (if available) for offline `.pkg` files, falling back to `pkg` repos otherwise.

---

## Installation

### Quick Install

```bash
# Clone
git clone https://github.com/yourusername/xconfig.git
cd xconfig

# Install the script
sudo install -m 755 bin/xconfig /usr/local/bin/xconfig

# Install external config templates (system-wide)
sudo mkdir -p /usr/local/etc/X11/cardDetect
sudo cp -r cardDetect/* /usr/local/etc/X11/cardDetect/
```

> The script also looks for `cardDetect` **next to the script** (`$SCRIPT_DIR/cardDetect`) so you can run in-tree during development.

---

## Usage

### Automatic (recommended)

```bash
sudo xconfig auto
```

Detects hardware and applies the best configuration (including NVIDIA/AMD/Intel and hypervisors).

### Interactive setup

```bash
sudo xconfig setup
# or
sudo xconfig manual
```

Opens the bsddialog/dialog UI. Choices are built safely via `set --` (no fragile quoting).

### Direct targets

```bash
# GPUs
sudo xconfig nvidia         # Auto-detect the correct NVIDIA branch
sudo xconfig intel          # Intel with external config template
sudo xconfig intel-auto     # Intel auto (no config file)
sudo xconfig amd            # Auto decide: amdgpu or radeonkms (based on device)
sudo xconfig amdgpu         # Force amdgpu template
sudo xconfig radeonkms      # Force radeonkms template

# Virtualization
sudo xconfig virtualbox
sudo xconfig vmware
sudo xconfig qemu
sudo xconfig hyperv

# Fallback / recovery
sudo xconfig vesa
sudo xconfig scfb
sudo xconfig safe

# Advanced
sudo xconfig dual           # Dual-monitor template
```

### System Info / Debug

```bash
sudo xconfig debug
```

Prints FreeBSD version, detected GPUs, candidate drivers, current Xorg status, presence of `/xdrivers`, and available config templates.

---

## External Configuration Files

Place templates in either:

* `$(dirname "$0")/cardDetect/` (development/portable), or
* `/usr/local/etc/X11/cardDetect/` (system-wide)

| File                    | Purpose                  |
| ----------------------- | ------------------------ |
| `XF86Config.intel`      | Intel (i915kms, tuned)   |
| `XF86Config.amdgpu`     | Modern AMD (Glamor/DRI3) |
| `XF86Config.radeonkms`  | Legacy AMD (DRI2)        |
| `XF86Config.virtualbox` | VirtualBox               |
| `XF86Config.vmware`     | VMware                   |
| `XF86Config.qemu`       | QEMU/KVM                 |
| `XF86Config.hyperv`     | Microsoft Hyper-V        |
| `XF86Config.scfb`       | Syscons framebuffer      |
| `XF86Config.vesa`       | Generic VESA             |
| `XF86Config.safe`       | Minimal recovery         |
| `XF86Config.dual`       | Dual-monitor starter     |

> When a template is applied, the existing `/etc/X11/xorg.conf` is backed up to `/etc/X11/backup/xorg.conf.YYYYMMDD_HHMMSS`.

---

## NVIDIA Branch Selection (heuristic)

The script classifies the device and selects an appropriate package branch:

* Newer/modern: `nvidia-driver-580`
* Previous gens: `nvidia-driver-470`
* Older gens: `nvidia-driver-390`, `nvidia-driver-340`, `nvidia-driver-304`

> Exact package names available on your system may vary; the script lists available `nvidia-driver-*` if installation fails.

---

## Offline Drivers (`/xdrivers`)

If `/xdrivers/drivers-list` and corresponding `.pkg` files exist, `xconfig` installs from there first:

```
/xdrivers/drivers-list      # lines: "<pkgname> <filename.pkg>"
/xdrivers/<filename.pkg>    # package payloads
```

Falls back to `pkg install` on failure.

---

## License

BSD 2-Clause “Simplified” License — see [LICENSE](LICENSE).

