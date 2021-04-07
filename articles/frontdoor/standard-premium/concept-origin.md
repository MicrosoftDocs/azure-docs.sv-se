---
title: Original-och ursprungs grupp i Azures främre dörr standard/Premium
description: I den här artikeln beskrivs vilka ursprungs-och ursprungs grupper som finns i en Azure-konfiguration för front dörren.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100532"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Original-och ursprungs grupp i Azure frontend-standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Den här artikeln beskriver begrepp om hur distributionen av webb program fungerar med Azures frontend-standard/Premium. Du får också lära dig om vad en *Ursprunglig* och en *ursprungs grupp* finns i konfigurationen för standard/Premium i Azure-dörren.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Ursprung

Azure front dörr standard/Premium-ursprung syftar på värd namnet eller den offentliga IP-adressen för ditt program som hanterar dina klient förfrågningar. Azure front dörr standard/Premium stöder både Azure-och icke-Azure-resurser i en ursprungs grupp. Programmet kan också finnas i ditt lokala data Center eller med en annan moln leverantör. Ursprunget bör inte förväxlas med databas nivån eller lagrings nivån. Ursprung ska visas som den offentliga slut punkten för din program Server del. När du lägger till ett ursprung i en start grupp för Azures främre dörr standard/Premium måste du också lägga till följande information:

* **Typ av ursprung:** Den typ av resurs som du vill lägga till. Frontend-dörren stöder automatisk identifiering av programmets Server delar från App Service, moln tjänst eller lagring. Om du vill ha en annan resurs i Azure eller till och med en icke-Azure-Server del väljer du **anpassad värd**.

    >[!IMPORTANT]
    >Under konfigurationen verifierar API: erna inte om ursprunget inte är tillgängligt från den främre dörren. Se till att front dörren kan komma till ditt ursprung.

* **Namn på prenumeration och ursprungs värd:** Om du inte valde en **anpassad värd** för din server dels värd typ väljer du din server del genom att välja lämplig prenumeration och motsvarande Server dels värd namn.

