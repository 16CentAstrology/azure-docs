---
title: Dell PowerEdge R360 for operational technology (OT) monitoring - Microsoft Defender for IoT
description: Learn about the Dell PowerEdge R360 appliance's configuration when used for OT monitoring with Microsoft Defender for IoT in enterprise deployments.
ms.date: 03/14/2024
ms.topic: reference
---

# Dell PowerEdge R360

This article describes the Dell PowerEdge R360 appliance, supported for operational technology (OT) sensors in an enterprise deployment.
The Dell PowerEdge R360 is also available for the on-premises management console.

|Appliance characteristic  | Description|
|---------|---------|
|**Hardware profile** | E1800|
|**Performance** | Max bandwidth: 1 Gbps<br>Max devices: 10,000 |
|**Physical Specifications** | Mounting: 1U with rail kit<br>Ports: 6x RJ45 1 GbE|
|**Status** | Supported, available as a preconfigured appliance|

The following image shows a view of the Dell PowerEdge R360 front panel:

:::image type="content" source="../media/tutorial-install-components/r360-front.png" alt-text="Photograph of the Dell PowerEdge R360 front panel." border="false":::

The following image shows a view of the Dell PowerEdge R360 back panel:

:::image type="content" source="../media/tutorial-install-components/r360-rear.png" alt-text="Photograph of the Dell PowerEdge R360 back panel." border="false":::

## Specifications

|Component| Technical specifications|
|:----|:----|
|Chassis| 1U rack server|
|Dimensions| Height: 1.68 in / 42.8 mm <br>Width: 18.97 in / 482.0 cm<br>Depth: 23.04 in / 585.3 mm (without bezel) 23.57 in / 598.9 mm (with bezel)|
|Processor| Intel Xeon E-2434 3.4 GHz <br>8M Cache<br> 4C/8T, Turbo, HT (55 W) DDR5-4800|
|Memory|32 GB |
|Storage| 2.4 TB Hard Drive |
|Network controller| - PowerEdge R360 Motherboard with with Broadcom 5720 Dual Port 1Gb On-Board LOM, <br>- PCIe Blank Filler, Low Profile. <br>- Intel Ethernet i350 Quad Port 1GbE BASE-T Adapter, PCIe Low Profile, V2|
|Management|iDRAC Group Manager, Disabled|
|Rack support| ReadyRails Sliding Rails With Cable Management Arm|

## Dell PowerEdge R360 - Bill of Materials

|Quantity|PN|Description|
|----|---|----|
|1| 210-BJTR | Base PowerEdge R360 Server|
|1| 461-AAIG | Trusted Platform Module 2.0 V3 |
|1| 321-BKHP | 2.5" Chassis with up to 8 Hot Plug Hard Drives, Front PERC |
|1| 338-CMRB | Intel Xeon E-2434 3.4G, 4C/8T, 8M Cache, Turbo, HT (55 W) DDR5-4800 |
|1| 412-BBHK | Heatsink |
|1| 370-AAIP | Performance Optimized |
|1| 370-BBKS | 4800 MT/s UDIMMs |
|2| 370-BBKF | 16 GB UDIMM, 4800 MT/s ECC |
|1| 780-BCDQ | RAID 10 |
|1| 405-ABCQ | PERC H355 Controller Card |
|1| 750-ACFR | Front PERC Mechanical Parts, front load |
|4| 400-BEFU | 1.2 TB Hard Drive SAS 12 Gbps 10k 512n 2.5in Hot Plug |
|1| 384-BBBH | Power Saving BIOS Settings |
|1| 387-BBEY | No Energy Star |
|1| 384-BDML | Standard Fan |
|1| 528-CTIC | iDRAC9, Enterprise 16G |
|2| 450-AADY | C13 to C14, PDU Style, 10 AMP, 6.5 Feet (2m), Power Cord |
|1| 330-BCMK | Riser Config 2, Butterfly Gen4 Riser (x8/x8) |
|1| 329-BJTH | PowerEdge R360 Motherboard with with Broadcom 5720 Dual Port 1Gb On-Board LOM |
|1| 414-BBJB | PCIe Blank Filler, Low Profile |
|1| 540-BDII | Intel Ethernet i350 Quad Port 1GbE BASE-T Adapter, PCIe Low Profile, V2, FIRMWARE RESTRICTIONS APPLY |
|1| 379-BCRG | iDRAC, Factory Generated Password, No OMQR |
|1| 379-BCQX | iDRAC Service Module (ISM), NOT Installed |
|1| 325-BEVH | PowerEdge 1U Standard Bezel |
|1| 350-BCTP | Dell Luggage Tag R360 |
|1| 379-BCQY | iDRAC Group Manager, Disabled |
|1| 470-AFBU | BOSS Blank |
|1| 770-BCWN | ReadyRails Sliding Rails With Cable Management Arm |

## Install Defender for IoT software on the DELL R360

This procedure describes how to install Defender for IoT software on the Dell R360.

The installation process takes about 20 minutes. During the installation, the system restarts several times.

To install Defender for IoT software:

1. Connect the screen and keyboard to the appliance, and then connect to the CLI.

