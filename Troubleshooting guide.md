#**Troubleshooting Guide**
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


-   **[Home](/Azure/SONiC/wiki){.d-block}**
-   **[ACL Configuration High Level
    Design](/Azure/SONiC/wiki/ACL-Configuration-High-Level-Design){.d-block}**
-   **[ACL Configuration Requirement
    Description](/Azure/SONiC/wiki/ACL-Configuration-Requirement-Description){.d-block}**
-   **[ACL High Level
    Design](/Azure/SONiC/wiki/ACL-High-Level-Design){.d-block}**
-   **[ACL test plan](/Azure/SONiC/wiki/ACL-test-plan){.d-block}**
-   **[Arch Spec
    Guidelines](/Azure/SONiC/wiki/Arch-Spec-Guidelines){.d-block}**
-   **[Architecture](/Azure/SONiC/wiki/Architecture){.d-block}**
-   **[Asymmetric PFC High Level
    Design](/Azure/SONiC/wiki/Asymmetric-PFC-High-Level-Design){.d-block}**
-   **[Becoming a
    contributor](/Azure/SONiC/wiki/Becoming-a-contributor){.d-block}**
-   **[BGP GR helper mode test
    plan](/Azure/SONiC/wiki/BGP-GR-helper-mode-test-plan){.d-block}**
-   **[BGP MP test plan](/Azure/SONiC/wiki/BGP-MP-test-plan){.d-block}**
-   **[Buffers Configuration Update
    design](/Azure/SONiC/wiki/Buffers-Configuration-Update-design){.d-block}**
-   **[Build Broadcom Opennsl and
    SAI](/Azure/SONiC/wiki/Build-Broadcom-Opennsl-and-SAI){.d-block}**
-   **[Building Guide](/Azure/SONiC/wiki/Building-Guide){.d-block}**
-   **[Command
    Reference](/Azure/SONiC/wiki/Command-Reference){.d-block}**
-   **[Community
    Meetings](/Azure/SONiC/wiki/Community-Meetings){.d-block}**
-   **[Configuration](/Azure/SONiC/wiki/Configuration){.d-block}**
-   **[Configuration with Minigraph
    (\~Sep 2017)](/Azure/SONiC/wiki/Configuration-with-Minigraph-(~Sep-2017)){.d-block}**
-   **[Converting old or creating new buffers
    config](/Azure/SONiC/wiki/Converting-old-or-creating-new-buffers-config){.d-block}**
-   **[Critical Resource Monitoring High Level
    Design](/Azure/SONiC/wiki/Critical-Resource-Monitoring-High-Level-Design){.d-block}**
-   **[CRM test plan](/Azure/SONiC/wiki/CRM-test-plan){.d-block}**
-   **[Design
    Guidelines](/Azure/SONiC/wiki/Design-Guidelines){.d-block}**
-   **[Developing Guide](/Azure/SONiC/wiki/Developing-Guide){.d-block}**
-   **[DHCP Relay Agent Test
    Plan](/Azure/SONiC/wiki/DHCP-Relay-Agent-Test-Plan){.d-block}**
-   **[ECMP and LAG Hash
    Seed](/Azure/SONiC/wiki/ECMP-and-LAG-Hash-Seed){.d-block}**
-   **[ECN WRED configuration
    utility](/Azure/SONiC/wiki/ECN-WRED-configuration-utility){.d-block}**
-   **[Enable ECN on lossless
    queues](/Azure/SONiC/wiki/Enable-ECN-on-lossless-queues){.d-block}**
-   **[Everflow High Level
    Design](/Azure/SONiC/wiki/Everflow-High-Level-Design){.d-block}**
-   **[Everflow test
    plan](/Azure/SONiC/wiki/Everflow-test-plan){.d-block}**
-   **[FAQ](/Azure/SONiC/wiki/FAQ){.d-block}**
-   **[Fast Reboot](/Azure/SONiC/wiki/Fast-Reboot){.d-block}**
-   **[FDB Scale Test
    Plan](/Azure/SONiC/wiki/FDB-Scale-Test-Plan){.d-block}**
-   **[Feature arch spec
    guidelines](/Azure/SONiC/wiki/Feature-arch-spec-guidelines){.d-block}**
-   **[FIB Scale Test
    Plan](/Azure/SONiC/wiki/FIB-Scale-Test-Plan){.d-block}**
