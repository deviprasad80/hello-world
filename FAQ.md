# FAQs

***How do we find out the onie version used by current SONIC? and what's the current version that's been used?***

``` Please enter "cat /host/machine.conf" command in the Sonic device.```
  - Example
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
