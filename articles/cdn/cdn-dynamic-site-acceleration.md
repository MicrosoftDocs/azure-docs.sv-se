---
title: Acceleration av dynamisk webbplats via Azure CDN
description: Azure CDN stöder DSA-optimering (Dynamic site acceleration) för filer med dynamiskt innehåll.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008369"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Acceleration av dynamisk webbplats via Azure CDN

Med nedbrytning av sociala medier, elektroniskt handels-och webbanpassade webbplatser genereras en snabbt ökande procent andel av de innehåll som betjänas för slutanvändare i real tid. Användare förväntar sig en snabb, tillförlitlig och anpassad webb upplevelse, oberoende av webbläsare, plats, enhet eller nätverk. De flesta innovationer som gör dessa upplevelser så att du kan engagera dig även långsamma sid hämtningar och försätta kvaliteten på konsument upplevelsen i risk. 

Standard funktioner för Content Delivery Network (CDN) inkluderar möjligheten att cachelagra filer närmare slutanvändare för att påskynda leveransen av statiska filer. Men med dynamiska webb program går det inte att cachelagra innehållet i gräns platser eftersom servern genererar innehållet som svar på användar beteendet. Att påskynda leveransen av sådant innehåll är mer komplex än vanlig Edge-cachelagring och kräver en komplett lösning som finjusterar varje element längs hela data Sök vägen från start till leverans. Med Azure CDN optimering av dynamisk webbplats acceleration (DSA) är prestandan för webb sidor med dynamiskt innehåll Mätbart bättre.

**Azure CDN från Akamai** och **Azure CDN från Verizon** erbjuder båda DSA-optimering via menyn **optimerad för** när du skapar en slut punkt. Den dynamiska webbplats accelerationen från Microsoft erbjuds via [Azures frontend-tjänst](../frontdoor/front-door-overview.md).

> [!Important]
> För **Azure CDN från Akamai** -profiler, kan du ändra optimeringen för en CDN-slutpunkt när den har skapats.
>   
> För **Azure CDN från Verizon**-profiler kan du inte ändra optimeringen av en CDN-slutpunkt när den väl har skapats.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfiguration av CDN-slutpunkt för att påskynda leverans av dynamiska filer

Om du vill konfigurera en CDN-slutpunkt för att optimera leverans av dynamiska filer kan du antingen använda Azure Portal, REST-API: erna eller någon av klient-SDK: erna för att göra samma sak program mässigt. 

**Konfigurera en CDN-slutpunkt för DSA-optimering med hjälp av Azure Portal:**

