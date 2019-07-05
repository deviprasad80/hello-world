There is already a FAQ document. We need to add these questions to that FAQ.

Shall we group the questions based on the module/protocol? And, find out how easy is it to provide a search in the FAQ document.



# FAQs

## Q:How to find the current onie version  version?

A: In the Sonic device, enter **cat /host/machine.conf**  command. This will give the current ONIE version along with other details such as vendor id, platform id, kernet version etc  
  - Example:
  ```
    admin@sonic:~$ cat /host/machine.conf
    onie_version=3.20.1.3
    onie_vendor_id=674
    onie_platform=x86_64-dell_s6000_s1220-r0
    onie_machine=dell_s6000_s1220
    onie_machine_rev=0
    onie_arch=x86_64
    onie_config_version=1
    onie_build_date="2015-05-03T16:20-0700"
    onie_partition_type=gpt
    onie_kernel_version=3.2.35
  ```

----------------------------------------------------------------------------------------------------------------------------
## Q:How many host entries does SONiC support?

A: SONiC currently supports around 2400 host entries

----------------------------------------------------------------------------------------------------------------------------
## Q:What is the maximum rate for ARP/ND?

A: Currently the max rate for ARP/ND is 600 packets. It will be increased it to higher number(8000) in CoPP file  to improve the learning time.

(https://github.com/Azure/SONiC/pull/399/commits/89abd4938d792215b75d801e87b47ccf2c22f111)

----------------------------------------------------------------------------------------------------------------------------
## How to print all the keys in a database?

redis-cli -n 4 keys "MGMT_INTERFACE*"
This will print all keys in the database number 4.
4 is the CONFIG_DB
6 is STATE_DB
0 is APP_DB

To get value for the particular key.
redis-cli -n 4 HGETALL "MGMT_INTERFACE|eth0|10.11.12.13/24"

----------------------------------------------------------------------------------------------------------------------------
1)	Can we add static routes in SONiC using config_db.json? Whats the syntax?
2)	Can we add static ARP in SONiC using config_db.json? Whats the syntax?
3)	Can we add static MAC  in SONiC using config_db.json? Whats the syntax?

