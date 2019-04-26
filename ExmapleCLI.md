## CONFIGURATION COMMANDS EXAMPLES

| S.No |COMMAND | EXAMPLE |
| ----| ------- | --------- |
|   1  |  config aaa authentication failthrough        | config aaa authentication failthrough enable  |
|	   | (Authenticate if primary server fails)        | config aaa authentication failthrough disable  | 
|	   |											   | config aaa authentication failthrough default  |
|	   |											   |												|
|   2  |  config aaa authentication fallback		   | config aaa authentication fallback enable    |
|	   |(Activate/deactivate secondary/tertiary 	   | config aaa authentication fallback disable   |
|      |authentication if the primary mechanism fails) | config aaa authentication fallback default   |
|  	   | 			   								   | 											  |
|	3  | config aaa authentication login			   | config aaa authentication login tacacs+  	  |
|	   | (Set the primary authentication entity)	   | config aaa authentication login local		  |
|      |											   | config aaa authentication login default      |
|	   |  											   | 											  |
|	4  |  config tacacs								   | config tacacs add 10.11.12.13 -t 10 -k testing789 -a mschap -o 50 -p 9	|
|	   | (TACACS configuration options)				   | config tacacs delete 10.11.12.13			  |
|      | 											   | config tacacs authtype mschap				  |
|	   |											   | config tacacs default authtype				  |
|      |											   | config tacacs passkey testing123			  |
|	   |											   | config tacacs timeout 60					  |
|	   | 							   				   |											  |
|	5  | config acl	update				 	     	   | config acl update full /etc/sonic/acl_full_snmp_1_2_ssh_4.json |
|	   | (ACL FULL/INCREMENTAL updates)				   | config acl update incremental /etc/sonic/acl_incremental_snmp_1_3_ssh_4.json |		
|	   | 											   | 		
|	6  | 	BGP Shutdown- All/Neighbor				   | config bgp shutdown all  					  |
|	   |   (To shut BGP services down)				   | config bgp shutdown neighbor 192.168.1.124	  |
|	   | 											   | config bgp shutdonw neighbor SONIC02SPINE	  |
|	   | 											   | 
|	   | 	BGP Startup - All/Neighbor				   | config bgp startup all
|	   |   (To bring up BGP services up)			   | config bgp startup neighbor 192.168.1.124	  |
|	   | 											   | config bgp startup neighbor SONIC02SPINE	  |
|	   | 											   | 
|	7  | config ECN									   | config ecn -profile wredprofileabcd -rmax 100|
|	   | (Part of QoS used for congestion notification)| (options: rmin/ymax/ymin/gmax/gmin)          |
|	   | 											   | 											  |				
|	8  | config interface ip add (assign IP)		   | config interface Vlan100 ip add 10.11.12.13/24 |
|	   | config interface ip remove	(unassign IP)	   | config interface Vlan100 ip remove 10.11.12.13/24 |
|	   | config interface pfc 						   | 												   |
|	   | (To set asymmetrical flow control. Off by default)|config interface Ethernet0 pfc asymmetric on   |
|	   | 											   | 												   |
|	   | config interface(Shutdown the interface)	   | config interface Ethernet0 shutdown			   |
|	   | config interface(Startup the interface)	   | config interface Ethernet0 startup				   |
|	   | 											   | 												   |
|	   | config interface speed (Set the speed)		   | onfig interface Ethernet0 speed 40000			   |
|	   | 											   | 												   |
|	   | config interface_naming_mode(Initially set to default)| config interface_naming_mode alias		   |
|	   | 											   | 												   |
|	9  | config load								   | config load									   |
|	   | (Load config from the file /etc/sonic/config_db.json)| 										   |
|	   | 											   | 												   |
|  10  | config load_mgmt config					   | config load_mgmt_config						   |
|	   | (To reconfigure hostname and mgmt interface)  | 												   |
|	   | 											   | 												   |
|  11  | config load minigraph						   | config load_minigraph							   |
|	   | (To load the configuration from /etc/sonic/minigraph.xml)|config load_minigraph				   |
|	   | 											   | 												   |
|  12  | config reload								   | config reload									   |
|	   | (clear current configuration,import new configurationn from /etc/sonic/config_db.json)| 		   |
|	   | 											   | 												   |
|	   | config save								   | config save -y									   |
|	   | (To save the configuration changes)		   | config save -y /etc/sonic/config2.json			   |
|	   | 											   | 												   |
|  13  | config mirror_session						   | config mirror_session add mrr_abcd 1.2.3.4 20.21.22.23 8 100 0x6558 0|
|	   | (To add/remove mirroring sessions)		       | config mirror_session remove mrr_abcd			   |
|	   | 											   | 												   |
|  14  | config portchannel							   | config portchannel add PortChannel0011			   |
|	   | (To add/delete the portchannel			       | config portchannel del PortChannel0011			   |
|	   | 											   | 												   |
|	   | config portchannel member					   | config portchannel member add PortChannel0011 Ethernet4|
|	   | (To add/delete a member port into the existing portchannel| config portchannel member del PortChannel0011 Ethernet4|
|	   | 											   | 												   |
|  15  | config QoS									   | 												   |
|	   | (To clear all the QoS configuration)		   | config qos clear								   |
|	   | (To reload the QoS configuration)			   | config qos reload								   |
|	   | 											   | 												   |
|  16  | config Vlan								   | config vlan add 100							   |
|	   | (To add/delete VLAN)						   | config vlan del 100							   |
|	   | 											   | 												   |
|	   | config Vlan member							   | config vlan member add 100 Ethernet0			   |
|	   | (To add/delete a member port into the existing vlan| config vlan member del 100 Ethernet0	       |
|	   | 											   | 												   |
|  17  | config warm_restart						   | config warm_restart bgp_timer 1000				   |
|	   | (To set the bgp_timer value)				   | 											       |
|      |											   |												   |
|	   | (To enable/disable this including services)   | config warm_restart enable						   |
|	   | 											   | config warm_restart enable swss		    	   | 
|	   | 											   | 												   |
|	   | (To set the neighsyncd_timer value	of SWSS service)| 	config warm_restart neighsyncd_timer 2000  |
|	   | (To set the neighsyncd_timer value	of SWSS service)|	config warm_restart teamsyncd_timer 3000   | 
|	   | 											   | 												   |
| 18   | config watermark telemetry interval 		   | config watermark telemetry interval 999		   |
|	   | (To configure the interval for telemetry)	   | 												   |
|	   | 											   | 												   |
| 19   | config management interface				   | /sbin/ifconfig eth0 10.11.12.13/24				   |
|	   | (To assign an IP to management interface)	   | 												   |

