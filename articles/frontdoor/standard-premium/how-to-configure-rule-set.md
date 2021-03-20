---
title: 'Azure-front dörr: Konfigurera regel uppsättning för front dörren'
description: Den här artikeln innehåller rikt linjer för hur du konfigurerar en regel uppsättning.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715606"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Konfigurera en regel uppsättning med Azure frontend-standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Den här artikeln visar hur du skapar en regel uppsättning och din första regel uppsättning i Azure Portal. Du lär dig sedan att koppla regeln till en väg från sidan regel uppsättning eller från slut punkts hanteraren.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan konfigurera en regel uppsättning måste du först skapa en Azure-frontend-standard/Premium. Mer information finns i [snabb start: skapa en Azure front dörr standard/Premium-profil](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Konfigurera regel uppsättning i Azure Portal

1. I din profil för din front dörr väljer du **regel uppsättning som** finns under **Inställningar**. Välj **Lägg till** och ge den ett namn för regel uppsättningen.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Skärm bild av landnings sidan för regel uppsättning.":::
    
1. Välj **Lägg till regel** för att skapa din första regel. Ge den ett regel namn. Välj sedan **Lägg till villkor** eller **Lägg till åtgärd** för att definiera din regel. Du kan lägga till upp till 10 villkor och 5 åtgärder för en regel. I det här exemplet använder vi Server variabeln för att lägga till ett svars huvud 8Geo-land * för förfrågningar som innehåller *contoso* i URL: en.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Skärm bild av konfigurations sidan för regel uppsättning.":::
    
    > [!NOTE]
    > * Om du vill ta bort ett villkor eller en åtgärd från en regel kan du använda pappers korgen till höger om det aktuella villkoret eller åtgärden.
    > * Om du vill skapa en regel som gäller för all inkommande trafik ska du inte ange några villkor.
    > * Om du vill stoppa utvärderingen av återstående regler om en regel uppfylls, kontrollerar du **stoppa utvärderingen av återstående regel**. Om det här alternativet är markerat och alla återstående regler i regel uppsättningen inte körs oavsett om matchnings villkoren uppfylldes.  

1. Du kan fastställa prioritetsordningen för reglerna i din regel uppsättning genom att använda pilknapparna för att flytta reglerna högre eller lägre prioritet. Listan är i stigande ordning, så den viktigaste regeln visas först.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Skärm bild av regel uppsättnings prioritet." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. När du har skapat en eller flera regler väljer du **Spara** för att slutföra skapandet av regel uppsättningen.

1. Koppla nu regeln till en väg så att den börjar gälla. Du kan associera de regler som har ställts in via regel uppsättnings sidan, eller så kan du gå till slut punkts hanteraren för att skapa kopplingen.
 
    **Sida för regel uppsättning**: 
    
    1. Välj den regel uppsättning som ska associeras.
    
    1. Välj den  olänkade länken.
     

    1. På sidan **koppla en väg** väljer du sedan den slut punkt och det flöde som du vill koppla till regel uppsättningen. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Skärm bild av sidan Skapa en väg.":::    
        
    1. Välj *Nästa* för att ändra regel uppsättnings ordningar om det finns flera regel uppsättningar under den valda vägen. Regel uppsättningen körs uppifrån och ned. Du kan ändra order genom att välja regel uppsättningen och flytta den uppåt eller nedåt. Välj sedan *associera*.
    
        > [!Note]
        > Du kan bara associera en regel uppsättning med en enda väg på den här sidan. Om du vill associera en regel uppsättning med flera vägar använder du slut punkts hanteraren.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Skärm bild av regel uppsättnings order.":::
    
    1. Regel uppsättningen är nu associerad med en väg. Du kan titta på svars huvudet och se att geo-landet har lagts till.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Skärm bild av regel som är associerad med en väg.":::

   **Slut punkts hanterare**: 
    
    1. Gå till slut punkts hanteraren och välj den slut punkt som du vill koppla till regel uppsättningen.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Skärm bild av val av slut punkt i slut punkts hanteraren." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Välj *Redigera slut punkt*.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Skärm bild av val av redigera slut punkt i slut punkts hanteraren." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Välj vägen. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Skärm bild av att välja en väg.":::
    
    1. På sidan *Uppdatera väg* väljer du de regel uppsättningar som du vill koppla till vägen från List rutan i *regler*. Sedan kan du ändra ordningen genom att flytta regel inställningar upp och ned. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Skärm bild av sidan uppdatera en väg.":::
    
    1. Välj sedan *Uppdatera* eller *Lägg till* för att slutföra associationen.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Ta bort en regel uppsättning från din Azure-profil för front dörren

I föregående steg konfigurerade du och associerade en regel uppsättning till din väg. Om du inte längre vill att regel uppsättningen är kopplad till din frontend-dörr kan du ta bort regel uppsättningen genom att utföra följande steg:

1. Gå till **sidan regel uppsättning** under **Inställningar** för att ta bort kopplingen mellan regel uppsättningen och alla tillhör ande vägar.

1. Expandera vägen och markera de tre punkterna. Välj sedan *Redigera väg*.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Skärm bild av väg expanderad i regel uppsättning.":::

1. Gå till regler-avsnittet på sidan Route, Välj regel uppsättningen och välj sedan på knappen *ta bort* . 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Skärm bild av sidan uppdatera väg för att ta bort en regel uppsättning." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Välj *Uppdatera* och regel uppsättningen kommer att kopplas från vägen.

1. Upprepa steg 2-5 för att ta bort kopplingen till andra vägar som är associerade med den här regel uppsättningen tills du ser att vägens status visas som den är *kopplad* till.

1. För regel uppsättning som inte är *associerad* kan du ta bort regel uppsättningen genom att klicka på de tre punkterna till höger och välja *ta bort*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Skärm bild av hur du tar bort en regel uppsättning.":::

1. Regel uppsättningen har nu tagits bort.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till [säkerhets rubriker med regel uppsättning](how-to-add-security-headers.md).
