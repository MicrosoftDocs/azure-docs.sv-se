---
title: CEF-nyckel (common Event format) och CommonSecurityLog fält mappning
description: Den här artikeln mappar CEF-nycklar till motsvarande fält namn i CommonSecurityLog i Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 1670d1bb291e30295018146f2a24c5282feac6e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311659"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>Fält mappning för CEF och CommonSecurityLog

I följande tabell mappas CEF-fält namn (common Event format) till de namn de använder i Azure Sentinel-CommonSecurityLog och kan vara användbara när du arbetar med en CEF-datakälla i Azure Sentinel.

Mer information finns i [ansluta din externa lösning med hjälp av vanligt händelse format](connect-common-event-format.md).

## <a name="a---c"></a>A-C

|CEF nyckel namn  |Fält namn för CommonSecurityLog  |Description  |
|---------|---------|---------|
| vara    |    <a name="deviceaction"></a> DeviceAction     |  Den åtgärd som anges i händelsen.       |
|   app  |    ApplicationProtocol     |  Protokollet som används i programmet, till exempel HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPs och så vidare.   |
| CNT    |    EventCount     |  Ett antal som är kopplat till händelsen och som visar hur många gånger samma händelse observerades.       |
| | | |

## <a name="d"></a>D

|CEF nyckel namn  |CommonSecurityLog namn  |Description  |
|---------|---------|---------|
|Enhets leverantör     |  DeviceVendor       | Sträng som, tillsammans med enhets produkter och versions definitioner, identifierar en unik typ av enhets sändning.       |
|Enhets produkt     |   DeviceProduct      |   Sträng som, tillsammans med enhets leverantör och versions definitioner, identifierar en unik typ av enhets sändning.        |
|Enhets version     |   DeviceVersion      |      En sträng som tillsammans med enhets produkt-och leverantörs definitioner identifierar en unik typ av enhets sändning.     |
| destinationDnsDomain    | DestinationDnsDomain        |   DNS-delen av det fullständigt kvalificerade domän namnet (FQDN).      |
| destinationServiceName | DestinationServiceName | Den tjänst som händelsen riktas mot. Till exempel `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identifierar det översatta målet som händelsen refererar till i ett IP-nätverk som en IPv4 IP-adress. |
| destinationTranslatedPort | DestinationTranslatedPort | Port, efter översättning, till exempel en brand vägg. <br>Giltiga port nummer: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | All information om riktningen som den observerade kommunikationen har vidtagit. Giltiga värden: <br>- `0` = Inkommande <br>- `1` = Utgående |
| deviceDnsDomain | DeviceDnsDomain | DNS-domän delen av det fullständiga domän namnet (FQDN) |
|DeviceEventClassID     |   DeviceEventClassID     |   Sträng eller heltal som fungerar som en unik identifierare per händelse typ.      |
| deviceExternalID | DeviceExternalID | Ett namn som unikt identifierar den enhet som genererar händelsen. |
| deviceFacility | DeviceFacility | Den anläggning som genererar händelsen.|
| deviceInboundInterface | DeviceInboundInterface |Gränssnittet som paketet eller data angavs på enheten.  |
| deviceNtDomain | DeviceNtDomain | Enhetens Windows-domän |
| deviceOutboundInterface | DeviceOutboundInterface |Gränssnitt som paketet eller data har kvar på enheten. |
| devicePayloadId |DevicePayloadId |Unik identifierare för den nytto last som är associerad med händelsen. |
| deviceProcessName | ProcessName | Process namn som är associerat med händelsen. <br><br>I UNIX är det till exempel processen som genererar syslog-posten. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Identifierar den översatta enhets adress som händelsen refererar till, i ett IP-nätverk. <br><br>Formatet är en IPv4-adress. |
| dhost |DestinationHostName | Målet som händelsen refererar till i ett IP-nätverk.  <br>Formatet ska vara ett fullständigt domän namn som är associerat med målnoden, när en nod är tillgänglig. Exempel: `host.domain.com` eller `host`. |
| dmac | DestinationMacAddress | Mål-MAC-adressen (FQDN) |
| dntdom | DestinationNTDomain | Domän namnet för mål adressen i Windows.|
| dpid | DestinationProcessId |ID för mål processen som är associerad med händelsen.|
| dpriv | DestinationUserPrivileges | Definierar målets användnings privilegier. <br>Giltiga värden: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | Namnet på händelsens mål process, till exempel `telnetd` eller `sshd.` |
| DPT | DestinationPort | Målport. <br>Giltiga värden: `*0` - `65535` |
| tid | DestinationIP | IpV4-målet som händelsen refererar till i ett IP-nätverk. |
| dtz | DeviceTimeZon | Timezone för enheten som genererar händelsen |
| DUID |DestinationUserId | Identifierar mål användaren efter ID. |
| duser | DestinationUserName |Identifierar mål användaren efter namn.|
| enheter | DeviceAddress | IPv4-adressen för enheten som genererar händelsen. |
| dvchost | DeviceName | FQDN som är associerat med noden enhet när en nod är tillgänglig. Exempel: `host.domain.com` eller `host`.| 
| dvcmac | DeviceMacAddress | MAC-adressen för enheten som genererar händelsen. |
| dvcpid | Process-ID | Definierar ID för processen på enheten som genererar händelsen. |

## <a name="e---i"></a>E-I

|CEF nyckel namn  |CommonSecurityLog namn  |Description  |
|---------|---------|---------|
|slut     |  EndTime       | Tiden då aktiviteten som är relaterad till händelsen avslutades.        |
|externalId    |   ExternalID      | Ett ID som används av den ursprungliga enheten. Normalt har dessa värden ökande värden som är associerade med en händelse.        |
|fileCreateTime     |  FileCreateTime      | Tid när filen skapades.        |
|fileHash     |   FileHash      |   Hash för en fil.      |
|fileId     |   FileID      |Ett ID som är kopplat till en fil, till exempel inode.         |
| fileModificationTime | FileModificationTime |Tid när filen senast ändrades. |
| filePath | FilePath | Fullständig sökväg till filen, inklusive fil namnet. Till exempel: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` eller `/usr/bin/zip` .|
| filePermission |FilePermission |Filens behörigheter. |
| Typen | Typen | Filtyp, till exempel pipe, socket och så vidare.|
|fname | Sökväg| Filens namn, utan sökvägen. |
| fsize | Storlek | Filens storlek. |
|Värd    |  Dator       | Värd, från syslog        |
|in     |  ReceivedBytes      |Antal överförda byte.         |
| | | |

