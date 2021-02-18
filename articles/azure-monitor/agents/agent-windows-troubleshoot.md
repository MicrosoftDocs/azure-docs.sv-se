---
title: Felsöka problem med Log Analytics agent för Windows
description: Beskriv symptom, orsaker och lösningar för de vanligaste problemen med Log Analytics agent för Windows i Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4e2531d511193586ef4605cc3732968b6db28d9f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621422"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Felsöka problem med Log Analytics-agenten för Windows 

Den här artikeln innehåller fel söknings fel som kan uppstå i Log Analytics-agenten för Windows i Azure Monitor och ger förslag på möjliga lösningar för att lösa dem.

Om inget av dessa steg fungerar för dig, är följande Support kanaler också tillgängliga:

* Kunder med Premier Support-förmåner kan öppna en support förfrågan med [Premier](https://premier.microsoft.com/).
* Kunder med support avtal för Azure kan öppna en support förfrågan [i Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Besök sidan Log Analytics feedback och granska skickade idéer och buggar [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) eller skicka en ny fil. 

## <a name="log-analytics-troubleshooting-tool"></a>Log Analytics fel söknings verktyg

Verktyget Log Analytics-agent för Windows-Felsökning är en samling PowerShell-skript som har utformats för att hjälpa till att hitta och diagnostisera problem med Log Analytics-agenten. Den medföljer automatiskt med agenten vid installationen. Att köra verktyget bör vara det första steget när du diagnostiserar ett problem.

### <a name="how-to-use"></a>Använd så här
1. Öppna PowerShell-prompt som administratör på datorn där Log Analytics-agenten är installerad.
1. Navigera till den katalog där verktyget finns.
   * `cd "C:\Program Files\Microsoft Monitoring Agent\Agent\Troubleshooter"`
1. Kör huvud skriptet med följande kommando:
   * `.\GetAgentInfo.ps1`
1. Välj ett fel söknings scenario.
1. Följ anvisningarna i-konsolen. (Obs! spårnings loggar steg kräver manuella åtgärder för att stoppa logg insamling. Baserat på problemets reproducerbarhet väntar du tills varaktigheten och trycker på ' för att stoppa logg insamling och fortsätter till nästa steg).

   Platsen för resultat filen loggas när den slutförs och ett nytt fönster för Explorer-fönster markeras.

### <a name="installation"></a>Installation
Fel söknings verktyget inkluderas automatiskt vid installation av Log Analytics-agentens build-10.20.18053.0 och senare.

### <a name="scenarios-covered"></a>Scenarier som omfattas
Nedan visas en lista över scenarier som kontrol leras av fel söknings verktyget:

- Agenten rapporterar inte data eller pulsslags data saknas
- Distributionen av agent tillägget kunde inte utföras
- Agent krasch
- Agent som förbrukar hög processor/minne
- Installations-/avinstallations problem
- Problem med anpassade loggar
- Problem med OMS-Gateway
- Problem med prestanda räknare
- Samla in alla loggar

>[!NOTE]
>Kör fel söknings verktyget när det uppstår problem. När du öppnar ett ärende kan du med loggarna ursprungligen hjälpa Support teamet att felsöka problemet snabbare.

## <a name="important-troubleshooting-sources"></a>Viktiga fel söknings källor

 För att hjälpa till med fel sökning av problem som rör Log Analytics agent för Windows loggar agenten händelser till händelse loggen i Windows, särskilt under *program-och Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Anslutningsproblem

Om agenten kommunicerar via en proxyserver eller brand vägg kan det finnas begränsningar för att förhindra kommunikation från käll datorn och tjänsten Azure Monitor. Om kommunikationen är blockerad, på grund av felaktig konfiguration, kan registrering med en arbets yta Miss lyckas vid försök att installera agenten eller konfigurera agenten efter installationen så att den rapporterar till en annan arbets yta. Agent kommunikation kan Miss lyckas efter en lyckad registrering. I det här avsnittet beskrivs metoderna för att felsöka den här typen av problem med Windows-agenten.

Kontrol lera att brand väggen eller proxyservern har kon figurer ATS för att tillåta följande portar och URL: er som beskrivs i följande tabell. Bekräfta också att HTTP-kontroll inte har Aktiver ATS för webb trafik, eftersom den kan förhindra en säker TLS-kanal mellan agenten och Azure Monitor.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Utgående|Yes |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Yes |  
|*.blob.core.windows.net |Port 443 |Utgående|Yes |  
|*. agentsvc.azure-automation.net |Port 443 |Utgående|Yes |  

För brand Väggs information som krävs för Azure Government, se [Azure Government hantering](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera med Automation-tjänsten för att använda Runbooks eller hanterings lösningar i din miljö, måste den ha åtkomst till port numret och de URL: er som beskrivs i [Konfigurera ditt nätverk för Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Det finns flera sätt som du kan kontrol lera om agenten lyckas kommunicera med Azure Monitor.

- Aktivera [Azure Log Analytics agenthälsa-utvärdering](../insights/solution-agenthealth.md) på arbets ytan. På instrument panelen Agenthälsa visar du kolumnen **antal agenter** som inte svarar för att snabbt se om agenten visas.  

- Kör följande fråga för att bekräfta att agenten skickar ett pulsslag till arbets ytan som den är konfigurerad att rapportera till. Ersätt `<ComputerName>` med datorns faktiska namn.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Om datorn lyckas kommunicera med tjänsten ska frågan returnera ett resultat. Om frågan inte returnerade ett resultat ska du först kontrol lera att agenten har kon figurer ATS för att rapportera till rätt arbets yta. Om den har kon figurer ATS korrekt går du till steg 3 och söker i Windows-händelseloggen för att identifiera om agenten loggar in vilket problem som kan förhindra att den kommunicerar med Azure Monitor.

- En annan metod för att identifiera ett anslutnings problem är genom att köra verktyget **TestCloudConnectivity** . Verktyget installeras som standard med agenten i mappen *%systemroot%\Program Files\Microsoft Monitoring Agent\Agent*. Från en upphöjd kommando tolk navigerar du till mappen och kör verktyget. Verktyget returnerar resultaten och högdagrar där testet misslyckades (till exempel om det var relaterat till en viss port/URL som blockerades). 

    ![TestCloudConnection verktygs körnings resultat](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrera *Operations Manager* händelse loggen efter **händelse källor**  -  *Hälsotjänst moduler*, *HealthService* och *service Connector* och filtrera efter **händelse nivå** *Varning* och *fel* för att bekräfta om den har skrivna händelser från följande tabell. Om de är det, granskar du lösnings stegen som ingår för varje möjlig händelse.

    |Händelse-ID |Källa |Beskrivning |Lösning |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Hälsotjänst |Det gick inte att ansluta till tjänsten från agenten |Felet kan uppstå om agenten inte kan kommunicera direkt eller via en brand vägg/proxyserver till Azure Monitor tjänsten. Verifiera inställningarna för agent proxy eller att nätverks brand väggen/proxyn tillåter TCP-trafik från datorn till tjänsten.|
    |2138 |Hälsotjänst moduler |Proxy kräver autentisering |Konfigurera agentens proxyinställningar och ange det användar namn/lösen ord som krävs för att autentisera med proxyservern. |
    |2129 |Hälsotjänst moduler |Misslyckad anslutning/TLS-förhandling |Kontrol lera inställningarna för TCP/IP för nätverkskortet och agentens proxyinställningar.|
    |2127 |Hälsotjänst moduler |Fel vid sändning av mottagna data felkod |Om det bara inträffar regelbundet under dagen kan det vara en slumpmässig avvikelse som kan ignoreras. Övervaka för att förstå hur ofta det sker. Om det inträffar ofta under dagen kontrollerar du först nätverks konfigurationen och proxyinställningarna. Om beskrivningen inkluderar HTTP-felkod 404 och det är första gången som agenten försöker skicka data till tjänsten, kommer den att innehålla ett 500-fel med en inre 404-felkod. 404 betyder att lagrings området för den nya arbets ytan fortfarande håller på att tillhandahållas. Vid nästa försök kommer data att skrivas till arbets ytan som förväntat. Ett HTTP-fel 403 kan tyda på problem med behörighet eller autentiseringsuppgifter. Det finns mer information i 403-fel som hjälper dig att felsöka problemet.|
    |4000 |Service Connector |DNS-namnmatchning misslyckades |Datorn kunde inte matcha Internet adressen som användes när data skickades till tjänsten. Detta kan vara inställningar för DNS-matchare på datorn, felaktiga proxyinställningar eller kanske ett tillfälligt DNS-problem med leverantören. Om det inträffar regelbundet kan det bero på ett tillfälligt nätverksrelaterade problem.|
    |4001 |Service Connector |Det gick inte att ansluta till tjänsten. |Felet kan uppstå om agenten inte kan kommunicera direkt eller via en brand vägg/proxyserver till Azure Monitor tjänsten. Verifiera inställningarna för agent proxy eller att nätverks brand väggen/proxyn tillåter TCP-trafik från datorn till tjänsten.|
    |4002 |Service Connector |Tjänsten returnerade HTTP-statuskod 403 som svar på en fråga. Kontakta tjänst administratören för hälso tillståndet för tjänsten. Frågan kommer att göras senare. |Det här felet skrivs under agentens inledande registrerings fas och du ser en URL som liknar följande: *https:// \<workspaceID> . OMS.OpInsights.Azure.com/AgentService.svc/AgentTopologyRequest*. Felkoden 403 betyder att den är förbjuden och kan orsakas av ett felanget arbetsyte-ID eller nyckel, eller så är data och tid felaktigt på datorn. Om tiden är +/-15 minuter från aktuell tid, Miss lyckas onboarding. Korrigera detta genom att uppdatera datum och/eller tidszon för Windows-datorn.|

## <a name="data-collection-issues"></a>Problem med data insamling

När agenten har installerats och rapporter ATS till den konfigurerade arbets ytan eller arbets ytorna kan det sluta ta emot konfiguration, insamling eller vidarebefordran av prestanda, loggar eller annan information till tjänsten beroende på vad som är aktiverat och mål datorn. Det är nödvändigt att fastställa om:

- Är det en specifik datatyp eller alla data som inte är tillgängliga i arbets ytan?
- Är data typen som anges av en lösning eller anges som en del av konfigurationen av data insamling för arbets yta?
- Hur många datorer påverkas? Är det en enstaka eller flera datorer som rapporterar till arbets ytan?
- Fungerade den och slutade den vid en viss tidpunkt, eller har den aldrig samlats in? 
- Är loggs öknings frågan du använder syntaktiskt korrekt? 
- Har agenten någonsin tagit emot sin konfiguration från Azure Monitor?

Det första steget i fel sökning är att avgöra om datorn skickar en pulsslags händelse.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Om frågan returnerar resultat måste du bestämma om en viss datatyp inte samlas in och vidarebefordras till tjänsten. Detta kan orsakas av att agenten inte tar emot en uppdaterad konfiguration från tjänsten, eller något annat problem som hindrar agenten från att fungera normalt. Utför följande steg för att ytterligare felsöka.

1. Öppna en kommando tolk med förhöjd behörighet på datorn och starta om Agent tjänsten genom att skriva `net stop healthservice && net start healthservice` .
2. Öppna händelse loggen *Operations Manager* och Sök efter **händelse-id** *7023, 7024, 7025, 7028* och *1210* från **händelse källan** *HealthService*.  Dessa händelser anger att agenten tar emot konfiguration från Azure Monitor och att datorn övervakas aktivt. Händelse beskrivningen för händelse-ID 1210 anges också på den sista raden alla lösningar och insikter som ingår i övervaknings området på agenten.  

    ![Beskrivning av händelse-ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Om du efter flera minuter inte ser de förväntade data i frågeresultatet eller visualiseringen, beroende på om du visar data från en lösning eller insikter *Operations Manager* , kan du söka efter **händelse källor** i *HealthService* och *Hälsotjänst moduler* och filtrera efter **händelse nivå** *Varning* och *fel* för att kontrol lera om den har skrivna händelser från följande tabell.

    |Händelse-ID |Källa |Beskrivning |Lösning |
    |---------|-------|------------|
    |8000 |HealthService |Den här händelsen anger om ett arbets flöde som rör prestanda, händelse eller annan insamlad datatyp inte kan vidarebefordra till tjänsten för inmatning till arbets ytan. | Händelse-ID 2136 från käll-HealthService skrivs tillsammans med den här händelsen och kan tyda på att agenten inte kan kommunicera med tjänsten, eventuellt på grund av felaktig konfiguration av proxy-och autentiseringsinställningar, nätverks avbrott eller nätverks brand vägg/proxy tillåter inte TCP-trafik från datorn till tjänsten.| 
    |10102 och 10103 |Hälsotjänst moduler |Det gick inte att matcha data källan i arbets flödet. |Detta kan inträffa om den angivna prestanda räknaren eller instansen inte finns på datorn eller felaktigt har definierats i data inställningarna för arbets ytan. Om det här är en användardefinierad [prestanda räknare](data-sources-performance-counters.md#configuring-performance-counters)kontrollerar du att den angivna informationen följer rätt format och finns på mål datorerna. |
    |26002 |Hälsotjänst moduler |Det gick inte att matcha data källan i arbets flödet. |Detta kan inträffa om den angivna Windows-händelseloggen inte finns på datorn. Det här felet kan ignoreras om datorn inte förväntas ha den här händelse loggen registrerad, annars om det är en användardefinierad [händelse logg](data-sources-windows-events.md#configuring-windows-event-logs)kontrollerar du att den angivna informationen är korrekt. |
