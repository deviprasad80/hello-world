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

 