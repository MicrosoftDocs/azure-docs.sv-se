---
title: Drift säkerhet i Azure | Microsoft Docs
description: Lär dig att Microsoft Azure övervaka loggar, dess tjänster och hur det fungerar genom att läsa den här översikten.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: ab54243eb7109965941cf60edd9358133550c7d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046648"
---
# <a name="azure-operational-security"></a>Drift säkerhet i Azure
## <a name="introduction"></a>Introduktion

### <a name="overview"></a>Översikt
Vi vet att säkerheten är ett jobb i molnet och hur viktigt det är att du hittar korrekt och tidsödande information om Azure-säkerhet. Ett av de bästa orsakerna till att använda Azure för dina program och tjänster är att dra nytta av den breda uppsättningen säkerhets verktyg och funktioner som är tillgängliga. Med dessa verktyg och funktioner kan du skapa säkra lösningar på den säkra Azure-plattformen. Windows Azure måste tillhandahålla konfidentialitet, integritet och tillgänglighet för kund information, samtidigt som du aktiverar öppet ansvar.

För att hjälpa kunderna att bättre förstå matrisen med säkerhets kontroller som implementeras inom Microsoft Azure från både kundens och Microsofts operativa perspektiv, är det här white paper, "Azure-arbetssäkerhet", skrivet som ger en omfattande titt på den drift säkerhet som är tillgänglig med Windows Azure.

### <a name="azure-platform"></a>Azure-plattform
Azure är en offentlig moln tjänst plattform som har stöd för ett brett urval av operativ system, programmeringsspråk, ramverk, verktyg, databaser och enheter. Den kan köra Linux-behållare med Docker-integrering, Bygg appar med Java Script, python, .NET, PHP, Java och Node.js; Bygg Server delar för iOS-, Android-och Windows-enheter. Azure Cloud Service stöder samma teknik miljon tals utvecklare och IT-proffs är redan förlitande på och litar på.

När du bygger på, eller migrerar IT-tillgångar till, förlitar sig en offentlig moln tjänst leverantör på organisationens möjligheter att skydda dina program och data med tjänsterna och de kontroller som de tillhandahåller för att hantera säkerheten för dina molnbaserade till gångar.

Azures infrastruktur är utformad från anläggningen till program för att vara värd för miljon tals kunder samtidigt, och det ger en säker grund där företag kan uppfylla sina säkerhets krav. Dessutom ger Azure dig en mängd olika konfigurerbara säkerhets alternativ och möjligheten att kontrol lera dem så att du kan anpassa säkerheten för att uppfylla de unika kraven i din organisations distributioner. I det här dokumentet får du hjälp med att förstå hur Azures säkerhetsfunktioner kan hjälpa dig att uppfylla dessa krav.

### <a name="abstract"></a>Sammanfattning
Azures drift säkerhet syftar på tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda data, program och andra till gångar i Microsoft Azure. Azures operativa säkerhet bygger på ett ramverk som införlivar den kunskap som du fått via olika funktioner som är unika för Microsoft, inklusive Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-programmet och djupgående medvetenhet om cybersäkerhet Threat landskap.

