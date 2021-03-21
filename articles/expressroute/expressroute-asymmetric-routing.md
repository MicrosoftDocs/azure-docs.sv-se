---
title: 'Azure-ExpressRoute: asymmetrisk routning'
description: Den här artikeln beskriver problem som kan uppstå i samband med asymmetrisk routning i ett nätverk som har flera länkar till ett mål.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97560516"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Asymmetrisk routning med flera nätverksvägar
Den här artikeln förklarar hur nätverks trafik kan ta olika vägar när flera vägar är tillgängliga mellan nätverks källa och mål.

Det finns två koncept du behöver känna till för att förstå asymmetrisk routning. Det första är resultatet av flera nätverks Sök vägar. Det andra är hur enheter, som en brand vägg, behåller tillstånd. Dessa typer av enheter kallas tillståndskänsliga enheter. När dessa två faktorer kombineras kan de skapa ett scenario där nätverks trafiken släpps av den tillstånds känsliga enheten.  Trafiken ignoreras eftersom den inte identifierade att trafiken härstammar från sig själv.

## <a name="multiple-network-paths"></a>Flera nätverksvägar
När ett företags nätverk bara har en länk till Internet via en Internet-leverantör, kommer all trafik till och från Internet att flytta samma sökväg. Det är vanligt att företag köper flera kretsar för att skapa redundanta sökvägar för att förbättra nätverkets drift tid. Med den här typen av konfiguration är det möjligt att trafiken går ut en länk till Internet och återgår via en annan länk. Det här scenariot kallas asymmetrisk routning. I asymmetrisk routning tar den returnerade nätverks trafiken en annan sökväg från det ursprungliga pågående flödet.

