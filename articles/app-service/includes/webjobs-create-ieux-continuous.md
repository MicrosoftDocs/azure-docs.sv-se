---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746675"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Skapa ett kontinuerligt webbjobb

1. Kom till [Azure Portal](https://portal.azure.com).
1. Gå till din **App Service** <abbr title="Din app-resurs kan vara en webbapp, en API-app eller en mobilapp.">App-resurs</abbr>.
1. Välj **WebJobs**.

   ![Välj webbjobb](../media/web-sites-create-web-jobs/select-webjobs.png)

1. På sidan **WebJobs** väljer du **Lägg till**.

    ![Webb jobbs sida](../media/web-sites-create-web-jobs/wjblade.png)

1. Använd **Lägg till webb jobb** inställningar som anges i tabellen.

   ![Skärm bild som visar de Lägg till webb jobbs inställningar som du behöver konfigurera.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Inställning      | Exempelvärde   | 
   | ------------ | ----------------- | 
   | <abbr title="Ett namn som är unikt inom en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken förutom `-` och `_` .">Name</abbr> | myContinuousWebJob | 
   | <abbr title=" En *zip* -fil som innehåller din körbara fil eller skript fil samt alla stödfiler som behövs för att köra programmet eller skriptet.">Fil uppladdning</abbr> | ConsoleApp.zip |
   | <abbr title="Typerna omfattar kontinuerlig, utlöst.">Typ</abbr> | Kontinuerlig igenkänning | 
   | <abbr title="Endast tillgängligt för kontinuerliga WebJobs. Bestämmer om programmet eller skriptet körs på alla instanser eller bara en instans. Alternativet att köra på flera instanser gäller inte för pris nivåerna kostnads fri eller delad.">Skala</abbr> | Flera instanser | 

1. Klicka på **OK**.

    Det nya webb jobbet visas på sidan **WebJobs** .

    ![Lista över WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **För att stoppa eller starta om** ett kontinuerligt webbjobb, högerklickar du på webb jobbet i listan och klickar på **stoppa** eller **Starta**.

   ![Stoppa ett kontinuerligt webbjobb](../media/web-sites-create-web-jobs/continuousstop.png)
