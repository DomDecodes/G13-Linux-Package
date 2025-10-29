# Logitech G13 Linux Driver - Installation Update

## Quick Installation

An automated installation script is now available that handles all dependencies and setup:

```bash
cd linux-g13-driver
chmod +x install.sh
./install.sh
```

After installation completes, **reboot your system** to apply all changes.

## Supported Distributions

The installation script now supports:

- **Ubuntu** and derivatives (Pop!_OS, Linux Mint, Elementary OS)
- **KDE Neon** (fully supported as of latest update)
- **Debian**
- **Arch Linux** and derivatives (Manjaro, EndeavourOS)
- **Fedora, RHEL, CentOS, Rocky Linux, AlmaLinux**
- **openSUSE**

The script will automatically:
- Detect your distribution
- Install all required dependencies
- Build the C++ driver
- Build the Java GUI
- Configure udev rules
- Setup systemd service for auto-start
- Add your user to the plugdev group

## Post-Installation

After rebooting, the driver starts automatically on login. You can:

- Check driver status: `systemctl --user status g13-driver`
- Stop the driver: `systemctl --user stop g13-driver`
- Start the driver: `systemctl --user start g13-driver`
- Launch configuration GUI: `java -jar g13-driver/Linux-G13-GUI.jar`

## Configuration

Configuration files are stored in `~/.g13/`:
- `bindings-N.properties` - Key bindings for each M-mode (0-3)
- `macro-N.properties` - Macro definitions (0-199)

**Note:** There is a known off-by-one indexing issue - physical key G1 appears as G0 in config files, G2 as G1, etc. Subtract 1 from the physical key number when editing config files.

## Troubleshooting

If the driver doesn't start after installation:
1. Ensure you rebooted after installation
2. Check USB connection and replug the G13
3. View logs: `journalctl --user -u g13-driver -f`
4. Verify udev rules: `ls -l /etc/udev/rules.d/99-g13*`

---

**System Requirements:**
- Linux kernel with uinput support
- USB access (handled by udev rules)
- Java 17+ (for GUI configuration tool)
