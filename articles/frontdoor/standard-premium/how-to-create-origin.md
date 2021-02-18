---
title: Konfigurera ett ursprung för Azure frontend standard/Premium (förhands granskning)
description: Den här artikeln visar hur du konfigurerar ett ursprung med slut punkts hanteraren.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 47134712bb953d9194bd943e228bae681877dcaa
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099445"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Konfigurera ett ursprung för Azure frontend standard/Premium (förhands granskning)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Den här artikeln visar hur du skapar en start punkt för Azure-dörrens standard-/Premium-ursprung i en befintlig ursprungs grupp. 

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa ett ursprung för Azures främre dörr standard/Premium måste du ha skapat minst en ursprungs grupp.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Skapa ett nytt ursprung för Azures främre dörr standard/Premium

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azures standard-/Premium-profil.

1. Välj **ursprungs grupp**. Välj sedan **+ Lägg** till för att skapa en ny ursprungs grupp.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Skärm bild av landnings sidan för start gruppen.":::

1. På sidan **Lägg till en ursprungs grupp** anger du ett unikt **namn** för den nya ursprungs gruppen.

1. Välj sedan **+ Lägg till ett ursprung** för att lägga till ett nytt ursprung i den här ursprungs gruppen. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Skärm bild av sidan Lägg till en ursprung.":::
  
    | Inställning | Värde |
    | --- | --- |
    | Namn | Ange ett unikt namn för det nya Azure-frontend-ursprunget. |   
    | Typ av ursprung | Den typ av resurs som du vill lägga till. Azure frontend-standarden/Premium stöder automatisk identifiering av appens ursprung från App Service, Cloud service eller Storage. Om du vill ha en annan resurs i Azure eller en icke-Azure-backend väljer du **anpassad värd**. |
    | Värdnamn  | Om du inte valde **anpassad värd** för typ av ursprungs värd väljer du Server delen genom att välja namnet på den ursprungliga värden i list rutan. |
    | Ursprungs värd huvud | Ange värd huvudets värde som skickas till Server delen för varje begäran. Mer information finns i [käll värd huvud](concept-origin.md#hostheader). |
    | HTTP-port | Ange värdet för den port som ursprungs platsen stöder för HTTP-protokoll. |
    | HTTPS-port | Ange värdet för den port som ursprungs platsen stöder för HTTPS-protokoll. |
    | Prioritet | Tilldela prioriteter till ditt olika ursprung när du vill använda ett primärt tjänst ursprung för all trafik. Ange också säkerhets kopior om den primära eller säkerhets kopierings ursprunget inte är tillgängligt. Mer information finns i [prioritet](concept-origin.md#priority). |
    | Vikt | Tilldela vikter till dina olika ursprung för att distribuera trafik över en uppsättning ursprung, antingen jämnt eller enligt viktade koefficienter. Mer information finns i [vikter](concept-origin.md#weighted). |
    | Status | Välj det här alternativet om du vill aktivera ursprung. |
    | Regel | Välj regel uppsättningar som ska tillämpas på den här vägen. Mer information om hur du konfigurerar regler finns i [Konfigurera en regel uppsättning för Azures frontend-dörr](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > Under konfigurationen validerar inte API: erna om ursprunget inte är tillgängligt från miljöer i front dörren. Se till att front dörren kan komma till ditt ursprung.

1. Välj **Lägg till** för att skapa det nya ursprunget. Det skapade ursprunget ska visas i ursprungs listan med gruppen
  
    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Skärm bild av sidan Lägg till en ursprung.":::

1. Välj **Lägg** till för att lägga till gruppen original i den aktuella slut punkten. Gruppen original bör visas på panelen för ursprungs gruppen.

## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort en ursprungs grupp när du inte längre behöver den, klickar du på **...** och väljer **ta bort** i list rutan.

:::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Skärm bild av hur du tar bort en ursprungs grupp.":::

Om du vill ta bort ett ursprung när du inte längre behöver det klickar du på **...** och väljer sedan **ta bort** i list rutan. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Skärm bild av hur du tar bort ett ursprung.":::

## <a name="next-steps"></a>Nästa steg

Mer information om anpassade domäner finns i [lägga till en anpassad domän](how-to-add-custom-domain.md) i din Azure frontend-standard/Premium-slutpunkt.
