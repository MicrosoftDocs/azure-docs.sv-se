---
title: System krav för Azure Kinect sensor SDK
description: Förstå system kraven för Azure Kinect sensor SDK i Windows och Linux.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, Kinect, system krav, processor, GPU, USB, installation, konfiguration, minimum, krav
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656982"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>System krav för Azure Kinect sensor SDK

Det här dokumentet innehåller information om de system krav som krävs för att installera sensor-SDK och distribuera din Azure Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Stödda operativsystem och arkitekturer

- Windows 10 april 2018 (version 1803, OS version 17134) Release (x64) eller en senare version
- Linux Ubuntu 18,04 (x64), med en GPU-drivrutin som använder OpenGLv 4.4 eller en senare version

Sensor-SDK: n är tillgänglig för Windows API (Win32) för interna C/C++ Windows-program. SDK: n är inte tillgänglig för UWP-program. Azure Kinect DK stöds inte för Windows 10 i S-läge.

## <a name="development-environment-requirements"></a>Krav för utvecklings miljö

Om du vill bidra till sensor SDK-utveckling går du till [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Lägsta maskin varu krav för värd datorer

Maskin varu kravet för dator värden beror på program/algoritm/sensor/upplösning som körs på värddatorn. Rekommenderad minsta konfiguration för sensor-SDK för Windows:

- Sjunde generation Intel &reg; CoreTM i3-processor (dubbla kärnor 2,4 GHz med HD620-GPU eller snabbare)
- 4 GB minne
- Dedikerad USB3-port
- Grafik driv rutin stöd för OpenGL 4,4 eller DirectX 11,0

Lägre end-eller äldre processorer kan också fungera beroende på dina användnings fall.

Prestanda skiljer sig också mellan Windows/Linux-operativsystem och grafik driv rutiner som används.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Maskin varu krav för innehålls spårnings värd datorer

Kraven för innehålls spårning av dator värden är strängare än kravet på allmän dator värd. Rekommenderad minsta Body tracking SDK-konfiguration för Windows:

- Sjunde generation Intel &reg; CoreTM i5-processor (fyra kärnor på 2,4 GHz eller snabbare)
- 4 GB minne
- NVIDIA GEFORCE GTX 1050 eller motsvarande
- Dedikerad USB3-port

Den rekommenderade minimala konfigurationen förutsätter K4A_DEPTH_MODE_NFOV_UNBINNED djupgående läge på 30fps tracking 5 Peoples. Lägre end-eller äldre processorer och NVIDIA-GPU: er kan också fungera beroende på dina användnings fall.

## <a name="usb3"></a>USB3

Det finns kända kompatibilitetsproblem med USB-värdstyrenheter. Du hittar mer information om [fel söknings sidan](troubleshooting.md#usb3-host-controller-compatibility)

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Kinect DK](about-azure-kinect-dk.md)

- [Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Konfigurera innehålls spårning i Azure Kinect](body-sdk-setup.md)