Den här white paper beskriver Microsofts metod för Azures drift säkerhet inom Microsoft Azure moln plattform och omfattar följande tjänster:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-introduction.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure Storage analys](/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure övervaka loggar

Microsoft Azure övervaka loggar är IT-hanterings lösningen för Hybrid molnet. Azure Monitor loggar som används separat eller för att utöka din befintliga System Center-distribution ger dig maximal flexibilitet och kontroll för molnbaserad hantering av infrastrukturen.

![Azure Monitor-loggar](./media/operational-security/azure-operational-security-fig1.png)

Med Azure Monitor loggar kan du hantera vilken instans som helst i alla moln, inklusive lokalt, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrens kraftigare lösningar. Azure Monitors loggar har skapats för den molnbaserade världen och erbjuder en ny metod för att hantera företaget som är det snabbaste, mest kostnads effektiva sättet att möta nya affärs utmaningar och hantera nya arbets belastningar, program och moln miljöer.

### <a name="azure-monitor-services"></a>Azure Monitor tjänster

Huvud funktionerna i Azure Monitor loggar tillhandahålls av en uppsättning tjänster som körs i Azure. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

| Tjänst  | Beskrivning|
| :------------- | :-------------|
| Azure Monitor-loggar | Övervaka och analysera tillgänglighet och prestanda för olika resurser inklusive fysiska och virtuella datorer. |
|Automation | Automatisera manuella processer och tillämpa konfigurationer för fysiska och virtuella datorer. |
| Backup | Säkerhetskopiera och återställa viktiga data. |
| Site Recovery | Ge hög tillgänglighet för viktiga program. |

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](https://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervaknings tjänster genom att samla in data från hanterade resurser till en central lagrings plats. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.


Med den här metoden kan du konsolidera data från olika källor, så att du kan kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.


![Diagram som visar data konsolidering från olika källor, så att du kan kombinera data från dina Azure-tjänster med din befintliga lokala miljö.](./media/operational-security/azure-operational-security-fig2.png)

Tjänsten Azure Monitor hanterar dina molnbaserade data på ett säkert sätt med hjälp av följande metoder:
-   data uppdelning
-   datakvarhållning
-   fysisk säkerhet
-   incident hantering
-   Fastställ
-   säkerhets standard certifieringar

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) tillhandahåller tjänster för säkerhets kopiering och återställning av data och ingår i Azure Monitors Svit med produkter och tjänster.
Tjänsten skyddar dina programdata och sparar dem i åratal utan stora investeringar och med minimala driftkostnader. Den kan säkerhetskopiera data från fysiska och virtuella Windows-servrar utöver program arbets belastningar som SQL Server och SharePoint. Den kan också användas av [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) för att replikera skyddade data till Azure för redundans och långsiktig lagring.


Skyddade data i Azure Backup lagras i ett säkerhetskopieringsvalv som finns i en viss geografisk region. Data replikeras inom samma region och kan också, beroende på typen av valv, replikeras till en annan region för ytterligare återhämtning.

### <a name="management-solutions"></a>Hanteringslösningar
[Azure Monitor](../../security-center/security-center-introduction.md) är Microsofts molnbaserade IT-hanterings lösning som hjälper dig att hantera och skydda din lokala infrastruktur och moln infrastruktur.


[Hanterings lösningar](../../azure-monitor/insights/solutions.md) är förpaketerade uppsättningar med logikar som implementerar ett visst hanterings scenario med hjälp av en eller flera Azure Monitor-tjänster. Olika lösningar är tillgängliga från Microsoft och från partner som du enkelt kan lägga till i din Azure-prenumeration för att öka värdet på din investering i Azure Monitor. Som partner kan du skapa egna lösningar som har stöd för dina program och tjänster och ge dem till användare via Azure Marketplace eller snabb starts mallar.


![Hanteringslösningar](./media/operational-security/azure-operational-security-fig4.png)

Ett exempel på en lösning som använder flera tjänster för att tillhandahålla ytterligare funktioner är den [uppdateringshantering lösningen](../../automation/update-management/overview.md). Den här lösningen använder [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md) -agenten för Windows och Linux för att samla in information om nödvändiga uppdateringar på varje agent. Den skriver dessa data till lagrings platsen för Azure Monitor loggar där du kan analysera den med en inkluderad instrument panel.

