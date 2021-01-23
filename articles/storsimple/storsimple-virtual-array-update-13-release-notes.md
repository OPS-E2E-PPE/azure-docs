---
title: Microsoft Azure StorSimple Virtual Array Update 1.3 release notes | Microsoft Docs
description: Describes critical open issues and resolutions for the Azure StorSimple Virtual Array running Update 1.3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
---

# StorSimple Virtual Array Update 1.3 release notes

The following release notes identify the critical open issues and the resolved issues for Microsoft Azure StorSimple Virtual Array updates.

The release notes are continuously updated. As critical issues requiring a workaround are discovered, they are added. Before you deploy your StorSimple Virtual Array, carefully review the information contained in the release notes.

Update 1.3 corresponds to software version 10.0.10319.0.

> [!IMPORTANT]
> - Update 1.3 is a critical update. We strongly recommend you install it as soon as possible.
> - You can install Update 1.3 only on devices running Update 1.2.
> - Updates are disruptive and restart your device. If I/O is in progress, the device incurs downtime. For detailed instructions on packages used to apply this update, go to [Download Update 1.3](#download-update-13).

## What's new in Update 1.3

This update contains the following improvements:

- Transport Layer Security (TLS) 1.2 is a mandatory update and must be installed. From this release onward, TLS 1.2 becomes the standard protocol for all Azure portal communication.
- Garbage collection bug fixes improve the performance of the garbage collection cycle when the device and storage account are in two distant regions.
- Fix for backup failures due to blob timeouts.
- Updated OS/.NET framework security patches:
  - [KB4540725](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4540725\V1.001\free\NEU\X64): March 2020 SSU (Servicing Stack Update)
  - [KB4565541](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4565541\V1.014\free\NEU\X64): July 2020 rollup
  - [KB4565622](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Partner\DOTNET47x\KB4565622\V1.000\free\NEU\x64): July 2020 .NET Framework update

## Download Update 1.3

To download this update, go to the [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) server, and download the KB4575898 package. This package contains the following packages:

- **KB4540725**, which contains cumulative Windows Updates for 2012 R2 up to March 2020. For more information on what is included in this rollup, go to [March monthly security rollup](https://support.microsoft.com/help/4540725).
- **KB4565541**, which contains cumulative Windows Updates for 2012 R2 up to July 2020. For more information on what is included in this rollup, go to [February monthly security rollup](https://support.microsoft.com/help/4565541).
- **KB4565622**, which contains cumulative.NET Framework updates up to July 2020. For more information on what is included in this rollup, go to [February monthly security rollup](https://support.microsoft.com/help/4565622).
- **KB3011067**, which contains device software updates.

Download KB4575898, and follow these instructions to [Apply the update via local web UI](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## Known issues in Update 1.3
No new issues were release-noted in Update 1.3. All the release-noted issues are carried over from previous releases. To see the summary of known issues included from the previous releases, go to [Known issues in Update 1.2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## Next steps
Download KB4575898 and [Apply the update via local web UI](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## References
Looking for an older release note? Go to:

- [StorSimple Virtual Array Update 1.2 Release Notes](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1.0 Release Notes](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0.6 Release Notes](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0.5 Release Notes](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0.4 Release Notes](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0.3 Release Notes](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0.1 and 0.2 Release Notes](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Virtual Array General Availability Release Notes](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)