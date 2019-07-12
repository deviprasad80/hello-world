There is already a FAQ document. We need to add these questions to that FAQ.

Shall we group the questions based on the module/protocol? And, find out how easy is it to provide a search in the FAQ document.


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
Currently we are using the T0 testbed scenario which is trying to install around 6400 IPv4 and 6400 IPv6 routes learnt from BGP Peers.
We wanted to know
- what is the maximum value of IPv4 and IPv6 routes that SONiC can install . 
- Does it depend on the maximum value of the CRM resources for IPv4 and IPv6 routes?
- What if the underlying hardware doesn't support that much IPv4 and IPv6 routes to get installed ?
How can we handle such scenario ?
Please comment on this and provide your suggestions ?

-----------------------------------------------------------------------------------------------------------------------------------------
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
Q: How to connect to routing daemons like ospf/bgp in sonic docker environment for enabling daemon debugs to debug a problem?  
   
A: 

------------------------------------------------------------------------------------------------------------------------------
Q: After I boot up the SONiC switch, it always has default IP configured on each port.
   How could I build an image without this default IP address configured on each port?

A: During the initial install and boot process, Sonic creates the IPs for each interface and stores this in the config_db.json file (in /etc/sonic).  You can delete those interfaces or replace them in the file and then reapply the configuration.  Also replacing the config_db.json can be part of your deployment strategy.

(https://groups.google.com/forum/#!topic/sonicproject/fKKDyunc6h8)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Q: WHERE DOES THE CONFIGURATION FROM "TEAMD" HAS BEEN MOVED TO?
A: ALL THE CONFIGURATION FROM "/ETC/TEAMD CONFIG FILE" HAS BEEN MOVED TO "TEAMMGRD"
(HTTPS://GROUPS.GOOGLE.COM/FORUM/#!TOPIC/SONICPROJECT/RT0-ZVOVB9U)

-------------------------------------------------------------------------------------
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
## ##Q: 1. What different with pytest ?

**A**: pytest more like acceptation test test by Python. Do it when code is done.
Unit test is do in development phase. Do it when code is not ready. In this phase we will spend more time to check the local variable / pointer in different stack frames are correct or NOT.
Also, check does have heap memory is leaked or overrun issue ...

The second different is pytest doesn't call orchagent API just operate Redis, unit test will call C++ function directly.
We can do more internal function / object operation more quickly.

Without no need to run two orchagent, syncD deamon and Redis server for each code modification.
So, can find bug and bug fix quickly more then pytest.

## Q: Why using libvs for unit test ?

**A**: We all need a mock data (or call virtual switch) in test phase (that using to save time and find bug quickly as possible)
We can using self mock data for core component test. That have less operation via SAI.
But in test for Orch class we want to test the path from CONFIG_DB to SAI is work or NOT. AND without fake dependencies (SAI or other orch). Closer to real case.
So, we using libvs that already simulate the chip behavior.

Use it can get closer behaviors with real cases and no spend duplicated effort.
  
https://groups.google.com/forum/#!topic/sonic-test-workgroup/GGBYggZBNmk

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Q:How to add static ARP in SONiC using config_db.json? What is the syntax?

**A**:	Refer the [section](https://github.com/Azure/sonic-swss/blob/master/doc/swss-schema.md#neigh_table-1) to add static ARP using configb_db.json.    

----------------------------------------------------------------------------------------------------------------------------
Is PDE specific to BRCM chipset? Not necessarily, who ever supoport SAI can use it.
What are the interfaces PDE provides for ASIC and platform? PDDF data driven framework (JSON APIs)& existing driver API's
Can framework allow vendor extensions ? PDDF supports vendor extensions
How to package PDE ?  PDE can be built along with full sonic image & dockers or individual docker
Will custom plugins (ex:BMC) could integrate with PDE? yes
Can we load PDE into multiple targets? possible 
