* **Ursprungs värd huvud:** Värd huvud värde som skickas till Server delen för varje begäran. Mer information finns i [käll värd huvud](#hostheader).

* **Prioritet:** Tilldela prioriteter till ditt olika ursprung när du vill använda ett primärt ursprung för all trafik. Ange också säkerhets kopior om de primära eller säkerhets kopierings ursprungen inte är tillgängliga. Mer information finns i [prioritet](#priority).

* **Vikt:** Tilldela vikter till ditt olika ursprung för att distribuera trafik över en uppsättning ursprung, antingen jämnt eller enligt viktade koefficienter. Mer information finns i [vikter](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Ursprungsvärdadress

Begär Anden som vidarebefordras av Azures frontend-standard/Premium till ett ursprung innehåller ett värd huvud fält som används för att hämta mål resursen. Värdet för det här fältet kommer normalt från ursprungs-URI: n som har värd rubriken och-porten.

Till exempel har en begäran om `www.contoso.com` att ha värd rubriken `www.contoso.com` . Om du använder Azure Portal för att konfigurera ditt ursprung är standardvärdet för det här fältet värd namnet för Server delen. Om ditt ursprung är `contoso-westus.azurewebsites.net` i Azure Portal är det automatiskt ifyllda värdet för ursprungs värd huvudet `contoso-westus.azurewebsites.net` . Men om du använder Azure Resource Manager mallar eller någon annan metod utan att ange det här fältet, skickar front dörren det inkommande värd namnet som värde för värd huvudet. Om begäran har gjorts för `www.contoso.com` och ditt ursprung är `contoso-westus.azurewebsites.net` som har ett tomt huvud fält, anger klient dörren värd rubriken som `www.contoso.com` .

De flesta Server Server delar (Azure Web Apps, Blob Storage och Cloud Services) kräver att värd huvudet matchar Server delens domän. Dock använder klient dels värden som dirigerar till Server delen ett annat värdnamn, till exempel `www.contoso.net` .

Om ditt ursprung kräver värd rubriken för att matcha backend-värdnamnet, kontrollerar du att Server delens värd huvud innehåller Server delens värdnamn.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Konfigurera ursprungs värd huvudet för ursprunget

Så här konfigurerar du fältet **ursprungs värd huvud** för ett ursprung i avsnittet ursprungs grupp:

1. Öppna din frontend-resurs och välj ursprungs gruppen med det ursprung som ska konfigureras.

2. Lägg till ett ursprung om du inte har gjort det eller redigera ett befintligt.

3. Ange ett eget värde för fältet ursprungs värd huvud eller lämna det tomt. Värd namnet för den inkommande begäran kommer att användas som värd huvud värde.

## <a name="origin-group"></a>Ursprungs grupp

En ursprungs grupp i Azure-frontend standard/Premium refererar till den uppsättning ursprung som tar emot liknande trafik för deras program. Det är med andra ord en logisk gruppering av dina program instanser över hela världen som tar emot samma trafik och svarar med ett förväntat beteende. De här ursprungen kan distribueras i olika regioner eller inom samma region. Alla ursprung kan vara i aktivt/aktivt distributions läge eller vad som definieras som aktiv/passiv konfiguration.

En ursprungs grupp definierar hur ursprung ska utvärderas via hälso avsökningar. Den definierar också hur belastnings utjämning sker mellan dem.

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Azure frontend-standard/Premium skickar regelbundna HTTP/HTTPS-avsöknings begär anden till var och en av dina konfigurerade ursprung. Avsöknings förfrågningar fastställer närhet och hälsa för varje ursprung för att belastningsutjämna dina slut användar förfrågningar. Hälso avsöknings inställningar för en ursprungs grupp definierar hur vi avsöker hälso tillståndet för app-arbetsdelar. Följande inställningar är tillgängliga för belastnings Utjämnings konfiguration:

* **Sökväg**: den URL som används för avsöknings begär Anden för alla ursprung i gruppen ursprung. Om ett av dina ursprung till exempel är `contoso-westus.azurewebsites.net` och sökvägen ställs in på/PROBE/test.aspx, kommer front dörr miljöer, förutsatt att protokollet är http, skicka begär Anden om hälso avsökning till `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protokoll**: definierar om hälso avsöknings förfrågningar ska skickas från Front dörren till dina ursprung med http-eller HTTPS-protokoll.

* **Metod**: den http-metod som ska användas för att skicka hälso avsökningar. Alternativen är GET eller HEAD (standard).
    > [!NOTE]
    > För att minska belastningen och kostnaden på dina Server delar rekommenderar front dörren att använda HEAD-begäranden för hälso avsökningar.

* **Intervall (sekunder)**: definierar frekvensen för hälso avsökningar i dina ursprung, eller i de intervall där varje front dörr miljöer skickar en avsökning.

    >[!NOTE]
    >För snabbare redundans anger du intervallet till ett lägre värde. Ju lägre värde, desto högre hälso avsöknings volym som dina Server delar får. Exempel: om intervallet är inställt på 30 sekunder, med 100 front-dörr pop globalt, kommer varje server del att ta emot cirka 200 avsöknings begär Anden per minut.

Mer information finns i [hälso avsökningar](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Inställningar för belastnings utjämning

Inställningarna för belastnings utjämning för ursprungs gruppen definierar hur vi utvärderar hälso avsökningar. De här inställningarna avgör om ursprunget är felfritt eller dåligt. De kan också kontrol lera belastnings Utjämnings trafik mellan olika ursprung i gruppen ursprung. Följande inställningar är tillgängliga för belastnings Utjämnings konfiguration:

* **Exempel storlek:** Identifierar hur många prover av hälso avsökningar som vi behöver ta hänsyn till för att utvärdera ursprungs tillståndet.

* **Lyckad samplings storlek:** Definierar exempel storleken som tidigare nämnts, antalet lyckade prover som krävs för att anropa ursprunget felfritt. Anta till exempel att ett hälso avsöknings intervall på en front dörr är 30 sekunder, exempel storleken är 5 och att prov storleken är 3. Varje gång vi utvärderar hälso avsökningar för ditt ursprung tittar vi på de senaste fem exemplen över 150 sekunder (5 × 30). Det krävs minst tre lyckade avsökningar för att deklarera ursprunget som felfri.

* **Fördröjnings känslighet (extra svars tid):** Anger om du vill att Azure frontend-dörren standard/Premium ska skicka begäran till ursprung inom ett känslighets intervall för svars tider eller vidarebefordra begäran till den närmaste server delen.

Mer information finns i [metod för minsta latens baserad routning](#latency).

## <a name="routing-methods"></a>Routningsmetoder

Azure frontend-standarden/Premium stöder olika typer av metoder för trafik cirkulation för att fastställa hur din HTTP/HTTPS-trafik ska dirigeras till olika tjänst slut punkter. När klienten begär en front dörr, tillämpas den konfigurerade routningsmetod för att se till att begär Anden vidarebefordras till den bästa backend-instansen. 

Det finns fyra metoder för trafikroutning som är tillgängliga i Azures främre dörr standard/Premium:

* **[Svars tid](#latency):** Latens-baserad routning ser till att begär Anden skickas till de lägsta svars tiderna som accepteras inom ett känslighets intervall. I princip skickas dina användar förfrågningar till "närmsta" uppsättning Server delar i förhållande till nätverks fördröjningen.
* **[Prioritet](#priority):** Du kan tilldela prioriteter till dina Server delar när du vill konfigurera en primär server del för att betjäna all trafik. Den sekundära Server delen kan vara en säkerhets kopia om den primära server delen inte är tillgänglig.
* **[Viktat](#weighted):** Du kan tilldela vikter till dina Server delar när du vill distribuera trafik över en uppsättning Server delar. Om du vill fördela eller enligt vikt koefficienter jämnt.

Alla konfigurationer av standard/Premium för Azure-frontend omfattar övervakning av Server dels hälsa och automatisk omedelbar global redundans. Mer information finns i [övervakning av Server](concept-health-probes.md)del. Din front dörr kan fungera baserat på en enda routningsmetod. Beroende på dina program behov kan du också kombinera flera metoder för routning för att bygga en optimal crawltopologi.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Lägsta latens baserad trafik routning

Distribution av Server delar på två eller flera platser över hela världen kan förbättra svars tiden för dina program genom att dirigera trafik till målet som är "närmast" till dina slutanvändare. Standard metoden för trafikroutning för din frontend-konfiguration vidarebefordrar begär Anden från slutanvändarna till den närmaste server delen av den främre dörren som tog emot begäran. I kombination med anycast-arkitekturen i Azures frontend, ser den här metoden till att alla slutanvändare får bästa möjliga prestanda baserat på deras plats.

Server delen "närmast" är inte nödvändigt vis närmast som uppmätt av geografiskt avstånd. I stället fastställer front dörren de närmaste server ändarna genom att mäta nätverks fördröjningen.

Nedan visas det övergripande besluts flödet:

| Tillgängliga Server delar | Prioritet | Svars tids signal (baserad på hälso avsökning) | Lekar |
|-------------| ----------- | ----------- | ----------- |
| Välj först alla Server delar som är aktiverade och returnerade felfria (200 OK) för hälso avsökningen. Om det finns sex Server delar A, B, C, D, E och F, och mellan dem C, är inte felfria och E har inaktiverats. Listan över tillgängliga Server delar är A, B, D och F.  | Därefter väljs de högsta prioritets bakändarna bland de tillgängliga. Om Server delen A, B och D har prioritet 1 och Server del F har prioriteten 2. De valda Server delarna är A, B och D.| Välj server delar med latens intervall (minsta latens & fördröjning i MS angivet). Om Server del A är 15 MS, B är 30 MS och D är 60 MS bort från den främre dörr miljön där begäran landats, och fördröjnings känslighet är 30 MS, så består den lägsta latens poolen av Server del A och B, eftersom D är över 30 MS bort från den närmaste server delen som är en. | Till sist kommer front dörren att runda av trafiken mellan den slutliga valda poolen av Server delar i förhållandet mellan vikter som anges. Anta att om Server delen A har en vikt på 5 och Server del B har en vikt på 8, kommer trafiken att distribueras i förhållandet 5:8 mellan server delar A och B. |

>[!NOTE]
> Som standard är egenskapen svars känslighet inställd på 0 MS, det vill säga alltid vidarebefordra begäran till den snabbast tillgängliga Server delen.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioriterad trafik-routning

En organisation vill ofta tillhandahålla hög tillgänglighet för sina tjänster genom att distribuera fler än en säkerhets kopierings tjänst, om den primära servern går nedåt. I branschen kallas den här topologin även för aktiv/vänte läge eller aktiv/passiv distributions topologi. Med Traffic-metoden Traffic-routing kan Azure-kunder enkelt implementera det här mönstret för växling vid fel.

Din standard-front dörr innehåller en lista med samma prioritet som server delar. Som standard skickar front dörren enbart trafik till Server delens högsta prioritet (lägsta värdet för prioritet) som är den primära uppsättningen med Server delar. Om de primära server delarna inte är tillgängliga dirigerar front dörren trafiken till den sekundära uppsättningen Server delar (andra lägsta värdet för prioritet). Om både den primära och den sekundära Server delen inte är tillgänglig, går trafiken till den tredje osv. Server delens tillgänglighet baseras på konfigurerad status (aktive rad eller inaktive rad) och den kontinuerliga hälso statusen för Server delen som bestäms av hälso avsökningarna.

#### <a name="configuring-priority-for-backends"></a>Konfigurera prioritet för Server delar

Varje server del i din backend-pool för konfigurationen av front dörren har en egenskap som kallas Priority, som kan vara ett tal mellan 1 och 5. Med Azures front dörr konfigurerar du Server dels prioriteten uttryckligen med den här egenskapen för varje server del. Den här egenskapen är ett värde mellan 1 och 5. Lägre värden representerar en högre prioritet. Server delar kan dela prioritets värden.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Viktad metod för trafik dragning
Med metoden "viktad" för trafik dragning kan du distribuera trafik jämnt eller använda en fördefinierad viktning.

I den viktade metoden för trafik routning tilldelar du en vikt till varje server del i konfigurationen för den lokala server delen. Vikten är ett heltal mellan 1 och 1 000. Den här parametern använder en standard vikt på "50".

Med en lista över tillgängliga Server delar som har godtagbar svars tid kan trafiken distribueras med en Round Robin-mekanism med hjälp av förhållandet mellan vikter. Om svars tiden för svars tiden anges till 0 millisekunder, börjar den här egenskapen inte gälla om det finns två server delar med samma nätverks fördröjning. 

Den viktade metoden möjliggör vissa användbara scenarier:

* **Gradvis program uppgradering**: ger en procent andel av trafiken som ska vidarebefordras till en ny server del och ökar gradvis trafiken över tid för att ta den i parivärde med andra server delar.
* **Programmigrering till Azure**: skapa en backend-pool med både Azure och externa server delar. Justera vikten på Server delarna för att föredra de nya server delarna. Du kan gradvis konfigurera detta genom att låta de nya server delarna vara inaktiverade, och sedan tilldela dem de lägsta vikterna, så att det blir långsamt att öka det till nivåer där de tar störst trafik. Sedan inaktiverar du de mindre önskade Server delarna och tar bort dem från poolen.  
* **Cloud-burst för ytterligare kapacitet**: utöka snabbt en lokal distribution till molnet genom att placera den bakom en front dörr. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera fler server delar och ange vilken del av trafiken som går till varje server del.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md)
