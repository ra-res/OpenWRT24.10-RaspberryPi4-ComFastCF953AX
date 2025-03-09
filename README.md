# Setting Up OpenWRT on Raspberry Pi 4 with ComFast CF-953AX and TP-LINK UE300 Ethernet Adapter

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

### SSH into the Raspberry PI

```sh
ssh root@192.168.1.1
```

### Step 1: Install the Required Packages
```sh
opkg update
opkg install cfdisk resize2fs tune2fs
```

### Step 2: Resize the Partition
```sh
cfdisk /dev/mmcblk0
```
Resize the `/dev/mmcblk0p2` partition (enter the desired space). After this, write the changes and quit.

### Step 3: Reboot
```sh
reboot
```

### Step 4: Remount Root as RO
```sh
mount -o remount,ro /
```

If remounting fails, reboot and remount as ro again.

### Step 5: Remove Reserved GDT Blocks
```sh
tune2fs -O^resize_inode /dev/mmcblk0p2
fsck.ext4 /dev/mmcblk0p2 
```

### Step 6: Reboot
```sh
reboot
```

### Step 7: Resize the F2FS Filesystem
```sh
resize2fs /dev/mmcblk0p2
```

### Step 8: Check New Root Partition Size
```sh
df –h
```

This will successfully increase your storage.

## Useful Links

- [Resize Storage on OpenWRT Raspberry Pi](https://github.com/rahulelex/resize-storage-on-openwrt-raspberry-pi)
- [USB-WiFi Discussions](https://github.com/morrownr/USB-WiFi/discussions/145)

---