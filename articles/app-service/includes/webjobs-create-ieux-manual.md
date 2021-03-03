---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5ef61502d0b2fccc92ca606992e965b45764baa0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746672"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Skapa ett manuellt utlöst webbjobb

1. Kom till [Azure Portal](https://portal.azure.com).
1. Gå till din **App Service** <abbr title="Din app-resurs kan vara en webbapp, en API-app eller en mobilapp.">App-resurs</abbr>.
1. Välj **WebJobs**.

    ![Välj webbjobb](../media/web-sites-create-web-jobs/select-webjobs.png)

2. På sidan **WebJobs** väljer du **Lägg till**.

   ![Webb jobbs sida](../media/web-sites-create-web-jobs/wjblade.png)

3. Använd **Lägg till webb jobb** inställningar som anges i tabellen.

    ![Skärm bild som visar de inställningar som måste ställas in för att skapa ett manuellt utlöst webb jobb.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Inställning      | Exempelvärde   | Beskrivning  |
    | ------------ | ----------------- | ------------ |
   | <abbr title="Ett namn som är unikt inom en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken förutom `-` och `_` .">Namn</abbr> | myTriggeredWebJob | 
    | <abbr title="En *zip* -fil som innehåller din körbara fil eller skript fil samt alla stödfiler som behövs för att köra programmet eller skriptet.">Fil uppladdning</abbr> | ConsoleApp.zip |
    | <abbr title="Typerna omfattar kontinuerlig, utlöst.">Typ</abbr> | Utlöses | 
    | <abbr title="Typer inklusive schemalagda eller manuella">Utlösare</a> | Manuell | |

4. Klicka på **OK**. 

   Det nya webb jobbet visas på sidan **WebJobs** .

   ![Lista över WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Om du vill köra ett manuellt webb jobb** högerklickar du på namnet i listan och klickar på **Kör**.
   
    ![Kör webbjobb](../media/web-sites-create-web-jobs/runondemand.png)

