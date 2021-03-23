---
title: CEF-nyckel (common Event format) och CommonSecurityLog fält mappning
description: Den här artikeln mappar CEF-nycklar till motsvarande fält namn i CommonSecurityLog i Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 6c23fe86af030d371e12914062bb9558e8db3484
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776299"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>Fält mappning för CEF och CommonSecurityLog

I följande tabell mappas CEF-fält namn (common Event format) till de namn de använder i Azure Sentinel-CommonSecurityLog och kan vara användbara när du arbetar med en CEF-datakälla i Azure Sentinel.

Mer information finns i [ansluta din externa lösning med hjälp av vanligt händelse format](connect-common-event-format.md).

## <a name="a---c"></a>A-C

|CEF nyckel namn  |Fält namn för CommonSecurityLog  |Beskrivning  |
|---------|---------|---------|
| vara    |    <a name="deviceaction"></a> DeviceAction     |  Den åtgärd som anges i händelsen.       |
|   app  |    ApplicationProtocol     |  Protokollet som används i programmet, till exempel HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPs och så vidare.   |
| CNT    |    EventCount     |  Ett antal som är kopplat till händelsen och som visar hur många gånger samma händelse observerades.       |
| | | |

## <a name="d"></a>D

|CEF nyckel namn  |CommonSecurityLog namn  |Beskrivning  |
|---------|---------|---------|
|Enhets leverantör     |  DeviceVendor       | Sträng som, tillsammans med enhets produkter och versions definitioner, identifierar en unik typ av enhets sändning.       |
|Enhets produkt     |   DeviceProduct      |   Sträng som, tillsammans med enhets leverantör och versions definitioner, identifierar en unik typ av enhets sändning.        |
|Enhets version     |   DeviceVersion      |      En sträng som tillsammans med enhets produkt-och leverantörs definitioner identifierar en unik typ av enhets sändning.     |
|DeviceEventClassID     |   DeviceEventClassID     |   Sträng eller heltal som fungerar som en unik identifierare per händelse typ.      |
| destinationDnsDomain    | DestinationDnsDomain        |   DNS-delen av det fullständigt kvalificerade domän namnet (FQDN).      |
| destinationServiceName | DestinationServiceName | Den tjänst som händelsen riktas mot. Till exempel `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identifierar det översatta målet som händelsen refererar till i ett IP-nätverk som en IPv4 IP-adress. |
| destinationTranslatedPort | DestinationTranslatedPort | Port, efter översättning, till exempel en brand vägg. <br>Giltiga port nummer: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | All information om riktningen som den observerade kommunikationen har vidtagit. Giltiga värden: <br>- `0` = Inkommande <br>- `1` = Utgående |
| deviceDnsDomain | DeviceDnsDomain | DNS-domän delen av det fullständiga domän namnet (FQDN) |
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

|CEF nyckel namn  |CommonSecurityLog namn  |Beskrivning  |
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

|CEF nyckel namn  |CommonSecurityLog namn  |Beskrivning  |
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

|CEF nyckel namn  |CommonSecurityLog namn  |Beskrivning  |
|---------|---------|---------|
|Anledning     |  Anledning      |Orsaken till att en gransknings händelse genererades. <br><br>Exempel: `Bad password` eller `Unknown user`.         |
|Förfrågan     |   RequestURL      | URL: en som används för en HTTP-begäran, inklusive protokollet. Till exempel `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   Användar agenten som är associerad med begäran.      |
| requestContext | RequestContext | Beskriver innehållet som begäran kommer från, till exempel HTTP-referenten. |
| requestCookies | RequestCookies |Cookies som är kopplade till begäran. |
| requestMethod | RequestMethod | Den metod som används för att få åtkomst till en URL. <br><br>Giltiga värden är metoder som `POST` , `GET` och så vidare. |
| RT | ReceiptTime | Tiden då händelsen som är relaterad till aktiviteten togs emot. |
|Allvarlighetsgrad     |  <a name="logseverity"></a> LogSeverity       |  En sträng eller ett heltal som beskriver hur viktigt händelsen är.<br><br> Giltiga sträng värden: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>Giltiga heltals värden är: `0` - `3` = Low, `4` - `6` = medium, `7` - `8` = High, `9` - `10` = Very-High |
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
| typ | Typ | Händelse typ. Värde värden är: <br>- `0`: bas händelse <br>- `1`: aggregerade <br>- `2`: korrelations händelse <br>- `3`: åtgärds händelse <br><br>**Obs**: den här händelsen kan utelämnas för bas händelser. |
| | | |

## <a name="unmapped-fields"></a>Omappade fält

Följande **CommonSecurityLog** fält namn saknar mappningar i CEF-nycklar:


|Fält namn för CommonSecurityLog  |Beskrivning  |
|---------|---------|
|**OriginalLogSeverity**     |  Alltid tomt, stöds för integrering med CiscoASA. <br>Mer information om allvarlighets GRADS värden finns i fältet [LogSeverity](#logseverity) .       |
|**RemoteIP**     |     Fjärr-IP-adressen. <br>Det här värdet baseras på fältet [CommunicationDirection](#communicationdirection) , om möjligt.     |
|**RemotePort**     |   Fjärrporten. <br>Det här värdet baseras på fältet [CommunicationDirection](#communicationdirection) , om möjligt.      |
|**SimplifiedDeviceAction**     |   Fören klar [DeviceAction](#deviceaction) -värdet till en statisk uppsättning värden, samtidigt som det ursprungliga värdet i fältet [DeviceAction](#deviceaction) hålls kvar. <br>Till exempel: `Denied`  >  `Deny` .      |
|     |         |


## <a name="next-steps"></a>Nästa steg

Mer information finns i [ansluta din externa lösning med hjälp av vanligt händelse format](connect-common-event-format.md).
