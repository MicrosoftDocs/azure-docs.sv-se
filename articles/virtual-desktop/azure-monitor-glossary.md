---
title: Övervaka Windows Virtual Desktop Preview-ordlista – Azure
description: En ord lista med termer och begrepp som rör Azure Monitor för virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 3/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b824bc13bc4f553d22358b69237173effb51594
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627140"
---
# <a name="azure-monitor-for-windows-virtual-desktop-preview-glossary"></a>Azure Monitor för ord listan för Windows Virtual Desktop (förhands granskning)

>[!IMPORTANT]
>Azure Monitor för det virtuella Windows-skrivbordet är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller en kort beskrivning av viktiga termer och begrepp som är relaterade till Azure Monitor för Windows Virtual Desktop (för hands version).

## <a name="alerts"></a>Aviseringar

Alla aktiva Azure Monitor aviseringar som du har konfigurerat för prenumerationen och klassificerats som [allvarlighets grad 0](#severity-0-alerts) visas på sidan Översikt. Information om hur du konfigurerar aviseringar finns i [svara på händelser med Azure Monitor aviseringar](../azure-monitor/alerts/tutorial-response.md).

## <a name="available-sessions"></a>Tillgängliga sessioner

Tillgängliga sessioner visar antalet tillgängliga sessioner i poolen. Tjänsten beräknar det här antalet genom att multiplicera antalet virtuella datorer (VM) med det maximala antalet sessioner som tillåts per virtuell dator och sedan subtrahera det totala antalet sessioner.

## <a name="connection-success"></a>Anslutningen lyckades

Det här objektet visar anslutnings hälsan. "Anslutningen lyckades" innebär att anslutningen kan komma åt värden, vilket bekräftas av stacken på den virtuella datorn. En misslyckad anslutning innebär att anslutningen inte kunde ansluta till värden.

## <a name="daily-active-users-dau"></a>Dagliga aktiva användare (DAU)

Det totala antalet användare som har startat en session under de senaste 24 timmarna.

## <a name="daily-alerts"></a>Dagliga aviseringar

Det totala antalet aviseringar som utlösts varje dag.

## <a name="daily-connections-and-reconnections"></a>Dagliga anslutningar och återanslutningar

Det totala antalet anslutningar och åter anslutningar som har startats eller slutförts under de senaste 24 timmarna.

## <a name="daily-connected-hours"></a>Anslutna timmar per dag

Det totala antalet timmar som ägnats åt att ansluta till en session över användare under de senaste 24 timmarna.

## <a name="diagnostics-and-errors"></a>Diagnostik och fel

När ett fel eller en varning visas i Azure Monitor för det virtuella Windows-skrivbordet, kategoriseras det av tre saker:

- Aktivitets typ: den här kategorin är hur felet kategoriseras av Windows Virtual Desktop Diagnostics. Kategorierna är hanterings aktiviteter, feeds, anslutningar, värd registreringar, fel och kontroll punkter. Läs mer om de här kategorierna i [använda Log Analytics för funktionen diagnostik](diagnostics-log-analytics.md).

- Typ: den här kategorin visar fel plats. 

     - Fel som marker ATS som "tjänst" eller "ServiceError = TRUE" uppstod i Windows Virtual Desktop-tjänsten.
     - Fel som har marker ATS som "distribution" eller märkt "ServiceError = FALSe" skedde utanför Windows Virtual Desktop-tjänsten.
     - Mer information om ServiceError-taggen finns i [vanliga fel scenarier](diagnostics-role-service.md#common-error-scenarios).

- Källa: den här kategorin ger en mer detaljerad beskrivning av var felet uppstod.

     - Diagnostik: den tjänst roll som ansvarar för övervakning och rapportering av tjänste aktivitet, så att användarna kan se och diagnostisera distributions problem.

     - RDBroker: tjänst rollen som ansvarar för att dirigera distributions aktiviteter, bevara objektens tillstånd, verifiera autentisering med mera.

     - RDGateway: den tjänst roll som ansvarar för att hantera nätverks anslutningar mellan slutanvändare och virtuella datorer.

     - RDStack: en program varu komponent som är installerad på dina virtuella datorer för att tillåta att de kommunicerar med Windows Virtual Desktop-tjänsten.

     - Klient: program vara som körs på slutanvändarens dator och som tillhandahåller gränssnittet till Windows Virtual Desktop-tjänsten. Den visar listan över publicerade resurser och är värd för fjärr skrivbords anslutningen när du har gjort ett val.

Varje problem med diagnostik eller fel innehåller ett meddelande som förklarar vad som gick fel. Mer information om fel sökning av fel finns i [identifiera och diagnostisera problem med virtuella Windows-datorer](diagnostics-role-service.md).

## <a name="input-delay"></a>Inmatningsfördröjning

"Fördröjning av inaktivitet" i Azure Monitor för virtuella Windows-datorer innebär prestanda räknaren för ingångs fördröjning per process för varje session. På sidan värd prestanda på [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)konfigureras den här prestanda räknaren att skicka en rapport till tjänsten en gång var 30: e sekund. De här 30-sekundernas intervall kallas "Samples" och rapporterar det värsta fallet i fönstret. Värdena median och P95 återspeglar median och 95 percentilen i alla exempel.

Under **ingångs fördröjning per värd** kan du välja en värd rad för en session för att filtrera alla andra visuella objekt på sidan till den värden. Du kan också välja ett process namn för att filtrera median värdet för ingångs fördröjning över tid.

Vi sätter fördröjningar i följande kategorier:

- Felfri: under 150 millisekunder.
- Acceptabel: 150-500 millisekunder.
- Låg: 500 – 2 millisekunder (under 2 sekunder).
- Felaktig: över 2 000 millisekunder (2 sekunder och uppåt).

Mer information om hur räknaren för indata-fördröjning fungerar finns i [prestanda räknare](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)för användarindata.

##  <a name="monthly-active-users-mau"></a>Månatliga aktiva användare (MAU)

Det totala antalet användare som har startat en session under de senaste 28 dagarna. Om du lagrar data i 30 dagar eller mindre kan du se lägre än förväntade MAU-och anslutnings värden under perioder där du har färre än 28 dagars data tillgängliga.

## <a name="performance-counters"></a>Prestandaräknare

Prestanda räknare visar prestanda för maskin varu komponenter, operativ system och program.

I följande tabell visas de rekommenderade prestanda räknarna och de tidsintervall som Azure Monitor används för virtuella Windows-datorer:

|Prestanda räknar namn|Tidsintervall|
|---|---|
|Logisk disk (C:) \\ medel längd för diskkölängd|30 sekunder|
|Logisk disk (C:) \\ medel s/disk överföring|60 sekunder|
|Logisk disk (C:) \\ Aktuell diskkölängd|30 sekunder|
|Tillgängligt minne ( \* ) \\ megabyte|30 sekunder|
|Minnes ( \* ) \\ sidfel/SEK|30 sekunder|
|Minne ( \* ) \\ sidor/s|30 sekunder|
|Minne ( \* ) \\ % allokerade byte som används|30 sekunder|
|Fysisk disk ( \* ) \\ Gnm. diskkölängd|30 sekunder|
|Fysisk disk ( \* ) \\ medel s/disk läsning|30 sekunder|
|Fysisk disk ( \* ) \\ medel s/disk överföring|30 sekunder|
|Fysisk disk ( \* ) \\ medel s/disk skrivning|30 sekunder|
|Processor information (_Total) \\ % processor tid|30 sekunder|
|Terminal Services ( \* ) \\ aktiva sessioner|60 sekunder|
|Terminal Services ( \* ) \\ inaktiva sessioner|60 sekunder|
|Terminal Services ( \* ) \\ Totalt antal sessioner|60 sekunder|
|\*Fördröjning av användarindata per process ( \* ) \\ Max fördröjning|30 sekunder|
|\*Fördröjning för användarindata per session ( \* ) \\ Max fördröjning för indata|30 sekunder|
|RemoteFX Network ( \* ) \\ aktuell TCP-förval|30 sekunder|
|RemoteFX-nätverk ( \* ) \\ aktuell UDP-bandbredd|30 sekunder|

Mer information om hur du läser prestanda räknare finns i [Konfigurera prestanda räknare](../azure-monitor/agents/data-sources-performance-counters.md).

Mer information om prestanda räknare för indata-fördröjning finns i [prestanda räknare](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)för användarindata.

## <a name="potential-connectivity-issues"></a>Potentiella anslutnings problem

Möjliga anslutnings problem visar värdar, användare, publicerade resurser och klienter med en hög anslutnings fel frekvens. När du väljer ett "rapport efter"-filter kan du utvärdera ärendets allvarlighets grad genom att kontrol lera värdena i de här kolumnerna:

- Försök (Antal anslutnings försök)
- Resurser (antal publicerade appar eller skriv bord)
- Värdar (antal virtuella datorer)
- Klienter

Om du till exempel väljer filtret **per användare** kan du kontrol lera om du vill se varje användares anslutnings försök i kolumnen **försök** .

Om du märker att ett anslutnings problem sträcker sig över flera värdar, användare, resurser eller klienter, är det troligt att problemet påverkar hela systemet. Om det inte är det, är det ett mindre problem som har lägre prioritet.

Du kan också välja poster för att visa ytterligare information. Du kan se vilka värdar, resurser och klient versioner som var involverade i problemet. I visningen visas även eventuella fel som rapporteras under anslutnings försöken.

## <a name="round-trip-time-rtt"></a>Tur och retur-tid

Tur och retur-tid (för tur och retur) är en uppskattning av anslutningens tur och retur-tid mellan slutanvändarens plats och sessionens värd Azure-region. Om du vill se vilka platser som har den bästa svars tiden kan du leta upp den önskade platsen i [Windows-verktyget för virtuella Skriv bords verktyg](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Sessionshistorik

**Sessions** -objektet visar status för alla sessioner, anslutna och frånkopplade. **Inaktiva sessioner** visar bara de frånkopplade sessionerna.

## <a name="severity-0-alerts"></a>Allvarlighets grad 0-varningar

De mest brådskande objekten som du behöver ta hand om direkt. Om du inte löser problemen kan det leda till att distributionen av Windows virtuella datorer slutar fungera.

## <a name="time-to-connect"></a>Tid för att ansluta

Tiden för att ansluta är tiden mellan när en användare startar sin session och när de räknas som inloggade på tjänsten. Att upprätta nya anslutningar är att ta längre tid än att återupprätta befintliga anslutningar.

## <a name="user-report"></a>Användarrapport

På sidan användar rapport kan du Visa en enskild användares anslutnings historik och diagnostisk information. Varje användar rapport visar användnings mönster, feedback från användare och eventuella fel som användare har påträffat under sina sessioner. De flesta mindre problem kan lösas med feedback från användaren. Om du behöver gå djupare kan du också filtrera information om ett angivet anslutnings-ID eller en viss tids period.

## <a name="users-per-core"></a>Användare per kärna

Detta är antalet användare i varje virtuell dator kärna. Spårning av det maximala antalet användare per kärna över tid kan hjälpa dig att identifiera om miljön körs konsekvent med hög, låg eller variation av antal användare per kärna. Att veta hur många användare som är aktiva hjälper dig att effektivt kunna använda resurser och skala miljön.

## <a name="windows-event-logs"></a>Windows-händelseloggar

Händelse loggar i Windows är data källor som samlas in av Log Analytics agenter på virtuella Windows-datorer. Du kan samla in händelser från standard loggar som system och program samt anpassade loggar som skapats av program som du behöver övervaka.

I följande tabell visas de Windows-händelseloggar som krävs för Azure Monitor för virtuella Windows-datorer:

|Händelse namn|Händelsetyp|
|---|---|
|Program|Fel och varning|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin|Fel, varning och information|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operational|Fel, varning och information|
|System|Fel och varning|
| Microsoft-FSLogix – appar/drift|Fel, varning och information|
|Microsoft-FSLogix-Apps/admin|Fel, varning och information|

Mer information om Windows-händelseloggar finns i [Egenskaper för Windows Event-poster](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Azure Monitor för virtuella Windows-datorer kan du läsa följande artiklar:

- [Använd Azure Monitor för virtuella Windows-datorer för att övervaka distributionen](azure-monitor.md)
- [Azure Monitor för fel sökning av virtuella Windows-datorer](troubleshoot-azure-monitor.md)

Du kan också konfigurera Azure Advisor som hjälper dig att avgöra hur du ska lösa eller förhindra vanliga problem. Läs mer i [använda Azure Advisor med det virtuella Windows-skrivbordet](azure-advisor.md).

Om du behöver hjälp eller har frågor kan du titta närmare på våra community-resurser:

- Ställ frågor eller gör förslag till communityn på [Windows-TechCommunity för virtuella skriv bord](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- Information om hur du lämnar feedback finns i [fel söknings översikt, feedback och support för Windows Virtual Desktop](troubleshoot-set-up-overview.md#report-issues).

- Du kan också lämna feedback för virtuella Windows-datorer i [hubben för Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