När du skapar en distribution används Runbooks i [Azure Automation](../../automation/automation-intro.md) för att installera nödvändiga uppdateringar. Du hanterar hela processen i portalen och du behöver inte tänka på de bakomliggande detaljerna.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper till att skydda dina Azure-resurser. Den ger integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer. I tjänsten kan du definiera principer som inte bara gäller för dina Azure-prenumerationer, utan även för [resurs grupper](../../azure-resource-manager/management/overview.md#resource-groups), så att du kan vara mer detaljerad.

### <a name="security-policies-and-recommendations"></a>Säkerhetsprinciper och säkerhetsrekommendationer

En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser i en viss prenumeration eller resursgrupp.

I Security Center skapar du principer baserat på företagets säkerhetsbehov, hur informationen ska användas och hur känslig den är.

![Säkerhetsprinciper och säkerhetsrekommendationer](./media/operational-security/azure-operational-security-fig5.png)


Principer som aktive ras på prenumerations nivå sprids automatiskt till alla resurs grupper i prenumerationen som visas i diagrammet på höger sida:


### <a name="data-collection"></a>Datainsamling

Security Center samlar in data från dina virtuella datorer (VM) för att utvärdera deras säkerhetstillstånd, ange säkerhetsrekommendationer och varna för hot. När din första åtkomst Security Center är data insamling aktiverat på alla virtuella datorer i din prenumeration. Insamling av data rekommenderas, men du kan avanmäla dig genom att stänga av insamling av data i Security Center-principen.

### <a name="data-sources"></a>Datakällor

- Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillstånd, identifiera säkerhetsproblem, rekommendera lösningar och upptäcka aktiva hot:

-   Azure-tjänster: Använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.

- Nätverkstrafik: Använder samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel källans/målets IP-adress/port, paketstorlek och nätverksprotokoll.

-   Partnerlösningar: Använder säkerhetsaviseringar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod.

-   Virtual Machines: Använder information om konfigurationer och säkerhetshändelser, till exempel händelser och granskningsloggar i Windows, IIS-loggar, syslog-meddelanden och kraschdumpfiler från dina virtuella datorer.

### <a name="data-protection"></a>Dataskydd

Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

-   **Datauppdelning**: Data lagras logiskt och separat på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

-   **Data åtkomst**: för att ge säkerhets rekommendationer och undersöka möjliga säkerhetshot kan Microsofts personal komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive krasch dum par, process skapande händelser, ögonblicks bilder av VM-diskar och artefakter, som oavsiktligt kan inkludera kunddata eller personliga data från dina virtuella datorer. Vi följer [villkoren för Microsoft Online Services och sekretess policyn](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), vilket innebär att Microsoft inte använder kunddata eller härleder information från dem för reklam eller liknande kommersiella ändamål.

-   **Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Dataplats

Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång. Azure Security Center utför den här analysen inom samma geografiska region som arbetsytan och tar bort de tillfälliga kopiorna när analysen är klar. Artefakter lagras centralt i samma region som den virtuella datorn.

-   **Dina lagrings konton**: ett lagrings konto anges för varje region där virtuella datorer körs. Det gör att du kan lagra data i samma region som den virtuella dator som data samlas in från.

-   **Azure Security Center Storage**: Information om säkerhetsvarningar, inklusive partneraviseringar, rekommendationer och status för säkerhetstillstånd lagras centralt (för närvarande i USA). Den här informationen kan omfatta relaterad konfigurationsinformation och säkerhetshändelser som samlas in från dina virtuella datorer för att ge dig säkerhetsvarningar, rekommendationer eller säkerhetsrelaterad statusinformation.


## <a name="azure-monitor"></a>Azure Monitor

Med säkerhets-och gransknings lösningen [Azure Monitor loggas](../../security-center/security-center-remediate-recommendations.md) kan den aktivt övervaka alla resurser, vilket kan hjälpa dig att minimera påverkan av säkerhets incidenter. Azure Monitor loggar Säkerhet och granskning har säkerhets domäner som kan användas för att övervaka resurser. Säkerhets domänen ger snabb åtkomst till alternativ för säkerhets övervakning. följande domäner omfattas i mer information:

-   Utvärdering av skadlig kod
-   Kontroll av uppdateringar
-   Identitet och åtkomst.

Azure Monitor tillhandahåller länkar till information om vissa typer av resurser. Den erbjuder visualisering, frågor, routning, avisering, automatisk skalning och automatisering av data både från Azure-infrastrukturen (aktivitets loggen) och varje enskild Azure-resurs (diagnostikloggar).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Moln program är komplexa med många rörliga delar. Övervakning tillhandahåller data för att säkerställa att programmet hålls igång i felfritt tillstånd. Det hjälper dig också att Stave av potentiella problem eller att felsöka tidigare.

Dessutom kan du använda övervaknings data för att få djupgående insikter om ditt program. Den kunskapen kan hjälpa dig att förbättra programmets prestanda eller hanterbarhet, eller automatisera åtgärder som annars skulle kräva manuell åtgärd.

### <a name="azure-activity-log"></a>Azure-aktivitetslogg


Det är en logg som ger inblick i de åtgärder som utfördes på resurser i din prenumeration. Aktivitets loggen kallades tidigare för "gransknings loggar" eller "drift loggar", eftersom den rapporterar kontroll Plans händelser för dina prenumerationer.

![Azure-aktivitetslogg](./media/operational-security/azure-operational-security-fig7.png)

Med aktivitets loggen kan du bestämma vad, vem och när som ska användas för alla Skriv åtgärder (skicka, skicka och ta bort) på resurserna i din prenumeration. Du kan också förstå status för åtgärden och andra relevanta egenskaper. Aktivitets loggen innehåller inte Läs-/GET-åtgärder eller åtgärder för resurser som använder den klassiska modellen.

### <a name="azure-diagnostic-logs"></a>Azure Diagnostic-loggar

De här loggarna genereras av en resurs och ger omfattande och frekventa data om driften av resursen. Innehållet i dessa loggar varierar beroende på resurs typ.

Windows Event System-loggar är till exempel en kategori av diagnostikloggar för virtuella datorer och blob-, tabell-och Queue-loggar är kategorier av diagnostikloggar för lagrings konton.

Diagnostikloggar skiljer sig från [aktivitets loggen (tidigare känd som Gransknings logg eller drift logg)](../../azure-monitor/essentials/platform-logs-overview.md). Aktivitets loggen ger inblick i de åtgärder som utfördes på resurser i din prenumeration. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit på egen hand.

### <a name="metrics"></a>Mått

Med Azure Monitor kan du använda telemetri för att få insyn i dina arbets belastningar prestanda och hälsa på Azure. Den viktigaste typen av Azure-telemetridata är måtten (kallas även prestanda räknare) som har genererats av de flesta Azure-resurser. Azure Monitor tillhandahåller flera olika sätt att konfigurera och använda dessa [mått](../../azure-monitor/data-platform.md) för övervakning och fel sökning. Mått är en värdefull källa för telemetri och gör att du kan utföra följande uppgifter:

-   **Spåra** resursens prestanda (till exempel en virtuell dator, webbplats eller logisk app) genom att rita måtten på ett Portal diagram och fästa diagrammet på en instrument panel.

-   **Få ett meddelande om ett problem** som påverkar resursens prestanda när ett mått överkorsar ett visst tröskelvärde.

-   **Konfigurera automatiska åtgärder**, till exempel automatisk skalning av en resurs eller utlöser en runbook när ett mått korsar ett visst tröskelvärde.

-   **Utföra avancerad analys** eller rapportering om resursernas prestanda eller användnings trender.

-   **Arkivera** prestanda-eller hälso historiken för din resurs för efterlevnads-eller gransknings syfte.

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Det är funktionen i Azure som möjliggör insamling av diagnostikdata i ett distribuerat program. Du kan använda det diagnostiska tillägget från olika källor. För närvarande finns stöd för [webb-och arbets roller i Azure Cloud Service](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](../../virtual-machines/windows/overview.md) som kör Microsoft Windows och [Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md). Andra Azure-tjänster har sin egen separata diagnostik.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Det är viktigt att granska nätverks säkerheten för att identifiera nätverks sårbarheter och säkerställa efterlevnaden av din IT-säkerhet och reglerande styrnings modell. Med vyn säkerhets grupp kan du hämta den konfigurerade nätverks säkerhets gruppen och säkerhets reglerna och de effektiva säkerhets reglerna. Med listan över regler som tillämpas kan du fastställa vilka portar som är öppna och utvärdera nätverks sårbarheten.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks nivå i, till och från Azure. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. Den här tjänsten omfattar paket insamling, nästa hopp, kontrol lera IP-flöde, vyn säkerhets grupp, NSG Flow-loggar. Övervakning av scenario nivå ger en heltäckande vy över nätverks resurser i motsats till övervakning av enskilda nätverks resurser.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Network Watcher har för närvarande följande funktioner:

-   **<a href="/azure/network-watcher/network-watcher-monitoring-overview">Gransknings loggar</a>**– åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa loggar kan visas i Azure Portal eller hämtas med hjälp av Microsoft-verktyg som Power BI eller verktyg från tredje part. Gransknings loggar är tillgängliga via portalen, PowerShell, CLI och REST API. Mer information om gransknings loggar finns i gransknings åtgärder med Resource Manager. Gransknings loggar är tillgängliga för åtgärder som utförs på alla nätverks resurser.


-   **<a href="/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-flödet verifieras</a>** – kontrollerar om ett paket tillåts eller nekas baserat på flödes information 5-tuple paket parametrar (mål-IP, käll-IP, målport, källport och protokoll). Om paketet nekas av en nätverks säkerhets grupp returneras regeln och nätverks säkerhets gruppen som nekade paketet.

-   **<a href="/azure/network-watcher/network-watcher-next-hop-overview">Nästa hopp</a>** – bestämmer nästa hopp för paket som dirigeras i Azures nätverks infrastruktur resurs, så att du kan diagnostisera eventuella felkonfigurerade användardefinierade vägar.

-   **<a href="/azure/network-watcher/network-watcher-security-group-view-overview">Vyn säkerhets grupp</a>** – hämtar de effektiva och tillämpade säkerhets reglerna som tillämpas på en virtuell dator.

-   **<a href="/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG flödes loggning</a>** – flödes loggar för nätverks säkerhets grupper gör att du kan samla in loggar som är relaterade till trafik som tillåts eller nekas av säkerhets reglerna i gruppen. Flödet definieras av en 5-tupel-information – käll-IP, mål-IP, källport, mål Port och protokoll.

## <a name="azure-storage-analytics"></a>Azure-lagringsanalys

[Lagringsanalys](/rest/api/storageservices/fileservices/storage-analytics) kan lagra mått som innehåller aggregerad transaktions statistik och kapacitets data om begär anden till en lagrings tjänst. Transaktioner rapporteras både på API-åtgärds nivå och på lagrings service nivå, och kapaciteten rapporteras på lagrings tjänst nivå. Mät data kan användas för att analysera användningen av lagrings tjänster, diagnostisera problem med begär Anden som gjorts mot lagrings tjänsten och förbättra prestanda för program som använder en tjänst.

[Azure-lagringsanalys](/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller mått data för ett lagrings konto. Du kan använda dessa data för att spåra begär Anden, analysera användnings trender och diagnostisera problem med ditt lagrings konto. Lagringsanalys loggning är tillgängligt för [BLOB-, Queue-och table-tjänsterna](../../storage/common/storage-introduction.md). Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst.

Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begär Anden loggas med bästa möjliga ansträngning. Logg poster skapas endast om det finns begär Anden som görs mot tjänst slut punkten. Om ett lagrings konto till exempel har aktivitet i dess BLOB-slutpunkt men inte i dess tabell-eller Queue-slutpunkter, skapas endast loggar som rör Blob Service.

Om du vill använda Lagringsanalys måste du aktivera det separat för varje tjänst som du vill övervaka. Du kan aktivera det i [Azure Portal](https://portal.azure.com/); Mer information finns i [övervaka ett lagrings konto i Azure Portal](../../storage/common/manage-storage-analytics-logs.md). Du kan också aktivera Lagringsanalys program mässigt via REST API eller klient biblioteket. Använd åtgärden Ange tjänst egenskaper för att aktivera Lagringsanalys individuellt för varje tjänst.

De aggregerade data lagras i en välkänd BLOB (för loggning) och i välkända tabeller (för mått) som kan nås med hjälp av Blob Service-och Table service-API: er.

Lagringsanalys har en gräns på 20 TB för mängden lagrade data som är oberoende av den totala gränsen för ditt lagrings konto. Alla loggar lagras i [block-blobar](../../storage/common/storage-analytics.md) i en behållare med namnet $logs, som skapas automatiskt när Lagringsanalys har Aktiver ATS för ett lagrings konto.

Följande åtgärder som utförs av Lagringsanalys är fakturerbara:

-   Begär Anden om att skapa blobbar för loggning
-   Begär Anden om att skapa tabell enheter för mått.

> [!Note]
> Mer information om fakturerings-och data lagrings principer finns [Lagringsanalys och fakturering](/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> För optimala prestanda vill du begränsa antalet mycket använda diskar som är anslutna till den virtuella datorn för att undvika möjlig begränsning. Om alla diskar inte används med hög tillgänglighet samtidigt kan lagrings kontot ha stöd för en större nummer disk.

> [!Note]
> Mer information om lagrings konto gränser finns i [skalbarhets mål för standard lagrings konton](../../storage/common/scalability-targets-standard-account.md).


Följande typer av autentiserade och anonyma begär Anden loggas.

| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade begäranden | Lyckade begäranden |
|Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begär Anden som använder en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade förfrågningar |
| Begär Anden som använder en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade förfrågningar |Timeoutfel för både klient och server |
|   Begäranden om analysdata |    Misslyckade GET-begäranden med felkoden 304 (har inte ändrats) |
| Begär Anden som görs av Lagringsanalys, till exempel när loggen skapas eller tas bort loggas inte. En fullständig lista över de loggade data som dokumenteras finns i avsnitten [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](/rest/api/storageservices/fileservices/storage-analytics-log-format) . | Alla andra misslyckade anonyma begär Anden loggas inte. En fullständig lista över loggade data dokumenteras i [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD innehåller också en fullständig uppsättning funktioner för identitets hantering, inklusive Multi-Factor Authentication, enhets registrering, lösen ords hantering via självbetjäning, grupp hantering via självbetjäning, hantering av privilegierade konton, rollbaserad åtkomst kontroll, övervakning av program användning, omfattande granskning och säkerhetsövervakning och aviseringar.

-   Förbättra program säkerheten med Azure AD multifaktas-autentisering och villkorlig åtkomst.

-   Övervaka program användning och skydda ditt företag mot avancerade hot med säkerhets rapportering och övervakning.

Azure AD (Active Directory Azure) tillhandahåller säkerhets-, aktivitets- och granskningsrapporter för din katalog. [Rapporten Azure Active Directory granskning](../../active-directory/reports-monitoring/overview-reports.md) hjälper kunder att identifiera de privilegierade åtgärder som inträffat i deras Azure Active Directory. Privilegierade åtgärder är ändringar i förhöjd behörighet (till exempel skapande av roller eller återställning av lösen ord), ändring av princip konfiguration (till exempel lösen ords principer) eller ändringar i katalog konfigurationen (till exempel ändringar i domän Federations inställningar).

Rapporterna innehåller gransknings posten för händelse namnet, aktören som utförde åtgärden, mål resursen som påverkas av ändringen och datum och tid (i UTC). Kunderna kan hämta listan över gransknings händelser för sina Azure Active Directory via [Azure Portal](https://portal.azure.com/), enligt beskrivningen i [Se gransknings loggarna](../../active-directory/reports-monitoring/overview-reports.md). Här är en lista över de rapporter som ingår:

| Säkerhetsrapporter  | Aktivitetsrapporter| Granskningsrapporter |
| :------------- | :-------------| :-------------|
|Inloggningar från okända källor | Programanvändning: sammanfattning | Kataloggranskningsrapport |
|Inloggningar efter flera fel | Programanvändning: detaljerad |   |
|Inloggningar från flera geografiska områden | Instrumentpanel för program |  |
|Inloggningar från IP-adresser med misstänkt aktivitet |Kontoetableringsfel |  |
|Oregelbunden inloggningsaktivitet |Enskilda användarenheter |  |
|Inloggningar från potentiellt infekterade enheter |Aktivitet för enskilda användare |   |
|Användare med avvikande inloggningsaktivitet |Aktivitetsrapport för grupper |   |
| |Aktivitetsrapport över registrering av lösenordsåterställning |   |
| |Lösenordsåterställningsaktivitet |   |



Data för de här rapporterna kan vara användbara för dina program, till exempel SIEM system, granskning och Business Intelligence verktyg. Azure AD repor ting- [API: erna](../../active-directory/reports-monitoring/concept-reporting-api.md) ger programmatisk åtkomst till data via en uppsättning rest-baserade API: er. Du kan anropa dessa API: er från olika programmeringsspråk och verktyg.

Händelser i gransknings rapporten för Azure AD sparas i 180 dagar.

> [!Note]
> Mer information om kvarhållning av rapporter finns i [Azure Active Directory bevarande principer](../../active-directory/reports-monitoring/reference-reports-data-retention.md)för rapporter.

För kunder som är intresserade av att lagra sina [gransknings händelser](../../active-directory/reports-monitoring/concept-audit-logs.md) för längre kvarhållningsperiod kan rapporterings-API: t användas för att regelbundet hämta gransknings händelser till ett separat data lager.

## <a name="summary"></a>Sammanfattning

Den här artikeln sammanfattar hur du skyddar din integritet och skyddar dina data, samtidigt som du levererar program vara och tjänster som hjälper dig att hantera IT-infrastrukturen i din organisation. Microsoft känner av att när de förlitar sina data till andra, kräver detta förtroende rigorös säkerhet. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst. Att skydda och skydda data är en högsta prioritet hos Microsoft.

Den här artikeln beskriver

-   Hur data samlas in, bearbetas och skyddas i Azure Monitors serien.

-   Analysera händelser snabbt i flera data källor. Identifiera säkerhets risker och förstå omfattningen och effekten av hot och attacker för att minimera skadan vid en säkerhets överträdelse.

-   Identifiera angrepps mönster genom att visualisera utgående skadlig IP-trafik och typer av skadliga hot. Förstå säkerhets position i hela miljön oavsett plattform.

-   Avbilda alla logg-och händelse data som krävs för en granskning av säkerhet eller efterlevnad. Bryt den tid och de resurser som krävs för att tillhandahålla en säkerhets granskning med en fullständig, sökbar och exporter bar logg-och händelse data uppsättning.

<ul>
<li>Samla in säkerhetsrelaterade händelser, gransknings-och överträdelse analyser för att hålla ett nära öga på dina till gångar:</li>
<ul>
<li>Säkerhetsstatus</li>
<li>Viktiga problem</li>
<li>Sammanfattar hot</li>
</ul>
</ul>

## <a name="next-steps"></a>Nästa steg

- [Design-och drift säkerhet](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft utformar sina tjänster och program vara med säkerhet i åtanke för att säkerställa att dess moln infrastruktur är elastisk och försvaras mot angrepp.

- [Azure Monitor loggar | Efterlevnad av säkerhets &](https://www.microsoft.com/cloud-platform/security-and-compliance)

Använd Microsofts säkerhets data och analys för att utföra mer intelligent och effektiv hot identifiering.

- [Azure Security Center planering och åtgärder](../../security-center/security-center-planning-and-operations-guide.md) En uppsättning steg och uppgifter som du kan följa för att optimera din användning av Security Center baserat på din organisations säkerhets krav och moln hanterings modell.