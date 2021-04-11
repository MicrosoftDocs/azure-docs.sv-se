---
title: Övervaka kostnads uppskattningar för virtuella Windows-datorer – Azure
description: Beräkna kostnader och priser för att använda Azure Monitor för virtuella Windows-datorer.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 5bd89a734a20c913bacca1f5531aa76d76418c80
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448227"
---
# <a name="estimate-azure-monitor-costs"></a>Uppskatta Azure Monitor kostnader

Azure Monitor loggar är en tjänst som samlar in, indexerar och lagrar data som genererats av din miljö. Därför baseras Azure Monitor prissättnings modellen på mängden data som samlas in och bearbetas (eller "inmatat") av din Log Analytics arbets yta i gigabyte per dag. Kostnaden för en Log Analytics-arbetsyta baseras inte bara på mängden data som samlas in, utan även vilken Azure Payment-plan du har valt och hur länge du väljer att lagra de data som din miljö genererar.

Den här artikeln förklarar följande saker för att hjälpa dig att förstå hur priser i Azure Monitor fungerar:

- Så här beräknar du inmatnings-och lagrings kostnader innan du aktiverar den här funktionen
- Så här mäter och styr du ditt intag och lagrings utrymme för att minska kostnaderna när du använder den här funktionen

>[!NOTE]
> Alla storlekar och priser som anges i den här artikeln är bara exempel på hur du kan visa hur uppskattningen fungerar. En mer exakt utvärdering baserad på din Azure Monitor Log Analytics prissättnings modell och Azure-region finns i [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Beräkna data inmatnings-och lagrings kostnader

Vi rekommenderar att du använder en fördefinierad uppsättning data som skrivs som loggar i Log Analytics-arbetsytan. I följande exempel uppskattar vi att du tittar på fakturerbara data i standard konfigurationen

De fördefinierade data uppsättningarna för Azure Monitor för virtuella Windows-datorer är:

- Prestanda räknare från sessionernas värdar
- Windows-händelseloggar från sessionens värdar
- Diagnostik för virtuella Windows-datorer från tjänst infrastrukturen

Dina data inmatnings-och lagrings kostnader beror på din miljö storlek, hälsa och användning. Exemplet som beräknar att vi kommer att använda i den här artikeln för att beräkna de kostnads intervall som du kan förväntas baseras på friska virtuella datorer som kör ljus till strömförbrukning, baserat på våra [rikt linjer för storleks ändring av virtuella](/remote/remote-desktop-services/virtual-machine-recs)datorer, för att beräkna en mängd data inmatnings-och lagrings kostnader.

Den virtuella dator för ljus användning som vi använder i vårt exempel innehåller följande komponenter:

- 4 virtuella processorer, 1 disk
- 16 sessioner per dag
- Genomsnittlig varaktighet på två timmar för sessionen (120 minuter)
- 100 processer per session

Den virtuella dator för energi förbrukning som vi kommer att använda i vårt exempel innehåller följande komponenter:

- 6 virtuella processorer, 1 disk
- 6 sessioner per dag
- Genomsnittlig varaktighet på sessionen på 4 timmar (240 minuter)
- 200 processer per session

## <a name="estimating-performance-counter-ingestion"></a>Uppskattar inmatningen av prestanda räknaren

Prestanda räknare visar hur system resurserna presterar. Data inmatningen för prestanda räknaren beror på miljöns storlek och användning. I de flesta fall bör prestanda räknare göra 80 till 99% av data inmatningen för Azure Monitor för virtuella Windows-datorer.

Innan du börjar uppskatta är det viktigt att du förstår att varje prestanda räknare skickar data till en speciell frekvens. Vi anger standard samplings frekvensen per minut (du kan också redigera den här hastigheten i dina inställningar), men denna frekvens kommer att tillämpas på olika multiplicerande faktorer beroende på räknaren. Följande faktorer påverkar hastigheten:

- För per virtuell dator-faktor skickar varje räknare data per virtuell dator i din miljö till standard samplings frekvensen per minut medan den virtuella datorn körs. Du kan beräkna antalet poster som de här räknarna skickar per dag genom att multiplicera standard samplings frekvensen per minut med antalet virtuella datorer i din miljö och sedan multiplicera det antalet med den genomsnittliga körnings tiden för virtuella datorer per dag.

   Sammanfattningsvis:

   Standard samplings frekvens per minut × antal processor kärnor i VM SKU × antal virtuella datorer × genomsnittlig körnings tid för virtuell dator per dag = antal poster som skickats per dag

- För per CPU-faktor skickar varje räknare med standard samplings frekvensen per minut per minut per vCPU i varje virtuell dator i din miljö medan den virtuella datorn körs. Du kan uppskatta antalet poster som räknarna skickar per dag genom att multiplicera standard samplings frekvensen per minut med antalet processor kärnor i VM SKU: n och sedan multiplicera det antalet minuter som den virtuella datorn körs och antalet virtuella datorer i din miljö.

   Sammanfattningsvis:
   
   Standard samplings frekvens per minut × antal processor kärnor i VM SKU × antal minuter som den virtuella datorn kör × antal virtuella datorer = antal poster som skickats per dag

- För per disk-faktor skickar varje räknare data med standard samplings frekvensen för varje disk i varje virtuell dator i din miljö. Antalet poster som räknarna skickar per dag är lika med standard samplings frekvensen per minut multiplicerat med antalet diskar i VM-SKU: n, multiplicerat med 60 minuter per timme och slutligen multipliceras med de genomsnittliga aktiva timmarna för en virtuell dator.

   Sammanfattningsvis:

   Standard samplings frekvens per minut × antal diskar i VM SKU × 60 minuter per timme × antal virtuella datorer × genomsnittlig körnings tid för virtuell dator per dag = antal poster som skickats per dag

- För varje-session skickar varje räknare data med standard samplings frekvensen för varje session i miljön medan sessionen är ansluten. Du kan uppskatta antalet poster som räknarna skickar per dag genom att multiplicera standard samplings intervallet per minut med det genomsnittliga antalet sessioner per dag och den genomsnittliga sessionens varaktighet.

   Sammanfattningsvis:

   Standard samplings frekvens per minut × sessioner per dag × genomsnittlig sessions varaktighet = antal poster som skickats per dag

- För varje process-faktor skickar varje räknare data till standard priset för varje process i varje session i din miljö. Du kan uppskatta antalet poster som dessa räknare skickar per dag genom att multiplicera standard samplings frekvensen per minut med det genomsnittliga antalet sessioner per dag och sedan multiplicera det med den genomsnittliga sessionens varaktighet och det genomsnittliga antalet processer per session.
  
   Sammanfattningsvis:

   Standard samplings frekvens per minut × sessioner per dag × genomsnittlig sessions varaktighet × Genomsnittligt antal processer per session = antal poster som skickats per dag

I följande tabell visas de 20 prestanda räknarna Azure Monitor för Windows Virtual Desktop samlas in och deras standard priser:

| Räknarens namn | Standard samplings frekvens | Frekvens faktor |
|--------------|---------------------|------------------|
| Logisk disk (C:) \\ % ledigt utrymme | 60 sekunder  | Per disk             |
| Logisk disk (C:) \\ medel längd för diskkölängd   | 30 sekunder    | Per disk             |
| Logisk disk (C:) \\ medel s/disk överföring  | 60 sekunder       | Per disk             |
| Logisk disk (C:) \\ Aktuell diskkölängd  | 30 sekunder      | Per disk             |
| Tillgängligt minne ( \* ) \\ megabyte | 30 sekunder    | Per virtuell dator  |
| Minnes ( \* ) \\ sidfel/SEK | 30 sekunder   | Per virtuell dator  |
| Minne ( \* ) \\ sidor/s | 30 sekunder   | Per virtuell dator  |
| Minne ( \* ) \\ % allokerade byte som används | 30 sekunder    | Per virtuell dator  |
| Fysisk disk ( \* ) \\ Gnm. diskkölängd | 30 sekunder      | Per disk             |
| Fysisk disk ( \* ) \\ medel s/disk läsning | 30 sekunder  | Per disk             |
| Fysisk disk ( \* ) \\ medel s/disk överföring | 30 sekunder  | Per disk             |
| Fysisk disk ( \* ) \\ medel s/disk skrivning | 30 sekunder | Per disk             |
| Processor information (_Total) \\ % processor tid | 30 sekunder | Per kärna/processor         |
| Terminal Services ( \* ) \\ aktiva sessioner          | 60 sekunder | Per virtuell dator  |
| Terminal Services ( \* ) \\ inaktiva sessioner        | 60 sekunder | Per virtuell dator  |
| Terminal Services ( \* ) \\ Totalt antal sessioner | 60 sekunder | Per virtuell dator  |
| Fördröjning av användarindata per process ( \* ) \\ Max fördröjning         | 30 sekunder | Per process          |
| Fördröjning för användarindata per session ( \* ) \\ Max fördröjning för indata        | 30 sekunder | Per session          |
| RemoteFX Network ( \* ) \\ aktuell TCP-förval | 30 sekunder | Per virtuell dator  |
| RemoteFX-nätverk ( \* ) \\ aktuell UDP-bandbredd     | 30 sekunder | Per virtuell dator  |

Om vi uppskattar att varje post storlek är 200 byte, skulle ett exempel på en virtuell dator som kör en ljus arbets belastning på standard samplings frekvensen skicka ungefär 90 MEGABYTE av prestanda räknar data per dag per virtuell dator. Under tiden skickar ett exempel på en virtuell dator som kör en energi arbets belastning ungefär 130 megabyte prestanda räknar data per dag per virtuell dator. Registrering av storlek och miljö kan dock variera, så de megabyte per dag som distributionen använder kan vara olika.

Mer information om prestanda räknare för indata-fördröjning finns i [prestanda räknare](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)för användarindata.

## <a name="estimating-windows-event-log-ingestion"></a>Uppskattar inmatningen av händelse loggen i Windows

Händelse loggar i Windows är data källor som samlas in av Log Analytics agenter på virtuella Windows-datorer. Du kan samla in händelser från standard loggar som system och program samt anpassade loggar som skapats av program som du behöver övervaka.

Detta är standard Windows-händelser för Azure Monitor för virtuella Windows-datorer:

- Program
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
- System
- Microsoft-FSLogix – appar/drift
- Microsoft-FSLogix-Apps/admin

Windows-händelser skickas när händelsens villkor uppfylls i miljön. Datorer i felfria tillstånd skickar färre händelser än datorer som är i fel tillstånd. Eftersom antalet händelser är oförutsägbart använder vi ett intervall på 1 000 till 10 000 händelser per virtuell dator per dag baserat på exempel från felfria miljöer för den här uppskattningen. Om vi t. ex. beräknar att varje händelse post storlek i det här exemplet är 1 500 byte, så hamnar det på ungefär 2 till 15 megabyte av händelse data per dag för den angivna miljön.

Mer information om Windows-händelser finns i [Egenskaper för Windows Event-poster](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Uppskattar inmatningen av diagnostik

Tjänsten Diagnostic skapar aktivitets loggar för både användar-och administrations åtgärder.

Detta är namnen på de aktivitets loggar som spårar diagnostiska räknare:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

Tjänsten skickar diagnostikinformation när miljön uppfyller de villkor som krävs för att skapa en post. Eftersom antalet diagnostiska poster är oförutsägbart, använder vi ett intervall på 500 till 1000 händelser per virtuell dator per dag baserat på exempel från felfria miljöer för den här uppskattningen.

Om vi till exempel uppskattar att varje diagnostisk post storlek i det här exemplet är 200 byte, skulle den totala inmatade informationen vara mindre än 1 MB per virtuell dator per dag.

Mer information om aktivitets loggs kategorier finns i [Windows Virtual Desktop Diagnostics](diagnostics-log-analytics.md).

## <a name="estimating-total-costs"></a>Uppskatta totala kostnader

Slutligen beräknar vi den totala kostnaden. I det här exemplet antar vi följande resultat baserat på exempel värden i föregående avsnitt:

| Datakälla        | Storleks uppskattning per dag (i megabyte)   |
|-------------------------------------|------------------------------------------|
| Prestandaräknare   | 90-130 |
| Händelser    | 2-15 |
| Diagnostik för virtuella Windows-datorer | \< 81.1 |

I det här exemplet är den totala inmatade informationen för Azure Monitor för virtuella Windows-datorer mellan 92 och 145 megabyte per virtuell dator per dag. Med andra ord matas varje virtuell dator med ungefär 3 till 5 GB data.

Med standard modellen för att betala per användning för [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/monitor/)kan du beräkna Azure Monitor data insamling och lagrings kostnad per månad. Beroende på din data inmatning kan du också överväga kapacitets reservations modellen för Log Analytics prissättning.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Hantera dina data inmatningar för att minska kostnaderna

Det här avsnittet beskriver hur du mäter och hanterar data inmatning för att minska kostnaderna.

Information om hur du hanterar rättigheter och behörigheter till arbets boken finns i [åtkomst kontroll](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>Om du tar bort data punkter påverkas deras motsvarande visuella objekt i Azure Monitor för virtuella Windows-skrivbordet.

### <a name="log-analytics-settings"></a>Log Analytics inställningar

Här följer några förslag på hur du kan optimera dina Log Analytics inställningar för att hantera data inmatning:

- Använd en angiven Log Analytics arbets yta för dina Windows-resurser för virtuella skriv bord för att säkerställa att Log Analytics endast samlar in prestanda räknare och händelser för de virtuella datorerna i Windows-distributionen för virtuella datorer.
- Justera dina Log Analytics lagrings inställningar för att hantera kostnader. Du kan minska kvarhållningsperioden, utvärdera om en fast lagrings pris nivå skulle bli mer kostnads effektiv eller ange gränser för hur mycket data du kan mata in för att begränsa effekten av en felaktig distribution. Mer information finns i [Hantera användning och kostnader för Azure Monitor loggar](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Ta bort överflödiga data

Vår standard konfiguration är den enda uppsättning data som vi rekommenderar för Azure Monitor för virtuella Windows-datorer. Du har alltid möjlighet att lägga till ytterligare data punkter och visa dem i Host Diagnostics: värd webbläsare eller skapa anpassade diagram för dem, men om du lägger till data ökar Log Analytics kostnaden. De kan tas bort för kostnads besparingar.

### <a name="measure-and-manage-your-performance-counter-data"></a>Mäta och hantera prestanda räknar data

Dina faktiska övervaknings kostnader beror på miljöns storlek, användning och hälsa. Information om hur du mäter data inmatning i Log Analytics-arbetsytan finns i förstå inmatad [logg data volym](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume).

De prestanda räknare som används för sessioner är förmodligen den största källan för inmatade data för Azure Monitor för Windows Virtual Desktop. Följande mall för anpassad fråga för en Log Analytics arbets yta kan spåra frekvens och megabyte som matas in per prestanda räknare under den senaste dagen:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Se till att ersätta mallens plats hållarnas värden med de värden som används i miljön, annars fungerar inte frågan.

Den här frågan visar alla prestanda räknare som du har aktiverat i miljön, inte bara standarderna för Azure Monitor för virtuella Windows-datorer. Den här informationen kan hjälpa dig att förstå vilka områden som ska användas för att minska kostnaderna, t. ex. minska räknar frekvensen eller ta bort den helt.

Du kan också minska kostnaderna genom att ta bort prestanda räknare. Information om hur du tar bort prestanda räknare eller redigerar befintliga räknare för att minska deras frekvens finns i [Konfigurera prestanda räknare](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Hantera händelse loggar i Windows

Windows-händelser är troligen inte att orsaka insamling i data inmatning när alla värdar är felfria. En felaktig värd kan öka antalet händelser som skickas till loggen, men informationen kan vara avgörande för att lösa problem med värden. Vi rekommenderar att du behåller dem. Mer information om hur du hanterar Windows-händelseloggar finns i [Konfigurera händelse loggar i Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Hantera diagnostik

Diagnostik för virtuella Windows-datorer bör innehålla mindre än 1% av dina kostnader för data lagring, så vi rekommenderar inte att du tar bort dem. [Använd Log Analytics för Diagnostics-funktionen](diagnostics-log-analytics.md)om du vill hantera Windows-diagnostik för virtuella skriv bord.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Monitor för virtuella Windows-datorer i de här artiklarna:

- [Använd Azure Monitor för virtuella Windows-datorer för att övervaka distributionen](azure-monitor.md).
- Använd [ord](azure-monitor-glossary.md) listan för att lära dig mer om termer och begrepp.
- Om det uppstår ett problem kan du läsa vår [fel söknings guide](troubleshoot-azure-monitor.md) för hjälp.
- Mer information om hur du hanterar dina övervaknings kostnader finns [i övervaka användning och uppskattade kostnader i Azure Monitor](../azure-monitor/usage-estimated-costs.md) .
