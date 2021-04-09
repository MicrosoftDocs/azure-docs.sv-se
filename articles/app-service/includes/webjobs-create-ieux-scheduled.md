---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746685"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Skapa ett schemalagt webb jobb


1. Kom till [Azure Portal](https://portal.azure.com).
1. Gå till din **App Service** <abbr title="Din app-resurs kan vara en webbapp, en API-app eller en mobilapp.">App-resurs</abbr>.
1. Välj **WebJobs**.

   ![Välj webbjobb](../media/web-sites-create-web-jobs/select-webjobs.png)

1. På sidan **WebJobs** väljer du **Lägg till**.

    ![Webb jobbs sida](../media/web-sites-create-web-jobs/wjblade.png)

1. Använd **Lägg till webb jobb** inställningar som anges i tabellen.

    ![Sidan Lägg till webb jobb](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Inställning      | Exempelvärde   |
    | ------------ | ----------------- | 
    | <abbr title="Ett namn som är unikt inom en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken förutom `-` och `_` .">Name</a> | myScheduledWebJob |  |
    | <abbr title="En *zip* -fil som innehåller din körbara fil eller skript fil samt alla stödfiler som behövs för att köra programmet eller skriptet.">Fil uppladdning</abbr> | ConsoleApp.zip |
    | <abbr title="Typerna omfattar kontinuerlig, utlöst.">Typ</abbr> | Utlöses |
    | <abbr title="Aktivera funktionen Always on för att schemalägga arbetet på ett tillförlitligt sätt. Always On är bara tillgängligt på pris nivåerna Basic, standard och Premium.">Utlösare</a> | Schemalagd |
    | CRON-uttryck</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>Lär dig mer om CRON-uttryck</summary>
     <a name="#ncrontab-expressions"></a>
    
     Du kan ange ett [NCRONTAB-uttryck](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) i portalen eller inkludera en `settings.job` fil i roten av din webb jobb *. zip* -fil, som i följande exempel:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Mer information finns i [Schemalägga ett utlöst webb jobb](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Klicka på **OK**.

    Det nya webb jobbet visas på sidan **WebJobs** .
    
    ![Lista över WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)
