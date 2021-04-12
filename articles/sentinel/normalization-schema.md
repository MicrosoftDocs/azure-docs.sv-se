---
title: Schema referens för Azure Sentinel-datanormalisering | Microsoft Docs
description: I den här artikeln visas schemat för Azure Sentinel data normalisering.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806943"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Schema referens för Azure Sentinel-datanormalisering

## <a name="terminology"></a>Terminologi

Följande terminologi används i Azure Sentinel-scheman:

| Period | Definition |
| ---- | ---------- |
| Rapporterings enhet | Systemet skickar posterna till Azure Sentinel. Det kanske inte är ämnes systemet för posten. |
| Post | En enhet med data som skickas från rapporterings enheten. Detta kallas ofta "logg", "händelse" eller "varning", men behöver inte nödvändigt vis vara något av dem. |
|

## <a name="data-types-and-formats"></a>Data typer och format

Värdena ska normaliseras baserat på rikt linjerna nedan. Detta är obligatoriskt för normaliserade fält och rekommenderas för andra fält. 

| Datatyp | Fysisk typ | Format och värde |
| --------- | ------------- | ---------------- |
| **Datum/tid** | Beroende på inmatnings metoden använder funktionen i fallande prioritet:<ul><li>Log Analytics inbyggd datetime-typ</li><li>Ett heltals fält med Log Analytics datetime numerisk representation</li><li>Ett sträng fält med Log Analytics datetime numerisk representation</li></ul> | Log Analytics datetime-representation. <br></br>Log Analytics datum & tid representationen liknar en annan typ än UNIX-tidsrepresentation. Läs de här rikt linjerna för konvertering. <br></br>Datumet & tiden ska vara tidszon justerad. |
| **MAC-adress** | Sträng | Colon-Hexadecimal notation |
| **IP-adress** | IP-adress | Schemat har inga separata IPv4-och IPv6-adresser. Alla IP-adresser kan innehålla antingen en IPv4-eller IPv6-adress:<ul><li>IPv4 i en punkt-Decimal form</li><li>IPv6 i 8 hextets-notation, som tillåter de kort former som beskrivs här.</li></ul> |
| **Användare** | Sträng | Följande tre användar fält är tillgängliga:<ul><li>Användarnamn</li><li>Användarens UPN</li><li>Användar domän</li></ul> |
| **Användar-ID** | Sträng | Följande 2 användar-ID: n stöds för närvarande:<ul><li>Användarens SID</li><li>Azure Active Directory-ID</li></ul> |
| **Enhet** | Sträng | Följande tre enhets-och värd kolumner stöds:<ul><li>ID</li><li>Name</li><li>Fullständigt kvalificerat domän namn (FQDN)</li></ul> |
| **Land** | Sträng | En sträng som använder ISO 3166-1 enligt denna prioritet:<ul><li>Alfa – 2 koder (t. ex. oss för USA)</li><li>Alfa-3 koder (t. ex. USA för USA)</li><li>Kort namn</li></ul> |
| **Region** | Sträng | Landets underordnade avdelnings namn med hjälp av ISO 3166-2 |
| **City** | Sträng | |
| **Long** | Double | ISO 6709-koordinat representation (signerad decimal) |
| **Latitud** | Double | ISO 6709-koordinat representation (signerad decimal) |
| **Hash-algoritm** | Sträng | Följande 4 hash-kolumner stöds:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Filtyp** | Sträng | Typ av filtyp:<ul><li>Anknytning</li><li>Klass</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Tabell schema för nätverks sessioner

Nedan visas schemat för tabellen nätverks sessioner, versions 1.0.0

