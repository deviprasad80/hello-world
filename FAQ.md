# FAQs

## How do we find out the onie version used by current SONIC? and what's the current version that's been used?

  In the Sonic device, enter **cat /host/machine.conf**  command.
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
## Does SONIC support CPU controlled fdb learning, Per port or per vlan basis?

Please note, Sonic supports hardware based fdb learning.  The events of learning/aging/move is notified by hardware/SAI to Orchagent. 

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




