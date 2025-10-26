
A couple of months ago, I purchased this 4G LTE USB dongle from Shopee for around 300+ PHP. Out of curiosity, I searched the internet to see if there was a way to modify the horrible web UI of the device. Here are some images of the device along with the board and its chips.

| ![front](https://i.ibb.co/55fNj7D/front.jpg "front")    | ![back](https://i.ibb.co/2s72SLL/back.jpg "back")       |
| ------------------------------------------------------- | ------------------------------------------------------- |
| ![board1](https://i.ibb.co/5vZXKMQ/board1.jpg "board1") | ![board2](https://i.ibb.co/1Z8WZq0/board2.jpg "board2") |
| ![front](https://i.ibb.co/sbChyH9/cpu.jpg "front")      | ![back](https://i.ibb.co/Z8mh33d/storage.jpg "back")    |
| ![board1](https://i.ibb.co/jTwXYQ8/soc1.jpg "board1")   | ![board2](https://i.ibb.co/GWfPq4M/soc2.jpg "board2")   |
| ![front](https://i.ibb.co/dQ82vyz/soc3.jpg "front")     |                                                         |

## ADB

Some devices do not have ADB enabled by default. To enable ADB:

1. Connect to the device and open this URL in a browser: `http://192.168.100.1/usbdebug.html`.
2. Verify by:

```bash
adb devices
```

## Device Specifications

The heart of the dongle is an MSM8916, running a stripped-down version of Android 4.4.4 KitKat. Interestingly, **the setup restricts the use of two CPU cores, likely to prevent the device from overheating**.

### Processor Information

```bash
root@msm8916_32_512:/ $ cat /proc/cpuinfo

processor       : 0
model name      : ARMv7 Processor rev 0 (v7l)
BogoMIPS        : 38.40
Features        : swp half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x0
CPU part        : 0xd03
CPU revision    : 0

processor       : 1
model name      : ARMv7 Processor rev 0 (v7l)
BogoMIPS        : 38.40
Features        : swp half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x0
CPU part        : 0xd03
CPU revision    : 0

Hardware        : Qualcomm Technologies, Inc MSM8916
Revision        : 0000
Serial          : 0000000000000000
Processor       : ARMv7 Processor rev 0 (v7l)
```

### Memory Information

```bash
root@msm8916_32_512:/ $ cat /proc/meminfo

MemTotal:         397808 kB
MemFree:           36072 kB
Buffers:            7876 kB
Cached:           115924 kB
SwapCached:         1024 kB
Active:            93944 kB
Inactive:         128576 kB
Active(anon):      45392 kB
Inactive(anon):    54336 kB
Active(file):      48552 kB
Inactive(file):    74240 kB
Unevictable:         740 kB
Mlocked:               0 kB
SwapTotal:        196604 kB
SwapFree:         193548 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:         98596 kB
Mapped:            57292 kB
Shmem:               268 kB
Slab:              33280 kB
SReclaimable:      11016 kB
SUnreclaim:        22264 kB
KernelStack:        5112 kB
PageTables:         5328 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:      395508 kB
Committed_AS:    4824384 kB
VmallocTotal:     499712 kB
VmallocUsed:       50288 kB
VmallocChunk:     311324 kB
```

## WebUI

The web UI is so poorly designed that you can bypass it entirely by simply changing the URL and calling **_main.html_** to access the main page. This goes the same as with the other pages.

## Display

The device runs Android with LCD support enabled. First, keep the screen awake and wake the device (the device may otherwise present a blank screen):

```bash
adb shell settings put system screen_off_timeout 2147483647
adb shell input keyevent 26
```

### adbcontrol

Download: [adbcontrol.zip](https://github.com/AlienWolfX/UZ801-USB_MODEM/releases/download/rev1/adbcontrol.zip)

Quick steps:

```bash
unzip adbcontrol.zip
cd adbcontrol
# Edit config.properties to point to your adb executable and an output folder
# Example config.properties entries:
# adbCommand=C:\path\to\adb.exe
# localImageFilePath=C:\tmp\adbcontrol_images
java -jar adbcontrol.jar
```
