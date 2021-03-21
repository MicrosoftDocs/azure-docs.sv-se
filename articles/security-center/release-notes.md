---
title: Viktig information för Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 4f2b31a4760a07779eebdd9492731ffe7e324d37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149650"
---
# <a name="whats-new-in-azure-security-center"></a>Vad är nytt i Azure Security Center?

Security Center är i aktiv utveckling och tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här sidan information om nya funktioner, fel korrigeringar och föråldrade funktioner.

Den här sidan uppdateras ofta, så du kan gå tillbaka den ofta. 

Om du vill veta mer om *planerade* ändringar som kommer snart till Security Center, se [viktiga kommande ändringar i Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Om du söker efter objekt som är äldre än sex månader hittar du dem i [arkivet för vad som är nytt i Azure Security Center](release-notes-archive.md).



## <a name="march-2021"></a>Mars 2021

Uppdateringar i mars inkluderar:

- [Azure Firewall Management är integrerad i Security Center](#azure-firewall-management-integrated-into-security-center)
- [SQL sårbarhet Assessment innehåller nu "Inaktivera regel"-upplevelsen (för hands version)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor arbets böcker som är integrerade i Security Center och tre mallar som tillhandahålls](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Instrument panelen för kontroll av efterlevnad innehåller nu Azure audit Reports (för hands version)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Rekommendations data kan visas i Azure Resource Graph med "utforska i ARG"](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Uppdateringar av principerna för distribution av arbets flödes automatisering](#updates-to-the-policies-for-deploying-workflow-automation)
- [Två tidigare rekommendationer skriver inte längre data direkt till Azure aktivitets loggen](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall Management är integrerad i Security Center

När du öppnar Azure Security Center visas sidan Översikt på den första sidan. 

Den här interaktiva instrument panelen ger en enhetlig vy över säkerhets position för dina hybrid moln arbets belastningar. Dessutom visas säkerhets aviseringar, täcknings information med mera.

Som en del av att hjälpa dig att visa din säkerhets status från en central miljö har vi integrerat Azure Firewall Manager på den här instrument panelen. Nu kan du kontrol lera status för brand Väggs täckning i alla nätverk och hantera Azure Firewall-principer centralt från Security Center.

Läs mer om den här instrument panelen på [sidan Översikt för Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center översikts instrument panel med en panel för Azure-brandvägg":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL sårbarhet Assessment innehåller nu "Inaktivera regel"-upplevelsen (för hands version)

Security Center innehåller en inbyggd sårbarhets-skanner som hjälper dig att identifiera, spåra och åtgärda potentiella databas sårbarheter. Resultaten från utvärderings genomsökningarna ger en översikt över säkerhets läget för SQL-datorer och information om eventuella säkerhets brister.

Om du har ett organisations behov av att ignorera en sökning i, i stället för att åtgärda det, kan du inaktivera det. Inaktiverade resultat påverkar inte din säkra poäng eller genererar oönskad brus.

Läs mer i [inaktivera vissa resultat](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor arbets böcker som är integrerade i Security Center och tre mallar som tillhandahålls

Som en del av förbränningen av våren 2021 presenterade vi en integrerad Azure Monitor arbets bok i Security Center.

Du kan utnyttja den nya integrationen för att börja använda färdiga mallar från Security Center galleriet. Med hjälp av mallar för arbets böcker kan du komma åt och bygga dynamiska och visuella rapporter för att spåra din organisations säkerhets position. Dessutom kan du skapa nya arbets böcker baserat på Security Center data eller andra data typer som stöds och snabbt distribuera grupp arbets böcker från Security Center GitHub community.

Tre rapporter för mallar tillhandahålls:

- **Säkra poäng över tid** – spåra dina prenumerations Poäng och ändringar av rekommendationer för dina resurser
- **System uppdateringar** – Visa saknade system uppdateringar av resurser, operativ system, allvarlighets grad med mera
- **Resultat från sårbarhets bedömning** – Visa resultaten av sårbarhets genomsökningar av dina Azure-resurser

Lär dig mer om att använda dessa rapporter eller [skapa egna interaktiva rapporter för Security Center data](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapport om säkra poäng över tid":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Instrument panelen för kontroll av efterlevnad innehåller nu Azure audit Reports (för hands version)

Du kan nu ladda ned Azure-och Dynamics-certifierings rapporter från instrument panelens verktygsfält för regelefterlevnad. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Instrument panelens verktygsfält för regelefterlevnad":::

Du kan välja fliken för de relevanta rapport typerna (PCI, SOC, ISO och andra) och använda filter för att hitta de speciella rapporter som du behöver.

Lär dig mer om [att hantera standarderna i din instrument panel för kontroll av efterlevnad](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrera listan över tillgängliga Azure audit-rapporter":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Rekommendations data kan visas i Azure Resource Graph med "utforska i ARG"

På sidan med rekommendations information finns nu verktygsfälts knappen "utforska i ARG". Använd den här knappen för att öppna en Azure Resource Graph-fråga och utforska, exportera och dela rekommendationens data.

Azure Resource Graph (ARG) ger direkt åtkomst till resursinformation i dina moln miljöer med robusta funktioner för filtrering, gruppering och sortering. Det är ett snabbt och effektivt sätt att fråga information i Azure-prenumerationer program mässigt eller inifrån Azure Portal.

Lär dig mer om [Azure Resource Graph (arg)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Utforska rekommendations data i Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Uppdateringar av principerna för distribution av arbets flödes automatisering

Att automatisera organisationens övervaknings-och incident svars processer kan avsevärt förbättra tiden det tar att undersöka och minimera säkerhets incidenter.

Vi tillhandahåller tre Azure Policy "DeployIfNotExist"-principer som skapar och konfigurerar automatiserings procedurer för arbets flöden så att du kan distribuera automatiseringar i hela organisationen:

|Mål  |Policy  |Princip-ID  |
|---------|---------|---------|
|Arbets flödes automatisering för säkerhets aviseringar|[Distribuera arbetsflödesautomation för Azure Security Center-aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Arbets flödes automatisering för säkerhets rekommendationer|[Distribuera arbetsflödesautomation för Azure Security Center-rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automatisering av arbets flöde för regelefterlevnad-ändringar|[Distribuera arbets flödes automatisering för Azure Security Center regelefterlevnad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Det finns två uppdateringar av funktionerna i dessa principer:

- När de är tilldelade förblir de aktiverade genom tvång.
- Du kan nu anpassa principerna och uppdatera alla parametrar även när de redan har distribuerats. Om en användare till exempel vill lägga till en annan bedömnings nyckel eller redigera en befintlig utvärderings nyckel kan de göra det.

Kom igång med [automatiserings mallar för arbets flöden](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Läs mer om hur du [automatiserar svar på Security Center utlösare](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Två tidigare rekommendationer skriver inte längre data direkt till Azure aktivitets loggen 

Security Center skickar data för nästan alla säkerhets rekommendationer till Azure Advisor vilket i sin tur skriver den till [Azure aktivitets logg](../azure-monitor/essentials/activity-log.md).

För två rekommendationer skrivs data samtidigt direkt till Azure aktivitets loggen. Med den här ändringen slutar Security Center att skriva data för dessa äldre säkerhets rekommendationer direkt till aktivitets loggen. I stället exporterar vi data till Azure Advisor som vi gör för alla andra rekommendationer.

De två tidigare rekommendationerna är:
- Problem med slut punkts skydd bör lösas på dina datorer
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas

Om du har åtkomst till information för dessa två rekommendationer i aktivitets loggens "rekommendation av typen TaskDiscovery", är detta inte längre tillgängligt.



## <a name="february-2021"></a>Februari 2021

Uppdateringar i februari inkluderar:

- [Sidan nya säkerhets aviseringar i Azure Portal som publiceras för allmän tillgänglighet (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes-rekommendationer för arbets belastnings skydd som har släppts för allmän tillgänglighet (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender för slut punkts integrering med Azure Defender stöder nu Windows Server 2019 och Windows 10 Virtual Desktop (WVD) (för hands version)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Direkt länk till princip från rekommendations informations sida](#direct-link-to-policy-from-recommendation-details-page)
- [Rekommendationen om SQL data klassificering påverkar inte längre dina säkra Poäng](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Automatisering av arbets flöden kan utlösas genom ändringar av regelefterlevnad (för hands version)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Förbättringar av till gångs inventerings Sidan](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Sidan nya säkerhets aviseringar i Azure Portal som publiceras för allmän tillgänglighet (GA)

Azure Security Center sidan säkerhets aviseringar har design ATS om för att tillhandahålla:

- **Förbättrad prioritering-upplevelse för aviseringar** – hjälper till att minska aviserings utmattningen och fokusera på de mest relevanta hoten. listan innehåller anpassningsbara filter och grupperingsalternativ.
- **Mer information i aviserings listan** – till exempel MITRE att&ack taktiker.
- **Knapp för att skapa exempel aviseringar** – för att utvärdera Azure Defender-funktioner och testa dina aviseringar. konfiguration (för SIEM-integrering, e-postaviseringar och automatisering av arbets flöden) kan du skapa exempel aviseringar från alla Azure Defender-planer.
- **Anpassning med Azure Sentinel-incidenten** – för kunder som använder båda produkterna är det nu en enklare upplevelse och det är enkelt att lära sig något från den andra.
- **Bättre prestanda** för listor med stora aviseringar.
- **Tangent bords navigering** via aviserings listan.
- **Aviseringar från Azure Resource Graph** – du kan fråga aviseringar i Azure Resource Graph, KUSTO-API för alla dina resurser. Detta är också användbart om du skapar dina egna aviserings instrument paneler. [Lär dig mer om Azure Resource Graph](../governance/resource-graph/index.yml).
- **Funktionen Skapa exempel aviseringar** – för att skapa exempel aviseringar från den nya aviserings upplevelsen, se [generera exempel på Azure Defender-aviseringar](security-center-alert-validation.md#generate-sample-azure-defender-alerts).

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center säkerhets aviserings listan":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes-rekommendationer för arbets belastnings skydd som har släppts för allmän tillgänglighet (GA)

Vi är glada över att kunna meddela allmän tillgänglighet (GA) för de rekommendationer som gäller för Kubernetes arbets belastnings skydd.

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard har Security Center lagt till Kubernetes nivå skärp rekommendationer, inklusive verk ställnings alternativ med Kubernetes-åtkomstkontroll.

När Azure Policy-tillägget för Kubernetes är installerat i Azure Kubernetes service-klustret (AKS), kommer varje begäran till Kubernetes API-servern att övervakas mot den fördefinierade uppsättningen med bästa praxis – visas som 13 säkerhets rekommendationer – innan den sparas i klustret. Du kan sedan konfigurera för att tillämpa bästa praxis och bestämma dem för framtida arbets belastningar.

Du kan till exempel bestämma att privilegierade behållare inte ska skapas och eventuella framtida förfrågningar om detta kommer att blockeras.

Läs mer i [metod tips för arbets belastnings skydd med Kubernetes-åtkomstkontroll](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Även om rekommendationerna var i för hands version visade de inte en AKS-klusterresurs, och de inkluderades inte i beräkningarna av dina säkra poäng. med detta GA-meddelande kommer dessa att ingå i Poäng beräkningen. Om du inte redan har reparerat dem kan det leda till en lätt inverkan på dina säkra poäng. Åtgärda dem när det är möjligt enligt beskrivningen i [åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Microsoft Defender för slut punkts integrering med Azure Defender stöder nu Windows Server 2019 och Windows 10 Virtual Desktop (WVD) (för hands version)

Microsoft Defender för slut punkt är en holistisk, Cloud-levererad slut punkts säkerhets lösning. Den ger riskfylld sårbarhets hantering och utvärdering samt slut punkts identifiering och-svar (EDR). En fullständig lista över fördelarna med att använda Defender för slut punkt tillsammans med Azure Security Center finns i [skydda dina slut punkter med Security Center s integrerade EDR-lösning: Microsoft Defender för slut punkt](security-center-wdatp.md).

När du aktiverar Azure Defender för servrar på en Windows-Server ingår en licens för Defender för slut punkt i planen. Om du redan har aktiverat Azure Defender för servrar och du har Windows 2019-servrar i din prenumeration tar de automatiskt emot Defender för slut punkten med denna uppdatering. Ingen manuell åtgärd krävs. 

Support har nu utökats till att omfatta Windows Server 2019 och [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Om du aktiverar Defender för slut punkt på en dator med Windows Server 2019 kontrollerar du att den uppfyller de krav som beskrivs i [aktivera Microsoft Defender för slut punkts integrering](security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration).

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Direkt länk till princip från rekommendations informations sida

När du granskar informationen om en rekommendation är det ofta bra att kunna se den underliggande principen. För varje rekommendation som stöds av en princip finns det en ny länk från rekommendations informations sidan:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Länk till Azure Policy sida för den speciella principen som stöder en rekommendation":::

Använd den här länken för att Visa princip definitionen och granska utvärderings logiken. 

Om du granskar listan över rekommendationer i [referens guiden för säkerhets rekommendationer](recommendations-reference.md)kan du också se länkar till princip definitions sidorna:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Åtkomst till Azure Policy sidan för en speciell princip direkt från referens sidan för Azure Security Center rekommendationer" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Rekommendationen om SQL data klassificering påverkar inte längre dina säkra Poäng
Rekommendations **känsliga data i SQL-databaserna ska klassificeras** inte längre påverkar dina säkra poäng. Detta är den enda rekommendationen i säkerhets kontrollen **tillämpa data klassificering** , så att kontrollen nu har ett säkert Poäng värde på 0.

En fullständig lista över alla säkerhets kontroller i Security Center, tillsammans med deras resultat och en lista över rekommendationerna, finns i [säkerhets kontroller och deras rekommendationer](secure-score-security-controls.md#security-controls-and-their-recommendations).

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Automatisering av arbets flöden kan utlösas genom ändringar av regelefterlevnad (för hands version)
Vi har lagt till en tredje datatyp till Utlösar alternativen för arbets flödes automatiseringar: ändringar i regelefterlevnad.

Lär dig hur du använder automatiserings verktygen för arbets flöde i [automatiserade svar på Security Center utlösare](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Använda ändringar i regelefterlevnad för att utlösa en arbets flödes automatisering" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Förbättringar av till gångs inventerings Sidan
Security Center sidan till gångs lager har förbättrats på följande sätt:

- Sammanfattningar överst på sidan innehåller nu **oregistrerade prenumerationer** som visar antalet prenumerationer utan Security Center aktiverat.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Antal oregistrerade prenumerationer i sammanfattningarna överst på till gångs inventerings Sidan":::

- Filter har utökats och förbättrats för att inkludera:
    - **Antal** – varje filter visar antalet resurser som uppfyller kriterierna för varje kategori

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Antal i filtren på sidan till gångs lager i Azure Security Center":::

    - **Innehåller undantags filter** (valfritt) – begränsa resultaten till resurser som har/inte har fått undantag. Filtret visas inte som standard, men det är tillgängligt från knappen **Lägg till filter** .

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Om du lägger till filtret innehåller undantag i Azure Security Center sidan till gångs inventering":::

Lär dig mer om hur du [utforskar och hanterar dina resurser med till gångs inventering](asset-inventory.md).

## <a name="january-2021"></a>Januari 2021

Uppdateringar i januari inkluderar:

- [Azure Security Benchmark är nu standard princip initiativet för Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Sårbarhets bedömning för lokala datorer och datorer med flera moln släpps för allmän tillgänglighet (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Säkra Poäng för hanterings grupper finns nu i förhands granskning](#secure-score-for-management-groups-is-now-available-in-preview)
- [Secure score-API har släppts för allmän tillgänglighet (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Dangling DNS-skydd som har lagts till i Azure Defender för App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Flera moln anslutningar släpps för allmän tillgänglighet (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Undanta hela rekommendationer från dina säkra Poäng för prenumerationer och hanterings grupper](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Användarna kan nu begära en synlighet för hela organisationen från sin globala administratör](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 för hands versioner har lagts till för att öka täckningen av Azures säkerhets prestanda](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [CSV-export av filtrerad lista över rekommendationer](#csv-export-of-filtered-list-of-recommendations)
- ["Ej tillämpligt" resurser rapporteras nu som "kompatibla" i Azure Policy bedömningar](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportera vecko Visa ögonblicks bilder av säkra data om Poäng och regelefterlevnad med kontinuerlig export (för hands version)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Benchmark är nu standard princip initiativet för Azure Security Center

Azures säkerhets prestanda är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet.

Under de senaste månaderna har Security Center listan över inbyggda säkerhets rekommendationer vuxit avsevärt för att utöka vår täckning av detta riktmärke.

I den här versionen är riktmärket grunden för Security Centers rekommendationer och helt integrerat som standard policy initiativ. 

Alla Azure-tjänster har en säkerhets bas linje sida i dokumentationen. [Detta är till exempel Security Centerens bas linje](security-baseline.md). Dessa bas linjer bygger på Azures säkerhets benchmark.

Om du använder Security Centers instrument panel för kontroll av efterlevnad visas två instanser av benchmark under en över gångs period:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center s instrument panel för övervakning av säkerhet i Azure":::

Befintliga rekommendationer påverkas inte och när benchmark-effekten växer visas ändringarna automatiskt i Security Center. 

Mer information finns på följande sidor:

- [Läs mer om Azures säkerhets prestanda](../security/benchmarks/introduction.md)
- [Anpassa uppsättningen standarder på instrument panelen för regelefterlevnad](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Sårbarhets bedömning för lokala datorer och datorer med flera moln släpps för allmän tillgänglighet (GA)

I oktober presenterade vi en förhands granskning för att genomsöka Azure Arc-aktiverade servrar med [Azure Defender for servers](defender-for-servers-introduction.md)"Integrated sårbarhet Assessment Scanner (drivs av Qualys).

Den är nu lanserad för allmän tillgänglighet (GA).

När du har aktiverat Azure Arc på datorer som inte är Azure-datorer erbjuder Security Center att distribuera den integrerade sårbarhets skannern på dem – manuellt och i skala.

Med den här uppdateringen kan du utnyttja kraften hos **Azure Defender för servrar** för att konsolidera ditt sårbarhets hanterings program över alla dina Azure-och icke-Azure-tillgångar.

Huvud funktioner:

- Övervaka etablerings status för VA-skannern (sårbarhets bedömning) på Azure Arc-datorer
- Etablering av den integrerade VA-agenten för att ta bort oskyddade Windows-och Linux Azure Arc-datorer (manuellt och i skala)
- Ta emot och analysera identifierade sårbarheter från distribuerade agenter (manuellt och i skala)
- Enhetlig upplevelse för virtuella Azure-datorer och Azure Arc-datorer

[Lär dig mer om att distribuera den integrerade sårbarhets-skannern till dina hybrid datorer](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Läs mer om Azure Arc-aktiverade servrar](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Säkra Poäng för hanterings grupper finns nu i förhands granskning

På sidan säker Poäng visas nu de aggregerade säkra poängen för dina hanterings grupper utöver prenumerations nivån. Nu kan du se listan över hanterings grupper i din organisation och poängen för varje hanterings grupp.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Visa säkra Poäng för dina hanterings grupper.":::

Läs mer om [säkra poäng-och säkerhets kontroller i Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Secure score-API har släppts för allmän tillgänglighet (GA)

Nu kan du komma åt dina poäng via [Secure score-API: et](/rest/api/securitycenter/securescores/). API-metoderna ger flexibiliteten att fråga data och skapa en egen rapporterings mekanism för dina säkra poäng över tid. Exempel:

- Använd API: et för **säkra Poäng** för att hämta poängen för en speciell prenumeration
- Använd API: erna för **säker Poäng** för att visa en lista över säkerhets kontroller och aktuell Poäng för dina prenumerationer

Lär dig mer om externa verktyg som möjliggörs med Secure score-API: et i [det säkra Poäng avsnittet i vår GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Läs mer om [säkra poäng-och säkerhets kontroller i Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Dangling DNS-skydd som har lagts till i Azure Defender för App Service

Under domänens övertag Ande är ett vanligt hot mot hög allvarlighets grad för organisationer. En under domän överköps kan uppstå när du har en DNS-post som pekar på en avetablerad webbplats. Sådana DNS-poster kallas även "Dangling DNS"-poster. CNAME-poster är särskilt sårbara för det här hotet. 

Under domäner överlåter hot aktörer att omdirigera trafik som är avsedd för en organisations domän till en plats som utför skadlig aktivitet.

Azure Defender för App Service identifierar nu Dangling DNS-poster när en App Service webbplats tas ur bruk. Detta är den tidpunkt då DNS-posten pekar på en resurs som inte finns, och din webbplats är sårbar för en under domän överköps. Dessa skydd är tillgängliga oavsett om dina domäner hanteras med Azure DNS eller en extern domän registrator och gäller för både App Service på Windows och App Service i Linux.

Läs mer:

- [App Service varnings referens tabell](alerts-reference.md#alerts-azureappserv) – innehåller två nya Azure Defender-aviseringar som utlöses när en Dangling DNS-post identifieras
- [Förhindra Dangling DNS-poster och Undvik under domän övertag](../security/fundamentals/subdomain-takeover.md) ande – Läs mer om hotet om att under domäner överdrives och Dangling DNS-aspekt
- [Introduktion till Azure Defender för App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Flera moln anslutningar släpps för allmän tillgänglighet (GA)

Med moln arbets belastningar som ofta spänner över flera moln plattformar måste moln säkerhets tjänster göra samma sak.

Azure Security Center skyddar arbets belastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

Genom att ansluta dina AWS-eller GCP-konton integreras deras inbyggda säkerhets verktyg som AWS Security Hub och GCP Security Command Center i Azure Security Center.

Den här funktionen innebär att Security Center ger insyn och skydd i alla större moln miljöer. Några av fördelarna med den här integrationen:

- Automatisk agent etablering – Security Center använder Azure Arc för att distribuera Log Analytics-agenten till dina AWS-instanser
- Principhantering
- Sårbarhetshantering
- Identifiering och svar för inbäddad slut punkt (EDR)
- Identifiering av felkonfigurationer av säkerhet
- En enskild vy som visar säkerhets rekommendationer från alla moln leverantörer
- Införliva alla resurser i Security Center säkra Poäng beräkningar
- Regelefterlevnad för AWS-och GCP-resurser

Från Security Center menyn väljer du **flera moln anslutningar** och du ser alternativen för att skapa nya anslutningar:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Knappen Lägg till AWS-konto på Security Center sidan med flera moln anslutningar":::

Läs mer i:
- [Anslut dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md)
- [Anslut dina GCP-konton till Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Undanta hela rekommendationer från dina säkra Poäng för prenumerationer och hanterings grupper

Vi utökar undantags funktionen för att inkludera hela rekommendationer. Ange ytterligare alternativ för att finjustera de säkerhets rekommendationer som Security Center gör för prenumerationer, hanterings grupper eller resurser.

Ibland visas en resurs som ohälsosam när du vet att problemet har lösts av ett tredjepartsverktyg som Security Center inte har identifierat. Eller så visas en rekommendation i ett omfång där du känner att den inte tillhör. Rekommendationen kan vara olämplig för en speciell prenumeration. Eller så kanske din organisation har valt att godkänna de risker som är relaterade till den aktuella resursen eller rekommendationen.

Med den här förhands gransknings funktionen kan du nu skapa ett undantag för att:

- **Undanta en resurs** för att se till att den inte visas i listan med felaktiga resurser i framtiden och inte påverkar dina säkra poäng. Resursen visas som ej tillämplig och orsaken visas som "undantagen" med den önskade justering som du väljer.

- **Undanta en prenumeration eller hanterings grupp** för att säkerställa att rekommendationen inte påverkar dina säkra poäng och inte visas för prenumerationen eller hanterings gruppen i framtiden. Detta relaterar till befintliga resurser och alla som du skapar i framtiden. Rekommendationen markeras med den speciella motivering som du väljer för det omfång som du har valt.

Läs mer i [undanta resurser och rekommendationer från dina säkra Poäng](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Användarna kan nu begära en synlighet för hela organisationen från sin globala administratör

Om en användare inte har behörighet att se Security Center data, ser de nu en länk för att begära behörigheter från organisationens globala administratör. Begäran omfattar den roll de vill och motiveringen för varför det är nödvändigt.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banderoll som informerar användaren om att de kan begära behörigheter för hela klienten.":::

Läs mer i [begära behörigheter för klient organisationen när dina inte är tillräckliga](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 för hands versioner har lagts till för att öka täckningen av Azures säkerhets prestanda

Azure Security Benchmark är standard policy initiativ i Azure Security Center. 

För att öka omfattningen av detta benchmark har följande 35 för hands versions rekommendationer lagts till i Security Center.

> [!TIP]
> För hands versions rekommendationer återger inte en resurs som inte är felfri och ingår inte i beräkningarna av dina säkra poäng. Åtgärda dem när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng. Lär dig mer om hur du svarar på dessa rekommendationer i att [åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

| Säkerhets kontroll                     | Nya rekommendationer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera kryptering i vila            | -Azure Cosmos DB konton ska använda Kundhanterade nycklar för att kryptera data i vila<br>-Azure Machine Learning arbets ytor ska vara krypterade med en kundhanterad nyckel (CMK)<br>– Ta med din egen nyckel data skydd måste vara aktiverat för MySQL-servrar<br>– Ta med din egen nyckel data skydd måste vara aktiverat för PostgreSQL-servrar<br>-Cognitive Services konton ska aktivera data kryptering med en kundhanterad nyckel (CMK)<br>– Behållar register ska krypteras med en kundhanterad nyckel (CMK)<br>-SQL-hanterade instanser bör använda Kundhanterade nycklar för att kryptera data i vila<br>– SQL-servrar bör använda Kundhanterade nycklar för att kryptera data i vila<br>-Lagrings konton ska använda kundhanterad nyckel (CMK) för kryptering                                                                                                                                                              |
| Implementera rekommenderade säkerhets metoder    | -Prenumerationer måste ha en e-postadress till en kontakt med säkerhets problem<br> – Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration<br> – E-postmeddelande för aviseringar med hög allvarlighets grad ska aktive ras<br> – E-postmeddelande till Prenumerationens ägare för aviseringar med hög allvarlighets grad måste vara aktiverat<br> – Nyckel valv ska ha rensnings skyddet aktiverat<br> – Nyckel valv ska ha mjuk borttagning aktiverat |
| Hantera åtkomst och behörigheter        | -Funktions appar ska ha "klient certifikat (inkommande klient certifikat)" aktiverat |
| Skydda program mot DDoS-attacker | – Brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway<br> – Brand vägg för webbaserade program (WAF) ska vara aktive rad för Azure-tjänsten för front dörr tjänsten |
| Begränsa obehörig nätverks åtkomst | – Brand väggen ska vara aktive rad på Key Vault<br> -Privat slut punkt ska konfigureras för Key Vault<br> – App-konfigurationen ska använda privat länk<br> – Azure cache för Redis bör finnas i ett virtuellt nätverk<br> -Azure Event Grid domäner ska använda privat länk<br> -Azure Event Grid ämnen ska använda privat länk<br> -Azure Machine Learning arbets ytor ska använda privat länk<br> – Azure SignalR-tjänsten bör använda privat länk<br> – Azure våren Cloud ska använda nätverks inmatning<br> – Behållar register ska inte tillåta obegränsad nätverks åtkomst<br> – Behållar register ska använda privat länk<br> – Åtkomst till offentligt nätverk ska inaktive ras för MariaDB-servrar<br> – Åtkomst till offentligt nätverk ska inaktive ras för MySQL-servrar<br> – Åtkomst till offentligt nätverk ska inaktive ras för PostgreSQL-servrar<br> – Lagrings kontot bör använda en anslutning för privat anslutning<br> -Lagrings konton bör begränsa nätverks åtkomst med hjälp av regler för virtuella nätverk<br> -VM Image Builder-mallar ska använda privat länk|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Relaterade länkar:

- [Läs mer om Azures säkerhets prestanda](../security/benchmarks/introduction.md)
- [Läs mer om Azure Database for MariaDB](../mariadb/overview.md)
- [Läs mer om Azure Database for MySQL](../mysql/overview.md)
- [Läs mer om Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>CSV-export av filtrerad lista över rekommendationer 

I november 2020 lade vi till filter till sidan rekommendationer ([rekommendations listan innehåller nu filter](#recommendations-list-now-includes-filters)). I december expanderade vi dessa filter ([rekommendationer sidan har nya filter för miljö, allvarlighets grad och tillgängliga svar](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Med det här meddelandet ändrar vi beteendet för knappen **Ladda ned till CSV** så att CSV-exporten bara innehåller de rekommendationer som visas i den filtrerade listan. 

I bilden nedan kan du till exempel se att listan har filtrerats till två rekommendationer. CSV-filen som genereras innehåller statusinformation för varje resurs som påverkas av dessa två rekommendationer.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportera filtrerade rekommendationer till en CSV-fil":::

Läs mer i [säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>"Ej tillämpligt" resurser rapporteras nu som "kompatibla" i Azure Policy bedömningar

Tidigare fanns resurser som utvärderades för en rekommendation och som **inte är tillämpliga** i Azure policy som "icke-kompatibla". Inga användar åtgärder kan ändra sin status till "kompatibel". Med den här ändringen har de rapporter ATS som "kompatibla" för förbättrad klarhet.

Den enda påverkan visas i Azure Policy där antalet kompatibla resurser kommer att öka. Det kommer inte att påverka dina säkra poäng i Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportera vecko Visa ögonblicks bilder av säkra data om Poäng och regelefterlevnad med kontinuerlig export (för hands version)

Vi har lagt till en ny förhands gransknings funktion i de [löpande export](continuous-export.md) verktygen för att exportera vecko Visa ögonblicks bilder av säkra poäng och regelefterlevnad.

När du definierar en löpande export anger du export frekvensen:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Välja frekvens för din kontinuerliga export":::

- **Strömning** – utvärderingar skickas i real tid när en resurs hälso tillstånd uppdateras (om inga uppdateringar inträffar skickas inga data).
- **Ögonblicks bilder** – en ögonblicks bild av det aktuella läget för alla utvärderingar av efterlevnad kommer att skickas varje vecka (detta är en förhands gransknings funktion för varje veckas ögonblicks bilder av säkra poäng och information om regelefterlevnad).

Läs mer om de fullständiga funktionerna i den här funktionen för att [kontinuerligt exportera Security Center data](continuous-export.md).

## <a name="december-2020"></a>December 2020

Uppdateringar i december inkluderar:

- [Azure Defender för SQL-servrar på datorer är allmänt tillgänglig](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender for SQL-stöd för Azure Synapse Analytics-dedikerad SQL-pool är allmänt tillgänglig](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globala administratörer kan nu ge sig själva behörigheter på klient nivå](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Två nya Azure Defender-planer: Azure Defender för DNS och Azure Defender för Resource Manager (för hands version)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Sidan nya säkerhets aviseringar i Azure Portal (för hands version)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Förvarad Security Center upplevelse i Azure SQL Database & SQL-hanterad instans](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Verktyg och filter för till gångs inventering har uppdaterats](#asset-inventory-tools-and-filters-updated)
- [Rekommendation om webbappar som begär SSL-certifikat som inte längre ingår i säkra Poäng](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Sidan rekommendationer har nya filter för miljö, allvarlighets grad och tillgängliga svar](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Kontinuerlig export hämtar nya data typer och förbättrade deployifnotexist-principer](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender för SQL-servrar på datorer är allmänt tillgänglig

Azure Security Center erbjuder två Azure Defender-planer för SQL-servrar:

- **Azure Defender för Azure SQL Database-servrar** – skyddar dina Azure-inhemska SQL-servrar 
- **Azure Defender för SQL-servrar på datorer** – utökar samma skydd till dina SQL-servrar i hybrid miljöer, i molnet och i lokala miljöer

Med det här meddelandet skyddar **Azure Defender för SQL** nu dina databaser och deras data oavsett var de befinner sig.

Azure Defender för SQL innehåller funktioner för sårbarhets bedömning. Verktyget för sårbarhets bedömning innehåller följande avancerade funktioner:

- **Bas linje konfiguration** (ny!) för att intelligent förfina resultaten av sårbarhets ökningar till dem som kan representera verkliga säkerhets problem. När du har fastställt ditt grundläggande säkerhets tillstånd rapporterar verktyget för sårbarhets bedömning endast avvikelser från det aktuella bas läget. Resultat som matchar bas linjen betraktas som att skicka efterföljande genomsökningar. På så sätt kan du och din analytiker fokusera på det som är viktigt.
- **Detaljerad information** som hjälper dig att *förstå* de identifierade avgörandena och varför de relaterar till dina resurser.
- **Reparations skript** som hjälper dig att minimera identifierade risker.

Läs mer om [Azure Defender för SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender for SQL-stöd för Azure Synapse Analytics-dedikerad SQL-pool är allmänt tillgänglig

Azure Synapse Analytics (tidigare SQL DW) är en analys tjänst som kombinerar företags data lager hantering och stor data analys. Dedikerade SQL-pooler är företags data lager funktionerna i Azure dataSynapses. Läs mer i [Vad är Azure Synapse Analytics (tidigare SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender för SQL skyddar dina dedikerade SQL-pooler med:

- **Avancerat skydd** för att identifiera hot och attacker 
- **Funktioner för sårbarhets bedömning** för att identifiera och åtgärda felaktig säkerhet

Azure Defender för SQL har stöd för Azure Synapse Analytics SQL-pooler läggs automatiskt till i Azure SQL Database-paket i Azure Security Center. Du hittar en ny "Azure Defender för SQL"-flik på din Synapse-arbetsyta på sidan Azure Portal.

Läs mer om [Azure Defender för SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Globala administratörer kan nu ge sig själva behörigheter på klient nivå

En användare med rollen Azure Active Directory **Global administratör** kan ha ansvar för hela företaget, men saknar Azure-behörighet att visa den här informationen i organisationen i Azure Security Center. 

Om du vill tilldela dig själv behörighet för klient organisations nivå följer du instruktionerna i [bevilja behörighet för klient organisationen till dig själv](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Två nya Azure Defender-planer: Azure Defender för DNS och Azure Defender för Resource Manager (för hands version)

Vi har lagt till två nya Cloud-inbyggda funktioner för skydd mot bredd skydd i din Azure-miljö.

Dessa nya skydd förbättrar avsevärt din återhämtning mot attacker från hot aktörer och ökar avsevärt antalet Azure-resurser som skyddas av Azure Defender.

- **Azure Defender för Resource Manager** – övervakar automatiskt alla resurs hanterings åtgärder som utförs i din organisation. Mer information finns i:
    - [Introduktion till Azure Defender för Resource Manager](defender-for-resource-manager-introduction.md)
    - [Svara på aviseringar från Azure Defender för Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista över aviseringar från Azure Defender för Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender för DNS** – kontinuerligt övervakar alla DNS-frågor från dina Azure-resurser. Mer information finns i:
    - [Introduktion till Azure Defender för DNS](defender-for-dns-introduction.md)
    - [Svara på aviseringar från Azure Defender för DNS](defender-for-dns-usage.md)
    - [Lista över aviseringar som tillhandahålls av Azure Defender för DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Sidan nya säkerhets aviseringar i Azure Portal (för hands version)

Azure Security Center sidan säkerhets aviseringar har design ATS om för att tillhandahålla:

- **Förbättrad prioritering-upplevelse för aviseringar** – hjälper till att minska aviserings utmattningen och fokusera på de mest relevanta hoten. listan innehåller anpassningsbara filter och grupperingsalternativ
- **Mer information i aviserings listan** – till exempel MITRE att&ack taktiker
- **Knapp för att skapa exempel aviseringar** – om du vill utvärdera Azure Defender-funktioner och testa aviserings konfigurationen (för Siem-integrering, e-postaviseringar och automatisering av arbets flöden) kan du skapa exempel aviseringar från alla Azure Defender-planer
- **Anpassning med Azure Sentinel-incidenten** – för kunder som använder båda produkterna är det nu en mer smidig upplevelse och det är enkelt att lära sig något från den andra
- **Bättre prestanda** för stora aviserings listor
- **Tangent bords navigering** genom aviserings listan
- **Aviseringar från Azure Resource Graph** – du kan fråga aviseringar i Azure Resource Graph, KUSTO-API för alla dina resurser. Detta är också användbart om du skapar dina egna aviserings instrument paneler. [Lär dig mer om Azure Resource Graph](../governance/resource-graph/index.yml).

Du kommer åt den nya upplevelsen genom att använda länken prova nu från banderollen högst upp på sidan säkerhets aviseringar.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banderoll med en länk till den nya förhands gransknings aviserings upplevelsen":::

Information om hur du skapar exempel aviseringar från den nya aviserings miljön finns i [generera exempel på Azure Defender-aviseringar](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Förvarad Security Center upplevelse i Azure SQL Database & SQL-hanterad instans 

Security Centers upplevelsen i SQL ger åtkomst till följande Security Center och Azure Defender för SQL-funktioner:

- **Säkerhets rekommendationer** – Security Center regelbundet analyserar säkerhets statusen för alla anslutna Azure-resurser för att identifiera möjliga felkonfigurationer av säkerhet. Det ger dig rekommendationer om hur du kan åtgärda dessa sårbarheter och förbättra organisationernas säkerhets position.
- **Säkerhets aviseringar** – en identifierings tjänst som kontinuerligt övervakar Azure SQL-aktiviteter för hot som SQL-inmatning, brute-force-attacker och missbruk av privilegier. Den här tjänsten utlöser detaljerade och åtgärdade säkerhets aviseringar i Security Center och ger alternativ för att fortsätta utredningar med Azure Sentinel, Microsofts Azure-inhemska SIEM-lösning.
- **Resultat** – en sårbarhets bedömnings tjänst som kontinuerligt övervakar Azure SQL-konfigurationer och hjälper till att åtgärda sårbarheter. Utvärderings genomsökningar ger en översikt över Azure SQL-säkerhetstillstånden tillsammans med detaljerade säkerhets resultat.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure Security Centers säkerhetsfunktioner för SQL finns i Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Verktyg och filter för till gångs inventering har uppdaterats

Inventerings sidan i Azure Security Center har uppdaterats med följande ändringar:

- **Guider och feedback** har lagts till i verktygsfältet. Då öppnas ett fönster med länkar till relaterad information och verktyg. 
- **Prenumerations filter** läggs till i de standard filter som är tillgängliga för dina resurser.
- **Öppna frågans** länk om du vill öppna de aktuella filter alternativen som en Azure Resource Graph-fråga (kallades tidigare i resurs diagram Utforskaren).
- **Operator alternativ** för varje filter. Nu kan du välja mellan fler logiska operatorer än ' = '. Du kanske till exempel vill hitta alla resurser med aktiva rekommendationer vars titlar innehåller strängen "kryptera". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Kontroller för alternativet operator i till gångs lager filter":::

Lär dig mer om inventering i [utforska och hantera dina resurser med till gångs inventering](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Rekommendation om webbappar som begär SSL-certifikat som inte längre ingår i säkra Poäng

Rekommendationen "webbappar ska begära ett SSL-certifikat för alla inkommande begär Anden" har flyttats från säkerhets kontrollen **Hantera åtkomst och behörigheter** (värt högst 4 punkter) i **implementeringen av rekommenderade säkerhets metoder** (vilket är värt inga poäng). 

Att se till att en webbapp begär ett certifikat gör det mycket säkrare. För offentliga webbappar är det dock irrelevant. Om du ansluter till din webbplats via HTTP och inte HTTPS får du inga klient certifikat. Så om ditt program kräver klient certifikat bör du inte tillåta begär anden till ditt program via HTTP. Läs mer i [Konfigurera ömsesidig TLS-autentisering för Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Med den här ändringen är rekommendationen nu en rekommenderad metod som inte påverkar dina poäng. 

Lär dig vilka rekommendationer som finns i varje säkerhets kontroll i [säkerhets kontroller och deras rekommendationer](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Sidan rekommendationer har nya filter för miljö, allvarlighets grad och tillgängliga svar

Azure Security Center övervakar alla anslutna resurser och genererar säkerhets rekommendationer. Använd dessa rekommendationer för att förstärka ditt hybrid moln position och spåra efterlevnaden av de principer och standarder som är relevanta för din organisation, bransch och land.

När Security Center fortsätter att utöka sin täckning och funktioner, växer listan över säkerhets rekommendationer varje månad. Se till exempel [29 Preview-rekommendationer som har lagts till för att öka täckningen av Azures säkerhets benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Med den växande listan måste du filtrera rekommendationerna för att hitta de bästa intressena. I november lade vi till filter till sidan rekommendationer (se [rekommendations listan innehåller nu filter](#recommendations-list-now-includes-filters)).

De filter som läggs till den här månaden innehåller alternativ för att förfina rekommendationer listan enligt:

- **Miljö** – Visa rekommendationer för dina AWS-, GCP-eller Azure-resurser (eller valfri kombination)
- **Allvarlighets grad** – Visa rekommendationer enligt allvarlighets GRADS klassificeringen som angetts av Security Center
- **Svars åtgärder** – Visa rekommendationer enligt tillgänglighet för Security Center svars alternativ: snabb korrigering, neka och framtvinga

    > [!TIP]
    > Filtret för svars åtgärder ersätter filtret **snabb korrigering tillgängligt (Ja/Nej)** . 
    > 
    > Läs mer om vart och ett av följande svars alternativ:
    > - [Snabb korrigerings reparation](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Förhindra felkonfiguration med Enforce/Deny-rekommendationer](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Rekommendationer grupperade efter säkerhets kontroll" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Kontinuerlig export hämtar nya data typer och förbättrade deployifnotexist-principer

Med Azure Security Center verktyg för kontinuerlig export kan du exportera Security Centers rekommendationer och aviseringar som du kan använda med andra övervaknings verktyg i din miljö.

Med kontinuerlig export kan du helt anpassa vad som ska exporteras och var det ska gå. Fullständig information finns i  [exportera Security Center data kontinuerligt](continuous-export.md).

Dessa verktyg har förbättrats och utökats på följande sätt:

- **Deployifnotexist principer för kontinuerlig export har förbättrats**. Nu är principerna:

    - **Kontrol lera om konfigurationen är aktive rad.** Om den inte är det visas principen som icke-kompatibel och skapa en kompatibel resurs. Läs mer om de angivna Azure Policy mallarna i avsnittet "distribuera på skala med Azure Policy" i [Konfigurera en löpande export](continuous-export.md#set-up-a-continuous-export).

    - **Stöd för att exportera säkerhets resultat.** När du använder Azure Policy-mallarna kan du konfigurera den kontinuerliga exporten så att den innehåller undersöknings resultat. Detta är relevant när du exporterar rekommendationer som har "sub"-rekommendationer, t. ex. resultat från skannrar i sårbarhets bedömning eller system uppdateringar för "överordnad" rekommendation "system uppdateringar bör installeras på dina datorer".
    
    - **Stöd för att exportera säkra Poäng data.**

- **Data för regelefterlevnad som lagts till (i för hands version).** Du kan nu regelbundet exportera uppdateringar till regler för regelefterlevnad, inklusive för anpassade initiativ till en Log Analytics arbets yta eller Event Hub. Den här funktionen är inte tillgänglig i nationella/suveräna moln.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Alternativen för att inkludera reglerad bedömnings information med dina kontinuerliga export data.":::


## <a name="november-2020"></a>November 2020

Uppdateringarna i november omfattar:

- [29 Preview-rekommendationer har lagts till för att öka täckningen av Azures säkerhets benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 har lagts till Security Center kontroll panelen för regelefterlevnad](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Listan rekommendationer innehåller nu filter](#recommendations-list-now-includes-filters)
- [Förbättrad och utökad automatisk etablerings upplevelse](#auto-provisioning-experience-improved-and-expanded)
- [Säkra poäng är nu tillgängligt i löpande export (förhands granskning)](#secure-score-is-now-available-in-continuous-export-preview)
- ["System uppdateringar bör installeras på dina datorer"-rekommendationen innehåller nu underrekommendationer](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Sidan princip hantering i Azure Portal visar nu status för standard princip tilldelningar](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 Preview-rekommendationer har lagts till för att öka täckningen av Azures säkerhets benchmark

Azures säkerhets Benchmark är Microsoft-skapade, Azure-/regionsspecifika, uppsättning rikt linjer för metod tips för säkerhet och efterlevnad baserat på vanliga ramverk för efterlevnad. [Läs mer om Azure Security Benchmark](../security/benchmarks/introduction.md).

Följande 29 Preview-rekommendationer har lagts till Security Center för att öka omfattningen av detta benchmark.

För hands versions rekommendationer återger inte en resurs som inte är felfri och ingår inte i beräkningarna av dina säkra poäng. Åtgärda dem när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng. Lär dig mer om hur du svarar på dessa rekommendationer i att [åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

| Säkerhets kontroll                     | Nya rekommendationer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kryptera data under överföring              | -Framtvinga SSL-anslutning måste vara aktiverat för PostgreSQL-databas servrar<br>-Framtvinga SSL-anslutning måste vara aktiverat för MySQL-databas servrar<br>-TLS bör uppdateras till den senaste versionen för din API-app<br>-TLS bör uppdateras till den senaste versionen för din Function-app<br>-TLS bör uppdateras till den senaste versionen för din webbapp<br>-FTPS måste anges i din API-app<br>-FTPS måste anges i din Function-app<br>-FTPS måste krävas i din webbapp                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Hantera åtkomst och behörigheter        | – Webbappar ska begära ett SSL-certifikat för alla inkommande begär Anden<br>-Hanterad identitet ska användas i din API-app<br>-Hanterad identitet ska användas i din Function-app<br>-Hanterad identitet ska användas i din webbapp                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Begränsa obehörig nätverks åtkomst | -Privat slut punkt måste vara aktive rad för PostgreSQL-servrar<br>-Privat slut punkt måste vara aktive rad för MariaDB-servrar<br>-Privat slut punkt måste vara aktive rad för MySQL-servrar                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Aktivera granskning och loggning          | -Diagnostikloggar i App Services ska vara aktive rad                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementera rekommenderade säkerhets metoder    | -Azure Backup ska vara aktive rad för virtuella datorer<br>-Geo-redundant säkerhets kopiering ska aktive ras för Azure Database for MariaDB<br>-Geo-redundant säkerhets kopiering ska aktive ras för Azure Database for MySQL<br>-Geo-redundant säkerhets kopiering ska aktive ras för Azure Database for PostgreSQL<br>– PHP bör uppdateras till den senaste versionen för din API-app<br>– PHP bör uppdateras till den senaste versionen för din webbapp<br>– Java bör uppdateras till den senaste versionen för din API-app<br>– Java bör uppdateras till den senaste versionen för din Function-app<br>– Java bör uppdateras till den senaste versionen för din webbapp<br>– Python bör uppdateras till den senaste versionen för din API-app<br>– Python bör uppdateras till den senaste versionen för din Function-app<br>– Python bör uppdateras till den senaste versionen för din webbapp<br>– Gransknings kvarhållning för SQL-servrar ska vara minst 90 dagar |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Relaterade länkar:

- [Läs mer om Azures säkerhets prestanda](../security/benchmarks/introduction.md)
- [Läs mer om Azure API Apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Läs mer om Azure Function-appar](../azure-functions/functions-overview.md)
- [Läs mer om Azure-Webbappar](../app-service/overview.md)
- [Läs mer om Azure Database for MariaDB](../mariadb/overview.md)
- [Läs mer om Azure Database for MySQL](../mysql/overview.md)
- [Läs mer om Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 har lagts till Security Center kontroll panelen för regelefterlevnad

NIST SP 800-171 R2 Standard är nu tillgängligt som ett inbyggt initiativ för användning med Azure Security Centers instrument panel för kontroll av efterlevnad. Mappningarna för kontrollerna beskrivs i [information om det inbyggda initiativet NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Om du vill använda standarderna för dina prenumerationer och kontinuerligt övervaka din kompatibilitetsstatus använder du anvisningarna i [Anpassa uppsättningen med standarder på instrument panelen för övervakning av efterlevnad](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="NIST SP 800 171 R2 Standard i Security Center kontroll panelen för regelefterlevnad":::

Mer information om den här standarden för efterlevnad finns i [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Listan rekommendationer innehåller nu filter

Nu kan du filtrera listan över säkerhets rekommendationer enligt ett villkors intervall. I följande exempel har listan rekommendationer filtrerats för att Visa rekommendationer som:

- är **allmänt tillgängliga** (dvs. inte för hands version)
- är för **lagrings konton**
- stöd för **snabb korrigerings** reparation

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filter för listan rekommendationer":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Förbättrad och utökad automatisk etablerings upplevelse

Funktionen automatisk etablering hjälper till att minska hanterings kostnaderna genom att installera de nödvändiga tilläggen på nya och befintliga virtuella Azure-datorer så att de kan dra nytta av Security Centers skydd. 

När Azure Security Center växer har fler tillägg utvecklats och Security Center kan övervaka en större lista med resurs typer. De automatiska etablerings verktygen har nu utökats för att stödja andra tillägg och resurs typer genom att använda funktionerna i Azure Policy.

Nu kan du konfigurera automatisk etablering av:

- Log Analytics-agent
- Nyårs Azure Policy tillägg för Kubernetes
- Nyårs Microsoft-beroende agent

Läs mer i [automatiska etablerings agenter och tillägg från Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Säkra poäng är nu tillgängligt i löpande export (förhands granskning)

Med kontinuerlig export av säkra Poäng kan du strömma ändringar i resultatet i real tid till Azure Event Hubs eller en Log Analytics arbets yta. Använd den här funktionen för att:

- spåra dina säkra poäng över tid med dynamiska rapporter
- Exportera säkra Poäng data till Azure Sentinel (eller någon annan SIEM)
- integrera dessa data med alla processer som du kanske redan använder för att övervaka säkra poäng i din organisation

Läs mer om hur du [exporterar Security Center data kontinuerligt](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"System uppdateringar bör installeras på dina datorer"-rekommendationen innehåller nu underrekommendationer

**System uppdateringarna bör installeras på** dator rekommendationerna har förbättrats. Den nya versionen innehåller underrekommendationer för varje saknad uppdatering och ger följande förbättringar:

- En omdesignad upplevelse på Azure Security Center sidor i Azure Portal. Rekommendations informations sidan för **System uppdateringar bör installeras på dina datorer** och innehåller en lista över de resultat som visas nedan. När du väljer en enskild sökning öppnas informations fönstret med en länk till reparations informationen och en lista över resurser som påverkas.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Öppna en av under rekommendationerna i Portal upplevelsen för den uppdaterade rekommendationen":::

- Omfattande data för rekommendationen från Azure Resource Graph (ARG). ARG är en Azure-tjänst som är utformad för att tillhandahålla effektiv resurs utforskning. Du kan använda ARG för att fråga i skala över en specifik uppsättning prenumerationer så att du effektivt kan styra din miljö. 

    För Azure Security Center kan du använda ARG och [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) för att fråga efter en mängd säkerhets position data.

    Om du tidigare har efterfrågat den här rekommendationen i ARG, var den enda tillgängliga informationen att rekommendationen måste åtgärdas på en dator. Följande fråga för den förbättrade versionen returnerar alla system uppdateringar som saknas grupperade efter dator.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Sidan princip hantering i Azure Portal visar nu status för standard princip tilldelningar

Nu kan du se om dina prenumerationer har tilldelats standard Security Center principen, på sidan **säkerhets princip** för Security Center i Azure Portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Sidan princip hantering i Azure Security Center visar standard princip tilldelningarna":::

## <a name="october-2020"></a>Oktober 2020

Uppdateringar i oktober inkluderar:
- [Sårbarhets bedömning för datorer på plats och flera moln (för hands version)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall-rekommendation har lagts till (för hands version)](#azure-firewall-recommendation-added-preview)
- [Auktoriserade IP-intervall bör definieras i Kubernetes Services-rekommendationen med snabb korrigering](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Instrument panelen för kontroll av efterlevnad innehåller nu alternativ för att ta bort standarder](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft. Security/securityStatuses-tabellen har tagits bort från Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Sårbarhets bedömning för datorer på plats och flera moln (för hands version)

[Azure Defender for servers](defender-for-servers-introduction.md)"Integrated sårbarhet Assessment Scanner (drivs av Qualys) genomsöker nu Azure Arc-aktiverade servrar.

När du har aktiverat Azure Arc på datorer som inte är Azure-datorer erbjuder Security Center att distribuera den integrerade sårbarhets skannern på dem – manuellt och i skala.

Med den här uppdateringen kan du utnyttja kraften hos **Azure Defender för servrar** för att konsolidera ditt sårbarhets hanterings program över alla dina Azure-och icke-Azure-tillgångar.

Huvud funktioner:

- Övervaka etablerings status för VA-skannern (sårbarhets bedömning) på Azure Arc-datorer
- Etablering av den integrerade VA-agenten för att ta bort oskyddade Windows-och Linux Azure Arc-datorer (manuellt och i skala)
- Ta emot och analysera identifierade sårbarheter från distribuerade agenter (manuellt och i skala)
- Enhetlig upplevelse för virtuella Azure-datorer och Azure Arc-datorer

[Lär dig mer om att distribuera den integrerade sårbarhets-skannern till dina hybrid datorer](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Läs mer om Azure Arc-aktiverade servrar](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall-rekommendation har lagts till (för hands version)

En ny rekommendation har lagts till för att skydda alla dina virtuella nätverk med Azure-brandväggen.

Rekommendationen, **virtuella nätverk bör skyddas av Azure Firewall** och du får hjälp att begränsa åtkomsten till dina virtuella nätverk och förhindra potentiella hot genom att använda Azure-brandväggen.

Läs mer om [Azure-brandväggen](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Auktoriserade IP-intervall bör definieras i Kubernetes Services-rekommendationen med snabb korrigering

Rekommendationen **för auktoriserade IP-adressintervall bör definieras på Kubernetes Services** nu med ett alternativ för snabb korrigering.

Mer information om den här rekommendationen och alla andra Security Center rekommendationer finns i [säkerhets rekommendationer – en referens guide](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="De auktoriserade IP-intervallen bör definieras i Kubernetes Services-rekommendation med alternativet för snabb korrigering":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Instrument panelen för kontroll av efterlevnad innehåller nu alternativ för att ta bort standarder

Security Centers instrument panel för kontroll av efterlevnad ger insikter om din position utifrån hur du uppfyller särskilda efterlevnadsprinciper och krav.

Instrument panelen innehåller en standard uppsättning regler. Om någon av de angivna standarderna inte är relevant för din organisation, är det nu en enkel process att ta bort dem från användar gränssnittet för en prenumeration. Standarder kan bara tas bort på *prenumerations* nivå. inte hanterings gruppens omfattning.

Läs mer i [ta bort en standard från din instrument panel](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft. Security/securityStatuses-tabellen har tagits bort från Azure Resource Graph (ARG)

Azure Resource Graph är en tjänst i Azure som är utformad för att tillhandahålla effektiv resurs utforskning med möjlighet att fråga i skala över en specifik uppsättning prenumerationer så att du effektivt kan styra din miljö. 

För Azure Security Center kan du använda ARG och [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) för att fråga efter en mängd säkerhets position data. Exempel:

- Till gångs lager använder (ARG)
- Vi har dokumenterat en exempel-ARG-fråga för att [identifiera konton utan Multi-Factor Authentication (MFA) aktiverat](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

I ARG finns det data tabeller som du kan använda i dina frågor.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer och de tillgängliga tabellerna":::

> [!TIP]
> I ARG-dokumentationen visas en lista över alla tillgängliga tabeller i [Azures resurs diagram tabell och referens för resurs typ](../governance/resource-graph/reference/supported-tables-resources.md).

Från den här uppdateringen har **Microsoft. Security/securityStatuses-** tabellen tagits bort. SecurityStatuses-API: et är fortfarande tillgängligt.

Data ersättning kan användas av Microsoft. Security/assessments-tabellen.

Den största skillnaden mellan Microsoft. Security/securityStatuses och Microsoft. Security/Assessment är att när den första visar agg regering av utvärderingar, innehåller sekunderna en enda post för varje.

Till exempel, Microsoft. Security/securityStatuses skulle returnera ett resultat med en matris med två policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Microsoft. Security/bedömningar innehåller en post för varje sådan princip utvärdering enligt följande:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Exempel på konvertering av en befintlig ARG-fråga med hjälp av securityStatuses för att nu använda bedömnings tabellen:**

Fråga som refererar till SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Ersättnings fråga för bedömnings tabellen:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Läs mer på följande länkar:
- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)