1. Connect an external CD or disk-on-key that contains the software you downloaded from the Azure portal.

1. Start the appliance.

1. Continue with the generic procedure for installing Defender for IoT software. For more information, see [Defender for IoT software installation](../how-to-install-software.md).

<!--
## Dell PowerEdge R350 installation

This section describes how to install Defender for IoT software on the Dell PowerEdge R350 appliance.

Before installing the software on the Dell appliance, you need to adjust the appliance's BIOS configuration.

> [!NOTE]
> Installation procedures are only relevant if you need to re-install software on a pre-configured device, or if you buy your own hardware and configure the appliance yourself.
>

### Prerequisites

To install the Dell PowerEdge R350 appliance, you need:

- An Enterprise license for Dell Remote Access Controller (iDrac)

- A BIOS configuration XML

### Set up the BIOS and RAID array

This procedure describes how to configure the BIOS configuration for an unconfigured sensor appliance.
If any of the steps below are missing in the BIOS, make sure that the hardware matches the specifications above.

Dell BIOS iDRAC is a system management software designed to give administrators control of Dell hardware remotely. It allows administrators to monitor system performance, configure settings, and troubleshoot hardware issues from a web browser. It can also be used to update system BIOS and firmware. The BIOS can be set up locally or remotely. To set up the BIOS remotely from a management computer, you need to define the iDRAC IP address and the management computer's IP address on the same subnet.

**To configure the iDRAC IP address**:

1. Power up the sensor.

1. If the OS is already installed, select the F2 key to enter the BIOS configuration.

1. Select **iDRAC Settings**.

1. Select **Network**.

   > [!NOTE]
   > During the installation, you must configure the default iDRAC IP address and password mentioned in the following steps. After the installation, you change these definitions.

1. Change the static IPv4 address to **10.100.100.250**.

1. Change the static subnet mask to **255.255.255.0**.

   :::image type="content" source="../media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Screenshot that shows the static subnet mask in iDRAC settings.":::

1. Select **Back** > **Finish**.

**To configure the Dell BIOS**:

This procedure describes how to update the Dell PowerEdge R350 configuration for your OT deployment.

Configure the appliance BIOS only if you didn't purchase your appliance from Arrow, or if you have an appliance, but don't have access to the XML configuration file.

1. Access the appliance's BIOS directly by using a keyboard and screen, or use iDRAC.

   - If the appliance isn't a Defender for IoT appliance, open a browser and go to the IP address configured beforehand. Sign in with the Dell default administrator privileges. Use **root** for the username and **calvin** for the password.

   - If the appliance is a Defender for IoT appliance, sign in by using **XXX** for the username and **XXX** for the password.

1. After you access the BIOS, go to **Device Settings**.

1. Choose the RAID-controlled configuration by selecting **Integrated RAID controller 1: Dell PERC\<PERC H755 Adapter\> Configuration Utility**.

1. Select **Configuration Management**.

1. Select **Create Virtual Disk**.

1. In the **Select RAID Level** field, select **RAID10**. In the **Virtual Disk Name** field, enter **ROOT** and select **Physical Disks**.

1. Select **Check All** and then select **Apply Changes**

1. Select **Ok**.

1. Scroll down and select **Create Virtual Disk**.

1. Select the **Confirm** check box and select **Yes**.

1. Select **OK**.

1. Return to the main screen and select **System BIOS**.

1. Select **Boot Settings**.

1. For the **Boot Mode** option, select **UEFI**.

1. Select **Back**, and then select **Finish** to exit the BIOS settings.

### Install Defender for IoT software on the Dell PowerEdge R350

This procedure describes how to install Defender for IoT software on the Dell PowerEdge R350.

The installation process takes about 20 minutes. After the installation, the system restarts several times.

**To install the software**:

1. Verify that the version media is mounted to the appliance in one of the following ways:

   - Connect an external CD or disk-on-key that contains the sensor software you downloaded from the Azure portal.

   - Mount the ISO image by using iDRAC. After signing in to iDRAC, select the virtual console, and then select **Virtual Media**.

1. In the **Map CD/DVD** section, select **Choose File**.

1. Choose the version ISO image file for this version from the dialog box that opens.

1. Select the **Map Device** button.

   :::image type="content" source="../media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Screenshot that shows a mapped device.":::

1. The media is mounted. Select **Close**.

1. Start the appliance. When you're using iDRAC, you can restart the servers by selecting the **Console Control** button. Then, on the **Keyboard Macros**, select the **Apply** button, which starts the Ctrl+Alt+Delete sequence.

1. Continue by installing OT sensor or on-premises management software. For more information, see [Defender for IoT software installation](../how-to-install-software.md).
-->
## Next steps

Continue learning about the system requirements for physical or virtual appliances. For more information, see [Which appliances do I need?](../ot-appliance-sizing.md).

Then, use any of the following procedures to continue:

- [Download software for an OT sensor](../ot-deploy/install-software-ot-sensor.md#download-software-files-from-the-azure-portal)
- [Download software files for an on-premises management console](../legacy-central-management/install-software-on-premises-management-console.md#download-software-files-from-the-azure-portal)