![Nätverk med flera sökvägar](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Även om asymmetrisk routning inträffar vanligt vis när du går till Internet. Det händer också när en kombination av flera sökvägar introduceras. Det första exemplet är om du har en Internet-sökväg och en privat sökväg som går till samma mål. Det andra exemplet är när du har flera privata sökvägar som också kommer till samma mål.

Varje router längs vägen mellan källan och målet beräknar den bästa vägen för att komma till målet. Routern bestämmer bästa möjliga sökväg baserat på två huvud faktorer:

* Routning mellan externa nätverk baseras på ett routningsprotokoll, Border Gateway Protocol (BGP). BGP tar annonser från grannar och kör dem via ett antal åtgärder för att avgöra den bästa vägen till det avsedda målet. Bästa vägen lagras i dess routningstabell.
* Längden på en nätmask som är associerad med en väg påverkar routningsvägarna. Om en router får flera annonser för samma IP-adress väljer routern sökvägen med den längre nät masken eftersom den betraktas som en mer speciell väg.

## <a name="stateful-devices"></a>Tillståndskänsliga enheter
Routrar tittar på ett pakets IP-huvud för att bestämma routningen. Vissa enheter leta djupare i paketet. De här enheterna tittar vanligt vis på Layer 4-Transmission Control Protocol (TCP) eller User Datagram Protocol (UDP) eller till och med Layer 7-huvuden (program skikt). Dessa typer av enheter är antingen säkerhetsenheter eller enheter för bandbreddsoptimering. 

En brandvägg är ett vanligt exempel på en tillståndskänslig enhet. En brand vägg tillåter eller avvisar paket som passerar genom sina gränssnitt baserat på olika kriterier. Dessa kriterier inkluderar, men är inte begränsade till protokoll, TCP/UDP-port och URL-huvuden. Den här nivån av paket inspektion kan medföra tung processor belastning på enheten. 

För att förbättra prestanda inspekterar brandväggen det första paketet i ett flöde. Om det tillåter att paketet passerar genom dess gränssnitt, behålls flödes informationen i status tabellen. Alla framgångs paket som är relaterade till det här flödet tillåts sedan baserat på den första bestämningen. Ett paket som är en del av ett befintligt flöde kan komma in i den brand vägg som det inte ursprungligen kom från. Eftersom den inte har någon tidigare tillståndsinformation om det ursprungliga flödet, släpper brand väggen paketet.

## <a name="asymmetric-routing-with-expressroute"></a>Asymmetrisk routning med ExpressRoute
När du ansluter till Microsoft via Azure ExpressRoute, ändras nätverket så här:

* Du har flera länkar till Microsoft. En länk är din befintliga Internet anslutning och den andra är via din ExpressRoute-anslutning. En viss trafik som är avsedd för Microsoft kan gå via Internet anslutningen, men återgå över din ExpressRoute-anslutning. Samma kan också hända när trafik går över ExpressRoute, men återgår över Internet vägen.
* Du har tagit emot mer information om IP-adresser från ExpressRoute-kretsen. Så när trafik från nätverket går till Microsoft för tjänster som erbjuds via ExpressRoute, kommer routrarna alltid att föredra ExpressRoute-anslutningen.

För att förstå hur dessa två ändringar har i ett nätverk ska vi ta hänsyn till några scenarier. Som exempel har du en krets till Internet och du använder alla Microsoft-tjänster via Internet. Trafiken från nätverket till och från Microsoft passerar samma Internet länk och passerar genom en brand vägg. Brand väggen registrerar flödet när det ser det första paketet. Varje pågående paket för den konversationen tillåts eftersom flödet finns i tillstånds tabellen.

![Asymmetrisk routning med ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Du kan sedan hämta en ExpressRoute-krets för att förbruka tjänster som erbjuds av Microsoft via ExpressRoute. Alla andra tjänster från Microsoft förbrukas via Internet. Du distribuerar en separat brand vägg vid din gräns som är ansluten till ExpressRoute-anslutningen. Microsoft annonserar mer specifika prefix till ditt nätverk via ExpressRoute för vissa tjänster. Din routningsinfrastruktur väljer ExpressRoute som primär väg för dessa prefix. 

Om du inte annonserar dina offentliga IP-adresser till Microsoft via ExpressRoute. Microsoft kommer att kommunicera med dina offentliga IP-adresser via Internet. Trafik som skickas från nätverket till Microsoft använder ExpressRoute-anslutningen, men retur trafiken från Microsoft använder sedan Internet Sök vägen. När brand väggen på din gräns ser ett svars paket för ett flöde som det inte känner till, kommer de att släppa dessa paket.

Om du väljer att annonsera samma Network Address Translation-pool (NAT) för ExpressRoute och för Internet. Du ser liknande problem med klienter i nätverket på privata IP-adresser. Begär Anden om tjänster som Windows Update kommer att gå via Internet eftersom IP-adresser för dessa tjänster inte annonseras via ExpressRoute. Men retur trafiken kommer tillbaka via ExpressRoute. Eftersom Microsoft tog emot en IP-adress med samma nätmask från Internet och ExpressRoute, är den önskade sökvägen alltid ExpressRoute. Om en brand vägg eller en annan tillstånds känslig enhet på din nätverks kant inte har en ExpressRoute-anslutning utan föregående information om ett flöde, tas dessa paket bort.

## <a name="asymmetric-routing-solutions"></a>Lösningar för asymmetrisk routning
Det finns två tillgängliga alternativ för att lösa problemet med asymmetrisk routning. Det första är via Routning och det andra är genom att använda en källroutning NAT (SNAT).

### <a name="routing"></a>Routning
Se till att dina offentliga IP-adresser annonseras till lämpliga Wide Area Network (WAN) länkar. Om du till exempel vill använda Internet för autentiseringstrafik och ExpressRoute för din e-posttrafik. Annonsera inte dina Active Directory Federation Services (AD FS) (AD FS) offentliga IP-adresser via ExpressRoute. Se också till att du inte exponerar din lokala AD FS-server för IP-adresser som routern tar emot via ExpressRoute. Routning som tas emot via ExpressRoute är mer specifik och gör ExpressRoute till förstahandsvalet för autentiseringstrafik till Microsoft. Om du inte är medveten om hur routningen görs i nätverket kan problem med asymmetrisk routning uppstå.

Om du vill använda ExpressRoute för autentisering kontrollerar du att du annonserar AD FS offentliga IP-adresser över ExpressRoute utan NAT. När den har kon figurer ATS på det här sättet går trafiken som härstammar från Microsoft till din lokala AD FS-server över ExpressRoute. Retur trafiken från nätverket som går till Microsoft använder ExpressRoute eftersom det är den önskade vägen via Internet.

### <a name="source-based-nat"></a>Källbaserad NAT
Ett annat sätt att lösa problemet med asymmetrisk routning är att använda SNAT. Du kan till exempel välja att inte annonsera den offentliga IP-adressen för en lokal Simple Mail Transfer Protocol (SMTP)-Server över ExpressRoute. I stället ska du använda Internet för den här typen av kommunikation. En begäran som kommer från Microsoft och som går till din lokala SMTP-server går igenom Internet. Du använder SNAT för att skicka den inkommande begäran till en intern IP-adress. Retur trafiken från SMTP-servern skickas till gräns brand väggen (som du använder för NAT) i stället för via ExpressRoute. Resultatet blir att RETUR trafiken tar Internet vägen.

![Nätverkskonfiguration för källbaserad NAT](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Identifiering av asymmetrisk routning
Traceroute är det bästa sättet att kontrollera att nätverkstrafik skickas via rätt väg. Om du förväntar dig att trafik från din lokala SMTP-server till Microsoft ska ta Internet vägen, kommer den förväntade traceroute från SMTP-servern att Microsoft 365. Resultatet verifierar att trafiken verkligen lämnar ditt nätverk mot Internet och inte mot ExpressRoute.

