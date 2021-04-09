---
title: Optimering av stor fil hämtning med Azure CDN
description: Lär dig hur stora fil hämtningar kan optimeras i Azure Content Delivery Network. Den här artikeln innehåller flera scenarier.
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
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: ed5768e89482d32bb140e9ba7064de2d20809892
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020729"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimering av stor fil hämtning med Azure CDN

Fil storlekar för innehåll som levereras via Internet fortsätter att växa på grund av förbättrade funktioner, förbättrad grafik och medie innehåll. Den här tillväxten drivs av många faktorer: bred bands inträngning, större billiga lagrings enheter, omfattande ökning av video med hög upplösning och Internet-anslutna enheter (IoT). En snabb och effektiv leverans metod för stora filer är viktigt för att säkerställa en smidig och roliga konsument upplevelse.

Leverans av stora filer har flera utmaningar. För det första kan den genomsnittliga tiden för att hämta en stor fil vara betydande eftersom program kanske inte laddar ned alla data sekventiellt. I vissa fall kan program ladda ned den sista delen av en fil före den första delen. Om bara en liten del av en fil begärs eller om en användare pausar en nedladdning kan nedladdningen inte utföras. Hämtningen kan också förskjutas tills efter att CDN (Content Delivery Network) hämtar hela filen från ursprungs servern. 

För det andra bestämmer svars tiden mellan en användares dator och filen den hastighet med vilken de kan visa innehåll. Dessutom påverkar överbelastnings-och kapacitets problem även data flöden. Större avstånd mellan servrar och användare skapar ytterligare möjligheter för paket förlust som ska ske, vilket minskar kvaliteten. Minskningen av kvalitet som orsakas av begränsat data flöde och ökad paket förlust kan öka vänte tiden för hämtning av en fil. 

Tredje, många stora filer levereras inte i sin helhet. Användare kan avbryta hämtningen halvvägs från och med bara de första minuterna i en lång MP4-video. Därför vill program-och medie leverans företag endast leverera den del av en fil som begärs. Effektiv distribution av de begärda delarna minskar den utgående trafiken från ursprungs servern. Effektiv distribution minskar också minnet och I/O-belastningen på ursprungs servern. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimera för leverans av stora filer med Azure CDN från Microsoft

**Azure CDN Standard från Microsoft** -slutpunkter levererar stora filer utan fil storlek. Ytterligare funktioner är aktiverade som standard för att göra leveransen av stora filer snabbare.

### <a name="object-chunking"></a>Objekt segment 

**Azure CDN Standard från Microsoft** använder en teknik som kallas objekt segment. När en stor fil begärs hämtar CDN mindre delar av filen från ursprunget. När CDN-POP-servern har tagit emot en filbegäran med full eller byte Range begär CDN-servern filen från ursprunget i segment om 8 MB. 

När segmentet har nått CDN-gränsen cachelagras det och betjänas direkt för användaren. CDN hämtar sedan nästa segment parallellt. Med den här för hämtningen ser du till att innehållet förblir ett segment framför användaren, vilket minskar svars tiden. Den här processen fortsätter tills hela filen har hämtats (om det begärs), alla byte-intervall är tillgängliga (om de begärs) eller om klienten avslutar anslutningen. 

