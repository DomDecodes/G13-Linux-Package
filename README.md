# G13-Linux-Package
Working Logitech G13 driver for Linux (2025). Easy installation, graphical configuration, 200 macro slots, automatic driver restart. Fixed all major bugs from previous versions. Supports Ubuntu 22.04+, Debian 12+, Fedora, Arch. KDE and GNOME compatible. 📺 Setup guide: https://youtu.be/c-47W4rEzhk
Update: I noticed that the install script didn't always install the driver in the proper directory. It simply referenced the G13 folder in the download directory. If that folder was removed, the whole thing broke. Furthermore, there was an issue with the driver not starting after some reboots due to a timing issue.
        Also, I added a start G13 Driver entry just in case autostart fails.
        The command to run the script changed to ./InstallSystem.sh

Install locations should be as followed:

/usr/local/bin/g13-driver                    # The driver executable
/opt/g13/Linux-G13-GUI.jar                   # Configuration GUI
/etc/udev/rules.d/99-g13-plugdev.rules      # USB permissions

~/.config/systemd/user/g13-driver.service   # Systemd service definition
~/.local/bin/start-g13-driver               # Start script
~/.local/share/applications/
    ├── g13-start-driver.desktop            # "G13 Start Driver" menu entry
    └── g13-config.desktop                  # "G13 Configuration" menu entry
~/.g13/                                      # Your configuration
    ├── bindings-0.properties               # M1 profile
    ├── bindings-1.properties               # M2 profile
    ├── bindings-2.properties               # M3 profile
    ├── bindings-3.properties               # MR profile
    └── macro-*.properties                  # Macros
```

---

## 🔑 Key Points:

### `/usr/local/bin/g13-driver`
- **Why here?** Standard location for user-installed binaries
- **Permanent?** ✅ Yes, survives system updates
- **In PATH?** ✅ Yes, can run from anywhere
- **Needs sudo to remove?** ✅ Yes

### `/opt/g13/`
- **Why here?** Standard location for third-party applications
- **Permanent?** ✅ Yes, dedicated application folder
- **Needs sudo to remove?** ✅ Yes

### `~/.g13/`
- **Why here?** User configuration, won't affect other users
- **Permanent?** ✅ Yes (unless you delete it)
- **Needs sudo to remove?** ❌ No, it's in your home

### `~/.config/` and `~/.local/`
- **Why here?** XDG Base Directory standard
- **Permanent?** ✅ Yes (unless you delete your user)
- **Needs sudo to remove?** ❌ No, it's in your home

---

## 🗂️ Visual Directory Tree:
```
/                                   (System root)
├── usr/
│   └── local/
│       └── bin/
│           └── g13-driver         ← Driver binary (permanent)
├── opt/
│   └── g13/
│       └── Linux-G13-GUI.jar      ← GUI (permanent)
└── etc/
    └── udev/
        └── rules.d/
            └── 99-g13-plugdev.rules  ← USB rules (permanent)

~/ (Your home)
├── .config/
│   └── systemd/
│       └── user/
│           └── g13-driver.service    ← Service file
├── .local/
│   ├── bin/
│   │   └── start-g13-driver          ← Start script
│   └── share/
│       └── applications/
│           ├── g13-start-driver.desktop   ← Menu entry
│           └── g13-config.desktop         ← Menu entry
└── .g13/                                   ← Your configs
    ├── bindings-0.properties
    ├── bindings-1.properties
    ├── bindings-2.properties
    ├── bindings-3.properties
    └── macro-*.properties
