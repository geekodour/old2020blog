---
title: "Some notes on WiFi and the wireless subsystem on Linux"
date: 2019-01-21T16:42:09+05:30
draft: true
categories:
  - Linux
  - Networks
  - Sysadmin
slug: WiFi-in-linux
---

It's been quite sometime since I posted anything proper on the internet, but recently I was reading about how WiFi is
deployed in enterprise and the wireless subsystem in the Linux kernel. So I thought it would be nice to have
a self note about it in form of a short blogpost.

# Table of contents
- [Wireless communication](#wireless-communication)
- [How our machines connect to our WNICs](#how-our-machines-connect-to-our-wnics)

## Wireless communication
A WNIC can operate in two ways:

- Infrastructure mode ( AP Topology )
    - uses Infrastructure BSS
    - BSSID is usually the MAC address of the AP

- AdHoc mode
    - uses Independent BSS ( IBSS )
    - BSSID for AdHoc is the IBSS which is a randomly generated bit string for each peer
    - Do not use a mac address
    - IBSS
        - A BSS that forms a self-contained network, and in which no access to a distribution system [(DS)](https://en.wikipedia.org/wiki/Wireless_distribution_system) is available.

In the **infrastructure mode** there are two types of Service sets:

- BSS (Basic SS)
    - A BSS consists of a group of computers and one AP, which links to a wired LAN
- ESS (Extended SS)
    - Multiple BSS
    - An ESS consists of more than one AP. An ESS lets mobile users roam anywhere within the area covered by multiple APs.
    - When in an ESS, user is unaware of which BSS they belong to they will reuse the SSID/ESSID.

> ### A note about SSID, BSSID and ESSID
> - **Service Set Identifier (SSID)** : SSID is the Name of a Network (name of the WLAN)
> - **Basic Service Set Identifier (BSSID)** : Identify Access Points and Their Clients in a multiple AP setup
> - **ESSID** : is the same as SSID here. 

## How our machines connect to our WNICs

```md
+-----------+   +--------+   +------+  +-----------+
| userspace +-->+ kernel +-->+ WNIC +-->external AP|
+-----------+   +--------+   +------+  +-----------+
```
Most of the info here on will be Linux specific.



It's important to understand there are 2 paths in which userspace communicates with the kernel when we're talking about WiFi: 

- **Data Path/(TX/RX) Path**: The data being received is passed from the wireless driver to the netdev core 
- **Control Path**: scan/authentication/association


## WNIC
WNICs can be of two types based on management of **MLME**(MAC Sublayer Management Entity) :

- SoftMAC
    - Example Hardware: Most laptops
    - Example Hardware Driver: [iwlwifi](https://wireless.wiki.kernel.org/en/users/drivers/iwlwifi)
- HardMAC/FullMAC
    - Example Hardware: Rpi3
    - Example Hardware Driver: [brcm80211](https://wireless.wiki.kernel.org/en/users/drivers/brcm80211)

If the WNICs wants to talk to the kernel then it has to use the **cfg80211** interface.

- HardMAC WNICs using HardMAC drivers have to implement the cfg80211 interfaces fully themselves.
- SoftMAC WNICs using SoftMAC drivers can use linux kernel's **mac80211** framework to talk to cfg80211.

![nl80211](/img/nl80211.png)

> **NOTE:** [MLME (MAC Sublayer Management Entity)](http://edge.cs.drexel.edu/regli/Classes/CS680/Papers/802.11/Ch11.pdf)
> MLME is the management entity where the Physical layer (PHY) MAC state machines reside.

## Kernel
- [**cfg80211**](https://www.kernel.org/doc/html/v4.9/80211/cfg80211.html): Kernel side of configuration management for wireless devices.
    - works with FullMAC-drivers and also with mac80211-based drivers.
- [**mac80211**](https://www.kernel.org/doc/html/v4.9/80211/mac80211.html) A driver API for SoftMAC wireless cards.
    - mac80211 registers itself with cfg80211 by using the `cfg80211_ops`
    - specific HW driver(eg. iwlwifi) registers itself with mac80211 by using the `ieee80211_ops`

## Userspace
- [**nl80211**](https://wireless.wiki.kernel.org/en/developers/documentation/nl80211) : User-space side of configuration management for wireless devices. It is a Netlink-based user-space protocol.
    - [Netlink](https://en.wikipedia.org/wiki/Netlink) is designed and used for transferring miscellaneous networking information between the kernel space and userspace processes.
        - The [libnl suite](https://www.infradead.org/~tgr/libnl/) is a collection of libraries providing APIs to netlink protocol based Linux kernel interfaces. It is designed for building command line tools and is blocking in nature for a generic netlink family discovery.
        - [ELL](https://01.org/ell) (successor to libnl, maybe?) 

- `wpa_supplicant` is supposed to handle MLME commands
    - If you want to use WPA/WPA2 you need to use `wpa_supplicant`
    - it is probably being replaced by [`iwd`](https://wiki.archlinux.org/index.php/Iwd)
        - `iwd` has a interactive command mode called `iwctl`

- [**iw**](https://wireless.wiki.kernel.org/en/users/documentation/iw): It is a new nl80211 based CLI configuration utility for wireless devices.
    - successor to `iwconfig`
    - not related to `iwd` in terms of devlopment
    - When using this tool, it is important to distinguish between the WiFi hardware (or PHYsical layer) and the WiFi interface (or MAC layer).
        - PHY layer: `iw phy`
        - Interface Layer: `iw dev`

> **Additional Jargons:**
>
> - WifiAP == Hotspot
> - Wifi Client == Wifi station

### About Multiple interfaces

[Multiple interfaces](https://wireless.wiki.kernel.org/en/users/documentation/iw/vif) can be used to run something like a station on `wlan0` and an AP on `wlan1` (depending on the HW). Otherwise we can also have the connection coming on a `eth0` and the AP on `wlan0`.

You can use linux virtual networking devices like [bridge](https://wiki.archlinux.org/index.php/Network_bridge) to pass the internet over.

----

### Some random commands
```
$ sudo iw dev wlp2s0 scan | less
$ sudo iwlist wlp2s0 scanning
```

Useful Links:

- [Wikipedia - WNIC](https://en.wikipedia.org/wiki/Wireless_network_interface_controller)
- [Source - nl80211.h](https://elixir.bootlin.com/linux/latest/source/include/uapi/linux/nl80211.h)
- [Wikipedia - WiFi Management Frames](https://en.wikipedia.org/wiki/IEEE_802.11#Management_frames)
- [How nl80211 works](https://stackoverflow.com/questions/21456235/how-nl80211-library-cfg80211-work)
- [HostAPd and wpa_supplicant](https://w1.fi/)
- [Arch Linux wiki on SW AP](https://wiki.archlinux.org/index.php/software_access_point)
- [Enterprise WAP](https://me.m01.eu/blog/2012/05/wpa-2-enterprise-from-scratch-on-a-raspberry-pi/)
- [Short intro to iw usage](http://ict.siit.tu.ac.th/help/iw)
