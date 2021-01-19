---
title: Introduktion till resurs fel sökning
titleSuffix: Azure Network Watcher
description: Den här sidan ger en översikt över fel söknings funktionerna i Network Watcher-resursen
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 0d0597c2df8731171505a090de6959d8a112c004
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569988"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introduktion till resurs fel sökning i Azure Network Watcher

Virtual Network gatewayer ger anslutning mellan lokala resurser och andra virtuella nätverk i Azure. Gatewayer för övervakning och deras anslutningar är viktiga för att säkerställa att kommunikationen inte bryts. Network Watcher ger möjlighet att felsöka gatewayer och anslutningar. Kapaciteten kan anropas via portalen, PowerShell, Azure CLI eller REST API. När den anropas diagnostiserar Network Watcher hälso tillståndet för gatewayen eller anslutningen och returnerar lämpliga resultat. Begäran är en tids krävande transaktion. Resultaten returneras när diagnostiken är klar.

![Skärm bild som visar Network Watcher V P N-diagnostik.][2]

## <a name="results"></a>Resultat

De preliminära resultaten som returneras ger en övergripande bild av resursens hälso tillstånd. Mer detaljerad information kan ges för resurser som visas i följande avsnitt:

Följande lista innehåller värdena som returneras med fel söknings-API:

* **StartTime** – det här värdet är den tid då fel söknings-API-anrop startades.
* **slut tid** – det här värdet är den tidpunkt då fel sökningen avslutades.
* **Code** – det här värdet är inte felfritt, om det uppstår ett enda diagnos fel.
* **resultat** – resultat är en samling resultat som returneras på anslutningen eller den virtuella Nätverksgatewayen.
    * **ID** – det här värdet är fel typen.
    * **Sammanfattning** – det här värdet är en sammanfattning av felet.
    * **detaljerat** – det här värdet ger en detaljerad beskrivning av felet.
    * **recommendedActions** – den här egenskapen är en samling rekommenderade åtgärder som ska vidtas.
      * **actionText** – det här värdet innehåller texten som beskriver vilken åtgärd som ska vidtas.
      * **actionUri** – det här värdet ger URI: n till dokumentationen om hur man agerar.
      * **actionUriText** – det här värdet är en kort beskrivning av åtgärds texten.

Följande tabeller visar de olika fel typerna (ID under resultat från föregående lista) som är tillgängliga och om felet skapar loggar.

### <a name="gateway"></a>Gateway

| Feltyp | Orsak | Loggas|
|---|---|---|
| NoFault | När inget fel har identifierats |Ja|
| GatewayNotFound | Det går inte att hitta någon gateway eller gateway som har tillhandahållits |Nej|
| PlannedMaintenance |  Gateway-instansen är under underhåll  |Nej|
| UserDrivenUpdate | Det här felet inträffar när en användaruppdatering pågår. Uppdateringen kan vara en storleksändring. | Nej |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gatewayen inte kan nås på grund av ett fel i tillståndsavsökningen. | Nej |
| PlatformInActive | Det är något problem med plattformen. | Inga|
| ServiceNotRunning | Den underliggande tjänsten körs inte. | Inga|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gatewayen. Det här felet är bara en varning.| Inga|
| ConnectionsNotConnected | Anslutningarna är inte anslutna. Det här felet är bara en varning.| Ja|
| GatewayCPUUsageExceeded | Den aktuella processor användningen för gateway är > 95%. | Ja |

### <a name="connection"></a>Anslutning

