
> Based on Debian 6.7 with MSM8916 mainline support

## Prerequisites

- ADB tools installed
- Device with USB debugging enabled
- Backup of current firmware (recommended)

## Installation Steps

1. Download the installation package:

```bash
wget https://download.wvthoog.nl/openstick-uz801-v3.0.zip
```

2. Extract the archive:

```bash
unzip openstick-uz801-v3.0.zip
cd openstick-uz801
```

3. Boot into fastboot mode:

```bash
adb reboot bootloader
```

4. Flash the firmware:

- **Windows**:

```batch
flash.bat
```

- **Linux/macOS**:

```bash
chmod +x flash.sh
./flash.sh
```

5. Wait for the flashing process to complete. Do not disconnect the device.

## Verification

After successful installation run:

`ssh user@192.168.200.1`

Password is: **1**

## Additional Resources

- [Wim van 't Hoog's Blog](https://wvthoog.nl/openstick/) - Original project documentation
- [Debian MSM8916 Wiki](https://wiki.debian.org/msm8916) - Platform documentation
- [Troubleshooting Guide](troubleshooting.md) - Common issues and solutions.
