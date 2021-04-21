---
title: Övervaka Azure File Sync | Microsoft Docs
description: Läs om hur du övervakar Azure File Sync distribution med hjälp Azure Monitor tjänst för synkronisering av lagring och Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8e9fbd5fd8fc90681432ee8403b6dd139d02a5a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797310"
---
# <a name="monitor-azure-file-sync"></a>Övervaka Azure File Sync

Använd Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

I den här artikeln beskrivs hur du övervakar Azure File Sync distribution med hjälp av Azure Monitor, tjänsten för synkronisering av lagring och Windows Server.

Följande scenarier beskrivs i den här guiden: 
- Visa Azure File Sync mått i Azure Monitor.
- Skapa aviseringar i Azure Monitor att proaktivt meddela dig om kritiska villkor.
- Visa hälsotillståndet för din Azure File Sync distribution med hjälp av Azure Portal.
- Så här använder du händelseloggar och prestandaräknare på Dina Windows-servrar för att övervaka hälsotillståndet för Azure File Sync distributionen. 

## <a name="azure-monitor"></a>Azure Monitor

Använd [Azure Monitor](../../azure-monitor/overview.md) för att visa mått och konfigurera aviseringar för synkronisering, molnnivåindelad lagring och serveranslutning.  

### <a name="metrics"></a>Mått

Mått för Azure File Sync är aktiverade som standard och skickas till Azure Monitor var 15:e minut.

**Så här visar Azure File Sync mått i Azure Monitor**
1. Gå till tjänsten **för synkronisering av** lagring i **Azure Portal** och klicka **på Mått.**
2. Klicka på **listrutan** Mått och välj det mått som du vill visa.