1.	We don’t support it today. Static routes can be added via linux “ip route add..” but it will not be persistent after reboot
2.	Yes, we can. Please refer (https://github.com/Azure/sonic-swss/blob/master/doc/swss-schema.md#neigh_table-1) section. 
3.	We don’t support static MAC via config_db. However, there is a way to add fdb entries like this (https://github.com/Azure/SONiC/issues/249)  

6 is STATE_DB. You would want to check 0 (APP_DB).


----------------------------------------------------------------------------------------------------------------------------
## Where do the python bindings, to program the switch's control plane are copied?

Answer:

The python bindings to program the switch's control plane are copied in an empty directory "switch_sai_thrift"  

----------------------------------------------------------------------------------------------------------------------------
## How to program FDB static/dynamic entries into ASIC?
  You can program FDB entries into ASIC as static/dynamic. Please check this link [here](https://github.com/Azure/SONiC/issues/249). Give type as either "static" or "dynamic"
  
  - Example:
  ```
  Update the “OP”: settting:
  “SET” is used to add
  [
       {
          "FDB_TABLE:Vlan1000:00-00-00-10-20-30": {
                 "port": "Ethernet16",
                 "type": "static"
             },
             "OP": “SET“
        }
  ]


  “DEL” is used to remove the static entry:
  [
       {
          "FDB_TABLE:Vlan1000:00-00-00-10-20-30": {
                 "port": "Ethernet16",
                 "type": "static"
             },
             "OP": “DEL”
        }
  ]
 ```

1.Enter into swss container
$ docker exec -it swss bash

2.     edit fdb.json

[
     {
        "FDB_TABLE:Vlan1000:00-00-00-10-20-30": {
               "port": "Ethernet16",
               "type": "static"
           },
           "OP": “SET“  --> “DEL”: remove
      }
]


3.     Apply fdb.json
swssconfig ./fdb.json


a. the VLAN configuration should be correct and interface (Ethernet16 in this example) should be 'up'
 b. config should NOT be reload --> The static fdb configuration not be retained after reload
---------------------------------------------------------------------------------------------------------------------------- 
## How to know the interface naming mode?
  Enter the command **show interface naming_mode**. Initially it will be "default".
  - Example:
  ```
  admin@sonic:~$ show interface naming_mode
  default
  
  admin@sonic:~$ show ip interfaces
  Interface    IPv4 address/mask    Admin/Oper
  -----------  -------------------  ------------
  Ethernet0    10.0.0.0/31          up/down
  Ethernet4    10.0.0.2/31          up/down
  Ethernet8    10.0.0.4/31          up/down
  ...
  docker0      240.127.1.1/24       up/down
  eth0         10.11.150.249/16     up/up
  lo           127.0.0.1/8          up/up
               10.1.0.1/32

 ```

---------------------------------------------------------------------------------------------------------------------------- 
## How to change the interface naming mode
   Enter the command -   
   **sudo config interface_naming_mode alias**  `Non-root user`  
   **config interface_naming_mode alias**  `Root user`  
   
   Logout and login into the device for the change to take effect.
   
   - Example: 
  ```
  admin@sonic:~$ sudo config interface_naming_mode alias
  Please logout and log back in for changes take effect.
  admin@sonic:~$ logout
  
  admin@sonic:~$ show interface naming_mode
  alias
  admin@sonic:~$ show ip interfaces
  Interface       IPv4 address/mask    Admin/Oper
  --------------  -------------------  ------------
  fortyGigE0/0    10.0.0.0/31          up/down
  fortyGigE0/4    10.0.0.2/31          up/down
  fortyGigE0/8    10.0.0.4/31          up/down
  ...
  docker0         240.127.1.1/24       up/down
  eth0            10.11.150.249/16     up/up
  lo              127.0.0.1/8          up/up
                  10.1.0.1/32
  ```

**Note** : Notice the change in the interface names under the "Interface" coloumn in the above two examples.

----------------------------------------------------------------------------------------------------------------------------
## vlan configuration from the python cli results in a change in the redis database but not in the kernel level. Why?

***Observation***
Interfaces of a switch  stick to their startup configurations no matter what changes are made dynamically from the python cli. Vlanmgrd needs to be restarted for the kernel to know about the change.  
   


 [Link](https://groups.google.com/forum/#!searchin/sonicproject/Vlan$20config$20from$20python$20cli%7Csort:date/sonicproject/wG0ZKYylVEU/GivLlGa5AAAJ)
 
----------------------------------------------------------------------------------------------------------------------------
## Q:How does SONIC support CPU controlled fdb learning; Per port or per vlan basis?

A: Sonic supports hardware based fdb learning. The events of learning/aging/move is notified by hardware/SAI to Orchagent.  

If yes, then , 
- As SONIC registers for fdb events, a new address entry is written back to SAI as dynamic entries?  
  - Sonic registers for fdb events and anytime hardware learns a new entry, it is notified from SAI driver to Orchagent  
  
- Can it handle aging, and move also?  
  - As I mentioned, aging/move happens in hardware and Orchagent gets notified of those events. Orchagent currently handles aging events but not move. The entry gets deleted if we get a move event   

If not, then 
- The fdb notifications  are just used for updating the local DB? And updating arp cache?
  - FDB notifications are for updating local DB and also update observers (Mirroring) for any fdb event. It is not for updating arp cache. ARP is completely managed in kernel stack.  
- Then dynamic fdb entries can only be configured by user using json?
  - Yes, thats the current support.  
  
  [Link](https://groups.google.com/forum/#!topic/sonicproject/ufi2wEylSCE)
----------------------------------------------------------------------------------------------------------------------------
## What is the sequence of starting SONiC dockers and how do we add one step in startup? 

I saw systemd/system/ directory in vs vm, where in the platform directory that we can inject another service?


[Link](https://groups.google.com/forum/#!topic/sonicproject/YTzqmm8Wqlo)

----------------------------------------------------------------------------------------------------------------------------
## Not able to see 'telemetry binary' after building the Debian package

 I followed the steps and built a Debian package in ubuntu 16.04 server.
and copied sonic-telemetry_0.1_amd64.deb to sonic switch 
installed it using "dpkg -i sonic-telemetry_0.1_amd64.deb". However, I don't see telemetry binary. Please let me know if I miss anything here.

root@switch2:/home/admin#  ./telemetry --help
bash: ./telemetry: No such file or directory


git clone https://github.com/Azure/sonic-telemetry.git
pushd sonic-telemetry
dpkg-buildpackage -rfakeroot -b -us -uc
popd

telemetry runs on a docker. Please have a look at /usr/bin/telemetry.sh.
The easy way is to include telemetry in sonic image itself. You can do that by marking ENABLE_SYSTEM_TELEMETRY=y  on the "rules/config" file while building sonic image. Telemetry service gets started automatically.

root@sonic:/home/admin# ps -elf | grep tel
4 S admin     2401     1  0  80   0 -  2955 -      Nov05 ?        00:00:00 /bin/bash /usr/bin/telemetry.sh attach
0 S admin     2403  2401  0  80   0 - 45153 SyS_ep Nov05 ?        00:00:00 docker attach --no-stdin telemetry
4 S root      2761  2370  0  80   0 - 159110 -     Nov05 pts/5    00:29:09 /usr/sbin/telemetry -logtostderr --insecure --port 8080 --allow_no_client_auth -v=2

-------------------------------------------------------------------------------------------------------------------------------
Is there any document other than radv spec? This document has limited information regarding radvd integration in sonic.
Also as per docker script we found that radvd will be configured and started only when Sonic DEVICE_ROLE is set to "ToRRouter". 
What is the significance of DEVICE_ROLE and difference between ToRRouter & LeafRouter role.
Why is radvd configured and started only on "ToRRouter"?

1. The radv is the IPv6 ND protocol part feature, used by routers to advertise their router-role information to the shared-link devices. 
2. DEVICE_ROLE meaning the device(switch) roles in the DC network, ToRRouter meaning "Top of the Rack Router/Switcher", LeafRouter that is the middle device between ToRRouter and Spine;  The ToRs often directly connect to the Servers(host), so ToRs need the function radvd to spread the router-link informations. But LeafRouter is not directly connected to the servers(hosts), so one must not enable this function.



-------------------------------------------------------------------------------------------------------------------------------
Is there demo or example code about call sonic CLI inside docker?

such as call `show interfaces status` in sonic-telemetry docker.


-------------------------------------------------------------------------------------------------------------------------------
It's not easy to find any information about what would be the minimum hardware requirements to run SONIC ? I'm reading through architecture guide, and am wondering
about every component being ran in separate docker container. Isn't it hardware-intensive ? How much RAM/CPU power would the HW need to run SONIC smoothly ?
Do you have any hands-on experience with that ?




-------------------------------------------------------------------------------------------------------------------------------
I have couple of questions: 


1) Are there any instructions to compile sonic-buildimage/src/sonic-sairedis code separately for a new platform and what are the steps required ?

2) Also, what are the various files at the top level i need to change. 

3) description of the Dockerfile.j2 and the requirements for the same

3) Any description of the docker containers that are used for sai





-------------------------------------------------------------------------------------------------------------------------------
•	Is there any document other than radv spec? This document has limited information regarding radvd integration in sonic.
•	Also as per docker script we found that radvd will be configured and started only when Sonic DEVICE_ROLE is set to "ToRRouter". 
o	What is the significance of DEVICE_ROLE and difference between ToRRouter & LeafRouter role.
o	Why is radvd configured and started only on "ToRRouter"?


1. The radv is the IPv6 ND protocol part feature, used to routers advertise  theirs router-role information to the shared-link devices. 
2. DEVICE_ROLE meaning the device(switch) roles in the DC network, ToRRouter meaning "Top of the Rack Router/Switcher", LeafRouter that is the middle device 
   between ToRRouter and Spine;  The ToRs often directly connect to the Servers(host), so ToRs need the function radvd to spread the router-link informations. But LeafRouter 
   not directly connects the servers(hosts), so not must enable this function. 





-------------------------------------------------------------------------------------------------------------------------------
Could anyone explain how  bcmcmd communicates with ASIC.

From command line, it looks bcmcmd connects to sswsyncd socket.

    # USAGE: bcmcmd [-f <sun_path>] -v <cmd>
      -v                         verbose mode
      -f                         domain socket filename, default /var/run/sswsyncd/sswsyncd.socket

is syncd listening on this socket?

I'm trying to understand whether communication is like:
    bcmcmd <--> ASIC
or
    bcmcmd <--> syncd <--> ASIC



-------------------------------------------------------------------------------------------------------------------------------
My intention is to use a configuration with all ports part of VLAN1. I observed that the switch default VLAN (VLAN1) is not being exposed out to users. "show vlan brief" doesn't display an entry for VLAN1.

When I tried to create VLAN1, it threw an error message.

Nov 12 16:29:31.708140 sonic INFO kernel: [45185.022439] IPv6: ADDRCONF(NETDEV_UP): Vlan1: link is not ready
Nov 12 16:29:31.710270 sonic NOTICE swss#orchagent: :- addVlan: Create an empty VLAN Vlan1 vid:1
Nov 12 16:29:31.712193 sonic ERR syncd#syncd: brcm_sai_create_vlan:95 vlan create failed with error Entry exists (0xfffffff8).
Nov 12 16:29:31.712446 sonic ERR syncd#syncd: :- processEvent: attr: SAI_VLAN_ATTR_VLAN_ID: 1
Nov 12 16:29:31.712622 sonic ERR syncd#syncd: :- processEvent: failed to execute api: create, key: SAI_OBJECT_TYPE_VLAN:oid:0x2600000000061e, status: SAI_STATUS_ITEM_ALREADY_EXISTS

At this point "show vlan brief" does display VLAN1. But when I try to add a port to be member of the VLAN1, orchagent crashes due to below error.

Nov 11 23:23:12.309510 sonic ERR syncd#syncd: :- syncd_main: Runtime error: :- translate_vid_to_rid: unable to get RID for VID: 0x26000000000616  

Now, the questions I have to the SONiC community are
 - What is the expected role of the switch default VLAN1? 
 - Is the above observed behavior as per SONiC design or is it because of some missing pieces in the code?
 - Why does cpu port continue to participate in switch default VLAN1 when none of the other ports are being allowed to be part of it?


The observations I made are on a Broadcom platform.

Really appreciate some insight.






-------------------------------------------------------------------------------------------------------------------------------
## Question about IP set on VLAN/LAG interface which is not existed

1. To create a VLAN and set IP on the VLAN interface. We can see IP configuration in CONFIG_DB, APP_DB and Linux kernel.
2. To delete VLAN, interface entry still exist in CONFIG_DB and APP_DB and vlan netdev is removed from Linux kernel.
    root@sonic:/usr# ip address show Vlan2
    78: Vlan2@Bridge: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
        link/ether cc:37:ab:ec:de:9c brd ff:ff:ff:ff:ff:ff
        inet 192.168.0.1/27 scope global Vlan2
           valid_lft forever preferred_lft forever
        inet6 fe80::ce37:abff:feec:de9c/64 scope link
           valid_lft forever preferred_lft forever
    root@sonic:/usr# config vlan del 2
    root@sonic:/usr# ip address show Vlan2
    Device "Vlan2" does not exist.
    root@sonic:/usr# redis-cli
    127.0.0.1:6379> select 0
    OK
    127.0.0.1:6379> hgetall "INTF_TABLE:Vlan2:192.168.0.1/27"
    1) "scope"
    2) "global"
    3) "family"
    4) "IPv4"
    127.0.0.1:6379>
 
