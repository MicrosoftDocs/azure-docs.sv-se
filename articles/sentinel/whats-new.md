---
title: Nyheter i Azure Sentinel
description: I den här artikeln beskrivs nya funktioner i Azure Sentinel från de senaste månaderna.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 31ba96e0f8772877d7b4881c6bab0561cbe7956e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604261"
---
# <a name="whats-new-in-azure-sentinel"></a>Nyheter i Azure Sentinel

Den här artikeln innehåller de senaste funktionerna som har lagts till för Azure Sentinel och nya funktioner i relaterade tjänster som ger en bättre användar upplevelse i Azure Sentinel.

Information om tidigare funktioner som levereras finns i våra [Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Antecknade funktioner finns för närvarande i för hands version. I [tilläggs villkoren för Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ingår ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.


> [!TIP]
> Våra team för hot jakt i Microsoft Contribute-frågor, spel böcker, arbets böcker och antecknings böcker till [Azure Sentinel-communityn](https://github.com/Azure/Azure-Sentinel), inklusive vissa [frågor](https://github.com/Azure/Azure-Sentinel) som dina team kan anpassa och använda. 
>
> Du kan också delta! Delta i [Azure Sentinel Threat Hunters GitHub-communityn](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="march-2021"></a>Mars 2021

- [Automatiserings regler och incident utlösta spel böcker](#automation-rules-and-incident-triggered-playbooks) (inklusive all ny Spelbok-dokumentation)
- [Nya aviserings anrikninger: utökad enhets mappning och anpassad information](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Skriv ut dina Azure Sentinel-arbetsböcker eller Spara som PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Incident filter och sorterings inställningar sparas nu i din session (offentlig för hands version)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender incident integrering (offentlig för hands version)](#microsoft-365-defender-incident-integration-public-preview)
- [Nya Microsoft Service Connectors med Azure Policy](#new-microsoft-service-connectors-using-azure-policy)
 
### <a name="automation-rules-and-incident-triggered-playbooks"></a>Automation-regler och incident utlösta spel böcker

Automation-regler är ett nytt koncept i Azure Sentinel som gör att du kan hantera automatisering av incident hantering centralt. Förutom att du kan tilldela spel böcker till incidenter (inte bara aviseringar som tidigare), kan du med automatiserings regler också automatisera svar för flera analys regler samtidigt, tagga, tilldela eller stänga incidenter utan att behöva spel böcker och styra ordningen på de åtgärder som utförs. Automation-regler effektiviserar automatiserings användningen i Azure Sentinel och gör det möjligt att förenkla komplexa arbets flöden för dina incident Dirigerings processer.

Läs mer i den här [fullständiga förklaringen av Automation-regler](automate-incident-handling-with-automation-rules.md).

Som nämnts ovan kan spel böcker nu aktive ras med incident utlösaren, förutom aviserings utlösaren. Incident utlösaren ger din spel böcker en större uppsättning indata att arbeta med (eftersom incidenten även omfattar all avisering och enhets data), vilket ger dig ännu mer kraft och flexibilitet i dina svars arbets flöden. Incident-utlöst spel böcker aktive ras genom att anropas från Automation-regler.

Lär dig mer om [spel böcker "Enhanced funktioner](automate-responses-with-playbooks.md)och hur du kan skapa [ett svars arbets flöde](tutorial-respond-threats-playbook.md) med spel böcker tillsammans med automatiserings regler.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nya aviserings anrikninger: utökad enhets mappning och anpassad information

Förbättra dina aviseringar på två nya sätt för att göra dem mer användbara och mer informativa.

Börja med att ta enhets mappningen till nästa nivå. Nu kan du mappa nästan 20 typer av entiteter, från användare, värdar och IP-adresser till filer och processer till post lådor, Azure-resurser och IoT-enheter. Du kan också använda flera identifierare för varje entitet för att förstärka sin unika identifiering. Detta ger dig en mycket rikare data uppsättning i dina incidenter, vilket ger en bredare korrelation och mer kraftfullare undersökning. [Lär dig det nya sättet att mappa entiteter](map-data-fields-to-entities.md) i dina aviseringar.

[Läs mer om entiteter](entities-in-azure-sentinel.md) och se den [fullständiga listan över tillgängliga entiteter och deras identifierare](entities-reference.md).

Ge dina utrednings-och svars funktioner en ännu större ökning genom att anpassa aviseringar till Surface-information från dina obehandlade händelser. Få insyn i händelse innehåll i dina incidenter, vilket ger dig större kraft och flexibilitet när du svarar på och undersöker säkerhetshot. [Lär dig hur du kan anpassa informationen](surface-custom-details-in-alerts.md) i dina aviseringar.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Skriv ut dina Azure Sentinel-arbetsböcker eller Spara som PDF

Nu kan du skriva ut Azure Sentinel-arbetsböcker, vilket innebär att du även kan exportera dem till PDF-filer och spara lokalt eller dela dem.

I arbets boken väljer du menyn Alternativ > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Skriv ut innehåll**. Välj sedan din skrivare eller Välj **Spara som PDF** efter behov.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Skriv ut din arbets bok eller Spara som PDF.":::

Mer information finns i [Självstudier: visualisera och övervaka dina data](tutorial-monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Incident filter och sorterings inställningar sparas nu i din session (offentlig för hands version)

Nu sparas dina incident filter och sortering i hela Azure Sentinel-sessionen, även om du navigerar till andra delar av produkten.
Så länge som du fortfarande befinner dig i samma session, kan du navigera tillbaka till [incidenter](tutorial-investigate-cases.md) -avsnittet i Azure Sentinel och Visa dina filter och sortering precis som du lämnade det.

> [!NOTE]
> Incident filter och sortering sparas inte när du har lämnat Azure Sentinel eller uppdaterat din webbläsare.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender incident integrering (offentlig för hands version)

Med incident integrering med Azure Sentinels [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) kan du strömma alla M365D-incidenter till Azure Sentinel och hålla dem synkroniserade mellan båda portalerna. Incidenter från M365D (tidigare kallat Microsoft Threat Protection eller MTP) inkluderar alla associerade aviseringar, entiteter och relevant information, vilket ger dig tillräckligt med sammanhang för att utföra prioritering och förberedande undersökningar i Azure Sentinel. Incidenter i Sentinel förblir i dubbelriktat läge med M365D, så att du kan dra nytta av fördelarna med båda portalerna i incident undersökningen.

Med både Azure Sentinel och Microsoft 365 Defender får du det bästa av båda världar. Du får insikter om att en SIEM ger dig en överblick över hela din organisations omfattning av informations resurser och även djupet på anpassade och skräddarsydda utgångs kraft som en XDR levererar för att skydda dina Microsoft 365-resurser, båda dessa koordinerade och synkroniserade för sömlös SOC-drift.

Mer information finns i [Microsoft 365 Defender-integrering med Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nya Microsoft Service Connectors med Azure Policy

[Azure policy](../governance/policy/overview.md) är en Azure-tjänst som gör att du kan använda principer för att övervaka och kontrol lera egenskaperna för en resurs. Användningen av principer garanterar att resurserna är kompatibla med dina IT-styrnings standarder.

Bland egenskaperna för resurser som kan styras av principer skapas och hanteras diagnostik-och gransknings loggar. Azure Sentinel använder nu Azure Policy så att du kan använda en gemensam uppsättning inställningar för diagnostikloggar till alla (aktuella och framtida) resurser av en viss typ vars loggar du vill mata in i Azure Sentinel. Tack vare att du Azure Policy behöver du inte längre ange inställningar för diagnos loggar resurs per resurs.

Azure Policy-baserade kopplingar är nu tillgängliga för följande Azure-tjänster:
- [Azure Key Vault](connect-azure-key-vault.md) (offentlig för hands version)
- [Azure Kubernetes service](connect-azure-kubernetes-service.md) (offentlig för hands version)
- Azure SQL-databaser/-servrar (GA)

Kunderna kommer fortfarande att kunna skicka loggarna manuellt för vissa instanser och behöver inte använda princip motorn.

## <a name="february-2021"></a>Februari 2021

- [Arbets bok för cybersäkerhet mognads modell certifiering (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Data anslutningar från tredje part](#third-party-data-connectors)
- [UEBA insikter på sidan entitet (offentlig för hands version)](#ueba-insights-in-the-entity-page-public-preview)
- [Förbättrad incident sökning (offentlig för hands version)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Arbets bok för cybersäkerhet mognads modell certifiering (CMMC)

Azure Sentinel CMMC-arbetsboken innehåller en mekanism för att Visa logg frågor justerade mot CMMC-kontroller i Microsoft-portföljen, inklusive Microsofts säkerhets erbjudanden, Office 365, teams, Intune, Windows Virtual Desktop och många fler.

CMMC-arbetsboken gör det möjligt för säkerhets arkitekter, tekniker, säkerhets åtgärder analytiker, chefer och IT-proffs att få insyn i situationen för säkerhets position av moln arbets belastningar. Det finns även rekommendationer för att välja, utforma, distribuera och konfigurera Microsoft-erbjudanden för anpassning till respektive CMMC krav och praxis.

Även om du inte är tvungen att följa CMMC, är CMMC-arbetsboken användbar för att skapa säkerhets drifts Center, utveckla aviseringar, visualisera hot och ge en situations medvetenhet om arbets belastningar.

Öppna arbets boken CMMC i avsnittet Azure Sentinel- **arbetsböcker** . Välj **mall** och Sök sedan efter **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Aktivera och inaktivera CMMC-arbetsboken" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Mer information finns i:

- [Arbets bok för Azure Sentinel cybersäkerhet förfallo modell certifiering (CMMC)](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Självstudie: Visualisera och övervaka dina data](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Data anslutningar från tredje part

Vår samling tredjeparts integreringar fortsätter att växa, med trettio kopplingar som läggs till under de senaste två månaderna. Här är en lista:

- [Agari phishing-skydd och varumärkes skydd](connect-agari-phishing-defense.md)
- [Akamai säkerhets händelser](connect-akamai-security-events.md)
- [Alsid för Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP-Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Black Berry-CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower-eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise-kontrollant](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google-arbetsyta (tidigare G Suite)](connect-google-workspace.md)
- [Imperva WAF-Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS-loggar](connect-nxlog-dns.md)
- [NXLog Linux-granskning](connect-nxlog-linuxaudit.md)
- [Onapsis-plattform](connect-data-sources.md)
- [E-postsäkerhet för Proofpoint på begäran (POD)](connect-proofpoint-pod.md)
- [Qualys sårbarhets hantering, kunskaps bas](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall-brandvägg](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec-Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>UEBA insikter på sidan entitet (offentlig för hands version)

Informations sidorna för Azure Sentinel-entiteten innehåller ett [insikts fönster](identify-threats-with-entity-behavior-analytics.md#entity-insights)som visar insikter om entiteten och hjälper till att snabbt identifiera avvikelser och säkerhetshot.

Om du har [aktiverat UEBA](ueba-enrichments.md)och har valt en tidsram på minst fyra dagar, kommer nu fönstret insikter även att innehålla följande nya avsnitt för UEBA Insights:

|Avsnitt  |Beskrivning  |
|---------|---------|
|**UEBA Insights**     | Sammanfattar avvikande användar aktiviteter: <br>– Över geografiska platser, enheter och miljöer<br>– Över tid-och frekvens horisonter jämfört med användarens egna historik <br>– Jämfört med peer-beteende <br>– Jämfört med organisationens beteende     |
|**Användar-peer-datorer baserade på säkerhets grupp medlemskap**     |   Visar en lista över användarens peer-datorer baserat på medlemskap i Azure AD-säkerhetsgrupper, som tillhandahåller säkerhets arbets grupper med en lista över andra användare som delar liknande behörigheter.  |
|**Användar behörighet till Azure-prenumeration**     |     Visar användarens åtkomst behörigheter till de Azure-prenumerationer som är tillgängliga direkt, eller via Azure AD-grupper/tjänstens huvud namn.   |
|**Hot indikatorer relaterade till användaren**     |  Visar en samling kända hot som rör IP-adresser som representeras i användarens aktiviteter. Hot visas efter hot typen och familjen och berikas av Microsofts tjänst för hot information.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Förbättrad incident sökning (offentlig för hands version)

Vi har förbättrat Sök upplevelsen av Azure Sentinel-incidenter, så att du kan navigera snabbare genom incidenter när du undersöker ett speciellt hot.

När du söker efter incidenter i Azure Sentinel kan du nu söka efter följande incident information:

- ID
- Rubrik
- Produkt
- Ägare
- Tagg

## <a name="january-2021"></a>Januari 2021

- [Analys regel guiden: förbättrad redigerings upplevelse för frågor (offentlig för hands version)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [PowerShell-modulen AZ. SecurityInsights (offentlig för hands version)](#azsecurityinsights-powershell-module-public-preview)
- [SQL Database-anslutning](#sql-database-connector)
- [Dynamics 365-anslutning (offentlig för hands version)](#dynamics-365-connector-public-preview)
- [Förbättrade incident kommentarer](#improved-incident-comments)
- [Dedikerade Log Analytics kluster](#dedicated-log-analytics-clusters)
- [Hanterade identiteter för Logic Apps](#logic-apps-managed-identities)
- [Förbättrad regel justering med för hands versions diagram för analys regel](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Analys regel guiden: förbättrad redigerings upplevelse för frågor (offentlig för hands version)

Guiden regel för schemalagd analys i Azure Sentinel innehåller nu följande förbättringar för att skriva och redigera frågor:

-   Ett expanderbart redigerings fönster som ger dig mer skärm utrymme för att visa din fråga.
-   Ord markering för ord i din frågeparameter.
-   Utökad stöd för automatisk komplettering.
-   Valideringar av frågor i real tid. Fel i frågan visas nu som ett rött block i rullnings listen och som en röd prick på fliken **Ange regelns logiska** namn. Dessutom går det inte att spara en fråga med fel.

Mer information finns i [Självstudier: skapa anpassade analys regler för att identifiera hot](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>PowerShell-modulen AZ. SecurityInsights (offentlig för hands version)

Azure Sentinel stöder nu den nya [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell-modulen.

Modulen **AZ. SecurityInsights** har stöd för vanliga scenarier för Azure Sentinel-användning, som att interagera med incidenter för att ändra Statues, allvarlighets grad, ägare och så vidare, lägga till kommentarer och etiketter till incidenter och skapa bok märken.

Även om vi rekommenderar att du använder [Azure Resource Manager-mallar (arm)](../azure-resource-manager/templates/index.yml) för din CI/CD-pipeline, är modulen **AZ. SecurityInsights** användbar för åtgärder efter distributionen och är avsedd för SOC Automation.  Till exempel kan SOC Automation innehålla steg för att konfigurera data kopplingar, skapa analys regler eller lägga till automatiserings åtgärder i analys regler.

Mer information, inklusive en fullständig lista och beskrivning av tillgängliga cmdlets, parameter beskrivningar och exempel finns i PowerShell-dokumentationen för [AZ. SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>SQL Database-anslutning

Azure Sentinel tillhandahåller nu en Azure SQL Database-anslutning, som du kan använda för att strömma dina databasers gransknings-och diagnostikloggar till Azure Sentinel och kontinuerligt övervaka aktivitet i alla dina instanser.

Azure SQL är en fullständigt hanterad databas motor för PaaS (Platform-as-a-Service) som hanterar de flesta funktioner för databas hantering, till exempel uppgradering, uppdatering, säkerhets kopiering och övervakning, utan medverkan från användaren.

Mer information finns i [ansluta Azure SQL Database-diagnostik och gransknings loggar](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365-anslutning (offentlig för hands version)

Azure Sentinel tillhandahåller nu en koppling för Microsoft Dynamics 365 som gör att du kan samla in dina Dynamics 365-programs användar-, administratörs-och support aktivitets loggar i Azure Sentinel. Du kan använda dessa data för att granska den helheten för data bearbetnings åtgärder som äger rum och analysera dem för möjliga säkerhets överträdelser.

Mer information finns i [ansluta Dynamics 365 aktivitets loggar till Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Förbättrade incident kommentarer

Analytiker använder incident kommentarer för att samar beta om incidenter, dokumentera processer och steg manuellt eller som en del av en Spelbok. 

Med vår förbättrade händelse för incident Kommentering kan du formatera dina kommentarer och redigera eller ta bort befintliga kommentarer.

Mer information finns i [skapa incidenter automatiskt från Microsofts säkerhets aviseringar](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Dedikerade Log Analytics kluster

Azure Sentinel stöder nu dedikerade Log Analytics kluster som ett distributions alternativ. Vi rekommenderar att du överväger ett dedikerat kluster om du:

- Mata in **över 1 TB per dag** i Azure Sentinel-arbetsytan
- **Ha flera Azure Sentinel-arbetsytor** i din Azure-registrering

Med dedikerade kluster kan du använda funktioner som Kundhanterade nycklar, säker databas, dubbel kryptering och snabbare frågor över flera arbets ytor när du har flera arbets ytor i samma kluster.

Mer information finns i [Azure Monitor loggar dedicerade kluster](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Hanterade identiteter för Logic Apps

Azure Sentinel stöder nu hanterade identiteter för Azure Sentinel Logic Apps-anslutningen, så att du kan bevilja behörigheter direkt till en speciell Spelbok för att arbeta med Azure Sentinel i stället för att skapa extra identiteter.

- **Utan en hanterad identitet** kräver Logic Apps-anslutningen en separat identitet med en Azure Sentinel RBAC-roll för att kunna köras på Azure Sentinel. Den separata identiteten kan vara en Azure AD-användare eller ett huvud namn för tjänsten, till exempel ett registrerat Azure AD-program.

- Att **Aktivera stöd för hanterad identitet i din Logic app** registrerar Logic-appen med Azure AD och ger ett objekt-ID. Använd objekt-ID i Azure Sentinel för att tilldela Logic-appen en Azure RBAC-roll i din Azure Sentinel-arbetsyta. 

Mer information finns i:

- [Autentisering med hanterad identitet i Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Dokumentation om Azure Sentinel Logic Apps Connector](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Förbättrad regel justering med analys regel för hands versions diagram (offentlig för hands version)

Azure Sentinel hjälper dig nu att bättre finjustera analys reglerna, vilket hjälper dig att öka precisionen och minska bruset.

När du har redigerat en analys regel på fliken **Ange regel logik** hittar du avsnittet **resultat simulering** till höger. 

Välj **testa med aktuella data** om du vill att Azure Sentinel ska köra en simulering av de senaste 50 körningarna av din analys regel. Ett diagram skapas för att visa det genomsnittliga antalet aviseringar som regeln skulle ha genererat, baserat på rå data som utvärderats. 

Mer information finns i [definiera regel frågans logik och konfigurera inställningar](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>December 2020

- [80 nya inbyggda jakt frågor](#80-new-built-in-hunting-queries)
- [Förbättringar av Log Analytics-agenten](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nya inbyggda jakt frågor
 
Azure Sentinels inbyggda jakt frågor ger SOC-analytiker möjlighet att minska luckor i nuvarande detektions täckning och tända nya jakt ledare.

Den här uppdateringen för Azure Sentinel innehåller nya jakt frågor som ger täckning över MITRE to&CK Framework-matris:

- **Samling**
- **Kommando och kontroll**
- **Åtkomst till autentiseringsuppgifter**
- **Identifiering**
- **Projektering**
- **Exfiltrering**
- **Påverkan**
- **Initial åtkomst**
- **Ständig**
- **Eskalering av privilegier**

De extra jakt frågorna är utformade för att hjälpa dig att hitta misstänkt aktivitet i din miljö. Även om de kan returnera legitim aktivitet och potentiellt skadlig aktivitet kan de vara användbara för att ge din jakt. 

Om du när du har kört dessa frågor kan du känna dig trygg med resultaten, du kanske vill konvertera dem till analys regler eller lägga till jakt resultat till befintliga eller nya incidenter.

Alla tillagda frågor är tillgängliga via sidan Azure Sentinel jakt. Mer information finns i [jakt efter hot med Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Förbättringar av Log Analytics-agenten

Azure Sentinel-användare drar nytta av följande förbättringar i Log Analytics-agenten:

- **Stöd för fler operativ system**, inklusive CentOS 8, RedHat 8 och SUSE Linux 15.
- **Stöd för python 3** förutom python 2

Azure Sentinel använder Log Analytics agent för att skicka händelser till din arbets yta, inklusive Windows säkerhets händelser, Syslog-händelser, CEF-loggar med mera.

> [!NOTE]
> Den Log Analytics agenten kallas ibland OMS-agenten eller Microsoft Monitoring Agent (MMA). 
> 

Mer information finns i Log Analytics- [dokumentationen](../azure-monitor/agents/log-analytics-agent.md) och viktig information om [Log Analytics agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>November 2020

- [Övervaka din spel böcker-hälsa i Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender-koppling (offentlig för hands version)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Övervaka din spel böcker-hälsa i Azure Sentinel

Azure Sentinel-spel böcker baseras på arbets flöden som skapats i [Azure log-appar](../logic-apps/index.yml), en moln tjänst som hjälper dig att schemalägga, automatisera och dirigera uppgifter, affärs processer och arbets flöden. Spel böcker kan anropas automatiskt när en incident skapas, eller när sorterar och arbetar med incidenter. 

För att ge insikter om hälso tillståndet, prestandan och användningen av din spel böcker har vi lagt till en [arbets bok](../azure-monitor/visualize/workbooks-overview.md) med namnet **spel böcker Health Monitoring**. 

Använd arbets boken **spel böcker Health Monitoring** för att övervaka hälso tillståndet för din spel böcker eller leta efter avvikelser i mängden lyckade eller misslyckade körningar. 

Arbets boken **spel böcker Health Monitoring** är nu tillgänglig i galleriet för Azure Sentinel-mallar:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exempel arbets bok för spel böcker Health Monitoring":::

Mer information finns i:

- [Logic Apps dokumentation](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Dokumentation för Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-koppling (offentlig för hands version)
 
Med Microsoft 365 Defender-anslutaren för Azure Sentinel kan du strömma avancerade jakt loggar (en typ av rå händelse data) från Microsoft 365 Defender till Azure Sentinel. 

Med integreringen av [Microsoft Defender för slut punkt (MDATP)](/windows/security/threat-protection/) i säkerhets paraplyen för [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) kan du nu samla in dina Microsoft Defender för slut punkts avancerade jakt händelser med hjälp av Microsoft 365 Defender-anslutningen och strömma dem direkt i nya syfte-skapade tabeller i Azure Sentinel-arbetsytan. 

Azure Sentinel-tabellerna bygger på samma schema som används i Microsoft 365 Defender-portalen, och ger dig fullständig åtkomst till en fullständig uppsättning avancerade jakt loggar. 

Mer information finns i [ansluta data från Microsoft 365 Defender till Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender kallades tidigare Microsoft Threat Protection eller MTP. Microsoft Defender för slut punkten kallades tidigare Microsoft Defender Advanced Threat Protection eller MDATP.
> 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Fordonsbaserad Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Få insyn i aviseringar](quickstart-get-visibility.md)
