---
title: Kör bakgrunds aktiviteter med WebJobs
description: Lär dig hur du använder WebJobs för att köra bakgrunds aktiviteter i Azure App Service. Välj bland en mängd olika skript format och kör dem med CRON-uttryck.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452806"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Kör bakgrunds aktiviteter med WebJobs i Azure App Service

Konceptet med att köra [bakgrunds aktiviteter](./webjobs-create-ieux-conceptual.md) i Azure finns i Azure App tjänst webb jobb. Lär dig hur du distribuerar <abbr title="Ett program eller skript i samma instans som en webbapp, API-app eller mobilapp.">WebJobs</abbr> genom att använda [Azure Portal](https://portal.azure.com) för att ladda upp en körbar fil eller ett skript. 

Tre WebJobs som stöds är:

* **Kontinuerlig**: startar omedelbart, vanligt vis körs i en oändlig loop.
* **Schemalagd**: startar från schemalagd utlösare
* **Manuell**: startar från manuell utlösare

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> Nästa steg

* [Lär dig mer om bakgrunds aktiviteter som webbjobb](./webjobs-create-ieux-conceptual.md)
* [Visa logg historik för webbjobb](./webjobs-create-ieux-view-log.md)

* Använd [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) för att förenkla många programmerings aktiviteter

* Lär dig att [utveckla och distribuera WebJobs med Visual Studio](webjobs-dotnet-deploy-vs.md)
