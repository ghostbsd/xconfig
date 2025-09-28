# xconfig

**Intelligent X11 Configuration Tool for GhostBSD**

[![FreeBSD](https://img.shields.io/badge/FreeBSD-14.x%2B-red.svg)](https://www.freebsd.org/)
[![GhostBSD](https://img.shields.io/badge/GhostBSD-23.x%2B-blue.svg)](https://ghostbsd.org/)
[![License](https://img.shields.io/badge/License-BSD--2--Clause-green.svg)](LICENSE)
[![Shell](https://img.shields.io/badge/Shell-POSIX%20sh-lightgrey.svg)](https://en.wikipedia.org/wiki/POSIX)

xconfig is a comprehensive X Window System configuration automation tool designed specifically for FreeBSD and GhostBSD. It provides intelligent hardware detection, automatic driver installation, and optimized X11 configurations for a wide range of graphics hardware and virtualization platforms.

## Features

- **Intelligent Hardware Detection**: Automatically detects graphics cards, virtualization platforms, and system configurations
- **Automated Driver Installation**: Installs and configures appropriate graphics drivers with proper kernel module loading
- **Comprehensive Platform Support**: Works with Intel, AMD, NVIDIA graphics, and all major virtualization platforms
- **Interactive Configuration**: User-friendly dialog-based interface for manual configuration
- **Modular Design**: External configuration files for easy maintenance and customization
- **Recovery Mode**: Safe mode configurations for troubleshooting graphics issues
- **Multi-Monitor Support**: Templates and configurations for dual-monitor setups
- **Detailed Logging**: Comprehensive logging system for debugging and troubleshooting

## Supported Hardware & Platforms

### Graphics Hardware
- **Intel Graphics**: i915kms driver with hardware acceleration
- **AMD Graphics**: 
  - Modern cards: amdgpu driver with DRI3 support
  - Legacy cards: radeonkms driver with DRI2 support
- **NVIDIA Graphics**: 
  - Latest cards: nvidia-driver-580 (RTX 50/40/30 series)
  - Legacy support: nvidia-driver-470/390/340/304
- **Generic**: VESA and SCFB (FreeBSD syscons framebuffer) drivers

### Virtualization Platforms
- **VirtualBox**: Guest additions integration
- **VMware**: VMware Tools compatibility
- **QEMU/KVM**: Cirrus and virtio graphics
- **Microsoft Hyper-V**: Enhanced session support
- **Generic VMs**: VESA fallback for unknown hypervisors

## Installation

### Quick Install
```bash
# Clone the repository
git clone https://github.com/yourusername/xconfig.git
cd xconfig

# Make executable and install
chmod +x bin/xconfig
sudo cp bin/xconfig /usr/local/bin/
sudo cp -r cardDetect /usr/local/etc/X11/
```

### Manual Installation
```bash
# Copy script
sudo install -m 755 bin/xconfig /usr/local/bin/

# Create configuration directory
sudo mkdir -p /usr/local/etc/X11/cardDetect
sudo cp cardDetect/* /usr/local/etc/X11/cardDetect/
```

## Usage

### Automatic Configuration (Recommended)
```bash
sudo xconfig auto
```
Detects hardware automatically and applies the best configuration.

### Interactive Setup
```bash
sudo xconfig setup
```
Opens a dialog-based interface for manual configuration selection.

### Direct Configuration
```bash
# Configure for specific hardware
sudo xconfig nvidia          # Auto-detect NVIDIA driver version
sudo xconfig intel          # Intel graphics with config file
sudo xconfig intel-auto     # Intel graphics with auto-detection
sudo xconfig amd             # Auto-detect AMD driver
sudo xconfig amdgpu         # Force amdgpu driver
sudo xconfig radeonkms      # Force radeonkms driver

# Configure for virtualization
sudo xconfig virtualbox     # VirtualBox guest
sudo xconfig vmware         # VMware guest
sudo xconfig qemu           # QEMU/KVM guest
sudo xconfig hyperv         # Microsoft Hyper-V guest

# Fallback and recovery modes
sudo xconfig vesa           # VESA compatible mode
sudo xconfig scfb           # FreeBSD syscons framebuffer
sudo xconfig safe           # Minimal recovery mode

# Advanced configurations
sudo xconfig dual           # Dual monitor template
```

### System Information
```bash
sudo xconfig debug
```
Displays comprehensive system information including:
- FreeBSD version and hardware details
- Available graphics drivers and packages
- Current X11 configuration status
- External configuration files availability

## Configuration Files

The tool includes optimized X11 configuration files for each supported platform:

| File | Purpose | Hardware/Platform |
|------|---------|------------------|
| `XF86Config.intel` | Intel graphics | i915kms driver with SNA acceleration |
| `XF86Config.amdgpu` | Modern AMD | amdgpu driver with Glamor acceleration |
| `XF86Config.radeonkms` | Legacy AMD | radeonkms driver for older cards |
| `XF86Config.virtualbox` | VirtualBox | Guest additions integration |
| `XF86Config.vmware` | VMware | VMware Tools compatibility |
| `XF86Config.qemu` | QEMU/KVM | Cirrus graphics driver |
| `XF86Config.hyperv` | Hyper-V | Microsoft virtualization platform |
| `XF86Config.scfb` | FreeBSD native | Syscons framebuffer driver |
| `XF86Config.vesa` | Universal | VESA compatible fallback |
| `XF86Config.safe` | Recovery | Minimal troubleshooting config |
| `XF86Config.dual` | Multi-monitor | Dual display template |

## 🔧 Advanced Usage

### Custom Driver Installation
The tool supports GhostBSD's `/xdrivers` directory for offline driver packages:
```bash
# Tool automatically checks /xdrivers for packages
# Falls back to pkg repository if not available
sudo xconfig nvidia
```

### NVIDIA Driver Version Selection
NVIDIA driver versions are automatically selected based on hardware:
- **RTX 50/40/30 series**: nvidia-driver-580
- **RTX 20/GTX 16 series**: nvidia-driver-470
- **GTX 10/900 series**: nvidia-driver-390
- **GTX 600-800 series**: nvidia-driver-340
- **Legacy cards**: nvidia-driver-304

## Troubleshooting

### X11 Won't Start
```bash
# Try safe mode
sudo xconfig safe

# Check logs
tail -f /var/log/xconfig.log
tail -f /var/log/Xorg.0.log
```

### Graphics Performance Issues
```bash
# Verify correct driver is loaded
kldstat | grep -E "(nvidia|amdgpu|radeonkms|i915kms)"

# Check hardware detection
sudo xconfig debug
```

## 📄 License

This project is licensed under the BSD 2-Clause License - see the [LICENSE](LICENSE) file for details.

