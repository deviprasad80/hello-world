# Troubleshooting Guide
Joe LeVeque edited this page Apr 6, 2018 

## Investigating Packet Drops

Packet drops can be investigated by viewing counters using the
`show interfaces counters` command.

-   **RX\_ERR/TX\_ERR** includes all physical layer (layer-2) related
    drops, such as FCS error, RUNT frames. If there is RX\_ERR or
    TX\_ERR, it usually indicates some physical layer link issues.

-   **RX\_DRP** include all layer-2, layer-3, ACL related drops in the
    switch ingress pipeline, drops due to insufficient ingress buffer.

-   **TX\_DRP** include mainly the egress buffer related drop due to
    congestion, including WRED drop.

-   **RX\_OVR/TX\_OVR** counts the oversized packets.

-   Example:

        admin@sonic:~$ show interfaces counters
              Iface            RX_OK      RX_RATE    RX_UTIL    RX_ERR    RX_DRP    RX_OVR            TX_OK      TX_RATE    TX_UTIL    TX_ERR    TX_DRP    TX_OVR
        -----------  ---------------  -----------  ---------  --------  --------  --------  ---------------  -----------  ---------  --------  --------  --------
          Ethernet0  471,729,839,997  653.87 MB/s     12.77%         0    18,682         0  409,682,385,925  556.84 MB/s     10.88%         0         0         0
          Ethernet4  453,838,006,636  632.97 MB/s     12.36%         0     1,636         0  388,299,875,056  529.34 MB/s     10.34%         0         0         0
          Ethernet8  549,034,764,539  761.15 MB/s     14.87%         0    18,274         0  457,603,227,659  615.20 MB/s     12.02%         0         0         0
         Ethernet12  458,052,204,029  636.84 MB/s     12.44%         0    17,614         0  388,341,776,615  527.37 MB/s     10.30%         0         0         0
         Ethernet16   16,679,692,972   13.83 MB/s      0.27%         0    17,605         0   18,206,586,265   17.51 MB/s      0.34%         0         0         0
         Ethernet20   47,983,339,172   35.89 MB/s      0.70%         0     2,174         0   58,986,354,359   51.83 MB/s      1.01%         0         0         0
         Ethernet24   33,543,533,441   36.59 MB/s      0.71%         0     1,613         0   43,066,076,370   49.92 MB/s      0.97%         0         0         0

## Physical Link Signal

Use the following command to get optical signal strength. Note: not all
types of links have such channel monitor values. The AOC and DAC cables
do not have such values.

Generally, optical power should be greater than -10dBm.

-   Example:

        admin@sonic:~$ show interfaces transceiver eeprom Ethernet12 --dom
        Ethernet12: SFP detected

                Connector : Unknown
                EncodingCodes : Unspecified
                ExtIdentOfTypeOfTransceiver : GBIC def not specified
                LengthOM3(UnitsOf10m) : 144
                RateIdentifier : Unspecified
                ReceivedPowerMeasurementType : Avg power
                TransceiverCodes :
                        10GEthernetComplianceCode : 10G Base-SR
                        InfinibandComplianceCode : 1X Copper Passive
                TypeOfTransceiver : QSFP
                VendorDataCode(YYYY-MM-DD Lot) : 2013-11-29
                VendorName : MOLEX
                VendorOUI : MOL
                VendorPN : 1064141400
                VendorRev : E th
                VendorSN : G13474P0120
                ChannelMonitorValues :
                        RX1Power : -5.7398dBm
                        RX2Power : -4.6055dBm
                        RX3Power : -5.0252dBm
                        RX4Power : -12.5414dBm
                        TX1Bias : 19.1600mA
                        TX2Bias : 19.1600mA
                        TX3Bias : 19.1600mA
                        TX4Bias : 19.1600mA
                ChannelStatus :
                        Rx1LOS : Off
                        Rx2LOS : Off
                        Rx3LOS : Off
                        Rx4LOS : Off
                        Tx1Fault : Off
                        Tx1LOS : Off
                        Tx2Fault : Off
                        Tx2LOS : Off
                        Tx3Fault : Off
                        Tx3LOS : Off
                        Tx4Fault : Off
                        Tx4LOS : Off
                ModuleMonitorValues :
                        Temperature : 23.7500C
                        Vcc : 3.2805Volts
                StatusIndicators :
                        DataNotReady : Off

