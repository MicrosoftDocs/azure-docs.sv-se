---
title: Publicera en rot-eller Apex-domän till en befintlig front dörr – Azure Portal
description: Lär dig att publicera en rot-eller Apex-domän till en befintlig front dörr med hjälp av Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94646346"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Registrera en rotdomän eller basdomän på din Front Door
Azures front dörr använder CNAME-poster för att verifiera domän ägarskap för onboarding av anpassade domäner. Front dörren exponerar inte klient delens IP-adress som är kopplad till din profil för din front dörr. Så du kan inte mappa din Apex-domän till en IP-adress om avsikten är att publicera den till Azures front dörr.

DNS-protokollet förhindrar att CNAME-poster tilldelas i zonens Apex. Om din domän exempelvis är `contoso.com` , kan du skapa CNAME-poster för `somelabel.contoso.com` , men du kan inte skapa CNAME för `contoso.com` sig själv. Den här begränsningen utgör ett problem för program ägare som har belastningsutjämnade program bakom Azures front dörr. Eftersom du måste skapa en CNAME-post när du använder en profil för en frontend-dörr är det inte möjligt att peka på profilen för den främre dörren från zonens Apex.

Det här problemet kan lösas med hjälp av Ali Aset-poster i Azure DNS. Till skillnad från CNAME-poster skapas Ali-poster i zonens Apex. Program ägare kan använda den för att peka sin zon Apex-post till en profil för en front dörr som har offentliga slut punkter. Program ägare pekar på samma profil för front dörren som används för alla andra domäner i DNS-zonen. Till exempel `contoso.com` och `www.contoso.com` kan peka på samma profil för front dörren. 

Att mappa din Apex eller rot domän till din profil för din klient organisation kräver i princip CNAME-förenkling eller DNS-jaga. En mekanism där DNS-providern rekursivt löser CNAME-posten tills den når en IP-adress. Den här funktionen stöds av Azure DNS för slut punkter i front dörren. 

> [!NOTE]
> Det finns andra DNS-leverantörer som stöder CNAME-förenkling eller DNS-jaga, men Azures front dörr rekommenderar att du använder Azure DNS för sina kunder för att vara värd för sina domäner.

Du kan använda Azure Portal för att publicera en Apex-domän på din front dörr och Aktivera HTTPS på den genom att associera den med ett certifikat för TLS-avslutning. Apex-domäner kallas även rot-eller blott-domäner.

## <a name="create-an-alias-record-for-zone-apex"></a>Skapa en aliasresurspost för Zone Apex

1. Öppna **Azure DNS** konfiguration för den domän som ska registreras.

1. Skapa eller redigera posten för Zone Apex.

1. Välj post **typen** som *en* post och välj sedan *Ja* för **post uppsättning för alias**. **Aliasuppsättningen** måste anges till *Azure Resource*.

1. Välj den Azure-prenumeration där din profil för din klient organisation finns. Välj sedan resursen front dörr från List rutan **Azure-resurs** .

1. Välj **OK** för att skicka ändringarna.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Aliasresurspost för Zone Apex":::

1. Steget ovan skapar en zon Apex-post som pekar på din frontend-resurs och även en CNAME-Postmappning "afdverify" (exempel- `afdverify.contosonews.com` ) till den som ska användas för att registrera domänen på din profil för din front dörr.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Publicera den anpassade domänen på din front dörr

1. På fliken front dörr designer väljer du ikonen "+" i avsnittet klient dels värdar för att lägga till en ny anpassad domän.

1. Ange rot-eller Apex-domän namnet i fältet namn på anpassad värd, exempel `contosonews.com` .

1. När CNAME-mappningen från domänen till din front dörr är verifierad väljer du på **Lägg till** för att lägga till den anpassade domänen.

1. Klicka på **Spara** för att skicka ändringarna.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Meny för anpassad domän":::

## <a name="enable-https-on-your-custom-domain"></a>Aktivera HTTPS på din anpassade domän

1. Välj den anpassade domän som lades till och under avsnittet **anpassad https för domän** ändra status till **aktive rad**.

1. Välj  **certifikat hanterings typ** att *"Använd mitt eget certifikat"*.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="HTTPS-inställningar för anpassad domän":::    

   > [!WARNING]
   > Hanterings typen för hanterade certifikat från Front dörren stöds för närvarande inte för spets-eller rot domäner. Det enda alternativet som är tillgängligt för att aktivera HTTPS på en Apex eller rotdomän för front dörr använder ditt eget anpassade TLS/SSL-certifikat på Azure Key Vault.

1. Se till att du har konfigurerat rätt behörigheter för front dörren för att få åtkomst till ditt nyckel valv som anges i användar gränssnittet, innan du fortsätter till nästa steg.

1. Välj ett **Key Vault konto** från den aktuella prenumerationen och välj sedan rätt **hemlighet** och **hemlig version** som ska mappas till rätt certifikat.

1. Välj **Uppdatera** för att spara valet och välj sedan **Spara**.

1. Välj **Uppdatera** efter ett par minuter och välj sedan den anpassade domänen igen för att se förloppet för certifikat etableringen. 

> [!WARNING]
> Se till att du har skapat lämpliga routningsregler för din Apex-domän eller lagt till domänen i befintliga regler för routning.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
