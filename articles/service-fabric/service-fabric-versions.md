---
title: Azure Service Fabric versioner
description: Lär dig mer om klusterversioner i Azure Service Fabric och plattformsversioner som aktivt stöds
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 4e656b0cd051079d4504ee2a55a9bf6993065396
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515319"
---
# <a name="service-fabric-supported-versions"></a>Service Fabric versioner som stöds
Tabellerna i den här artikeln beskriver Service Fabric och plattformsversioner som aktivt stöds.

## <a name="windows"></a>Windows

| Service Fabric runtime |Kan uppgradera direkt från|Kan nedgraderas till|Kompatibel SDK- eller NuGet-paketversion|Dotnet-körningar som stöds** |OS-version |Supporten har förser dig med slut |
| --- | --- | --- | --- | --- | --- | --- |
| 8.0 RTO | 7.1 CU10 | 7.2 | Mindre än eller lika med version 5.0 | .NET 5.0 (GA), .NET Core 3.1, .NET Core 2.1, <br>Alla >=4.5 .NET Full Framework| [Se os-version som stöds](#supported-windows-versions-and-support-end-date) | Aktuell version |
| 7,2 CU7 | 7,0 CU9 | 7.1 | Mindre än eller lika med version 4.2 | .NET 5.0 (förhandsversionsstöd), .NET Core 3.1, .NET Core 2.1,<br>Alla >= 4,5 Net Full Framework | [Se OPERATIVSYSTEMversion som stöds](#supported-windows-versions-and-support-end-date) | Den 30 november 2021 |
| 7.2 CU6 | 7.0 CU4 |7.1 | Mindre än eller lika med version 4.2 | .NET 5.0 (förhandsversionsstöd), .NET Core 3.1, .NET Core 2.1,<br>Alla >= 4,5 Net Full Framework | [Se OPERATIVSYSTEMversion som stöds](#supported-windows-versions-and-support-end-date)| Den 30 november 2021 |
| 7.2 RTO-CU5 | 7.0 CU4 | 7.1 |Mindre än eller lika med version 4.2 | .NET Core 3.1, .NET Core 2.1,<br>Alla >= 4,5 Net Full Framework | [Se OPERATIVSYSTEMversion som stöds](#supported-windows-versions-and-support-end-date)| Den 30 november 2021 |
| 7.1 |7.0 CU3 |Ej tillämpligt | Mindre än eller lika med version 4.1 | .NET Core 3.1, .NET Core 2.1,<br>Alla >= 4,5 Net Full Framework | [Se os-version som stöds](#supported-windows-versions-and-support-end-date) | Den 31 juli 2021 |

** Service Fabric tillhandahåller inte någon .NET Core-körning. Tjänstförfattaren ansvarar för att se till att den är <a href="https://docs.microsoft.com/dotnet/core/deploying/">tillgänglig.</a>

## <a name="supported-windows-versions-and-support-end-date"></a>Windows-versioner som stöds och slutdatum för support
Stöd för Service Fabric på ett specifikt operativsystem upphör när stödet för OS-versionen når sin livslängd.


### <a name="windows-server"></a>Windows Server

| OS-version | Service Fabric slutdatum för support | Länk för operativsystemlivscykel |
|---|---|---|
|Windows Server 2019|1/9/2029|<a href="https://docs.microsoft.com/lifecycle/products/windows-server-2019">Windows Server 2019 – Microsoft Lifecycle</a>|
|Windows Server 2016 |1/12/2027|<a href="https://docs.microsoft.com/lifecycle/products/windows-server-2016">Windows Server 2016 – Microsoft Lifecycle</a>|
|Windows Server 2012 R2 |10/10/2023|<a href="https://docs.microsoft.com/lifecycle/products/windows-server-2012-r2">Windows Server 2012 R2 – Microsoft Lifecycle</a>|
|Version 20H2 |5/10/2022|<a href="https://docs.microsoft.com/lifecycle/products/windows-server">Windows Server – Microsoft Lifecycle</a>|
|Version 2004 |12/14/2021|<a href="https://docs.microsoft.com/lifecycle/products/windows-server">Windows Server – Microsoft Lifecycle</a>|
|Version 1909 |5/11/2021|<a href="https://docs.microsoft.com/lifecycle/products/windows-server">Windows Server – Microsoft Lifecycle</a>|

<br>

### <a name="windows-10"></a>Windows 10

| OS-version | Service Fabric slutdatum för support | Länk till operativsystemets livscykel |
| --- | --- | --- |
| Windows 10 2019 LTSC | 1/9/2029 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-2019-ltsc">Windows 10 2019 LTSC – Microsoft Lifecycle</a> |
| Version 20H2 | 5/9/2023 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise och utbildning – Microsoft Lifecycle</a> |
| Version 2004 | 12/14/2021| <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise och utbildning – Microsoft Lifecycle</a> |
| Version 1909 | 5/10/2022 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise och utbildning – Microsoft Lifecycle</a> |
| Version 1809 | 5/11/2021 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise and Education – Microsoft Lifecycle</a> |
| Version 1803 | 5/11/2021 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise and Education – Microsoft Lifecycle</a> |

## <a name="linux"></a>Linux

| Service Fabric runtime | Kan uppgradera direkt från |Kan nedgraderas till |Kompatibel SDK- eller NuGet-paketversion | Dotnet-körningar som stöds** | OS-version | Supporten har förser dig med slut |
| --- | --- | --- | --- | --- | --- | --- |
| 8.0 RTO | 7.1 CU8 | 7.2 | Mindre än eller lika med version 5.0 | .NET Core 3.1, .NET Core 2.1 | [Se os-version som stöds](#supported-linux-versions-and-support-end-date) | Aktuell version |
| 7,2 CU7 | 7,0 CU9 | 7.1 | Mindre än eller lika med version 4.2 | .NET Core 3.1, .NET Core 2.1 | [Se os-version som stöds](#supported-linux-versions-and-support-end-date) | Den 30 november 2021 |
| 7.2 RTO-CU6 | 7.0 CU4 | 7.1 | Mindre än eller lika med version 4.2 | .NET Core 3.1, .NET Core 2.1 | [Se OPERATIVSYSTEMversion som stöds](#supported-linux-versions-and-support-end-date) | Den 30 november 2021 |
| 7.1 | 7.0 CU3 | Ej tillämpligt | Mindre än eller lika med version 4.1 | .NET Core 3.1, .NET Core 2.1 | [Se OPERATIVSYSTEMversion som stöds](#supported-linux-versions-and-support-end-date) | Den 31 juli 2021 |

** Service Fabric tillhandahåller ingen .NET Core-körning och tjänstförfattaren ansvarar för att se till att den är <a href="https://docs.microsoft.com/dotnet/core/deploying/">tillgänglig</a>

## <a name="supported-linux-versions-and-support-end-date"></a>Linux-versioner som stöds och slutdatum för support
Stöd för Service Fabric på ett specifikt operativsystem upphör när stödet för OS-versionen når slutet av livscykeln.

#### <a name="ubuntu"></a>Ubuntu
| OS-version | Service Fabric slutdatum för support| Länk till operativsystemets livscykel |
| --- | --- | --- |
| Ubuntu 18.04 | April 2028 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu-livscykel</a>|
| Ubuntu 16.04 | April 2024 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu-livscykel</a>|

<br>

## <a name="service-fabric-version-name-and-number-reference"></a>Service Fabric versionsnamn och nummerreferens
I följande tabell visas versionsnamnen för Service Fabric och deras motsvarande versionsnummer.

| Versionsnamn | Windows-versionsnummer | Linux-versionsnummer |
| --- | --- | --- |
| 8.0 RTO | 8.0.514.9590 | 8.0.513.1 | 
| 7,2 CU7 | 7.2.477.9590 | 7.2.476.1 |
| 7,2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7,2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7.2 CU3 | 7.2.433.9590 | NA |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 RTO | 7.2.413.9590 | NA |
| 7.1 CU10 | 7.1.510.9590 | NA |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,4 CU8 | 6.4.670.9590 | Inte tillämpligt|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU6 | 6.4.658.9590 | Inte tillämpligt|
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU2 | 6.4.622.9590 | Inte tillämpligt|
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.1 CU3 | 6.1.472.9494 | Inte tillämpligt|
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 5.7 CU4 | 5.7.221.9494 | Inte tillämpligt|
| 5.7 RTO | 5.7.198.9494 | Inte tillämpligt|
| 5.6 CU3 | 5.6.220.9494 | Inte tillämpligt|
| 5.6 CU2 | 5.6.210.9494 | Inte tillämpligt|
| 5.6 RTO | 5.6.204.9494 | Inte tillämpligt|
| 5,5 CU4 | 5.5.232.0 | Inte tillämpligt|
| 5.5 CU3 | 5.5.227.0 | Inte tillämpligt|
| 5,5 CU2 | 5.5.219.0 | Inte tillämpligt|
| 5,5 CU1 | 5.5.216.0    | Inte tillämpligt|
| 5,4 CU2 | 5.4.164.9494 | Inte tillämpligt|
| 5.3 CU3 | 5.3.311.9590 | Inte tillämpligt|
| 5.3 CU2 | 5.3.301.9590 | Inte tillämpligt|
| 5.3 CU1 | 5.3.204.9494 | Inte tillämpligt|
| 5.3 RTO | 5.3.121.9494 | Inte tillämpligt|
