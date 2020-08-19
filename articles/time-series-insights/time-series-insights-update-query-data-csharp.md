---
title: Fråga efter data från en Gen2-miljö med C#-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du frågar data från en Azure Time Series Insights Gen2-miljö med hjälp av en app som skrivits i C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/14/2020
ms.custom: seodec18
ms.openlocfilehash: 19c21f1e95127d064d90d5c6915b4dd67871adaa
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263494"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Fråga efter data från Azure Time Series Insights Gen2-miljön med hjälp av C Sharp

Det här C#-exemplet visar hur du frågar efter data från [Gen2-API: er för data åtkomst](https://docs.microsoft.com/rest/api/time-series-insights/reference-data-access-overview) i Azure Time Series Insights Gen2-miljöer.

> [!TIP]
> Visa Gen2 C#-kod exempel på [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Sammanfattning

Exempel koden nedan visar följande funktioner:

* Stöd för automatisk generering av SDK från [Azure AutoRest](https://github.com/Azure/AutoRest).
* Så här hämtar du en åtkomsttoken via Azure Active Directory med [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Så här skickar du den hämtade åtkomsttoken i `Authorization` rubriken för efterföljande API-begäranden för data åtkomst.
* Exemplet innehåller ett konsol gränssnitt som demonstrerar hur HTTP-begäranden görs till följande:
  * [API för Gen2-miljöer](https://docs.microsoft.com/rest/api/time-series-insights/reference-environments-apis)
    * [Hämta tillgänglighets-API för miljöer](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability) och [Hämta API för Event schema](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [API för Gen2-fråga](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)
    * [Hämta händelse-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [Hämta serie-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)och [Hämta API för sammanställd serie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [API: er för Time Series-modellen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Hämta hierarkier API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) och [hierarkier batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [Hämta API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) och [typer batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [Hämta instanser API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) och [instanser batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* Avancerade [Sök](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#search-features) -och [TSX](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) -funktioner.

## <a name="prerequisites-and-setup"></a>Krav och installation

Slutför följande steg innan du kompilerar och kör exempel koden:

1. [Etablera en Gen2 Azure Time Series Insightss](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) miljö.
1. Konfigurera din Azure Time Series Insightss miljö för Azure Active Directory enligt beskrivningen i [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md).
1. Kör [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) som anges i [Readme.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) för att generera Azure Time Series Insights Gen2 klient beroenden.
1. Öppna `TSIPreviewDataPlaneclient.sln` lösningen och ange `DataPlaneClientSampleApp` som standard projekt i Visual Studio.
1. Installera de nödvändiga projekt beroendena med stegen som beskrivs [nedan](#project-dependencies) och kompilera exemplet till en körbar `.exe` fil.
1. Kör `.exe` filen genom att dubbelklicka på den.

## <a name="project-dependencies"></a>Projektberoenden

Vi rekommenderar att du använder den senaste versionen av Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -version 16.4.2 +

Exempel koden har flera obligatoriska beroenden som kan visas i [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) -filen.

Ladda ned paketen i Visual Studio 2019 genom att välja alternativet **build**  >  **build-lösning** .

Du kan också lägga till varje paket med [NuGet 2.12 +](https://www.nuget.org/). Ett exempel:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Exempel kod för C#

Exempel koden för C# finns i [csharpquery-exempel](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample)

> [!NOTE]
>
> * Kod exemplet kan köras utan att ändra standard miljövariablerna.
> * Kod exemplet kompileras till en körbar app för .NET-konsolen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om frågor i [API-referensen för frågor](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).

* Läs hur du [ansluter en JavaScript-app med hjälp av klient-SDK](https://github.com/microsoft/tsiclient) för att Azure Time Series Insights.