-   **[Generate Minigraph File Based On Testbed
    Type](/Azure/SONiC/wiki/Generate-Minigraph-File-Based-On-Testbed-Type){.d-block}**
-   **[How to breakout a
    port](/Azure/SONiC/wiki/How-to-breakout-a-port){.d-block}**
-   **[How to Change the
    Password](/Azure/SONiC/wiki/How-to-Change-the-Password){.d-block}**
-   **[How to Check SNMP
    Configuration](/Azure/SONiC/wiki/How-to-Check-SNMP-Configuration){.d-block}**
-   **[How to Clear MAC
    table](/Azure/SONiC/wiki/How-to-Clear-MAC-table){.d-block}**
-   **[How to Deploy
    SONiC](/Azure/SONiC/wiki/How-to-Deploy-SONiC){.d-block}**
-   **[How To Reset
    Password](/Azure/SONiC/wiki/How-To-Reset-Password){.d-block}**
-   **[How to Use Mellanox SDK Debug
    Utilities](/Azure/SONiC/wiki/How-to-Use-Mellanox-SDK-Debug-Utilities){.d-block}**
-   **[How to Use SAI
    Player](/Azure/SONiC/wiki/How-to-Use-SAI-Player){.d-block}**
-   **[HOWTO compile and run sai server
    docker](/Azure/SONiC/wiki/HOWTO-compile-and-run-sai-server-docker){.d-block}**
-   **[HOWTO Reset syncd
    docker](/Azure/SONiC/wiki/HOWTO-Reset-syncd-docker){.d-block}**
-   **[HOWTO write a PTF
    Test](/Azure/SONiC/wiki/HOWTO-write-a-PTF-Test){.d-block}**
-   **[IPv4 Decapsulation
    test](/Azure/SONiC/wiki/IPv4-Decapsulation-test){.d-block}**
-   **[L2 Switch mode](/Azure/SONiC/wiki/L2-Switch-mode){.d-block}**
-   **[LAG Feature Test
    Suite](/Azure/SONiC/wiki/LAG-Feature-Test-Suite){.d-block}**
-   **[LogAnalyzer](/Azure/SONiC/wiki/LogAnalyzer){.d-block}**
-   **[Manual for Enable SONiC L2 Mode on MSN2700 with Minimum
    ConfigDB](/Azure/SONiC/wiki/Manual-for-Enable-SONiC-L2-Mode-on-MSN2700-with-Minimum-ConfigDB){.d-block}**
-   **[Mellanox SDK and PRM Sniffer Utility CLI Design for
    SONiC](/Azure/SONiC/wiki/Mellanox-SDK-and-PRM-Sniffer-Utility-CLI-Design-for-SONiC){.d-block}**
-   **[MMU Allocation](/Azure/SONiC/wiki/MMU-Allocation){.d-block}**
-   **[OCP Summit 2018 and SONiC SAI Workshop Slide
    Collection](/Azure/SONiC/wiki/OCP-Summit-2018--and-SONiC-SAI-Workshop-Slide-Collection){.d-block}**
-   **[OS10 SONiC](/Azure/SONiC/wiki/OS10-SONiC){.d-block}**
-   **[PFC and Queue SNMP counters High Level
    Design](/Azure/SONiC/wiki/PFC-and-Queue-SNMP-counters-High-Level-Design){.d-block}**
-   **[PFC Watchdog](/Azure/SONiC/wiki/PFC-Watchdog){.d-block}**
-   **[PFC Watchdog
    Design](/Azure/SONiC/wiki/PFC-Watchdog-Design){.d-block}**
-   **[PFC Watchdog Test
    Plan](/Azure/SONiC/wiki/PFC-Watchdog-Test-Plan){.d-block}**
-   **[Port Breakout and Speed
    Requirements](/Azure/SONiC/wiki/Port-Breakout-and-Speed-Requirements){.d-block}**
-   **[Port Breakout High Level
    Design](/Azure/SONiC/wiki/Port-Breakout-High-Level-Design){.d-block}**
-   **[Port Configuration Utility High Level
    Design](/Azure/SONiC/wiki/Port-Configuration-Utility-High-Level-Design){.d-block}**
-   **[Port Speed
    Configuration](/Azure/SONiC/wiki/Port-Speed-Configuration){.d-block}**