## SONiC Tech-Support Dump

Generate a dump of troubleshooting data. This is similar to a \"show
tac\" command on other devices. An archive file containing the dump will
be saved to the device which can be sent to the SONiC development team
for troubleshooting.

-   Example:

        admin@sonic:~$ show techsupport

## Isolate SONiC Device from the Network

When there is suspicion that a SONiC device is dropping traffic and
behaving abnormally, you may want to isolate the device from the
network. Before isolating the device, please generate SONiC tech-support
first.

You can shut down BGP sessions to neighbors using a form of the
`config bgp shutdown` command. There are a few variations of this
command, examples follow.

-   Shutdown BGP session with neighbor by neighbor\'s hostname:

-   Example:

        admin@sonic:~$ sudo config bgp shutdown neighbor SONIC02SPINE

-   Shutdown BGP session with neighbor by neighbor\'s IP address:

-   Example:

        admin@sonic:~$ sudo config bgp shutdown neighbor 192.168.1.124

-   Shutdown BGP sessions with all neighbors:

-   Example:

        admin@sonic:~$ sudo config bgp shutdown all

## Config load vlan configure failed



## Unable to configure Management interface IP

- Example:
  ```
	admin@sonic:~$ /sbin/ifconfig eth0 10.11.12.13/24
	SIOCSIFADDR: Operation not permitted
	SIOCSIFFLAGS: Operation not permitted
	SIOCSIFNETMASK: Operation not permitted
  ```

Enter the command ***/sbin/ifconfig eth0*** to know the current management interface status for the interface eth0  

## Unable to get output for show arp

- Example:
  ```
  admin@sonic:~$ show arp -if Ethernet0
  Address    MacAddress    Iface    Vlan
  ---------  ------------  -------  ------
  Total number of entries 0 
  ```
  
Check the status of the interface using ***show ip interfaces***. Only when the interface is connected to a node and is up, only then an entry to the arp table will be added  

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Unable to reinstall Static Route after pull out and push in the link 

```
 {
  "VLAN": {
  "Vlan100": {
  "vlanid": "100"
  }
 },
  "VLAN_MEMBER": {
  "Vlan100|Ethernet32": {
  "tagging_mode": "untagged"
  }
 },
  "VLAN_INTERFACE": {
  "Vlan100|32.1.1.1/24": {},
  "Vlan100|32.2.1.1/24": {}
  },
  "INTERFACE": {
  "Ethernet33|33.1.1.1/24": {},
  "Ethernet33|33.2.1.1/24": {}
  },
  "LOOPBACK_INTERFACE": {
  "Loopback10|189.1.1.1/32": {}
  }
  }
  [topo]
  tc1(32.1.1.2/24)----(Ethernet32 Vlan100 32.1.1.1/24)DUT----(33.1.1.2/24)tc2
  [operation & rslt]  
  
```
1.Load the above configuration  
2.F`rom DUT ping tc2 33.1.1.2 successfull  
3.Add static route by command `sudo ip route add 40.1.1.0/24 nexthop via 33.1.1.2`  
4.tc1 send ip packets to 40.1.1.2,tc2 receive all the packets from tc1  
5.Pull out the line between dut and tc2.Kernel has the route item 40.1.1.0/24 and hardware del the 40.1.1.0/24  

```	
   admin@tau1t8-01:$ ip route  
   default via 10.251.0.1 dev eth0 proto zebra  
   10.0.0.56/31 dev PortChannel0001 proto kernel scope link src 10.0.0.56  
  10.0.0.58/31 dev PortChannel0002 proto kernel scope link src 10.0.0.58  
  10.0.0.60/31 dev PortChannel0003 proto kernel scope link src 10.0.0.60  
  10.0.0.62/31 dev PortChannel0004 proto kernel scope link src 10.0.0.62  
  10.251.0.0/24 dev eth0 proto kernel scope link src 10.251.0.189  
  32.1.1.0/24 dev Vlan100 proto kernel scope link src 32.1.1.1  
  32.2.1.0/24 dev Vlan100 proto kernel scope link src 32.2.1.1  
  33.1.1.0/24 dev Ethernet33 proto kernel scope link src 33.1.1.1  
  33.2.1.0/24 dev Ethernet33 proto kernel scope link src 33.2.1.1  
  40.1.1.0/24 via 33.1.1.2 dev Ethernet33  
  50.1.1.0/24 via 32.1.1.2 dev Vlan100  
  240.127.1.0/24 dev docker0 proto kernel scope link src 240.127.1.1  
  
  admin@tau1t8-01:$ show arp  
  Command: /usr/sbin/arp -n  
  Address HWtype HWaddress Flags Mask Iface  
  10.251.0.13 ether 00:06:06:03:ad:bc C eth0  
  33.1.1.2 ether 00:1b:21:ba:f3:bd C Ethernet33  
  32.1.1.2 ether 00:1b:21:ba:f3:bb C Vlan100  
  10.251.0.246 ether 00:0c:29:55:e2:ee C eth0  
  10.251.0.14 ether 00:03:0f:60:ae:82 C eth0  
  10.251.0.1 ether 00:03:0f:81:12:67 C eth0  

  admin@tau1t8-01:~$ redis-cli -n 0 keys ROUTE  
