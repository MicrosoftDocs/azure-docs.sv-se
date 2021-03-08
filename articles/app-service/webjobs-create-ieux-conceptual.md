---
title: Webbjobb, bakgrunds aktiviteter, på Azure
description: Lär dig mer om WebJobs.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452279"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>WebJobs kör bakgrunds aktiviteter i Azure App Service

Den här artikeln visar hur du distribuerar WebJobs med hjälp av [Azure Portal](https://portal.azure.com) för att ladda upp en körbar fil eller ett skript. Information om hur du utvecklar och distribuerar WebJobs med hjälp av Visual Studio finns i [Distribuera WebJobs med Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Översikt
WebJobs är en funktion i [Azure App Service](index.yml) som gör att du kan köra ett program eller skript i samma instans som en WEBBAPP, API-app eller mobilapp. Det kostar inget extra att använda WebJobs.

> [!IMPORTANT]
> WebJobs stöds ännu inte för App Service i Linux.

Azure WebJobs SDK kan användas med WebJobs för att förenkla många programmerings aktiviteter. Mer information finns i [Vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions är ett annat sätt att köra program och skript. En jämförelse mellan WebJobs och Functions finns i [Välj mellan Flow, Logic Apps, Functions och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Webb jobb typer

I följande tabell beskrivs skillnaderna mellan *kontinuerliga* och *utlösta* WebJobs.


|Kontinuerlig igenkänning  |Utlöses  |
|---------|---------|
| Startar omedelbart när webb jobbet skapas. För att det ska gå att avsluta jobbet fungerar programmet eller skriptet vanligt vis i en oändlig slinga. Om jobbet har slutförts kan du starta om det. | Startar bara när de utlöses manuellt eller enligt ett schema. |
| Körs på alla instanser som webbappen körs på. Du kan också begränsa webbjobbet till en enda instans. |Körs på en enda instans som Azure väljer för belastnings utjämning.|
| Stöder fjärrfelsökning. | Stöder inte fjärrfelsökning.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Lägg till webb jobb till käll kontroll

Om du har en käll kontroll som kon figurer ATS med ditt program, ska webbjobben distribueras som en del av käll kontroll integreringen. När käll kontrollen har kon figurer ATS med ditt program går det inte att lägga till ett webb jobb från Azure Portal.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Filtyper som stöds för skript eller program

Följande filtyper stöds:

* . cmd,. bat,. exe (med Windows cmd)
* . ps1 (med PowerShell)
* . sh (med bash)
* . php (med PHP)
* . py (med python)
* . js (med Node.js)
* . jar (med Java)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett webb jobb](./webjobs-create-ieux.md)
* Visa logg historik för [Webbjobb](./webjobs-create-ieux-view-log.md)