## <a name="m---p"></a>M-P

|CEF nyckel namn  |CommonSecurityLog namn  |Description  |
|---------|---------|---------|
|msg   |  Meddelande       | Ett meddelande som ger mer information om händelsen.        |
|Name     |  Aktivitet       |   En sträng som representerar en läslig och begriplig Beskrivning av händelsen.     |
|oldFileCreateTime     |  OldFileCreateTime       | Tid när den gamla filen skapades.        |
|oldFileHash     |   OldFileHash      |   Den gamla filens hash-värde.      |
|oldFileId     |   OldFileId     |   Och ID som är kopplat till den gamla filen, till exempel inode.      |
| oldFileModificationTime | OldFileModificationTime |Tid när den gamla filen senast ändrades. |
| oldFileName |  OldFileName |Namnet på den gamla filen. |
| oldFilePath | OldFilePath | Fullständig sökväg till den gamla filen, inklusive fil namnet. <br>Exempel: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` eller `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Behörigheter för den gamla filen. |
|oldFileSize | OldFileSize | Den gamla filens storlek.|
| oldFileType | OldFileType | Den gamla filens filtyp, till exempel en pipe, en socket och så vidare.|
| out | SentBytes | Antal överförda byte. |
| Resultat | Resultat | Resultatet av händelsen, till exempel `success` eller `failure` .|
|proto    |  Protokoll       | Transport protokoll som identifierar det Layer-4-protokoll som används. <br><br>Möjliga värden är protokoll namn, till exempel `TCP` eller `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|CEF nyckel namn  |CommonSecurityLog namn  |Description  |
|---------|---------|---------|
|Anledning     |  Anledning      |Orsaken till att en gransknings händelse genererades. <br><br>Exempel: `Bad password` eller `Unknown user`.         |
|Förfrågan     |   RequestURL      | URL: en som används för en HTTP-begäran, inklusive protokollet. Till exempel `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   Användar agenten som är associerad med begäran.      |
| requestContext | RequestContext | Beskriver innehållet som begäran kommer från, till exempel HTTP-referenten. |
| requestCookies | RequestCookies |Cookies som är kopplade till begäran. |
| requestMethod | RequestMethod | Den metod som används för att få åtkomst till en URL. <br><br>Giltiga värden är metoder som `POST` , `GET` och så vidare. |
| RT | ReceiptTime | Tiden då händelsen som är relaterad till aktiviteten togs emot. |
|Allvarlighetsgrad     |  <a name="logseverity"></a> LogSeverity       |  En sträng eller ett heltal som beskriver hur viktigt händelsen är.<br><br> Giltiga sträng värden: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>Giltiga heltals värden är:<br> - `0`-`3` = Låg <br>- `4`-`6` = Medel<br>- `7`-`8` = Hög<br>- `9`-`10` = Very-High |
| shost    | SourceHostName        |Identifierar källan som händelsen refererar till i ett IP-nätverk. Formatet ska vara ett fullständigt kvalificerat domän namn (DQDN) som är kopplat till Källnoden när en nod är tillgänglig. Exempel: `host` eller `host.domain.com`. |
| smac | SourceMacAddress | Käll-MAC-adress. |
| sntdom | SourceNTDomain | Namnet på Windows-domänen för käll adressen. |
| sourceDnsDomain | SourceDnsDomain | DNS-delen av fullständigt domän namn. |
| sourceServiceName | SourceServiceName | Tjänsten som ansvarar för att generera händelsen. |
| sourceTranslatedAddress | SourceTranslatedAddress | Identifierar den översatta källa som händelsen refererar till i ett IP-nätverk. |
| sourceTranslatedPort | SourceTranslatedPort | Käll port efter översättning, till exempel en brand vägg. <br>Giltiga port nummer är `0`  -  `65535` . |
| SPID | SourceProcessId | ID för käll processen som är associerad med händelsen.|
| spriv | SourceUserPrivileges | Käll användarens privilegier. <br><br>Giltiga värden är: `Administrator` , `User` , `Guest` |
| sproc | SourceProcessName | Namnet på händelsens käll process.|
| spt | SourcePort | Käll port numret. <br>Giltiga port nummer är `0`  -  `65535` . |
| src | SourceIP |Källan som en händelse refererar till i ett IP-nätverk, som en IPv4-adress. |
| start | StartTime | Den tidpunkt då aktiviteten som händelsen refererar till startade. |
| suid | SourceUserID | Identifierar käll användaren per ID. |
| suser | SourceUserName | Identifierar käll användaren efter namn. |
| typ | Typ | Händelse typ. Värde värden är: <br>- `0`: bas händelse <br>- `1`: aggregerade <br>- `2`: korrelations händelse <br>- `3`: åtgärds händelse <br><br>**Obs**: den här händelsen kan utelämnas för bas händelser. |
| | | |

