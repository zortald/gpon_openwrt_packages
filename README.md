# OpenWrt for Nokia G-1425-MA, G-140W-C and G-1426-MA

OpenWrt (snapshot, Linux 6.18) for three Nokia GPON ONTs. They are **different hardware**
(G-1426-MA: different SoC, CPU byte order, Wi-Fi and flash layout; G-1425-MA and G-140W-C:
same SoC family, different boards), so each has its own firmware.
**Never flash one model's image onto another.** The stock firmware stays in the other
flash bank as a fallback.

This repository holds:

- the firmware images for the stock web upgrade — see [Releases](https://github.com/zortald/gpon_openwrt_packages/releases);
- the **package feed** with kernel modules and target packages for each build
  (served at `https://zortald.github.io/gpon_openwrt_packages/`);
- [hardware dumps](hardware/) from the units the firmware was verified on.

Русская версия: [README.ru.md](README.ru.md)

## Which one do I have

| | G-1425-MA | G-140W-C | G-1426-MA |
|---|---|---|---|
| Model on the label | G-1425-MA | G-140W-C | G-1426-MA |
| SoC | EcoNet EN7526GT | EcoNet EN7526G | EcoNet EN7528DU |
| Stock `/usr/hwconfig/` | — | — | `G1426MA/` (`BoardInnerID=G1426MA`) |
| Bootloader banner (serial) | `EN751221 ... free bootbase` | `EN751221 ... free bootbase` | `EN7528 ... free bootbase` |
| Wi-Fi in stock | MT7603 + MT7662 (802.11ac) | MT7603 only (the verified stock build has no driver for the 5 GHz MT7662E) | MT7915 (Wi-Fi 6) |

Other revisions or stock firmwares may differ again — check before flashing.

## Download

| | G-1425-MA | G-140W-C | G-1426-MA |
|---|---|---|---|
| Release | [v40](https://github.com/zortald/gpon_openwrt_packages/releases/tag/v40) | [g140w-c-v2.8](https://github.com/zortald/gpon_openwrt_packages/releases/tag/g140w-c-v2.8) (pre-release: install from stock not yet tested on this model) | [g1426ma-v8](https://github.com/zortald/gpon_openwrt_packages/releases/tag/g1426ma-v8) |
| Install from stock | [g1425-openwrt-v40-web-upgrade.bin](https://github.com/zortald/gpon_openwrt_packages/releases/download/v40/g1425-openwrt-v40-web-upgrade.bin) | [g140w-c-openwrt-v2.8-web-upgrade.bin](https://github.com/zortald/gpon_openwrt_packages/releases/download/g140w-c-v2.8/g140w-c-openwrt-v2.8-web-upgrade.bin) | [g1426-openwrt-v8-web-upgrade.bin](https://github.com/zortald/gpon_openwrt_packages/releases/download/g1426ma-v8/g1426-openwrt-v8-web-upgrade.bin) |
| Pre-release (not yet tested on hardware) | [v41](https://github.com/zortald/gpon_openwrt_packages/releases/tag/v41): 5 GHz calibration from stock, usteer, first `sysupgrade` | — | — |
| Update from OpenWrt | — (reinstall from stock; `sysupgrade` from v41) | [g140w-c-openwrt-v2.8-sysupgrade.bin](https://github.com/zortald/gpon_openwrt_packages/releases/download/g140w-c-v2.8/g140w-c-openwrt-v2.8-sysupgrade.bin) (`sysupgrade`) | [g1426-openwrt-v8-sysupgrade.bin](https://github.com/zortald/gpon_openwrt_packages/releases/download/g1426ma-v8/g1426-openwrt-v8-sysupgrade.bin) (`sysupgrade -n`) |

## Hardware

Each column was verified on one unit.

| | G-1425-MA | G-140W-C | G-1426-MA |
|---|---|---|---|
| Stock firmware | `3FE46343AFIA94`, Linux 3.18.21 | `3FE46343AFIA89`, Linux 3.18.21 | `3FE49218HJIJ62` (HJI.J62p05), Linux 3.18.21 |
| SoC | EN7526GT (EN751221 family), MIPS 34Kc, 1 core, big-endian | EN7526G (EN751221 family), MIPS 34Kc, big-endian; OpenWrt uses 1 core | EN7528DU, MIPS 1004Kc, 2 cores × 2 threads, 900 MHz, little-endian |
| RAM | 512 MB DDR3, ~427 MB usable | 512 MB DDR3, ~427 MB usable | 512 MB DDR3, ~430 MB usable |
| Flash | 256 MB SPI NAND, Micron MT29F2G01, 163 blocks reserved for BMT | 256 MB SPI NAND, Winbond W25M02G | 256 MB SPI NAND, Micron MT29F2G01, 143 blocks reserved for BMT |
| Bootloader | EcoNet free bootbase v1.1, dual bank with a boot flag | EcoNet free bootbase v1.1, dual bank with a boot flag | EcoNet free bootbase v1.1, dual bank with a boot flag |
| Ethernet | 4 × 1 GbE, multi-chip-module MT7530 | 4 × 1 GbE, multi-chip-module MT7530 | 4 × 1 GbE, built-in EN7528 switch |
| Wi-Fi | MT7603E 2.4 GHz + MT7662E 5 GHz 2×2 802.11ac | MT7603E 2.4 GHz + MT7662E 5 GHz 2×2 802.11ac | MT7915 DBDC 2.4 + 5 GHz 802.11ax |
| USB | 1 × USB 3.0 | 2 × USB-A (USB 3.0 host) | 1 × USB 3.0 |
| Serial | J521 (GND/TX/RX), 3.3 V, 115200 8N1 | 3.3 V, 115200 8N1 | 3.3 V, 115200 8N1 |
| Not supported | GPON | GPON, phone ports (FXS) | GPON, phone port (FXS) |
| Dumps | [hardware/](hardware/) | — | [hardware/g1426ma/](hardware/g1426ma/) |

## Install (stock web upgrade)

1. **Unplug the uplink cable from port 1.** Stock firmware bridges all ports and runs a DHCP server.
2. Connect a computer to a LAN port and open `http://192.168.1.1/upgrade.cgi`
   (the menu item is hidden for regular users, the page itself works).
   G-1426-MA: log in first (`telecomadmin` / `admintelecom`).
   G-140W-C: log in as the web administrator first (the account depends on the stock firmware).
3. Choose the `.bin` file for **your** model and press Upgrade. The stock updater writes
   the other bank, switches the boot flag and reboots into OpenWrt.
4. G-1425-MA only: if there is no network after the very first boot, power-cycle once.

The images never touch the bootloader or the stock partitions.

| | G-1425-MA | G-140W-C | G-1426-MA |
|---|---|---|---|
| Stock runs from | bank A | bank A | bank B |
| OpenWrt goes to | bank B | bank B | bank A |
| What the stock updater checks | header and CRC | header and CRC (same updater as G-1425-MA) | header, CRC, board ID / part number in the image |

## Defaults

Same on all models unless noted.

| | |
|---|---|
| LAN | `10.100.0.1/24`, DHCP server, ports 2–4 |
| WAN | port 1, DHCP client |
| Wi-Fi | **open** network, 2.4 + 5 GHz, 802.11k/v, country UA: `G1425` / `G140WC` / `G1426` |
| MAC | G-1425-MA: WAN = factory MAC, LAN = −1, Wi-Fi = +1 / +2. G-140W-C: WAN = LAN = factory MAC, Wi-Fi = +1 / +2. G-1426-MA: LAN = factory MAC, WAN = +8, Wi-Fi = +2 / +3 |
| Web UI | LuCI at `http://10.100.0.1` — **set a root password first** |
| Hardware NAT | G-1426-MA and G-140W-C: routed LAN↔WAN flows offloaded to the PPE (~900 Mbit/s, CPU idle) |
| Time zone | Europe/Kyiv |
| Extras | ModemManager + usb-modeswitch (USB modems), USB storage (ext4/vfat), WireGuard |

## Flash layout

On all models the overlay (OpenWrt settings and packages) lives in volume 1 of the
former stock `/data`; volume 0 `data` (8 MB) stays for stock, which would otherwise
reformat the whole partition. Settings survive switching to stock and back.
OpenWrt confirms a good boot (`/etc/init.d/bootconfirm`); after unconfirmed boots the
bootloader falls back to stock.

### G-1425-MA and G-140W-C

The same map on both.

| mtd | Name | Size | Used by |
|---|---|---|---|
| 0 | bootloader | 256 KB | read-only |
| 1 | romfile | 256 KB | read-only |
| 2 | stock_kernel | 3 MB | stock, bank A (read-only) |
| 3 | stock_rootfs | 20 MB | stock, bank A (read-only) |
| 4 | kernel_slave | 3 MB | OpenWrt kernel, bank B |
| 5 | rootfs_slave | 20 MB | OpenWrt squashfs (UBI) |
| 6 | bosa | 256 KB | optics calibration (read-only) |
| 7 | ri | 256 KB | factory data, MAC at 0x3e (read-only) |
| 8 / 9 | flag / flagback | 256 KB each | boot bank selection (4 big-endian words) |
| 10 | stock_config | 10 MB | stock settings incl. GPON (read-only) |
| 11 | openwrt_data | 160 MB | former stock `/data`: `data` (8 MB) + `rootfs_data` (~130 MB) |
| 12 | stock_log | 10 MB | stock logs (read-only) |

### G-1426-MA

| Offset | Name | Size | Used by |
|---|---|---|---|
| 0x0000000 | bootloader | 256 KB | read-only |
| 0x0040000 | romfile | 256 KB | read-only |
| 0x0080000 | kernel | 4 MB | OpenWrt kernel, bank A |
| 0x0480000 | ubi | 30 MB | OpenWrt squashfs (UBI) |
| 0x22c0000 | factory | 256 KB | copy of this unit's Wi-Fi calibration (made on first boot) |
| 0x2300000 | tclinux_slave | 34.5 MB | stock, bank B (read-only) |
| 0x4580000 | bosa | 256 KB | optics calibration (read-only) |
| 0x45c0000 | ri | 256 KB | factory data, MAC at 0x3e (read-only) |
| 0x4600000 | flag / flagback | 256 KB each | boot bank selection (4 little-endian words) |
| 0x4680000 | config | 10 MB | stock settings incl. GPON (read-only) |
| 0x5080000 | openwrt_data | 128 MB | former stock `/data`: `data` (8 MB) + `rootfs_data` (~112 MB) |
| 0xd080000 | oopsfs, log | 6 MB, 10 MB | stock (read-only) |

## Package feed

`downloads.openwrt.org` does not build the econet target, so its kernel modules and
target libraries are not available there. This feed provides them.

Kernel modules only install on the exact kernel they were built with, so there is one
folder per kernel build, named by the kernel package hash. The firmware adds its folder
to apk at first boot:

    https://zortald.github.io/gpon_openwrt_packages/<kernel-hash>/packages.adb

Userspace packages come from the official snapshot feeds. Packages here are signed with
the build key shipped in the firmware.

| Folder | Model | Architecture | Kernel | Firmware |
|---|---|---|---|---|
| [af8c55194775dcae215a7df4c97d3160](af8c55194775dcae215a7df4c97d3160/) | G-1425-MA | `mips_24kc` | 6.18.60~af8c5519…-r1 | v38–v40 |
| [7d991811584158991fd8b3ea9fb13c0b](7d991811584158991fd8b3ea9fb13c0b/) | G-1425-MA, G-140W-C | `mips_24kc` | 6.18.60~7d991811…-r1 | v41, g140w-c-v2.8 |
| [6f45af18e396b5d378e1a1baaebc5a16](6f45af18e396b5d378e1a1baaebc5a16/) | G-1426-MA | `mipsel_24kc` | 6.18.60~6f45af18…-r1 | g1426ma-v5, v6, v8 |

## Back to stock

G-1425-MA and G-140W-C, from OpenWrt (G-140W-C: same flag layout, tested over serial only):

    printf '\0\0\0\0\0\0\0\0\0\0\0\001\0\0\0\016' > /tmp/f; mtd write /tmp/f flag; reboot

G-1426-MA, from OpenWrt:

    en75_chboot factory; reboot

If OpenWrt does not boot at all, the bootloader returns to stock by itself after the
unconfirmed-boot counter runs out. Over serial, the bootloader (vendor login) can switch
banks too: G-1425-MA and G-140W-C — `nander 2f00000 20000 0`, `nander 2f40000 20000 0`, `reset`;
G-1426-MA — `swimg 1`, `reset`.

## Known limitations

| | G-1425-MA | G-140W-C | G-1426-MA |
|---|---|---|---|
| GPON | not supported | not supported | not supported |
| CPU | one core | one core (the second one hangs the system) | all four threads |
| Updates | reinstall from stock; `sysupgrade` from v41 (pre-release) | `sysupgrade` | `sysupgrade` |
| Other | — | phone ports not supported | phone port not supported |

Both are snapshot builds: userspace packages from the official feed may occasionally need
newer libraries than the firmware has.

## Build

Source: [zortald/gpon_openwrt](https://github.com/zortald/gpon_openwrt) (fork of OpenWrt; build
configs in `configs/`). OpenWrt snapshot, kernel 6.18.60.

- G-1425-MA: target `econet/en751221` with
  [openwrt/openwrt#25330](https://github.com/openwrt/openwrt/pull/25330) (EcoNet DSA switch
  support) and a device definition: device tree, partition map, HDR3 image header for the
  stock bootloader and updater.
- G-140W-C: the same target as the G-1425-MA with its own device definition; `econet_eth`
  hardware NAT through the multi-chip-module MT7530, flow control on its TRGMII link, and
  Wi-Fi stations told not to send A-MSDUs inside A-MPDUs (the copy cost a third of the CPU).
- G-1426-MA: target `econet/en7528` (upstream) with a device definition: device tree,
  partition map, HDR3 image header, stock-updater image format, BMT reserve size
  (7 %, `econet,pool-good-block-percent`), and patches to the `econet_eth` driver (RX alignment,
  hardware NAT through the built-in switch, larger RX ring).
