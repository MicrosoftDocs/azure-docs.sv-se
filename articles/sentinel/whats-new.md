---
title: Vad är nytt i Azure Sentinel
description: I den här artikeln beskrivs nya Azure Sentinel från de senaste månaderna.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7f9a8cb54458999d8f20a258bc36241dfdbd0de8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376043"
---
# <a name="whats-new-in-azure-sentinel"></a>Vad är nytt i Azure Sentinel

Den här artikeln innehåller de senaste funktionerna som Azure Sentinel och nya funktioner i relaterade tjänster som ger en förbättrad användarupplevelse i Azure Sentinel.

Information om tidigare funktioner som levereras finns i våra [Tech Community-bloggar.](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)

Antecknade funktioner finns för närvarande i FÖRHANDSVERSION. Tilläggsvillkor [för förhandsversionen av Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts i allmän tillgänglighet.


> [!TIP]
> Våra hotjaktsteam på Microsoft bidrar med frågor, spelböcker, arbetsböcker och anteckningsböcker [](https://github.com/Azure/Azure-Sentinel) till [Azure Sentinel Community,](https://github.com/Azure/Azure-Sentinel)inklusive specifika jaktfrågor som dina team kan anpassa och använda.
>
> Du kan också bidra! Gå med i [GitHub Azure Sentinel Threat GitHub-communityn](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="april-2021"></a>April 2021

- [Incidenttidslinje (offentlig förhandsversion)](#incident-timeline-public-preview)

### <a name="incident-timeline-public-preview"></a>Incidenttidslinje (offentlig förhandsversion)

Den första fliken på sidan med incidentinformation är nu **tidslinjen**, som visar en tidslinje med aviseringar och bokmärken i incidenten. Tidslinjen för en incident kan hjälpa dig att bättre förstå incidenten och rekonstruera tidslinjen för angriparens aktivitet i relaterade aviseringar och bokmärken.

- Välj ett objekt på tidslinjen för att se dess information, utan att lämna incidentkontexten
- Filtrera innehållet i tidslinjen för att endast visa aviseringar eller bokmärken, eller objekt med en viss allvarlighetsgrad eller MITRE-taktik.
- Du kan välja länken **Systemaviserings-ID** för att visa hela posten eller länken **Händelser** för att se relaterade händelser i **området Loggar.**

Exempel:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Fliken Incidenttidslinje":::

Mer information finns i [Självstudie: Undersöka incidenter med Azure Sentinel](tutorial-investigate-cases.md).

## <a name="march-2021"></a>Mars 2021

- [Ange att arbetsböcker ska uppdateras automatiskt i visningsläge](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nya identifieringar för Azure Firewall](#new-detections-for-azure-firewall)
- [Automatiseringsregler och incidentutlösta spelböcker](#automation-rules-and-incident-triggered-playbooks) (inklusive all ny spelboksdokumentation)
- [Nya aviseringsberik: förbättrad entitetsmappning och anpassad information](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Skriva ut Azure Sentinel arbetsböcker eller spara som PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Incidentfilter och sorteringsinställningar sparas nu i din session (offentlig förhandsversion)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 integrering av Defender-incidenter (offentlig förhandsversion)](#microsoft-365-defender-incident-integration-public-preview)
- [Nya Microsoft-tjänstanslutningar med Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Ange att arbetsböcker ska uppdateras automatiskt i visningsläge

Azure Sentinel kan nu använda den nya Azure Monitor [att](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) automatiskt uppdatera arbetsboksdata under en visningssession.

I varje arbetsbok eller arbetsboksmall väljer du :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Uppdatera automatiskt för** att visa dina intervallalternativ. Välj det alternativ som du vill använda för den aktuella visningssessionen och välj **Tillämpa**.

- Uppdateringsintervall som stöds sträcker sig **från 5 minuter** till **1 dag.**
- Automatisk uppdatering är inaktiverat som standard. För att optimera prestanda inaktiveras även automatisk uppdatering varje gång du stänger en arbetsbok och körs inte i bakgrunden. Aktivera automatisk uppdatering igen vid behov nästa gång du öppnar arbetsboken.
- Automatisk uppdatering pausas när du redigerar en arbetsbok och automatiska uppdateringsintervall startas om varje gång du växlar tillbaka till visningsläget från redigeringsläget.

    Intervallen startas också om om du uppdaterar arbetsboken manuellt genom att välja :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **knappen** Uppdatera.

Mer information finns i [Självstudie: Visualisera och övervaka dina data](tutorial-monitor-your-data.md) och Azure Monitor [dokumentationen](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Nya identifieringar för Azure Firewall

Flera out-of-the-box-Azure Firewall har lagts till i [analytics-området](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) i Azure Sentinel. Dessa nya identifieringar gör att säkerhetsteam kan få aviseringar om datorer i det interna nätverket försöker fråga eller ansluta till Internetdomännamn eller IP-adresser som är associerade med kända IPC:er, enligt definitionen i identifieringsregelfrågan.

De nya identifieringarna är:

- [Solorigate Network Beacon](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Kända GALLIUM-domäner och hash-värden](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Känd IRIDIUM IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Kända domän-/IP-adresser för gruppen Kända kända användargrupper](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [THALLIUM-domäner som ingår i DCU-borttagning](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Känd MALDOC-relaterad maldoc-hash](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Kända STRONTIUM-gruppdomäner](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [XOIUM – Domän- och IP-IPC: er – mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Identifieringar för Azure Firewall läggs kontinuerligt till i det inbyggda mallgalleriet. Om du vill hämta de senaste identifieringarna Azure Firewall **du** under Regelmallar filtrera **datakällorna** efter **Azure Firewall**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nya identifieringar i arbetsboken För analyseffektivitet":::

Mer information finns i [Nya identifieringar för Azure Firewall i Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Automatiseringsregler och incidentutlösta spelböcker

Automatiseringsregler är ett nytt begrepp i Azure Sentinel, så att du centralt kan hantera automatiseringen av incidenthantering. Förutom att du kan tilldela spelböcker till incidenter (inte bara aviseringar som tidigare), kan du med automatiseringsregler även automatisera svar för flera analysregler samtidigt, automatiskt tagga, tilldela eller stänga incidenter utan behov av spelböcker och styra ordningen på de åtgärder som körs. Automatiseringsregler effektiviserar användningen av automatisering i Azure Sentinel och gör att du kan förenkla komplexa arbetsflöden för dina incidentorkestreringsprocesser.

Läs mer med den [här fullständiga förklaringen av automatiseringsregler.](automate-incident-handling-with-automation-rules.md)

Som nämnts ovan kan spelböcker nu aktiveras med incidentutlösaren utöver aviseringsutlösaren. Incidentutlösaren ger dina spelböcker en större uppsättning indata att arbeta med (eftersom incidenten även innehåller alla aviserings- och entitetsdata), vilket ger dig ännu mer kraft och flexibilitet i dina svarsarbetsflöden. Incidentutlösta spelböcker aktiveras genom att anropas från automatiseringsregler.

Lär dig mer [om spelböckers förbättrade funktioner](automate-responses-with-playbooks.md)och hur du skapar ett arbetsflöde för svar [med](tutorial-respond-threats-playbook.md) hjälp av spelböcker tillsammans med automatiseringsregler.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nya aviseringsberik: förbättrad entitetsmappning och anpassad information

Utöka dina aviseringar på två nya sätt så att de blir mer användbara och informativa.

Börja med att ta entitetsmappningen till nästa nivå. Nu kan du mappa nästan 20 typer av entiteter, från användare, värdar och IP-adresser till filer och processer till postlådor, Azure-resurser och IoT-enheter. Du kan också använda flera identifierare för varje entitet för att förbättra deras unika identifiering. Detta ger dig en mycket mer omfattande datauppsättning i dina incidenter, vilket ger en bredare korrelation och mer kraftfull undersökning. [Lär dig det nya sättet att mappa entiteter](map-data-fields-to-entities.md) i dina aviseringar.

[Läs mer om entiteter](entities-in-azure-sentinel.md) och se [den fullständiga listan över tillgängliga entiteter och deras identifierare.](entities-reference.md)

Ge dina undersökande och svars-funktioner ännu bättre genom att anpassa dina aviseringar för att visa information från dina råhändelser. Få insyn i händelseinnehåll i dina incidenter, vilket ger dig ännu större kraft och flexibilitet när det gäller att svara på och undersöka säkerhetshot. [Lär dig hur du visar anpassad information](surface-custom-details-in-alerts.md) i dina aviseringar.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Skriva ut Azure Sentinel arbetsböcker eller spara som PDF

Nu kan du skriva Azure Sentinel arbetsböcker, vilket även gör att du kan exportera till dem till PDF-filer och spara lokalt eller dela.

I arbetsboken väljer du alternativmenyn för att > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Skriv ut innehåll**. Välj sedan din skrivare eller välj **Spara som PDF** efter behov.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Skriv ut din arbetsbok eller spara som PDF.":::

Mer information finns i [Självstudie: Visualisera och övervaka dina data.](tutorial-monitor-your-data.md)

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Incidentfilter och sorteringsinställningar sparas nu i sessionen (offentlig förhandsversion)

Nu sparas dina incidentfilter och -sortering under hela Azure Sentinel sessionen, även när du navigerar till andra områden av produkten.
Så länge du fortfarande är i samma session [](tutorial-investigate-cases.md) visar navigeringen tillbaka till området Incidenter i Azure Sentinel dina filter och sortering precis som du lämnade den.

> [!NOTE]
> Incidentfilter och sortering sparas inte när du har Azure Sentinel eller uppdatera webbläsaren.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 defender-incidentintegrering (offentlig förhandsversion)

Azure Sentinel med Microsoft 365 [Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) kan du strömma alla M365D-incidenter till Azure Sentinel och hålla dem synkroniserade mellan båda portalerna. Incidenter från M365D (kallades tidigare Microsoft Threat Protection eller MTP) innehåller alla associerade aviseringar, entiteter och relevant information, vilket ger dig tillräcklig kontext för att utföra trediga och preliminära undersökningar i Azure Sentinel. I Sentinel synkroniseras incidenter i båda riktningarna med M365D, så att du kan dra nytta av fördelarna med båda portalerna i incidentundersökningen.

Genom att Azure Sentinel och Microsoft 365 Defender tillsammans får du det bästa av båda världarna. Du får den omfattande insikten som en SIEM ger dig i hela organisationens omfång av informationsresurser, och även djupet av anpassad och skräddarsydd undersökande kraft som en XDR levererar för att skydda dina Microsoft 365-resurser, båda dessa koordinerade och synkroniserade för sömlös SOC-drift.

Mer information finns i Microsoft 365 [Defender-integrering med Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nya Microsoft-tjänstanslutningar med Azure Policy

[Azure Policy](../governance/policy/overview.md) är en Azure-tjänst som gör att du kan använda principer för att framtvinga och kontrollera egenskaperna för en resurs. Användning av principer säkerställer att resurser efterlever dina IT-styrningsstandarder.

Bland egenskaperna för resurser som kan styras av principer finns skapande och hantering av diagnostik- och granskningsloggar. Azure Sentinel nu Azure Policy så att du kan använda en gemensam uppsättning diagnostiklogginställningar för alla (aktuella och framtida) resurser av en viss typ vars loggar du vill mata in i Azure Sentinel. Tack vare Azure Policy behöver du inte längre ange resursen diagnostiklogginställningar per resurs.

Azure Policy-baserade anslutningsappar är nu tillgängliga för följande Azure-tjänster:
- [Azure Key Vault](connect-azure-key-vault.md) (offentlig förhandsversion)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (offentlig förhandsversion)
- Azure SQL databaser/servrar (GA)

Kunderna kommer fortfarande att kunna skicka loggarna manuellt för specifika instanser och behöver inte använda principmotorn.

## <a name="february-2021"></a>Februari 2021

- [Arbetsbok för CMMC (Cybersecurity Maturity Model Certification)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Dataanslutningar från tredje part](#third-party-data-connectors)
- [UEBA-insikter på entitetssidan (offentlig förhandsversion)](#ueba-insights-in-the-entity-page-public-preview)
- [Förbättrad incidentsökning (offentlig förhandsversion)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Arbetsbok för CMMC (Cybersecurity Maturity Model Certification)

I Azure Sentinel CMMC-arbetsboken finns en mekanism för att visa loggfrågor som är anpassade till CMMC-kontroller i Microsoft-portföljen, inklusive Microsofts säkerhetserbjudanden, Office 365, Teams, Intune, Windows Virtual Desktop och många fler.

Arbetsboken för CMMC gör det möjligt för säkerhetsarkitekter, ingenjörer, säkerhetsanalytiker, chefer och IT-proffs att få insyn i situationsmedvetenhet för säkerhetspositionen för molnarbetsbelastningar. Det finns även rekommendationer för att välja, utforma, distribuera och konfigurera Microsoft-erbjudanden för anpassning till respektive KRAV och praxis för cmmc.

Även om du inte behöver följa CMMC är CMMC-arbetsboken användbar för att skapa säkerhetsdriftscenter, utveckla aviseringar, visualisera hot och tillhandahålla situationsmedvetenhet för arbetsbelastningar.

Öppna CMMC-arbetsboken i Azure Sentinel **arbetsböcker.** Välj **Mall** och sök sedan efter **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Aktivera och inaktivera CMMC-arbetsboksguiden" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Mer information finns i:

- [Azure Sentinel arbetsbok för Cybersecurity Maturity Model Certification (CMMC)](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Självstudie: Visualisera och övervaka dina data](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Dataanslutningar från tredje part

Vår samling integreringar från tredje part fortsätter att växa, och 30 anslutningsappar har lagts till under de senaste två månaderna. Här är en lista:

- [Agari Phishing Defense och Varumärkesskydd](connect-agari-phishing-defense.md)
- [Akamai-säkerhetshändelser](connect-akamai-security-events.md)
- [Alsid för Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (tidigare G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS-loggar](connect-nxlog-dns.md)
- [NXLog Linux-granskning](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys– kunskapsbas för sårbarhetshantering](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall-brandväggen](connect-data-sources.md)
- [Sophos Cloud Molnbaseradx](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>UEBA-insikter på entitetssidan (offentlig förhandsversion)

Sidan Azure Sentinel entitetsinformation innehåller fönstret [Insikter,](identify-threats-with-entity-behavior-analytics.md#entity-insights)som visar beteendeinsikter för entiteten och hjälper dig att snabbt identifiera avvikelser och säkerhetshot.

Om du har [aktiverat UEBA](ueba-enrichments.md)och har valt en tidsram på minst fyra dagar innehåller fönstret Insikter nu även följande nya avsnitt för UEBA-insikter:

|Avsnitt  |Beskrivning  |
|---------|---------|
|**UEBA Insights**     | Sammanfattar avvikande användaraktiviteter: <br>– Över geografiska platser, enheter och miljöer<br>– Horisonter över tid och frekvens, jämfört med användarens egen historik <br>– Jämfört med peer-peers beteende <br>– Jämfört med organisationens beteende     |
|**Användar-peers baserat på medlemskap i säkerhetsgrupper**     |   Visar en lista över användarens peer-användare baserat på medlemskap i Azure AD-säkerhetsgrupper, vilket ger säkerhetsdriftsteam en lista över andra användare som delar liknande behörigheter.  |
|**Behörigheter för användaråtkomst till Azure-prenumeration**     |     Visar användarens åtkomstbehörigheter till Azure-prenumerationerna som är tillgängliga direkt eller via Azure AD-grupper/tjänsthuvudnamn.   |
|**Hotindikatorer relaterade till användaren**     |  Visar en samling kända hot som rör IP-adresser som representeras i användarens aktiviteter. Hot listas efter hottyp och familj och berikas av Microsofts hotinformationstjänst.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Förbättrad incidentsökning (offentlig förhandsversion)

Vi har förbättrat sökupplevelsen Azure Sentinel incidenter så att du kan navigera snabbare genom incidenter när du undersöker ett specifikt hot.

När du söker efter incidenter i Azure Sentinel kan du nu söka efter följande incidentinformation:

- ID
- Rubrik
- Produkt
- Ägare
- Tagg

## <a name="january-2021"></a>Januari 2021

- [Guide för analysregel: Förbättrad frågeredigeringsupplevelse (offentlig förhandsversion)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az.SecurityInsights PowerShell-modul (offentlig förhandsversion)](#azsecurityinsights-powershell-module-public-preview)
- [SQL Database-anslutningsapp](#sql-database-connector)
- [Dynamics 365-anslutningsapp (offentlig förhandsversion)](#dynamics-365-connector-public-preview)
- [Förbättrade incidentkommentarer](#improved-incident-comments)
- [Dedikerade Log Analytics-kluster](#dedicated-log-analytics-clusters)
- [Hanterade identiteter för logikappar](#logic-apps-managed-identities)
- [Förbättrad regeljustering med diagram för förhandsgranskning av analysregel](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Guide för analysregel: Förbättrad frågeredigeringsupplevelse (offentlig förhandsversion)

Guiden Azure Sentinel för schemalagd analys innehåller nu följande förbättringar för att skriva och redigera frågor:

-   Ett expanderbart redigeringsfönster som ger dig mer skärmutrymme för att visa din fråga.
-   Markering av nyckelord i frågekoden.
-   Utökat stöd för automatisk komplettering.
-   Frågeverifieringar i realtid. Fel i frågan visas nu som ett rött block i rullningslisten och som en röd punkt i namnet på fliken **Ange** regellogik. Dessutom går det inte att spara en fråga med fel.

Mer information finns i [Självstudie: Skapa anpassade analysregler för att identifiera hot.](tutorial-detect-threats-custom.md)
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell-modul (offentlig förhandsversion)

Azure Sentinel stöder nu den nya [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell-modulen.

**Modulen Az.SecurityInsights** har stöd för vanliga Azure Sentinel-användningsfall, som att interagera med incidenter för att ändra platser, allvarlighetsgrad, ägare och så vidare, lägga till kommentarer och etiketter till incidenter och skapa bokmärken.

Även om vi [rekommenderar att du använder arm Azure Resource Manager mallar (Azure Resource Manager)](../azure-resource-manager/templates/index.yml) för din CI/CD-pipeline, är modulen **Az.SecurityInsights** användbar för uppgifter efter distributionen och är avsedd för SOC-automatisering.  Din SOC-automatisering kan till exempel innehålla steg för att konfigurera dataanslutningsappar, skapa analysregler eller lägga till automatiseringsåtgärder i analysregler.

Mer information, inklusive en fullständig lista och beskrivning av tillgängliga cmdlets, parameterbeskrivningar och exempel finns i [Az.SecurityInsights PowerShell-dokumentationen.](/powershell/module/az.securityinsights/)

### <a name="sql-database-connector"></a>SQL Database-anslutningsapp

Azure Sentinel nu en Azure SQL-databasanslutning som du kan använda för att strömma databaserna gransknings- och diagnostikloggar till Azure Sentinel och kontinuerligt övervaka aktiviteten i alla dina instanser.

Azure SQL är en fullständigt hanterad PaaS-databasmotor (Platform-as-a-Service) som hanterar de flesta databashanteringsfunktioner, till exempel uppgradering, uppdatering, säkerhetskopiering och övervakning, utan inblandning av användaren.

Mer information finns i Connect Azure SQL database diagnostics and auditing logs (Ansluta [databasdiagnostik- och granskningsloggar).](connect-azure-sql-logs.md)

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365-anslutningsapp (offentlig förhandsversion)

Azure Sentinel nu en anslutningsapp för Microsoft Dynamics 365 som gör att du kan samla in dynamics 365-programmens användar-, administratörs- och supportaktivitetsloggar till Azure Sentinel. Du kan använda dessa data för att granska alla databearbetningsåtgärder som sker och analysera dem för möjliga säkerhetsöverträdelser.

Mer information finns i Ansluta [Dynamics 365-aktivitetsloggar till Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Förbättrade incidentkommentarer

Analytiker använder incidentkommentarer för att samarbeta kring incidenter, dokumentera processer och steg manuellt eller som en del av en spelbok. 

Med vår förbättrade upplevelse av incidentkommentarer kan du formatera dina kommentarer och redigera eller ta bort befintliga kommentarer.

Mer information finns i Skapa [incidenter automatiskt från Microsofts säkerhetsaviseringar.](create-incidents-from-alerts.md)
### <a name="dedicated-log-analytics-clusters"></a>Dedikerade Log Analytics-kluster

Azure Sentinel stöder nu dedikerade Log Analytics-kluster som ett distributionsalternativ. Vi rekommenderar att du överväger ett dedikerat kluster om du:

- **Mata in över 1 TB per dag i** din Azure Sentinel arbetsyta
- **Ha flera Azure Sentinel arbetsytor i** din Azure-registrering

Med dedikerade kluster kan du använda funktioner som kund hanterade nycklar, låsbox, dubbel kryptering och snabbare frågor mellan arbetsytor när du har flera arbetsytor i samma kluster.

Mer information finns i [Azure Monitor loggar dedikerade kluster](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Hanterade identiteter för logikappar

Azure Sentinel stöder nu hanterade identiteter för Azure Sentinel Logic Apps-anslutningsappen, vilket gör att du kan bevilja behörigheter direkt till en specifik spelbok för att arbeta med Azure Sentinel i stället för att skapa extra identiteter.

- **Utan en hanterad** identitet kräver Logic Apps-anslutningsappen en separat identitet med en Azure Sentinel RBAC-roll för att kunna köras Azure Sentinel. Den separata identiteten kan vara en Azure AD-användare eller ett huvudnamn för tjänsten, till exempel ett Azure AD-registrerat program.

- **När du slår på stöd för hanterade identiteter i** logikappen registreras logikappen med Azure AD och innehåller ett objekt-ID. Använd objekt-ID:t i Azure Sentinel för att tilldela logikappen en Azure RBAC-roll i din Azure Sentinel arbetsyta. 

Mer information finns i:

- [Autentisering med hanterad identitet i Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [dokumentation Azure Sentinel Logic Apps anslutningsappen](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Förbättrad regeljustering med diagram för förhandsversionen av analysregel (offentlig förhandsversion)

Azure Sentinel hjälper dig att finjustera dina analysregler bättre, vilket hjälper dig att öka noggrannheten och minska bruset.

När du har redigerat en analysregel **på fliken Ange regellogik** hittar du **området Resultatsimulering** till höger. 

Välj **Testa med aktuella data för** att Azure Sentinel köra en simulering av de senaste 50 körningarna av din analysregel. Ett diagram genereras för att visa det genomsnittliga antalet aviseringar som regeln skulle ha genererat, baserat på de rådata som utvärderats. 

Mer information finns i Definiera [regelfrågelogiken och konfigurera inställningar.](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)

## <a name="december-2020"></a>December 2020

- [80 nya inbyggda jaktfrågor](#80-new-built-in-hunting-queries)
- [Förbättringar av Log Analytics-agenten](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nya inbyggda jaktfrågor
 
Azure Sentinel inbyggda jaktfrågor ger SOC-analytikerna möjlighet att minska luckorna i den aktuella identifieringstäckningen och starta nya jakt leads.

Den här uppdateringen Azure Sentinel innehåller nya jaktfrågor som ger täckning i matrisen för MITRE ATT&CK-ramverket:

- **Samling**
- **Kommando och kontroll**
- **Åtkomst till autentiseringsuppgifter**
- **Identifiering**
- **Körnings-**
- **Exfiltrering**
- **Påverkan**
- **Första åtkomst**
- **Uthållighet**
- **Privilegieeskalering**

De tillagda jaktfrågorna är utformade för att hjälpa dig att hitta misstänkt aktivitet i din miljö. Även om de kan returnera legitim aktivitet och potentiellt skadlig aktivitet kan de vara användbara när du ska guida din jakt. 

Om du är säker på resultatet när du har kört dessa frågor kan du konvertera dem till analysregler eller lägga till jaktresultat till befintliga eller nya incidenter.

Alla tillagda frågor är tillgängliga via sidan Azure Sentinel Jakt. Mer information finns i [Hunt for threats with Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Förbättringar av Log Analytics-agenten

Azure Sentinel dra nytta av följande förbättringar av Log Analytics-agenten:

- **Stöd för fler operativsystem,** inklusive CentOS 8, RedHat 8 och SUSE Linux 15.
- **Stöd för Python 3** utöver Python 2

Azure Sentinel använder Log Analytics-agenten för att skicka händelser till din arbetsyta, inklusive Windows-säkerhet, Syslog-händelser, CEF-loggar med mera.

> [!NOTE]
> Log Analytics-agenten kallas ibland OMS-agenten eller Microsoft Monitoring Agent (MMA). 
> 

Mer information finns i Log [Analytics-dokumentationen och](../azure-monitor/agents/log-analytics-agent.md) viktig information om [Log Analytics-agenten.](https://github.com/microsoft/OMS-Agent-for-Linux/releases)
## <a name="november-2020"></a>November 2020

- [Övervaka dina spelböckers hälsa i Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender-anslutning (offentlig förhandsversion)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Övervaka dina spelböckers hälsa i Azure Sentinel

Azure Sentinel är baserade på arbetsflöden som skapats i [Azure Log Apps](../logic-apps/index.yml), en molntjänst som hjälper dig att schemalägga, automatisera och orkestrera uppgifter, affärsprocesser och arbetsflöden. Spelböcker kan anropas automatiskt när en incident skapas eller när du trederar och arbetar med incidenter. 

För att ge insikter om hälsa, prestanda och användning av dina spelböcker har vi lagt till en arbetsbok med [namnet](../azure-monitor/visualize/workbooks-overview.md) **Hälsoövervakning av spelböcker.** 

Använd **arbetsboken för** hälsoövervakning av spelböcker för att övervaka hälsotillståndet för dina spelböcker eller leta efter avvikelser i antalet misslyckade eller misslyckade körningar. 

Arbetsboken **för hälsoövervakning av spelböcker** är nu tillgänglig i galleriet Azure Sentinel Mallar:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Arbetsbok för exempelspelböcker om hälsoövervakning":::

Mer information finns i:

- [Logic Apps dokumentation](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Dokumentation för Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-anslutningsapp (offentlig förhandsversion)
 
Med Microsoft 365 Defender-anslutningsappen för Azure Sentinel kan du strömma avancerade jaktloggar (en typ av rådata) från Microsoft 365 Defender till Azure Sentinel. 

Med integreringen av [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) i säkerhetsparaplyet [för Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) kan du nu samla in dina avancerade jakthändelser för Microsoft Defender för slutpunkter med hjälp av Microsoft 365 Defender-anslutningsappen och strömma dem direkt till nya specialbyggda tabeller på din Azure Sentinel-arbetsyta. 

Tabellerna Azure Sentinel bygger på samma schema som används i Microsoft 365 Defender-portalen och ger dig fullständig åtkomst till den fullständiga uppsättningen avancerade jaktloggar. 

Mer information finns i Ansluta [data från Microsoft 365 Defender till Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender kallades tidigare för Microsoft Threat Protection eller MTP. Microsoft Defender för slutpunkt kallades tidigare för Microsoft Defender Avancerat skydd eller MDATP.
> 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[On-board Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Få insyn i aviseringar](quickstart-get-visibility.md)
