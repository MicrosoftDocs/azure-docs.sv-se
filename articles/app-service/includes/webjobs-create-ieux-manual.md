---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 40e9e2b0e7437566a132f2402d3bed01b284d11a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081094"
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
    
    | Inställning      | Exempelvärde   | 
    | ------------ | ----------------- | 
   | <abbr title="Ett namn som är unikt inom en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken förutom `-` och `_` .">Name</abbr> | myTriggeredWebJob | 
    | <abbr title="En *zip* -fil som innehåller din körbara fil eller skript fil samt alla stödfiler som behövs för att köra programmet eller skriptet.">Fil uppladdning</abbr> | ConsoleApp.zip |
    | <abbr title="Typerna omfattar kontinuerlig, utlöst.">Typ</abbr> | Utlöses | 
    | <abbr title="Typer inklusive schemalagda eller manuella">Utlösare</a> | Manuell | 

4. Klicka på **OK**. 

   Det nya webb jobbet visas på sidan **WebJobs** .

   ![Lista över WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Om du vill köra ett manuellt webb jobb** högerklickar du på namnet i listan och klickar på **Kör**.
   
    ![Kör webbjobb](../media/web-sites-create-web-jobs/runondemand.png)

