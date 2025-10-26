# Release Notes - openSUSE Edition v1.0

## Overview

This is a **special openSUSE-specific release** of the Logitech G13 Linux Driver. openSUSE requires different configuration than Ubuntu/Debian, so this package includes an adapted installer and documentation.

**✅ Tested on:**
- openSUSE Tumbleweed (Kernel 6.8.0, KDE Plasma 5.27)
- openSUSE Leap 15.5

## What's Different from Standard Installation?

openSUSE has unique requirements:

| Requirement | Standard Linux | openSUSE |
|-------------|----------------|----------|
| **plugdev group** | Pre-exists | Must be created |
| **Additional groups** | plugdev only | plugdev + input |
| **uinput permissions** | Usually correct | Requires udev rule |
| **After installation** | Logout/login | **Full reboot required** |
| **Package manager** | apt/dnf/pacman | zypper |

## What's Included

### Installation
- **install-opensuse.sh** - Automated installer for openSUSE
  - Auto-detects username
  - Creates missing groups
  - Installs correct packages with zypper
  - Sets up uinput permissions
  - Handles AppIndicator gracefully if unavailable

### Documentation
- **README-OpenSUSE.md** - Quick start guide
- **InstallGuide-OpenSUSE.md** - Complete installation instructions
- **QuickReference-OpenSUSE.md** - Command cheat sheet
- **OpenSUSE-Technical-Summary.md** - Technical details for maintainers

### Driver Components
- C++ driver with LCD support
- Java GUI for configuration
- Systemd service for auto-start
- Desktop launcher
- Pre-configured macros

## Installation Instructions

### Quick Install

```bash
# Extract the archive
tar -xzf SUSE-linux-g13-driver-v1.0.tar.gz
cd SUSE-linux-g13-driver

# Run the installer
chmod +x install-opensuse.sh
./install-opensuse.sh

# REBOOT (required!)
sudo reboot
```

### Verify Installation

After rebooting:

```bash
# Check driver status (should show "active (running)")
systemctl --user status g13-driver

# Check G13 is detected
lsusb | grep G13

# G13 backlight should be grey/blue (not white!)
```

### Launch Configuration GUI

Search for **"G13 Configuration"** in your application menu, or run:

```bash
java -jar ~/src/linux-g13-driver/g13-driver/Linux-G13-GUI.jar
```

## Features

✅ **All 22 G-keys** programmable  
✅ **4 profiles** (M1, M2, M3, MR buttons)  
✅ **RGB backlight** control  
✅ **Macro recording** with delays  
✅ **200 macro slots** (16 pre-configured, 184 user-editable)  
✅ **Joystick support** (arrow keys or absolute positioning)  
✅ **Auto-start** on login via systemd  
✅ **System tray icon** (if AppIndicator available)  
✅ **Desktop launcher** in application menu  

## Known Issues

### Key Indexing Off-by-One

Physical G-keys are numbered **G1-G22** on the device, but config files use **G0-G21**.

**Example:** Physical G1 → G0 in config

The GUI handles this automatically.

### No Tray Icon

The system tray icon requires `libayatana-appindicator` which may not be available in openSUSE repositories. The driver works perfectly without it - use the application menu launcher instead.

## Requirements

### System Requirements
- openSUSE Tumbleweed or Leap 15.3+
- Java 17 or newer
- USB port
- Internet connection (for dependencies)

### Automatically Installed
- gcc, g++, make
- libusb-1.0
- Java 17 OpenJDK
- Maven
- AppIndicator library (optional)

## Troubleshooting

### Driver Won't Start

```bash
# Check logs
journalctl --user -u g13-driver -n 50

# Common issue: didn't reboot after installation
# Solution: Reboot!
sudo reboot
```

### Backlight Stays White

Driver isn't connecting. Check:

```bash
systemctl --user status g13-driver
journalctl --user -u g13-driver -f
```

### Permission Errors

```bash
# Verify you're in required groups
id | grep -E "(plugdev|input)"

# If not, add yourself and reboot
sudo usermod -a -G plugdev $USER
sudo usermod -a -G input $USER
sudo reboot
```

See **InstallGuide-OpenSUSE.md** for complete troubleshooting guide.

## File Verification

Verify the download integrity:

```bash
sha256sum -c SUSE-linux-g13-driver-v1.0.tar.gz.sha256
```

Should output: `SUSE-linux-g13-driver-v1.0.tar.gz: OK`

## Configuration Files

After installation:

- **Driver:** `~/src/linux-g13-driver/g13-driver/`
- **Config:** `~/.g13/`
  - `bindings-0.properties` through `bindings-3.properties` (profiles)
  - `macro-0.properties` through `macro-199.properties` (macros)
- **Service:** `~/.config/systemd/user/g13-driver.service`
- **Desktop:** `~/.local/share/applications/g13-config.desktop`

## Uninstall

```bash
# Stop driver
systemctl --user stop g13-driver
systemctl --user disable g13-driver

# Remove files
rm -rf ~/src/linux-g13-driver
rm ~/.config/systemd/user/g13-driver.service
rm ~/.local/share/applications/g13-config.desktop

# Remove udev rules
sudo rm /etc/udev/rules.d/99-g13-plugdev.rules
sudo rm /etc/udev/rules.d/99-uinput.rules
sudo udevadm control --reload-rules

# Remove groups (optional)
sudo gpasswd -d $USER plugdev
sudo gpasswd -d $USER input
```

## Support

- **Documentation:** See included markdown files
- **Issues:** Report on GitHub
- **openSUSE version:** Include output of `cat /etc/os-release`

## Credits

- **Original driver:** ecraven/g13
- **Ayatana fork:** LordBooker/Linux-G13-Driver  
- **openSUSE adaptations:** Community contributors
- **Testing:** openSUSE Tumbleweed community

## License

GPL-3.0 License

---

**Important:** This is specifically for **openSUSE**. For other distributions (Ubuntu, Debian, Fedora, Arch), use the standard installation package.

**Key Reminder:** REBOOT after installation - logout/login is not sufficient on openSUSE!