## <a name="custom-fields"></a>Anpassade fält

Följande tabeller mappar namnen på CEF-nycklar och CommonSecurityLog-fält som är tillgängliga för kunder som inte kan använda data som inte gäller för något av de inbyggda fälten.

### <a name="custom-ipv6-address-fields"></a>Anpassade IPv6-adress fält

Följande tabell mappar CEF-nyckel-och CommonSecurityLog-namn för *IPv6* -adress fälten som är tillgängliga för anpassade data.

|CEF nyckel namn  |CommonSecurityLog namn  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Anpassade nummer fält

Följande tabell mappar CEF-nyckel-och CommonSecurityLog-namn för de *siffer* fält som är tillgängliga för anpassade data.

|CEF nyckel namn  |CommonSecurityLog namn  |
|---------|---------|
|     cn1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     cn2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     cn3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Anpassade sträng fält

Följande tabell mappar CEF-nyckel-och CommonSecurityLog-namn för de *sträng* fält som är tillgängliga för anpassade data.

|CEF nyckel namn  |CommonSecurityLog namn  |
|---------|---------|
|     CS1    |     DeviceCustomString1 <sup> [1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup> [1](#use-sparingly)</sup>    |
|     CS2    |     DeviceCustomString2 <sup> [1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label <sup> [1](#use-sparingly)</sup> |
|     CS3    |     DeviceCustomString3 <sup> [1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label <sup> [1](#use-sparingly)</sup> |
|     CS4    |     DeviceCustomString4 <sup> [1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label <sup> [1](#use-sparingly)</sup>  |
|     cs5    |     DeviceCustomString5 <sup> [1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup> [1](#use-sparingly)</sup>    |
|     cs6    |     DeviceCustomString6 <sup> [1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label <sup> [1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> vi rekommenderar att du använder **DeviceCustomString** -fälten sparsamt och använder mer exakta, inbyggda fält när det är möjligt.
> 

### <a name="custom-timestamp-fields"></a>Anpassade tidsstämpelfält

Följande tabell mappar CEF-nyckel-och CommonSecurityLog-namn för de *tidsstämpelfält* som är tillgängliga för anpassade data.

|CEF nyckel namn  |CommonSecurityLog namn  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Anpassade heltals data fält

Följande tabell mappar CEF-nyckel-och CommonSecurityLog-namn för de *heltals* fält som är tillgängliga för anpassade data.

|CEF nyckel namn  |CommonSecurityLog namn  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Anriknings fält

Följande **CommonSecurityLog** -fält läggs till av Azure Sentinel för att utöka de ursprungliga händelser som tas emot från käll enheterna och har inga mappningar i CEF-nycklar:

### <a name="threat-intelligence-fields"></a>Hot informations fält

|Fält namn för CommonSecurityLog  |Description  |
|---------|---------|
|   **IndicatorThreatType**  |  Hot typen [MaliciousIP](#MaliciousIP) , enligt hot Intelligence-flödet.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | Visar en lista över alla IP-adresser i meddelandet som motsvarar det aktuella hotet Intelligence-flödet. |
|  **MaliciousIPCountry**   | [MaliciousIP](#MaliciousIP) -landet, enligt den geografiska informationen vid tidpunkten för inmatningen av posten.        |
| **MaliciousIPLatitude**    |   [MaliciousIP](#MaliciousIP) longitud, enligt den geografiska informationen vid tidpunkten för inmatningen av posten.      |
| **MaliciousIPLongitude**    |  [MaliciousIP](#MaliciousIP) longitud, enligt den geografiska informationen vid tidpunkten för inmatningen av posten.       |
| **ReportReferenceLink**    |    Länk till hot informations rapporten.     |
|  **ThreatConfidence**   |   [MaliciousIP](#MaliciousIP) hot, enligt Hot information-flödet.      |
| **ThreatDescription**    |   Beskrivningen av [MaliciousIP](#MaliciousIP) -hotet, enligt Threat Intelligence-flödet.      |
| **ThreatSeverity** | Hotets allvarlighets grad för [MaliciousIP](#MaliciousIP), enligt hot Intelligence-flödet vid tidpunkten för inmatningen av posten. |
|     |         |

### <a name="additional-enrichment-fields"></a>Ytterligare anriknings fält

|Fält namn för CommonSecurityLog  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  Alltid tomt, stöds för integrering med CiscoASA. <br>Mer information om allvarlighets GRADS värden finns i fältet [LogSeverity](#logseverity) .       |
|**RemoteIP**     |     Fjärr-IP-adressen. <br>Det här värdet baseras på fältet [CommunicationDirection](#communicationdirection) , om möjligt.     |
|**RemotePort**     |   Fjärrporten. <br>Det här värdet baseras på fältet [CommunicationDirection](#communicationdirection) , om möjligt.      |
|**SimplifiedDeviceAction**     |   Fören klar [DeviceAction](#deviceaction) -värdet till en statisk uppsättning värden, samtidigt som det ursprungliga värdet i fältet [DeviceAction](#deviceaction) hålls kvar. <br>Till exempel: `Denied`  >  `Deny` .      |
|**SourceSystem**     | Har alltid definierats som **OpsManager**.        |
|     |         |

## <a name="next-steps"></a>Nästa steg

Mer information finns i [ansluta din externa lösning med hjälp av vanligt händelse format](connect-common-event-format.md).
