# Node-Red flow for SMA devices

This project is for owners of the new generation SMA devices (PV inverters and Home Managers), who want to control or read out values on their Node-Red based home automation system. The Node-Red server runs typically on a server which is placed in the local network, to which the SMA devices need also be connected. Most Node-Red nodes for SMA devices found in Internet are outdated and support only older devices, but not the newest generation of SMA devices. These circumstances let me develop a Node-Red flow by myself to support those new SMA devices. Following SMA devices are supported:

* SMA Energy Meter (EMETER-20)
* SMA Sunny Home Manager 2.0 (HM-2.0)
* SMA Sunny Tripower 5.0 / 6.0 / 8.0 / 10.0 Smart Energy (STPx0-3SE)

My goal was to develop a Node-Red flow consisting only of basic nodes, like udp- and tcp-nodes (which are part of the basic Node-Red installation) to have the largest flexibility.

# Overview of the SMA Energy Meter/Home Manager flow

![Home Manager flow](/SMA_SunnyHomeManager2.0.png)

The SMA Home Manager sends per default every 1 second automatically a UDP multicast message to target address 239.12.255.254 and port 9522. Whoever client registers for listening to this multicast address can receive this message. The messages with a payload of exactly 608 bytes we are interested in. The UDP node needs to be configured accordingly as shown in the following picture.

![UDP multicast](/udp.png)
