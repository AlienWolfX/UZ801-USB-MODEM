
## Root Access Setup

### Prerequisites

Download these required files:

- [SuperSU v2.82 SR5](https://github.com/AlienWolfX/UZ801-USB_MODEM/releases/download/rev1/SR5-SuperSU-v2.82-SR5-20171001224502.zip)
- [TWRP Recovery 3.1.1](https://github.com/AlienWolfX/UZ801-USB_MODEM/releases/download/rev1/twrp-3.1.1-0-seed.img)

### Installing SuperSU

1. Push SuperSU zip to device:

```bash
adb push SR5-SuperSU-v2.82-SR5-20171001224502.zip /sdcard
```

2. Boot into TWRP recovery:

```bash
adb reboot bootloader
fastboot boot twrp-3.1.1-0-seed.img
```

> [!NOTE]
> Wait for ADB to detect the device again. This may take a few moments.

3. Install SuperSU through TWRP:

```bash
adb shell
twrp install /sdcard/SR5-SuperSU-v2.82-SR5-20171001224502.zip
reboot
```

## BusyBox Installation

The default BusyBox installation lacks several commands (like `vi`). Here's how to install a complete version:

### Steps

1. Download [BusyBox APK](https://github.com/AlienWolfX/UZ801-USB_MODEM/releases/download/rev1/busybox.apk)

1. Install the APK:

```bash
adb install busybox.apk
```

1. Launch BusyBox:
   - Follow the [View Display Guide](https://github.com/AlienWolfX/UZ801-USB_MODEM?tab=readme-ov-file#view-device-display)
   - Open BusyBox application
   - Tap "Install"
   - Grant root permissions when prompted

### Verification

To verify installation:

```bash
adb shell
busybox --help
```

## APK Modifications

### Locating the APK

First, identify and pull the correct APK:

```bash
# MifiService.apk is typically located at:
adb pull /system/priv-app/MifiService.apk
```

### Setting Up Test Keys

```bash
# Clone Android platform build tools
git clone https://android.googlesource.com/platform/build

# Navigate to security folder
cd build/target/product/security/

# Generate platform key files
openssl pkcs8 -inform DER -nocrypt -in platform.pk8 -out platform.pem
openssl pkcs12 -export -in platform.x509.pem -inkey platform.pem -out platform.p12 -password pass:android -name testkey
keytool -importkeystore -deststorepass android -destkeystore platform.keystore -srckeystore platform.p12 -srcstoretype PKCS12 -srcstorepass android

# Move keystore to working directory
mv platform.keystore {YOUR_WORK_DIR}
```

### Modifying the APK

1. Decompile:

```bash
java -jar apktool.jar d MifiService.apk -o MifiService
```

2. Make modifications in the `assets` folder or any part of the APK that you need to modify

3. Recompile (use `android` as passphrase when prompted):

```bash
java -jar apktool.jar b -o unsigned.apk MifiService
```

4. Sign and align:

```bash
zipalign -v 4 unsigned.apk aligned.apk
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore ./platform.keystore aligned.apk testkey
```

5. Install modified APK:

```bash
adb install -r aligned.apk
```

#### Changing Default IP by [tarokeitaro](https://github.com/AlienWolfX/UZ801-USB_MODEM/issues/11#issuecomment-2473418269)

1. Replace IP addresses in the decompiled APK:
   - Search for all instances of `192.168.100.` in the `MifiService` folder
   - Replace with your desired IP range (e.g., `192.168.1.` or `192.168.0.`)

   > [!NOTE]
   > Use VS Code's global search and replace feature (Ctrl+Shift+H) to find and replace all instances efficiently.

2. Recompile the APK:

```bash
java -jar apktool.jar b -o unsigned.apk MifiService
```

3. Sign and align the APK (PASS: android):

```bash
zipalign -v 4 unsigned.apk aligned.apk
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore ./platform.keystore aligned.apk testkey
```

4. Install the modified APK:

```bash
adb install -r aligned.apk
```

5. Mount `/system` partition as read-write:

```bash
mount -o rw,remount,rw /system
```

6. Edit the initialization script:

```bash
busybox vi /system/bin/initmifiservice.sh
```

   Find line 22: `ifconfig br0 192.168.100.1 up` and change the IP to match your chosen range.

   > [!NOTE]
   > This script sets the bridge interface IP. Make sure it matches your APK modifications.

7. Pull the Android services framework:

```bash
adb pull /system/framework/services.jar
```

8. Decompile the services framework:

```bash
java -jar apktool.jar d -o services services.jar
```

9. Update IP addresses in services framework:
    - Search for all instances of `192.168.100.` in the `services` folder
    - Replace with your chosen IP range to maintain consistency

10. Recompile the services framework:

```bash
java -jar apktool.jar b -c -f -o services.jar services
```

11. Push the modified services framework back to device:

```bash
adb push services.jar /system/framework/
```

12. Remount `/system` as read-only:

```bash
mount -o ro,remount,ro /system
```

13. Reboot device for changes to take effect:

```bash
adb reboot
```

> [!WARNING]
> Ensure all three components (APK, script, and services.jar) use the same IP range, or the device may not function correctly.