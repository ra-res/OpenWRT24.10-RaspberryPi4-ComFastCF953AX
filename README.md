# Setting Up OpenWRT on Raspberry Pi 4 with ComFast CF-953AX and TP-LINK UE300 Ethernet Adapter

## Required Hardware and Costs

1. **Raspberry Pi 4 Model B** 
   - RAM: 2GB / 4GB / 8GB 
   - Approximate Cost: £30 - £60

2. **MicroSD Card (Minimum 16GB recommended)**
   - Approximate Cost: £8 - £20

3. **ComFast CF-953AX WiFi 6 USB Adapter**
   - Approximate Cost: £10 - £15

4. **TP-LINK UE300 USB 3.0 to Gigabit Ethernet Adapter**
   - Approximate Cost: £10 - £15

5. **Power Supply for Raspberry Pi**
   - Approximate Cost: £10

### Total Approximate Cost: £68 - £120

## Step 1: Downloading the Customized OpenWRT Firmware

1. Visit the [OpenWRT Firmware Selector](https://firmware-selector.openwrt.org/).
2. Select **Raspberry Pi 4** from the device list.
3. Click on **Request Build**.
4. Add the following packages next to the default ones:
    - `kmod-mt7921e`
    - `kmod-mt7921u`
    - `kmod-usb-net-rtl8152`
    - `kmod-usb-core`
    - `kmod-usb-uhci`
    - `kmod-usb-ohci`
    - `kmod-usb2`
    - `usbutils`
    - `openvpn-openssl`
    - `luci-app-openvpn`
    - `adguardhome`
    - `vim`
5. Click on **Request Build** to generate the firmware.
6. Click on **Factory (EX4)** to download the firmware.

## Step 2: Installing the Image on the MicroSD Card

1. Download and install the [Raspberry Pi Imager](https://www.raspberrypi.com/software/).
2. Use the Raspberry Pi Imager to flash the downloaded OpenWRT image onto your MicroSD card.

## Step 3: Network Configuration

Edit the network configuration by adding the following to your `/etc/config/network` file:

```plaintext
config device
        option name 'br-lan'
        option type 'bridge'
        list ports 'eth0'

config interface 'lan'
        option device 'br-lan'
        option proto 'static'
        option ipaddr '192.168.1.1'
        option netmask '255.255.255.0'
        option ip6assign '60'

config interface 'wan'
        option device 'eth1'
        option proto 'dhcp'
```

## Step 4: Setting Up AdGuard Home

Follow the [AdGuard Home setup guide](https://openwrt.org/docs/guide-user/services/dns/adguard-home) and add HaGeZi Ultimate to the DNS blocklist.

## Step 5: Repartitioning the Filesystem (Credits: [rahulelex/resize-storage-on-openwrt-raspberry-pi](https://github.com/rahulelex/resize-storage-on-openwrt-raspberry-pi/tree/master))

1. SSH into the Raspberry PI

```sh
ssh root@192.168.1.1
```

2. Install the Required Packages
```sh
opkg update
opkg install cfdisk resize2fs tune2fs
```

3. Resize the Partition
```sh
cfdisk /dev/mmcblk0
```
Resize the `/dev/mmcblk0p2` partition (enter the desired space). After this, write the changes and quit.

3. Reboot
```sh
reboot
```

4. Remount Root as RO
```sh
mount -o remount,ro /
```

If remounting fails, reboot and remount as ro again.

5. Remove Reserved GDT Blocks
```sh
tune2fs -O^resize_inode /dev/mmcblk0p2
fsck.ext4 /dev/mmcblk0p2 
```

6. Reboot
```sh
reboot
```

7. Resize the F2FS Filesystem
```sh
resize2fs /dev/mmcblk0p2
```

8. Check New Root Partition Size
```sh
df –h
```

This will successfully increase your storage.

## Useful Links

- [Resize Storage on OpenWRT Raspberry Pi](https://github.com/rahulelex/resize-storage-on-openwrt-raspberry-pi)
- [USB-WiFi Discussions](https://github.com/morrownr/USB-WiFi/discussions/145)

---
