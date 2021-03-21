---
title: Konfigurera Azure front dörr standard/Premium-slutpunkt med slut punkts hanteraren
description: Den här artikeln visar hur du konfigurerar en slut punkt med slut punkts hanteraren.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099672"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Konfigurera en Azure front dörr standard/Premium-slutpunkt (för hands version) med slut punkts hanteraren

> [!NOTE]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa **[Azures front dörrs dokument](../front-door-overview.md)**.

Den här artikeln visar hur du skapar en slut punkt för en befintlig Azure frontend-standard/Premium-profil med slut punkts hanteraren.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa en slut punkt för en Azure frontend-dörr med slut punkts hanteraren måste du skapa minst en profil för Azures frontend-dörr. Profilen måste ha minst en eller flera Azure front dörrs standard-/Premium-slutpunkter. Du kan använda flera profiler för att organisera dina Azure frontend-slutpunkters standard-/Premium-slutpunkter efter Internet domän, webb program eller andra kriterier. 

Information om hur du skapar en profil för Azure-frontend finns i [skapa en ny Azure-profil för front dörr standard/Premium](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Skapa en ny slut punkt för Azure front dörr standard/Premium

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azures standard-/Premium-profil.

1. Välj **slut punkts hanteraren**. Välj sedan **Lägg till en slut punkt** för att skapa en ny slut punkt.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Skärm bild av Lägg till en slut punkt via slut punkts hanteraren.":::

1. På sidan **Lägg till en slut punkt** anger du och väljer följande inställningar.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Skärm bild av sidan Lägg till en slut punkt.":::

    | Inställningar | Värde |
    | -------- | ----- |
    | Namn | Ange ett unikt namn för den nya standard-/Premium-slutpunkten för Azure frontend-dörren. Det här namnet används för att komma åt dina cachelagrade resurser på domänen `<endpointname>.az01.azurefd.net` |
    | Timeout för ursprungligt svar (SEK) | Ange ett timeout-värde i sekunder som Azure-frontend väntar innan den överväger anslutningen med sitt ursprung. |
    | Status | Markera kryss rutan för att aktivera den här slut punkten. |

## <a name="add-domains-origin-group-routes-and-security"></a>Lägg till domäner, ursprungs grupp, vägar och säkerhet

1. Konfigurera vägen genom att välja **Redigera slut punkt** vid slut punkten.

1. På sidan **Redigera slut punkt** väljer du **+ Lägg till** under domäner.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Skärm bild av sidan Välj domän på sidan Redigera slut punkt.":::

### <a name="add-domain"></a>Lägg till domän

1. På sidan **Lägg till domän** väljer du att associera en domän *från din Azure-profil för front dörren* eller *lägger till en ny domän*. Information om hur du skapar en helt ny domän finns i [skapa en ny anpassad domän för Azures främre dörr standard/Premium](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Skärm bild av sidan Lägg till en domän.":::

1. Välj **Lägg till** för att lägga till domänen i den aktuella slut punkten. Den valda domänen ska visas på domän panelen.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Skärm bild av domäner i vyn domän.":::

### <a name="add-origin-group"></a>Lägg till ursprungs grupp

1. Välj **Lägg till** i vyn ursprungs grupper. Sidan **Lägg till en ursprungs grupp** visas 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Skärm bild av sidan Lägg till en start grupp":::

1. I **namn** anger du ett unikt namn för den nya ursprungs gruppen

1. Välj **Lägg till ett ursprung** för att lägga till ett nytt ursprung i den aktuella gruppen.
 
#### <a name="health-probes"></a>Hälso avsökningar
Front dörren skickar regelbundna HTTP/HTTPS-avsöknings begär anden till var och en av dina ursprung. Avsöknings förfrågningar fastställer närhet och hälsa för varje ursprung för att belastningsutjämna dina slut användar förfrågningar. Hälso avsöknings inställningar för en ursprungs grupp definierar hur vi avsöker hälso tillståndet för appens ursprung. Följande inställningar är tillgängliga för belastnings Utjämnings konfiguration:

> [!WARNING]
> Eftersom front dörren har många gräns miljöer globalt kan hälso avsöknings volymen för ditt ursprung vara ganska hög från 25 förfrågningar varje minut till så hög som 1200 förfrågningar per minut, beroende på den angivna hälso avsöknings frekvensen. Med standard avsöknings frekvensen på 30 sekunder bör avsöknings volymen för ditt ursprung vara cirka 200 förfrågningar per minut.

* **Status**: Ange om du vill aktivera hälso avsökningen. Om du har ett enda ursprung i din ursprungs grupp kan du välja att inaktivera hälso avsökningar som minskar belastningen på din program Server del. Även om du har flera ursprung i gruppen men bara en av dem är i aktiverat läge, kan du inaktivera hälso avsökningar.
   
* **Sökväg**: den URL som används för avsöknings begär Anden för alla ursprung i den här ursprungs gruppen. Om ett av dina ursprung till exempel är contoso-westus.azurewebsites.net och sökvägen har angetts till/PROBE/test.aspx, kommer front dörr miljöer, förutsatt att protokollet är inställt på HTTP, att skicka hälso avsöknings begär anden till `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protokoll**: anger om hälso avsöknings förfrågningar ska skickas från Front dörren till ditt ursprung med http-eller HTTPS-protokoll.
   
* **Avsöknings metod**: http-metoden som används för att skicka hälso avsökningar. Alternativen är GET eller HEAD (standard).

    > [!NOTE]
    > För lägre belastning och kostnad på ditt ursprung rekommenderar front dörren att använda HEAD-begäranden för hälso avsökningar.

* **Intervall (i sekunder)**: definierar frekvensen för hälso avsökningar i ditt ursprung eller i de intervall där varje front dörr miljö skickar en avsökning.
   
    >[!NOTE]
    >För snabbare redundans anger du intervallet till ett lägre värde. Ju lägre värde, desto högre hälso avsöknings volym som ditt ursprung får. Exempel: om intervallet är inställt på 30 sekunder, med 100 front-dörr pop globalt, kommer varje server del att ta emot cirka 200 avsöknings begär Anden per minut.

#### <a name="load-balancing"></a>Belastningsutjämning
Inställningarna för belastnings utjämning för ursprungs gruppen definierar hur vi utvärderar hälso avsökningar. De här inställningarna avgör om Server delen är felfri eller ohälsosam. De kan också kontrol lera belastnings Utjämnings trafik mellan olika ursprung i gruppen ursprung. Följande inställningar är tillgängliga för belastnings Utjämnings konfiguration:

- **Exempel storlek**. Identifierar hur många prover av hälso avsökningar som vi behöver ta hänsyn till för att utvärdera ursprungs tillståndet.

- **Samplings storlek har slutförts**. Definierar exempel storleken som tidigare nämnts, antalet lyckade prover som krävs för att anropa ursprunget felfritt. Anta till exempel att ett hälso avsöknings intervall på en front dörr är 30 sekunder, exempel storleken är 5 och att prov storleken är 3. Varje gång vi utvärderar hälso avsökningar för ditt ursprung tittar vi på de senaste fem exemplen över 150 sekunder (5 × 30). Det krävs minst tre lyckade avsökningar för att deklarera Server delen som felfri.

- **Fördröjnings känslighet (extra latens)**. Anger om du vill att frontend-dörren ska skicka begäran till ursprung inom ett känslighets intervall för svars tider eller vidarebefordra begäran till närmaste Server del.

Välj **Lägg** till för att lägga till gruppen original i den aktuella slut punkten. Ursprungs gruppen ska visas i panelen för ursprungs grupper

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Skärm bild av ursprung i gruppen ursprung.":::

### <a name="add-route"></a>Lägg till väg

Välj **Lägg till** i vyn vägar visas sidan **Lägg till en väg** . Information om hur du associerar domänen och den ursprungliga gruppen finns i [skapa en ny Azure frontend-väg](how-to-configure-route.md)

### <a name="add-security"></a>Lägg till säkerhet

1. Väljer **Lägg till** i vyn säkerhet visas sidan **Lägg till en WAF-princip**
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Skärm bild av sidan Lägg till en WAF-princip.":::

1. **WAF-princip**: Välj en WAF-princip som du vill använda för den valda domänen i den här slut punkten. 
  
   Välj **Skapa nytt** för att skapa en helt ny WAF-princip.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Skärm bild av skapa en ny WAF-princip.":::
   
    **Namn**: Ange ett unikt namn för den nya WAF-principen. Du kan redigera den här principen med mer konfiguration från sidan brand vägg för webb program.

    **Domäner**: Välj den domän där WAF-principen ska tillämpas.

1. Välj knappen **Lägg till** . WAF-principen ska visas i säkerhets panelen

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Skärm bild av WAF-princip i vyn säkerhet.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort en slut punkt när den inte längre behövs väljer du **ta bort slut punkt** i slutet av raden slut punkt 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Skärm bild av hur du tar bort en slut punkt.":::

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om anpassade domäner fortsätter du att [lägga till en anpassad domän](how-to-add-custom-domain.md).
