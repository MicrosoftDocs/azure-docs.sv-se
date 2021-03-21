---
title: Ladda ned Azure Kinect sensor SDK
description: Lär dig hur du hämtar och installerar Azure Kinect sensor SDK i Windows och Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, Hämta uppdatering, senaste, tillgänglig, installera
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179637"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Ladda ned Azure Kinect sensor SDK

Den här sidan innehåller nedladdnings länkarna för varje version av Azure Kinect sensor SDK. Installations programmet innehåller alla filer som krävs för att utveckla för Azure-Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Azure Kinect sensor SDK-innehåll

- Sidhuvud och bibliotek för att bygga ett program med hjälp av Azure Kinect DK.
- Distribuerbara DLL-filer som krävs av program med hjälp av Azure Kinect DK.
- [Azure Kinect Viewer](azure-kinect-viewer.md).
- [Azure Kinect-inspelaren](azure-kinect-recorder.md).
- [Verktyget för inbyggd Azure Kinect-programvara](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Installations anvisningar för Windows

Du hittar installations information för de senaste och tidigare versionerna av Azure Kinect sensor SDK och inbyggd program vara [här](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

Du hittar käll koden [här](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).

> [!NOTE]
> När du installerar SDK måste du komma ihåg vilken sökväg du installerar till. Till exempel "C:\Program Files\Azure Kinect SDK 1,2". Du hittar de verktyg som refereras till i artiklar i den här sökvägen.

## <a name="linux-installation-instructions"></a>Installations anvisningar för Linux

För närvarande är den enda distribution som stöds Ubuntu 18,04. Information om hur du begär stöd för andra distributioner finns på [den här sidan](https://aka.ms/azurekinectfeedback).

Först måste du konfigurera [Microsofts paket lagrings plats](https://packages.microsoft.com/)genom att följa anvisningarna [här](/windows-server/administration/linux-package-repository-for-microsoft-software).

Nu kan du installera de nödvändiga paketen. `k4a-tools`Paketet innehåller [Azure Kinect Viewer](azure-kinect-viewer.md), [Azure Kinect-inspelaren](record-sensor-streams-file.md)och verktyget för [inbyggd Azure Kinect-programvara](azure-kinect-firmware-tool.md). Installera paketet genom att köra:

`sudo apt install k4a-tools`
 
Det här kommandot installerar de beroende paket som krävs för att verktygen ska fungera korrekt, inklusive den senaste versionen av `libk4a<major>.<minor>` . Du måste lägga till udev-regler för att få åtkomst till Azure Kinect DK utan att vara rot användaren. Instruktioner finns i [installations programmet för Linux-enheter](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup). Alternativt kan du starta program som använder enheten som rot.

`libk4a<major>.<minor>-dev`Paketet innehåller sidhuvudena och cmake-filerna för att bygga program/körbara filer mot `libk4a` .

`libk4a<major>.<minor>`Paketet innehåller de delade objekt som krävs för att köra program/körbara filer som är beroende av `libk4a` .

De grundläggande självstudierna kräver `libk4a<major>.<minor>-dev` paketet. Installera paketet genom att köra:

`sudo apt install libk4a<major>.<minor>-dev` 

Om kommandot lyckas är SDK klart för användning.

Se till att installera den matchande versionen av `libk4a<major>.<minor>` med `libk4a<major>.<minor>-dev` . Om du till exempel installerar `libk4a4.1-dev` paketet installerar du motsvarande `libk4a4.1` paket som innehåller den matchande versionen av delade objekt-filer. Den senaste versionen av `libk4a` finns i länkarna i nästa avsnitt.

## <a name="change-log-and-older-versions"></a>Ändrings logg och äldre versioner

Du kan hitta ändrings loggen för Azure Kinect sensor SDK [här](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Om du behöver en äldre version av Azure Kinect sensor SDK hittar du den [här](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Nästa steg

[Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)