3. Re-create VLAN, we expect that IP configuration on VLAN interface shall set to linux kernel, but no IP address on the VLAN interface in Linux kernel.
    root@sonic:/usr# config vlan add 2
    root@sonic:/usr# ip address show Vlan2
    79: Vlan2@Bridge: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
        link/ether cc:37:ab:ec:de:9c brd ff:ff:ff:ff:ff:ff
        inet6 fe80::ce37:abff:feec:de9c/64 scope link
           valid_lft forever preferred_lft forever
    root@sonic:/usr# redis-cli
    127.0.0.1:6379> select 0
    OK
    127.0.0.1:6379> hgetall "INTF_TABLE:Vlan2:192.168.0.1/27"
    1) "scope"
    2) "global"
    3) "family"
    4) "IPv4"
    127.0.0.1:6379>
 
We think it is bug.
For current design, SONiC allow user to set IP on LAG/VLAN which is not created and the intfMgrd will re-try until LAG/VLAN is ready. 
Please see source code as follows. 
https://github.com/Azure/sonic-swss/blob/master/cfgmgr/intfmgr.cpp, start from line 193
    193          * Don't proceed if port/LAG/VLAN is not ready yet. 
    194          * The pending task will be checked periodically and retried. 
    195          */ 
    196         if (!isIntfStateOk(alias)) 
    197         { 
    198             SWSS_LOG_DEBUG("Interface is not ready, skipping %s", alias.c_str()); 
    199             return false; 
    200         } 
 
 