| Fältnamn | Värdetyp | Exempel | Beskrivning | Associerade OSSEM-entiteter |
|-|-|-|-|-|
| Typ | Sträng | Trafik | Typ av händelse som samlas in | Händelse |
| EventSubType | Sträng | Autentisering | Ytterligare beskrivning av typ om tillämpligt | Händelse |
| EventCount | Integer  | 10 | Antalet händelser som sammanställts, om tillämpligt. | Händelse |
| EventEndTime | Datum/tid | Se "data typer" | Tiden då händelsen avslutades | Händelse |
| EventMessage | sträng |  åtkomst nekad | Ett allmänt meddelande eller en beskrivning, antingen inkluderad i eller genererad från posten | Händelse |
| DvcIpAddr | IP-adress |  23.21.23.34 | IP-adressen för enheten som genererar posten | Anordningar<br>IP-adress |
| DvcMacAddr | Sträng | 06:10:9f: EB: 8F: 14 | MAC-adressen för nätverks gränssnittet för den rapporterings enhet som händelsen skickades från. | Anordningar<br>Mac |
| DvcHostname | Enhets namn (sträng) | syslogserver1.contoso.com | Enhets namnet på enheten som genererar meddelandet. | Enhet |
| EventProduct | Sträng | OfficeSharepoint | Produkten som genererar händelsen. | Händelse |
| EventProductVersion | sträng | 9.0 |  Den version av produkten som genererar händelsen. | Händelse |
| EventResourceId | Enhets-ID (sträng) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Resurs-ID för enheten som genererar meddelandet. | Händelse |
| EventReportUrl | Sträng | https://192.168.1.1/repoerts/ae3-56.htm | En länk till den fullständiga rapporten som skapats av rapporterings enheten | Händelse |
| EventVendor | Sträng | Microsoft | Leverantören av produkten som genererar händelsen. | Händelse |
| EventResult | Multivärde: lyckad, partiell, misslyckad, [tom] (sträng) | Klart | Resultatet som rapporteras för aktiviteten. Tomt värde om det inte är tillämpligt. | Händelse |
| EventResultDetails | Sträng | Fel lösen ord | Orsak eller Detaljer för resultatet som rapporteras i EventResult | Händelse |
| EventSchemaVersion | Verkligen | 0.1 | Schema version för Azure Sentinel. För närvarande 0,1. | Händelse |
| EventSeverity | Sträng | Låg | Om aktiviteten som rapporteras har en säkerhets påverkan anger allvarlighets graden för påverkan. | Händelse |
| EventOriginalUid | Sträng | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | Post-ID: t från rapporterings enheten. | Händelse |
| EventStartTime | Datum/tid | Se "data typer" | Tiden då händelsen angavs | Händelse |
| TimeGenerated | Datum/tid | Se "data typer" | Tiden då händelsen inträffade, som rapporteras av rapport källan. | Anpassat fält |
| EventTimeIngested | Datum/tid | Se "data typer" | Tiden då händelsen skrevs till Azure Sentinel. Kommer att läggas till av Azure Sentinel. | Händelse |
| EventUid | GUID (sträng) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Unik identifierare som används av Azure Sentinel för att markera en rad. | Händelse |
| NetworkApplicationProtocol | Sträng | HTTPS | Det program skikts protokoll som används av anslutningen eller sessionen. | Nätverk |
| DstBytes | int | 32455 | Antalet byte som har skickats från målet till källan för anslutningen eller sessionen. | Mål |
| SrcBytes | int | 46536 | Antalet byte som skickats från källan till målet för anslutningen eller sessionen. | Källa |
| NetworkBytes | int | 78991 | Antal byte som har skickats i båda riktningarna. Om både BytesReceived och bytes sent finns måste BytesTotal vara lika med summan. | Nätverk |
| NetworkDirection | Multi-Value: inkommande, utgående (sträng) | Inkommande | Riktningen på anslutningen eller sessionen, in i eller ut ur organisationen. | Nätverk |
| DstGeoCity | Sträng | Burlington | Den stad som är kopplad till målets IP-adress | Mål<br>Geografi |
| DstGeoCountry | Land (sträng) | USA | Det land som är associerat med käll-IP-adressen | Mål<br>Geografi |
| DstDvcHostname | Enhets namn (sträng) |  victim_pc | Enhets namnet på mål enheten | Mål<br>Enhet |
| DstDvcFqdn | Sträng | victim_pc. contoso. local | Det fullständigt kvalificerade domän namnet för värden där loggen skapades | Mål<br>Enhet |
| DstDomainHostname | sträng | CONTOSO | Mål domänen, domänen för mål värden (webbplats, domän namn osv.), till exempel för DNS-sökningar eller NS-sökningar | Mål |
| DstInterfaceName | sträng | Microsoft Hyper-V nätverkskort | Nätverks gränssnittet som används för anslutningen eller sessionen av mål enheten. | Mål |
| DstInterfaceGuid | sträng | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | GUID för det nätverks gränssnitt som användes för autentiseringsbegäran  | Mål |
| DstIpAddr | IP-adress | 2001: db8:: ff00:42:8329 | IP-adressen för anslutningens eller sessionens mål, som oftast kallas mål-IP i nätverks paketet | Mål<br>IP-adress |
| DstDvcIpAddr | IP-adress | 75.22.12.2 | Mål-IP-adressen för en enhet som inte är direkt kopplad till nätverks paketet | Mål<br>Anordningar<br>IP-adress
| DstGeoLatitude | Latitude (dubbel) | 44,475833 | Latitud för den geografiska koordinat som är kopplad till målets IP-adress | Mål<br>Geografi |
| DstMacAddr | Sträng | 06:10:9f: EB: 8F: 14 | MAC-adressen för det nätverks gränssnitt där anslutningen eller sessionen avslutades, som oftast kallas för mål-MAC i nätverks paketet | Mål<br>OS |
| DstDvcMacAddr | Sträng | 06:10:9f: EB: 8F: 14 | Mål-MAC-adressen för en enhet som inte är direkt kopplad till nätverks paketet. | Mål<br>Anordningar<br>OS |
| DstDvcDomain | Sträng | CONTOSO | Domänen för mål enheten. | Mål<br>Enhet |
| DstPortNumber | Integer | 443 | Målets IP-port. | Mål<br>Port |
| DstGeoRegion | Region (sträng) | Vermont | Regionen i ett land som är kopplat till målets IP-adress | Mål<br>Geografi |
| DstResourceId | Enhets-ID (sträng) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | Resurs-ID för mål enheten. | Mål |
| DstNatIpAddr | IP-adress | 2::1 | Om den rapporteras av en mellanhand NAT-enhet, till exempel en brand vägg, den IP-adress som används av NAT-enheten för kommunikation med källan. | Mål-NAT,<br>IP-adress |
| DstNatPortNumber | int | 443 | Om det rapporteras av en mellanhand NAT-enhet, till exempel en brand vägg, den port som används av NAT-enheten för kommunikation med källan. | Mål-NAT,<br>Port |
| DstUserSid | Användarens SID |  S-12-1445 | Användar-ID för den identitet som är kopplad till sessionens mål. Normalt används den identitet som används för att autentisera en server. Mer information finns i "data typer". | Mål<br>User |
| DstUserAadId | Sträng (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | Objekt-ID för Azure AD-kontot för användaren i slutet av sessionen | Mål<br>User |
| DstUserName | Användar namn (sträng) | John | Användar namnet för den identitet som är kopplad till sessionens mål.  | Mål<br>User |
| DstUserUpn | sträng | johnd@anon.com | UPN för den identitet som är kopplad till sessionens mål. | Mål<br>User |
| DstUserDomain | sträng | ARBETS | Domän-eller dator namnet för kontot vid sessionens mål | Mål<br>User |
| DstZone | Sträng | DMZ | Nätverks zonen för målet, som definieras av rapporterings enheten. | Mål |
| DstGeoLongitude | Longitud (dubbel) | – 73,211944 | Longitud för den geografiska koordinat som är kopplad till målets IP-adress | Mål<br>Geografi |
| DvcAction | Multivärde: Tillåt, neka, Drop (sträng) | Tillåt | Om det rapporteras av en mellanliggande enhet, till exempel en brand vägg, den åtgärd som utförs av enheten. | Enhet |
| DvcInboundInterface | Sträng | eth0 | Om det rapporteras av en mellanliggande enhet, till exempel en brand vägg, används nätverks gränssnittet för anslutningen till käll enheten. | Enhet |
| DvcOutboundInterface | Sträng  | Ethernet-nätverkskort Ethernet 4 | Om det rapporteras av en mellanliggande enhet, till exempel en brand vägg, används nätverks gränssnittet för anslutningen till mål enheten. | Enhet |
| NetworkDuration | Integer | 1500 | Tiden, i millisekunder, för att slutföra nätverks sessionen eller-anslutningen | Nätverk |
| NetworkIcmpCode | Integer | 34 | För ICMP-meddelanden anger du ett numeriskt värde för ICMP-meddelanden (RFC 2780 eller RFC 4443). | Nätverk |
| NetworkIcmpType | Sträng | Det går inte att hitta målet | För ett ICMP-meddelande skriver du text representation för ICMP-meddelande (RFC 2780 eller RFC 4443). | Nätverk |
| DstPackets | int  | 446 | Antalet paket som skickats från målet till källan för anslutningen eller sessionen. Innebörden av ett paket definieras av rapporterings enheten. | Mål |
| SrcPackets | int  | 6478 | Antalet paket som skickas från källan till målet för anslutningen eller sessionen. Innebörden av ett paket definieras av rapporterings enheten. | Källa |
| NetworkPackets | int  | 0 | Antal paket som skickats i båda riktningarna. Om både PacketsReceived och PacketsSent finns måste BytesTotal vara lika med summan. | Nätverk |
| HttpRequestTime | Integer | 700 | Hur lång tid det tog att skicka begäran till servern, om tillämpligt. | Http |
| HttpResponseTime | Integer | 800 | Hur lång tid det tog att ta emot ett svar på servern, om tillämpligt. | Http |
| NetworkRuleName | Sträng | AnyAnyDrop | Namnet eller ID: t för den regel som DeviceAction valdes vid | Nätverk |
| NetworkRuleNumber | int |  23 | Matchat regel nummer  | Nätverk |
| NetworkSessionId | sträng | 172_12_53_32_4322__123_64_207_1_80 | Sessions-ID: t som rapporteras av rapporterings enheten. Till exempel, L7-sessions-ID för vissa program efter autentisering | Nätverk |
| SrcGeoCity | Sträng | Burlington | Den stad som är associerad med käll-IP-adressen | Källicensservern<br>Geografi |
| SrcGeoCountry | Land (sträng) | USA | Det land som är associerat med käll-IP-adressen | Källicensservern<br>Geografi |
| SrcDvcHostname | Enhets namn (sträng) |  villain | Enhets namnet för käll enheten | Källicensservern<br>Enhet |
| SrcDvcFqdn | sträng | Villain.malicious.com | Det fullständigt kvalificerade domän namnet för värden där loggen skapades | Källicensservern<br>Enhet |
| SrcDvcDomain | sträng | EVILORG | Domän för enheten som sessionen initierades från | Källicensservern<br>Enhet |
| SrcDvcOs | Sträng | iOS | Käll enhetens operativ system | Källicensservern<br>Enhet |
| SrcDvcModelName | Sträng | Samsung Galaxy-anteckning | Käll enhetens modell namn | Källicensservern<br>Enhet |
| SrcDvcModelNumber | Sträng | 10,0 | Käll enhetens modell nummer | Källicensservern<br>Enhet |
| SrcDvcType | Sträng | Mobilt | Typ av käll enhet | Källicensservern<br> Enhet |
| SrcIntefaceName | Sträng | eth01 | Nätverks gränssnittet som används för anslutningen eller sessionen av käll enheten. | Källa |
| SrcInterfaceGuid | Sträng | 46ad544b-eaf0-47ef-827c-266030f545a6 | GUID för det nätverks gränssnitt som används | Källa |
| SrcIpAddr | IP-adress | 77.138.103.108 | IP-adressen som anslutningen eller sessionen kommer från. | Källicensservern<br>IP-adress |
| SrcDvcIpAddr | IP-adress | 77.138.103.108 | Käll-IP-adressen för en enhet som inte är direkt kopplad till nätverks paketet (insamlat av en provider eller som uttryckligen beräknas). | Källicensservern<br>Anordningar<br>IP-adress |
| SrcGeoLatitude | Latitude (dubbel) | 44,475833 | Latitud för den geografiska koordinat som är associerad med käll-IP-adressen | Källicensservern<br>Geografi |
| SrcGeoLongitude | Longitud (dubbel) | – 73,211944 | Longituden för den geografiska koordinat som är associerad med käll-IP-adressen | Källicensservern<br>Geografi |
| SrcMacAddr | Sträng | 06:10:9f: EB: 8F: 14 | MAC-adressen för det nätverks gränssnitt som anslutningen för OD-sessionen kommer från. | Källicensservern<br>Mac |
| SrcDvcMacAddr | Sträng | 06:10:9f: EB: 8F: 14 | Käll-MAC-adressen för en enhet som inte är direkt kopplad till nätverks paketet. | Källicensservern<br>Anordningar<br>Mac |
| SrcPortNumber | Integer | 2335 | IP-porten som anslutningen kommer från. Är kanske inte relevant för en session som består av flera anslutningar. | Källicensservern<br>Port |
| SrcGeoRegion | Region (sträng) | Vermont | Regionen i ett land som är kopplat till käll-IP-adressen | Källicensservern<br>Geografi |
| SrcResourceId | Sträng | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Resurs-ID för enheten som genererar meddelandet. | Källa |
| SrcNatIpAddr | IP-adress | 4.3.2.1 | Om den rapporteras av en mellanhand NAT-enhet, till exempel en brand vägg, den IP-adress som används av NAT-enheten för kommunikation med målet. | Källans NAT,<br>IP-adress |
| SrcNatPortNumber | Integer | 345 | Om det rapporteras av en mellanhand NAT-enhet, till exempel en brand vägg, den port som används av NAT-enheten för kommunikation med målet. | Källans NAT,<br>Port |
| SrcUserSid | Användar-ID (sträng) | S-15-1445 | Användar-ID för den identitet som är kopplad till sessionens källa. Normalt utför användaren en åtgärd på klienten. Mer information finns i "data typer". | Källicensservern<br>User |
| SrcUserAadId | Sträng (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | Objekt-ID för Azure AD-kontot för användaren vid sessionens källa | Källicensservern<br>User |
| SrcUserName | Användar namn (sträng) | Bob | Användar namnet för den identitet som är kopplad till sessionens källa. Normalt utför användaren en åtgärd på klienten. Mer information finns i "data typer". | Källa<br>User |
| SrcUserUpn | sträng | bob@alice.com | UPN för det konto som initierar sessionen | Källicensservern<br>User |
| SrcUserDomain | sträng | SKRIVBORDSGADGETAR | Domänen för det konto som initierar sessionen | Källicensservern<br>User |
| SrcZone | Sträng | Tryck på | Källans nätverks zon, som definieras av rapporterings enheten. | Källa |
| NetworkProtocol | Sträng | TCP | IP-protokollet som används av anslutningen eller sessionen. Normalt, TCP, UDP eller ICMP | Nätverk |
| CloudAppName | Sträng | Facebook | Namnet på mål programmet för ett HTTP-program som identifieras av en proxy. | Moln |
| CloudAppId | Sträng | 124 | ID för mål programmet för ett HTTP-program som identifieras av en proxy. Det här värdet är ofta speciellt för den proxy som används. | Moln |
| CloudAppOperation | Sträng | DeleteFile | Åtgärden som användaren utförde i kontexten för mål programmet för ett HTTP-program som identifieras av en proxy. Det här värdet är ofta speciellt för den proxy som används. | Moln |
| CloudAppRiskLevel | Sträng | 3 | Risk nivån som associeras med ett HTTP-program som identifieras av en proxy. Det här värdet är ofta speciellt för den proxy som används. | Moln |
| Sökväg | Sträng | ImNotMalicious.exe | Fil namnet som överförs via nätverks anslutningar för protokoll som FTP och HTTP, som innehåller fil namns informationen. | Fil |
| FilePath | Sträng | C:\Malicious\ImNotMalicious.exe | Den fullständiga sökvägen, inklusive fil namnet, för filen | Fil |
| FileHashMd5 | Sträng | 51BC68715FC7C109DCEA406B42D9D78F | MD5-hash-värdet för den fil som överförs via nätverks anslutningar för protokoll. | Fil |
| FileHashSha1 | Sträng | 491AE3... C299821476F4 | SHA1-hash-värdet för den fil som överförs via nätverks anslutningar för protokoll. | Fil |
| FileHashSha256 | Sträng | 9B8F8EDB... C129976F03 | SHA256 hash-värdet för den fil som överförs via nätverks anslutningar för protokoll. | Fil |
| FileHashSha512 | Sträng | 5E127D... F69F73F01F361 | SHA512 hash-värdet för den fil som överförs via nätverks anslutningar för protokoll. | Fil |
| FileExtension |  Sträng | exe | Den typ av fil som överförs via nätverks anslutningar för protokoll som FTP och HTTP. | Fil
| FileMimeType | Sträng | program-MSWord | MIME-typen för den fil som överförs via nätverks anslutningar för protokoll som FTP och HTTP | Fil |
| Storlek | Integer | 23500 | Fil storleken, i byte, för den överförda filen över nätverks anslutningarna för protokoll. | Fil |
| HttpVersion | Sträng | 2.0 | HTTP-begäran version för HTTP/HTTPS-nätverksanslutningar. | Http |
| HttpRequestMethod | Sträng | GET | HTTP-metoden för HTTP/HTTPS-klientsessioner. | Http |
| HttpStatusCode | Sträng | 404 | HTTP-statuskod för HTTP/HTTPS-klientsessioner. | Http |
| HttpContentType | Sträng | multipart/form-data; gränser = något | Innehålls typ huvudet för HTTP-svar för HTTP/HTTPS-klientsessioner. | Http |
| HttpReferrerOriginal | Sträng | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP referent-huvudet för HTTP/HTTPS-klientsessioner. | Http |
| HttpUserAgentOriginal | Sträng | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, t. ex. Gecko) Chrome/83.0.4103.97 Safari/537.36 | HTTP-användaragent-huvud för HTTP/HTTPS-klientsessioner. | Http |
| HttpRequestXff | Sträng | 120.12.41.1 | HTTP X-vidarebefordrad – för sidhuvud för HTTP/HTTPS-klientsessioner. | Http |
| UrlCategory | Sträng | Sökmotorer | Den definierade grupperingen av en URL (eller som bara baseras på den domän i URL: en) som är relaterad till det som är (d.v.s. vuxna, nyheter, annonsering, parkiska domäner osv.) | url |
| UrlOriginal | Sträng | https://contoso.com/fo/?k=v&q = u # f | URL för HTTP-begäran för HTTP/HTTPS-klientsessioner. | URL |
| UrlHostname | Sträng | contoso.com | Domän delen av en HTTP-begärans-URL för HTTP/HTTPS-klientsessioner. | URL |
| ThreatCategory | Sträng | Trojan | Kategorin för ett hot som identifieras av ett säkerhets system, till exempel webbsäker Gateway för en IP-adress och är associerad med den här sessionen. | Hot |
| ThreatId | Sträng | TR. 124 | ID för ett hot som identifieras av ett säkerhets system, till exempel webbsäker Gateway för en IP-adress och är associerad med den här sessionen. | Hot |
| ThreatName | Sträng | EICAR Test fil | Namnet på det hot eller skadlig kod som har identifierats | Hot |
| AdditionalFields | Dynamiskt (JSON-påse) | {<br>Egenskap1: "val1",<br>Egenskap2: "val2"<br>} | Om ingen respektive kolumn i schemat matchar, kan ytterligare fält lagras i en JSON-uppsättning.<br>Vi rekommenderar att du inte använder den här metoden som inmatnings tid för att tolka data i en JSON. I stället rekommenderar vi att du befordrar ytterligare kolumner.<br>För framtida tolknings-och tids tolknings scenarier kommer ytterligare data att samlas in i den här JSON-säckens kolumn. | Anpassat fält |
| 
