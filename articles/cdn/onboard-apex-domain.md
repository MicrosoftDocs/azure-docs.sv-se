---
title: Publicera en rot-eller Apex-domän till en befintlig Azure CDN slut punkt – Azure Portal
description: Lär dig att publicera en rot-eller Apex-domän till en befintlig Azure CDN slut punkt med hjälp av Azure Portal.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370513"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Publicera en rot-eller Apex-domän till en befintlig Azure CDN-slutpunkt

Azure CDN använder CNAME-poster för att verifiera domän ägarskap för onboarding av anpassade domäner. CDN exponerar inte klient delens IP-adress som är kopplad till din CDN-profil. Du kan inte mappa din Apex-domän till en IP-adress om avsikten är att publicera den till Azure CDN.

DNS-protokollet förhindrar att CNAME-poster tilldelas i zonens Apex. Om din domän exempelvis är `contoso.com` , kan du skapa CNAME-poster för `somelabel.contoso.com` , men du kan inte skapa CNAME för `contoso.com` sig själv. Den här begränsningen utgör ett problem för program ägare som har belastningsutjämnade program bakom Azure CDN. Eftersom användning av en CDN-profil kräver att en CNAME-post skapas går det inte att peka på CDN-profilen från zonens Apex.

Det här problemet kan lösas genom att använda Ali Asets poster i Azure DNS. Till skillnad från CNAME-poster skapas Ali-poster i zonens Apex. Program ägare kan använda den för att peka sin zon Apex-post till en CDN-profil som har offentliga slut punkter. Program ägare pekar på samma CDN-profil som används för alla andra domäner i DNS-zonen. Till exempel `contoso.com` och `www.contoso.com` kan peka på samma CDN-profil. 

Att mappa din Apex eller rot domän till din CDN-profil kräver CNAME-förenkling eller DNS-jaga. En mekanism där DNS-providern rekursivt löser CNAME-posten tills den når en IP-adress. Den här funktionen stöds av Azure DNS för CDN-slutpunkter. 

> [!NOTE]
> Det finns även andra DNS-leverantörer som stöder CNAME-förenkling eller DNS-jaga, men Azure CDN rekommenderar att du använder Azure DNS för sina kunder för att vara värd för sina domäner.

Du kan använda Azure Portal för att publicera en Apex-domän i CDN och Aktivera HTTPS på den genom att associera den med ett certifikat för TLS-avslutning. Apex-domäner kallas även rot-eller blott-domäner.

## <a name="create-an-alias-record-for-zone-apex"></a>Skapa en aliasresurspost för Zone Apex

1. Öppna **Azure DNS** konfiguration för den domän som ska registreras.

2. Välj **+ Postuppsättning**.

3. I **Lägg till post uppsättning** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ------|
    | Namn | Ange **@** . |
    | Typ | Välj **en**. |
    | Aliaspostuppsättning | Välj **Ja**. |
    | Aliastyp | Välj **Azure-resurs**. |
    | Välj en prenumeration | Välj din prenumeration. |
    | Azure-resurs | Välj CDN-slutpunkten. |

4. Ange ditt värde för **TTL**.

5. Välj **OK** för att skicka ändringarna.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Aliasresurspost för Zone Apex":::

6. I ovanstående steg skapas en zon Apex-post som pekar på din CDN-resurs. En CNAME- **cdnverify** används för att registrera domänen i CDN-profilen.
    1. Exempel **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Publicera den anpassade domänen i CDN

När du har registrerat din anpassade domän kan du lägga till den i din CDN-slutpunkt. 

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till den CDN-profil som innehåller den slutpunkt du vill mappa till en anpassad domän.
    
2. På sidan **CDN-profil** väljer du den CDN-slutpunkt som du vill associera med den anpassade domänen.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="Val av CDN-slutpunkt" border="true":::
    
3. Välj **+ anpassad domän**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Knappen Lägg till anpassad domän" border="true":::

4. I **Lägg till en anpassad domän**, fylls **slut punktens värdnamn** i i förväg och härleds från CDN-slutpunktens URL: **\<endpoint-hostname>** . azureedge.net. Det kan inte ändras.

5. För **anpassat värdnamn** anger du den anpassade rot-eller Apex-domänen som ska användas som käll domän för din CNAME-post. 
    1. Till exempel **contoso.com**. **Använd inte namnet på cdnverify-underdomänen**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Lägg till anpassad domän" border="true":::

6. Välj **Lägg till**.

   Azure verifierar att det finns en CNAME-post för det anpassade domännamnet som du har angett. Om CNAME är korrekt verifieras din anpassade domän. 

   Det kan ta lite tid innan inställningarna för den nya anpassade domänen sprids till alla CDN-gränsnoder: 
    - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
    - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
    - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter.   

## <a name="enable-https-on-your-custom-domain"></a>Aktivera HTTPS på din anpassade domän

Mer information om hur du aktiverar HTTPS på en anpassad domän för Azure CDN finns i [Självstudier: Konfigurera https på en Azure CDN anpassad domän](cdn-custom-ssl.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en CDN-slutpunkt](cdn-create-new-endpoint.md).
