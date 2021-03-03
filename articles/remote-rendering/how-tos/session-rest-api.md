---
title: Session Management-REST API
description: Beskriver hur du hanterar sessioner
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 414dc161d7cba2e89d15c47b8da5abdfb7cb3338
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712172"
---
# <a name="use-the-session-management-rest-api"></a>Använda REST API:et för sessionshantering

Om du vill använda funktioner för Azure-fjärrrendering måste du skapa en *session*. Varje session motsvarar en server som tilldelas i Azure och som en klient enhet kan ansluta till. När en enhet ansluts återger servern begärda data och ger resultatet som en video ström. När sessionen skapas väljer du vilken [typ av Server](../reference/vm-sizes.md) som du vill köra på, vilket fastställer prissättningen. När sessionen inte behövs längre bör den stoppas. Om den inte stoppas manuellt stängs den av automatiskt när sessionens *låne tid* upphör att gälla.

## <a name="rest-api-reference"></a>Referens för REST-API

Du hittar REST API-referensen [här](/rest/api/mixedreality/2021-01-01preview/remoterendering) och Swagger-definitionerna [här](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).
Vi tillhandahåller ett PowerShell-skript i [databasen arr samples](https://github.com/Azure/azure-remote-rendering) i mappen *scripts* , som kallas *RenderingSession.ps1*, som visar användningen av vår tjänst. Skriptet och dess konfiguration beskrivs här: exempel på [PowerShell-skript](../samples/powershell-example-scripts.md).
Vi tillhandahåller även SDK: er för [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java och python.

> [!IMPORTANT]
> Latens är en viktig faktor vid användning av fjärrrendering. För bästa möjliga upplevelse kan du skapa sessioner i den region som är närmast dig. Du kan använda [Azures svars tids test](https://www.azurespeed.com/Azure/Latency) för att avgöra vilken region som är närmast dig.

> [!IMPORTANT]
> En ARR Runtime SDK krävs för att en klient enhet ska kunna ansluta till en rendering-session. Dessa SDK: er är tillgängliga i [.net](/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) och [C++](/cpp/api/remote-rendering/). Förutom att ansluta till tjänsten kan dessa SDK: er också användas för att starta och stoppa sessioner.

## <a name="next-steps"></a>Nästa steg

* [Använda Azure-frontend-API: er för autentisering](frontend-apis.md)
* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)