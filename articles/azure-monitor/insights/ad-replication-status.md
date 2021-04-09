---
title: Övervaka Active Directory replikeringsstatus
description: Active Directory-replikeringsstatus Solution Pack övervakar regelbundet din Active Directory-miljö för eventuella replikeringsfel.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: c99ad16e119c4262aa6d9d645b2457cdd46061b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700685"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Övervaka Active Directory replikeringsstatus med Azure Monitor

![AD-replikeringsstatus symbol](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory är en viktig del av företagets IT-miljö. För att säkerställa hög tillgänglighet och hög prestanda har varje domänkontrollant en egen kopia av Active Directory databasen. Domänkontrollanter replikeras med varandra för att kunna sprida ändringar i företaget. Fel i den här replikeringsrelationen kan orsaka en rad olika problem i företaget.

AD-replikeringsstatuss lösningen övervakar regelbundet din Active Directory-miljö för eventuella replikeringsfel.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

### <a name="prerequisites"></a>Förutsättningar

* AD-replikeringsstatus-lösningen kräver en version av .NET Framework 4.6.2 som stöds eller som är installerad på varje dator som har Log Analytics agent för Windows (kallas även Microsoft Monitoring Agent (MMA)) installerad.  Agenten används av System Center 2016-Operations Manager, Operations Manager 2012 R2 och Azure Monitor.
* Lösningen stöder domänkontrollanter som kör Windows Server 2008 och 2008 R2, Windows Server 2012 och 2012 R2 och Windows Server 2016.
* En Log Analytics arbets yta där du kan lägga till Active Directory hälso kontroll från Azure Marketplace i Azure Portal. Ingen ytterligare konfiguration krävs.


### <a name="install-agents-on-domain-controllers"></a>Installera agenter på domänkontrollanter
Du måste installera agenter på domänkontrollanter som är medlemmar i domänen för att kunna utvärderas. Eller så måste du installera agenter på medlems servrar och konfigurera agenterna för att skicka AD Replication-data till Azure Monitor. Information om hur du ansluter Windows-datorer till Azure Monitor finns i [ansluta Windows-datorer till Azure Monitor](../agents/agent-windows.md). Om din domänkontrollant redan är en del av en befintlig System Center Operations Manager-miljö som du vill ansluta till Azure Monitor, se [ansluta Operations Manager till Azure Monitor](../agents/om-agents.md).

### <a name="enable-non-domain-controller"></a>Aktivera icke-domänkontrollant
Om du inte vill ansluta någon av domän kontrol Lanterna direkt till Azure Monitor kan du använda en annan dator i domänen som är ansluten till Azure Monitor för att samla in data för AD-replikeringsstatus lösnings paketet och skicka data.

1. Kontrol lera att datorn är medlem i den domän som du vill övervaka med hjälp av AD-replikeringsstatus-lösningen.
2. [Anslut Windows-datorn för att Azure Monitor](../agents/om-agents.md) eller [Anslut den med din befintliga Operations Manager miljö till Azure Monitor](../agents/om-agents.md), om den inte redan är ansluten.
3. På den datorn anger du följande register nyckel:<br>Nyckel: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName> \Solutions\ADReplication**<br>Värde: **IsTarget**<br>Värde data: **True**

   > [!NOTE]
   > Ändringarna börjar inte gälla förrän du startar om tjänsten Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Installera lösning
   > Följ processen som beskrivs i [installera en övervaknings lösning](solutions.md#install-a-monitoring-solution) för att lägga till **Active Directory-replikeringsstatus** -lösningen i Log Analytics-arbetsytan. Det krävs ingen ytterligare konfiguration.


## <a name="ad-replication-status-data-collection-details"></a>Information om AD-replikeringsstatus data insamling
I följande tabell visas metoder för data insamling och annan information om hur data samlas in för AD-replikeringsstatus.

| plattform | Direkt agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM agent-data som skickats via hanterings grupp | samlings frekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |var femte dag |



## <a name="understanding-replication-errors"></a>Förstå replikeringsfel

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Panelen AD-replikeringsstatus visar hur många replikeringsfel du för närvarande har. **Kritiska replikeringsfel** är fel som ligger på eller över 75% av tombstone- [livstiden](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10)) för Active Directory skogen.

![AD-replikeringsstatus panel](./media/ad-replication-status/oms-ad-replication-tile.png)

När du klickar på panelen kan du Visa mer information om felen.
![AD-replikeringsstatus instrument panel](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status för mål server och käll Server status
De här kolumnerna visar status för mål servrar och käll servrar som har fel vid replikering. Antalet efter varje namn på domänkontrollanten anger antalet replikeringsfel på domänkontrollanten.

Felen för både mål servrar och käll servrar visas eftersom vissa problem är enklare att felsöka från käll serverns perspektiv och andra från mål servern.

I det här exemplet kan du se att många mål servrar har ungefär samma antal fel, men det finns en käll Server (ADDC35) som har många fler fel än alla andra. Det är troligt att det finns problem med ADDC35 som gör att det inte går att skicka data till replikeringspartner. Att åtgärda problemen på ADDC35 kan lösa många av de fel som visas i mål server delen.

### <a name="replication-error-types"></a>Fel typer för replikering
Det här avsnittet innehåller information om vilka typer av fel som identifieras i företaget. Varje fel har en unik numerisk kod och ett meddelande som kan hjälpa dig att avgöra orsaken till felet.

Ring högst upp ger dig en uppfattning om vilka fel som visas mer och mindre ofta i din miljö.

Det visar när flera domänkontrollanter upplever samma replikeringsfel. I det här fallet kanske du kan identifiera eller identifiera en lösning på en domänkontrollant och sedan upprepa den på andra domänkontrollanter som påverkas av samma fel.

### <a name="tombstone-lifetime"></a>Tombstone-livstid
Tombstone-livstiden avgör hur länge ett borttaget objekt, som kallas tombstone, bevaras i Active Directory databasen. När ett borttaget objekt passerar tombstone-livstiden tar en skräp insamlings process automatiskt bort från den Active Directory databasen.

Standard-tombstone-livstiden är 180 dagar för de senaste versionerna av Windows, men den var 60 dagar i äldre versioner och kan ändras explicit av en Active Directory administratör.

Det är viktigt att veta om du har replikeringsfel som närmar sig eller som har passerat tombstone-livstiden. Om två domänkontrollanter upplever ett replikeringsfel som fortsätter förbi tombstone-livstiden inaktive ras replikeringen mellan de två domän kontrol Lanterna, även om det underliggande replikeringsfel är löst.

Med hjälp av tombstone-livstiden kan du identifiera platser där inaktiverad replikering är en risk för att det sker. Varje fel i kategorin **över 100% TLS** representerar en partition som inte har repliker ATS mellan käll-och mål servern för minst tombstone-livstiden för skogen.

I den här situationen räcker det inte att åtgärda replikeringen för att lösa problemet. Du måste minst manuellt undersöka för att identifiera och rensa kvarvarande objekt innan du kan starta om replikeringen. Du kan även behöva inaktivera en domänkontrollant.

Förutom att identifiera eventuella replikeringsfel som tidigare har gått förbi tombstone-livstiden, vill du också ta hänsyn till eventuella fel som ligger i kategorierna **50-75% TLS** eller **75-100% TLS** .

Detta är fel som är tydligt kvarvarande, inte tillfälligt, så att de förmodligen behöver åtgärdas. Den goda nyheten är att de ännu inte har nått tombstone-livstiden. Om du åtgärdar problemen och *innan* de når tombstone-livstiden kan replikeringen starta om med minimala manuella åtgärder.

Som tidigare nämnts visar instrument panelens panel för AD-replikeringsstatuss lösningen antalet *kritiska* replikeringsfel i din miljö, som definieras som fel som ligger över 75% av tombstone-livstiden (inklusive fel som är över 100% av TLS). Sträva efter att behålla det här talet vid 0.

> [!NOTE]
> Alla beräkningar för tombstone-procent i procent baseras på den faktiska tombstone-livstiden för Active Directory skogen, så du kan lita på att dessa procent andelar är korrekta, även om du har ett anpassat värde för tombstone-livstid.
>
>

### <a name="ad-replication-status-details"></a>Status information för AD-replikering
När du klickar på ett objekt i en av listorna visas ytterligare information om den med hjälp av en logg fråga. Resultatet filtreras så att endast de fel som är relaterade till objektet visas. Om du till exempel klickar på den första domänkontrollanten i listan under **mål server status (ADDC02)** visas frågeresultat filtrerade för att visa fel med domänkontrollanten som visas som mål server:

![Status fel för AD-replikering i frågeresultat](./media/ad-replication-status/oms-ad-replication-search-details.png)

Härifrån kan du filtrera ytterligare, ändra logg frågan och så vidare. Mer information om hur du använder logg frågor i Azure Monitor finns i [analysera loggdata i Azure Monitor](../logs/log-query-overview.md).

I fältet **HelpLink** visas URL: en till en TechNet-sida med ytterligare information om felet. Du kan kopiera och klistra in länken i webbläsarfönstret för att se information om fel sökning och korrigering av felet.

Du kan också exportera resultaten till Excel genom att klicka på **Exportera** . Genom att exportera data kan du visualisera replikeringsdata på ett valfritt sätt som du vill.

![status fel i exporterad AD-replikering i Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD-replikeringsstatus vanliga frågor och svar
**F: hur ofta uppdateras status data för AD Replication?**
A: informationen uppdateras var femte dag.

**F: finns det ett sätt att konfigurera hur ofta dessa data ska uppdateras?**
A: inte just nu.

**F: behöver jag lägga till alla mina domänkontrollanter till min Log Analytics-arbetsyta för att se replikeringsstatus?**
A: Nej, endast en enda domänkontrollant måste läggas till. Om du har flera domänkontrollanter på arbets ytan Log Analytics skickas data från alla dem till Azure Monitor.

**F: Jag vill inte lägga till några domänkontrollanter i min Log Analytics-arbetsyta. Kan jag fortfarande använda AD-replikeringsstatus lösningen?**

S: Ja. Du kan ange värdet för en register nyckel för att aktivera det. Se [Aktivera icke-domänkontrollant](#enable-non-domain-controller).

**F: Vad är namnet på processen som utför data insamlingen?**
A: AdvisorAssessment.exe

**F: hur lång tid tar det för data att samlas in?**
A: data insamlings tiden beror på Active Directorys miljöns storlek, men det tar vanligt vis mindre än 15 minuter.

**F: vilken typ av data samlas in?**
A: replikeringsinformation samlas in via LDAP.

**F: finns det ett sätt att konfigurera när data samlas in?**
A: inte just nu.

**F: vilka behörigheter behöver jag för att samla in data?**
A: normala användar behörigheter till Active Directory räcker.

## <a name="troubleshoot-data-collection-problems"></a>Felsöka problem med data insamling
För att kunna samla in data kräver AD-replikeringsstatus Solution Pack att minst en domänkontrollant är ansluten till Log Analytics-arbetsytan. Innan du ansluter en domänkontrollant visas ett meddelande som anger att **data fortfarande samlas in**.

Om du behöver hjälp med att ansluta en av dina domänkontrollanter kan du Visa dokumentation på [Anslut Windows-datorer till Azure Monitor](../agents/om-agents.md). Alternativt, om domänkontrollanten redan är ansluten till en befintlig System Center Operations Managers miljö, kan du Visa dokumentationen vid [anslutning System Center Operations Manager till Azure Monitor](../agents/om-agents.md).

Om du inte vill ansluta någon av domän kontrol Lanterna direkt till Azure Monitor eller System Center Operations Manager, se [Aktivera icke-domänkontrollant](#enable-non-domain-controller).

## <a name="next-steps"></a>Nästa steg
* Använd [logg frågor i Azure Monitor](../logs/log-query-overview.md) om du vill visa detaljerad information om Active Directory replikeringsstatus.

