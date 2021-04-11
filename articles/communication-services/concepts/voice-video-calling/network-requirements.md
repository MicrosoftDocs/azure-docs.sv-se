---
title: Förbered din organisations nätverk för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om nätverks kraven för Azure Communication Services röst-och video samtal
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22bf8af89771e57621a0d8851ca31bb750b7cda2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108357"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Se till att medier med hög kvalitet i Azure Communication Services

Det här dokumentet ger en översikt över faktorer och bästa praxis som bör övervägas när du skapar högkvalitativ multimedia kommunikations upplevelser med Azure Communication Services.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Faktorer som påverkar medie kvaliteten och tillförlitligheten

Det finns många olika faktorer som bidrar till Azure Communication Services real tids medier (ljud-, video-och program delning) kvalitet. Här ingår nätverks kvalitet, bandbredd, brand vägg, värd och enhets konfiguration.


### <a name="network-quality"></a>Nätverks kvalitet

Kvaliteten på real tids medier över IP påverkas avsevärt av den underliggande nätverks anslutningens kvalitet, men särskilt med mängden:
* **Svars tid**. Det här är den tid det tar att hämta ett IP-paket från punkt A till punkt B i nätverket. Den här nätverks spridningen bestäms av det fysiska avståndet mellan de två punkterna och eventuella ytterligare kostnader som uppstår av de enheter som trafiken flödar genom. Svars tiden mäts som enkelriktad eller rund resa.
* **Paket förlust**. En procent andel av paketen som förloras under en viss tids period. Paket förlust påverkar direkt ljud kvaliteten – från små, enskilda förlorade paket som har nästan ingen påverkan, till högpresterande burst-förluster som gör att ljud klipps ned.
* **Mottagna Darr i mellan paket eller bara Darr**. Detta är den genomsnittliga förändringen i fördröjning mellan efterföljande paket. Azure Communication Services kan anpassas till vissa nivåer av Darr genom buffring. Det är bara när skakningarna överskrider den buffert som en deltagare kommer att uppmärksamma sina effekter på.

### <a name="network-bandwidth"></a>Nätverksbandbredd

Kontrol lera att nätverket är konfigurerat för att stödja den bandbredd som krävs av samtidiga Azure Communication Services-mediafiler och andra affärs program. Det är viktigt att testa nätverks Sök vägen från slut punkt till slut punkt för bandbredds Flask halsar i distributionen av din lösning för multimedia kommunikations tjänster.

Nedan visas bandbredds kraven för Java scripts SDK: er:

|Bandbredd|Scenarier|
|:--|:--|
|40 kbps|Anrop mellan peer-to-peer-ljud|
|500 kbps|Uppringning mellan peer-to-peer-ljud och skärm delning|
|500 kbps|Video om peer-to-peer-kvalitet som anropar 360p på 30FPS|
|1,2 Mbit/s|Video om peer-to-peer HD-kvalitet som anropas med en lösning på HD 720p på 30FPS|
|500 kbps|Grupp video som anropar 360p på 30FPS|
|1,2 Mbit/s|HD-gruppvideo som ringer med lösning på HD 720p på 30FPS| 

Nedan visas bandbredds kraven för inbyggda Android-och iOS-SDK: er:

|Bandbredd|Scenarier|
|:--|:--|
|30 kbps|Anrop mellan peer-to-peer-ljud |
|130 kbps|Uppringning mellan peer-to-peer-ljud och skärm delning|
|500 kbps|Video om peer-to-peer-kvalitet som anropar 360p på 30FPS|
|1,2 Mbit/s|Video om peer-to-peer HD-kvalitet som anropas med en lösning på HD 720p på 30FPS|
|1,5 Mbit/s|Video om peer-to-peer HD-kvalitet som anropas med en lösning på HD 1080p på 30FPS |
|500kbps/1Mbps|Grupp video samtal|
|1Mbps/2 Mbit|HD-gruppvideo som anropar (540p-videor på 1080p-skärmen)|

### <a name="firewalls-configuration"></a>Brand Väggs konfiguration

Azure Communication Services-anslutningar kräver Internet anslutning till vissa portar och IP-adresser för att leverera multimedie upplevelser med hög kvalitet. Azure Communication Services kan fortfarande fungera utan åtkomst till dessa portar och IP-adresser. Den bästa upplevelsen tillhandahålls dock när de rekommenderade portarna och IP-intervallen är öppna.