-   **[Porting Guide](/Azure/SONiC/wiki/Porting-Guide){.d-block}**
-   **[QoS configuration in Config DB. ECN WRED configuration utility
    test
    plan](/Azure/SONiC/wiki/QoS-configuration-in-Config-DB.-ECN-WRED-configuration-utility-test-plan){.d-block}**
-   **[Quick Start](/Azure/SONiC/wiki/Quick-Start){.d-block}**
-   **[Router
    Advertiser](/Azure/SONiC/wiki/Router-Advertiser){.d-block}**
-   **[Run Time Buffers Configuration Update
    design](/Azure/SONiC/wiki/Run-Time-Buffers-Configuration-Update-design){.d-block}**
-   **[SNMP lldpLocManAddrTable and lldpRemManAddrTable
    design](/Azure/SONiC/wiki/SNMP-lldpLocManAddrTable-and-lldpRemManAddrTable-design){.d-block}**
-   **[SONiC Clear FDB CLI
    Design](/Azure/SONiC/wiki/SONiC-Clear-FDB-CLI-Design){.d-block}**
-   **[SONiC P4 Software
    Switch](/Azure/SONiC/wiki/SONiC-P4-Software-Switch){.d-block}**
-   **[SONiC Pre Summit Workshop and Hackathon
    2019](/Azure/SONiC/wiki/SONiC-Pre-Summit-Workshop-and-Hackathon-2019){.d-block}**
-   **[Sonic Roadmap
    Planning](/Azure/SONiC/wiki/Sonic-Roadmap-Planning){.d-block}**
-   **[SONiC SMMP Design on Management
    Ports](/Azure/SONiC/wiki/SONiC-SMMP-Design-on-Management-Ports){.d-block}**
-   **[SONiC to SONiC
    update](/Azure/SONiC/wiki/SONiC-to-SONiC-update){.d-block}**
-   **[sonic version](/Azure/SONiC/wiki/sonic-version){.d-block}**
-   **[SONiC Workshop in Beijing Oct
    2018](/Azure/SONiC/wiki/SONiC-Workshop-in-Beijing-Oct-2018){.d-block}**
-   **[SONiC Workshop on OCP Summit
    2016](/Azure/SONiC/wiki/SONiC-Workshop-on-OCP-Summit-2016){.d-block}**
-   **[SSD Health design
    (Draft)](/Azure/SONiC/wiki/SSD-Health-design-(Draft)){.d-block}**
-   **[Supported Devices and
    Platforms](/Azure/SONiC/wiki/Supported-Devices-and-Platforms){.d-block}**
-   **[Testing Guide](/Azure/SONiC/wiki/Testing-Guide){.d-block}**
-   **[The PSU CLI and SNMP
    support](/Azure/SONiC/wiki/The-PSU-CLI-and-SNMP-support){.d-block}**
-   **[Troubleshooting
    Guide](/Azure/SONiC/wiki/Troubleshooting-Guide){.d-block}**
-   **[Upgrade SONiC to Debian
    9](/Azure/SONiC/wiki/Upgrade-SONiC-to-Debian-9){.d-block}**
-   **[Upgrading SONiC kernel to 3.16.0ΓÇÉ5 or later
    versions](/Azure/SONiC/wiki/Upgrading-SONiC-kernel-to-3.16.0%E2%80%905-or-later-versions){.d-block}**
-   **[Use routeresync to Swap the Routing
    Stack](/Azure/SONiC/wiki/Use-routeresync-to-Swap-the-Routing-Stack){.d-block}**
-   **[Using swssconfig to apply
    configuration](/Azure/SONiC/wiki/Using-swssconfig-to-apply-configuration){.d-block}**
-   **[VLAN Feature Test
    Suite](/Azure/SONiC/wiki/VLAN-Feature-Test-Suite){.d-block}**
-   **[VLAN trunk test
    plan](/Azure/SONiC/wiki/VLAN-trunk-test-plan){.d-block}**
-   Show 74 more pages...
:::
:::
:::

