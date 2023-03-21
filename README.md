# Node-Red flow for SMA devices

This project is for owners of the new generation SMA devices (Photovoltaic inverters and Home Managers), who want to control or read out values on their Node-Red based home automation system. The Node-Red server runs typically on a server which is placed in the local network, to which the SMA devices need also be connected. Most Node-Red nodes for SMA devices found in Internet are outdated and support only older devices, but not the newest generation of SMA devices. These circumstances let me develop a [SMA Node-Red flow](/flow_sma.json) Node-Red flow by myself to support those new SMA devices. Following SMA devices are supported:

* SMA Energy Meter (EMETER-20)
* SMA Sunny Home Manager 2.0 (HM-2.0)
* SMA Sunny Tripower 5.0 / 6.0 / 8.0 / 10.0 Smart Energy (STPx0-3SE)

My goal was to develop a Node-Red flow consisting only of basic nodes, like udp- and tcp-nodes (which are part of the basic Node-Red installation) to have the largest flexibility.

## Overview of the SMA Energy Meter/Home Manager flow

![SMA Home Manager flow](/SMA_SunnyHomeManager2.0.png)

The SMA Home Manager sends per default every 1 second automatically a UDP multicast message to target address 239.12.255.254 and port 9522. Whoever client registers for listening to this multicast address can receive this message. The messages with a payload of exactly 608 bytes we are interested in. The UDP node needs to be configured accordingly as shown in the following picture.

![UDP multicast](/udp.png)

## Overview of the SMA PV-inverter flow

![SMA Tripower PV-inverter](/SMA_SunnyTripowerX.0SE.png)

My first approch has been using the Modbus TCP server of the SMA inverter. It turned out that this server is running very instable and needs from time to time a restart because of hang-up. Unfortunately the problem resolved only by a complete restart of the inverter every few days - impractical. The other solution has been using the Webconnect server of the device, which acts as a normal webserver. Over this Webconnect API all parameters including the parameters of the SMA Home Manager can be queried.


For the first change node 2 rules (msg.headers and msg.payload) need to be defined as shown in the following picture. The string in the 2nd rule contains the user and password for the SMA inverter.

![First change node](/first_change.png)

The 2 strings from the previous node serve as input for the HTTP login request node. It needs to be configured for POST method with the IP-address of the SMA inverter. Set the options of the node as shown in the following picture. The response of the HTTP login request is a session ID number (SID).

![HTTP login request](/http_login_request.png)

The session ID number (SID) of the response is added to the message payload by the session ID function node. The message is the fed into the second change node. In this change node the SMA keys of the interesting PV-inverter values are configured, which is shown in the following picture. Some examples for SMA keys are:

* Supplied power [W]: 6100_40463600
* Absorbed power [W]: 6100_40463700
* PV generation power [W]: 6100_0046C200
* Present battery charge power [W]: 6100_00496900
* Present battery discharge power [W]: 6100_00496A00
* State of battery charge [%]: 6100_00295A00
* Total yield energy [Wh]: 6400_00462400
* Absorbed energy [Wh]: 6400_00462500

![Second change node](/second_change.png)

The 2 strings from the previous node serve as input for the HTTP getValues request node. It needs to be configured for POST method with the IP-address of the SMA inverter. Set the options of the node as shown in the following picture. The session ID number (SID) is a parameter for this HTTP request. The response of the HTTP getValues request is containing all requested values from the SMA inverter. The values are extracted from the response message by a following function node before displaying.

![HTTP getValues request](/http_getvalues_request.png)

At the end the actual session needs to be closed again. The 2 strings from the previous change node serve as input for the HTTP logout request node. It needs to be configured for POST method with the IP-address of the SMA inverter. Set the options of the node as shown in the following picture. The session ID number (SID) is a parameter for this HTTP request.

![HTTP logout request](/http_logout_request.png)
