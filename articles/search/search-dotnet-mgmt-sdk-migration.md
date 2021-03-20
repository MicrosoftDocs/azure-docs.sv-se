---
title: Uppgradera till Azure Search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Uppgradera till Azure Search .NET Management SDK från tidigare versioner. Lär dig mer om nya funktioner och de kod ändringar som krävs för migrering.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936715"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Uppgradera versioner av Azure Search .NET Management SDK

Den här artikeln förklarar hur du migrerar till efterföljande versioner av Azure Search .NET Management SDK, som används för att etablera eller avetablera Sök tjänster, justera kapacitet och hantera API-nycklar.

Hanterings-SDK: er är riktade till en angiven version av hanterings REST API. Mer information om begrepp och åtgärder finns i [Sök hantering (rest)](/rest/api/searchmanagement/).

## <a name="versions"></a>Versioner

| SDK-version | Motsvarande REST API version | Ändring av funktions tillägg eller beteende |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | API-version = 2020-30-20 | Lägger till slut punkts säkerhet (IP-brandväggar och integrering med [Azures privata länk](../private-link/private-endpoint-overview.md)) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | API-version = 2019-10-01 | Förbättrad användbarhet. Ändring av [list nyckel nycklar](/rest/api/searchmanagement/querykeys/listbysearchservice) (Get är upphör). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | API-version = 2015-08-19  | Första versionen |

## <a name="how-to-upgrade"></a>Så här uppgraderar du

1. Uppdatera din NuGet-referens för att `Microsoft.Azure.Management.Search` använda antingen NuGet Package Manager-konsolen eller genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

1. När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas, vilket innebär att du är klar.

1. Om din version Miss lyckas kan det bero på att du har implementerat några av SDK-gränssnitten (till exempel för enhets testning) som har ändrats. För att lösa detta måste du implementera nya metoder som `BeginCreateOrUpdateWithHttpMessagesAsync` .

1. När du har åtgärdat eventuella build-fel kan du göra ändringar i programmet för att dra nytta av nya funktioner. 

## <a name="upgrade-to-30"></a>Uppgradera till 3,0

Version 3,0 lägger till privat slut punkts skydd genom att begränsa åtkomsten till IP-intervall och genom att integrera med Azures privata länk för Sök tjänster som inte ska visas på det offentliga Internet.

### <a name="new-apis"></a>Nya API: er

| API | Kategori| Information |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP-brandvägg | Begränsa åtkomsten till en tjänst slut punkt till en lista över tillåtna IP-adresser. Se [Konfigurera IP-brandvägg](service-configure-firewall.md) för begrepp och Portal instruktioner. |
| [Delad privat länk resurs](/rest/api/searchmanagement/sharedprivatelinkresources) | Private Link | Skapa en delad privat länk resurs som ska användas av en Sök tjänst.  |
| [Anslutningar för privata slut punkter](/rest/api/searchmanagement/privateendpointconnections) | Private Link | Upprätta och hantera anslutningar till en Sök tjänst via privat slut punkt. Se [skapa en privat slut punkt](service-create-private-endpoint.md) för begrepp och Portal instruktioner.|
| [Privata länk resurser](/rest/api/searchmanagement/privatelinkresources/) | Private Link | För en Sök tjänst som har en privat slut punkts anslutning hämtar du en lista över alla tjänster som används i samma virtuella nätverk. Om din Sök lösning innehåller indexerare som hämtar från Azure Data källor (Azure Storage, Cosmos DB, Azure SQL) eller använder Cognitive Services eller Key Vault, bör alla dessa resurser ha slut punkter i det virtuella nätverket och detta API ska returnera en lista. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Private Link | Detta är en egenskap för att skapa eller uppdatera tjänst begär Anden. När den är inaktive rad är privat länk den enda åtkomsten. |

### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Du kan inte längre använda Hämta i en [lista över](/rest/api/searchmanagement/querykeys/listbysearchservice) förfrågningar om förfrågningar. I tidigare versioner kunde du använda antingen GET eller POST, i den här versionen och i alla versioner som flyttas framåt stöds endast POST. 

## <a name="upgrade-to-20"></a>Uppgradera till 2,0

Version 2 av Azure Search .NET Management SDK är en mindre uppgradering, så om du ändrar koden bör du bara kräva minimal ansträngning. Ändringarna i SDK är strikta ändringar på klient sidan för att förbättra användbarheten hos själva SDK: n. Dessa förändringar sker på flera plan:

* `Services.CreateOrUpdate` och dess asynkrona versioner avsöker nu automatiskt etableringen `SearchService` och returnerar inte förrän tjänst etableringen har slutförts. Detta gör att du slipper skriva en sådan avsöknings kod själv.

* Om du fortfarande vill avsöka tjänst etableringen manuellt kan du använda den nya `Services.BeginCreateOrUpdate` metoden eller en av dess asynkrona versioner.

* Nya metoder `Services.Update` och dess asynkrona versioner har lagts till i SDK: n. Dessa metoder använder HTTP-korrigering för att stödja stegvis uppdatering av en tjänst. Du kan till exempel skala en tjänst genom att skicka en `SearchService` instans till dessa metoder som bara innehåller de önskade `partitionCount` egenskaperna och `replicaCount` . Det gamla sättet att anropa `Services.Get` , ändra det returnerade `SearchService` och skicka det till `Services.CreateOrUpdate` stöds fortfarande, men det behövs inte längre. 

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem är det bästa forumet för att publicera frågor [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Om du hittar en bugg kan du ange ett problem i [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att märka din ärende rubrik med "[search]".