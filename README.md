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
        option ipaddr '10.10.10.1'
        option netmask '255.255.255.0'
        option ip6assign '60'

config interface 'wan'
        option device 'eth1'
        option proto 'dhcp'
```

## Step 4: Setting Up AdGuard Home

Follow the [AdGuard Home setup guide](https://openwrt.org/docs/guide-user/services/dns/adguard-home) using HaGeZi Ultimate.

## Useful Links

- [Resize Storage on OpenWRT Raspberry Pi](https://github.com/rahulelex/resize-storage-on-openwrt-raspberry-pi)
- [USB-WiFi Discussions](https://github.com/morrownr/USB-WiFi/discussions/145)

---
