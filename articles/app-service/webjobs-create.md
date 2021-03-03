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
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 8afc8ca9b0dedb10ecdb30e8abb22a5d0986de5b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723885"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Kör bakgrunds aktiviteter med WebJobs i Azure App Service

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

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Filtyper som stöds för skript eller program

Följande filtyper stöds:

* . cmd,. bat,. exe (med Windows cmd)
* . ps1 (med PowerShell)
* . sh (med bash)
* . php (med PHP)
* . py (med python)
* . js (med Node.js)
* . jar (med Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Skapa ett kontinuerligt webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Om du har en käll kontroll som kon figurer ATS med ditt program, ska webbjobben distribueras som en del av käll kontroll integreringen. När käll kontrollen har kon figurer ATS med ditt program går det inte att lägga till ett webb jobb från Azure Portal.

1. I [Azure Portal](https://portal.azure.com)går du till **App Service** sidan i din app service webbapp, API-app eller mobilapp.

2. Välj **WebJobs**.

   ![Välj webbjobb](./media/web-sites-create-web-jobs/select-webjobs.png)

2. På sidan **WebJobs** väljer du **Lägg till**.

    ![Webb jobbs sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd **Lägg till webb jobb** inställningar som anges i tabellen.

   ![Skärm bild som visar de Lägg till webb jobbs inställningar som du behöver konfigurera.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myContinuousWebJob | Ett namn som är unikt inom en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken förutom "-" och "_". |
   | **Fil uppladdning** | ConsoleApp.zip | En *zip* -fil som innehåller din körbara fil eller skript fil samt alla stödfiler som behövs för att köra programmet eller skriptet. Den körbara filen eller skript fil typerna som stöds finns i avsnittet [filtyper som stöds](#acceptablefiles) . |
   | **Typ** | Kontinuerlig igenkänning | [Webb jobbs typerna](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Skalning** | Flera instanser | Endast tillgängligt för kontinuerliga WebJobs. Bestämmer om programmet eller skriptet körs på alla instanser eller bara en instans. Alternativet att köra på flera instanser gäller inte för [pris nivåerna](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)kostnads fri eller delad. | 

4. Klicka på **OK**.

   Det nya webb jobbet visas på sidan **WebJobs** .

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. För att stoppa eller starta om ett kontinuerligt webbjobb, högerklickar du på webb jobbet i listan och klickar på **stoppa** eller **Starta**.

    ![Stoppa ett kontinuerligt webbjobb](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Skapa ett manuellt utlöst webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I [Azure Portal](https://portal.azure.com)går du till **App Service** sidan i din app service webbapp, API-app eller mobilapp.

2. Välj **WebJobs**.

   ![Välj webbjobb](./media/web-sites-create-web-jobs/select-webjobs.png)

2. På sidan **WebJobs** väljer du **Lägg till**.

    ![Webb jobbs sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd **Lägg till webb jobb** inställningar som anges i tabellen.

   ![Skärm bild som visar de inställningar som måste ställas in för att skapa ett manuellt utlöst webb jobb.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myTriggeredWebJob | Ett namn som är unikt inom en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken förutom "-" och "_".|
   | **Fil uppladdning** | ConsoleApp.zip | En *zip* -fil som innehåller din körbara fil eller skript fil samt alla stödfiler som behövs för att köra programmet eller skriptet. Den körbara filen eller skript fil typerna som stöds finns i avsnittet [filtyper som stöds](#acceptablefiles) . |
   | **Typ** | Utlöses | [Webb jobbs typerna](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Utlösare** | Manuell | |

4. Klicka på **OK**.

   Det nya webb jobbet visas på sidan **WebJobs** .

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Om du vill köra webb jobbet högerklickar du på namnet i listan och klickar på **Kör**.
   
    ![Kör webbjobb](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Skapa ett schemalagt webb jobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I [Azure Portal](https://portal.azure.com)går du till **App Service** sidan i din app service webbapp, API-app eller mobilapp.

2. Välj **WebJobs**.

   ![Välj webbjobb](./media/web-sites-create-web-jobs/select-webjobs.png)

2. På sidan **WebJobs** väljer du **Lägg till**.

   ![Webb jobbs sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd **Lägg till webb jobb** inställningar som anges i tabellen.

   ![Sidan Lägg till webb jobb](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myScheduledWebJob | Ett namn som är unikt inom en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken förutom "-" och "_". |
   | **Fil uppladdning** | ConsoleApp.zip | En *zip* -fil som innehåller din körbara fil eller skript fil samt alla stödfiler som behövs för att köra programmet eller skriptet. Den körbara filen eller skript fil typerna som stöds finns i avsnittet [filtyper som stöds](#acceptablefiles) . |
   | **Typ** | Utlöses | [Webb jobbs typerna](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Utlösare** | Schemalagd | Aktivera funktionen Always on för att schemalägga arbetet på ett tillförlitligt sätt. Always On är bara tillgängligt på pris nivåerna Basic, standard och Premium.|
   | **CRON-uttryck** | 0 0/20 * * * * | [Cron-uttryck](#ncrontab-expressions) beskrivs i följande avsnitt. |

4. Klicka på **OK**.

   Det nya webb jobbet visas på sidan **WebJobs** .

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB-uttryck

Du kan ange ett [NCRONTAB-uttryck](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) i portalen eller inkludera en `settings.job` fil i roten av din webb jobb *. zip* -fil, som i följande exempel:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Mer information finns i [Schemalägga ett utlöst webb jobb](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Visa jobb historiken

1. Välj det webbjobb som du vill visa historik för och välj sedan knappen **loggar** .
   
   ![Knappen loggar](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. På sidan **information om webb jobb** väljer du en tid för att visa information om en körning.
   
   ![Information om webb jobb](./media/web-sites-create-web-jobs/webjobdetails.png)

3. På sidan **jobb körnings information** väljer du **Växla utdata** för att se texten i logg innehållet.
   
    ![Körnings information för webb jobb](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Om du vill visa utmatnings texten i ett separat webbläsarfönster väljer du **Hämta**. Om du vill ladda ned själva texten högerklickar du på **Ladda ned** och använder webbläsarens alternativ för att spara filens innehåll.
   
5. Välj länken **WebJobs** -länk överst på sidan för att gå till en lista med WebJobs.

    ![Webb jobbets dynamiska adress](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista med WebJobs på Historik panelen](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Nästa steg

Azure WebJobs SDK kan användas med WebJobs för att förenkla många programmerings aktiviteter. Mer information finns i [Vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