We think intfMgrd shall subscribe to VLAN_TABLE in STATE_DB.
When it received change of VLAN_TABLE in STATE_DB, shall update APP_DB.
And follow original design, enter retry stage if VLAN is deleted after creating. Right?
But no implement. Please advise us your suggestion. 

----------------------------------------------------------------------------------------------------------------------------------------

## How to configure ecmp in config_db.json? 

ECMP routes are learned via BGP. There is currently no static support for this via config_db

----------------------------------------------------------------------------------------------------------------------------------------
Currently we are using the T0 testbed scenario which is trying to install around 6400 IPv4 and 6400 IPv6 routes learnt from BGP Peers.
We wanted to know
- what is the maximum value of IPv4 and IPv6 routes that SONiC can install . 
- Does it depend on the maximum value of the CRM resources for IPv4 and IPv6 routes?
- What if the underlying hardware doesn't support that much IPv4 and IPv6 routes to get installed ?
How can we handle such scenario ?
Please comment on this and provide your suggestions ?



-----------------------------------------------------------------------------------------------------------------------------------------
I am trying to setting the MTU of VLAN interface. My configuration is shown as below.

    "VLAN": {
        "Vlan100": {
            "mtu": "1300"
        }
    },
    "VLAN_MEMBER": {
        "Vlan100|Ethernet8": {
            "tagging_mode": "untagged"
        }
    },
    "VLAN_INTERFACE": {
        "Vlan100|192.168.99.1/24": {}
    },