1.	"ROUTE_TABLE:189.1.1.1"  
2.	"ROUTE_TABLE:127.0.0.0/8"  
3.	"ROUTE_TABLE:32.1.1.0/24"  
4.	"ROUTE_TABLE:10.251.0.0/24"  
5.	"ROUTE_TABLE:32.2.1.0/24"  
6.	"ROUTE_TABLE:0.0.0.0/0"  
7.	"ROUTE_TABLE:240.127.1.0/24"  
8.	"ROUTE_TABLE:fe80::/64"  
9.	"ROUTE_TABLE:::1"  
10.	"ROUTE_TABLE:fc00:2::/64"  
11.	"ROUTE_TABLE:50.1.1.0/24"  

  admin@tau1t8-01:$ redis-cli -n 1 keys ROUTE40.1.1.0/24*  
  (empty list or set)  
  
  Push in the line again,the Ethernet33 change to UP.But the route 40.1.1.0/24 not reinstall to hardware but kernel still has the route item  
admin@tau1t8-01:$ ip route show 40.1.1.0/24  
40.1.1.0/24 via 33.1.1.2 dev Ethernet33  
admin@tau1t8-01:$ show arp 33.1.1.2  
Command: /usr/sbin/arp -n 33.1.1.2  
Address HWtype HWaddress Flags Mask Iface  
33.1.1.2 ether 00:1b:21:ba:f3:bd C Ethernet33  
admin@tau1t8-01:$ redis-cli -n 0 keys ROUTE  
12.	"ROUTE_TABLE:189.1.1.1"  
13.	"ROUTE_TABLE:127.0.0.0/8"  
14.	"ROUTE_TABLE:32.1.1.0/24"  
15.	"ROUTE_TABLE:10.251.0.0/24"  
16.	"ROUTE_TABLE:32.2.1.0/24"  
17.	"ROUTE_TABLE:0.0.0.0/0"  
18.	"ROUTE_TABLE:240.127.1.0/24"  
19.	"ROUTE_TABLE:fe80::/64"  
20.	"ROUTE_TABLE:::1"  
21.	"ROUTE_TABLE:fc00:2::/64"  
22.	"ROUTE_TABLE:50.1.1.0/24"  
admin@tau1t8-01:~$ redis-cli -n 1 keys ROUTE40.1.1.0/24*  
(empty list or set)  
（Note: This problem does not exist when static routing is added with zebra command）  


