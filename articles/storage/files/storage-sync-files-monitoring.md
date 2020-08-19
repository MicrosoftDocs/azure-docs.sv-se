---
title: Övervaka Azure File Sync | Microsoft Docs
description: Läs om hur du övervakar Azure File Sync-distributionen med hjälp av Azure Monitor, tjänsten Storage Sync och Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9db8a0397c836e8cbc45404d9c4f149255fc76fa
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271064"
---
# <a name="monitor-azure-file-sync"></a>Övervaka Azure File Sync

Använd Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln beskriver hur du övervakar Azure File Sync-distributionen med hjälp av Azure Monitor, tjänsten Storage Sync och Windows Server.

Följande scenarier beskrivs i den här guiden: 
- Visa Azure File Sync mått i Azure Monitor.
- Skapa aviseringar i Azure Monitor för att proaktivt meddela dig om kritiska villkor.
- Visa hälso tillståndet för din Azure File Sync-distribution med hjälp av Azure Portal.
- Så här använder du händelse loggarna och prestanda räknarna på dina Windows-servrar för att övervaka hälso tillståndet för din Azure File Sync-distribution. 

## <a name="azure-monitor"></a>Azure Monitor

Använd [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) om du vill visa mått och konfigurera aviseringar för synkronisering, moln nivåer och server anslutning.  

### <a name="metrics"></a>Mått

Mått för Azure File Sync är aktiverade som standard och skickas till Azure Monitor var 15: e minut.

**Visa Azure File Sync mått i Azure Monitor**
- Gå till **tjänsten Storage Sync** i **Azure Portal** och klicka på **mått**.
- Klicka på list rutan **mått** och välj det mått som du vill visa.

Följande mått för Azure File Sync är tillgängliga i Azure Monitor:

| Måttnamn | Beskrivning |
|-|-|
| Byte som har synkroniserats | Storlek på överförda data (uppladdning och nedladdning).<br><br>Enhet: byte<br>Sammansättnings typ: sum<br>Tillämpliga dimensioner: Server slut punkt, namn, synkroniseringsanslutning, namn på Sync-grupp |
| Återkalla moln nivå | Storlek på data som har återkallats.<br><br>**Obs!** det här måttet tas bort i framtiden. Använd storleks måttet återkalla storlek på moln nivå för att övervaka storleken på data som återkallas.<br><br>Enhet: byte<br>Sammansättnings typ: sum<br>Tillämplig dimension: Server namn |
| Återställnings storlek för moln nivå | Storlek på data som har återkallats.<br><br>Enhet: byte<br>Sammansättnings typ: sum<br>Tillämplig dimension: Server namn, namn på Sync-grupp |
| Återställnings storlek för moln skikt per program | Storlek på data som återkallas av program.<br><br>Enhet: byte<br>Sammansättnings typ: sum<br>Tillämplig dimension: program namn, Server namn, namn på Sync-grupp |
| Återkalla data flöde för moln nivå | Storlek på data återställnings data flöde.<br><br>Enhet: byte<br>Sammansättnings typ: sum<br>Tillämplig dimension: Server namn, namn på Sync-grupp |
| Filer som inte synkroniseras | Antal filer som inte kan synkroniseras.<br><br>Enhet: antal<br>Sammansättnings typ: sum<br>Tillämpliga dimensioner: Server slut punkt, namn, synkroniseringsanslutning, namn på Sync-grupp |
| Synkroniserade filer | Antal överförda filer (uppladdning och nedladdning).<br><br>Enhet: antal<br>Sammansättnings typ: sum<br>Tillämpliga dimensioner: Server slut punkt, namn, synkroniseringsanslutning, namn på Sync-grupp |
| Status för server online | Antal pulsslag som tagits emot från servern.<br><br>Enhet: antal<br>Sammansättnings typ: högsta<br>Tillämplig dimension: Server namn |
| Resultat av Sync-session | Resultat av synkroniseringsbegäran (1 = lyckad synkroniseringsanslutning, 0 = det gick inte att synkronisera sessionen)<br><br>Enhet: antal<br>Samlings typer: Max<br>Tillämpliga dimensioner: Server slut punkt, namn, synkroniseringsanslutning, namn på Sync-grupp |

### <a name="alerts"></a>Aviseringar

Aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. Mer information om hur du konfigurerar aviseringar i Azure Monitor finns i [Översikt över aviseringar i Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

**Skapa aviseringar för Azure File Sync**

- Gå till **tjänsten för synkronisering av lagring** i **Azure Portal**. 
- Klicka på **aviseringar** i avsnittet övervakning och klicka sedan på **+ ny varnings regel**.
- Klicka på **Välj villkor** och ange följande information för aviseringen: 
    - **Mått**
    - **Dimensions namn**
    - **Aviserings logik**
- Klicka på **Välj åtgärds grupp** och Lägg till en åtgärds grupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
- Fyll i **aviserings informationen** som **aviserings regelns namn**, **Beskrivning** och **allvarlighets grad**.
- Klicka på **skapa aviserings regel** för att skapa aviseringen.  

I följande tabell visas några exempel scenarier som du kan använda för att övervaka och rätt mått för aviseringen:

| Scenario | Mått som ska användas för avisering |
|-|-|
| Server slut punkt hälsa visar ett fel i portalen | Resultat av Sync-session |
| Det går inte att synkronisera filer till en server eller moln slut punkt | Filer som inte synkroniseras |
| Den registrerade servern kan inte kommunicera med tjänsten för synkronisering av lagring | Status för server online |
| Återställnings storleken för moln skiktet har överskridit 500GiB per dag  | Återställnings storlek för moln nivå |

Instruktioner för hur du skapar aviseringar för dessa scenarier finns i avsnittet med [aviserings exempel](#alert-examples) .

## <a name="storage-sync-service"></a>Storage Sync Service (Tjänst för synkronisering av lagring)

Om du vill visa hälso tillståndet för din Azure File Sync-distribution i **Azure Portal**går du till **tjänsten Storage Sync** och följande information är tillgänglig:

- Registrerad Server hälsa
- Server slut punkts hälsa
    - Filer som inte synkroniseras
    - Synkronisera aktivitet
    - Effektiv moln nivå
    - Filer som inte är i nivå
    - Återkalla fel
- Mått

### <a name="registered-server-health"></a>Registrerad Server hälsa

Om du vill visa den **registrerade serverns hälso tillstånd** i portalen navigerar du till avsnittet **registrerade servrar** i **tjänsten för synkronisering av lagring**.

- Om det **registrerade Server** läget är **online**, kommunicerar servern med tjänsten.
- Om det **registrerade Server** läget **visas offline**körs inte övervaknings processen för synkronisering av lagring (AzureStorageSyncMonitor.exe) eller så går det inte att få åtkomst till tjänsten Azure File Sync. Se [fel söknings dokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) för vägledning.

### <a name="server-endpoint-health"></a>Server slut punkts hälsa

Om du vill visa hälso tillståndet för en **Server slut punkt** i portalen går du till avsnittet **Sync-grupper** i tjänsten för synkronisering av **lagring** och väljer en Sync- **grupp**.

- **Server slut punktens hälso** -och **synkroniserings aktivitet** i portalen baseras på de synkroniserings händelser som loggas i händelse loggen för TELEMETRI på servern (ID 9102 och 9302). Om en Sync-session Miss lyckas på grund av ett tillfälligt fel, till exempel fel som avbrutits, kommer synkronisering fortfarande att visas som felfri i portalen så länge den aktuella synkroniseringen pågår (filer tillämpas). Händelse-ID 9302 är synkroniseringens förlopps händelse och händelse-ID 9102 loggas när en Sync-session har slutförts.  Mer information finns i [synkronisering av hälso tillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och [synkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Om portalen visar ett fel eftersom synkroniseringen inte gör något kan du läsa mer i [fel söknings dokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) .
- **Filerna som inte synkroniserar** antalet i portalen baseras på händelse-ID 9121 som har loggats i händelse loggen för telemetri på servern. Den här händelsen loggas för varje per objekt-fel när synkroniseringstjänsten har slutförts. Information om hur du löser fel per objekt finns i [Hur gör jag för att se om det finns specifika filer eller mappar som inte synkroniseras?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Om du vill visa **moln nivå effektiviteten** i portalen går du till **serverns slut punkts egenskaper** och navigerar till avsnittet **moln nivå** . De data som tillhandahålls för att effektivisera moln nivån baseras på händelse-ID 9071 som loggas i händelse loggen för telemetri på servern. Mer information finns i [Översikt över moln nivåer](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering).
- Om du vill visa **filer som inte** är på nivå av och **återkalla fel** i portalen går du till **serverns slut punkts egenskaper** och navigerar till avsnittet **moln nivå** . **Filer som inte skiktas** baseras på händelse-ID 9003 som loggas i händelse loggen för telemetri på servern och **återställnings fel** baseras på händelse-ID 9006. Information om hur du undersöker filer som inte går att gå till nivån eller återkalla finns i [Felsöka filer som inte är på nivå](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) och [fel sökning av filer som inte kan återkallas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Mått diagram

- Följande mått diagram visas i Storage Sync service portal:

  | Måttnamn | Beskrivning | Blad namn |
  |-|-|-|
  | Byte som har synkroniserats | Storlek på överförda data (uppladdning och nedladdning) | Synkronisera grupp, Server slut punkt |
  | Återkalla moln nivå | Storlek på data som återkallas | Registrerade servrar |
  | Filer som inte synkroniseras | Antal filer som inte kan synkroniseras | Server slut punkt |
  | Synkroniserade filer | Antal överförda filer (uppladdning och nedladdning) | Synkronisera grupp, Server slut punkt |
  | Status för server online | Antal pulsslag som tagits emot från servern | Registrerade servrar |

- Läs mer i [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Diagrammen i Storage Sync Service Portal har ett tidsintervall på 24 timmar. Använd Azure Monitor om du vill visa olika tidsintervall eller dimensioner.

## <a name="windows-server"></a>Windows Server

På den **Windows-Server** där Azure File Sync-agenten har installerats kan du Visa Server slut punkternas hälso tillstånd på servern med hjälp av **händelse loggarna** och **prestanda räknarna**.

### <a name="event-logs"></a>Händelseloggar

Använd händelse loggen för telemetri på-servern för att övervaka registrerad Server, synkronisering och moln nivå hälsa. Händelse loggen för telemetri finns i Loggboken under *program-och Services\Microsoft\FileSync\Agent*.

Sync-hälsa

- Händelse-ID 9102 loggas när en Sync-session har slutförts. Använd den här händelsen för att avgöra om Sync-sessioner lyckas (**HResult = 0**) och om det finns synkroniseringsfel per objekt (**PerItemErrorCount**). Mer information finns i dokumentationen om [synkronisering av hälso tillstånd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) och  [meddelanden per objekt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > Synkronisering av sessioner fungerar ibland inte generellt eller har en PerItemErrorCount som inte är noll. De kommer dock fortfarande att fortsätta och vissa filer synkroniseras. Du kan se detta i de använda fälten, till exempel AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes. Dessa fält visar hur mycket av sessionen som har lyckats. Om flera Sync-sessioner visas på en rad och de har ett ökande antal tillämpade, kan du ange att synkroniseringen ska göras innan du öppnar ett support ärende.

- Händelse-ID 9121 loggas för varje objekt per objekt när Sync-sessionen har slutförts. Använd den här händelsen för att avgöra hur många filer som inte kan synkroniseras med det här felet (**PersistentCount** och **TransientCount**). Beständiga fel per objekt bör undersökas, se [Hur gör jag för att se om det finns specifika filer eller mappar som inte synkroniseras?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Händelse-ID 9302 loggas var 5 till 10 minuter om det finns en aktiv Sync-session. Använd den här händelsen för att ta reda på om den pågående synkroniseringen görs (**AppliedItemCount > 0**). Om synkronisering inte sker, bör Sync-sessionen sluta att fungera och händelse-ID 9102 kommer att loggas med felet. Mer information finns i dokumentationen för [Sync-förloppet](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Registrerad Server hälsa

- Händelse-ID 9301 loggas var 30: e sekund när en server frågar tjänsten efter jobb. Om GetNextJob slutförs med **status = 0**, kan servern kommunicera med tjänsten. Om GetNextJob har slutförts med ett fel kan du läsa mer i [fel söknings dokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) .

Hälso tillstånd för moln nivåer

- Om du vill övervaka nivå aktivitet på en server använder du händelse-ID 9003, 9016 och 9029 i händelse loggen för telemetri, som finns i Loggboken under *program-och Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9003 ger fel distribution för en server slut punkt. Exempel: totalt antal fel och felkod. En händelse loggas per felkod.
  - Händelse-ID 9016 ger ghosting-resultat för en volym. Till exempel: ledigt utrymme i procent är, antalet filer som är avbildade i sessionen och antalet filer som misslyckades med Ghost.
  - Händelse-ID 9029 tillhandahåller ghosting-sessionsinformation för en server slut punkt. Exempel: antal filer som försökts i sessionen, antalet filer som skiktas i sessionen och antalet filer som redan har delats på nivå.
  
- Om du vill övervaka återställnings aktivitet på en server använder du händelse-ID 9005, 9006, 9009, 9059 och 9071 i händelse loggen för telemetri, som finns i Loggboken under *program-och Services\Microsoft\FileSync\Agent*.

  - Händelse-ID 9005 ger åter kallelse tillförlitlighet för en server slut punkt. Till exempel: totalt antal unika filer som används och totalt antal unika filer med misslyckad åtkomst.
  - Händelse-ID 9006 ger återställnings fel distribution för en server slut punkt. Exempel: totalt antal misslyckade förfrågningar och ErrorCode. En händelse loggas per felkod.
  - Händelse-ID 9009 ger information om återkallande-sessionsinformation för en server slut punkt. Till exempel: DurationSeconds, CountFilesRecallSucceeded och CountFilesRecallFailed.
  - Händelse-ID 9059 tillhandahåller distribution av program åter användning för en server slut punkt. Till exempel: ShareId, program namn och TotalEgressNetworkBytes.
  - Händelse-ID 9071 ger effektiv moln nivå för en server slut punkt. Till exempel: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes och TotalCacheMissBytes.

### <a name="performance-counters"></a>Prestandaräknare

Använd Azure File Sync prestanda räknare på servern för att övervaka synkronisering av aktiviteter.

Öppna prestanda övervakaren (Perfmon.exe) om du vill visa Azure File Sync prestanda räknare på servern. Du hittar räknarna under objekten **AFS-överförda byte** och **AFS-synkronisering** .

Följande prestanda räknare för Azure File Sync är tillgängliga i prestanda övervakaren:

| Object\Counter-namn för prestanda | Beskrivning |
|-|-|
| AFS-byte Transferred\Downloaded byte/s | Antal hämtade byte per sekund. |
| AFS-byte Transferred\Uploaded byte/s | Antal överförda byte per sekund. |
| AFS-byte Transferred\Total byte/s | Totalt antal byte per sekund (uppladdning och nedladdning). |
| AFS Sync-Operations\Downloaded Sync-filer/SEK | Antal hämtade filer per sekund. |
| AFS Sync-Operations\Uploaded Sync-filer/SEK | Antal överförda filer per sekund. |
| AFS Sync-Operations\Total Sync File-åtgärder/SEK | Totalt antal filer som har synkroniserats (uppladdning och nedladdning). |

## <a name="alert-examples"></a>Aviserings exempel
Det här avsnittet innehåller några exempel på aviseringar för Azure File Sync.

  > [!Note]  
  > Om du skapar en avisering och det är för brus, justera tröskelvärdet och aviserings logiken.
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Så här skapar du en avisering om Server slut punkt hälsan visar ett fel i portalen

1. I **Azure Portal**navigerar du till respektive **synkroniseringstjänst för lagring**. 
2. Gå till avsnittet **övervakning** och klicka på **aviseringar**. 
3. Skapa en ny varnings regel genom att klicka på **+ ny varnings regel** . 
4. Konfigurera villkor genom att klicka på **Välj villkor**.
5. I bladet **Konfigurera signal logik** klickar du på **Sync-session resultat** under signal namn.  
6. Välj följande dimensions konfiguration: 
    - Dimensions namn: **Server slut punktens namn**  
    - Operator **=** 
    - Dimensions värden: **alla aktuella och framtida värden**  
7. Navigera till **aviserings logiken** och Slutför följande: 
    - Tröskelvärdet har angetts till **statisk** 
    - Operator: **mindre än** 
    - Sammansättnings typ: **högsta**  
    - Tröskel värde: **1** 
    - Utvärderas baserat på: agg regerings kornig het = **24 timmar** | Utvärderings frekvens = **varje timme** 
    - Klicka på **Slutför.** 
8. Klicka på **Välj åtgärds grupp** för att lägga till en åtgärds grupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
9. Fyll i **aviserings informationen** som **aviserings regelns namn**, **Beskrivning** och **allvarlighets grad**.
10. Klicka på **Skapa aviseringsregel**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Så här skapar du en avisering om filer inte kan synkroniseras till en server eller moln slut punkt

1. I **Azure Portal**navigerar du till respektive **synkroniseringstjänst för lagring**. 
2. Gå till avsnittet **övervakning** och klicka på **aviseringar**. 
3. Skapa en ny varnings regel genom att klicka på **+ ny varnings regel** . 
4. Konfigurera villkor genom att klicka på **Välj villkor**.
5. I bladet **Konfigurera signal logik** klickar du på **filer som inte synkroniseras** under signal namn.  
6. Välj följande dimensions konfiguration: 
     - Dimensions namn: **Server slut punktens namn**  
     - Operator **=** 
     - Dimensions värden: **alla aktuella och framtida värden**  
7. Navigera till **aviserings logiken** och Slutför följande: 
     - Tröskelvärdet har angetts till **statisk** 
     - Operator: **större än** 
     - Sammansättnings typ: **totalt**  
     - Tröskelvärde: **100** 
     - Utvärderas baserat på: agg regerings granularitet = **5 minuter** | Utvärderings frekvens = **var 5: e minut** 
     - Klicka på **Slutför.** 
8. Klicka på **Välj åtgärds grupp** för att lägga till en åtgärds grupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
9. Fyll i **aviserings informationen** som **aviserings regelns namn**, **Beskrivning** och **allvarlighets grad**.
10. Klicka på **Skapa aviseringsregel**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Så här skapar du en avisering om en registrerad Server inte kan kommunicera med lagrings tjänsten för synkronisering

1. I **Azure Portal**navigerar du till respektive **synkroniseringstjänst för lagring**. 
2. Gå till avsnittet **övervakning** och klicka på **aviseringar**. 
3. Skapa en ny varnings regel genom att klicka på **+ ny varnings regel** . 
4. Konfigurera villkor genom att klicka på **Välj villkor**.
5. I bladet **Konfigurera signal logik** klickar du på **serverns onlinestatus** under signal namn.  
6. Välj följande dimensions konfiguration: 
     - Dimensions namn: **Server namn**  
     - Operator **=** 
     - Dimensions värden: **alla aktuella och framtida värden**  
7. Navigera till **aviserings logiken** och Slutför följande: 
     - Tröskelvärdet har angetts till **statisk** 
     - Operator: **mindre än** 
     - Sammansättnings typ: **högsta**  
     - Tröskelvärde (i byte): **1** 
     - Utvärderas baserat på: agg regerings kornig het = **1 timme** | Utvärderings frekvens = **var 30: e minut** 
     - Klicka på **Slutför.** 
8. Klicka på **Välj åtgärds grupp** för att lägga till en åtgärds grupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
9. Fyll i **aviserings informationen** som **aviserings regelns namn**, **Beskrivning** och **allvarlighets grad**.
10. Klicka på **Skapa aviseringsregel**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Så här skapar du en avisering om återställnings storleken för moln skiktet har överskridit 500GiB under en dag

1. I **Azure Portal**navigerar du till respektive **synkroniseringstjänst för lagring**. 
2. Gå till avsnittet **övervakning** och klicka på **aviseringar**. 
3. Skapa en ny varnings regel genom att klicka på **+ ny varnings regel** . 
4. Konfigurera villkor genom att klicka på **Välj villkor**.
5. I bladet **Konfigurera signal logik** klickar du på **moln nivå åter kallelse storlek** under signal namn.  
6. Välj följande dimensions konfiguration: 
     - Dimensions namn: **Server namn**  
     - Operator **=** 
     - Dimensions värden: **alla aktuella och framtida värden**  
7. Navigera till **aviserings logiken** och Slutför följande: 
     - Tröskelvärdet har angetts till **statisk** 
     - Operator: **större än** 
     - Sammansättnings typ: **totalt**  
     - Tröskelvärde (i byte): **67108864000** 
     - Utvärderas baserat på: agg regerings kornig het = **24 timmar** | Utvärderings frekvens = **varje timme** 
    - Klicka på **Slutför.** 
8. Klicka på **Välj åtgärds grupp** för att lägga till en åtgärds grupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
9. Fyll i **aviserings informationen** som **aviserings regelns namn**, **Beskrivning** och **allvarlighets grad**.
10. Klicka på **Skapa aviseringsregel**. 

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Överväg inställningar för brand vägg och proxy](storage-sync-files-firewall-and-proxy.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)
- [Vanliga frågor och svar om Azure Files](storage-files-faq.md)