1. På sidan **CDN-profil** väljer du **slut punkt**.

   ![Lägg till en ny CDN-slutpunkt](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Fönstret **Lägg till slutpunkt** visas.

2. Under **optimerad för** väljer du **dynamisk webbplats acceleration**.

    ![Skapa en ny CDN-slutpunkt med DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. För **avsöknings Sök väg** anger du en giltig sökväg till en fil.

    Avsöknings Sök vägen är en funktion som är speciell för DSA och en giltig sökväg krävs för att kunna skapas. DSA använder en liten *avsöknings Sök vägs* fil som placerats på ursprungs servern för att optimera konfigurationen för nätverks routning för CDN. För avsöknings Sök vägs filen kan du ladda ned och ladda upp exempel filen till din webbplats eller använda en befintlig till gång på ditt ursprung som är cirka 10 KB stor.

4. Ange andra obligatoriska slut punkts alternativ (mer information finns i [skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) och välj sedan **Lägg till**.

   När CDN-slutpunkten har skapats tillämpar den DSA-optimeringar för alla filer som matchar vissa villkor. 


**Konfigurera en befintlig slut punkt för DSA (endast Azure CDN från Akamai-profiler):**

1. På sidan **CDN-profil** väljer du den slut punkt som du vill ändra.

2. Välj **optimering** i det vänstra fönstret. 

   Sidan **optimering** visas.

3. Under **optimerad för** väljer du **dynamisk webbplats acceleration** och väljer sedan **Spara**.

> [!Note]
> DSA debiteras extra kostnader. Mer information finns i [Content Delivery Network prissättning](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimering med Azure CDN

Dynamisk webbplats acceleration på Azure CDN påskyndar leveransen av dynamiska till gångar med hjälp av följande tekniker:

-   [Väg optimering](#route-optimization)
-   [TCP-optimeringar](#tcp-optimizations)
-   [Objekt för hämtning (endast Azure CDN från Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Komprimering av adaptiv bild (endast Azure CDN från Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Väg optimering

Väg optimering är viktigt eftersom Internet är en dynamisk plats, där trafik och tillfälligt drift avbrott ständigt ändrar nätverk sto pol Ogin. Border Gateway Protocol (BGP) är Dirigerings protokollet för Internet, men det kan finnas snabbare vägar via servrar för mellanliggande närvaro (PoP). 

Med väg optimering väljer du den mest optimala sökvägen till ursprunget så att en plats ständigt är tillgänglig och dynamiskt innehåll levereras till slutanvändare via den snabbaste och mest pålitliga vägen som är möjlig. 

Akamai-nätverket använder tekniker för att samla in data i real tid och jämföra olika sökvägar via olika noder i Akamai-servern, samt standard-BGP-vägen över öppna Internet för att fastställa den snabbaste vägen mellan ursprunget och CDN-gränsen. Dessa tekniker undviker överbelastnings punkter på Internet och långa vägar. 

På samma sätt använder Verizon-nätverket en kombination av anycast-DNS, hög kapacitets support pop och hälso kontroller, för att fastställa de bästa gatewayerna till bästa möjliga väg data från klienten till ursprunget.
 
Som ett resultat levereras fullständigt dynamiskt och transaktionellt innehåll snabbare och mer tillförlitligt till slutanvändare, även när det inte går att cachelagra. 

### <a name="tcp-optimizations"></a>TCP-optimeringar

Transmission Control Protocol (TCP) är standarden för Internet Protocol Suite som används för att leverera information mellan program i ett IP-nätverk.  Som standard krävs flera backend-begäranden för att konfigurera en TCP-anslutning, samt begränsningar för att undvika överbelastningar i nätverket, vilket leder till ineffektivhet i skala. **Azure CDN från Akamai** hanterar det här problemet genom att optimera i tre områden: 

 - [Tar bort långsam TCP-start](#eliminating-tcp-slow-start)
 - [Använda beständiga anslutningar](#leveraging-persistent-connections)
 - [Justera TCP-paketets parametrar](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Tar bort långsam TCP-start

TCP *långsam start* är en algoritm för TCP-protokollet som förhindrar överbelastning av nätverket genom att begränsa mängden data som skickas över nätverket. Den börjar med små överbelastnings fönster storlekar mellan avsändare och mottagare tills det maximala antalet har nåtts eller så har paket förlusten identifierats.

 Både **Azure CDN från Akamai** och **Azure CDN från Verizon** -profiler eliminerar TCP långsam start med följande tre steg:

1. Övervakning av hälsa och bandbredd används för att mäta bandbredden för anslutningar mellan Edge PoP-servrar.
    
2. Mått delas mellan Edge PoP-servrar så att varje server är medveten om nätverks förhållandena och Server hälsan för de andra pop-servrarna runt dem.  
    
3. CDN Edge-servrarna gör antaganden om vissa överförings parametrar, till exempel vad den optimala fönster storleken ska vara när den kommunicerar med andra CDN Edge-servrar i närheten. Det här steget innebär att den inledande storleken på överbelastnings fönstret kan ökas om hälso tillståndet för anslutningen mellan CDN Edge-servrarna kan hantera data överföringar med högre paket.  

#### <a name="leveraging-persistent-connections"></a>Använda beständiga anslutningar

Med hjälp av en CDN ansluter färre unika datorer till din ursprungs Server direkt jämfört med användare som ansluter direkt till ditt ursprung. Azure CDN också pooler för användar förfrågningar för att upprätta färre anslutningar med ursprunget.

Som tidigare nämnts krävs flera hand skaknings begär Anden för att upprätta en TCP-anslutning. Beständiga anslutningar, som implementeras av `Keep-Alive` http-huvudet, återanvänd befintliga TCP-anslutningar för flera HTTP-förfrågningar för att spara svars tider och påskynda leveransen. 

**Azure CDN från Verizon** skickar även regelbundet Keep-Alive-paket över TCP-anslutningen för att förhindra att en öppen anslutning stängs.

#### <a name="tuning-tcp-packet-parameters"></a>Justera TCP-paketets parametrar

**Azure CDN från Akamai** justerar parametrarna som reglerar server-till-Server-anslutningar och minskar antalet långa avdrags fördröjningar som krävs för att hämta innehåll som är inbäddat på platsen med hjälp av följande tekniker:

- Öka det inledande överbelastnings fönstret så att fler paket kan skickas utan att vänta på en bekräftelse.
- Minskning av den första tids gränsen för omöverföring så att en förlust upptäcks och återöverföring sker snabbare.
- Minskning av den minsta och högsta tids gränsen för omsändning för att minska vänte tiden innan de antar att paket förlorades under överföringen.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objekt för hämtning (endast Azure CDN från Akamai)

De flesta webbplatser består av en HTML-sida som refererar till olika resurser, till exempel bilder och skript. När en klient begär en webb sida laddar webbläsaren först ned och parsar HTML-objektet och gör ytterligare förfrågningar till länkade till gångar som krävs för att läsa in sidan. 

*Prefetch* är en teknik för att hämta bilder och skript som bäddats in på HTML-sidan, medan HTML-koden hanteras i webbläsaren och innan webbläsaren även gör dessa objekt begär Anden. 

När för hämtnings alternativet är aktiverat vid den tidpunkt då CDN hanterar HTML-sidan till klientens webbläsare, parsar CDN HTML-filen och gör ytterligare förfrågningar för länkade resurser och lagrar dem i cacheminnet. När klienten gör begär Anden för länkade till gångar, har CDN Edge-servern redan de begärda objekten och kan hantera dem direkt utan att det går att göra en fördröjning i ursprunget. Den här optimeringen fördelar både cacheable och innehåll som inte är cachelagrat.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Komprimering av adaptiv bild (endast Azure CDN från Akamai)

Vissa enheter, särskilt mobila, upplever långsammare nätverks hastigheter från tid till gång. I dessa scenarier är det mer fördelaktigt för användaren att ta emot mindre bilder på webb sidan snabbare i stället för att vänta en lång tid för högupplösta bilder.

Den här funktionen övervakar automatiskt nätverks kvalitet och använder standard metoder för JPEG-komprimering när nätverks hastigheter är långsammare för att förbättra leverans tiden.

Komprimering av adaptiv bild | Fil namns tillägg  
--- | ---  
JPEG-komprimering | . jpg,. jpeg,. jpe,. jig,. jgig,. JGI

## <a name="caching"></a>Cachelagring

Med DSA är cachelagring inaktiverat som standard på CDN, även när ursprunget innehåller `Cache-Control` eller innehåller `Expires` rubriker i svaret. DSA används vanligt vis för dynamiska till gångar som inte ska cachelagras eftersom de är unika för varje klient. Cachelagring kan avbryta detta beteende.

Om du har en webbplats med en blandning av statiska och dynamiska till gångar, är det bäst att ta en hybrid metod för att få bästa möjliga prestanda. 

För **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** -profiler, kan du aktivera cachelagring för vissa DSA-slutpunkter med hjälp av [regler för cachelagring](cdn-caching-rules.md).

För att få åtkomst till regler för cachelagring:

1. På sidan **CDN-profil** under Inställningar väljer du **regler för cachelagring**.  
    
    ![Knappen CDN-cachelagringsregler](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Sidan för **cachelagring av regler** öppnas.

2. Skapa en global eller anpassad regel för cachelagring för att aktivera cachelagring för din DSA-slutpunkt. 

För **Azure CDN Premium från Verizon** -profiler aktiverar du cachelagring för vissa DSA-slutpunkter med hjälp av [regel motorn](./cdn-verizon-premium-rules-engine.md). Alla regler som skapas påverkar bara de slut punkter i din profil som är optimerade för DSA. 

För att komma åt regel motorn:
    
1. På sidan **CDN-profil** väljer du **Hantera**.  
    
    ![Knappen Hantera CDN-profil](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Hanterings portalen för CDN öppnas.

2. Välj **och** på hanterings portalen för CDN och välj sedan **regel motor**. 

    ![Regel motor för DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Du kan också använda två CDN-slutpunkter: en slut punkt som är optimerad med DSA för att leverera dynamiska till gångar och en annan slut punkt som är optimerad med en statisk optimerings typ, t. ex. allmän webb leverans, för att leverera till gångar Ändra webb sidans webb adresser så att de länkar direkt till till gången på den CDN-slutpunkt som du planerar att använda. 

Exempel: `mydynamic.azureedge.net/index.html` är en dynamisk sida och läses in från DSA-slutpunkten.  HTML-sidan hänvisar till flera statiska till gångar, till exempel JavaScript-bibliotek eller avbildningar som läses in från den statiska CDN-slutpunkten, till exempel `mystatic.azureedge.net/banner.jpg` och `mystatic.azureedge.net/scripts.js` .