| Feltyp | Orsak | Loggas|
|---|---|---|
| NoFault | När inget fel har identifierats |Ja|
| GatewayNotFound | Det går inte att hitta någon gateway eller gateway som har tillhandahållits |Nej|
| PlannedMaintenance | Gateway-instansen är under underhåll  |Nej|
| UserDrivenUpdate | Det här felet inträffar när en användaruppdatering pågår. Uppdateringen kan vara en storleksändring.  | Nej |
| VipUnResponsive | Det här felet uppstår när den primära instansen av gatewayen inte kan nås på grund av ett fel i tillståndsavsökningen. | Inga |
| ConnectionEntityNotFound | Anslutnings konfigurationen saknas | Inga |
| ConnectionIsMarkedDisconnected | Anslutningen har marker ATS som "frånkopplad" |Inga|
| ConnectionNotConfiguredOnGateway | Ingen anslutning har kon figurer ATS för den underliggande tjänsten. | Ja |
| ConnectionMarkedStandby | Den underliggande tjänsten är markerad som standby.| Ja|
| Autentisering | I förväg delad nyckel matchar inte | Ja|
| PeerReachability | Det går inte att komma åt peer-gatewayen. | Ja|
| IkePolicyMismatch | Peer-gatewayen har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse-fel | Ett fel uppstod vid parsning av WFP-loggen. |Ja|

## <a name="supported-gateway-types"></a>Gateway-typer som stöds

I följande tabell visas vilka gatewayer och anslutningar som stöds med Network Watcher fel sökning:

| Gateway eller anslutning | Stöds  |
|---------|---------|
|**Gateway-typer**   |         |
|VPN      | Stöds        |
|ExpressRoute | Stöds inte |
|**VPN-typer** | |
|Route-baserad | Stöds|
|Principbaserad | Stöds inte|
|**Anslutningstyper**||
|IPSec| Stöds|
|VNet2Vnet| Stöds|
|ExpressRoute| Stöds inte|
|VPNClient| Stöds inte|

## <a name="log-files"></a>Loggfiler

De resurs fel söknings loggfilerna lagras i ett lagrings konto när resurs fel sökningen är färdig. Följande bild visar exempel innehållet för ett anrop som resulterade i ett fel.

![zip-fil][1]

> [!NOTE]
> I vissa fall skrivs bara en delmängd av loggfilerna till lagringen.

Anvisningar om hur du laddar ned filer från Azure Storage-konton finns i [komma igång med Azure Blob Storage med hjälp av .net](../storage/blobs/storage-quickstart-blobs-dotnet.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer hittar du här på följande länk: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** -filen innehåller övergripande statistik för anslutningen, inklusive ingångs-och utgående byte, anslutnings status och tidpunkten då anslutningen upprättades.

> [!NOTE]
> Om anropet till fel söknings-API: t returnerar felfri, är det enda som returneras i zip-filen en **ConnectionStats.txt** -fil.

Innehållet i den här filen liknar följande exempel:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** -filen innehåller CPU-användning och minne som är tillgängliga vid test tillfället.  Innehållet i den här filen liknar följande exempel:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** -filen innehåller eventuella IKE-fel som påträffades under övervakningen.

I följande exempel visas innehållet i en IKEErrors.txt-fil. Felen kan variera beroende på problemet.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Logg filen **Scrubbed-wfpdiag.txt** innehåller logg filen för WFP. Den här loggen innehåller loggning av ignorerade paket och IKE/AuthIP-problem.

I följande exempel visas innehållet i Scrubbed-wfpdiag.txts filen. I det här exemplet var den delade nyckeln till en anslutning inte korrekt eftersom kan ses från den tredje raden från den nedre. Följande exempel är bara ett kodfragment till hela loggen, eftersom loggen kan vara lång beroende på problemet.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt. sum

Filen **wfpdiag.txt. sum** är en logg som visar de buffertar och händelser som bearbetas.

Följande exempel är innehållet i en wfpdiag.txt. SUM-fil.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="considerations"></a>Överväganden 
* Endast en fel söknings åtgärd kan köras i taget per prenumeration. Om du vill köra ytterligare en fel söknings åtgärd väntar du tills den tidigare slutförts. Om fler åtgärder utlöses medan en tidigare åtgärd inte har slutförts kommer efterföljande åtgärder att Miss klaras. 
* CLI-fel: om du använder Azure CLI för att köra kommandot måste VPN Gateway och lagrings kontot finnas i samma resurs grupp. Kunder med resurser i olika resurs grupper kan använda PowerShell eller Azure Portal i stället.  


## <a name="next-steps"></a>Nästa steg

Information om hur du diagnostiserar ett problem med en gateway-eller gateway-anslutning finns i [diagnostisera kommunikations problem mellan nätverk](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
