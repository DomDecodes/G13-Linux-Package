# Logitech G13 Linux Driver

A functional Logitech G13 driver for modern Linux systems with GUI configuration support.

## Features

- 200 macro slots with recording and timing
- 4 binding profiles (M1, M2, M3, M4)
- GUI configuration tool with interactive keypad image
- Automatic driver restart on configuration changes
- System tray integration via Ayatana AppIndicator
- Passthrough key mapping and macro repeat options

## System Requirements

### Software
- Ubuntu 22.04+ or Debian 12+ (tested on Ubuntu Studio 24.04)
- Kernel 5.x or newer
- OpenJDK 11+
- Maven 3.6+
- libusb-1.0-0-dev
- libayatana-appindicator3-dev

### Hardware
- Logitech G13 Advanced Gameboard

## Installation

### 1. Install Dependencies

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install -y build-essential libusb-1.0-0-dev openjdk-17-jdk maven libayatana-appindicator3-dev
```

**Fedora:**
```bash
sudo dnf install -y gcc gcc-c++ make libusb-devel java-17-openjdk-devel maven libayatana-appindicator-gtk3-devel
```

**Arch:**
```bash
sudo pacman -S --needed base-devel libusb jdk-openjdk maven libayatana-appindicator
```

**Note:** Modern Linux desktops require Ayatana AppIndicator for system tray functionality. The older libappindicator is not supported.

### 2. Build

```bash
cd ~/src
git clone https://github.com/YOUR_USERNAME/linux-g13-driver.git
cd linux-g13-driver

# Build C++ driver
cd g13-driver/src
make clean
make

# Build Java GUI
cd ../../g13-config-tool
mvn clean package

# Copy JAR to driver directory
cp target/Linux-G13-GUI.jar ../g13-driver/
```

### 3. Configure udev Rules

```bash
sudo cp g13-driver/src/udev/99-g13-plugdev.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules
sudo udevadm trigger
sudo usermod -a -G plugdev $USER
```

Log out and back in for group changes to take effect.

### 4. Configure Systemd Service

```bash
mkdir -p ~/.config/systemd/user

cat > ~/.config/systemd/user/g13-driver.service << 'EOF'
[Unit]
Description=Logitech G13 Driver
After=graphical-session.target

[Service]
Type=simple
WorkingDirectory=/home/YOUR_USERNAME/src/linux-g13-driver/g13-driver
ExecStart=/home/YOUR_USERNAME/src/linux-g13-driver/g13-driver/Linux-G13-Driver
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
EOF

sed -i "s|YOUR_USERNAME|$USER|g" ~/.config/systemd/user/g13-driver.service

systemctl --user daemon-reload
systemctl --user enable g13-driver.service
```

### 5. Create Desktop Launcher (Optional)

```bash
cat > ~/.local/share/applications/g13-config.desktop << 'EOF'
[Desktop Entry]
Name=G13 Configuration
Comment=Configure Logitech G13 Gameboard
Exec=java -jar /home/YOUR_USERNAME/src/linux-g13-driver/g13-driver/Linux-G13-GUI.jar
Icon=input-gaming
Terminal=false
Type=Application
Categories=Settings;HardwareSettings;
EOF

sed -i "s|YOUR_USERNAME|$USER|g" ~/.local/share/applications/g13-config.desktop
```

### 6. Reboot

Reboot your system for group permissions and system tray integration to function properly.

```bash
sudo reboot
```

## Usage

### Starting the Configuration GUI

```bash
cd ~/src/linux-g13-driver/g13-driver
java -jar Linux-G13-GUI.jar
```

Alternatively, launch "G13 Configuration" from your application menu.

### Configuration Workflow

1. Select a G-key by clicking it on the keypad image
2. Choose binding type:
   - **Passthrough**: Enable checkbox, press a key to bind directly
   - **Macro**: Enable checkbox, select macro slot from dropdown
3. Individual changes auto-save upon interaction
4. Switch profiles using the M1, M2, M3, MR buttons (these correspond to the 4 buttons below the LCD screen on the physical device)
5. Click "Save Current Profile" to write configuration and restart the driver
6. Confirm driver restart when prompted to apply changes

### Creating Macros

1. Select a macro slot (slots 16-199 are user-editable; 0-15 are predefined)
2. Enter a macro name
3. Click "Clear & Record"
4. Execute your key sequence
5. Click "Stop Recording"

Changes are saved automatically.

### Known Issue: Key Indexing

Physical G-keys (G1-G22) map to config file indices G0-G21. The GUI handles this conversion automatically.

## Troubleshooting

### Driver Status

```bash
# Check service status
systemctl --user status g13-driver

# View logs
journalctl --user -u g13-driver -f

# Manual start for debugging
cd ~/src/linux-g13-driver/g13-driver
./Linux-G13-Driver
```

### System Tray Icon

If the tray icon does not appear after installation, reboot the system first. Additional configuration may be required for specific desktop environments:

**GNOME:**
```bash
sudo apt install gnome-shell-extension-appindicator
gnome-extensions enable appindicatorsupport@rgcjonas.gmail.com
```
Log out and back in after enabling.

**KDE Plasma:**
Right-click system tray → Configure System Tray → Entries → Set "Status Notifier Items" to "Always shown"

### Permission Issues

Verify group membership:
```bash
groups | grep plugdev
```

If not present:
```bash
sudo usermod -a -G plugdev $USER
```
Log out and back in.

### Device Detection

```bash
# Verify USB connection
lsusb | grep -i logitech

# Restart driver
systemctl --user restart g13-driver
```

## Project Structure

```
linux-g13-driver/
├── g13-driver/                    # C++ driver
│   ├── src/cpp/                   # Source code
│   ├── src/udev/                  # udev rules
│   ├── Linux-G13-Driver           # Compiled binary
│   └── Linux-G13-GUI.jar          # GUI application
├── g13-config-tool/               # Java GUI source
│   ├── src/main/java/com/booker/g13/
│   └── pom.xml
└── ~/.g13/                        # User configuration
    ├── bindings-[0-3].properties  # Profile configurations
    └── macro-[0-199].properties   # Macro definitions
```

## Changes in This Fork

This fork addresses several issues from the original LordBooker driver:

1. **Auto-save timing**: Corrected `loadingData` flag sequencing in `KeybindPanel.setSelectedKey()` to enable saves when selecting G-keys
2. **Panel synchronization**: Added `saveBindings()` call after dropdown sync in `KeybindPanel.setSelectedMacro()`
3. **Driver restart**: Implemented automatic driver restart with user confirmation after profile saves
4. **Macro editor state**: Modified dropdown behavior to remain enabled for read-only macros while disabling only edit controls

## Credits

- **ecraven**: Original G13 Linux driver
- **LordBooker**: GUI implementation and Ayatana AppIndicator support
- **Community contributors**: Various fixes and improvements

## License

This project inherits licensing from the original repositories. Refer to original authors' terms for details.

## Contributing

Contributions are welcome. Please fork the repository, create a feature branch, test changes thoroughly, and submit a pull request.