| Kategori | IP-intervall eller FQDN | Portar | 
| :-- | :-- | :-- |
| Medie trafik | [Intervall med IP-adresser för offentliga Azure-moln](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 till 3481, TCP-portar 443 |
| Signalering, telemetri, registrering| *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Nätverks optimering

Följande uppgifter är valfria och krävs inte för att distribuera Azure Communication Services. Använd den här vägledningen för att optimera nätverket och Azures kommunikations tjänsternas prestanda eller om du vet att du har vissa nätverks begränsningar.
Du kanske vill optimera ytterligare om:
* Azure Communication Services körs långsamt (kanske du har inte tillräckligt med bandbredd)
* Anrop håller på att släppas (kan bero på en brand vägg eller proxy-blockerare)
* Anrop har statiska och avskurna eller röster som robotar (kan vara Darr eller paket förlust)

| Nätverks optimerings aktivitet | Information |
| :-- | :-- |
| Planera ditt nätverk | I den här dokumentationen hittar du minimi kraven för ditt nätverk för samtal. Se exempel på [team för att planera nätverket](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) |
| Extern namn matchning | Se till att alla datorer som kör SDK: er för Azure Communications Services kan lösa externa DNS-frågor för att identifiera de tjänster som tillhandahålls av Azure Communication servicers och att brand väggarna inte hindrar åtkomst. Se till att SDK: er kan matcha adresser *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io  |
| Underhåll beständighets för session | Kontrol lera att brand väggen inte ändrar de mappade NAT-adresserna (Network Address Translation) eller portarna för UDP
Verifiera storleken på NAT-poolen | Verifiera den Network Address Translation (NAT) pool som krävs för användar anslutning. När flera användare och enheter får åtkomst till Azure Communication Services med hjälp av [NAT (Network Address Translation) eller Pat (port Address Translation)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365)kontrollerar du att de dolda enheterna bakom varje offentligt DIRIGERAd IP-adress inte överskrider det antal som stöds. Se till att lämpliga offentliga IP-adresser tilldelas till NAT-poolerna för att förhindra port överbelastning. Port överbelastning bidrar till interna användare och enheter kan inte ansluta till Azure Communication Services |
| Vägledning för intrångs identifiering och förebyggande av skydd | Om din miljö har ett [intrångs identifierings](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) -eller skydds system (ID/IP-adresser) distribuerade för ett extra säkerhets lager för utgående anslutningar, Tillåt alla Azure Communication Services-URL: er |
| Konfigurera VPN för delad-tunnel | Vi rekommenderar att du anger en alternativ sökväg för team trafik som kringgår det virtuella privata nätverket (VPN), vanligt vis kallat [VPN för delad tunnel](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing). Delade tunnlar innebär att trafik för Azure Communications Services inte går via VPN, utan går i stället direkt till Azure. Om du kringgår ditt VPN får du en positiv inverkan på medie kvaliteten och det minskar belastningen från VPN-enheterna och organisationens nätverk. Om du vill implementera en VPN-anslutning med delad tunnel arbetar du med din VPN-leverantör. Andra orsaker till varför vi rekommenderar att du hoppar över VPN: <ul><li> VPN-nätverk är vanligt vis inte utformade eller konfigurerade för att stödja real tids medier.</li><li> VPN kanske även inte stöder UDP (vilket krävs för Azure Communication Services)</li><li>VPN ger också ett extra krypterings lager ovanpå medie trafik som redan är krypterad.</li><li>Anslutning till Azure Communication Services kanske inte är effektiv på grund av hår trafik genom en VPN-enhet.</li></ul>|
| Implementera QoS | [Använd tjänst kvalitet (QoS)](https://docs.microsoft.com/microsoftteams/qos-in-teams) för att konfigurera paket prioritering. Detta ger bättre samtals kvalitet och hjälper dig att övervaka och felsöka samtals kvalitet. QoS bör implementeras på alla segment i ett hanterat nätverk. Även om ett nätverk har etablerats korrekt för bandbredd, ger QoS en risk minskning i händelse av oförutsedda nätverks händelser. Med QoS prioriteras röst trafiken så att dessa oväntade händelser inte påverkar kvaliteten negativt. | 
| Optimera WiFi | På samma sätt som VPN är WiFi-nätverk inte nödvändigt vis utformade eller konfigurerade för att stödja real tids medier. Att planera för eller optimera ett WiFi-nätverk som stöder Azure Communication Services är ett viktigt övervägande för en högkvalitativ distribution. Tänk på följande faktorer: <ul><li>Implementera QoS-eller WiFi-multimediet (WMM) för att säkerställa att medie trafiken prioriteras på rätt sätt i dina WiFi-nätverk.</li><li>Planera och optimera WiFi-banden och placering av åtkomst punkter. 2,4 GHz-intervallet kan ge en lämplig upplevelse beroende på åtkomst punktens placering, men åtkomst punkter påverkas ofta av andra konsument enheter som arbetar inom intervallet. 5 GHz-intervallet är bättre lämpat för real tids medier på grund av dess täta intervall, men det krävs fler åtkomst punkter för att få tillräckligt med täckning. Slut punkter måste också stödja intervallet och konfigureras för att utnyttja dessa band.</li><li>Om du använder WiFi-nätverk med dubbla band kan du överväga att implementera band styrning. Band styrning är en teknik som implementeras av WiFi-leverantörer för att påverka klienter med dubbla band som använder 5 GHz-intervallet.</li><li>När åtkomst punkter i samma kanal är för nära tillsammans kan de orsaka att signalen överlappar och oavsiktligt konkurrerar, vilket resulterar i en försämrad användar upplevelse. Se till att åtkomst punkter bredvid varandra finns på kanaler som inte överlappar varandra.</li></ul> Varje trådlös leverantör har egna rekommendationer för att distribuera den trådlösa lösningen. Kontakta din WiFi-leverantör om du vill ha mer information.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>Operativ system och webbläsare (för JavaScript SDK: er)

Azure Communication Services röst-/video-SDK: er har stöd för vissa operativ system och webbläsare.
Lär dig mer om de operativ system och webbläsare som de anropande SDK: erna stöder i den [anropande konceptuella dokumentationen](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

## <a name="next-steps"></a>Nästa steg

Följande dokument kan vara intressanta för dig:

- Lär dig mer om att [anropa bibliotek](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- Lär dig mer om [klient-server arkitektur](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- Lär dig mer om [topologier för anrops flöden](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)