Then, I generate packet with the size more than the MTU, the packet was neither dropped nor fragmented by switch
Dose anyone know the correct behavior of this MTU setting?


ANSWER:

In general,To set mtu as following:
     
"PORT": {
        “Ethernet0”: {
                             "mtu" : "9100"
                            }
        ...............

-----------------------------------------------------------------------------------------------------------------------------------
1) Are there any instructions to compile sonic-buildimage/src/sonic-sairedis code separately for a new platform and what are the steps required?  
2) Also, what are the various files at the top level i need to change?  
3) description of the Dockerfile.j2 and the requirements for the same  
3) Any description of the docker containers that are used for sai  


ANSWER:  

sonic-sairedis are platform independent except vslib, I think.
For new platform, add/modify sonic-buildimage/platform and sonic-buildimage/device.
e.g. for broadcom platform, SAI library (libsaibcm package) is referenced in sonic-buildimage/platform/broadcom/sai.mk.
See also porting guide https://github.com/Azure/SONiC/wiki/Porting-Guide

------------------------------------------------------------------------------------------------------------------------------------
How to configure pvlan for a port?  

As per SAI header saiport.h, attribute SAI_PORT_ATTR_PORT_VLAN_ID can be used to configure port vlan id.
How to configure this attribute through SONiC?

SAI specification treats below two operations differently  
1. setting port vlan id  
2. adding a port as untagged memeber of vlan  

If the application intends to use a different untagged VLAN it can do so by setting the port attribute SAI_PORT_ATTR_PORT_VLAN_ID as well as adding the port as an untagged member of the particular VLAN  

From SONiC CLI or json configuration, I could not figure out how to do (1).
Is it so SONiC does both these operations always together?




-------------------------------------------------------------------------------------------------------------------------------------
Is there demo or example code about call sonic CLI inside docker?

such as call `show interfaces status` in sonic-telemetry docker.




-------------------------------------------------------------------------------------------------------------------------------------
It's not easy to find any information about what would be the minimum hardware requirements to run SONIC ? I'm reading through architecture guide, and am wondering
about every component being ran in separate docker container. Isn't it hardware-intensive ? How much RAM/CPU power would the HW need to run SONIC smoothly ?
Do you have any hands-on experience with that ?



SONiC currently only supports x86_64 CPU (https://github.com/Azure/SONiC/wiki/Supported-Devices-and-Platforms)
-------------------------------------------------------------------------------------------------------------------------------------
1) Are there any instructions to compile sonic-buildimage/src/sonic-sairedis code separately for a new platform and what are the steps required ?

2) Also, what are the various files at the top level i need to change. 

3) description of the Dockerfile.j2 and the requirements for the same

