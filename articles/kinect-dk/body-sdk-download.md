---
title: Hämtning av Azure Kinect Body tracking SDK
description: Lär dig hur du hämtar varje version av Azure Kinect sensor SDK i Windows eller Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, Kinect, SDK, hämtnings uppdatering, senaste, tillgänglig, installation, brödtext, spårning
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654500"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Hämta Azure Kinect Body tracking SDK

Det här dokumentet innehåller länkar för att installera varje version av Azure Kinect Body tracking SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Innehåll för Azure Kinect Body tracking SDK

- Sidhuvud och bibliotek för att bygga ett program för innehålls spårning med hjälp av Azure Kinect DK.
- Distribuerbara DLL-filer som behövs för innehålls spårnings program med hjälp av Azure Kinect DK.
- Exempel på brödtext som spårar program.

## <a name="windows-download-links"></a>Länkar för Windows-hämtning

Version       | Ladda ned
--------------|----------
1.1.0 | [databaspaketet](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Installations anvisningar för Linux

För närvarande är den enda distribution som stöds Ubuntu 18,04 och 20,04. Information om hur du begär stöd för andra distributioner finns på [den här sidan](https://aka.ms/azurekinectfeedback).

Först måste du konfigurera [Microsofts paket lagrings plats](https://packages.microsoft.com/)genom att följa anvisningarna [här](/windows-server/administration/linux-package-repository-for-microsoft-software).

`libk4abt<major>.<minor>-dev`Paketet innehåller de huvuden-och cmake-filer som ska byggas `libk4abt` .
`libk4abt<major>.<minor>`Paketet innehåller de delade objekt som krävs för att köra körbara filer som är beroende av `libk4abt` och exempel visnings programmet.

De grundläggande självstudierna kräver `libk4abt<major>.<minor>-dev` paketet. Kör för att installera den

`sudo apt install libk4abt<major>.<minor>-dev`

Om kommandot lyckas är SDK klart för användning.

> [!NOTE]
> När du installerar SDK måste du komma ihåg vilken sökväg du installerar till. Till exempel "C:\Program Files\Azure Kinect Body tracking SDK 1.0.0". Du hittar exemplen som refereras i artiklar i den här sökvägen.
> Bröd text spårnings exemplen finns i mappen [Body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) i Azure-Kinect-samples-lagringsplatsen. Du hittar exemplen som refereras i artiklar här.

## <a name="change-log"></a>Ändringslogg

### <a name="v110"></a>v-1.1.0
* Zoomfunktionen Lägg till stöd för DirectML (endast Windows) och TensorRT-körning av attityd uppskattnings modell. Se vanliga frågor och svar om nya körnings miljöer.
* Zoomfunktionen Lägg till `model_path` i `k4abt_tracker_configuration_t` struktur. Tillåter användare att ange sökväg för en attityd uppskattnings modell. Standardvärdet för `dnn_model_2_0_op11.onnx` standard beräknings modellen finns i den aktuella katalogen.
* Zoomfunktionen Inkludera en `dnn_model_2_0_lite_op11.onnx` beräknings modell för lite attityd. Den här modellen är för hög ~ dubbelt så hög prestanda ökning för en lägre precision på ~ 5%.
* Zoomfunktionen Verifierade exempel kompileras med Visual Studio 2019 och uppdaterings exempel för att använda den här versionen.
* [Viktig ändring] Uppdatera till ONNX Runtime 1,6 med stöd för processor, CUDA 11,1, DirectML (endast Windows) och TensorRT 7.2.1-körnings miljöer. Kräver att NVIDIA-drivrutinen uppdateras till R455 eller bättre.
* [Viktig ändring] Ingen NuGet-installation.
* [Fel korrigering] Lägg till stöd för NVIDIA RTX 30xx-seriens GPU- [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Fel korrigering] Lägg till stöd för AMD-och Intel-integrerade GPU- [länkar](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) (endast Windows)
* [Fel korrigering] Uppdatera till CUDA 11,1- [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [Fel korrigering] Uppdatera till sensor SDK 1.4.1- [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v-1.0.1
* [Fel korrigering] Åtgärda problemet att SDK kraschar om du läser in onnxruntime.dll från sökväg på Windows version 19025 eller senare: [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v-1.0.0
* Zoomfunktionen Lägg till C#-omslutningen i MSI-installationsprogrammet.
* [Fel korrigering] Åtgärda problemet att huvud rotationen inte kan identifieras korrekt: [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Fel korrigering] Åtgärda problemet att CPU-användningen går upp till 100% på Linux-datorn: [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Stickprov Lägg till två exempel i exempel lagrings platsen. Exempel 1 visar hur du transformerar text spårnings resultat från [länken](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)djup yta till färg utrymme. exempel 2 visar hur du identifierar [länken](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) golv plan

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Kinect DK](about-azure-kinect-dk.md)

- [Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Konfigurera innehålls spårning i Azure Kinect](body-sdk-setup.md)