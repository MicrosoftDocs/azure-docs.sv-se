---
title: För inläsning av till gångar i en Azure CDN-slutpunkt | Microsoft Docs
description: Lär dig hur du förinstallerar cachelagrat innehåll i en Azure Content Delivery Network-slutpunkt. Den här funktionen är tillgänglig i vissa versioner av produkten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385055"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Läsa in tillgångar för en Azure CDN-slutpunkt i förväg
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Som standard cachelagras till gångar bara när de begärs. Eftersom gräns servrarna ännu inte har cachelagrat innehållet och behöver vidarebefordra begäran till ursprungs servern, kan den första begäran från varje region ta längre tid än efterföljande begär Anden. Undvik den här svars tiden genom att i förväg läsa in till gångar. Förutom att tillhandahålla en bättre kund upplevelse kan för hands inläsning av cachelagrade till gångar minska nätverks trafiken på ursprungs servern.

> [!NOTE]
> För inläsning av till gångar är användbart för stora händelser eller innehåll som blir tillgängliga för många användare samtidigt, till exempel en ny film version eller en program uppdatering.
> 
> 

Den här självstudien vägleder dig genom förladdningen av cachelagrat innehåll på alla Azure CDN Edge-noder.

## <a name="to-pre-load-assets"></a>Till gång till i förväg
1. I [Azure Portal](https://portal.azure.com)bläddrar du till CDN-profilen som innehåller den slut punkt som du vill läsa in i förväg. Profil fönstret öppnas.
    
2. Klicka på slut punkten i listan. Slut punkts fönstret öppnas.
3. I fönstret CDN-slutpunkt väljer du **load**.
   
    ![Fönstret CDN-slutpunkt](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **Inläsnings** fönstret öppnas.
   
    ![Inläsnings fönster för CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. För **innehålls Sök väg** anger du den fullständiga sökvägen till varje till gång som du vill läsa in (till exempel `/pictures/kitten.png` ).
   
   > [!TIP]
   > När du har påbörjat ange text visas text rutorna för **innehålls Sök vägen** så att du kan skapa en lista över flera till gångar. Om du vill ta bort till gångar från listan väljer du knappen med tre punkter (...) och väljer sedan **ta bort**.
   > 
   > Varje innehålls Sök väg måste vara en relativ URL som passar följande [reguljära uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Läs in en enda fil Sök väg: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Läs in en enda fil med frågesträng: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Eftersom varje till gång måste ha en egen sökväg finns det ingen jokertecken för för inläsnings till gångar.
   > 
   > 
   
    ![Knappen Läs in](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. När du är klar med att ange innehålls Sök vägar väljer du **load**.
   

> [!NOTE]
> Det finns en gräns på 10 belastnings begär Anden per minut per CDN-profil och 50 samtidiga sökvägar kan bearbetas samtidigt. Varje sökväg har en sökväg på högst 1024 tecken.
> 
> 

## <a name="see-also"></a>Se även
* [Rensa en Azure CDN-slutpunkt](cdn-purge-endpoint.md)
* [Referens för Azure CDN REST API: för inläsning av innehåll på en slut punkt](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Referens för Azure CDN REST API: Rensa innehåll från en slut punkt](/rest/api/cdn/cdn/endpoints/purgecontent)