::: {.gollum-markdown-content}
::: {.Box .Box--condensed .mb-4}
::: {.Box-body .wiki-custom-sidebar .markdown-body}
-   [Home](/Azure/SONiC/wiki/Home){.internal .present}
-   [FAQ](/Azure/SONiC/wiki/FAQ){.internal .present}
-   For Users
    -   [Quick Start](/Azure/SONiC/wiki/Quick-Start){.internal .present}
    -   [Supported Devices and
        Platforms](/Azure/SONiC/wiki/Supported-Devices-and-Platforms){.internal
        .present}
    -   [SONiC P4 Software
        Switch](/Azure/SONiC/wiki/SONiC-P4-Software-Switch){.internal
        .present}
    -   [Configuration](/Azure/SONiC/wiki/Configuration){.internal
        .present}
    -   [Command
        Reference](/Azure/SONiC/wiki/Command-Reference){.internal
        .present}
    -   [Troubleshooting
        Guide](/Azure/SONiC/wiki/Troubleshooting-Guide){.internal
        .present}
-   For Developers
    -   [Becoming a
        Contributor](/Azure/SONiC/wiki/Becoming-a-contributor){.internal
        .present}
    -   [Architecture](/Azure/SONiC/wiki/Architecture){.internal
        .present}
    -   [Building Guide](/Azure/SONiC/wiki/Building-Guide){.internal
        .present}
    -   [Porting Guide](/Azure/SONiC/wiki/Porting-Guide){.internal
        .present}
    -   [Testing Guide](/Azure/SONiC/wiki/Testing-Guide){.internal
        .present}
    -   [Developing Guide](/Azure/SONiC/wiki/Developing-Guide){.internal
        .present}
-   [Sonic Roadmap
    Planning](/Azure/SONiC/wiki/Sonic-Roadmap-Planning){.internal
    .present}
-   [Report Issues](https://github.com/Azure/SONiC/issues)
-   Presentations
    -   [OCP Summit
        2016](files/talks/SAI-SONiC-OCP-Summit-Mar16-shared.pdf)
    -   [OCP Summit
        2017](https://www.youtube.com/watch?v=DvFTCpwnUQ4&feature=youtu.be)
-   [Community
    Meetings](https://github.com/Azure/SONiC/wiki/Community-Meetings)
-   Join Us
    -   [Mailing
        list](https://groups.google.com/forum/#!forum/sonicproject)
    -   [Slack
        discussion](https://groups.google.com/forum/#!forum/sonicproject)
:::
:::
:::

##### Clone this wiki locally {#clone-this-wiki-locally .mt-0 .mb-2}

::: {.width-full .input-group}
:::
:::
:::
:::

::: {.modal-backdrop .js-touch-events}
:::
:::
:::

::: {.footer .container-lg .width-full .p-responsive role="contentinfo"}
::: {.position-relative .d-flex .flex-row-reverse .flex-lg-row .flex-wrap .flex-lg-nowrap .flex-justify-center .flex-lg-justify-between .pt-6 .pb-2 .mt-6 .f6 .text-gray .border-top .border-gray-light}
-   ┬⌐ 2019 [GitHub]{title="0.35737s from unicorn-64465cd49-m6qv6"}, Inc.
-   [Terms](https://github.com/site/terms)
-   [Privacy](https://github.com/site/privacy)
-   [Security](https://github.com/security)
-   [Status](https://githubstatus.com/)
-   [Help](https://help.github.com)

<!-- -->

-   [Contact GitHub](https://github.com/contact)
-   [Pricing](https://github.com/pricing)
-   [API](https://developer.github.com)
-   [Training](https://training.github.com)
-   [Blog](https://github.blog)
-   [About](https://github.com/about)
:::

::: {.d-flex .flex-justify-center .pb-6}
[]{.f6 .text-gray-light}
:::
:::

::: {#ajax-error-message .ajax-error-message .flash .flash-error}
You can't perform that action at this time.
:::

::: {.js-stale-session-flash .stale-session-flash .flash .flash-warn .flash-banner hidden=""}
[You signed in with another tab or window. [Reload]() to refresh your
session.]{.signed-in-tab-flash} [You signed out in another tab or
window. [Reload]() to refresh your session.]{.signed-out-tab-flash}
:::

::: {.octocat-spinner .my-6 .js-details-dialog-spinner}
:::

::: {.Popover .js-hovercard-content .position-absolute style="display: none; outline: none;" tabindex="0"}
::: {.Popover-message .Popover-message--bottom-left .Popover-message--large .Box .box-shadow-large style="width:360px;"}
:::
:::

::: {.js-global-screen-reader-notice .sr-only aria-live="polite"}
:::
:::
:::
:::
