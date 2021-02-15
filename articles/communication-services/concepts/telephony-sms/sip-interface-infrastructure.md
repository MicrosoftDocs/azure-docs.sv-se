---
title: Infrastruktur krav för SIP-gränssnitt – Azure Communication Services
description: Bekanta dig med infrastruktur kraven för konfiguration av SIP-användargränssnittet i Azure Communication Services
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b258f2ef82e74073e3e4f1aa61b036d423c30300
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100422487"
---
# <a name="sip-interface-infrastructure-requirements"></a>Infrastruktur krav för SIP-gränssnitt 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
I den här artikeln beskrivs anslutnings information för infrastruktur, licensiering och sessioner (SBC) som du vill ha i åtanke när du planerar distributionen av SIP-gränssnittet.


## <a name="infrastructure-requirements"></a>Krav på infrastruktur
Infrastruktur kraven för de SBCs-, domän-och nätverks anslutnings krav som stöds för att distribuera SIP-gränssnittet visas i följande tabell:  

|Infrastruktur krav|Du behöver följande|
|:--- |:--- |
|Sessionens kant linje styrenhet (SBC)|En SBC som stöds. Mer information finns i [SBCs som stöds](#supported-session-border-controllers-sbcs).|
|Telefoni trunkar anslutna till SBC|En eller flera telefoni trunkar anslutna till SBC. På ena sidan ansluter SBC till Azure Communication Service via SIP-gränssnittet. SBC kan också ansluta till en tredjeparts telefoni entitet, till exempel PBXs, analoga telefoni kort och så vidare. Alla alternativ för PSTN-anslutning som är anslutna till SBC fungerar. (För konfigurering av PSTN-trunkar till SBC, se SBC-leverantörer eller trunk-leverantörer.)|
|Azure-prenumeration|En Azure-prenumeration som du använder för att skapa ACS-resurs och konfigurationen och anslutningen till SBC.|
|Åtkomsttoken för kommunikations tjänster|För att ringa samtal behöver du en giltig åtkomsttoken med `voip` scope. Se [åtkomsttoken](https://docs.microsoft.com/azure/communication-services/concepts/identity-model#access-tokens)|
|Offentlig IP-adress för SBC|En offentlig IP-adress som kan användas för att ansluta till SBC. Beroende på typen av SBC kan SBC använda NAT.|
|Fullständigt kvalificerat domän namn (FQDN) för SBC|Ett fullständigt domän namn för SBC, där domän delen i FQDN inte matchar registrerade domäner i din Microsoft 365-eller Office 365-organisation. Mer information finns i [SBC domän namn](#sbc-domain-names).|
|Offentlig DNS-post för SBC |En offentlig DNS-post som mappar SBC FQDN till den offentliga IP-adressen. |
|Offentligt betrott certifikat för SBC |Ett certifikat för SBC som ska användas för all kommunikation med SIP-gränssnittet. Mer information finns i [offentligt betrott certifikat för SBC](#public-trusted-certificate-for-the-sbc).|
|Brand Väggs-IP-adresser och portar för SIP-signaler och media |SBC kommunicerar med följande tjänster i molnet:<br/><br/>SIP-proxy som hanterar signalering<br/>Medie processor, som hanterar Media<br/><br/>Dessa två tjänster har separata IP-adresser i Microsoft Cloud, som beskrivs senare i det här dokumentet.


## <a name="sbc-domain-names"></a>SBC domän namn

Kunder utan Office 365 kan använda alla domän namn som de kan hämta ett offentligt certifikat för.

I följande tabell visas exempel på DNS-namn som registrerats för klienten, om namnet kan användas som ett fullständigt domän namn (FQDN) för SBC och exempel på giltiga FQDN-namn:

|DNS-namn|Kan användas för SBC FQDN|Exempel på FQDN-namn|
|:--- |:--- |:--- |
contoso.com|Ja|**Giltiga namn:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|Inga|Användning av *. onmicrosoft.com-domäner stöds inte för SBC-namn

Om du är en Office 365-kund måste SBC domän namnet inte matcha registrerade i domänerna för Office 365-klienten. Nedan visas ett exempel på en Office 365-och Azure Communication Service-samexistens:

|Domän registrerad i Office 365|Exempel på SBC FQDN i Teams|Exempel på SBC FQDN-namn i ACS|
|:--- |:--- |:--- |
**contoso.com** (domän på andra nivån)|**SBC.contoso.com** (namn på den andra nivån-domänen)|**SBC.ACS.contoso.com** (namn i domän på tredje nivån)<br/>**SBC.fabrikam.com** (alla namn inom en annan domän)|
|**O365.contoso.com** (domän på tredje nivån)|**SBC.O365.contoso.com** (namn i domän på tredje nivån)|**SBC.contoso.com** (namn på den andra nivån-domänen)<br/>**SBC.ACS.O365.contoso.com** (namn i den fjärde nivån domän)<br/>**SBC.fabrikam.com** (alla namn inom en annan domän)

SBC koppling fungerar på resurs nivån för kommunikations tjänster, vilket innebär att du kan koppla många SBCs till en resurs av en enskild kommunikations tjänst, men du kan inte länka en enskild SBC till fler än en kommunikations tjänst resurs. Unika SBC-FQDN krävs för länkning till olika resurser.

## <a name="public-trusted-certificate-for-the-sbc"></a>Offentligt betrott certifikat för SBC

Microsoft rekommenderar att du begär certifikatet för SBC genom att generera en begäran om certifikat signering (CSR). För detaljerade anvisningar om hur du skapar en kund service representant för en SBC, se de sammanlänknings instruktioner eller den dokumentation som tillhandahålls av dina SBC-leverantörer. 

  > [!NOTE]
  > De flesta certifikat utfärdare kräver att den privata nyckel storleken är minst 2048. Tänk på detta när du genererar CSR.

Certifikatet måste ha SBC FQDN som eget namn (CN) eller fältet Alternativt namn för certifikat mottagare (SAN). Certifikatet ska utfärdas direkt från en certifikat utfärdare, inte från en mellanliggande leverantör.

Alternativt stöder SIP-gränssnittet för kommunikations tjänster ett jokertecken i CN och/eller SAN och jokertecknet måste följa standard [RFC http över TLS](https://tools.ietf.org/html/rfc2818#section-3.1). 

Ett exempel skulle använda `\*.contoso.com` som skulle matcha SBC FQDN `sbc.contoso.com` , men inte matcha med `sbc.test.contoso.com` .

Certifikatet måste genereras av någon av följande rot certifikat utfärdare:

- AffirmTrust
- AddTrust extern CA-rot
- Baltimore CyberTrust-rot *
- Buypass
- CyberTrust
- Klass 3 offentlig primär certifikat utfärdare
- Comodo säker rot certifikat utfärdare
- Tyska Telekom 
- DigiCert global rot certifikat utfärdare
- DigiCert med hög säkerhet och EV-rot certifikat utfärdare
- Trust
- GlobalSign
- Go-Daddy
- Förtroende
- VeriSign, Inc. 
- SSL.com
- Starfield
- Symantec Enterprise Mobile root för Microsoft 
- SwissSign
- Thawte tidsstämplar CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (tyska telekom)
- QuoVadis

Microsoft arbetar med att lägga till ytterligare certifikat utfärdare baserat på kund förfrågningar. 

## <a name="sip-signaling-fqdns"></a>SIP-signalering: FQDN 

Anslutnings punkterna för SIP-gränssnittet för kommunikations tjänster är följande tre FQDN: er:

- **SIP.pstnhub.Microsoft.com** – globalt FQDN – måste provas först. När SBC skickar en begäran om att matcha det här namnet returnerar Microsoft Azure DNS-servrarna en IP-adress som pekar på det primära Azure-datacenter som är tilldelat SBC. Tilldelningen baseras på prestanda mått för data Center och geografiskt närhet till SBC. IP-adressen som returnerades motsvarar det primära fullständiga domän namnet.
- **SIP2.pstnhub.Microsoft.com** – sekundärt FQDN – geografiskt mappar till den andra prioritets regionen.
- **sip3.pstnhub.Microsoft.com** – tertiär FQDN – geografiskt mappar till den tredje prioritets regionen.

Att placera dessa tre FQDN: er måste vara i ordning för att:

- Ge optimal upplevelse (mindre inläst och närmast det SBC Data Center som har tilldelats genom att fråga det första FQDN).
- Tillhandahålla redundans när en anslutning från en SBC upprättas till ett Data Center som har ett tillfälligt problem. Mer information finns i [mekanismen för redundans](#failover-mechanism-for-sip-signaling) nedan.  

FQDN-namnen – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com och sip3.pstnhub.microsoft.com – kommer att matchas mot någon av följande IP-adresser:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Öppna brand Väggs portar för de här IP-adresserna för att tillåta inkommande och utgående trafik till och från adresserna för signalering. Om din brand vägg stöder DNS-namn `sip-all.pstnhub.microsoft.com` matchas FQDN till alla dessa IP-adresser. 

## <a name="sip-signaling-ports"></a>SIP-signalering: portar

Använd följande portar för SIP-gränssnittet för kommunikations tjänster:

|Trafik|Från|Om du vill|Källport|Målport|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|SIP-proxy|SBC|1024 – 65535|Definierad på SBC (för Office 365 GCC High/DoD port 5061 måste användas)|
SIP/TLS|SBC|SIP-proxy|Definierad i SBC|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Redundans för SIP-signaler

SBC gör en DNS-fråga för att lösa sip.pstnhub.microsoft.com. Baserat på SBC-platsen och prestanda måtten för data centret väljs det primära data centret. Om det primära data centret upplever ett problem kommer SBC att försöka utföra sip2.pstnhub.microsoft.com, som matchar det andra tilldelade data centret, och i sällsynta fall att data Center i två regioner inte är tillgängliga försöker SBC igen med det senaste fullständiga domän namnet (sip3.pstnhub.microsoft.com), vilket ger den tertiära Data Center-IP: en.

## <a name="media-traffic-ip-and-port-ranges"></a>Medie trafik: IP-och port intervall

Medie trafiken flödar till och från en separat tjänst som kallas medie processor. Vid tidpunkten för att publicera medie processorn för ACS kan du använda valfri Azure IP-adress. Ladda ned [den fullständiga listan med adresser](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Portintervall
Port intervallet för medie processorerna visas i följande tabell: 

|Trafik|Från|Om du vill|Källport|Målport|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Medie processor|SBC|3478-3481 och 49152 – 53247|Definierad i SBC|
|UDP/SRTP|SBC|Medie processor|Definierad i SBC|3478-3481 och 49152 – 53247|

  > [!NOTE]
  > Microsoft rekommenderar minst två portar per samtidiga anrop på SBC.


## <a name="media-traffic-media-processors-geography"></a>Medie trafik: geografi för medie processorer

Medie trafiken flödar via komponenter som kallas medie processorer. Medie processorer placeras i samma data Center som SIP-proxyservrar. Det finns även ytterligare medie processorer för att optimera medie flödet. Vi har till exempel ingen SIP-proxyvärd i Australien (SIP-flöden via Singapore eller Hongkong), men vi har medie processorn lokalt i Australien. Behovet av medie processorer lokalt styrs av svars tiden som vi upplever genom att skicka trafikavstånd, till exempel från Australien till Singapore eller Hongkong. Svars tiden i exemplet på trafik som flödar från Australien till Hongkong eller Singapore är acceptabel för att bevara bra samtals kvalitet för SIP-trafik, för real tids medie trafik.

Platser där både SIP-proxy och medie processor komponenter distribueras:
- USA (två i västra USA och östra USA-datacenter)
- Europa (Amsterdam och Dublin-datacenter)
- Asien (Singapore och Hong Kong Data Center)
- Australien (AU, öst och sydöstra Data Center)

Platser där endast medie processorer distribueras (SIP-flöden via det närmaste data centret som anges ovan):
- Japan (JP, östra och västra Data Center)


## <a name="media-traffic-codecs"></a>Medie trafik: codec

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Ben mellan SBC-och moln medie processorer eller Microsoft Teams-klienten.
Gäller både versaler och ej kringgåde fall.

Gränssnittet för direkt routning i benet mellan sessionens kant linje styrenhet och moln medie processor kan använda följande codecenheter:

- SILKE, G. 711, G. 722, G. 729

Du kan tvinga användningen av en speciell codec på sessionens kant linje styrenhet genom att utesluta oönskade codecs från erbjudandet.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Ben mellan ACS SDK-appen och moln medie processorn

I benet mellan moln medie processorn och ACS SDK-appen är SILKe eller G. 722 används. Valet av codec i den här delen baseras på Microsoft-algoritmer som tar hänsyn till flera parametrar. 

## <a name="supported-session-border-controllers-sbcs"></a>Gräns styrenheter för sessioner som stöds (SBCs)

Certifiering pågår. Kunder kan använda [Teams kant linjer för certifierade sessioner](https://docs.microsoft.com/MicrosoftTeams/direct-routing-border-controllers). 

## <a name="next-steps"></a>Nästa steg

### <a name="conceptual-documentation"></a>Konceptuell dokumentation

- [Telefoni koncept](./telephony-concept.md)
- [Telefonnummer typer i Azure Communication Services](./plan-solution.md)
- [Prissättning](../pricing.md)

### <a name="quickstarts"></a>Snabbstarter

- [Ring till telefon](../../quickstarts/voice-video-calling/pstn-call.md)
