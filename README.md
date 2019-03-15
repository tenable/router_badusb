# BadUSB in Routers
Material found in this repository was originally presented at [BSides Dublin](https://www.bsidesdub.ie/) on March 23, 2019. The slides are included here in pdf format.

This repository contains configuration files for [P4wnP1](https://github.com/mame82/P4wnP1), a BadUSB framework for the Raspberry Pi. The configuration files allow an attacker to execute BadUSB style attacks on certain routers.

The following hardware and software were used for the BadUSB atacks:

* Raspberry Pi Zero
* USB-A Addon
* 8 GB microSD
* Raspbian Stretch (Version: November 2018)
* P4wnP1 (Version: [9c8cc09a6503f10309c04310c3bba9c07caab8b7](https://github.com/mame82/P4wnP1/tree/9c8cc09a6503f10309c04310c3bba9c07caab8b7))

<img src="/images/pi.jpg" height="25%" width="25%" />

---

## mikrotik_mitm

<img src="/images/mikrotik_pi.jpg" height="50%" width="50%" />

The mikrotik_mitm directory contains configuration files to man-in-the-middle outbound traffic from RouterOS LAN hosts. The configuration files were tested using RouterOS on an hAP with default configurations. Presumably, it works on any RouterOS based router that supports 4g USB functionality. The attack will cause all internet bound traffic to be routed to the Raspberry Pi plugged into the USB port. The Pi will forward all of the internet traffic to a remote VPN server.

[![PoC Video](http://img.youtube.com/vi/3X7xrgan5Tk/0.jpg)](http://www.youtube.com/watch?v=3X7xrgan5Tk)

As written the "remote" VPN server is at 192.168.1.64. If you are going to try this out for yourself, you'll need to adjust the openvpn connection and possibly the iptables / dhcp options depending on where your VPN server is.

<img src="/images/mitm_diagram.png" height="75%" width="75%" />

As mentioned, RouterOS will recognize the USB device using the default configuration. However! For some reason, the router won't recognize the Pi until you first plug in something else. You only have to do it once and then you are good until the router reboots. I'm not sure of the root cause of this. I've been using a little ethernet adapter.

<img src="/images/usb_enet_adapter.jpg" height="50%" width="50%" />

---

## mikrotik_network_only

This is a non-mitm version of the MikroTik attack. The Pi will be assigned 192.168.4.1 and it should have access to both the WAN and LAN. LAN devices should also be able to reach the Pi. This is kind of useful if you just want to plug in your Pi as some type of local server... or if you want a reverse shell out to the internet.

---

## asus_bsides_routing_table

<img src="/images/asus_pi.jpg" height="50%" width="50%" />

The asus_bsides_routing_table directory contains configuration files to hijack traffic bound for http://securitybsides.com. The attack relies on the ability of the USB WAN to insert arbitrary entries into the router's routing table via DHCP options.

This attack was tested against an Asus RT-AC51U with load balancing dual WAN configured.

[![PoC Video](http://img.youtube.com/vi/LvWo8fUaJdo/0.jpg)](http://www.youtube.com/watch?v=LvWo8fUaJdo)

---

## Traditional Attacks over IP

<img src="/images/netgear_pi.jpg" height="50%" width="50%" />

A variety of routers support printer sharing (Netgear, Linksys, and TP-Link). The way this works is that you plug your USB printer into the router and then you install client software on your LAN host which gives it the ability to talk to the printer via the router.

I've found that the router/client software will actually communicate with pretty much any USB device. For whatever reason, the device makers didn't limit the functionality to printers only.

As such, an attacker can execute any of the normal payloads that come with P4wnP1. In my PoC video, I'm using hid_keyboard2.txt. The downside to this attack is that it requires special software be installed and that the user actually click "connect".

[![PoC Video](http://img.youtube.com/vi/aoaB6hiHGiM/0.jpg)](http://www.youtube.com/watch?v=aoaB6hiHGiM)
