# FAQs

## Q:How to find the current onie version  version?

## A: In the Sonic device, enter **cat /host/machine.conf**  command. This will give the current ONIE version along with other details such as vendor id, platform id, kernet version etc  
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

## A: SONiC currently supports around 2400 host entries

----------------------------------------------------------------------------------------------------------------------------
## Q:What is the maximum rate for ARP/ND?

## A: Currently the max rate for ARP/ND is 600 packets. It will be increased it to higher number(8000) in CoPP file  to improve the learning time.

----------------------------------------------------------------------------------------------------------------------------
## Q:How to print all the keys in a database?

A: redis-cli -n 4 keys "MGMT_INTERFACE*"
   This will print all keys in the database number 4.
   4 is the CONFIG_DB
   6 is STATE_DB
   0 is APP_DB

----------------------------------------------------------------------------------------------------------------------------
## Q: How to get the value for a particular key?

## A: To get value for the particular key. For example, the management interface;  

   ``` redis-cli -n 4 HGETALL "MGMT_INTERFACE|eth0|10.11.12.13/24" ```

----------------------------------------------------------------------------------------------------------------------------
## Q:How to add static routes in SONiC using config_db.json? Whats the syntax? 

## A:	Static route addition is not supported in SONiC at present. They can be added via linux “ip route add..” command but it will not be persistent after reboot.  

----------------------------------------------------------------------------------------------------------------------------
## Q:How to add static ARP in SONiC using config_db.json? Whats the syntax?

## A:	Refer the [section!](https://github.com/Azure/sonic-swss/blob/master/doc/swss-schema.md#neigh_table-1) to add static ARP using configb_db.json.    

----------------------------------------------------------------------------------------------------------------------------
## Q:How to add static MAC  in SONiC using config_db.json? Whats the syntax?

## A:	Static MAC configuration via config_db is not currently supported.  There is an alternate way by adding an FDB entry file.  
    ```
	   [
         {
           "FDB_TABLE:Vlan1000:00-00-00-10-20-30": {
                  "port": "Ethernet31",
                  "type": "static"
              },
              "OP": "SET"
         }
       ]
	```
    Later use swssconfig tool which is located in docker swss to load it into APP_DB.  
	Alternatively you can add entry to app_db manually, but you also need to publish event to the channel that subscribed by fdborch.  
	
----------------------------------------------------------------------------------------------------------------------------
## Q:Where do the python bindings, to program the switch's control plane are copied?

## A: The python bindings to program the switch's control plane are copied in an empty directory "switch_sai_thrift".  

----------------------------------------------------------------------------------------------------------------------------
## Q:How to program FDB static/dynamic entries into ASIC?

## A: You can program FDB entries into ASIC as static/dynamic. Please check this link [here!](https://github.com/Azure/SONiC/issues/249). Give type as either "static" or "dynamic"
  
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

Enter into swss container  
```
   $ docker exec -it swss bash
```
Edit fdb.json  

   ```
      [
        {
           "FDB_TABLE:Vlan1000:00-00-00-10-20-30": {
                  "port": "Ethernet16",
                  "type": "static"
              },
              "OP": “SET“  --> “DEL”: remove
         }
      ]
   ```

Apply fdb.json  

   ``` swssconfig ./fdb.json ```  

NOTE:  
    a. The VLAN configuration should be correct and interface (Ethernet16 in this example) should be 'up'.  
    b. config should NOT be reloaded because the static fdb configuration is not retained after reload.  

---------------------------------------------------------------------------------------------------------------------------- 
## Q:How to know the interface naming mode?

## A: Enter the command "show interface naming_mode". Initially it will be "default".  

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
## Q:How to change the interface naming mode?

## A: Enter the command -   
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

NOTE:  Notice the change in the interface names under the "Interface" coloumn in the above two examples.  

----------------------------------------------------------------------------------------------------------------------------
## Q:vlan configuration from the python cli results in a change in the redis database but not in the kernel level. Why?