3) Any description of the docker containers that are used for sai

(https://groups.google.com/forum/#!searchin/sonicproject/sonic$20sai-redis$20question%7Csort:date/sonicproject/pf-WXvaTwT4/nqjI7HE6BAAJ)
A: sonic-sairedis are platform independent except vslib, I think.
For new platform, add/modify sonic-buildimage/platform and sonic-buildimage/device.
e.g. for broadcom platform, SAI library (libsaibcm package) is referenced in sonic-buildimage/platform/broadcom/sai.mk.
See also porting guide https://github.com/Azure/SONiC/wiki/Porting-Guide


---------------------------------------------------------------------------------------------------------------------------------------------------
Q: vlan configuration from the python cli results in a change in the redis database but not in the kernel level!!!
Interfaces of a switch  stick to their startup configurations no matter what changes are made dynamically from the python cli.
vlanmgrd needs to be restarted for the kernel to know about the change.
why is  this,please????

A:


---------------------------------------------------------------------------------------------------------------------------------------------------
Q: How to connect 2 sonic-vs containers via virtual Ethernet link?

A: 1)      Start two debian containers (sw0 and sw1) as below:

docker run -id --name sw0 debian bash

docker run -id --name sw1 debian bash

 

2)      Create virtual Ethernet link

      sudo ip link add sw0vEthernet0 type veth peer name sw1vEthernet0

 

3)      Map the link endpoints to network namespace of respective containers

      sudo ip link set sw0vEthernet0 netns sw0_pid

      sudo ip link set sw1vEthernet0 netns sw1_pid

 

Where sw0_pid and sw1_pid are PIDs of sw0 and sw1 containers

 

4)      Change names of the interfaces to vEthernet0 and bring them up

nsenter -t sw0_pid -n ip link set dev sw0vEthernet0 name vEthernet0

nsenter -t sw0_pid -n ip link set dev vEthernet0 up

 

nsenter -t sw1_pid -n ip link set dev sw1vEthernet0 name vEthernet0

nsenter -t sw1_pid -n ip link set dev vEthernet0 up

 

5)      Start two sonic-vs containers (vs0 and vs1)

$ docker run --privileged --network container:sw0 --name vs0 -d docker-sonic-vs

docker run --privileged --network container:sw1 --name vs1 -d docker-sonic-vs

 

6)      Configure Ethernet0 interface in each sonic-vs container to 10.0.0.0/31 (in vs0) and 10.0.0.1/31 (in vs1)

$ docker exec -it vs0 bash

# ifconfig Ethernet0 10.0.0.0/31 up

 

$ docker exec -it vs1 bash

# ifconfig Ethernet0 10.0.0.1/31 up

 

After running the above sequence I can ping 10.0.0.1 address from vs0 and 10.0.0.0 from vs1 container.

 

However when I run “show interfaces status” command in each sonic-vs container, I get the below output indicating that operating status of Ethernet0 port is down.

 

Interface            Lanes    Speed    MTU           Alias    Oper    Admin

-----------  ---------------  -------  -----  --------------  ------  -------

  Ethernet0      29,30,31,32      N/A    N/A    fortyGigE0/0    down      N/A
  Ethernet4      25,26,27,28      N/A    N/A    fortyGigE0/4    down      N/A
  Ethernet8      37,38,39,40      N/A    N/A    fortyGigE0/8    down      N/A


The problem is visible in both sonic-vs containers.



Q: What might a reason for the above problem?

A: able to fix the problem by bringing up vEthernet0 interface using ifconfig

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
switch default VLAN 1 behavior

My intention is to use a configuration with all ports part of VLAN1. I observed that the switch default VLAN (VLAN1) is not being exposed out to users. "show vlan brief" doesn't display an entry for VLAN1.

When I tried to create VLAN1, it threw an error message.

Nov 12 16:29:31.708140 sonic INFO kernel: [45185.022439] IPv6: ADDRCONF(NETDEV_UP): Vlan1: link is not ready
Nov 12 16:29:31.710270 sonic NOTICE swss#orchagent: :- addVlan: Create an empty VLAN Vlan1 vid:1
Nov 12 16:29:31.712193 sonic ERR syncd#syncd: brcm_sai_create_vlan:95 vlan create failed with error Entry exists (0xfffffff8).
Nov 12 16:29:31.712446 sonic ERR syncd#syncd: :- processEvent: attr: SAI_VLAN_ATTR_VLAN_ID: 1
Nov 12 16:29:31.712622 sonic ERR syncd#syncd: :- processEvent: failed to execute api: create, key: SAI_OBJECT_TYPE_VLAN:oid:0x2600000000061e, status: SAI_STATUS_ITEM_ALREADY_EXISTS

