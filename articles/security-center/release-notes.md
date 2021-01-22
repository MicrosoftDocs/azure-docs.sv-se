---
title: Viktig information för Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2021
ms.author: memildin
ms.openlocfilehash: d171e3a7f1f0d63d0a5d130c6e19b246bf0cd795
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661822"
---
# <a name="whats-new-in-azure-security-center"></a>Vad är nytt i Azure Security Center?

Security Center är i aktiv utveckling och tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här sidan information om nya funktioner, fel korrigeringar och föråldrade funktioner.

Den här sidan uppdateras ofta, så du kan gå tillbaka den ofta. 

Om du vill veta mer om *planerade* ändringar som kommer snart till Security Center, se [viktiga kommande ändringar i Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Om du söker efter objekt som är äldre än sex månader hittar du dem i [arkivet för vad som är nytt i Azure Security Center](release-notes-archive.md).


## <a name="january-2021"></a>Januari 2021

Uppdateringar i december inkluderar:

- [Sårbarhets bedömning för lokala datorer och datorer med flera moln släpps för allmän tillgänglighet (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Secure score-API har släppts för allmän tillgänglighet (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [CSV-export av filtrerad lista över rekommendationer](#csv-export-of-filtered-list-of-recommendations)
- ["Ej tillämpligt" resurser rapporteras nu som "kompatibla" i Azure Policy bedömningar](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)


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


### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Secure score-API har släppts för allmän tillgänglighet (GA)

Nu kan du komma åt dina poäng via [Secure score-API: et](/rest/api/securitycenter/securescores/). API-metoderna ger flexibiliteten att fråga data och skapa en egen rapporterings mekanism för dina säkra poäng över tid. Du kan till exempel använda **Secure Scores** -API: et för att hämta poängen för en speciell prenumeration. Dessutom kan du använda API: et för **säker Poäng** för att visa en lista över säkerhets kontrollerna och de aktuella poängen för dina prenumerationer.

Exempel på externa verktyg som möjliggörs med Secure score-API: et finns i avsnittet [Secure Scores i vår GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Läs mer om [säkra poäng-och säkerhets kontroller i Azure Security Center](secure-score-security-controls.md).


### <a name="csv-export-of-filtered-list-of-recommendations"></a>CSV-export av filtrerad lista över rekommendationer 

I november 2020 lade vi till filter till sidan rekommendationer ([rekommendations listan innehåller nu filter](#recommendations-list-now-includes-filters)). I december expanderade vi dessa filter ([rekommendationer sidan har nya filter för miljö, allvarlighets grad och tillgängliga svar](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Med det här meddelandet ändrar vi beteendet för knappen **Ladda ned till CSV** så att CSV-exporten bara innehåller de rekommendationer som visas i den filtrerade listan. 

I bilden nedan kan du till exempel se att listan har filtrerats till två rekommendationer. CSV-filen som genereras innehåller statusinformation för varje resurs som påverkas av dessa två rekommendationer.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportera filtrerade rekommendationer till en CSV-fil":::

Läs mer i [säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>"Ej tillämpligt" resurser rapporteras nu som "kompatibla" i Azure Policy bedömningar

Tidigare fanns resurser som utvärderades för en rekommendation och som **inte är tillämpliga** i Azure policy som "icke-kompatibla". Inga användar åtgärder kan ändra sin status till "kompatibel". Med den här ändringen har de rapporter ATS som "kompatibla" för förbättrad klarhet.

Den enda påverkan visas i Azure Policy där antalet kompatibla resurser kommer att öka. Det kommer inte att påverka dina säkra poäng i Azure Security Center.



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

Om du vill tilldela dig själv behörighet för klient organisations nivå följer du instruktionerna i [bevilja behörighet för klient organisationen till dig själv](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself).


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
- **Operator alternativ** för varje filter. Nu kan du välja bland fler logiska operatorer än ' = '. Du kanske till exempel vill hitta alla resurser med aktiva rekommendationer vars titlar innehåller strängen "kryptera". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Kontroller för alternativet operator i till gångs lager filter":::

Lär dig mer om inventering i [utforska och hantera dina resurser med till gångs inventering](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Rekommendation om webbappar som begär SSL-certifikat som inte längre ingår i säkra Poäng

Rekommendationen "webbappar ska begära ett SSL-certifikat för alla inkommande begär Anden" har flyttats från säkerhets kontrollen **Hantera åtkomst och behörigheter** (värt högst 4 punkter) i **implementeringen av rekommenderade säkerhets metoder** (vilket är värt inga poäng). 

Att se till att dina webbappar begär ett certifikat gör det verkligen säkrare. För offentliga webbappar är det dock irrelevant. Om du ansluter till din webbplats via HTTP och inte HTTPS får du inga klient certifikat. Så om ditt program kräver klient certifikat bör du inte tillåta begär anden till ditt program via HTTP. Läs mer i [Konfigurera ömsesidig TLS-autentisering för Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Med den här ändringen är rekommendationen nu en rekommenderad metod som inte påverkar dina poäng. 

Lär dig vilka rekommendationer som finns i varje säkerhets kontroll i [säkerhets kontroller och deras rekommendationer](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Sidan rekommendationer har nya filter för miljö, allvarlighets grad och tillgängliga svar

Azure Security Center övervakar alla anslutna resurser och genererar säkerhets rekommendationer. Använd dessa rekommendationer för att förstärka ditt hybrid moln position och spåra efterlevnaden av de principer och standarder som är relevanta för din organisation, bransch och land.

När Security Center fortsätter att utöka sin täckning och funktioner, växer listan över säkerhets rekommendationer varje månad. Se till exempel [29 Preview-rekommendationer som har lagts till för att öka täckningen av Azures säkerhets benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Med den växande listan behöver du kunna filtrera fram rekommendationerna för bästa intresse. I november lade vi till filter till sidan rekommendationer (se [rekommendations listan innehåller nu filter](#recommendations-list-now-includes-filters)).

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
- [Rekommendationen "system uppdateringar bör installeras på dina datorer" innehåller nu under rekommendationer](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [Sidan princip hantering i Azure Portal visar nu status för standard princip tilldelningar](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 Preview-rekommendationer har lagts till för att öka täckningen av Azures säkerhets benchmark

Azures säkerhets prestanda är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. [Läs mer om Azure Security Benchmark](../security/benchmarks/introduction.md).

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

När Azure Security Center växer har fler tillägg utvecklats och Security Center kan övervaka en större lista med resurs typer. De automatiska etablerings verktygen har nu utökats för att stödja ytterligare tillägg och resurs typer genom att använda funktionerna i Azure Policy.

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


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>Rekommendationen "system uppdateringar bör installeras på dina datorer" innehåller nu under rekommendationer

**System uppdateringarna bör installeras på** dator rekommendationerna har förbättrats. Den nya versionen innehåller under rekommendationer för varje saknad uppdatering och ger följande förbättringar:

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

Instrument panelen innehåller en standard uppsättning regler. Om någon av de angivna standarderna inte är relevant för din organisation, är det nu en enkel process att bara ta bort dem från användar gränssnittet för en prenumeration. Standarder kan bara tas bort på *prenumerations* nivå. inte hanterings gruppens omfattning.

Läs mer i [ta bort en standard från din instrument panel](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft. Security/securityStatuses-tabellen har tagits bort från Azure Resource Graph (ARG)

Azure Resource Graph är en tjänst i Azure som är utformad för att tillhandahålla effektiv resurs utforskning med möjlighet att fråga i skala över en specifik uppsättning prenumerationer så att du effektivt kan styra din miljö. 

För Azure Security Center kan du använda ARG och [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) för att fråga efter en mängd säkerhets position data. Till exempel:

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


## <a name="september-2020"></a>September 2020

Uppdateringar i september omfattar:
- [Security Center får ett nytt utseende!](#security-center-gets-a-new-look)
- [Azure Defender lanserad](#azure-defender-released)
- [Azure Defender för Key Vault är allmänt tillgänglig](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender för lagrings skydd för filer och ADLS Gen2 är allmänt tillgängligt](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Till gångs inventerings verktyg är nu allmänt tillgängliga](#asset-inventory-tools-are-now-generally-available)
- [Inaktivera en särskilt sårbarhet som söker efter genomsökningar av behållar register och virtuella datorer](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Undanta en resurs från en rekommendation](#exempt-a-resource-from-a-recommendation)
- [AWS-och GCP-anslutningar i Security Center ta en upplevelse i flera moln](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Paket för Kubernetes-skydds rekommendation](#kubernetes-workload-protection-recommendation-bundle)
- [Rön om sårbarhets bedömning är nu tillgängliga i löpande export](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Förhindra felaktig säkerhet genom att tvinga rekommendationer när du skapar nya resurser](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Bättre rekommendationer för nätverks säkerhets grupp](#network-security-group-recommendations-improved)
- [Föråldrad för hands version AKS-rekommendation "Pod Security Policies definieras i Kubernetes Services"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-postaviseringar från Azure Security Center bättre](#email-notifications-from-azure-security-center-improved)
- [Säkra Poäng omfattar inte för hands versions rekommendationer](#secure-score-doesnt-include-preview-recommendations)
- [Rekommendationerna innehåller nu en allvarlighets grad och aktualitets intervallet](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center får ett nytt utseende!

Vi har publicerat ett uppdaterat användar gränssnitt för Security Center Portal sidor. De nya sidorna innehåller en ny översikts sida och instrument paneler för säkra poäng, till gångs lager och Azure Defender.

På sidan för den omdesignade översikten finns nu en panel för att komma åt säkra poäng-, inventarie-och Azure Defender-instrumentpaneler. Den har också en panel som länkar till instrument panelen för kontroll av efterlevnad.

Läs mer om [översikts sidan](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender lanserad

**Azure Defender** är CWPP (Cloud Protection Platform) som är integrerad i Security Center för avancerad, intelligent, skydd av dina Azure-och hybrid arbets belastningar. Den ersätter Security Center standard alternativ för pris nivå. 

När du aktiverar Azure Defender från avsnittet **priser och inställningar** i Azure Security Center, aktive ras följande Defender-planer samtidigt och ger omfattande försvars områden i din miljö:

- [Azure Defender för servrar](defender-for-servers-introduction.md)
- [Azure Defender för App Service](defender-for-app-service-introduction.md)
- [Azure Defender för Storage](defender-for-storage-introduction.md)
- [Azure Defender för SQL](defender-for-sql-introduction.md)
- [Azure Defender för Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender för containerregister](defender-for-container-registries-introduction.md)

Var och en av dessa planer beskrivs separat i Security Center-dokumentationen.

Med dess dedikerade instrument panel tillhandahåller Azure Defender säkerhets aviseringar och Avancerat skydd för virtuella datorer, SQL-databaser, behållare, webb program, nätverk och mycket annat.

[Läs mer om Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender för Key Vault är allmänt tillgänglig

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. 

**Azure Defender för Key Vault** ger Azure-inbyggt Avancerat skydd för Azure Key Vault, vilket ger ytterligare ett lager av säkerhets information. Som tillägg skyddar Azure Defender för Key Vault därför många av resurserna beroende på dina Key Vault-konton.

Den valfria planen är nu GA. Funktionen var i för hands version som "Avancerat skydd för Azure Key Vault".

Key Vault sidorna i Azure Portal innehåller nu också en särskild **säkerhets** sida för **Security Center** rekommendationer och aviseringar.

Läs mer i [Azure Defender för Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender för lagrings skydd för filer och ADLS Gen2 är allmänt tillgängligt 

**Azure Defender för lagring** identifierar potentiellt skadlig aktivitet på dina Azure Storage-konton. Dina data kan skyddas oavsett om de lagras som BLOB-behållare, fil resurser eller data sjöar.

Support för [Azure Files](../storage/files/storage-files-introduction.md) och [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) är nu allmänt tillgänglig.

Från 1 oktober 2020 börjar vi debitera för att skydda resurser på dessa tjänster.

Läs mer i [Azure Defender för lagring](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Till gångs inventerings verktyg är nu allmänt tillgängliga

Sidan till gångs inventeringen för Azure Security Center ger en enda sida för att Visa säkerhets position för de resurser som du har anslutit till Security Center.

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen.

När en resurs har utestående rekommendationer visas de i inventeringen.

Läs mer i [utforska och hantera dina resurser med till gångs inventering](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Inaktivera en särskilt sårbarhet som söker efter genomsökningar av behållar register och virtuella datorer

Azure Defender innehåller sårbarhets skannrar för att skanna bilder i dina Azure Container Registry och dina virtuella datorer.

Om du har ett organisations behov av att ignorera en sökning i, i stället för att åtgärda det, kan du inaktivera det. Inaktiverade resultat påverkar inte din säkra poäng eller genererar oönskad brus.

När en sökning matchar de kriterier som du har definierat i inaktiverade regler visas de inte i listan över resultat.

Det här alternativet är tillgängligt från sidan rekommendationer information för:

- **Säkerhets risker i Azure Container Registry avbildningar bör åtgärdas**
- **Säkerhets risker på dina virtuella datorer bör åtgärdas**

Läs mer i [inaktivera speciella resultat för dina behållar avbildningar](defender-for-container-registries-usage.md#disable-specific-findings-preview) och [inaktivera de olika resultaten för dina virtuella datorer](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Undanta en resurs från en rekommendation

Ibland visas en resurs som inte är felfri för en speciell rekommendation (och därmed sänka dina säkra poäng) även om du känner till att den inte borde vara det. Den kan ha åtgärd ATS av en process som inte spåras av Security Center. Eller så kanske din organisation har valt att godkänna risken för den aktuella resursen. 

I sådana fall kan du skapa en undantags regel och se till att resursen inte listas bland de felaktiga resurserna i framtiden. Dessa regler kan omfatta dokumenterade motiveringar som beskrivs nedan.

Läs mer i [undanta en resurs från rekommendationer och säkra Poäng](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS-och GCP-anslutningar i Security Center ta en upplevelse i flera moln

Med moln arbets belastningar som ofta spänner över flera moln plattformar måste moln säkerhets tjänster göra samma sak.

Azure Security Center skyddar nu arbets belastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

När du registrerar dina AWS-och GCP-konton i Security Center integreras AWS-säkerhetshubben, GCP säkerhets kommando och Azure Security Center. 

Läs mer i [Anslut dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md) och [Anslut dina GCP-konton till Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Paket för Kubernetes-skydds rekommendation

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard, lägger Security Center till Kubernetes nivå skärp rekommendationer, inklusive verk ställnings alternativ med Kubernetes-åtkomstkontroll.

När du har installerat Azure Policy-tillägget för Kubernetes i ditt AKS-kluster, kommer varje begäran till Kubernetes API-servern att övervakas mot den fördefinierade uppsättningen med bästa metoder innan den sparas i klustret. Du kan sedan konfigurera för att tillämpa bästa praxis och bestämma dem för framtida arbets belastningar.

Du kan till exempel bestämma att privilegierade behållare inte ska skapas och eventuella framtida förfrågningar om detta kommer att blockeras.

Läs mer i [metod tips för arbets belastnings skydd med Kubernetes-åtkomstkontroll](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Rön om sårbarhets bedömning är nu tillgängliga i löpande export

Använd kontinuerlig export för att strömma aviseringar och rekommendationer i real tid till Azure Event Hubs, Log Analytics arbets ytor eller Azure Monitor. Därifrån kan du integrera dessa data med Siem (till exempel Azure Sentinel, Power BI, Azure Datautforskaren med mera.

Security Center s verktyg för integrerad sårbarhets bedömning returnerar resultat om dina resurser som åtgärds bara rekommendationer i en överordnad rekommendation, till exempel säkerhets risker i dina virtuella datorer, bör åtgärdas. 

Säkerhets resultaten är nu tillgängliga för export genom löpande export när du väljer rekommendationer och aktiverar alternativet **Inkludera säkerhets resultat** .

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Ta med säkerhets brister växla i kontinuerlig export konfiguration" :::

Relaterade sidor:

- [Security Center är en integrerad lösning för sårbarhets bedömning för Azure Virtual Machines](deploy-vulnerability-assessment-vm.md)
- [Security Center är en integrerad lösning för sårbarhets bedömning för Azure Container Registry avbildningar](defender-for-container-registries-usage.md)
- [Löpande export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Förhindra felaktig säkerhet genom att tvinga rekommendationer när du skapar nya resurser

Fel konfiguration av säkerhet är en stor orsak till säkerhets incidenter. Security Center kan nu hjälpa till *att förhindra* fel konfiguration av nya resurser med avseende på vissa rekommendationer. 

Den här funktionen kan hjälpa till att hålla arbets belastningarna säkra och stabilisera dina säkra poäng.

Att framtvinga en säker konfiguration, som baseras på en viss rekommendation, erbjuds i två lägen:

- Genom att använda Azure Policy **neka** kan du förhindra att resurser som inte är felfria skapas

- Med alternativet **tillämpa** kan du dra nytta av Azure policys **DeployIfNotExist** -påverkan och automatiskt reparera icke-kompatibla resurser när de skapas
 
Detta är tillgängligt för de valda säkerhets rekommendationerna och finns överst på sidan med resurs information.

Läs mer i [förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Bättre rekommendationer för nätverks säkerhets grupp

Följande säkerhets rekommendationer relaterade till nätverks säkerhets grupper har förbättrats för att minska vissa instanser av falska positiva identifieringar.

- Alla nätverks portar bör vara begränsade på NSG som är kopplade till den virtuella datorn
- Hanterings portar bör stängas på dina virtuella datorer
- Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper
- Undernät ska associeras med en nätverks säkerhets grupp


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Föråldrad för hands version AKS-rekommendation "Pod Security Policies definieras i Kubernetes Services"

Förhands gransknings rekommendationen "Pod Security Policies" måste definieras i Kubernetes Services ", vilket beskrivs i dokumentationen för [Azure Kubernetes-tjänsten](../aks/use-pod-security-policies.md) .

Funktionen Pod Security Policy (för hands version) är inställd för utfasning och kommer inte längre att vara tillgänglig efter den 15 oktober 2020 i Azure Policy för AKS.

När Pod säkerhets princip (förhands granskning) är föråldrad måste du inaktivera funktionen på alla befintliga kluster med hjälp av den föråldrade funktionen för att utföra framtida kluster uppgraderingar och hålla koll på Azure-supporten.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-postaviseringar från Azure Security Center bättre

Följande delar av e-postmeddelandena om säkerhets aviseringar har förbättrats: 

- Har lagt till möjligheten att skicka e-postmeddelanden om aviseringar för alla allvarlighets nivåer
- Har lagt till möjligheten att meddela användare med olika Azure-roller i prenumerationen
- Vi meddelar proaktivt prenumerations ägare som standard på aviseringar med hög allvarlighets grad (som har hög sannolikhet för att vara äkta)
- Vi har tagit bort fältet telefonnummer från konfigurations sidan för e-postaviseringar

Läs mer i [Konfigurera e-postaviseringar för säkerhets aviseringar](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Säkra Poäng omfattar inte för hands versions rekommendationer 

Security Center utvärderar kontinuerligt dina resurser, prenumerationer och din organisation efter säkerhets problem. Den sammanställer sedan alla resultat i en enda poäng så att du snabbt kan tala om din aktuella säkerhets situation: ju högre poäng, desto lägre är den identifierade risk nivån.

När nya hot upptäcks görs nya säkerhets rådgivning i Security Center via nya rekommendationer. För att undvika oväntade ändringar av dina säkra poäng och för att tillhandahålla en respitperiod där du kan utforska nya rekommendationer innan de påverkar dina resultat, ingår rekommendationer som har flaggats som för **hands version** inte längre i beräkningarna av dina säkra poäng. De bör fortfarande åtgärdas när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng.

För **hands versions** rekommendationer återges inte heller en resurs "ohälsosam".

Ett exempel på en förhands gransknings rekommendation:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Rekommendation med förhands gransknings flaggan":::

[Läs mer om säkra Poäng](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Rekommendationerna innehåller nu en allvarlighets grad och aktualitets intervallet

På informations sidan för rekommendationer finns nu en indikator för aktualitets intervall (när det är relevant) och en tydlig visning av rekommendationens allvarlighets grad.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Sidan rekommendation visar aktualitet och allvarlighets grad":::



## <a name="august-2020"></a>Augusti 2020

Uppdateringarna i augusti inkluderar:

- [Till gångs lager – kraftfull ny vy av position till gångarnas säkerhet](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Stöd har lagts till för Azure Active Directory säkerhets inställningar (för Multi-Factor Authentication)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Rekommendation för tjänstens huvud namn lades till](#service-principals-recommendation-added)
- [Sårbarhets bedömning på virtuella datorer – rekommendationer och konsoliderade principer](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nya AKS säkerhets principer har lagts till i ASC_default initiativ – för användning av privata för hands kunder](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Till gångs lager – kraftfull ny vy av position till gångarnas säkerhet

Security Center till gångs lagret (för närvarande i för hands version) ger dig ett sätt att Visa säkerhets position för de resurser som du har anslutit till Security Center.

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen. När en resurs har utestående rekommendationer visas de i inventeringen.

Du kan använda vyn och dess filter för att utforska dina säkerhets position data och vidta ytterligare åtgärder baserat på dina resultat.

Läs mer om [till gångs inventering](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Stöd har lagts till för Azure Active Directory säkerhets inställningar (för Multi-Factor Authentication)

Security Center har lagt till fullständigt stöd för [säkerhets standarder](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), Microsofts kostnads fria identitets skydds skydd.

Säkerhets standarder tillhandahåller förkonfigurerade säkerhets inställningar för identitet för att skydda din organisation från vanliga problem med identitets angrepp. Säkerhets standarder skyddar redan över 5 000 000 klienter totalt. 50 000-klienter skyddas också av Security Center.

Security Center har nu en säkerhets rekommendation när den identifierar en Azure-prenumeration utan säkerhets inställningar som är aktiverade. Fram till nu rekommenderar Security Center att du aktiverar Multi-Factor Authentication med villkorlig åtkomst, som är en del av Azure Active Directory (AD) Premium-licensen. För kunder som använder Azure AD kostnads fri rekommenderar vi nu att du aktiverar säkerhets inställningar. 

Vårt mål är att uppmuntra fler kunder att skydda sina moln miljöer med MFA och minimera en av de högsta riskerna som också är mest påverkan av dina [säkra Poäng](secure-score-security-controls.md).

Läs mer om [säkerhets inställningar](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Rekommendation för tjänstens huvud namn lades till

En ny rekommendation har lagts till för att rekommendera att Security Center kunder som använder hanterings certifikat för att hantera sina prenumerationer växlar till tjänstens huvud namn.

Rekommendationen är att **tjänstens huvud namn ska användas för att skydda dina prenumerationer i stället för hanterings certifikat** som rekommenderar att du använder tjänstens huvud namn eller Azure Resource Manager för att hantera dina prenumerationer på ett säkert sätt. 

Läs mer om [program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Sårbarhets bedömning på virtuella datorer – rekommendationer och konsoliderade principer

Security Center granskar de virtuella datorerna för att identifiera om de använder en lösning för sårbarhets bedömning. Om ingen lösning för sårbarhets bedömning hittas ger Security Center en rekommendation för att förenkla distributionen.

När det finns sårbarheter ger Security Center en rekommendation som sammanfattar resultaten för att undersöka och åtgärda vid behov.

För att säkerställa en konsekvent upplevelse för alla användare, oavsett vilken skanner typ de använder, har vi förenat fyra rekommendationer i följande två:

|Enhetlig rekommendation|Ändra beskrivning|
|----|:----|
|**En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer**|Ersätter följande två rekommendationer:<br> **•** Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys (nu föråldrad) (ingår i standard-nivån)<br> **•** Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer (nu föråldrade) (standard-och kostnads fria nivåer)|
|**Säkerhets risker på dina virtuella datorer bör åtgärdas**|Ersätter följande två rekommendationer:<br>**•** Åtgärda säkerhets problem som finns på dina virtuella datorer (drivs av Qualys) (nu föråldrade)<br>**•** Sårbarheter bör åtgärdas av en lösning för sårbarhets bedömning (nu föråldrad)|
|||

Nu ska du använda samma rekommendation för att distribuera Security Centers tillägg för sårbarhets bedömning eller en privat licensierad lösning ("BYOL") från en partner, till exempel Qualys eller Rapid7.

När säkerhets risker upptäcks och rapporteras till Security Center får du dessutom en enda rekommendation som meddelar dig om avgöranden, oavsett vilken lösning för sårbarhets bedömning som identifierade dem.

#### <a name="updating-dependencies"></a>Uppdaterar beroenden

Om du har skript, frågor eller automatiseringar som refererar till föregående rekommendationer eller princip nycklar/namn, använder du tabellerna nedan för att uppdatera referenserna:

##### <a name="before-august-2020"></a>Före augusti 2020

|Rekommendation|Omfång|
|----|:----|
|**Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys)**<br>Nyckel: 550e890b-e652-4D22-8274-60b3bdb24c63|Inbyggd|
|**Åtgärda sårbarheter som finns på dina virtuella datorer (drivs av Qualys)**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggd|
|**Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer**<br>Nyckel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning**<br>Nyckel: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Policy|Omfång|
|----|:----|
|**Sårbarhets bedömning ska vara aktiverat på virtuella datorer**<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Inbyggd|
|**Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning**<br>Princip-ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Från augusti 2020

|Rekommendation|Omfång|
|----|:----|
|**En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer**<br>Nyckel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Inbyggda + BYOL|
|**Säkerhets risker på dina virtuella datorer bör åtgärdas**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggda + BYOL|
||||

|Policy|Omfång|
|----|:----|
|[**Sårbarhets bedömning ska vara aktiverat på virtuella datorer**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Inbyggda + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nya AKS säkerhets principer har lagts till i ASC_default initiativ – för användning av privata för hands kunder

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard, lägger Security Center till principer för Kubernetes-nivå och skärper rekommendationer, inklusive verk ställnings alternativ med Kubernetes-åtkomstkontroll.

Den tidiga fasen i det här projektet innehåller en privat för hands version och tillägg av nya (inaktiverade som standard) principer för ASC_default initiativ.

Du kan ignorera dessa principer utan att påverka din miljö. Om du vill aktivera dem kan du registrera dig för för hands versionen på https://aka.ms/SecurityPrP och välja bland följande alternativ:

1. **Enkel förhands granskning** – om du bara vill ansluta till denna privata för hands version Nämna uttryckligen "ASC-kontinuerlig skanning" som förhands granskningen som du vill delta i.
1. **Kontinuerligt program** – som ska läggas till i den här och framtida privata för hands versionerna. Du måste slutföra ett profil-och sekretess avtal.