## A: Interfaces of a switch  stick to their startup configurations no matter what changes are made dynamically from the python cli. Vlanmgrd needs to be restarted for the kernel to know about the change. Refer the below mentioned link for further information.  
   [Link!](https://groups.google.com/forum/#!searchin/sonicproject/Vlan$20config$20from$20python$20cli%7Csort:date/sonicproject/wG0ZKYylVEU/GivLlGa5AAAJ)
 
----------------------------------------------------------------------------------------------------------------------------
## Q:How to see 'telemetry binary' after building the Debian package?
   Using Debian package in ubuntu 16.04 server, sonic-telemetry_0.1_amd64.deb is copied to sonic switch and installed it using "dpkg -i sonic-telemetry_0.1_amd64.deb". But unable to see telemetry binary.  

Example:
 ```
    root@switch2:/home/admin#  ./telemetry --help
    bash: ./telemetry: No such file or directory 
  
    git clone https://github.com/Azure/sonic-telemetry.git
    pushd sonic-telemetry
    dpkg-buildpackage -rfakeroot -b -us -uc
    popd
 ```
 
## A: Telemetry runs on a docker and it is located in the path: /usr/bin/telemetry.sh.  
   An easy way is to include telemetry in sonic image itself. You can do that by marking ENABLE_SYSTEM_TELEMETRY=y  on the "rules/config" file while building sonic image. Telemetry service gets started automatically.   

   Use the following command to view the telemtry process currently running.  

Example:
 ```
    root@sonic:/home/admin# ps -elf | grep tel
    4 S admin     2401     1  0  80   0 -  2955 -      Nov05 ?        00:00:00 /bin/bash /usr/bin/telemetry.sh attach
    0 S admin     2403  2401  0  80   0 - 45153 SyS_ep Nov05 ?        00:00:00 docker attach --no-stdin telemetry
    4 S root      2761  2370  0  80   0 - 159110 -     Nov05 pts/5    00:29:09 /usr/sbin/telemetry -logtostderr --insecure --port 8080 --allow_no_client_auth -v=2
 ```

-------------------------------------------------------------------------------------------------------------------------------
## Q:What is the significance of DEVICE_ROLE and difference between ToRRouter & LeafRouter role. Why is radvd configured and started only when Sonic DEVICE_ROLE is set to "ToRRouter"?

## A: The radv is the IPv6 ND protocol part feature, used by routers to advertise their router-role information to the shared-link devices. DEVICE_ROLE meaning the device(switch) roles in the DC network, ToRRouter meaning "Top of the Rack Router/Switcher", LeafRouter that is the middle device between ToRRouter and Spine; The ToRs often directly connect to the Servers(host), so ToRs need the function radvd to spread the router-link informations. But LeafRouter is not directly connected to the servers(hosts), so one must not enable this function.   

-------------------------------------------------------------------------------------------------------------------------------
## Q:How to configure ecmp in config_db.json? 

## A: ECMP routes are learned via BGP. There is currently no static support for this via config_db.  

----------------------------------------------------------------------------------------------------------------------------------------
## Q:How to set the MTU size?

A: To set the MTU size using an entry in the configb_db.json file using the following steps.  
  
Example:
 ```
    "PORT": {
        “Ethernet0”: {
                             "mtu" : "9100"
                            }
        ...............
 ```
 
-----------------------------------------------------------------------------------------------------------------------------------
## Q:What is the CPU configuration that SONiC currently supports?

## A: SONiC currently only supports x86_64 CPU. For more information refer [this!](https://github.com/Azure/SONiC/wiki/Supported-Devices-and-Platforms)

-------------------------------------------------------------------------------------------------------------------------------------
## Q:How to connect 2 sonic-vs containers via virtual Ethernet link?

## A: Start two debian containers (sw0 and sw1) as below  

 ```	
	docker run -id --name sw0 debian bash
    docker run -id --name sw1 debian bash
 ```

   Create virtual Ethernet link  
   
 ```
    sudo ip link add sw0vEthernet0 type veth peer name sw1vEthernet0
 ```
 
   Map the link endpoints to network namespace of respective containers where sw0_pid and sw1_pid are PIDs of sw0 and sw1 containers  
 
 ```
    sudo ip link set sw0vEthernet0 netns sw0_pid
    sudo ip link set sw1vEthernet0 netns sw1_pid
 ```

   Change names of the interfaces to vEthernet0 and bring them up

 ```
    nsenter -t sw0_pid -n ip link set dev sw0vEthernet0 name vEthernet0
    nsenter -t sw0_pid -n ip link set dev vEthernet0 up
 
    nsenter -t sw1_pid -n ip link set dev sw1vEthernet0 name vEthernet0
    nsenter -t sw1_pid -n ip link set dev vEthernet0 up

 ```
 
   Start two sonic-vs containers (vs0 and vs1)

 ```
   $ docker run --privileged --network container:sw0 --name vs0 -d docker-sonic-vs
   $ docker run --privileged --network container:sw1 --name vs1 -d docker-sonic-vs
 
 ```

   Configure Ethernet0 interface in each sonic-vs container to 10.0.0.0/31 (in vs0) and 10.0.0.1/31 (in vs1)

 ```

   $ docker exec -it vs0 bash
     ifconfig Ethernet0 10.0.0.0/31 up
	 
   $ docker exec -it vs1 bash
     ifconfig Ethernet0 10.0.0.1/31 up

 ```

Now run “show interfaces status” command in each sonic-vs container.  

 ```
    Interface       Lanes        Speed    MTU     Alias         Oper     Admin
  -----------  ---------------  -------  -----  --------------  ------  -------
  Ethernet0      29,30,31,32      N/A    N/A    fortyGigE0/0    down      N/A
  Ethernet4      25,26,27,28      N/A    N/A    fortyGigE0/4    down      N/A
  Ethernet8      37,38,39,40      N/A    N/A    fortyGigE0/8    down      N/A

 ```

If the output shows that operating status of Ethernet0 port is down, then bring up the vEthernet0 interface in each VS using "ifconfig" command.


After running the above sequence check by pinging 10.0.0.1 address from vs0 and 10.0.0.0 from vs1 container.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Q:What are the get attributes for switch in SAI for table size?  

## A: Following are the get attributes for switch in SAI for table size.
   ```
      SAI_SWITCH_ATTR_FDB_TABLE_SIZE  
      SAI_SWITCH_ATTR_L3_NEIGHBOR_TABLE_SIZE  
      SAI_SWITCH_ATTR_L3_ROUTE_TABLE_SIZE 
	  
   ```   
------------------------------------------------------------------------------------------------------
## Q:After booting the SONiC switch, it always has default IP configured on each port. How to build an image without this default IP address configured on each port?  

## A: During the initial install and boot process, Sonic creates the IPs for each interface and stores this in the config_db.json file (in /etc/sonic).  These interfaces can either be deleted or replaced in the file and then reapply the configuration.  Also replacing the config_db.json can be part of the deployment strategy.  

(https://groups.google.com/forum/#!topic/sonicproject/fKKDyunc6h8)  

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Q:Which daemon writes to APP_DB?  

## A: "fpmsyncd" daemon writes to APP_DB  

-------------------------------------------------------------------------------------
## Q:Where does the configuration from "teamd" has been moved to?  

A: All the configuration from "/etc/teamd config file" has been moved to "teammgrd"  

--------------------------------------------------------------------------------------------
## Q:How is VRF configured in Linux kernel?  

## A: VRF is configured though the CLI wrapper but eventually SONiC uses the linux NetLink calls  

---------------------------------------------------------------------------------------------
## Q:Does VRF design support later versions of Linux Kernel 4.9?  

## A: Yes. VRF design supports later versions of Linux kernel.  

-------------------------------------------------------------------------------------------
## Q:How does SONiC support link aggregation?

## A: SONiC supports link aggregation usig "teamd" container. SONiC uses [libteam!] (http://libteam.org/) as the LACP implementation.The configuration is stored in the configuration database. Please refer to the [configuration!] (https://github.com/Azure/SONiC/wiki/Configuration) samples here to configure the port-channels.  

-------------------------------------------------------------------------------------------
## Q:What is the main task of a daemon?

## A: The main task of a daemon is to post device data to DB. Currently, to fetch switch peripheral devices related data SONiC will directly access hardware through platform pluggins

-------------------------------------------------------------------------------------------
## Q:What are the different types of PMON container daemons?

## A: PMON container has the following daemons.  

```
   xcvrd for transceivers/SFPs 
   ledd  for front panel LEDs  
   psud  for PSUs

```
-------------------------------------------------------------------------------------------
## Q:Does FAN unit has a daemon?

## A: Currently there is no daemon for FAN unit  

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Q:What package does SONiC use?

## A: SONiC uses the "click Python package" in order to expose the available APIs.

-------------------------------------------------------------------------------------------------------------------------------------
## Q:Why does an IPv6 address is assigned by default when an L2 VLAN is created?

## A: SONiC supports IPv6 forwarding and a link local address is added by the Kernel. When L2 VLAN is created that IP address is assigned to it by default.  
   If required it can be disabled by adding a line in /etc/sysctl.conf  on switch.
   
   ```
     # Uncomment the next line to enable packet forwarding for IPv6
	 #  Enabling this option disables Stateless Address Autoconfiguration
	 #  based on Router Advertisements for this host
	 #net.ipv6.conf.all.forwarding=1
	
	 net.ipv6.conf.all.disable_ipv6=1  <<<<<<<<<<<
   ```
--------------------------------------------------------------------------------------------------------------------------------------

   
 