Mer information om byte Range-begäran finns i [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras i CDN-cachen. Efterföljande begär Anden för fil-eller byte-intervallen hanteras från CDN-cachen. Om inte alla segment cachelagras i CDN används för hämtning för att begära segment från ursprunget. Den här optimeringen är beroende av möjligheten för ursprungs servern att stödja byte intervall begär Anden. om ursprungs servern inte stöder byte intervall begär Anden, kommer begär Anden att hämta data som är större än 8 MB att Miss Miss läge. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer
Det finns inga begränsningar för maximal fil storlek.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimera för leverans av stora filer med Azure CDN från Verizon

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -slutpunkter levererar stora filer utan fil storlek. Ytterligare funktioner är aktiverade som standard för att göra leveransen av stora filer snabbare.

### <a name="complete-cache-fill"></a>Slutför cache-fyllning

Med standard funktionen Slutför cache Fill kan CDN hämta en fil till cachen när en inledande begäran överges eller förloras. 

Fullständig cache-fyllning är mest användbart för stora till gångar. Användarna kan vanligt vis inte ladda ned dem från start till slut. De använder progressiv nedladdning. Standard beteendet tvingar Edge-servern att initiera en bakgrunds hämtning av till gången från ursprungs servern. Efteråt finns till gången i Edge-serverns lokala cacheminne. När det fullständiga objektet finns i cacheminnet, uppfyller Edge-servern byte-range-begäranden till CDN för det cachelagrade objektet.

Standard beteendet kan inaktive ras via regel motorn i **Azure CDN Premium från Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Snabb ifyllning av peer-cache

Standard funktionen för peer-cache-fyllning använder en sofistikerad patentskyddad algoritm. Den använder ytterligare Edge caching-servrar baserat på bandbredds-och mängd begär ande mått för att uppfylla klient begär Anden för stora, mycket populära objekt. Den här funktionen förhindrar en situation där ett stort antal extra förfrågningar skickas till en användares ursprungs Server. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

Funktioner för stor fil optimering för **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** är aktiverade som standard när du använder den allmänna optimerings typen för webb leverans. Det finns inga begränsningar för maximal fil storlek. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimera för leverans av stora filer med Azure CDN Standard från Akamai

**Azure CDN Standard från Akamai** profil slut punkter erbjuder en funktion som levererar stora filer effektivt till användare i hela världen i stor skala. Funktionen minskar fördröjningen eftersom belastningen på ursprungs servrarna minskar.

Den stora fil optimerings typen funktion aktiverar nätverks optimeringar och konfigurationer för att leverera stora filer snabbare och mer besvarade. Allmän webb leverans med **Azure CDN Standard från Akamai** -slutpunkter cachelagrar filer endast under 1,8 GB och kan tunnla (inte cache) filer upp till 150 GB. Med stor fil optimering cachelagras filer upp till 150 GB.

Optimering av stora filer är effektiv när vissa villkor är uppfyllda. Villkoren omfattar hur ursprungs servern fungerar och vilka storlekar och typer av filer som begärs. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurera en Akamai CDN-slutpunkt för att optimera leveransen av stora filer

Du kan konfigurera **Azure CDN Standard från Akamai** -slutpunkten för att optimera leveransen för stora filer via Azure Portal. Du kan också använda REST-API: erna eller klient-SDK: er för att göra detta. Följande steg visar processen via Azure Portal för en **Azure CDN Standard från Akamai** profil:

1. Om du vill lägga till en ny slut punkt väljer du **slut punkt** på sidan Akamai **CDN Profile** .

    ![Ny slut punkt](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Välj **stor fil hämtning** i list rutan **optimerad för** .

    ![Optimering av stora filer har valts](./media/cdn-large-file-optimization/cdn-large-file-select.png)


När du har skapat CDN-slutpunkten tillämpar den stora fil optimeringar för alla filer som matchar vissa villkor. I följande avsnitt beskrivs den här processen.

### <a name="object-chunking"></a>Objekt segment 

Optimering av stora filer med **Azure CDN Standard från Akamai** använder en teknik som kallas objekt segment. När en stor fil begärs hämtar CDN mindre delar av filen från ursprunget. När CDN-POP-servern tar emot en fil förfrågan med fullständig eller byte-Range, kontrollerar den om filtypen stöds för den här optimeringen. Den kontrollerar också om fil typen uppfyller fil storleks kraven. Om fil storleken är större än 10 MB begär CDN Edge-servern filen från ursprunget i segment om 2 MB. 

När segmentet har nått CDN-gränsen cachelagras det och betjänas direkt för användaren. CDN hämtar sedan nästa segment parallellt. Med den här för hämtningen ser du till att innehållet förblir ett segment framför användaren, vilket minskar svars tiden. Den här processen fortsätter tills hela filen har hämtats (om det begärs), alla byte-intervall är tillgängliga (om de begärs) eller om klienten avslutar anslutningen. 

Mer information om byte Range-begäran finns i [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras i CDN-cachen. Efterföljande begär Anden för fil-eller byte-intervallen hanteras från CDN-cachen. Om inte alla segment cachelagras i CDN används för hämtning för att begära segment från ursprunget. Den här optimeringen är beroende av möjligheten för ursprungs servern att stödja byte intervall begär Anden. om ursprungs servern inte stöder byte intervall begär Anden är den här optimeringen inte effektiv.

### <a name="caching"></a>Cachelagring
Optimering av stora filer använder olika standardvärden för cachelagring – förfallo tider från allmän webb leverans. Det skiljer sig mellan positiv cachelagring och negativ cachelagring baserat på HTTP-svars koder. Om ursprungs servern anger en förfallo tid via en Cache-Control-eller Expires-rubrik i svaret, följer CDN det värdet. När ursprunget inte anges och filen matchar typ-och storleks villkoren för den här optimerings typen använder CDN standardvärden för optimering av stora filer. Annars använder CDN standardvärden för allmän webb leverans.

| Cachelagring  | Allmän webb | Optimering av stora filer 
--- | --- | --- 
Cachelagring: positiv <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |1 dag  
Cachelagring: negativ <br> HTTP 204, 305, 404, <br> och 405 | Inget | 1 sekund 

### <a name="deal-with-origin-failure"></a>Hantera ursprungs problem

Längden på den ursprungliga läsnings tids gränsen ökar från två sekunder för allmän webb leverans till två minuter för den stora fil optimerings typen. Detta ökar de större fil storlekarna för att undvika en för tidig tids gräns anslutning.

När en anslutnings tid är slut försöker CDN ett antal gånger innan det skickar ett fel meddelande om "504-Gateway-timeout" till klienten. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

I följande tabell visas en uppsättning villkor som ska uppfyllas för stor fil optimering:

Villkor | Värden 
--- | --- 
Filtyper som stöds | 3G2, 3GP, ASF, AVI, bz2, DMG, exe, F4V, FLV, <br> GZ, HDP, ISO, JXR, M4V, MKV, MOV, MP4, <br> MPEG, MPG, MTS, pkg, QT, RM, SWF, tar, <br> tgz, WDP, webm, WEBP, WMA, WMV, zip  
Minsta fil storlek | 10 MB 
Maximal filstorlek | 150 GB 
Ursprungs Server egenskaper | Måste ha stöd för byte range-begäranden 

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Överväg följande ytterligare aspekter för optimerings typen:

- Segment processen genererar ytterligare begär anden till ursprungs servern. Den totala mängden data som levereras från ursprunget är dock mycket mindre. Chunking resulterar i bättre egenskaper för cachelagring i CDN.

- Minnet och I/O-belastningen minskas med ursprunget eftersom mindre delar av filen levereras.

- För segment som cachelagras i CDN finns det inga ytterligare begär anden till ursprunget förrän innehållet går ut eller tas bort från cachen.

- Användare kan göra intervall begär anden till CDN, som behandlas som vilken normal fil som helst. Optimering gäller endast om det är en giltig filtyp och byte-intervallet är mellan 10 MB och 150 GB. Om den genomsnittliga fil storleken som krävs är mindre än 10 MB använder du allmän webb leverans i stället.

