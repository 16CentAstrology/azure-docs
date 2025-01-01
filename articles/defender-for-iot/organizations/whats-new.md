---
title: What's new in Microsoft Defender for IoT
description: This article describes new features available in Microsoft Defender for IoT, including both OT and Enterprise IoT networks, and both on-premises and in the Azure portal.
ms.topic: whats-new
ms.date: 10/14/2024
ms.custom: enterprise-iot
---

# What's new in Microsoft Defender for IoT?

This article describes features available in Microsoft Defender for IoT, across both OT and Enterprise IoT networks, both on-premises and in the Azure portal, and for versions released in the last nine months.

Features released earlier than nine months ago are described in the [What's new archive for Microsoft Defender for IoT for organizations](release-notes-archive.md). For more information specific to OT monitoring software versions, see [OT monitoring software release notes](release-notes.md).

> [!NOTE]
> Noted features listed below are in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include other legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

[!INCLUDE [defender-iot-defender-reference](../includes/defender-for-iot-defender-reference.md)]

## On-premises management console retirement

The legacy on-premises management console won't be available for download after **January 1st, 2025**. We recommend transitioning to the new architecture using the full spectrum of on-premises and cloud APIs before this date. For more information, see [on-premises management console retirement](ot-deploy/on-premises-management-console-retirement.md).

## December 2024

|Service area  |Updates  |
|---------|---------|
| **OT networks** | - [Support Multiple Source Devices in DDoS Attack Alerts](#support-multiple-source-devices-in-ddos-attack-alerts) |

### Support Multiple Source Devices in DDoS Attack Alerts

Alert details now display up to 10 source devices involved in DDoS attack.

## October 2024

|Service area  |Updates  |
|---------|---------|
| **OT networks** | - [Add wildcards to allowlist domain names](#add-wildcards-allowlist-domain-names)<br> - [Added protocol](#added-protocol) <br> - [New sensor setting type Public addresses](#new-sensor-setting-type-public-addresses) <br>  - [Improved OT sensor onboarding](#improved-ot-sensor-onboarding) |

### Add wildcards allowlist domain names

When adding domain names to the FQDN allowlist use the `*` wildcard to include all sub-domains. For more information, see [allow internet connections on an OT network](how-to-accelerate-alert-incident-response.md#allow-internet-connections-on-an-ot-network).

### Added protocol

We now support the OCPI protocol. See [the updated protocol list](concept-supported-protocols.md#supported-protocols-for-ot-device-discovery).

### New sensor setting type Public addresses

We're adding the **Public addresses** type to the sensor settings, that allows you to exclude public IP addresses that might have been used for internal use and shouldn't be tracked. For more information, see [add sensor settings](configure-sensor-settings-portal.md#add-sensor-settings).

### Improved OT sensor onboarding

If there are connection problems, during sensor onboarding, between the OT sensor and the Azure portal at the configuration stage, the process can't be completed until the connection problem is solved.

We now support completing the configuration process without the need to solve the communication problem, allowing you to continue the onboarding of your OT sensor quickly and solve the problem at a later time. For more information, see [activate your OT sensor](ot-deploy/activate-deploy-sensor.md#activate-your-ot-sensor).

## July 2024

|Service area  |Updates  |
|---------|---------|
| **OT networks** | - [Security update](#security-update) |

### Security update

This update resolves a CVE, which is listed in [software version 24.1.4 feature documentation](release-notes.md#version-2414).

## June 2024

|Service area  |Updates  |
|---------|---------|
| **OT networks** | - [Malicious URL path alert](#malicious-url-path-alert)<br> - [Newly supported protocols](#newly-supported-protocols)|

### Malicious URL path alert

The new alert, Malicious URL path, allows users to identify malicious paths in legitimate URLs. The Malicious URL path alert expands Defender for IoT's threat identification to include generic URL signatures, crucial for countering a wide range of cyber threats.

For more information, this alert is described in the [Malware engine alerts table](alert-engine-messages.md#malware-engine-alerts).  

### Newly supported protocols

We now support the Open protocol. [See the updated protocol list](concept-supported-protocols.md).

## April 2024

|Service area  |Updates  |
|---------|---------|
| **OT networks** | - [Single sign-on for the sensor console](#single-sign-on-for-the-sensor-console)<br>- [Sensor time drift detection](#sensor-time-drift-detection)<br>- [Security update](#security-update-1) |

### Single sign-on for the sensor console

You can set up single sign-on (SSO) for the Defender for IoT sensor console using Microsoft Entra ID. SSO allows simple sign in for your organization's users, allows your organization to meet regulation standards, and increases your security posture. With SSO, your users don't need multiple login credentials across different sensors and sites.

Using Microsoft Entra ID simplifies the onboarding and offboarding processes, reduces administrative overhead, and ensures consistent access controls across the organization.

:::image type="content" source="media/set-up-sso/sso-sign-in.png" alt-text="Screenshot of the sensor console login screen with SSO.":::

For more information, see [Set up single sign-on on for the sensor console](set-up-sso.md).

### Sensor time drift detection

This version introduces a new troubleshooting test in the connectivity tool feature, specifically designed to identify time drift issues.

One common challenge when connecting sensors to Defender for IoT in the Azure portal arises from discrepancies in the sensor’s UTC time, which can lead to connectivity problems. To address this issue, we recommend that you configure a Network Time Protocol (NTP) server [in the sensor settings](configure-sensor-settings-portal.md#ntp).

### Security update

This update resolves six CVEs, which are listed in [software version 24.1.3 feature documentation](release-notes.md#version-2413).

## Next steps

[Getting started with Defender for IoT](getting-started.md)