At this point "show vlan brief" does display VLAN1. But when I try to add a port to be member of the VLAN1, orchagent crashes due to below error.

Nov 11 23:23:12.309510 sonic ERR syncd#syncd: :- syncd_main: Runtime error: :- translate_vid_to_rid: unable to get RID for VID: 0x26000000000616  

Now, the questions I have to the SONiC community are
 - What is the expected role of the switch default VLAN1? 
 - Is the above observed behavior as per SONiC design or is it because of some missing pieces in the code?
 - Why does cpu port continue to participate in switch default VLAN1 when none of the other ports are being allowed to be part of it?


The observations I made are on a Broadcom platform.




---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
TABLE_SIZE attribute in switch expected value?

Q: What are the get attributes for switch in SAI for table size?  

A: Following are the get attributes for switch in SAI for table size.
   ```
      SAI_SWITCH_ATTR_FDB_TABLE_SIZE  
      SAI_SWITCH_ATTR_L3_NEIGHBOR_TABLE_SIZE  
      SAI_SWITCH_ATTR_L3_ROUTE_TABLE_SIZE  
   ```   
------------------------------------------------------------------------------------------------------
Q: How to connect to routing daemons like ospf/bgp in sonic docker environment for enabling daemon debugs to debug a problem?  
   
A: 

------------------------------------------------------------------------------------------------------------------------------
Q: After I boot up the SONiC switch, it always has default IP configured on each port.
   How could I build an image without this default IP address configured on each port?

A: During the initial install and boot process, Sonic creates the IPs for each interface and stores this in the config_db.json file (in /etc/sonic).  You can delete those interfaces or replace them in the file and then reapply the configuration.  Also replacing the config_db.json can be part of your deployment strategy.