![Skärmbild av Azure File Sync mått](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

Följande mått för Azure File Sync är tillgängliga i Azure Monitor:

| Måttnamn | Description |
|-|-|
| Synkroniserade byte | Storleken på data som överförs (ladda upp och ladda ned).<br><br>Enhet: Byte<br>Sammansättningstyp: Summa<br>Tillämpliga dimensioner: Serverslutpunktens namn, synkroniseringsriktning, synkroniseringsgruppens namn |
| Återkalla lagringsnivåer för moln | Storleken på återkallade data.<br><br>**Obs!** Det här måttet tas bort i framtiden. Använd måttet Storlek för återkallningsstorlek för molnnivåinsamling för att övervaka storleken på data som återkallas.<br><br>Enhet: Byte<br>Sammansättningstyp: Summa<br>Tillämplig dimension: Servernamn |
| Storlek för återkallande av molnnivåin tiering | Storleken på återkallade data.<br><br>Enhet: Byte<br>Sammansättningstyp: Summa<br>Tillämpliga dimensioner: Servernamn, Synkroniseringsgruppnamn |
| Storlek på återkallande av molnnivå efter program | Storleken på data som återkallas av programmet.<br><br>Enhet: Byte<br>Sammansättningstyp: Summa<br>Tillämpliga dimensioner: Programnamn, Servernamn, Synkroniseringsgruppnamn |
| Dataflöde för återkallande av molnnivåinkallning | Dataflödets storlek.<br><br>Enhet: Byte<br>Sammansättningstyp: Summa<br>Tillämpliga dimensioner: Servernamn, Synkroniseringsgruppsnamn |
| Filer synkroniseras inte | Antal filer som inte kan synkroniseras.<br><br>Enhet: Antal<br>Sammansättningstyper: Genomsnitt, summa<br>Tillämpliga dimensioner: Serverslutpunktsnamn, Synkroniseringsriktning, Synkroniseringsgruppnamn |
| Synkroniserade filer | Antal filer som överförts (ladda upp och ladda ned).<br><br>Enhet: Antal<br>Sammansättningstyp: Summa<br>Tillämpliga dimensioner: Serverslutpunktsnamn, Synkroniseringsriktning, Synkroniseringsgruppnamn |
| Serverns onlinestatus | Antal pulsslag som tagits emot från servern.<br><br>Enhet: Antal<br>Sammansättningstyp: Max<br>Tillämplig dimension: Servernamn |
| Synkroniseringssessionsresultat | Resultat av synkroniseringssession (1 = lyckad synkroniseringssession; 0 = misslyckad synkroniseringssession)<br><br>Enhet: Antal<br>Sammansättningstyper: Maximalt<br>Tillämpliga dimensioner: Serverslutpunktens namn, synkroniseringsriktning, synkroniseringsgruppens namn |

### <a name="alerts"></a>Aviseringar

Aviseringar meddelar dig proaktivt när viktiga villkor finns i dina övervakningsdata. Mer information om hur du konfigurerar aviseringar Azure Monitor finns i [Översikt över aviseringar i Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md).

**Skapa aviseringar för Azure File Sync**

1. Gå till tjänsten **för synkronisering av** lagring i **Azure Portal**. 
2. Klicka **på** Aviseringar i avsnittet Övervakning och klicka sedan på **+ Ny aviseringsregel.**
3. Klicka **på Välj** villkor och ange följande information för aviseringen: 
    - **Mått**
    - **Dimensionsnamn**
    - **Aviseringslogik**
4. Klicka **på Välj åtgärdsgrupp** och lägg till en åtgärdsgrupp (e-post, SMS osv.) i aviseringen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.
5. Fyll i **aviseringsinformationen,** t.ex. **Aviseringsregelnamn,** **Beskrivning** och **Allvarlighetsgrad.**
6. Klicka **på Skapa aviseringsregel** för att skapa aviseringen.  

I följande tabell visas några exempelscenarier för övervakning och rätt mått som ska användas för aviseringen:

| Scenario | Mått som ska användas för avisering |
|-|-|
| Serverslutpunktens hälsa visar ett fel i portalen | Resultat av synkroniseringssession |
| Filer kan inte synkroniseras till en server eller molnslutpunkt | Filer synkroniseras inte |
| Den registrerade servern kan inte kommunicera med tjänsten för synkronisering av lagring | Onlinestatus för server |
| Storleken på återkallande av molnnivå har överskridit 500GiB på en dag  | Storlek för återkallande av molnnivå |

Anvisningar om hur du skapar aviseringar för dessa scenarier finns i avsnittet [Aviseringsexempel.](#alert-examples)

## <a name="storage-sync-service"></a>Storage Sync Service (Tjänst för synkronisering av lagring)

Om du vill visa hälsotillståndet Azure File Sync **distributionen i Azure Portal** går du till tjänsten för synkronisering **av** lagring och följande information finns tillgänglig:

- Registrerad serverhälsa
- Serverslutpunktshälsa
    - Filer synkroniseras inte
    - Synkroniseringsaktivitet
    - Effektivitet för molnnivåindelad lagring
    - Filer som inte är nivåindelad
    - Återkalla fel
- Mått

### <a name="registered-server-health"></a>Registrerad serverhälsa

Om du vill **visa den registrerade serverhälsan** i portalen går du till avsnittet **Registrerade servrar** i tjänsten för synkronisering **av lagring.**

![Skärmbild av hälsotillståndet för registrerade servrar](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Om **tillståndet Registrerad server** **är Online** kommunicerar servern med tjänsten.
- Om tillståndet **Registrerad server** är Visas **som Offline** körs inte övervakningsprocessen för lagringssynkronisering (AzureStorageSyncMonitor.exe) eller om servern inte kan komma åt Azure File Sync tjänsten. Mer information finns i felsökningsdokumentationen. [](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity)

### <a name="server-endpoint-health"></a>Serverslutpunktens hälsa

Om du vill visa hälsotillståndet för en  **serverslutpunkt** i portalen går du till avsnittet Synkroniseringsgrupper i tjänsten för synkronisering **av** lagring och väljer en **synkroniseringsgrupp.**

![Skärmbild av serverslutpunktens hälsa](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- **Serverslutpunktens**  hälsa och synkroniseringsaktivitet i portalen baseras på de synkroniseringshändelser som loggas i telemetrihändelseloggen på servern (ID 9102 och 9302). Om en synkroniseringssession misslyckas på grund av ett tillfälligt fel, till  exempel om felet avbröts, visas serverslutpunkten fortfarande som felfri i portalen så länge den aktuella synkroniseringssessionen pågår (filerna tillämpas). Händelse-ID 9302 är synkroniseringsförloppet och händelse-ID 9102 loggas när en synkroniseringssession har slutförts.  Mer information finns i [Synkroniseringshälsa och](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) [synkroniseringsförloppet.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session) Om serverslutpunktens hälsa visar **ett fel** eller **ingen aktivitet** kan du läsa [felsökningsdokumentationen](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) för vägledning.
- Antalet **filer som inte synkroniseras** i portalen baseras på händelse-ID 9121 som loggas i telemetrihändelseloggen på servern. Den här händelsen loggas för varje fel per objekt när synkroniseringssessionen har slutförts. Information om hur du löser fel per objekt finns Hur gör jag för att om det finns specifika filer eller [mappar som inte synkroniseras?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Om du vill visa effektiviteten för molnnivåindelad lagring i portalen går du till **Egenskaper för serverslutpunkt** och går **till avsnittet Molnnivåindelad.**  De data som tillhandahålls för effektivitet för molnnivåinloggning baseras på händelse-ID 9071 som loggas i telemetrihändelseloggen på servern. Mer information finns i [Övervaka molnnivåindelad lagring.](file-sync-monitor-cloud-tiering.md)
- Om du vill visa  **filer som inte** är nivåindelad och återkallar fel i portalen går du till Egenskaper för **serverslutpunkt** och går till avsnittet **Lagringsnivåer för** moln. **Filer som inte är** nivåindelade baseras på händelse-ID 9003 som **loggas** i telemetrihändelseloggen på servern och återkallningsfel baseras på händelse-ID 9006. Information om hur du undersöker filer [](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) som inte nivåindelade eller återkallas finns i Felsöka filer som inte kan nivåindelade och Så här felsöker du filer som [inte kan återkallas.](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)

### <a name="metric-charts"></a>Måttdiagram

- Följande måttdiagram kan visas i portalen för tjänsten för synkronisering av lagring:

  | Måttnamn | Description | Bladnamn |
  |-|-|-|
  | Synkroniserade byte | Storleken på data som överförs (ladda upp och ladda ned) | Synkroniseringsgrupp, serverslutpunkt |
  | Återkallande av molnnivåinkallning | Storlek på återkallade data | Registrerade servrar |
  | Filer synkroniseras inte | Antal filer som inte kan synkroniseras | Serverslutpunkt |
  | Synkroniserade filer | Antal filer som överförts (ladda upp och ladda ned) | Synkroniseringsgrupp, serverslutpunkt |
  | Serverns onlinestatus | Antal pulsslag som tagits emot från servern | Registrerade servrar |

- Mer information finns i [Azure Monitor](#azure-monitor).

  > [!Note]  
  > Diagrammen i portalen för tjänsten för synkronisering av lagring har ett 24-timmarsintervall. Om du vill visa olika tidsintervall eller dimensioner använder du Azure Monitor.

## <a name="windows-server"></a>Windows Server

På **Den Windows Server** där Azure File Sync agenten är installerad kan du visa hälsotillståndet  för serverslutpunkterna på den servern med hjälp av händelseloggarna och **prestandaräknarna**.

### <a name="event-logs"></a>Händelseloggar

Använd händelseloggen för telemetri på servern för att övervaka registrerad server, synkronisering och hälsotillstånd för molnnivåinloggning. Händelseloggen för telemetri finns i Loggboken program *och tjänster\Microsoft\FileSync\Agent*.

Synkroniseringshälsa

- Händelse-ID 9102 loggas när en synkroniseringssession har slutförts. Använd den här händelsen för att avgöra om synkroniseringssessioner lyckas (**HResult = 0**) och om det finns synkroniseringsfel per objekt (**PerItemErrorCount**). Mer information finns i dokumentationen [om synkroniseringshälsa](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync)  [och fel per](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) objekt.

  > [!Note]  
  > Synkroniseringssessioner misslyckas ibland totalt eller har ett PerItemErrorCount som inte är noll. Men de fortsätter fortfarande och vissa filer synkroniseras korrekt. Du kan se detta i tillämpade fält som AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes. De här fälten visar hur mycket av sessionen som lyckades. Om du ser att flera synkroniseringssessioner misslyckas på en rad och de har ett ökande antal tillämpade ska du ge synkroniseringen tid att försöka igen innan du öppnar en supportbiljett.

- Händelse-ID 9121 loggas för varje fel per objekt när synkroniseringssessionen har slutförts. Använd den här händelsen för att fastställa antalet filer som inte kan synkroniseras med det här felet (**PersistentCount** och **TransientCount**). Beständiga fel per objekt bör undersökas. Se Hur gör jag för att om det finns specifika filer eller [mappar som inte synkroniseras?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Händelse-ID 9302 loggas var 5:e till 10:e minut om det finns en aktiv synkroniseringssession. Använd den här händelsen för att avgöra hur många objekt som ska synkroniseras (**TotalItemCount**), antalet objekt som har synkroniserats hittills (**AppliedItemCount**) och antalet objekt som inte har synkroniserats på grund av ett fel per objekt (**PerItemErrorCount).** Om synkroniseringen inte går framåt (**AppliedItemCount=0**) misslyckas synkroniseringssessionen så småningom och händelse-ID 9102 loggas med felet. Mer information finns i dokumentationen [om synkroniseringsförloppet.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Registrerad serverhälsa

- Händelse-ID 9301 loggas var 30:e sekund när en server frågar tjänsten efter jobb. Om GetNextJob slutförs **med status = 0** kan servern kommunicera med tjänsten. Om GetNextJob slutförs med ett fel kan du läsa [felsökningsdokumentationen](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) för vägledning.

Hälsa för molnnivåindelad

- Om du vill övervaka nivåindelningsaktiviteten på en server använder du händelse-ID 9003, 9016 och 9029 i händelseloggen telemetri som finns i Loggboken under *Program och tjänster\Microsoft\FileSync\Agent.*

  - Händelse-ID 9003 tillhandahåller feldistribution för en serverslutpunkt. Till exempel: Totalt antal fel och Felkod. En händelse loggas per felkod.
  - Händelse-ID 9016 ger spökresultat för en volym. Till exempel: Ledigt utrymme i procent är, Antal filer som är spökade i sessionen och Antal filer som inte gick att spöka.
  - Händelse-ID 9029 innehåller information om spöksessioner för en serverslutpunkt. Exempel: Antal filer som försöks i sessionen, Antal filer som nivåindelats i sessionen och Antal filer som redan har nivåindelats.

- Om du vill övervaka återkallning på en server använder du händelse-ID 9005, 9006, 9009, 9059 och 9071 i händelseloggen telemetri som finns i Loggboken under Program och *tjänster\Microsoft\FileSync\Agent.*

  - Händelse-ID 9005 ger återkallelsetillförlitlighet för en serverslutpunkt. Exempel: Totalt antal unika filer som nås och Totalt antal unika filer med misslyckad åtkomst.
  - Händelse-ID 9006 ger distribution av återkallelsefel för en serverslutpunkt. Exempel: Totalt antal misslyckade förfrågningar och ErrorCode. En händelse loggas per felkod.
  - Händelse-ID 9009 ger information om återkallning av session för en serverslutpunkt. Exempel: DurationSeconds, CountFilesRecallSucceeded och CountFilesRecallFailed.
  - Händelse-ID 9059 ger distribution av program återkallade för en serverslutpunkt. Exempel: ShareId, Programnamn och TotalEgressNetworkBytes.
  - Händelse-ID 9071 ger effektivitet för molnnivåindelad lagring för en serverslutpunkt. Exempel: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes och TotalCacheMissBytes.

### <a name="performance-counters"></a>Prestandaräknare

Använd Azure File Sync prestandaräknare på servern för att övervaka synkroniseringsaktiviteten.

Om du Azure File Sync prestandaräknare på servern öppnar du Prestandaövervakaren (Perfmon.exe). Räknarna finns under objekten **AFS-byte som överförts och** **AFS-synkroniseringsåtgärder.**

Följande prestandaräknare för Azure File Sync är tillgängliga i Prestandaövervakaren:

| Prestandaobjekt\Räknarnamn | Description |
|-|-|
| AFS-byte som överförts\hämtade byte/sek | Antal byte som laddats ned per sekund. |
| AFS-byte som överförts\överförda byte/sek | Antal byte som laddats upp per sekund. |
| AFS-byte som överförts\Totalt antal byte/sek | Totalt antal byte per sekund (ladda upp och ladda ned). |
| AFS-synkroniseringsåtgärder\Nedladdade synkroniseringsfiler/sek | Antal filer som laddats ned per sekund. |
| AFS-synkroniseringsåtgärder\Uppladdade synkroniseringsfiler/sek | Antal filer som laddats upp per sekund. |
| AFS-synkroniseringsåtgärder\Totalt antal filåtgärder för synkronisering per sekund | Totalt antal synkroniserade filer (ladda upp och ladda ned). |

## <a name="alert-examples"></a>Aviseringsexempel
Det här avsnittet innehåller några exempelaviseringar för Azure File Sync.

  > [!Note]  
  > Om du skapar en avisering och den är för högljudd justerar du tröskelvärdet och aviseringslogiken.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Så här skapar du en avisering om serverslutpunktens hälsa visar ett fel i portalen

1. I **Azure Portal** du till respektive tjänst **för synkronisering av lagring.** 
2. Gå till avsnittet **Övervakning** och klicka på **Aviseringar.** 
3. Klicka på **+ Ny aviseringsregel** för att skapa en ny aviseringsregel. 
4. Konfigurera villkor genom att klicka på **Välj villkor.**
5. I **bladet Konfigurera signallogik** klickar du **på Synkroniseringssessionsresultat** under signalnamn.  
6. Välj följande dimensionskonfiguration: 
     - Dimensionsnamn: **Serverslutpunktsnamn**  
     - Operatör: **=** 
     - Dimensionsvärden: **Alla aktuella och framtida värden**  
7. Gå till **Aviseringslogik** och slutför följande: 
     - Tröskelvärde inställt på **Statisk** 
     - Operator: **Mindre än** 
     - Sammansättningstyp: **Maximalt**  
     - Tröskelvärde: **1** 
     - Utvärderas baserat på: Sammansättningskornighet = **24 |** Utvärderingsfrekvens = **Varje timme** 
     - Klicka **på Klar.** 
8. Klicka **på Välj åtgärdsgrupp** för att lägga till en åtgärdsgrupp (e-post, SMS osv.) i aviseringen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.
9. Fyll i **aviseringsinformationen,** t.ex. **Aviseringsregelnamn,** **Beskrivning** och **Allvarlighetsgrad.**
10. Klicka på **Skapa aviseringsregel**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Så här skapar du en avisering om filer inte kan synkroniseras till en server eller molnslutpunkt

1. I **Azure Portal** till respektive tjänst **för synkronisering av lagring.** 
2. Gå till avsnittet **Övervakning** och klicka på **Aviseringar.** 
3. Klicka på **+ Ny aviseringsregel** för att skapa en ny aviseringsregel. 
4. Konfigurera villkor genom att klicka på **Välj villkor.**
5. I **bladet Konfigurera signallogik** klickar **du på Filer synkroniseras inte** under signalnamn.  
6. Välj följande dimensionskonfiguration: 
     - Dimensionsnamn: **Serverslutpunktsnamn**  
     - Operatör: **=** 
     - Dimensionsvärden: **Alla aktuella och framtida värden**  
7. Gå till **Aviseringslogik** och slutför följande: 
     - Tröskelvärde inställt på **Statisk** 
     - Operator: **Större än** 
     - Sammansättningstyp: **Genomsnitt**  
     - Tröskelvärde: **100** 
     - Utvärderas baserat på: Sammansättningskornighet = **5 minuter** | Utvärderingsfrekvens = **var 5:e minut** 
     - Klicka **på Klar.** 
8. Klicka **på Välj åtgärdsgrupp** för att lägga till en åtgärdsgrupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.
9. Fyll i **aviseringsinformationen** som **Aviseringsregelnamn**, **Beskrivning** och **Allvarlighetsgrad**.
10. Klicka på **Skapa aviseringsregel**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Så här skapar du en avisering om en registrerad server inte kan kommunicera med tjänsten för synkronisering av lagring

1. I **Azure Portal** du till respektive tjänst **för synkronisering av lagring.** 
2. Gå till avsnittet **Övervakning** och klicka på **Aviseringar.** 
3. Klicka på **+ Ny aviseringsregel** för att skapa en ny aviseringsregel. 
4. Konfigurera villkor genom att klicka på **Välj villkor.**
5. I **bladet Konfigurera signallogik** klickar du **på Server-onlinestatus** under signalnamn.  
6. Välj följande dimensionskonfiguration: 
     - Dimensionsnamn: **Servernamn**  
     - Operatör: **=** 
     - Dimensionsvärden: **Alla aktuella och framtida värden**  
7. Gå till **Aviseringslogik** och slutför följande: 
     - Tröskelvärde inställt på **Statisk** 
     - Operator: **Mindre än** 
     - Sammansättningstyp: **Maximalt**  
     - Tröskelvärde (i byte): **1** 
     - Utvärderas baserat på: Sammansättningskornighet = **1 timme** | Utvärderingsfrekvens = **var 30:e minut** 
         - Observera att måtten skickas till Azure Monitor var 15:e till 20:e minut. Ange inte **utvärderingsfrekvensen till mindre** än 30 minuter (genererar falska aviseringar).
     - Klicka **på Klar.** 
8. Klicka **på Välj åtgärdsgrupp** för att lägga till en åtgärdsgrupp (e-post, SMS osv.) i aviseringen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.
9. Fyll i **aviseringsinformationen,** t.ex. **Aviseringsregelnamn,** **Beskrivning** och **Allvarlighetsgrad.**
10. Klicka på **Skapa aviseringsregel**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Så här skapar du en avisering om storleken på molnnivåinkallning har överskridit 500GiB under en dag

1. I **Azure Portal** till respektive tjänst **för synkronisering av lagring.** 
2. Gå till avsnittet **Övervakning** och klicka på **Aviseringar.** 
3. Klicka på **+ Ny aviseringsregel** för att skapa en ny aviseringsregel. 
4. Konfigurera villkor genom att klicka på **Välj villkor.**
5. I **bladet Konfigurera signallogik** klickar du **på Storlek på återkallelsestorlek för molnnivå** under signalnamn.  
6. Välj följande dimensionskonfiguration: 
     - Dimensionsnamn: **Servernamn**  
     - Operatör: **=** 
     - Dimensionsvärden: **Alla aktuella och framtida värden**  
7. Gå till **Aviseringslogik** och slutför följande: 
     - Tröskelvärde inställt på **Statisk** 
     - Operator: **Större än** 
     - Sammansättningstyp: **Totalt**  
     - Tröskelvärde (i byte): **67108864000** 
     - Utvärderas baserat på: Sammansättningskornighet = **24 timmar** | Utvärderingsfrekvens = **Varje timme** 
     - Klicka **på Klar.** 
8. Klicka **på Välj åtgärdsgrupp** för att lägga till en åtgärdsgrupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.
9. Fyll i **aviseringsinformationen** som **Aviseringsregelnamn**, **Beskrivning** och **Allvarlighetsgrad**.
10. Klicka på **Skapa aviseringsregel**. 

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](file-sync-planning.md)
- [Överväg brandväggs- och proxyinställningar](file-sync-firewall-and-proxy.md)
- [Distribuera Azure File Sync](file-sync-deployment-guide.md)
- [Felsöka Azure File Sync](file-sync-troubleshoot.md)