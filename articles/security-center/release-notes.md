---
title: Information om Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c47543fa1401a8da533a9af3ebe4554e405622d3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835549"
---
# <a name="whats-new-in-azure-security-center"></a>Vad är nytt i Azure Security Center?

Security Center är i aktiv utveckling och får kontinuerliga förbättringar. Den här sidan innehåller information om nya funktioner, felkorrigeringar och föråldrade funktioner för att hålla dig uppdaterad med den senaste utvecklingen.

Den här sidan uppdateras ofta, så gå ofta tillbaka till den. 

Information om *planerade ändringar* som snart kommer att Security Center finns i Viktiga kommande ändringar i [Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Om du letar efter objekt som är äldre än sex månader hittar du dem i Arkiv för Nyheter [i Azure Security Center](release-notes-archive.md).

## <a name="april-2021"></a>April 2021

Uppdateringar i april är:
- [Nyligen nedlästa containerregisteravbildningar genomsöks nu varje vecka (allmän tillgänglighet)](#recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability)
- [Använd Azure Defender för Kubernetes för att skydda Kubernetes-hybriddistributioner och Kubernetes-distributioner i flera moln (förhandsversion)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [Rekommendationer för att Azure Defender för DNS och Resource Manager (förhandsversion)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview)
- [Tre standarder för regelefterlevnad har lagts till: Azure CIS 1.3.0, CMMC Level 3 och Nya Zeeland ISM Restricted](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Fyra nya rekommendationer som rör gästkonfiguration (förhandsversion)](#four-new-recommendations-related-to-guest-configuration-preview)
- [CMK-rekommendationer har flyttats till metodtips för säkerhetskontroll](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender-aviseringar är inaktuella](#11-azure-defender-alerts-deprecated)
- [Två rekommendationer från säkerhetskontrollen "Tillämpa systemuppdateringar" är inaktuella](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Azure Defender för SQL på datorpanelen borttagen från Azure Defender instrumentpanel](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)

### <a name="recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability"></a>Nyligen nedlästa containerregisteravbildningar genomsöks nu varje vecka (allmän tillgänglighet)

Azure Defender för containerregister innehåller en inbyggd sårbarhetsskanner. Den här skannern söker omedelbart igenom alla avbildningar som du push-meddelandena till registret och alla avbildningar som har dragits under de senaste 30 dagarna.

Nya säkerhetsrisker upptäcks varje dag. Med den här uppdateringen **genomsöks** containeravbildningar som har hämtas från dina register under de senaste 30 dagarna varje vecka. Detta säkerställer att nyligen identifierade säkerhetsrisker identifieras i dina bilder.

Genomsökningen debiteras per bild, så det tillkommer ingen extra kostnad för dessa nya genomsökningar.

Läs mer om den här [skannern i Använda Azure Defender för containerregister för att söka igenom dina avbildningar efter säkerhetsrisker.](defender-for-container-registries-usage.md)


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>Använd Azure Defender för Kubernetes för att skydda Kubernetes-hybriddistributioner och Kubernetes-distributioner i flera moln (förhandsversion)

Azure Defender för Kubernetes utökar sina hotskyddsfunktioner för att skydda dina kluster oavsett var de distribueras. Detta har aktiverats genom integrering med [Azure Arc aktiverat Kubernetes](../azure-arc/kubernetes/overview.md) och dess [nya tilläggsfunktioner](../azure-arc/kubernetes/extensions.md). 

När du har aktiverat Azure Arc på dina Kubernetes-kluster som inte är Azure erbjuder en ny rekommendation från Azure Security Center att distribuera Azure Defender-tillägget till dem med bara några få klickningar.

Använd rekommendationen (**Azure Arc-aktiverade Kubernetes-kluster** bör ha Azure Defender-tillägget installerat ) och tillägget för att skydda Kubernetes-kluster som distribuerats i andra molnleverantörer, men inte på deras hanterade Kubernetes-tjänster.

Den här integreringen mellan Azure Security Center, Azure Defender och Azure Arc kubernetes ger:

- Enkel etablering av Azure Defender till oskyddade och Azure Arc Kubernetes-kluster (manuellt och i stor skala)
- Övervakning av Azure Defender tillägget och dess etableringstillstånd från Azure Arc portalen
- Säkerhetsrekommendationer från Security Center rapporteras på den nya sidan Säkerhet i Azure Arc portalen
- Identifierade säkerhetshot från Azure Defender rapporteras på den nya sidan Säkerhet i Azure Arc portalen
- Azure Arc Kubernetes-kluster är integrerade i Azure Security Center plattform och upplevelse

Läs mer i [Använda Azure Defender för Kubernetes med dina lokala Kubernetes-kluster och Kubernetes-kluster för flera moln.](defender-for-kubernetes-azure-arc.md)

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center rekommendation för att distribuera Azure Defender för Azure Arc Kubernetes-kluster." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview"></a>Rekommendationer för att Azure Defender för DNS och Resource Manager (förhandsversion)

Två nya rekommendationer har lagts till för att förenkla processen att [aktivera Azure Defender för Resource Manager](defender-for-resource-manager-introduction.md) och Azure Defender för [DNS:](defender-for-dns-introduction.md)

- **Azure Defender för Resource Manager** ska vara aktiverat – Defender for Resource Manager övervakar automatiskt resurshanteringsåtgärder i din organisation. Azure Defender identifierar hot och varnar dig om misstänkt aktivitet.
- **Azure Defender för DNS ska** vara aktiverat – Defender för DNS ger ett extra skyddslager för dina molnresurser genom att kontinuerligt övervaka alla DNS-frågor från dina Azure-resurser. Azure Defender dig om misstänkt aktivitet i DNS-lagret.

Om du aktiverar Azure Defender-planer resulterar det i avgifter. Läs mer om prisinformationen per region Security Center sidan med priser: https://aka.ms/pricing-security-center .

> [!TIP]
> Förhandsversionsrekommendationer återger inte en resurs som inte är skadad och de ingår inte i beräkningarna av dina säkerhetspoäng. Åtgärda dem när det är möjligt, så att de bidrar till dina poäng när förhandsgranskningsperioden är slut. Läs mer om hur du svarar på dessa rekommendationer [i Åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Tre standarder för regelefterlevnad har lagts till: Azure CIS 1.3.0, CMMC Level 3 och Nya Zeeland ISM Restricted

Vi har lagt till tre standarder för användning med Azure Security Center. Med hjälp av instrumentpanelen för regelefterlevnad kan du nu spåra din efterlevnad med:

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC Nivå 3](../governance/policy/samples/cmmc-l3.md)
- [Nya Zeeland ISM Restricted](../governance/policy/samples/new-zealand-ism.md)

Du kan tilldela dessa till dina prenumerationer enligt beskrivningen i Anpassa uppsättningen standarder i [instrumentpanelen för regelefterlevnad.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Tre standarder har lagts till för användning Azure Security Center på instrumentpanelen för regelefterlevnad." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

Läs mer i:
- [Anpassa uppsättningen standarder i instrumentpanelen för regelefterlevnad](update-regulatory-compliance-packages.md)
- [Självstudie: Förbättra regelefterlevnaden](security-center-compliance-dashboard.md)
- [Vanliga frågor och svar om instrumentpanelen för regelefterlevnad](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Fyra nya rekommendationer som rör gästkonfiguration (förhandsversion)

Azures [gästkonfigurationstillägg rapporterar](../governance/policy/concepts/guest-configuration.md) till Security Center för att säkerställa att dina virtuella datorers gästinställningar är härdade. Tillägget krävs inte för Arc-aktiverade servrar eftersom det ingår i Arc Connected Machine-agenten. Tillägget kräver en system hanterad identitet på datorn.

Vi har lagt till fyra nya rekommendationer för att Security Center för att få ut det här tillägget på bästa sätt.

- Två rekommendationer uppmanar dig att installera tillägget och dess nödvändiga system hanterade identitet:
    - **Gästkonfigurationstillägget ska installeras på dina datorer**
    - **Gästkonfigurationstillägget för virtuella datorer ska distribueras med system tilldelad hanterad identitet**

- När tillägget installeras och körs börjar granskning av dina datorer och du uppmanas att härda inställningar som konfiguration av operativsystem- och miljöinställningar. Dessa två rekommendationer uppmanar dig att härda dina Windows- och Linux-datorer enligt beskrivningen:
    - **Windows Defender Exploit Guard ska vara aktiverat på dina datorer**
    - **Autentisering till Linux-datorer bör kräva SSH-nycklar**

Läs mer i [Förstå Azure Policy gästkonfiguration.](../governance/policy/concepts/guest-configuration.md)

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK-rekommendationer har flyttats till metodtips för säkerhetskontroll

Alla organisationers säkerhetsprogram innehåller krav på datakryptering. Som standard krypteras Azure-kundernas data i vila med tjänst hanterade nycklar. Kund hanterade nycklar (CMK) krävs dock ofta för att uppfylla regelefterlevnadsstandarder. Med CMK:er kan du kryptera dina data [Azure Key Vault](../key-vault/general/overview.md) en nyckel som skapats och ägs av dig. Detta ger dig fullständig kontroll och ansvar för nyckellivscykeln, inklusive rotation och hantering.

Azure Security Center säkerhetskontroller är logiska grupper av relaterade säkerhetsrekommendationer och återspeglar dina sårbara angreppsytor. Varje kontroll har ett maximalt antal punkter som du kan lägga till i din säkerhetspoäng om du åtgärdar alla rekommendationer som anges i kontrollen för alla dina resurser. Säkerhetskontrollen **Implementera säkerhetsmetoder** är värd noll punkter. Därför påverkar inte rekommendationerna i den här kontrollen dina säkerhetspoäng.

Rekommendationerna nedan flyttas till säkerhetskontrollen Implementera metodtips **för att** bättre återspegla deras valfria natur. Den här flytten säkerställer att dessa rekommendationer har den lämpligaste kontrollen för att uppfylla deras mål.

- Azure Cosmos DB bör använda kund hanterade nycklar för att kryptera vilodata
- Azure Machine Learning bör krypteras med en kund hanterad nyckel (CMK)
- Cognitive Services bör aktivera datakryptering med en kund hanterad nyckel (CMK)
- Containerregister ska krypteras med en kund hanterad nyckel (CMK)
- SQL-hanterade instanser bör använda kund hanterade nycklar för att kryptera vilodata
- SQL-servrar bör använda kund hanterade nycklar för att kryptera vilodata
- Lagringskonton bör använda kund hanterad nyckel (CMK) för kryptering

Lär dig vilka rekommendationer som finns i varje säkerhetskontroll [i Säkerhetskontroller och deras rekommendationer.](secure-score-security-controls.md#security-controls-and-their-recommendations)


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender inaktuella aviseringar

De Azure Defender aviseringarna nedan är inaktuella.

- Nya aviseringar ersätter dessa två aviseringar och ger bättre täckning:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | FÖRHANDSVERSION – Funktionskörningen "Get-AzureDomainInfo" för MicroBurst-verktygslådan har identifierats |
    | ARM_MicroBurstRunbook    | FÖRHANDSVERSION – Funktionskörningen "Get-AzurePasswords" för MicroBurst-verktygslådan har identifierats  |
    |                          |                                                                          |

- De här nio aviseringarna är relaterade Azure Active Directory Identity Protection en anslutningsapp (IPC) som redan är inaktuell:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Obekanta inloggningsegenskaper |
    | AnonymousLogin      | Anonym IP-adress          |
    | InfectedDeviceLogin | IP-adress länkad till skadlig kod     |
    | ImpossibleTravel    | Ovanlig resa               |
    | MaliciousIP         | Skadlig IP-adress          |
    | LeakedCredentials   | Läckta autentiseringsuppgifter            |
    | Password Entim       | Lösenordsutlösenord                |
    | LeakedCredentials   | Azure AD-hotinformation  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > Dessa nio IPC-aviseringar har aldrig Security Center aviseringar. De är en del av Azure Active Directory (AAD) Identity Protection Connector (IPC) som skickade dem till Security Center. Under de senaste två åren är de enda kunder som har sett aviseringarna organisationer som konfigurerade exporten (från anslutningsappen till ASC) 2019 eller tidigare. AAD IPC har fortsatt att visa dem i sina egna aviseringssystem och de har fortsatt att vara tillgängliga i Azure Sentinel. Den enda ändringen är att de inte längre visas i Security Center.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Två rekommendationer från säkerhetskontrollen "Tillämpa systemuppdateringar" är inaktuella 

Följande två rekommendationer är inaktuella och ändringarna kan leda till en liten inverkan på säkerhetspoängen:

- **Datorerna bör startas om för att tillämpa systemuppdateringar**
- **Övervakningsagenten ska installeras på datorerna**. Den här rekommendationen gäller endast lokala datorer och en del av dess logik överförs till en annan rekommendation. Problem med **Log Analytics-agentens** hälsotillstånd bör lösas på dina datorer

Vi rekommenderar att du kontrollerar dina konfigurationer för kontinuerlig export och arbetsflödesautomatisering för att se om dessa rekommendationer ingår i dem. Dessutom bör alla instrumentpaneler eller andra övervakningsverktyg som använder dem uppdateras i enlighet med detta.

Läs mer om dessa rekommendationer på [referenssidan för säkerhetsrekommendationer.](recommendations-reference.md)

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Azure Defender för SQL på datorpanelen borttagen från Azure Defender instrumentpanel

I Azure Defender instrumentpanelens täckningsområde finns paneler för relevanta Azure Defender för din miljö. På grund av ett problem med rapportering av antalet skyddade och oskyddade resurser har vi beslutat att tillfälligt ta bort resurstäckningsstatusen för Azure Defender för **SQL** på datorer tills problemet har lösts.


## <a name="march-2021"></a>Mars 2021

Uppdateringar i mars omfattar:

- [Azure Firewall integrerad hantering i Security Center](#azure-firewall-management-integrated-into-security-center)
- [Sårbarhetsbedömning i SQL innehåller nu funktionen "Inaktivera regel" (förhandsversion)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor arbetsböcker som är integrerade i Security Center och tre mallar tillhandahålls](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Instrumentpanelen för regelefterlevnad innehåller nu Azure-granskningsrapporter (förhandsversion)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Rekommendationsdata kan visas i Azure Resource Graph "Utforska i ARG"](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Uppdateringar av principerna för att distribuera arbetsflödesautomation](#updates-to-the-policies-for-deploying-workflow-automation)
- [Två äldre rekommendationer skriver inte längre data direkt till Azure-aktivitetsloggen](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Förbättringar på sidan Rekommendationer](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall integrerad hantering i Security Center

När du öppnar Azure Security Center visas översiktssidan på den första sidan. 

Den här interaktiva instrumentpanelen ger en enhetlig vy över säkerhetsstatusen för dina hybridmolnarbetsbelastningar. Dessutom visas säkerhetsaviseringar, täckningsinformation med mera.

Som en del av att hjälpa dig att visa din säkerhetsstatus från en central upplevelse har vi integrerat Azure Firewall Manager i den här instrumentpanelen. Du kan nu kontrollera statusen för brandväggens täckning i alla nätverk och centralt hantera Azure Firewall principer från och med Security Center.

Läs mer om den här [Azure Security Center på översiktssidan](overview-page.md)för .

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center översiktsinstrumentpanel med en panel för Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Sårbarhetsbedömning i SQL innehåller nu funktionen "Inaktivera regel" (förhandsversion)

Security Center innehåller en inbyggd sårbarhetsskanner som hjälper dig att identifiera, spåra och åtgärda potentiella säkerhetsrisker i databasen. Resultaten från dina utvärderingsgenomsökningar ger en översikt över sql-datorernas säkerhetstillstånd och information om eventuella säkerhetsresultat.

Om du har en organisation som behöver ignorera en upptäckt i stället för att åtgärda den kan du inaktivera den om du vill. Inaktiverade resultat påverkar inte din säkerhetspoäng eller genererar oönskade brus.

Läs mer i [Inaktivera specifika resultat.](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor-arbetsböcker som är integrerade Security Center och tre mallar tillhandahålls

Som en del av Ignite spring 2021 presenterade vi en integrerad Azure Monitor-arbetsböcker i Security Center.

Du kan använda den nya integreringen för att börja använda färdiga mallar från Security Center galleriet. Med hjälp av arbetsboksmallar kan du komma åt och skapa dynamiska och visuella rapporter för att spåra organisationens säkerhetsstatus. Dessutom kan du skapa nya arbetsböcker baserat på Security Center data eller andra datatyper som stöds och snabbt distribuera arbetsböcker från Security Center GitHub-communityn.

Tre mallrapporter tillhandahålls:

- **Säkerhetspoäng över tid** – Spåra dina prenumerationers poäng och ändringar av rekommendationer för dina resurser
- **Systemuppdateringar** – Visa saknade systemuppdateringar efter resurser, operativsystem, allvarlighetsgrad med mera
- **Resultat av sårbarhetsbedömning** – Visa resultatet av sårbarhetsgenomsökningar för dina Azure-resurser

Lär dig mer om att använda dessa rapporter eller skapa [egna, interaktiva rapporter för att skapa Security Center data](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapport om säkerhetspoäng över tid":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Instrumentpanelen för regelefterlevnad innehåller nu Azure-granskningsrapporter (förhandsversion)

Från instrumentpanelen för regelefterlevnad kan du nu ladda ned Azure- och Dynamics-certifieringsrapporter. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Verktygsfältet för instrumentpanelen för regelefterlevnad":::

Du kan välja fliken för relevanta rapporttyper (PCI, SOC, ISO med flera) och använda filter för att hitta de specifika rapporter som du behöver.

Läs mer om att [hantera standarderna på instrumentpanelen för regelefterlevnad.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrera listan över tillgängliga Azure-granskningsrapporter":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Rekommendationsdata kan visas i Azure Resource Graph "Utforska i ARG"

Rekommendationsinformationssidorna innehåller nu verktygsfältsknappen Utforska i ARG. Använd den här knappen för att Azure Resource Graph en fråga och utforska, exportera och dela rekommendationens data.

Azure Resource Graph (ARG) ger omedelbar åtkomst till resursinformation i dina molnmiljöer med robusta filtrerings-, grupperings- och sorteringsfunktioner. Det är ett snabbt och effektivt sätt att fråga efter information i Azure-prenumerationer programmatiskt eller Azure Portal.

Läs mer [om Azure Resource Graph (ARG)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Utforska rekommendationsdata i Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Uppdateringar av principerna för att distribuera arbetsflödesautomation

Att automatisera organisationens processer för övervakning och incidenter kan avsevärt förbättra den tid det tar att undersöka och minimera säkerhetsincidenter.

Vi tillhandahåller tre Azure Policy "DeployIfNotExist"-principer som skapar och konfigurerar arbetsflödesautomatiseringsprocedurer så att du kan distribuera dina automatiseringar i organisationen:

|Mål  |Policy  |Princip-ID  |
|---------|---------|---------|
|Arbetsflödesautomation för säkerhetsaviseringar|[Distribuera arbetsflödesautomation för Azure Security Center-aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Arbetsflödesautomation för säkerhetsrekommendationer|[Distribuera arbetsflödesautomation för Azure Security Center-rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Arbetsflödesautomation för ändringar av regelefterlevnad|[Distribuera Arbetsflödesautomation för Azure Security Center regelefterlevnad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Det finns två uppdateringar av funktionerna i dessa principer:

- När de tilldelas förblir de aktiverade av tvingande.
- Du kan nu anpassa dessa principer och uppdatera någon av parametrarna även efter att de redan har distribuerats. Om en användare till exempel vill lägga till en annan utvärderingsnyckel eller redigera en befintlig utvärderingsnyckel kan de göra det.

Kom igång med mallar [för arbetsflödesautomation.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

Läs mer om hur du [automatiserar svar på Security Center utlösare.](workflow-automation.md)


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Två äldre rekommendationer skriver inte längre data direkt till Azure-aktivitetsloggen 

Security Center skickar data för nästan alla säkerhetsrekommendationer till Azure Advisor, som i sin tur skriver dem till [Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md).

För två rekommendationer skrivs datan samtidigt direkt till Azure-aktivitetsloggen. Med den här ändringen Security Center data för dessa äldre säkerhetsrekommendationer direkt till aktivitetsloggen. I stället exporterar vi data till Azure Advisor som vi gör för alla andra rekommendationer.

De två äldre rekommendationerna är:
- Problem med slutpunktsskyddshälsa bör lösas på dina datorer
- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas

Om du har använt informationen för dessa två rekommendationer i aktivitetsloggens kategori "Rekommendation av typen TaskDiscovery" är detta inte längre tillgängligt.


### <a name="recommendations-page-enhancements"></a>Förbättringar på sidan Rekommendationer 

Vi har släppt en förbättrad version av rekommendationslistan för att visa mer information på ett ögonblick.

Nu visas följande på sidan:

1. Högsta poäng och aktuell poäng för varje säkerhetskontroll.
1. Ikoner som ersätter taggar som **Snabbkorrigering och** **Förhandsversion.**
1. En ny kolumn som visar [principinitiativ som](security-policy-concept.md) är relaterat till varje rekommendation – synlig när "Gruppera efter kontroller" är inaktiverat.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Förbättringar av Azure Security Center rekommendationer – mars 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Förbättringar av Azure Security Center rekommendationerna &quot;platt&quot; lista – mars 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Läs mer i [Säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).


## <a name="february-2021"></a>Februari 2021

Uppdateringar i februari omfattar:

- [Ny sida för säkerhetsaviseringar i Azure Portal släpps för allmän tillgänglighet (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Rekommendationer för Kubernetes-arbetsbelastningsskydd har släppts för allmän tillgänglighet (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender för slutpunktsintegrering med Azure Defender stöder nu Windows Server 2019 och Windows 10 Virtual Desktop (WVD) (i förhandsversion)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Direktlänk till princip från sidan med rekommendationsinformation](#direct-link-to-policy-from-recommendation-details-page)
- [Rekommendationen för SQL-dataklassificering påverkar inte längre dina säkerhetspoäng](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Arbetsflödesautomationerna kan utlösas av ändringar av regelefterlevnadsutvärderingar (i förhandsversion)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Förbättringar på sidan Tillgångsinventering](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Ny sida för säkerhetsaviseringar i Azure Portal släpps för allmän tillgänglighet (GA)

Azure Security Center säkerhetsaviseringar har gjorts om för att ge:

- **Förbättrad grupperingsupplevelse för aviseringar** – hjälper till att minska aviseringarna och fokusera på de mest relevanta hoten enklare. Listan innehåller anpassningsbara filter och grupperingsalternativ.
- **Mer information i aviseringslistan** – till exempel MITRE ATT&ACK-taktiker.
- **Knapp för att skapa exempelaviseringar** – för att Azure Defender funktioner och testa dina aviseringar. (för SIEM-integrering, e-postaviseringar och arbetsflödesautomatiseringar) kan du skapa exempelaviseringar från alla Azure Defender planer.
- **Anpassning med Azure Sentinel** incidentupplevelse – för kunder som använder båda produkterna är det nu enklare att växla mellan dem och det är enkelt att lära sig en av dem.
- **Bättre prestanda** för stora aviseringslistor.
- **Tangentbordsnavigering** via aviseringslistan.
- **Aviseringar från Azure Resource Graph** – du kan köra frågor mot aviseringar i Azure Resource Graph, kusto-liknande API för alla dina resurser. Detta är också användbart om du skapar dina egna instrumentpaneler för aviseringar. [Läs mer om att Azure Resource Graph](../governance/resource-graph/index.yml).
- **Skapa funktionen för exempelaviseringar** – Om du vill skapa exempelaviseringar från den nya aviseringsupplevelsen kan du se [Generera exempelaviseringar Azure Defender exempelaviseringar.](security-center-alert-validation.md#generate-sample-azure-defender-alerts)

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center säkerhetsaviseringar":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Rekommendationer för Kubernetes-arbetsbelastningsskydd har släppts för allmän tillgänglighet (GA)

Vi är glada över att kunna meddela allmän tillgänglighet (GA) för uppsättningen rekommendationer för Kubernetes-arbetsbelastningsskydd.

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard har Security Center lagt till rekommendationer för härdning på Kubernetes-nivå, inklusive framtvingandealternativ med Kubernetes-antagningskontroll.

När Azure Policy-tillägget för Kubernetes är installerat på ditt Azure Kubernetes Service-kluster (AKS) övervakas varje begäran till Kubernetes API-servern mot den fördefinierade uppsättningen bästa praxis – visas som 13 säkerhetsrekommendationer – innan den bevaras i klustret. Du kan sedan konfigurera för att framtvinga bästa praxis och be om dem för framtida arbetsbelastningar.

Du kan till exempel uppmana privilegierade containrar att inte skapas, och eventuella framtida begäranden om att göra det blockeras.

Läs mer i [Metodtips för arbetsbelastningsskydd med Kubernetes-antagningskontroll.](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

> [!NOTE]
> Rekommendationerna var i förhandsversion, men de renderade inte en AKS-klusterresurs som inte var felhälsosam, och de inkluderades inte i beräkningarna av dina säkerhetspoäng. med det här GA-meddelandet kommer dessa att ingå i poängberäkningen. Om du inte redan har åtgärdat dem kan detta leda till en liten inverkan på säkerhetspoängen. Åtgärda dem när det är möjligt enligt beskrivningen i [Åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Microsoft Defender för slutpunktsintegrering med Azure Defender stöder nu Windows Server 2019 och Windows 10 Virtual Desktop (WVD) (i förhandsversion)

Microsoft Defender för slutpunkt är en holistisk, moln levererad slutpunktssäkerhetslösning. Den tillhandahåller riskbaserad sårbarhetshantering och utvärdering samt slutpunktsidentifiering och svar (EDR). En fullständig lista över fördelarna med att använda Defender för slutpunkt tillsammans med Azure Security Center finns i Skydda dina [slutpunkter med Security Center:s integrerade EDR-lösning: Microsoft Defender för slutpunkt.](security-center-wdatp.md)

När du Azure Defender för servrar på en Windows-server ingår en licens för Defender för slutpunkt i planen. Om du redan har aktiverat Azure Defender för servrar och du har Windows 2019-servrar i din prenumeration får de automatiskt Defender for Endpoint med den här uppdateringen. Ingen manuell åtgärd krävs. 

Stödet har nu utökats till att omfatta Windows Server 2019 [och Windows Virtual Desktop (WVD).](../virtual-desktop/overview.md)

> [!NOTE]
> Om du aktiverar Defender för slutpunkt på en Windows Server 2019-dator måste du kontrollera att den uppfyller kraven som beskrivs i Aktivera Integrering av [Microsoft Defender för slutpunkt.](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Direktlänk till princip från sidan med rekommendationsinformation

När du granskar informationen om en rekommendation är det ofta bra att kunna se den underliggande principen. För varje rekommendation som stöds av en princip finns det en ny länk från sidan med rekommendationsinformation:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Länk till Azure Policy för den specifika princip som stöder en rekommendation":::

Använd den här länken för att visa principdefinitionen och granska utvärderingslogiken. 

Om du granskar listan över rekommendationer i referensguiden för säkerhetsrekommendationer visas även länkar till principdefinitionssidorna: [](recommendations-reference.md)

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Åtkomst till Azure Policy för en specifik princip direkt från referenssidan Azure Security Center rekommendationer" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Rekommendationen för SQL-dataklassificering påverkar inte längre dina säkerhetspoäng
Rekommendationen **Känsliga data i DINA SQL-databaser bör inte längre** klassificeras påverkar din säkerhetspoäng. Det här är den enda rekommendationen i **säkerhetskontrollen** Tillämpa dataklassificering, så att kontrollen nu har värdet 0 för säkerhetspoäng.

En fullständig lista över alla säkerhetskontroller i Security Center, tillsammans med deras poäng och en lista över rekommendationerna i var och en, finns i [Säkerhetskontroller och deras rekommendationer.](secure-score-security-controls.md#security-controls-and-their-recommendations)

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Arbetsflödesautomation kan utlösas av ändringar i utvärderingar av regelefterlevnad (i förhandsversion)
Vi har lagt till en tredje datatyp till utlösaralternativen för dina arbetsflödesautomatiseringar: ändringar av regelefterlevnadsbedömningar.

Lär dig hur du använder verktygen för arbetsflödesautomatisering [i Automatisera svar Security Center utlösare](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Använda ändringar av regelefterlevnadsutvärderingar för att utlösa en arbetsflödesautomation" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Sidförbättringar för tillgångsinventering
Security Center tillgångslagersidan har förbättrats på följande sätt:

- Sammanfattningar överst på sidan innehåller nu **Oregistrerade prenumerationer,** som visar antalet prenumerationer utan att Security Center aktiverat.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Antal oregistrerade prenumerationer i sammanfattningarna överst på sidan för tillgångsinventering":::

- Filter har utökats och förbättrats så att de omfattar:
    - **Antal** – Varje filter visar antalet resurser som uppfyller kriterierna för varje kategori

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Antal i filtren på sidan för tillgångsinventering i Azure Security Center":::

    - **Innehåller undantagsfilter** (valfritt) – begränsa resultatet till resurser som har/inte har undantag. Det här filtret visas inte som standard, men kan nås från knappen **Lägg till** filter.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Lägga till filtret &quot;contains exemption&quot; Azure Security Center på sidan för tillgångsinventering":::

Läs mer om hur du [utforskar och hanterar dina resurser med tillgångsinventering.](asset-inventory.md)

## <a name="january-2021"></a>Januari 2021

Uppdateringar i januari är:

- [Azure Security Benchmark är nu standardprincipinitiativ för Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Sårbarhetsbedömning för lokala datorer och datorer med flera moln släpps för allmän tillgänglighet (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Säkerhetspoäng för hanteringsgrupper är nu tillgängligt i förhandsversion](#secure-score-for-management-groups-is-now-available-in-preview)
- [Säkerhetspoäng-API släpps för allmän tillgänglighet (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Dns-skydd har lagts till i Azure Defender för App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Anslutningsappar för flera moln släpps för allmän tillgänglighet (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Undanta hela rekommendationer från dina säkerhetspoäng för prenumerationer och hanteringsgrupper](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Användare kan nu begära synlighet för hela klientorganisationen från den globala administratören](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 rekommendationer för förhandsversion har lagts till för att öka täckningen av Azure Security Benchmark](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [CSV-export av filtrerad lista med rekommendationer](#csv-export-of-filtered-list-of-recommendations)
- ["Ej tillämpligt" resurser rapporteras nu som "kompatibla" i Azure Policy utvärderingar](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportera veckovisa ögonblicksbilder av data om säkerhetspoäng och regelefterlevnad med löpande export (förhandsversion)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Benchmark är nu standardprincipinitiativ för Azure Security Center

Azure Security Benchmark är Microsofts microsoft-specifika uppsättning riktlinjer för bästa praxis för säkerhet och efterlevnad baserat på vanliga ramverk för efterlevnad. Det här respekterade benchmark-måttet bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) med fokus på molncentrerad säkerhet.

De senaste månaderna har Security Center lista över inbyggda säkerhetsrekommendationer ökat avsevärt för att utöka vår täckning av det här benchmark-måttet.

Från den här versionen är benchmark grunden för Security Center rekommendationerna och helt integrerade som standardprincipinitiativ. 

Alla Azure-tjänster har en säkerhetsbaslinjesida i sin dokumentation. Detta är [till exempel Security Center för baslinjen](security-baseline.md). Dessa baslinjer bygger på Azure Security Benchmark.

Om du använder Security Center instrumentpanel för regelefterlevnad visas två instanser av benchmark under en övergångsperiod:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center instrumentpanel för regelefterlevnad som visar Azure Security Benchmark":::

Befintliga rekommendationer påverkas inte och när benchmark växer återspeglas ändringarna automatiskt i Security Center. 

Mer information finns på följande sidor:

- [Läs mer om Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Anpassa uppsättningen standarder i instrumentpanelen för regelefterlevnad](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Sårbarhetsbedömning för lokala datorer och datorer med flera moln släpps för allmän tillgänglighet (GA)

I oktober presenterade vi en förhandsversion för genomsökning av Azure Arc-aktiverade servrar [med Azure Defender](defender-for-servers-introduction.md)för servrarnas integrerade skanner för sårbarhetsbedömning (drivs av Qualys).

Den har nu släppts för allmän tillgänglighet (GA).

När du har aktiverat Azure Arc på dina icke-Azure-datorer kommer Security Center att erbjuda att distribuera den integrerade sårbarhetsskannern på dem – manuellt och i stor skala.

Med den här uppdateringen kan du frigöra kraften hos **Azure Defender** för servrar för att konsolidera ditt program för sårbarhetshantering för alla dina Azure- och icke-Azure-tillgångar.

Huvudfunktioner:

- Övervaka va-skannerns etableringstillstånd (sårbarhetsbedömning) på Azure Arc datorer
- Etablering av den integrerade VA-agenten till oskyddade Windows- och Linux Azure Arc datorer (manuellt och i stor skala)
- Ta emot och analysera identifierade säkerhetsrisker från distribuerade agenter (manuellt och i stor skala)
- Enhetlig upplevelse för virtuella Azure-datorer Azure Arc datorer

[Läs mer om hur du distribuerar den integrerade sårbarhetsskannern till dina hybriddatorer.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Läs mer om Azure Arc aktiverade servrar](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Säkerhetspoäng för hanteringsgrupper är nu tillgängligt i förhandsversion

Sidan med säkerhetspoäng visar nu aggregerade säkerhetspoäng för dina hanteringsgrupper utöver prenumerationsnivån. Så nu kan du se listan över hanteringsgrupper i din organisation och poängen för varje hanteringsgrupp.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Visa säkerhetspoängen för dina hanteringsgrupper.":::

Läs mer om [säkerhetspoäng och säkerhetskontroller i Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Säkerhetspoäng-API släpps för allmän tillgänglighet (GA)

Nu kan du komma åt dina poäng via [API:et för säkerhetspoäng.](/rest/api/securitycenter/securescores/) API-metoderna ger flexibiliteten att köra frågor mot data och skapa en egen rapporteringsmekanism för dina säkerhetspoäng över tid. Exempel:

- använda **API:et för** säkerhetspoäng för att hämta poängen för en specifik prenumeration
- använd **API:et För säkerhetspoängkontroller** för att visa en lista över säkerhetskontroller och aktuella poäng för dina prenumerationer

Lär dig mer om externa verktyg som är möjliga med API:et för säkerhetspoäng i [området för säkerhetspoäng i vår GitHub-community.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

Läs mer om [säkerhetspoäng och säkerhetskontroller i Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Dns-skydd har lagts till i Azure Defender för App Service

Övertagande av underdomäner är ett vanligt hot med hög allvarlighetsgrad för organisationer. Ett övertagande av en underdomän kan ske när du har en DNS-post som pekar på en avetableerad webbplats. Sådana DNS-poster kallas även för "dinglande DNS-poster". CNAME-poster är särskilt sårbara för det här hotet. 

Övertagande av underdomäner gör det möjligt för hot aktörer att omdirigera trafik avsedd för en organisations domän till en webbplats som utför skadlig aktivitet.

Azure Defender för App Service identifierar nu dingling DNS-poster när en App Service webbplats inaktiveras. Det är då DNS-posten pekar på en resurs som inte finns och din webbplats är sårbar för ett övertagande av en underdomän. Dessa skydd är tillgängliga oavsett om dina domäner hanteras med Azure DNS eller en extern domänregistrator och gäller för både App Service i Windows och App Service på Linux.

Läs mer:

- [App Service aviseringsreferenstabell](alerts-reference.md#alerts-azureappserv) – Innehåller två nya Azure Defender-aviseringar som utlöses när en dinglande DNS-post identifieras
- [Förhindra intjäning av DNS-poster](../security/fundamentals/subdomain-takeover.md) och undvik övertagande av underdomäner – Lär dig mer om hotet vid övertagande av underdomäner och den hotande DNS-aspekten
- [Introduktion till Azure Defender för App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Anslutningsappar för flera moln släpps för allmän tillgänglighet (GA)

När molnarbetsbelastningar ofta omfattar flera molnplattformar måste molnsäkerhetstjänsterna göra samma sak.

Azure Security Center skyddar arbetsbelastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

När du ansluter dina AWS- eller GCP-konton integreras deras interna säkerhetsverktyg som AWS Security Hub och GCP Security Command Center i Azure Security Center.

Den här funktionen innebär Security Center ger synlighet och skydd i alla större molnmiljöer. Några av fördelarna med den här integreringen:

- Automatisk agentetablering – Security Center använder Azure Arc för att distribuera Log Analytics-agenten till dina AWS-instanser
- Principhantering
- Sårbarhetshantering
- Inbäddad slutpunktsidentifiering och svar (EDR)
- Identifiering av felaktiga säkerhetskonfigurationer
- En enda vy som visar säkerhetsrekommendationer från alla molnleverantörer
- Införliva alla dina resurser i Security Center av säkerhetspoängberäkningar
- Utvärderingar av regelefterlevnad för dina AWS- och GCP-resurser

På Security Center menyn väljer du **Anslutningsappar för flera** moln så ser du alternativen för att skapa nya anslutningsappar:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Knappen Lägg till AWS-Security Center på sidan för anslutningsappar för flera moln":::

Läs mer i:
- [Anslut dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md)
- [Anslut dina GCP-konton till Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Undanta hela rekommendationer från din säkerhetspoäng för prenumerationer och hanteringsgrupper

Vi utökar undantagsfunktionerna till att omfatta hela rekommendationer. Tillhandahålla ytterligare alternativ för att finjustera de säkerhetsrekommendationer som Security Center gör för dina prenumerationer, hanteringsgrupp eller resurser.

Ibland visas en resurs som skadad när du vet att problemet har lösts av ett verktyg från tredje part som Security Center inte har identifierat. Eller så visas en rekommendation i ett omfång där du anser att den inte tillhör. Rekommendationen kan vara olämplig för en viss prenumeration. Eller så kanske din organisation har valt att acceptera de risker som är relaterade till den specifika resursen eller rekommendationen.

Med den här förhandsgranskningsfunktionen kan du nu skapa ett undantag för en rekommendation om att:

- **Undanta en resurs** för att se till att den inte listas med de resurser som inte är felfria i framtiden och inte påverkar dina säkerhetspoäng. Resursen visas som ej tillämplig och orsaken visas som "undantagen" med den specifika motivering som du väljer.

- **Undanta en prenumeration eller hanteringsgrupp** för att säkerställa att rekommendationen inte påverkar dina säkerhetspoäng och inte visas för prenumerationen eller hanteringsgruppen i framtiden. Detta gäller för befintliga resurser och eventuella som du skapar i framtiden. Rekommendationen markeras med den specifika motivering som du väljer för det omfång som du har valt.

Läs mer i [Undanta resurser och rekommendationer från din säkerhetspoäng.](exempt-resource.md)



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Användare kan nu begära synlighet för hela klientorganisationen från den globala administratören

Om en användare inte har behörighet att se Security Center data visas nu en länk för att begära behörigheter från organisationens globala administratör. Begäran innehåller den roll som de vill ha och anledningen till varför det är nödvändigt.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banderoll som informerar en användare om att de kan begära behörigheter för hela klientorganisationen.":::

Läs mer i [Begär behörigheter för hela klientorganisationen när dina inte är tillräckliga.](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 rekommendationer för förhandsversion har lagts till för att öka täckningen av Azure Security Benchmark

[Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) är standardprincipinitiativ i Azure Security Center. 

För att öka täckningen för det här benchmark-måttet har följande 35 rekommendationer för förhandsversion lagts till i Security Center.

> [!TIP]
> Förhandsversionsrekommendationer återger inte en resurs som inte är skadad och de ingår inte i beräkningarna av dina säkerhetspoäng. Åtgärda dem när det är möjligt, så att de bidrar till dina poäng när förhandsgranskningsperioden är slut. Läs mer om hur du svarar på dessa rekommendationer [i Åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

| Säkerhetskontroll                     | Nya rekommendationer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera kryptering i vila            | – Azure Cosmos DB-konton ska använda kund hanterade nycklar för att kryptera vilodata<br>- Azure Machine Learning arbetsytor ska krypteras med en kund hanterad nyckel (CMK)<br>– Bring Your Own Key-dataskydd ska vara aktiverat för MySQL-servrar<br>– Bring Your Own Key-dataskydd ska vara aktiverat för PostgreSQL-servrar<br>– Cognitive Services-konton ska aktivera datakryptering med en kund hanterad nyckel (CMK)<br>– Containerregister ska krypteras med en kund hanterad nyckel (CMK)<br>– SQL-hanterade instanser bör använda kund hanterade nycklar för att kryptera vilodata<br>– SQL-servrar bör använda kund hanterade nycklar för att kryptera vilodata<br>– Lagringskonton bör använda kund hanterad nyckel (CMK) för kryptering                                                                                                                                                              |
| Implementera metodtips för säkerhet    | – Prenumerationer ska ha en kontakt-e-postadress för säkerhetsproblem<br> – Automatisk etablering av Log Analytics-agenten ska aktiveras i din prenumeration<br> – E-postavisering för aviseringar med hög allvarlighetsgrad ska vara aktiverat<br> – E-postavisering till prenumerationens ägare om aviseringar med hög allvarlighetsgrad ska vara aktiverat<br> – Rensningsskydd bör vara aktiverat för nyckelvalv<br> – Mjuk borttagning ska vara aktiverat för nyckelvalv |
| Hantera åtkomst och behörigheter        | – Funktionsappar ska ha "Klientcertifikat (inkommande klientcertifikat)" aktiverat |
| Skydda program mot DDoS-attacker | – Web Application Firewall (WAF) ska vara aktiverat för Application Gateway<br> – Web Application Firewall (WAF) ska vara aktiverat för Azure Front Door Service tjänsten |
| Begränsa obehörig nätverksåtkomst | – Brandväggen ska vara aktiverad på Key Vault<br> – Privat slutpunkt ska konfigureras för Key Vault<br> – App Configuration bör använda private link<br> – Azure Cache for Redis ska finnas i ett virtuellt nätverk<br> - Azure Event Grid domäner ska använda privat länk<br> - Azure Event Grid ämnen bör använda privat länk<br> - Azure Machine Learning arbetsytor ska använda privat länk<br> – Azure SignalR Service bör använda private link<br> - Azure Spring Cloud bör använda nätverksinjektion<br> – Containerregister bör inte tillåta obegränsad nätverksåtkomst<br> – Containerregister bör använda private link<br> – Offentlig nätverksåtkomst ska vara inaktiverad för MariaDB-servrar<br> – Offentlig nätverksåtkomst ska vara inaktiverad för MySQL-servrar<br> – Offentlig nätverksåtkomst ska inaktiveras för PostgreSQL-servrar<br> – Lagringskontot bör använda en privat länkanslutning<br> – Lagringskonton bör begränsa nätverksåtkomsten med hjälp av regler för virtuellt nätverk<br> – Image Builder ska använda private link|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Relaterade länkar:

- [Läs mer om Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Läs mer om Azure Database for MariaDB](../mariadb/overview.md)
- [Läs mer om Azure Database for MySQL](../mysql/overview.md)
- [Läs mer om Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>CSV-export av filtrerad lista med rekommendationer 

I november 2020 lade vi till filter på rekommendationssidan ( Listan över rekommendationer[innehåller nu filter](#recommendations-list-now-includes-filters)). I december expanderade vi dessa filter[(sidan Rekommendationer har nya filter för miljö, allvarlighetsgrad och tillgängliga svar).](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses) 

Med det här meddelandet ändrar vi beteendet för knappen Ladda ned till CSV så att **CSV-exporten** endast innehåller de rekommendationer som för närvarande visas i den filtrerade listan. 

I bilden nedan kan du till exempel se att listan har filtrerats efter två rekommendationer. CSV-filen som genereras innehåller statusinformation för varje resurs som påverkas av dessa två rekommendationer.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportera filtrerade rekommendationer till en CSV-fil":::

Läs mer i [Säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>"Ej tillämpligt" resurser rapporteras nu som "kompatibla" i Azure Policy utvärderingar

Tidigare visades resurser som utvärderades för  en rekommendation och inte var tillämpliga i Azure Policy "Icke-kompatibla". Inga användaråtgärder kan ändra sitt tillstånd till "Kompatibel". Med den här ändringen rapporteras de som "kompatibla" för bättre tydlighet.

Den enda påverkan visas i Azure Policy där antalet kompatibla resurser kommer att öka. Säkerhetspoängen påverkas inte i Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportera veckovisa ögonblicksbilder av data om säkerhetspoäng och regelefterlevnad med löpande export (förhandsversion)

Vi har lagt till en ny förhandsgranskningsfunktion i verktygen för löpande [export](continuous-export.md) för att exportera veckovisa ögonblicksbilder av säkerhetspoäng och regelefterlevnadsdata.

När du definierar en löpande export anger du exportfrekvensen:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Välja frekvens för din löpande export":::

- **Direktuppspelning** – utvärderingar skickas i realtid när en resurs hälsotillstånd uppdateras (om inga uppdateringar sker skickas inga data).
- **Ögonblicksbilder** – en ögonblicksbild av det aktuella tillståndet för alla utvärderingar av regelefterlevnad skickas varje vecka (det här är en förhandsversionsfunktion för veckovisa ögonblicksbilder av säkerhetspoäng och data om regelefterlevnad).

Läs mer om de fullständiga funktionerna i den här funktionen i [Exportera kontinuerligt Security Center data](continuous-export.md).

## <a name="december-2020"></a>December 2020

Uppdateringar i december är:

- [Azure Defender för SQL-servrar på datorer är allmänt tillgänglig](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender för SQL-stöd för Azure Synapse Analytics dedikerad SQL-pool är allmänt tillgänglig](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globala administratörer kan nu bevilja sig själva behörigheter på klientorganisationsnivå](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Två nya Azure Defender planer: Azure Defender för DNS och Azure Defender för Resource Manager (i förhandsversion)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Ny sida med säkerhetsaviseringar i Azure Portal (förhandsversion)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Förnyad Security Center erfarenhet av Azure SQL Database & SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Tillgångsinventeringsverktyg och filter har uppdaterats](#asset-inventory-tools-and-filters-updated)
- [Rekommendation om webbappar som begär SSL-certifikat som inte längre ingår i säkerhetspoäng](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Sidan Rekommendationer innehåller nya filter för miljö, allvarlighetsgrad och tillgängliga svar](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Löpande export hämtar nya datatyper och förbättrade deployifnotexist-principer](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender för SQL-servrar på datorer är allmänt tillgänglig

Azure Security Center erbjuder två Azure Defender för SQL-servrar:

- **Azure Defender för Azure SQL databasservrar** – skyddar dina Azure-interna SQL-servrar 
- **Azure Defender för SQL-servrar** på datorer – utökar samma skydd till dina SQL-servrar i hybridmiljöer, molnmiljöer och lokala miljöer

Med det här **Azure Defender för SQL** nu dina databaser och deras data oavsett var de finns.

Azure Defender sql innehåller funktioner för sårbarhetsbedömning. Verktyget för sårbarhetsbedömning innehåller följande avancerade funktioner:

- **Baslinjekonfiguration** (ny!) för att intelligent förfina resultatet av sårbarhetsgenomsökningar till sådana som kan representera verkliga säkerhetsproblem. När du har upprättat baslinjesäkerhetstillståndet rapporterar sårbarhetsbedömningsverktyget endast avvikelser från baslinjetillståndet. Resultat som matchar baslinjen anses skicka efterföljande genomsökningar. På så sätt kan du och dina analytiker fokusera din uppmärksamhet där det är viktigt.
- **Detaljerad benchmark-information** som hjälper dig *att förstå* de identifierade resultaten och varför de relaterar till dina resurser.
- **Reparationsskript som hjälper** dig att minimera identifierade risker.

Läs mer om [Azure Defender för SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender för SQL-stöd för Azure Synapse Analytics dedikerad SQL-pool är allmänt tillgänglig

Azure Synapse Analytics (tidigare SQL DW) är en analystjänst som kombinerar informationslager för företag och stordataanalys. Dedikerade SQL-pooler är funktioner för företagsdatalager i Azure Synapse. Läs mer i [Vad är Azure Synapse Analytics (tidigare SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender för SQL skyddar dina dedikerade SQL-pooler med:

- **Avancerat hotskydd** för att identifiera hot och attacker 
- **Funktioner för sårbarhetsbedömning** för att identifiera och åtgärda felaktiga säkerhetskonfigurationer

Azure Defender för SQL-stöd för Azure Synapse Analytics sql-pooler läggs automatiskt till i Azure SQL-databassamlingen i Azure Security Center. Du hittar en ny "Azure Defender för SQL" på sidan för Synapse-arbetsytan i Azure Portal.

Läs mer om [Azure Defender för SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Globala administratörer kan nu bevilja sig själva behörigheter på klientnivå

En användare med Azure Active Directory rollen  Global administratör kan ha ansvarsområden för hela klientorganisationen, men saknar Azure-behörighet att visa den organisationsomfattande informationen i Azure Security Center. 

Om du vill tilldela dig själv behörigheter på klientorganisationsnivå följer du anvisningarna i Bevilja behörigheter [för hela klientorganisationen till dig själv.](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Två nya Azure Defender planer: Azure Defender för DNS och Azure Defender för Resource Manager (i förhandsversion)

Vi har lagt till två nya molnbaserade funktioner för skydd mot hot i din Azure-miljö.

Dessa nya skydd förbättrar avsevärt din motståndskraft mot attacker från hot aktörer och ökar avsevärt antalet Azure-resurser som skyddas av Azure Defender.

- **Azure Defender för Resource Manager** – övervakar automatiskt alla resurshanteringsåtgärder som utförs i din organisation. Mer information finns i:
    - [Introduktion till Azure Defender för Resource Manager](defender-for-resource-manager-introduction.md)
    - [Svara på aviseringar från Azure Defender för Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista över aviseringar som tillhandahålls av Azure Defender för Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender dns – övervakar** kontinuerligt alla DNS-frågor från dina Azure-resurser. Mer information finns i:
    - [Introduktion till Azure Defender för DNS](defender-for-dns-introduction.md)
    - [Svara på aviseringar från Azure Defender för DNS](defender-for-dns-usage.md)
    - [Lista över aviseringar som tillhandahålls av Azure Defender för DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Ny sida för säkerhetsaviseringar i Azure Portal (förhandsversion)

Azure Security Center säkerhetsaviseringar har gjorts om för att ge:

- **Förbättrad grupperingsupplevelse för aviseringar** – hjälper till att minska aviseringarna och fokusera på de mest relevanta hoten enklare. Listan innehåller anpassningsbara filter och grupperingsalternativ
- **Mer information i aviseringslistan** – till exempel MITRE ATT&ACK-taktiker
- **Knapp för** att skapa exempelaviseringar – om du vill utvärdera Azure Defender-funktioner och testa din aviseringskonfiguration (för SIEM-integrering, e-postaviseringar och arbetsflödesautomatiseringar) kan du skapa exempelaviseringar från alla Azure Defender planer
- **Anpassning med Azure Sentinel** incidentupplevelsen – för kunder som använder båda produkterna är det nu enklare att växla mellan dem och det är enkelt att lära sig en av de andra
- **Bättre prestanda för** stora aviseringslistor
- **Tangentbordsnavigering** via aviseringslistan
- **Aviseringar från Azure Resource Graph** – du kan köra frågor mot aviseringar i Azure Resource Graph, kusto-liknande API för alla dina resurser. Detta är också användbart om du skapar dina egna instrumentpaneler för aviseringar. [Läs mer om Azure Resource Graph](../governance/resource-graph/index.yml).

Om du vill komma åt den nya upplevelsen använder du länken "prova nu" från banderollen överst på sidan med säkerhetsaviseringar.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banderoll med länk till den nya upplevelsen för förhandsgranskningsaviseringar":::

Om du vill skapa exempelaviseringar från den nya aviseringsupplevelsen [kan du Azure Defender generera exempelaviseringar.](security-center-alert-validation.md#generate-sample-azure-defender-alerts)


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Förnyad Security Center erfarenhet av Azure SQL Database & SQL Managed Instance 

Den Security Center upplevelsen i SQL ger åtkomst till följande funktioner Security Center och Azure Defender SQL-funktioner:

- **Säkerhetsrekommendationer** – Security Center regelbundet säkerhetstillståndet för alla anslutna Azure-resurser för att identifiera potentiella säkerhetsfelkonfigurationer. Den ger sedan rekommendationer om hur du åtgärdar dessa sårbarheter och förbättrar organisationens säkerhetsposition.
- **Säkerhetsaviseringar** – en identifieringstjänst som kontinuerligt övervakar Azure SQL aktiviteter för hot som SQL-igenkänning, brute force-attacker och behörighetsmissbruk. Den här tjänsten utlöser detaljerade och åtgärdsorienterade säkerhetsaviseringar i Security Center och tillhandahåller alternativ för att fortsätta undersökningar med Azure Sentinel, Microsofts Azure-inbyggda SIEM-lösning.
- **Resultat** – en tjänst för sårbarhetsbedömning som kontinuerligt övervakar Azure SQL konfigurationer och hjälper till att åtgärda sårbarheter. Utvärderingsgenomsökningar ger en översikt över Azure SQL säkerhets tillstånd tillsammans med detaljerade säkerhetsresultat.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure Security Center säkerhetsfunktioner för SQL är tillgängliga från Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Tillgångsinventeringsverktyg och filter har uppdaterats

Inventeringssidan i Azure Security Center har uppdaterats med följande ändringar:

- **Guider och feedback har** lagts till i verktygsfältet. Då öppnas ett fönster med länkar till relaterad information och verktyg. 
- **Prenumerationsfiltret** har lagts till i standardfiltren som är tillgängliga för dina resurser.
- **Öppna frågelänken** för att öppna de aktuella filteralternativen som en Azure Resource Graph fråga (kallades tidigare "Visa i resursgrafutforskaren").
- **Operatoralternativ** för varje filter. Nu kan du välja bland fler logiska operatorer än =. Du kanske till exempel vill hitta alla resurser med aktiva rekommendationer vars rubriker innehåller strängen "encrypt". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Kontroller för operatoralternativet i tillgångsinventeringens filter":::

Läs mer om inventering i [Utforska och hantera dina resurser med tillgångslager.](asset-inventory.md)


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Rekommendation om webbappar som begär SSL-certifikat som inte längre ingår i säkerhetspoäng

Rekommendationen "Webbappar bör begära ett SSL-certifikat för alla inkommande begäranden" har flyttats från säkerhetskontrollen Hantera åtkomst och behörigheter **(högst** 4 punkter) till **Implementera** rekommenderade säkerhetsmetoder (vilket inte är värt några poäng). 

Att se till att en webbapp begär ett certifikat gör det verkligen säkrare. Men för offentliga webbappar är det irrelevant. Om du använder webbplatsen via HTTP och inte HTTPS får du inget klientcertifikat. Så om ditt program kräver klientcertifikat bör du inte tillåta begäranden till ditt program via HTTP. Läs mer i [Konfigurera ömsesidig TLS-autentisering för Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Med den här ändringen är rekommendationen nu en rekommenderad metod som inte påverkar dina poäng. 

Lär dig vilka rekommendationer som finns i varje säkerhetskontroll [i Säkerhetskontroller och deras rekommendationer.](secure-score-security-controls.md#security-controls-and-their-recommendations)


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Sidan Rekommendationer har nya filter för miljö, allvarlighetsgrad och tillgängliga svar

Azure Security Center övervakar alla anslutna resurser och genererar säkerhetsrekommendationer. Använd dessa rekommendationer för att stärka din hybridmolnstatus och spåra efterlevnad av de principer och standarder som är relevanta för din organisation, bransch och land.

När Security Center fortsätter att utöka sin täckning och sina funktioner ökar listan över säkerhetsrekommendationer varje månad. Se till exempel [29 rekommendationer för förhandsversioner har lagts till för att öka täckningen för Azure Security Benchmark.](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)

Med den växande listan finns det ett behov av att filtrera rekommendationerna för att hitta de som är mest intressanta. I november lade vi till filter på rekommendationssidan (se listan [över rekommendationer innehåller nu filter](#recommendations-list-now-includes-filters)).

Filtren som lagts till den här månaden ger alternativ för att förfina listan över rekommendationer enligt:

- **Miljö** – Visa rekommendationer för dina AWS-, GCP- eller Azure-resurser (eller valfri kombination)
- **Allvarlighetsgrad** – Visa rekommendationer enligt den allvarlighetsgrad som angetts av Security Center
- **Svarsåtgärder** – Visa rekommendationer beroende på tillgängligheten för Security Center svarsalternativ: Snabbkorrigering, Neka och Framtvinga

    > [!TIP]
    > Filtret för svarsåtgärder ersätter det **tillgängliga snabbkorrigeringsfiltret (Ja/Nej).** 
    > 
    > Läs mer om vart och ett av dessa svarsalternativ:
    > - [Snabbkorrigeringsreparation](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Förhindra felkonfiguration med Enforce/Deny-rekommendationer](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Rekommendationer grupperade efter säkerhetskontroll" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Löpande export hämtar nya datatyper och förbättrade deployifnotexist-principer

Azure Security Center verktyg för löpande export kan du exportera Security Center rekommendationer och aviseringar för användning med andra övervakningsverktyg i din miljö.

Med löpande export kan du anpassa vad som ska exporteras och vart det ska gå. Fullständig information finns i Exportera [data Security Center kontinuerligt.](continuous-export.md)

Dessa verktyg har förbättrats och utökats på följande sätt:

- **Den kontinuerliga exportens deployifnotexist-principer förbättrades.** Principerna nu:

    - **Kontrollera om konfigurationen är aktiverad.** Om den inte är det visas principen som icke-kompatibel och skapar en kompatibel resurs. Läs mer om de Azure Policy mallarna på fliken "Distribuera i Azure Policy skala" [i Konfigurera en löpande export.](continuous-export.md#set-up-a-continuous-export)

    - **Stöd för export av säkerhetsresultat.** När du använder Azure Policy kan du konfigurera den löpande exporten så att den innehåller resultat. Detta är relevant när du exporterar rekommendationer som har "underordnade" rekommendationer, till exempel resultat från skannrar för sårbarhetsbedömning eller specifika systemuppdateringar för den "överordnade" rekommendationen "Systemuppdateringar ska installeras på dina datorer".
    
    - **Stöd för export av säkerhetspoängdata.**

- **Utvärderingsdata för regelefterlevnad har lagts till (i förhandsversion).** Du kan nu kontinuerligt exportera uppdateringar av regelefterlevnadsutvärderingar, inklusive för anpassade initiativ, till en Log Analytics-arbetsyta eller händelsehubb. Den här funktionen är inte tillgänglig i nationella/nationella moln.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Alternativen för att inkludera utvärderingsinformation som följer regelefter lag med dina kontinuerliga exportdata.":::


## <a name="november-2020"></a>November 2020

Uppdateringar i november är:

- [29 förhandsgranskningsrekommendationer har lagts till för att öka täckningen för Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 har lagts Security Center på instrumentpanelen för regelefterlevnad](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Rekommendationslistan innehåller nu filter](#recommendations-list-now-includes-filters)
- [Den automatiska etableringen har förbättrats och utökats](#auto-provisioning-experience-improved-and-expanded)
- [Säkerhetspoäng är nu tillgängligt i löpande export (förhandsversion)](#secure-score-is-now-available-in-continuous-export-preview)
- [Rekommendationen "Systemuppdateringar ska installeras på dina datorer" innehåller nu underrekommendationer](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Sidan Principhantering i Azure Portal nu status för standardprinciptilldelningar](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 förhandsgranskningsrekommendationer har lagts till för att öka täckningen för Azure Security Benchmark

Azure Security Benchmark är Microsofts skribenterade, Azure-specifika, uppsättning riktlinjer för bästa praxis för säkerhet och efterlevnad baserat på vanliga ramverk för efterlevnad. [Läs mer om Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

Följande 29 förhandsgranskningsrekommendationer har lagts till i Security Center att öka täckningen för det här benchmark-måttet.

Förhandsversionsrekommendationer återger inte en resurs som inte är skadad och de ingår inte i beräkningarna av dina säkerhetspoäng. Åtgärda dem när det är möjligt, så att de bidrar till dina poäng när förhandsgranskningsperioden är slut. Läs mer om hur du svarar på dessa rekommendationer [i Åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

| Säkerhetskontroll                     | Nya rekommendationer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kryptera data under överföring              | – Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar<br>– Framtvinga SSL-anslutning ska vara aktiverat för MySQL-databasservrar<br>– TLS bör uppdateras till den senaste versionen för din API-app<br>– TLS bör uppdateras till den senaste versionen för funktionsappen<br>– TLS bör uppdateras till den senaste versionen för din webbapp<br>– FTPS måste vara obligatoriskt i din API-app<br>– FTPS måste vara obligatoriskt i funktionsappen<br>– FTPS måste vara obligatoriskt i webbappen                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Hantera åtkomst och behörigheter        | – Webbappar bör begära ett SSL-certifikat för alla inkommande begäranden<br>– Hanterad identitet ska användas i din API-app<br>– Hanterad identitet ska användas i funktionsappen<br>– Hanterad identitet ska användas i din webbapp                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Begränsa obehörig nätverksåtkomst | – Privat slutpunkt ska vara aktiverad för PostgreSQL-servrar<br>– Privat slutpunkt ska vara aktiverad för MariaDB-servrar<br>– Privat slutpunkt ska vara aktiverad för MySQL-servrar                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Aktivera granskning och loggning          | – Diagnostikloggar i App Services ska vara aktiverade                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementera metodtips för säkerhet    | – Azure Backup ska vara aktiverat för virtuella datorer<br>– Geo-redundant säkerhetskopiering ska aktiveras för Azure Database for MariaDB<br>– Geo-redundant säkerhetskopiering ska aktiveras för Azure Database for MySQL<br>– Geo-redundant säkerhetskopiering ska aktiveras för Azure Database for PostgreSQL<br>– PHP bör uppdateras till den senaste versionen för DIN API-app<br>– PHP bör uppdateras till den senaste versionen för din webbapp<br>– Java bör uppdateras till den senaste versionen för din API-app<br>– Java bör uppdateras till den senaste versionen för din funktionsapp<br>– Java bör uppdateras till den senaste versionen för din webbapp<br>– Python bör uppdateras till den senaste versionen för din API-app<br>– Python bör uppdateras till den senaste versionen för din funktionsapp<br>– Python bör uppdateras till den senaste versionen för webbappen<br>– Granskningsbevarande för SQL-servrar ska vara inställt på minst 90 dagar |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Relaterade länkar:

- [Läs mer om Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Läs mer om Azure API-appar](../app-service/app-service-web-tutorial-rest-api.md)
- [Läs mer om Azure-funktionsappar](../azure-functions/functions-overview.md)
- [Läs mer om Azure-webbappar](../app-service/overview.md)
- [Läs mer om Azure Database for MariaDB](../mariadb/overview.md)
- [Läs mer om Azure Database for MySQL](../mysql/overview.md)
- [Läs mer om Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 har lagts Security Center på instrumentpanelen för regelefterlevnad

NIST SP 800-171 R2-standarden är nu tillgänglig som ett inbyggt initiativ för användning med Azure Security Center:s instrumentpanel för regelefterlevnad. Mappningarna för kontrollerna beskrivs i Information om [det inbyggda initiativet NIST SP 800-171 R2 Regulatory Compliance.](../governance/policy/samples/nist-sp-800-171-r2.md) 

Om du vill tillämpa standarden på dina prenumerationer och kontinuerligt övervaka din efterlevnadsstatus följer du anvisningarna i Anpassa uppsättningen standarder på [instrumentpanelen för regelefterlevnad.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="NIST SP 800 171 R2-standarden Security Center på instrumentpanelen för regelefterlevnad":::

Mer information om den här efterlevnadsstandarden finns [i NIST SP 800-171 R2.](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)


### <a name="recommendations-list-now-includes-filters"></a>Rekommendationslistan innehåller nu filter

Nu kan du filtrera listan över säkerhetsrekommendationer enligt ett antal kriterier. I följande exempel har listan med rekommendationer filtrerats för att visa rekommendationer som:

- är **allmänt tillgängliga** (det vill säga inte förhandsversion)
- är för **lagringskonton**
- support **quick fix** remediation

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filter för listan med rekommendationer":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Funktionen för automatisk etablering har förbättrats och utökats

Funktionen för automatisk etablering bidrar till att minska hanteringskostnader genom att installera de tillägg som krävs på nya och befintliga virtuella Azure-datorer så att de kan dra nytta av Security Center skydd. 

När Azure Security Center växer har fler tillägg utvecklats och Security Center kan övervaka en större lista över resurstyper. Verktygen för automatisk etablering har nu utökats för att stödja andra tillägg och resurstyper genom att utnyttja funktionerna i Azure Policy.

Nu kan du konfigurera automatisk etablering av:

- Log Analytics-agent
- (Nytt) Azure Policy för Kubernetes
- (Nytt) Microsoft Dependency Agent

Läs mer i [Auto provisioning agents and extensions from Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Säkerhetspoäng är nu tillgängligt i löpande export (förhandsversion)

Med kontinuerlig export av säkerhetspoäng kan du strömma ändringar av dina poäng i realtid till Azure Event Hubs eller en Log Analytics-arbetsyta. Använd den här funktionen för att:

- spåra dina säkerhetspoäng över tid med dynamiska rapporter
- exportera säkerhetspoängdata till Azure Sentinel (eller någon annan SIEM)
- integrera dessa data med alla processer som du kanske redan använder för att övervaka säkerhetspoäng i din organisation

Läs mer om hur du [kontinuerligt exporterar Security Center data](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Rekommendationen "Systemuppdateringar ska installeras på dina datorer" innehåller nu underrekommendationer

Rekommendationen **Systemuppdateringar bör installeras på dina datorer** har förbättrats. Den nya versionen innehåller underrekommendationer för varje uppdatering som saknas och ger följande förbättringar:

- En omdesignad upplevelse Azure Security Center på Azure Portal. Sidan med rekommendationsinformation **för Systemuppdateringar ska installeras på dina datorer innehåller** en lista över resultat som visas nedan. När du väljer en enda finding öppnas informationsfönstret med en länk till reparationsinformationen och en lista över berörda resurser.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Öppna ett av underrekommendationerna i portalen för den uppdaterade rekommendationen":::

- Berikade data för rekommendationen från Azure Resource Graph (ARG). ARG är en Azure-tjänst som är utformad för att tillhandahålla effektiv resursgranskning. Du kan använda ARG för att fråga i stor skala över en viss uppsättning prenumerationer så att du effektivt kan styra din miljö. 

    Du Azure Security Center arg och [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) för att fråga en mängd olika säkerhetsstatusdata.

    Om du tidigare efterfrågade den här rekommendationen i ARG var den enda tillgängliga informationen att rekommendationen måste åtgärdas på en dator. Följande fråga från den förbättrade versionen returnerar alla saknade systemuppdateringar grupperade efter dator.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Principhanteringssidan i Azure Portal nu status för standardprinciptilldelningar

Nu kan du se om dina prenumerationer har standardprincipen för Security Center tilldelad på sidan Security Center **säkerhetsprincip** i Azure Portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Sidan för principhantering i Azure Security Center visar standardprinciptilldelningarna":::