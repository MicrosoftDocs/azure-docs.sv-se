---
title: Skala upp funktioner och kapaciteter
description: Lär dig hur du skalar upp en app i Azure App Service. Få mer processor, minne, disk utrymme och ytterligare funktioner.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: eb0ce5cfadbab247299cd3c22bf6ee618bf19731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582487"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Skala upp en app i Azure App Service

Den här artikeln visar hur du skalar din app i Azure App Service. Det finns två arbets flöden för skalning, skala upp och ut och den här artikeln beskriver arbets flödet skala upp.

* [Skala upp](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): få mer processor, minne, disk utrymme och ytterligare funktioner som dedikerade virtuella datorer (VM), anpassade domäner och certifikat, mellanlagring, automatisk skalning och mycket annat. Du skalar upp genom att ändra pris nivån för den App Service plan som appen tillhör.
* [Skala ut](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): öka antalet virtuella dator instanser som kör din app.
  Du kan skala ut till så många som 30 instanser, beroende på din pris nivå. [App Service-miljöer](environment/intro.md) i **isolerad** nivå ökar ytterligare antalet utskalning till 100 instanser. Mer information om att skala ut finns i [skala antalet instanser manuellt eller automatiskt](../azure-monitor/autoscale/autoscale-get-started.md). Där får du reda på hur du använder autoskalning, vilket är att skala antalet instanser automatiskt baserat på fördefinierade regler och scheman.

Skalnings inställningarna tar bara några sekunder att tillämpa och påverka alla appar i din [App Service plan](../app-service/overview-hosting-plans.md).
De kräver inte att du ändrar koden eller distribuerar om ditt program.

Information om priser och funktioner för enskilda App Services planer finns [App Service pris information](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Innan du växlar en App Service plan från den **kostnads fria** nivån måste du först ta bort [utgifts gränserna](https://azure.microsoft.com/pricing/spending-limits/) för din Azure-prenumeration. Om du vill visa eller ändra alternativ för Microsoft Azure App Service prenumerationen, se [Microsoft Azure prenumerationer][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Skala upp din pris nivå

> [!NOTE]
> För att skala upp till **PremiumV3** -nivån, se [Konfigurera PremiumV3-nivå för App Service](app-service-configure-premium-tier.md).
>

1. Öppna [Azure-portalen][portal] i webbläsaren.

1. På sidan App Service app går du till menyn till vänster och väljer **skala upp (App Service plan)**.
   
3. Välj din nivå och välj sedan **Använd**. Välj olika kategorier (till exempel **produktion**) och **Se även ytterligare alternativ** för att visa fler nivåer.
   
    ![Navigera för att skala upp din Azure-App.][ChooseWHP]

    När åtgärden har slutförts visas ett popup-meddelande med en grön kryss markering.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Skala relaterade resurser
Om din app är beroende av andra tjänster, till exempel Azure SQL Database eller Azure Storage, kan du skala upp resurserna separat. De här resurserna hanteras inte av App Service plan.

1. På sidan **Översikt** för din app väljer du länken **resurs grupp** .
   
    ![Skala upp Azure-appens relaterade resurser](./media/web-sites-scale/RGEssentialsLink.png)

2. I **sammanfattnings** delen av sidan **resurs grupp** väljer du en resurs som du vill skala. Följande skärm bild visar en SQL Database-resurs.
   
    ![Gå till resurs grupp sidan för att skala upp Azure-appen](./media/web-sites-scale/ResourceGroup.png)

    Information om hur du skalar upp den relaterade resursen finns i dokumentationen för den aktuella resurs typen. Om du till exempel vill skala upp en enda SQL Database, se [skala enkla databas resurser i Azure SQL Database](../azure-sql/database/single-database-scale.md). Om du vill skala upp en Azure Database for MySQL resurs, se [skala MySQL-resurser](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Jämför pris nivåer

Detaljerad information, till exempel VM-storlekar för varje pris nivå, finns [App Service pris information](https://azure.microsoft.com/pricing/details/app-service).

En tabell med begränsningar, kvoter och begränsningar för tjänster, samt funktioner som stöds på varje nivå finns i [App Service gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Fler resurser

[Skala instansantalet manuellt eller automatiskt](../azure-monitor/autoscale/autoscale-get-started.md)  
[Konfigurera PremiumV3-nivån för App Service](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png