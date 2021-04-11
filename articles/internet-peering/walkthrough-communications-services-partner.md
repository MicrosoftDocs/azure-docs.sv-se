---
title: Genom gång av Azure Internet peering för kommunikation tjänster
titleSuffix: Azure
description: Genom gång av Azure Internet peering för kommunikation tjänster
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499010"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Genom gång av Azure Internet peering för kommunikation tjänster

I det här avsnittet beskrivs de steg som en kommunikations tjänst leverantör måste följa för att upprätta en direkt koppling till Microsoft.

**Leverantörer av kommunikations tjänster:**  Leverantörer av kommunikations tjänster är organisationer som erbjuder kommunikations tjänster (kommunikation, meddelanden, konferenser osv.) och som vill integrera sin infrastruktur för kommunikations tjänster (SBC/SIP Gateway osv.)  med Azure Communication Services och Microsoft Teams. 

Leverantörer av Azure Internet-peering stöder kommunikations tjänster för att upprätta direkt koppling med Microsoft på någon av dess gräns webbplatser (pop-platser). Listan över alla offentliga kanter-webbplatser finns i [PeeringDB](https://www.peeringdb.com/net/694).

Azure Internet-peering ger hög tillförlitlighet och QoS (Quality of Service) som är aktiverat för kommunikations tjänster för att garantera högkvalitativa och högpresterande, högkvalitativa tjänster.

## <a name="technical-requirements"></a>Tekniska krav
De tekniska kraven för att upprätta direkt Interconnect för kommunikations tjänster är följande:
-   Motparten måste ange ett eget autonomt system nummer (ASN), som måste vara offentligt.
-   Motparten måste ha redundant samman koppling (PNI) på varje kopplings plats för att säkerställa lokal redundans.
-   Peer-datorn måste ha GEO-redundans för att säkerställa redundansväxling i händelse av plats fel i region/tunnelbane.
-   Peer-datorn måste ha BGP-sessionerna som aktiv-aktiv för att säkerställa hög tillgänglighet och snabbare konvergens och bör inte tillhandahållas som primär och säkerhets kopia.
-   Peer-datorn måste ha en 1:1-kvot för peer-peering-routrar för peering-kretsar och ingen hastighets begränsning tillämpas.
-   Peer-datorn måste tillhandahålla och annonsera sitt eget offentligt dirigerade IPv4-adress utrymme som används av peers kommunikations tjänst slut punkter (t. ex. SBC). 
-   Motparten måste ange information om vilken trafik klass och slut punkter som lagras i varje annonserat undernät. 
-   Motparten måste köra BGP över dubbelriktad vidarebefordring (BFD) för att under lätta väg konvergensen under andra vägar.
-   Alla prefix för infrastruktur för kommunikation registreras i Azure Portal och annonseras med grupp strängen 8075:8007.
-   Peer-kopplingen får inte avsluta peering på en enhet som kör en tillstånds känslig brand vägg. 
-   Microsoft konfigurerar länkarna för samman kopplings länkarna som en fördröjning (länk buntar) som standard, så att peer-datorer måste ha stöd för LACP (länk agg regerings kontroll protokoll) på sammanlänknings länkarna.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Upprätta direkt koppling med Microsoft för kommunikations tjänster.

Följ stegen nedan om du vill upprätta en direkt koppling med Azure Internet-peering:

**1. associera peer-offentligt ASN till Azure-prenumerationen:**

Ignorera det här steget om peer redan har associerat offentlig ASN till Azure-prenumeration.

[Koppla peer-ASN till Azure-prenumerationen med hjälp av portalen – Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

Nästa steg är att skapa en direkt peering-anslutning för peering-tjänsten.

> [!NOTE]
> När ASN-associationen har godkänts kan du skicka ett e-postmeddelande till peeringservices@microsoft.com ditt ASN-och prenumerations-ID för att koppla din prenumeration till kommunikations tjänster. 

**2. skapa direkt peering-anslutning för peering-tjänsten:**

Följ instruktionerna för att [skapa eller ändra en direkt peering med hjälp av portalen](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal)

Se till att det uppfyller kraven för hög tillgänglighet.

Kontrol lera att du väljer följande alternativ på sidan "skapa en peering":

Peering-typ:   **direkt**

Microsoft-nätverk:  **8075**

SKU:        **Premium kostnads fritt**


Under "anslutnings sidan för direkt peering" väljer du följande alternativ:

Sessions-provider:   **Microsoft**

Använd för peering-tjänster:   **aktiverade**

> [!NOTE] 
> Ignorera följande meddelande när du väljer för att aktivera peering-tjänster.
> *Aktivera inte om du inte har kontaktat dig peering@microsoft.com om att bli en Maps-Provider.*


  **2a. Använd befintlig direkt peering-anslutning för peering-tjänster**

Om du har en befintlig direkt peering som du vill använda för att stödja peering-tjänsten kan du aktivera på Azure Portal.
1.  Följ instruktionerna för att [konvertera en äldre direkt peering till Azure-resursen med hjälp av portalen](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal).
Vid behov kan du beställa ytterligare kretsar för att uppfylla krav på hög tillgänglighet.

2.  Följ stegen för att [Aktivera peering-tjänsten](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) på en direkt peering med hjälp av portalen.




**3. registrera dina prefix för optimerad routning**

För optimerad routning för infrastruktur prefix för kommunikations tjänster bör du registrera alla dina prefix med dina peering-kopplingar.
[Registrera Azure peering-tjänsten – Azure Portal | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

Den här nyckeln fylls i automatiskt för kommunikations tjänst partners, så partner behöver inte använda någon prefixlängd för att registrera sig. 

Se till att de prefix som registreras har meddelats via de direkta sammankopplingar som upprättats för regionen.


## <a name="faqs"></a>Vanliga frågor och svar:

**C.**  Jag har mindre undernät (</24) för mina kommunikations tjänster. Kan jag få de mindre undernäten också att dirigeras?

**En.**  Ja, Microsoft Azure peering-tjänsten har stöd för mindre prefix-routning också. Se till att du registrerar de mindre prefixen för Routning och att det är detsamma som meddelas över sammanlänkningarna.

**C.**  Vilka Microsoft-vägar kommer vi att få via dessa samanslutningar?

**En.** Microsoft tillkännager alla Microsofts offentliga tjänst-prefix över dessa samanslutna. Detta säkerställer inte bara kommunikation men andra moln tjänster är tillgängliga från samma koppling.

**C.**  Jag behöver ange en prefixlängd, hur många vägar Microsoft skulle lanseras?

**En.** Microsoft tillkännager ungefär 280 prefix på Internet och det kan öka med 10-15% i framtiden. En säker gräns på 400-500 kan därför vara bra att ange som "maximalt antal prefix"

**C.** Kommer Microsoft att annonsera om peer-prefixen till Internet?

**En.** Nej.

**C.** Finns det någon avgift för den här tjänsten?

**En.** Nej, men peer förväntas medföra att platser korsar Connect-kostnader.

**C.** Vilken är den minsta länk hastigheten för en samman koppling?

**En.** 10Gbps.

**C.** Är peer-kopplad till ett service avtal?

**En.** Ja, när användningen når 40% en omöknings process på 45 60day måste börja.

**C.** Vad är fördelen med den här tjänsten via den aktuella direkt peering-eller Express-vägen?

**En.** Avräknings fri och fullständig sökväg är optimerad för röst trafik via Microsoft WAN och konvergensen är justerad för under-sekund med BFD.

**C.** Hur tar det för att slutföra onboarding-processen?

**En.** Tiden kommer att variera beroende på antalet och platsen för platserna, och om peer migrerar befintliga privata peer-kopplingar eller om du etablerar nya kablar. Operatören bör planera för 3 + veckor.

**C.** Kan vi använda API: er för onboarding?

**En.** Det finns för närvarande inget API-stöd och konfigurationen måste utföras via webb portalen. 