(https://groups.google.com/forum/#!topic/sonicproject/fKKDyunc6h8)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Q: Which daemon writes to APP_DB?

A: "fpmsyncd" writes to APP_DB

----------------------------------------------------
Q: Where does the configuration from "teamd" has been moved to?

A: All the configuration from "/etc/teamd config file" has been moved to "teammgrd"

(https://groups.google.com/forum/#!topic/sonicproject/Rt0-ZVovB9U)

-------------------------------------------------------------------------------------
Q: How does VRF configures in Linux kernel?  

A: As of now, though there is a CLI wrapper, SONiC ultimately uses the linux NetLink calls

-------------------------------------------------------------------------------------------
Q: Can we safely assume VRF design supports later versions of Linux Kernel 4.9?  
A: Yes

-------------------------------------------------------------------------------------------
Q: How does SONiC support link aggregation?


A: SONiC supports link aggregation with teamd container.  
&nbsp; SONiC is using [libteam] (http://libteam.org/) as the LACP implementation.  
&nbsp; The configuration is stored in the configuration database. Please refer to the [configuration] (https://github.com/Azure/SONiC/wiki/Configuration) samples here to configure the port-channels.

-------------------------------------------------------------------------------------------
Q: What is the main task of a daemon?

A: The main task of a daemon is to post device data to DB. Currently to fetch switch peripheral devices related data SONiC will directly access hardware through platform pluggins

Q: What are the different types of daemons?

A: PMON container has 

```
   xcvrd for transceivers/SFPs 
   ledd  for front panel LEDs  
   psud  for PSUs

```

Q: Does FAN unit has a daemon?

A: Currently there is no daemon for FAN unit  

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
frr.conf is created by template file, and folder is created at /etc/sonic/frr/ when i check the mount in bgp docker container I see the same files as expected.
But when I access the FRR daemon with vtysh i find it empty of config


ANSWER:

Need to use vtysh -b first. Then vtysh  


There are currently 3 modes of managing FRR configurations in SONiC today, they are controlled by the following attribute in config_db:
{
    "DEVICE_METADATA": {
        "localhost": {
            "docker_routing_config_mode": "xxxxxx",
        }
    },

FRR itself has 2 modes of config management:
   - integrated: the configurations of all daemons is maintained in frr.conf
   - non-integrated: the configurations of each daemon is maintained in a specific <name>.conf file ( bgpd.conf, ospfd.conf etc..) and the "generic" config in zebra.conf
this behavior is controlled by the presence of "service integrated-vtysh-config" in vtysh.conf

for SONiC you can control this with:
if docker_routing_config_mode is not present then FRR will function in non-integrated mode and bgpd.conf and zebra.conf files will be generated with a template and contents from config_db
if docker_routing_config_mode is set to "separated" then FRR will function in non-integrated mode and bgpd.conf and zebra.conf files will be generated with a template and contents from config_db
if docker_routing_config_mode is set to "unified" then FRR will function in integrated mode and a frr.conf file will be generated with a template and contents from config_db
if docker_routing_config_mode is set to anything else (for eg. "split" or even "Mickey Mouse" as Nikos would put it ) then FRR functions in non-integrated mode and the config is not generated, its up to you to upload a /etc/sonic/frr/bgpd.conf on the host  

before in non-integrated or non-managed mode the frr.conf file was not removed hence the behavior where the empty frr.conf file was used. Since commit f40795a it should work as advertised  

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Which of Mellanox's switches are compatible with DX10's?

ANSWER:

I have a pair of DX10's that don't play nice with some of my NIC's - in particular Mellanox Connectx-4's.
They work fine when forced to 40 GbE, but won't get link with 100GbE. I have worked through a couple of things with Mellanox (cables [DAC and Optical]) but no success. At this point Mellanox stopped investigation since the DX10's are not on the compatibility list of the CX4's

I have also reached out to Celestica but they apparently don't do hardware validation at all.


Interestingly I have some Mellanox CX5's and those work without issue (with the same cables). 

However, I am looking for two things -

1. Feedback if anyone is successfully using CX4's with a DX10 or another Broadcom based switch
2. Feedback on other NICs that are known to be compatible with  DX10's (or other Broadcom based switches) - basically looking for an alternative to the CX5'

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
When I cloned and tried to compile for Broadcom platform, I am getting foll= owing error in both master and 201904 branch.

Is the following error seen by everyone? Is anyone working to resolve this?=  Not sure how Jenkins is able to get a clean build.

Any help to resolve this build issue is highly appreciated.

 

dpkg-buildpackage: info: host architecture amd64

dpkg-checkbuilddeps: error: Unmet build dependencies: libcmocka-dev python3= -all-dev python3-all-dbg

dpkg-buildpackage: warning: build dependencies/conflicts unsatisfied; abort= ing

dpkg-buildpackage: warning: (Use -d flag to override.)

Makefile:9: recipe for target '/sonic/target/debs/stretch/libyang0.16_0.16.=

105-1_amd64.deb' failed

make[1]: *** [/sonic/target/debs/stretch/libyang0.16_0.16.105-1_amd64.deb] = Error 3

make[1]: Leaving directory '/sonic/src/libyang'

Thanks & Regards,

Kannan.KVS






------------------------------------------------------------------------------------------------------------------------------------
I would like to know if there is an architectural guideline to avoid kernel programming from the orchagent ?

  I see that mostly kernel programming happens from the manager components. 

  The Arch document talks of kernel programming from syncd also. 

  https://github.com/Azure/SONiC/wiki/Architecture (port-state interactions) 
  
  


-------------------------------------------------------------------------------------------------------------------------------------   
Q: What package does SONiC use?

A: SONiC uses the "click Python package" in order to expose the available APIs.

-------------------------------------------------------------------------------------------------------------------------------------
Q: Why does an IPv6 address is assigned by default when an L2 VLAN is created?

A: SONiC supports IPv6 forwarding and a link local address is added by the Kernel. When L2 VLAN is created that IP address is assigned to it by default.  
   If required it can be disabled by adding a line in /etc/sysctl.conf  on switch.
   
   ```
     # Uncomment the next line to enable packet forwarding for IPv6
	 #  Enabling this option disables Stateless Address Autoconfiguration
	 #  based on Router Advertisements for this host
	 #net.ipv6.conf.all.forwarding=1
	
	 net.ipv6.conf.all.disable_ipv6=1  <<<<<<<<<<<
   ```
--------------------------------------------------------------------------------------------------------------------------------------

   
 















