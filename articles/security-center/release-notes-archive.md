---
title: Arkiv över nyheter i Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center från sex månader sedan och tidigare.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/04/2021
ms.author: memildin
ms.openlocfilehash: 9d376a374d1934f55b6a6fb15f1642c81b30b2fc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718673"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Arkiv för nyheter i Azure Security Center?

Den primära [sidan Vad är nytt i Azure Security Center?](release-notes.md) innehåller uppdateringar för de senaste sex månaderna, medan den här sidan innehåller äldre objekt.

Den här sidan innehåller information om:

- Nya funktioner
- Felkorrigeringar
- Föråldrade funktioner


## <a name="october-2020"></a>Oktober 2020

Uppdateringar i oktober är:
- [Sårbarhetsbedömning för lokala datorer och datorer med flera moln (förhandsversion)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall rekommendation har lagts till (förhandsversion)](#azure-firewall-recommendation-added-preview)
- [Auktoriserade IP-intervall bör definieras på Kubernetes Services-rekommendationen som uppdateras med snabbkorrigering](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Instrumentpanelen för regelefterlevnad innehåller nu alternativ för att ta bort standarder](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabellen Microsoft.Security/securityStatuses har tagits bort från Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Sårbarhetsbedömning för lokala datorer och datorer med flera moln (förhandsversion)

[Azure Defender för servrarnas](defender-for-servers-introduction.md)integrerade skanner för sårbarhetsbedömning (drivs av Qualys) söker nu igenom Azure Arc aktiverade servrar.

När du har aktiverat Azure Arc på dina icke-Azure-datorer kommer Security Center att erbjuda att distribuera den integrerade sårbarhetsskannern på dem – manuellt och i stor skala.

Med den här uppdateringen kan du frigöra kraften hos **Azure Defender** för servrar för att konsolidera ditt program för sårbarhetshantering för alla dina Azure- och icke-Azure-tillgångar.

Huvudfunktioner:

- Övervaka va-skannerns etableringstillstånd (sårbarhetsbedömning) på Azure Arc datorer
- Etablering av den integrerade VA-agenten till oskyddade Windows- och Linux Azure Arc datorer (manuellt och i stor skala)
- Ta emot och analysera identifierade säkerhetsrisker från distribuerade agenter (manuellt och i stor skala)
- Enhetlig upplevelse för virtuella Azure-datorer Azure Arc datorer

[Läs mer om hur du distribuerar den integrerade sårbarhetsskannern till dina hybriddatorer.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Läs mer om Azure Arc-aktiverade servrar](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall rekommendation har lagts till (förhandsversion)

En ny rekommendation har lagts till för att skydda alla dina virtuella nätverk med Azure Firewall.

Rekommendationen Virtuella **nätverk bör skyddas** av Azure Firewall dig att begränsa åtkomsten till dina virtuella nätverk och förhindra potentiella hot med hjälp av Azure Firewall.

Läs mer om [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Auktoriserade IP-intervall bör definieras på Kubernetes Services-rekommendationen uppdaterad med snabbkorrigering

Rekommendationen **Auktoriserade IP-intervall bör definieras på Kubernetes Services har** nu ett snabbkorrigeringsalternativ.

Mer information om den här rekommendationen och alla andra Security Center finns i [Säkerhetsrekommendationer – en referensguide.](recommendations-reference.md)

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Auktoriserade IP-intervall ska definieras på Kubernetes Services-rekommendationen med snabbkorrigeringsalternativet":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Instrumentpanelen för regelefterlevnad innehåller nu alternativ för att ta bort standarder

Security Center instrumentpanel för regelefterlevnad ger insikter om din efterlevnadsstatus baserat på hur du uppfyller specifika efterlevnadskontroller och krav.

Instrumentpanelen innehåller en standarduppsättning reglerande standarder. Om någon av de angivna standarderna inte är relevant för din organisation är det nu en enkel process att ta bort dem från användargränssnittet för en prenumeration. Standarder kan endast tas bort på *prenumerationsnivå.* inte hanteringsgruppens omfång.

Läs mer i [Ta bort en standard från instrumentpanelen](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabellen Microsoft.Security/securityStatuses har tagits bort från Azure Resource Graph (ARG)

Azure Resource Graph är en tjänst i Azure som är utformad för att tillhandahålla effektiv resursutforskning med möjlighet att köra frågor i stor skala över en viss uppsättning prenumerationer så att du effektivt kan styra din miljö. 

Du Azure Security Center arg och [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) för att fråga en mängd olika säkerhetsstatusdata. Exempel:

- Tillgångsinventering använder (ARG)
- Vi har dokumenterat ett exempel på en [ARG-fråga för hur du identifierar konton utan aktiverad multifaktorautentisering (MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

I ARG finns det datatabeller som du kan använda i dina frågor.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer och tillgängliga tabeller":::

> [!TIP]
> ARG-dokumentationen visar en lista över alla tillgängliga [tabeller Azure Resource Graph tabell- och resurstypsreferensen](../governance/resource-graph/reference/supported-tables-resources.md).

Från den här uppdateringen har **tabellen Microsoft.Security/securityStatuses** tagits bort. SECURITYStatuses-API:et är fortfarande tillgängligt.

Dataersättning kan användas av tabellen Microsoft.Security/Assessments.

Den största skillnaden mellan Microsoft.Security/securityStatuses och Microsoft.Security/Assessments är att sekunderna innehåller en enda post för var och en medan den första visar sammansättning av utvärderingar.

Till exempel skulle Microsoft.Security/securityStatuses returnera ett resultat med en matris med två policyAssessments:

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
Microsoft.Security/Assessments kommer att innehålla en post för varje sådan principutvärdering enligt följande:

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

**Exempel på konvertering av en befintlig ARG-fråga med hjälp av securityStatuses för att nu använda utvärderingstabellen:**

Fråga som refererar till SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Ersättningsfråga för tabellen Utvärderingar:

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

Uppdateringar i september är:
- [Security Center får ett nytt utseende!](#security-center-gets-a-new-look)
- [Azure Defender har släppts](#azure-defender-released)
- [Azure Defender för Key Vault är allmänt tillgänglig](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender lagringsskydd för filer och ADLS Gen2 är allmänt tillgängligt](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Tillgångsinventeringsverktyg är nu allmänt tillgängliga](#asset-inventory-tools-are-now-generally-available)
- [Inaktivera en specifik sårbarhetssökning för genomsökningar av containerregister och virtuella datorer](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Undanta en resurs från en rekommendation](#exempt-a-resource-from-a-recommendation)
- [AWS- och GCP-anslutningsappar i Security Center ger en upplevelse för flera moln](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Rekommendationspaket för Kubernetes-arbetsbelastningsskydd](#kubernetes-workload-protection-recommendation-bundle)
- [Resultat från sårbarhetsbedömningar är nu tillgängliga i löpande export](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Förhindra felaktiga säkerhetskonfigurationer genom att framtvinga rekommendationer när du skapar nya resurser](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Rekommendationer för nätverkssäkerhetsgrupp har förbättrats](#network-security-group-recommendations-improved)
- [Föråldrad förhandsversion av AKS-rekommendationen "Pod Security Policies should be defined on Kubernetes Services" (Säkerhetsprinciper för poddar bör definieras i Kubernetes Services)](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-postmeddelanden från Azure Security Center har förbättrats](#email-notifications-from-azure-security-center-improved)
- [Säkerhetspoäng innehåller inte rekommendationer för förhandsversioner](#secure-score-doesnt-include-preview-recommendations)
- [Rekommendationerna innehåller nu en allvarlighetsgradsindikator och intervall för freshness](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center får ett nytt utseende!

Vi har släppt ett uppdaterat användargränssnitt Security Center på portalsidorna. De nya sidorna innehåller en ny översiktssida och instrumentpaneler för säkerhetspoäng, tillgångsinventering och Azure Defender.

Den omdesignade översiktssidan har nu en panel för åtkomst till säkerhetspoäng, tillgångsinventering och Azure Defender instrumentpaneler. Den har också en panel som länkar till instrumentpanelen för regelefterlevnad.

Läs mer om [översiktssidan](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender har släppts

**Azure Defender** är molnplattformen för arbetsbelastningsskydd (CWPP) integrerad i Security Center för avancerade, intelligenta, skydd av dina Azure- och hybridarbetsbelastningar. Den ersätter Security Center standardalternativ för prisnivå. 

När du Azure Defender från  området Priser och inställningar i Azure Security Center aktiveras följande Defender-planer samtidigt och ger omfattande skydd för beräknings-, data- och tjänstlager i din miljö:

- [Azure Defender för servrar](defender-for-servers-introduction.md)
- [Azure Defender för App Service](defender-for-app-service-introduction.md)
- [Azure Defender för Storage](defender-for-storage-introduction.md)
- [Azure Defender för SQL](defender-for-sql-introduction.md)
- [Azure Defender för Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender för containerregister](defender-for-container-registries-introduction.md)

Var och en av dessa planer förklaras separat i Security Center dokumentationen.

Med sin dedikerade instrumentpanel Azure Defender säkerhetsaviseringar och avancerat skydd mot hot för virtuella datorer, SQL-databaser, containrar, webbprogram, ditt nätverk med mera.

[Läs mer om Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender för Key Vault är allmänt tillgänglig

Azure Key Vault är en molntjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. 

**Azure Defender för Key Vault** azure-inbyggt, avancerat skydd mot hot för Azure Key Vault, vilket ger ytterligare ett lager med säkerhetsintelligens. Dessutom är Azure Defender för Key Vault därför att skydda många av de resurser som är beroende av dina Key Vault konton.

Den valfria planen är nu GA. Den här funktionen var i förhandsversion som "advanced threat protection for Azure Key Vault".

Dessutom innehåller de Key Vault sidorna i Azure Portal nu en dedikerad **säkerhetssida** för **att Security Center** rekommendationer och aviseringar.

Läs mer i [Azure Defender för Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender lagringsskydd för filer och ADLS Gen2 är allmänt tillgängligt 

**Azure Defender för Storage** identifierar potentiellt skadlig aktivitet på dina Azure Storage konton. Dina data kan skyddas oavsett om de lagras som blobcontainrar, filresurser eller datasjöar.

Stöd för [Azure Files](../storage/files/storage-files-introduction.md) och [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) är nu allmänt tillgängligt.

Från och med den 1 oktober 2020 börjar vi debitera för att skydda resurser på dessa tjänster.

Läs mer i [Azure Defender för Storage](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Tillgångslagerverktyg är nu allmänt tillgängliga

Sidan för tillgångsinventering i Azure Security Center innehåller en enda sida för att visa säkerhetsstatusen för de resurser som du har anslutit till Security Center.

Security Center regelbundet säkerhetstillståndet för dina Azure-resurser för att identifiera potentiella säkerhetsproblem. Den ger dig sedan rekommendationer om hur du åtgärdar dessa sårbarheter.

När en resurs har utestående rekommendationer visas de i inventeringen.

Läs mer i [Utforska och hantera dina resurser med tillgångsinventering.](asset-inventory.md)



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Inaktivera en specifik sårbarhetssökning för genomsökningar av containerregister och virtuella datorer

Azure Defender säkerhetsskannrar för att söka igenom avbildningar i Azure Container Registry och virtuella datorer.

Om du har en organisation som behöver ignorera en finding i stället för att åtgärda den kan du inaktivera den om du vill. Inaktiverade resultat påverkar inte säkerhetspoängen eller genererar oönskade brus.

När en finding matchar de kriterier som du har definierat i dina inaktiveringsregler visas den inte i listan över resultat.

Det här alternativet finns på sidorna med information om rekommendationer för:

- **Sårbarheter i Azure Container Registry avbildningar bör åtgärdas**
- **Sårbarheter i dina virtuella datorer bör åtgärdas**

Läs mer i [Inaktivera specifika resultat för dina containeravbildningar](defender-for-container-registries-usage.md#disable-specific-findings-preview) och Inaktivera specifika resultat för dina virtuella [datorer.](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)


### <a name="exempt-a-resource-from-a-recommendation"></a>Undanta en resurs från en rekommendation

Ibland visas en resurs som skadad för en specifik rekommendation (och därmed sänka säkerhetspoängen) även om du tycker att den inte borde vara det. Den kan ha åtgärdats av en process som inte spårats av Security Center. Eller så kanske din organisation har valt att acceptera risken för den specifika resursen. 

I sådana fall kan du skapa en undantagsregel och se till att resursen inte listas bland de resurser som inte är felsamma i framtiden. Dessa regler kan innehålla dokumenterade motiveringar enligt beskrivningen nedan.

Läs mer i [Undanta en resurs från rekommendationer och säkerhetspoäng.](exempt-resource.md)


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS- och GCP-anslutningsappar i Security Center ger en upplevelse för flera moln

När molnarbetsbelastningar ofta omfattar flera molnplattformar måste molnsäkerhetstjänsterna göra samma sak.

Azure Security Center skyddar nu arbetsbelastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

När du integrerar dina AWS- och GCP-konton i Security Center integrerar AWS Security Hub, GCP Security Command och Azure Security Center. 

Läs mer i [Connect your AWS accounts to Azure Security Center](quickstart-onboard-aws.md) Connect your [GCP accounts to Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Rekommendationspaket för Kubernetes-arbetsbelastningsskydd

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard lägger Security Center till rekommendationer för kubernetes-nivåhärdning, inklusive framtvingandealternativ med Kubernetes-antagningskontroll.

När du har installerat Azure Policy-tillägget för Kubernetes i ditt AKS-kluster övervakas varje begäran till Kubernetes API-servern mot den fördefinierade uppsättningen bästa praxis innan den bevaras i klustret. Du kan sedan konfigurera för att framtvinga bästa praxis och be om dem för framtida arbetsbelastningar.

Du kan till exempel uppmana privilegierade containrar att inte skapas, och eventuella framtida begäranden om att göra det blockeras.

Läs mer i [Metodtips för arbetsbelastningsskydd med Kubernetes-antagningskontroll.](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Resultat från sårbarhetsbedömningar är nu tillgängliga i löpande export

Använd kontinuerlig export för att strömma aviseringar och rekommendationer i realtid till Azure Event Hubs, Log Analytics-arbetsytor eller Azure Monitor. Därifrån kan du integrera dessa data med SIEM:er (till exempel Azure Sentinel, Power BI, Azure Data Explorer med mera.

Security Center integrerade verktyg för sårbarhetsbedömning returnerar resultat om dina resurser som användbara rekommendationer inom en överordnad rekommendation, till exempel "Sårbarheter i dina virtuella datorer bör åtgärdas". 

Säkerhetsresultaten är nu tillgängliga för export via löpande export när du väljer rekommendationer och aktiverar alternativet **inkludera säkerhetsresultat.**

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Inkludera säkerhetsresultat i konfiguration av löpande export" :::

Relaterade sidor:

- [Security Center integrerad lösning för sårbarhetsbedömning för virtuella Azure-datorer](deploy-vulnerability-assessment-vm.md)
- [Security Center är en integrerad lösning för sårbarhetsbedömning för Azure Container Registry bilder](defender-for-container-registries-usage.md)
- [Löpande export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Förhindra felaktiga säkerhetskonfigurationer genom att framtvinga rekommendationer när du skapar nya resurser

Felaktiga säkerhetskonfigurationer är en viktig orsak till säkerhetsincidenter. Security Center har nu möjlighet att hjälpa *till att förhindra* felaktiga konfigurationer av nya resurser med avseende på specifika rekommendationer. 

Den här funktionen kan hjälpa till att skydda dina arbetsbelastningar och stabilisera dina säkerhetspoäng.

Framtvingande av en säker konfiguration, baserat på en specifik rekommendation, erbjuds i två lägen:

- Med den **nekande** effekten av Azure Policy kan du förhindra att resurser med feltillstånd skapas

- Med  alternativet Framtvinga kan du dra nytta av Azure-principens **DeployIfNotExist-effekt** och automatiskt reparera icke-kompatibla resurser när de skapas
 
Detta är tillgängligt för valda säkerhetsrekommendationer och finns längst upp på sidan med resursinformation.

Läs mer i [Prevent misconfigurations with Enforce/Deny recommendations (Förhindra felaktiga konfigurationer med Enforce/Deny-rekommendationer).](prevent-misconfigurations.md)

###  <a name="network-security-group-recommendations-improved"></a>Rekommendationer för nätverkssäkerhetsgrupp har förbättrats

Följande säkerhetsrekommendationer som rör nätverkssäkerhetsgrupper har förbättrats för att minska vissa förekomster av falska positiva resultat.

- Alla nätverksportar ska vara begränsade på NSG:er som är kopplade till den virtuella datorn
- Hanteringsportar bör stängas på dina virtuella datorer
- Internetuppspelande virtuella datorer bör skyddas med nätverkssäkerhetsgrupper
- Undernät ska associeras med en nätverkssäkerhetsgrupp


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Föråldrad förhandsversion av AKS-rekommendationen "Pod Security Policies should be defined on Kubernetes Services" (Säkerhetsprinciper för poddar bör definieras i Kubernetes Services)

Förhandsversionsrekommendationen "Pod Security Policies should be defined on Kubernetes Services" (Pod security policies should be defined on Kubernetes Services) är inaktuell enligt [beskrivningen](../aks/use-pod-security-policies.md) i Azure Kubernetes Service dokumentationen.

Funktionen poddsäkerhetsprincip (förhandsversion) är inställd på utfasning och kommer inte längre att vara tillgänglig efter den 15 oktober 2020 till förmån för Azure Policy för AKS.

När poddsäkerhetsprincipen (förhandsversion) är inaktuell måste du inaktivera funktionen på befintliga kluster med den inaktuella funktionen för att utföra framtida klusteruppgraderingar och hålla dig inom Azure-supporten.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-postmeddelanden från Azure Security Center har förbättrats

Följande e-postmeddelanden om säkerhetsaviseringar har förbättrats: 

- Möjligheten att skicka e-postaviseringar om aviseringar för alla allvarlighetsnivåerna har lagts till
- Möjligheten att meddela användare med olika Azure-roller i prenumerationen har lagts till
- Vi meddelar prenumerationsägare som standard aviseringar med hög allvarlighetsgrad (som har hög sannolikhet att vara äkta överträdelser)
- Vi har tagit bort fältet telefonnummer från konfigurationssidan för e-postaviseringar

Läs mer i Konfigurera [e-postaviseringar för säkerhetsaviseringar.](security-center-provide-security-contact-details.md)


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Säkerhetspoäng innehåller inte förhandsgranskningsrekommendationer 

Security Center utvärderar kontinuerligt dina resurser, prenumerationer och organisationen för säkerhetsproblem. Sedan aggregerar den alla resultat i en enda poäng så att du snabbt kan se din aktuella säkerhets situation: ju högre poäng, desto lägre risknivå.

När nya hot upptäcks görs nya säkerhetsråd tillgängliga i Security Center nya rekommendationer. För att undvika oväntade ändringar av säkerhetspoängen och för att tillhandahålla en respitperiod där  du kan utforska nya rekommendationer innan de påverkar dina poäng tas rekommendationer som flaggats som förhandsversion inte längre med i beräkningarna av dina säkerhetspoäng. De bör fortfarande åtgärdas när det är möjligt, så att de bidrar till dina poäng när förhandsversionsperioden är slut.

Dessutom **återger inte** förhandsversionsrekommendationer en resurs "Inte felig".

Ett exempel på en förhandsversionsrekommendation:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Rekommendation med förhandsversionsflaggan":::

[Läs mer om säkerhetspoäng.](secure-score-security-controls.md)


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Rekommendationerna innehåller nu en allvarlighetsgradsindikator och intervall för freshness

Informationssidan för rekommendationer innehåller nu en intervallindikator för freshness (när det är relevant) och en tydlig visning av rekommendationens allvarlighetsgrad.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Rekommendationssidan visar hur nya och allvarlighetsgrad är":::


## <a name="august-2020"></a>Augusti 2020

Uppdateringar i augusti omfattar:

- [Tillgångsinventering – kraftfull ny vy över dina tillgångars säkerhetsstatus](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Stöd har lagts Azure Active Directory standardinställningar för säkerhet (för multifaktorautentisering)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Rekommendation för tjänstens huvudnamn har lagts till](#service-principals-recommendation-added)
- [Sårbarhetsbedömning på virtuella datorer – rekommendationer och principer konsoliderade](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nya AKS-säkerhetsprinciper har lagts till ASC_default initiativ – endast för privat förhandsversion](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Tillgångsinventering – kraftfull ny vy över dina tillgångars säkerhetsstatus

Security Center tillgångsinventering (för närvarande i förhandsversion) är ett sätt att visa säkerhetsstatusen för de resurser som du har anslutit till Security Center.

Security Center regelbundet säkerhetstillståndet för dina Azure-resurser för att identifiera potentiella säkerhetsproblem. Den ger dig sedan rekommendationer om hur du åtgärdar dessa sårbarheter. När en resurs har utestående rekommendationer visas de i inventeringen.

Du kan använda vyn och dess filter för att utforska dina säkerhetsstatusdata och vidta ytterligare åtgärder baserat på dina resultat.

Läs mer om [tillgångsinventering.](asset-inventory.md)


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Stöd har lagts Azure Active Directory standardinställningar för säkerhet (för multifaktorautentisering)

Security Center har lagt till fullständigt stöd [för säkerhetsstandarder](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)– Microsofts kostnadsfria identitetsskydd.

Standardinställningarna för säkerhet tillhandahåller förkonfigurerade säkerhetsinställningar för identiteter som skyddar din organisation mot vanliga identitetsrelaterade attacker. Standardinställningarna för säkerhet skyddar redan över 5 miljoner klienter totalt. 50 000 klienter skyddas också av Security Center.

Security Center nu en säkerhetsrekommendation när den identifierar en Azure-prenumeration utan aktiverade säkerhetsstandarder. Fram till nu Security Center vi att aktivera multifaktorautentisering med villkorlig åtkomst, som är en del av Azure Active Directory (AD) Premium-licensen. För kunder som använder Azure AD kostnadsfritt rekommenderar vi nu att du aktiverar standardinställningar för säkerhet. 

Vårt mål är att uppmuntra fler kunder att skydda sina molnmiljöer med MFA och minimera en av de högsta riskerna som också är den som påverkar [dina säkerhetspoäng mest.](secure-score-security-controls.md)

Läs mer om [standardinställningar för säkerhet.](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)


### <a name="service-principals-recommendation-added"></a>Rekommendation för tjänstens huvudnamn har lagts till

En ny rekommendation har lagts till för att rekommendera att Security Center kunder som använder hanteringscertifikat för att hantera sina prenumerationer växlar till tjänstens huvudnamn.

Rekommendationen **Tjänstens huvudnamn** bör användas för att skydda dina prenumerationer i stället för hanteringscertifikat rekommenderar att du använder tjänstens huvudnamn eller Azure Resource Manager för att hantera dina prenumerationer på ett säkrare sätt. 

Läs mer om [program- och tjänsthuvudnamnsobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Sårbarhetsbedömning på virtuella datorer – rekommendationer och principer konsoliderade

Security Center kontrollerar dina virtuella datorer för att identifiera om de kör en lösning för sårbarhetsbedömning. Om ingen lösning för sårbarhetsbedömning hittas Security Center en rekommendation för att förenkla distributionen.

När sårbarheter hittas ger Security Center en rekommendation som sammanfattar resultaten så att du kan undersöka och åtgärda efter behov.

För att säkerställa en konsekvent upplevelse för alla användare, oavsett vilken skannertyp de använder, har vi samlat fyra rekommendationer i följande två:

|Enhetlig rekommendation|Ändra beskrivning|
|----|:----|
|**En lösning för sårbarhetsbedömning ska vara aktiverad på dina virtuella datorer**|Ersätter följande två rekommendationer:<br> Aktivera den inbyggda lösningen för sårbarhetsbedömning på virtuella datorer (drivs av Qualys (nu inaktuell) (ingår i standardnivån)<br> Lösningen för sårbarhetsbedömning bör installeras på dina virtuella datorer (nu inaktuell) (standard- och kostnadsfria nivåer)|
|**Sårbarheter i dina virtuella datorer bör åtgärdas**|Ersätter följande två rekommendationer:<br>Åtgärda sårbarheter på dina virtuella datorer (som drivs av Qualys) (är nu inaktuella)<br>Sårbarheter bör åtgärdas av en lösning för sårbarhetsbedömning (nu inaktuell)|
|||

Nu ska du använda samma rekommendation för att distribuera Security Center tillägget för sårbarhetsbedömning eller en privat licensierad lösning ("BYOL") från en partner som Qualys eller Rapid7.

När sårbarheter hittas och rapporteras till Security Center varnar en enda rekommendation dig om resultaten oavsett vilken lösning för sårbarhetsbedömning som identifierade dem.

#### <a name="updating-dependencies"></a>Uppdatera beroenden

Om du har skript, frågor eller automatiseringar som refererar till tidigare rekommendationer eller principnycklar/namn använder du tabellerna nedan för att uppdatera referenserna:

##### <a name="before-august-2020"></a>Före augusti 2020

| Rekommendation|Omfång|
|----|:----|
|**Aktivera den inbyggda lösningen för sårbarhetsbedömning på virtuella datorer (drivs av Qualys)**<br>Nyckel: 550e890b-e652-4d22-8274-60b3bdb24c63|Inbyggd|
|**Åtgärda sårbarheter på dina virtuella datorer (som drivs av Qualys)**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggd|
|**Lösningen för sårbarhetsbedömning bör installeras på dina virtuella datorer**<br>Nyckel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Sårbarheter bör åtgärdas av en lösning för sårbarhetsbedömning**<br>Nyckel: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|Policy|Omfång|
|----|:----|
|**Sårbarhetsbedömning bör aktiveras på virtuella datorer**<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Inbyggd|
|**Sårbarheter bör åtgärdas av en lösning för sårbarhetsbedömning**<br>Princip-ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>Från augusti 2020

|Rekommendation|Omfång|
|----|:----|
|**En lösning för sårbarhetsbedömning ska vara aktiverad på dina virtuella datorer**<br>Nyckel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Inbyggd + BYOL|
|**Sårbarheter i dina virtuella datorer bör åtgärdas**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggd + BYOL|
|||

|Policy|Omfång|
|----|:----|
|[**Sårbarhetsbedömning bör aktiveras på virtuella datorer**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Inbyggd + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nya AKS-säkerhetsprinciper har lagts till ASC_default initiativ – endast för privat förhandsversion

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard lägger Security Center till principer på Kubernetes-nivå och härdningsrekommendationer, inklusive tvingande alternativ med Kubernetes-antagningskontroll.

Den tidiga fasen i det här projektet innehåller en privat förhandsgranskning och tillägg av nya (inaktiverade som standard) principer till ASC_default initiativet.

Du kan ignorera dessa principer på ett säkert sätt och det kommer inte att påverka din miljö. Om du vill aktivera dem registrerar du dig för förhandsversionen på https://aka.ms/SecurityPrP och väljer bland följande alternativ:

1. **Enskild förhandsgranskning** – Om du bara vill delta i den här privata förhandsversionen. Nämn uttryckligen "KONTINUERLIG ASC-genomsökning" som den förhandsversion som du vill ansluta till.
1. **Pågående program** – ska läggas till i den här och framtida privata förhandsversioner. Du måste slutföra ett profil- och sekretessavtal.


## <a name="july-2020"></a>Juli 2020

Uppdateringar i juli omfattar:
- [Sårbarhetsbedömning för virtuella datorer är nu tillgängligt för avbildningar som inte finns på marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Hotskydd för Azure Storage utökat med Azure Files och Azure Data Lake Storage Gen2 (förhandsversion)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Åtta nya rekommendationer för att aktivera funktioner för skydd mot hot](#eight-new-recommendations-to-enable-threat-protection-features)
- [Förbättringar av containersäkerheten – snabbare genomsökning av registret och uppdaterad dokumentation](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Anpassningsbara programkontroller har uppdaterats med en ny rekommendation och stöd för jokertecken i sökvägsregler](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sex principer för sql-avancerad datasäkerhet är inaktuella](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Sårbarhetsbedömning för virtuella datorer är nu tillgängligt för avbildningar som inte finns på marketplace

När du distribuerar en lösning för sårbarhetsbedömning Security Center en verifieringskontroll innan du distribuerade. Kontrollen var att bekräfta en Marketplace-SKU för den virtuella måldatorn. 

Från den här uppdateringen har kontrollen tagits bort och du kan nu distribuera verktyg för sårbarhetsbedömning till "anpassade" Windows- och Linux-datorer. Anpassade avbildningar är sådana som du har ändrat från marketplace-standardvärdena.

Även om du nu kan distribuera tillägget för integrerad sårbarhetsbedömning (som drivs av Qualys) på många fler datorer är stödet bara tillgängligt om du använder ett operativsystem som anges i Distribuera den integrerade sårbarhetsskannern till virtuella datorer på [standardnivå](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

Läs mer om den [integrerade sårbarhetsskannern för virtuella datorer (kräver Azure Defender).](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)

Läs mer om hur du använder din egen privat licensierade lösning för sårbarhetsbedömning från Qualys eller Rapid7 i Distribuera en [lösning för sårbarhetsgenomsökning för partner.](deploy-vulnerability-assessment-vm.md)


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Skydd mot hot för Azure Storage utökat till att Azure Files och Azure Data Lake Storage Gen2 (förhandsversion)

Hotskydd för Azure Storage identifierar potentiellt skadlig aktivitet på dina Azure Storage konton. Security Center visar aviseringar när den identifierar försök att komma åt eller utnyttja dina lagringskonton. 

Dina data kan skyddas oavsett om de lagras som blobcontainrar, filresurser eller datasjöar.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Åtta nya rekommendationer för att aktivera funktioner för skydd mot hot

Åtta nya rekommendationer har lagts till för att tillhandahålla ett enkelt sätt att aktivera Azure Security Center:s hotskyddsfunktioner för följande resurstyper: virtuella datorer, App Service-planer, Azure SQL Database-servrar, SQL-servrar på datorer, Azure Storage-konton, Azure Kubernetes Service-kluster, Azure Container Registry-register och Azure Key Vault-valv.

De nya rekommendationerna är:

- **Avancerad datasäkerhet ska vara aktiverat på Azure SQL Database servrar**
- **Avancerad datasäkerhet ska vara aktiverat på SQL-servrar på datorer**
- **Avancerat skydd ska vara aktiverat på Azure App Service planer**
- **Avancerat skydd ska vara aktiverat Azure Container Registry register**
- **Avancerat skydd ska vara aktiverat på Azure Key Vault valv**
- **Avancerat skydd ska vara aktiverat på Azure Kubernetes Service kluster**
- **Advanced Threat Protection ska vara aktiverat på Azure Storage konton**
- **Avancerat skydd ska vara aktiverat på virtuella datorer**

Dessa nya rekommendationer tillhör **Enable Azure Defender** security control (Aktivera Azure Defender säkerhetskontroll).

Rekommendationerna omfattar även funktionen snabbkorrigering. 

> [!IMPORTANT]
> Om du åtgärdar dessa rekommendationer debiteras du för att skydda relevanta resurser. Dessa avgifter börjar omedelbart om du har relaterade resurser i den aktuella prenumerationen. Eller i framtiden, om du lägger till dem vid ett senare tillfälle.
> 
> Om du till exempel inte har några Azure Kubernetes Service i din prenumeration och du aktiverar skydd mot hot tillkommer inga avgifter. Om du i framtiden lägger till ett kluster i samma prenumeration skyddas det automatiskt och avgifterna börjar vid den tidpunkten.

Läs mer om var och en av dessa på [referenssidan för säkerhetsrekommendationer.](recommendations-reference.md)

Läs mer om [skydd mot hot i Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Förbättringar av containersäkerheten – snabbare genomsökning av registret och uppdaterad dokumentation

Som en del av de kontinuerliga investeringar i domänen för containersäkerhet delar vi med oss av en betydande prestandaförbättring i Security Center:s dynamiska genomsökningar av containeravbildningar som lagras i Azure Container Registry. Genomsökningar slutförs nu vanligtvis på cirka två minuter. I vissa fall kan det ta upp till 15 minuter.

För att förbättra tydligheten och vägledningen Azure Security Center säkerhetsfunktioner för containrar har vi även uppdaterat dokumentationssidorna för containersäkerhet. 

Läs mer Security Center om säkerhet för containrar i följande artiklar:

- [Översikt över Security Center säkerhetsfunktioner för containrar](container-security.md)
- [Information om integreringen med Azure Container Registry](defender-for-container-registries-introduction.md)
- [Information om integreringen med Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Så här genomsöker du dina register och härdar Docker-värdarna](container-security.md)
- [Säkerhetsaviseringar från hotskyddsfunktionerna för Azure Kubernetes Service kluster](alerts-reference.md#alerts-akscluster)
- [Säkerhetsaviseringar från hotskyddsfunktionerna för Azure Kubernetes Service värdar](alerts-reference.md#alerts-containerhost)
- [Säkerhetsrekommendationer för containrar](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Anpassningsbara programkontroller har uppdaterats med en ny rekommendation och stöd för jokertecken i sökvägsregler

Funktionen för anpassningsbara programkontroller har fått två viktiga uppdateringar:

* En ny rekommendation identifierar potentiellt legitimt beteende som inte tidigare har tillåtits. Den nya rekommendationen **Allowlist-regler** i din princip för anpassningsbar programkontroll bör uppdateras. Du uppmanas att lägga till nya regler i den befintliga principen för att minska antalet falska positiva identifieringar i aviseringar om överträdelser av anpassningsbara programkontroller.

* Sökvägsregler stöder nu jokertecken. Från den här uppdateringen kan du konfigurera tillåtna sökvägsregler med jokertecken. Det finns två scenarier som stöds:

    * Använda ett jokertecken i slutet av en sökväg för att tillåta alla körbara filer i den här mappen och undermappar

    * Använda ett jokertecken mitt i en sökväg för att aktivera ett känt körbart namn med ett föränderligt mappnamn (t.ex. personliga användarmappar med en känd körbar fil, automatiskt genererade mappnamn osv.).


[Läs mer om anpassningsbara programkontroller.](security-center-adaptive-application.md)



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sex principer för avancerad datasäkerhet i SQL är inaktuella

Sex principer som rör avancerad datasäkerhet för SQL-datorer håller på att bli inaktuella:

- Avancerade hotskyddstyper ska vara inställda på Alla i avancerade inställningar för datasäkerhet i SQL Managed Instance
- Advanced Threat Protection-typer bör anges till "Alla" i avancerade inställningar för datasäkerhet i SQL Server
- Avancerade datasäkerhetsinställningar för SQL Managed Instance bör innehålla en e-postadress för att ta emot säkerhetsaviseringar
- Avancerade datasäkerhetsinställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhetsaviseringar
- E-postaviseringar till administratörer och prenumerationsägare ska aktiveras i avancerade datasäkerhetsinställningar för SQL Managed Instance
- E-postaviseringar till administratörer och prenumerationsägare ska aktiveras i avancerade inställningar för datasäkerhet i SQL Server

Läs mer [om inbyggda principer](./policy-reference.md).



## <a name="june-2020"></a>Juni 2020

Uppdateringar i juni omfattar:

- [Säkerhetspoängs-API (förhandsversion)](#secure-score-api-preview)
- [Avancerad datasäkerhet för SQL-datorer (Azure, andra moln och lokalt) (förhandsversion)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Två nya rekommendationer för att distribuera Log Analytics-agenten Azure Arc datorer (förhandsversion)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nya principer för att skapa automatiseringskonfigurationer för löpande export och arbetsflöden i stor skala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Ny rekommendation för att använda NSG:er för att skydda virtuella datorer som inte är internetuppspelade](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nya principer för att aktivera skydd mot hot och avancerad datasäkerhet](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Säkerhetspoängs-API (förhandsversion)

Nu kan du komma åt dina poäng via [API:et för säkerhetspoäng](/rest/api/securitycenter/securescores/) (för närvarande i förhandsversion). API-metoderna ger flexibiliteten att köra frågor mot data och skapa en egen rapporteringsmekanism för dina säkerhetspoäng över tid. Du kan till exempel använda API:et **för säkerhetspoäng** för att hämta poängen för en specifik prenumeration. Dessutom kan du använda API:et **Secure Score Controls** för att visa en lista över säkerhetskontroller och aktuella poäng för dina prenumerationer.

Exempel på externa verktyg som möjliggjordes med API:et för säkerhetspoäng finns [i området för säkerhetspoäng i vår GitHub-community.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

Läs mer om [säkerhetspoäng och säkerhetskontroller i Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Avancerad datasäkerhet för SQL-datorer (Azure, andra moln och lokalt) (förhandsversion)

Azure Security Center avancerade datasäkerhet för SQL-datorer skyddar nu SQL-servrar som finns i Azure, i andra molnmiljöer och även på lokala datorer. Detta utökar skyddet för dina Azure-interna SQL-servrar för att ge fullständigt stöd för hybridmiljöer.

Avancerad datasäkerhet ger sårbarhetsbedömning och avancerat skydd mot hot för dina SQL-datorer oavsett var de finns.

Konfigurera omfattar två steg:

1. Distribuera Log Analytics-agenten till SQL Server värddator för att tillhandahålla anslutningen till Azure-kontot.

1. Aktivera det valfria paketet Security Center på sidan med priser och inställningar.

Läs mer om [avancerad datasäkerhet för SQL-datorer.](defender-for-sql-usage.md)



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Två nya rekommendationer för att distribuera Log Analytics-agenten Azure Arc datorer (förhandsversion)

Två nya rekommendationer har lagts till för att hjälpa dig att distribuera [Log Analytics-agenten](../azure-monitor/agents/log-analytics-agent.md) till Azure Arc datorer och se till att de skyddas av Azure Security Center:

- **Log Analytics-agenten ska installeras på dina Windows-baserade Azure Arc (förhandsversion)**
- **Log Analytics-agenten ska installeras på dina Linux-baserade Azure Arc (förhandsversion)**

Dessa nya rekommendationer visas i samma fyra säkerhetskontroller som den befintliga (relaterade) rekommendationen. Övervakningsagenten ska installeras på dina datorer: åtgärda säkerhetskonfigurationer, tillämpa anpassningsbar programkontroll, tillämpa systemuppdateringar och aktivera slutpunktsskydd.

Rekommendationerna omfattar även funktionen Snabbkorrigering för att påskynda distributionsprocessen. 

Läs mer om de här två nya rekommendationerna i tabellen [med compute- och apprekommendationer.](recommendations-reference.md#recs-compute)

Läs mer om hur Azure Security Center använder agenten i [Vad är Log Analytics-agenten?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Läs mer om [tillägg för Azure Arc datorer](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nya principer för att skapa konfigurationer för kontinuerlig export och arbetsflödesautomatisering i stor skala

Att automatisera organisationens processer för övervakning och incidenter kan avsevärt förbättra den tid det tar att undersöka och minimera säkerhetsincidenter.

Om du vill distribuera dina automatiseringskonfigurationer i hela organisationen använder du de här [](continuous-export.md) inbyggda Azure-principerna "DeployIfdNotExist" för att skapa och konfigurera procedurer för kontinuerlig export [och arbetsflödesautomatisering:](workflow-automation.md)

Principerna finns i Azure Policy:


|Mål  |Policy  |Princip-ID  |
|---------|---------|---------|
|Löpande export till händelsehubb|[Distribuera export till händelsehubben för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Löpande export till Log Analytics-arbetsyta|[Distribuera export till Log Analytics-arbetsytan för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Arbetsflödesautomation för säkerhetsaviseringar|[Distribuera arbetsflödesautomation för Azure Security Center-aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Arbetsflödesautomation för säkerhetsrekommendationer|[Distribuera arbetsflödesautomation för Azure Security Center-rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Kom igång med mallar [för automatisering av arbetsflöden.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

Läs mer om hur du använder de två exportpolicyerna [i Konfigurera arbetsflödesautomation](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) i stor skala med hjälp av angivna principer [och Konfigurera en löpande export.](continuous-export.md#set-up-a-continuous-export)


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Ny rekommendation för att använda NSG:er för att skydda virtuella datorer som inte är internetuppspelade

Säkerhetskontrollen "implementera säkerhetsmetoder" innehåller nu följande nya rekommendation:

- **Virtuella datorer som inte är internetriktade bör skyddas med nätverkssäkerhetsgrupper**

En befintlig rekommendation, **Internetuppriktade** virtuella datorer bör skyddas med nätverkssäkerhetsgrupper , gjorde ingen skillnad mellan Internetuppriktade och icke-Internetuppspelade virtuella datorer. För båda har en rekommendation med hög allvarlighetsgrad genererats om en virtuell dator inte har tilldelats till en nätverkssäkerhetsgrupp. Den här nya rekommendationen separerar datorer som inte är internetriktade för att minska falska positiva resultat och undvika onödiga aviseringar med hög allvarlighetsgrad.

Läs mer i tabellen [Nätverksrekommendationer.](recommendations-reference.md#recs-networking)




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nya principer för att aktivera skydd mot hot och avancerad datasäkerhet

De nya principerna nedan har lagts till i ASC-standardinitiativ och är utformade för att hjälpa till med att aktivera skydd mot hot eller avancerad datasäkerhet för relevanta resurstyper.

Principerna finns i Azure Policy:


| Policy                                                                                                                                                                                                                                                                | Princip-ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Avancerad datasäkerhet ska vara aktiverat på Azure SQL Database servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Avancerad datasäkerhet ska vara aktiverat på SQL-servrar på datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Avancerat skydd ska vara aktiverat på Azure Storage konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Avancerat skydd ska aktiveras på Azure Key Vault valv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Avancerat skydd ska vara aktiverat på Azure App Service planer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Avancerat hotskydd ska aktiveras Azure Container Registry register](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Avancerat skydd ska vara aktiverat Azure Kubernetes Service kluster](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Avancerat hotskydd ska aktiveras på Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Läs mer om [skydd mot hot i Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Maj 2020

Uppdateringar i kan vara:
- [Regler för undertryckning av aviseringar (förhandsversion)](#alert-suppression-rules-preview)
- [Sårbarhetsbedömning av virtuella datorer är nu allmänt tillgänglig](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Ändringar av just-in-time-åtkomst (JIT) för virtuella datorer](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Anpassade rekommendationer har flyttats till en separat säkerhetskontroll](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Växla tillagt för att visa rekommendationer i kontroller eller som en platt lista](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Utökad säkerhetskontroll – Implementera metodtips för säkerhet](#expanded-security-control-implement-security-best-practices)
- [Anpassade principer med anpassade metadata är nu allmänt tillgängliga](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Analysfunktioner för kraschdump som migreras till fillös attackidentifiering](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regler för undertryckning av aviseringar (förhandsversion)

Den här nya funktionen (för närvarande i förhandsversion) hjälper till att minska varningsutmattning. Använd regler för att automatiskt dölja aviseringar som är kända för att vara mindre aktiva eller relaterade till normala aktiviteter i din organisation. På så sätt kan du fokusera på de mest relevanta hoten. 

Aviseringar som matchar dina aktiverade undertryckningsregler genereras fortfarande, men deras tillstånd kommer att stängas. Du kan se tillståndet i Azure Portal eller hur du kommer åt Security Center säkerhetsaviseringar.

Undertryckningsregler definierar de kriterier för vilka aviseringar ska avvisas automatiskt. Normalt använder du en undertryckningsregel för att:

- ignorera aviseringar som du har identifierat som falska positiva resultat

- ignorera aviseringar som utlöses för ofta för att vara användbara

Läs mer om [att förhindra aviseringar Azure Security Center av hotskydd.](alerts-suppression-rules.md)


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Sårbarhetsbedömning av virtuella datorer är nu allmänt tillgänglig

Security Center standardnivån innehåller nu en integrerad sårbarhetsbedömning för virtuella datorer utan extra kostnad. Det här tillägget drivs av Qualys men rapporterar resultaten direkt tillbaka till Security Center. Du behöver inte en Qualys-licens eller ens ett Qualys-konto – allt hanteras sömlöst i Security Center.

Den nya lösningen kan kontinuerligt genomsöka dina virtuella datorer för att hitta sårbarheter och presentera resultaten i Security Center. 

Använd den nya säkerhetsrekommendationen för att distribuera lösningen:

"Aktivera den inbyggda lösningen för sårbarhetsbedömning på virtuella datorer (drivs av Qualys)"

Läs mer om [Security Center i den integrerade sårbarhetsbedömningen för virtuella datorer.](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Ändringar av just-in-time-åtkomst (JIT) för virtuell dator (VM)

Security Center innehåller en valfri funktion för att skydda hanteringsportarna för dina virtuella datorer. Detta ger ett skydd mot den vanligaste formen av brute force-attacker.

Den här uppdateringen medför följande ändringar i den här funktionen:

- Rekommendationen som uppmanar dig att aktivera JIT på en virtuell dator har bytt namn. Tidigare, "Just-in-time network access control should be applied on virtual machines" (Just-in-time-nätverksåtkomstkontroll ska tillämpas på virtuella datorer) är det nu: "Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk".

- Rekommendationen utlöses bara om det finns öppna hanteringsportar.

Läs mer om [JIT-åtkomstfunktionen](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Anpassade rekommendationer har flyttats till en separat säkerhetskontroll

En säkerhetskontroll som introducerades med den förbättrade säkerhetspoängen var "Implementera metodtips för säkerhet". Alla anpassade rekommendationer som skapats för dina prenumerationer placerades automatiskt i den kontrollen. 

För att göra det enklare att hitta dina anpassade rekommendationer har vi flyttat dem till en dedikerad säkerhetskontroll, "Anpassade rekommendationer". Den här kontrollen påverkar inte dina säkerhetspoäng.

Läs mer om säkerhetskontroller i [Förbättrad säkerhetspoäng (förhandsversion) i Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Växla tillagt för att visa rekommendationer i kontroller eller som en platt lista

Säkerhetskontroller är logiska grupper med relaterade säkerhetsrekommendationer. De återspeglar dina sårbara angreppsytor. En kontroll är en uppsättning säkerhetsrekommendationer med instruktioner som hjälper dig att implementera dessa rekommendationer.

Om du omedelbart vill se hur väl din organisation skyddar varje enskild angreppsyta kan du granska poängen för varje säkerhetskontroll.

Som standard visas dina rekommendationer i säkerhetskontrollerna. Från den här uppdateringen kan du också visa dem som en lista. Om du vill visa dem som enkla listor sorterade efter hälsotillståndet för de berörda resurserna använder du den nya växlingsknappen Gruppera efter kontroller. Växlingsknappen finns ovanför listan i portalen.

Säkerhetskontrollerna – och den här växlingsknappen – ingår i den nya säkerhetspoängupplevelsen. Kom ihåg att skicka feedback från portalen.

Läs mer om säkerhetskontroller i [Förbättrad säkerhetspoäng (förhandsversion) i Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Växla gruppera efter kontroller för rekommendationer":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Utökad säkerhetskontroll – Implementera metodtips för säkerhet 

En säkerhetskontroll som introducerades med den förbättrade säkerhetspoängen är "Implementera metodtips för säkerhet". När en rekommendation finns i den här kontrollen påverkar den inte säkerhetspoängen. 

Med den här uppdateringen har tre rekommendationer flyttats från de kontroller som de ursprungligen placerades i och till den här bästa praxiskontrollen. Vi har tagit det här steget eftersom vi har fastställt att risken för dessa tre rekommendationer är lägre än vad vi först trodde.

Dessutom har två nya rekommendationer introducerats och lagts till i den här kontrollen.

De tre rekommendationerna som flyttas är:

- **MFA ska vara aktiverat för konton med läsbehörighet för din prenumeration** (ursprungligen i kontrollen "Aktivera MFA")
- **Externa konton med läsbehörighet bör tas bort från din prenumeration** (ursprungligen i kontrollen "Hantera åtkomst och behörigheter")
- **Högst 3 ägare bör anges för din prenumeration (ursprungligen** i kontrollen "Hantera åtkomst och behörigheter")

De två nya rekommendationerna som läggs till i kontrollen är:

- **Gästkonfigurationstillägget ska** installeras på virtuella Windows-datorer (förhandsversion) – Med [hjälp av Azure Policy gästkonfigurationen](../governance/policy/concepts/guest-configuration.md) visas i virtuella datorer för server- och programinställningar (endast Windows).

- Windows Defender Exploit Guard ska vara aktiverat på dina datorer **(förhandsversion)** – Windows Defender Exploit Guard utnyttjar Azure Policy gästkonfigurationsagenten. Exploit Guard har fyra komponenter som är utformade för att låsa enheter mot en mängd olika angreppsvektorer och blockera beteenden som ofta används i attacker med skadlig kod samtidigt som företag kan balansera sina säkerhetsrisker och produktivitetskrav (endast Windows).

Läs mer om Windows Defender Exploit Guard i [Skapa och distribuera en Exploit Guard-princip.](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)

Läs mer om säkerhetskontroller i [Förbättrad säkerhetspoäng (förhandsversion).](secure-score-security-controls.md)



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Anpassade principer med anpassade metadata är nu allmänt tillgängliga

Anpassade principer är nu en del av Security Center, säkerhetspoäng och instrumentpanelen för regelefterlevnadsstandarder. Den här funktionen är nu allmänt tillgänglig och gör att du kan utöka organisationens säkerhetsutvärderingstäckning i Security Center. 

Skapa ett anpassat initiativ i Azure Policy, lägg till principer i det och publicera det i Azure Security Center och visualisera det som rekommendationer.

Nu har vi också lagt till alternativet för att redigera anpassade rekommendationsmetadata. Metadataalternativen omfattar bland annat allvarlighetsgrad, reparationssteg och hotinformation.  

Läs mer om [att förbättra dina anpassade rekommendationer med detaljerad information.](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Analysfunktioner för kraschdump som migreras till fillös attackidentifiering 

Vi integrerar cda-analysfunktionerna (Windows crash dump analysis) i [fillös attackidentifiering.](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers) Analys av fillös attackidentifiering ger förbättrade versioner av följande säkerhetsaviseringar för Windows-datorer: Identifierad kodinjektion, Maskering av identifierad Windows-modul, Shellcode-identifiering och Misstänkt kodsegment har identifierats.

Några av fördelarna med den här övergången:

- **Proaktiv och snabb identifiering av skadlig** kod – CDA-metoden innebär att vänta på att en krasch ska inträffa och sedan köra analyser för att hitta skadliga artefakter. Användning av fillös attackidentifiering ger proaktiv identifiering av minneslösa hot medan de körs. 

- **Berikade** aviseringar – Säkerhetsaviseringarna från fillös attackidentifiering omfattar berikande åtgärder som inte är tillgängliga från CDA, till exempel information om aktiva nätverksanslutningar. 

- **Aviseringsaggregering** – När CDA identifierade flera attackmönster i en enda kraschdump utlöstes flera säkerhetsaviseringar. Fillös attackidentifiering kombinerar alla identifierade attackmönster från samma process till en enda avisering, vilket gör att du inte behöver korrelera flera aviseringar.

- **Minskade krav på Log Analytics-arbetsytan** – Kraschdumpar som innehåller potentiellt känsliga data kommer inte längre att överföras till Log Analytics-arbetsytan.






## <a name="april-2020"></a>April 2020

Uppdateringar i april är:
- [Dynamiska efterlevnadspaket är nu allmänt tillgängliga](#dynamic-compliance-packages-are-now-generally-available)
- [Identitetsrekommendationer ingår nu på den Azure Security Center kostnadsfria nivån](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamiska efterlevnadspaket är nu allmänt tillgängliga

Instrumentpanelen Azure Security Center regelefterlevnad innehåller nu **dynamiska efterlevnadspaket** (nu allmänt tillgängliga) för att spåra ytterligare bransch- och regelstandarder.

Dynamiska efterlevnadspaket kan läggas till i din prenumeration eller hanteringsgrupp från Security Center säkerhetsprincipsidan. När du har introducerat en standard eller ett benchmark-mått visas standarden på instrumentpanelen för regelefterlevnad med alla associerade efterlevnadsdata mappade som utvärderingar. En sammanfattningsrapport för någon av de standarder som har publiceras kommer att vara tillgänglig för nedladdning.

Nu kan du lägga till standarder som:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official och UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (ny)** (som är en mer fullständig representation av Azure CIS 1.1.0)

Dessutom har vi nyligen lagt till [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction), Microsoft-skribenterade Azure-specifika riktlinjer för bästa praxis för säkerhet och efterlevnad baserat på vanliga ramverk för efterlevnad. Ytterligare standarder kommer att stödjas på instrumentpanelen när de blir tillgängliga.  
 
Läs mer om [att anpassa uppsättningen standarder på instrumentpanelen för regelefterlevnad.](update-regulatory-compliance-packages.md)


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Identitetsrekommendationer ingår nu på den Azure Security Center kostnadsfria nivån

Säkerhetsrekommendationer för identitet och åtkomst på den kostnadsfria Azure Security Center är nu allmänt tillgängliga. Det här är en del av arbetet med att göra CSPM-funktionerna (Cloud Security Posture Management) kostnadsfria. Hittills var dessa rekommendationer bara tillgängliga på standardprisnivån.

Exempel på rekommendationer för identitet och åtkomst är:

- "Multifaktorautentisering ska aktiveras för konton med ägarbehörighet för din prenumeration."
- "Högst tre ägare bör anges för din prenumeration."
- "Inaktuella konton bör tas bort från prenumerationen."

Om du har prenumerationer på den kostnadsfria prisnivån påverkas deras säkerhetspoäng av den här ändringen eftersom de aldrig utvärderades för deras identitets- och åtkomstsäkerhet.

Läs mer om [rekommendationer för identitet och åtkomst.](recommendations-reference.md#recs-identityandaccess)

Läs mer om [att hantera multifaktorautentisering (MFA) för dina prenumerationer.](security-center-identity-access.md)



## <a name="march-2020"></a>Mars 2020

Uppdateringar i mars omfattar:

- [Arbetsflödesautomation är nu allmänt tillgängligt](#workflow-automation-is-now-generally-available)
- [Integrering av Azure Security Center med Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Skydd för Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Förbättrad just-in-time-upplevelse](#improved-just-in-time-experience)
- [Två säkerhetsrekommendationer för webbaserade program är inaktuella](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Arbetsflödesautomation är nu allmänt tillgängligt

Funktionen arbetsflödesautomation i Azure Security Center är nu allmänt tillgänglig. Använd den för att automatiskt Logic Apps säkerhetsaviseringar och rekommendationer. Dessutom är manuella utlösare tillgängliga för aviseringar och alla rekommendationer som har snabbkorrigeringsalternativet tillgängligt.

Alla säkerhetsprogram innehåller flera arbetsflöden för incidenter. Dessa processer kan omfatta att meddela relevanta intressenter, starta en ändringshanteringsprocess och tillämpa specifika åtgärdssteg. Säkerhetsexperter rekommenderar att du automatiserar så många steg som möjligt i dessa procedurer. Automatisering minskar omkostnaderna och kan förbättra säkerheten genom att se till att processstegen utförs snabbt, konsekvent och enligt dina fördefinierade krav.

Mer information om automatiska och manuella Security Center för att köra arbetsflöden finns i [arbetsflödesautomation.](workflow-automation.md)

Läs mer om [hur du skapar Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integrering av Azure Security Center med Windows Admin Center

Nu är det möjligt att flytta dina lokala Windows-servrar från Windows Admin Center direkt till Azure Security Center. Security Center sedan din enda fönsterruta för att visa säkerhetsinformation för alla dina Windows Admin Center-resurser, inklusive lokala servrar, virtuella datorer och ytterligare PaaS-arbetsbelastningar.

När du har Windows Admin Center från Windows Admin Center till Azure Security Center kan du:

- Visa säkerhetsaviseringar och rekommendationer i Security Center tillägget för Windows Admin Center.
- Visa säkerhetsstatusen och hämta ytterligare detaljerad information om dina Windows Admin Center-hanterade servrar i Security Center i Azure Portal (eller via ett API).

Läs mer om [hur du integrerar Azure Security Center med Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Skydd för Azure Kubernetes Service

Azure Security Center utökar sina containersäkerhetsfunktioner för att skydda Azure Kubernetes Service (AKS).

Den populära Kubernetes-plattformen med öppen källkod har blivit så populär att den nu är en branschstandard för containerorkestrering. Trots den omfattande implementeringen finns det fortfarande brist på förståelse för hur du skyddar en Kubernetes-miljö. Att skydda angreppsytorna i ett containerprogram kräver expertis för att säkerställa att infrastrukturen är konfigurerad på ett säkert sätt och ständigt övervakas efter potentiella hot.

I Security Center skydd ingår:

- **Identifiering och synlighet** – Kontinuerlig identifiering av hanterade AKS-instanser i de prenumerationer som registrerats Security Center.
- **Säkerhetsrekommendationer** – Användbara rekommendationer som hjälper dig att följa metodtipsen för säkerhet för AKS. Dessa rekommendationer ingår i din säkerhetspoäng för att säkerställa att de visas som en del av din organisations säkerhetsstatus. Ett exempel på en AKS-relaterad rekommendation som du kan se är "Rollbaserad åtkomstkontroll bör användas för att begränsa åtkomsten till ett Kubernetes-tjänstkluster".
- **Hotskydd** – Genom kontinuerlig analys av din AKS-distribution Security Center dig om hot och skadlig aktivitet som har identifierats på värd- och AKS-klusternivå.

Läs mer om [Integrering av Azure Kubernetes Services med Security Center](defender-for-kubernetes-introduction.md).

Läs mer om [containersäkerhetsfunktionerna i Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Förbättrad just-in-time-upplevelse

Funktionerna, driften och användargränssnittet för Azure Security Center just-in-time-verktyg som skyddar dina hanteringsportar har förbättrats på följande sätt: 

- **Motiveringsfält** – När du begär åtkomst till en virtuell dator (VM) via just-in-time-sidan i Azure Portal finns ett nytt valfritt fält tillgängligt för att ange en motivering för begäran. Information som anges i det här fältet kan spåras i aktivitetsloggen. 
- **Automatisk rensning av redundanta JIT-regler (just-in-time)** – När du uppdaterar en JIT-princip körs ett rensningsverktyg automatiskt för att kontrollera giltigheten för hela regeluppsättningen. Verktyget söker efter matchningsfel mellan regler i principen och reglerna i NSG:n. Om rensningsverktyget hittar ett matchningsfel avgör det orsaken och tar bort inbyggda regler som inte längre behövs när det är säkert att göra det. Rensaren tar aldrig bort regler som du har skapat. 

Läs mer om [JIT-åtkomstfunktionen](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Två säkerhetsrekommendationer för webbaserade program är inaktuella

Två säkerhetsrekommendationer som rör webbprogram håller på att bli inaktuella: 

- Reglerna för webbprogram på IaaS NSG:er bör härdas.
    (Relaterad princip: NSG:erna för webbprogram på IaaS ska härdas)

- Åtkomst till App Services bör begränsas.
    (Relaterad princip: Åtkomst till App Services bör begränsas [förhandsversion])

Dessa rekommendationer visas inte längre Security Center listan över rekommendationer. De relaterade principerna tas inte längre med i initiativet med namnet "Security Center Default".

Läs mer om [säkerhetsrekommendationer.](recommendations-reference.md)




## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Fillös attackidentifiering för Linux (förhandsversion)

När angripare som ökar använder dolda metoder för att undvika identifiering Azure Security Center utökar fillös attackidentifiering för Linux, förutom Windows. Fillösa attacker utnyttjar sårbarheter i programvara, matar in skadliga nyttolaster i oskadliga systemprocesser och döljs i minnet. Dessa tekniker:

- minimera eller eliminera spår av skadlig kod på disken
- minska risken för identifiering av diskbaserade lösningar för skanning av skadlig kod avsevärt

För att motverka det här Azure Security Center ut fillös attackidentifiering för Windows i oktober 2018 och har nu utökat fillös attackidentifiering på Linux också. 



## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score-preview"></a>Förbättrad säkerhetspoäng (förhandsversion)

En förbättrad version av funktionen för säkerhetspoäng i Azure Security Center är nu tillgänglig som förhandsversion. I den här versionen grupperas flera rekommendationer i säkerhetskontroller som bättre återspeglar dina sårbara angreppsytor (till exempel begränsa åtkomsten till hanteringsportar).

Bekanta dig med säkerhetspoängändringarna under förhandsversionsfasen och fastställ andra åtgärder som hjälper dig att ytterligare skydda din miljö.

Läs mer om [förbättrade säkerhetspoäng (förhandsversion)](secure-score-security-controls.md).



## <a name="november-2019"></a>November 2019

Uppdateringar i november är:
 - [Hotskydd för Azure Key Vault i Nordamerika (förhandsversion)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Threat Protection för Azure Storage inkluderar malware reputationscreening](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Arbetsflödesautomation med Logic Apps (förhandsversion)](#workflow-automation-with-logic-apps-preview)
 - [Snabbåtgärd för massresurser som är allmänt tillgängliga](#quick-fix-for-bulk-resources-generally-available)
 - [Genomsök containeravbildningar efter säkerhetsrisker (förhandsversion)](#scan-container-images-for-vulnerabilities-preview)
 - [Ytterligare standarder för regelefterlevnad (förhandsversion)](#additional-regulatory-compliance-standards-preview)
 - [Threat Protection för Azure Kubernetes Service (förhandsversion)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Sårbarhetsbedömning av virtuell dator (förhandsversion)](#virtual-machine-vulnerability-assessment-preview)
 - [Avancerad datasäkerhet för SQL-servrar på Azure Virtual Machines (förhandsversion)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Stöd för anpassade principer (förhandsversion)](#support-for-custom-policies-preview)
 - [Utöka Azure Security Center täckning med plattform för communityn och partner](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Avancerade integreringar med export av rekommendationer och aviseringar (förhandsversion)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Registrera de virtuella servrarna för att Security Center från Windows Admin Center (förhandsversion)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Hotskydd för Azure Key Vault i Nordamerika (förhandsversion)

Azure Key Vault är en viktig tjänst för att skydda data och förbättra prestanda för molnprogram genom att erbjuda möjligheten att centralt hantera nycklar, hemligheter, kryptografiska nycklar och principer i molnet. Eftersom Azure Key Vault lagrar känsliga och affärskritiska data krävs maximal säkerhet för nyckelvalven och de data som lagras i dem.

Azure Security Center stöd för Threat Protection för Azure Key Vault ett extra lager med säkerhetsintelligens som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja nyckelvalv. Med det här nya skyddslagret kan kunder hantera hot mot sina nyckelvalv utan att vara säkerhetsexpert eller hantera system för säkerhetsövervakning. Funktionen är i offentlig förhandsversion i Nordamerika regioner.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Threat Protection för Azure Storage inkluderar malware reputationscreening

Hotskydd för Azure Storage erbjuder nya identifieringar som drivs av Microsoft Threat Intelligence för att identifiera uppladdningar av skadlig kod till Azure Storage med hjälp av hash-ryktesanalys och misstänkt åtkomst från en aktiv Tor-avslutsnod (en maskeringsproxy). Nu kan du visa upptäckt skadlig kod på lagringskonton med hjälp av Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Arbetsflödesautomation med Logic Apps (förhandsversion)

Organisationer med centralt hanterad säkerhet och IT/drift implementerar interna arbetsflödesprocesser för att driva nödvändiga åtgärder inom organisationen när avvikelser identifieras i deras miljöer. I många fall är dessa arbetsflöden upprepningsbara processer och automatisering kan effektivisera processer i organisationen mycket.

Idag introducerar vi en ny funktion i Security Center som gör att kunder kan skapa automatiseringskonfigurationer som utnyttjar Azure Logic Apps och skapa principer som automatiskt utlöser dem baserat på specifika ASC-resultat, till exempel rekommendationer eller aviseringar. Azure Logic App kan konfigureras för att göra anpassade åtgärder som stöds av den stora communityn med Logic App-anslutningsappar eller använda någon av mallarna som tillhandahålls av Security Center, till exempel skicka ett e-postmeddelande eller öppna en &trade; ServiceNow-biljett.

Mer information om automatiska och manuella funktioner Security Center för att köra arbetsflöden finns i [arbetsflödesautomation.](workflow-automation.md)

Mer information om hur du skapar Logic Apps finns i [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Snabbåtgärd för massresurser som är allmänt tillgängliga

Med de många uppgifter som en användare får som en del av Säkerhetspoäng kan det bli svårt att effektivt åtgärda problem i en stor vagnpark.

För att förenkla reparationen av felkonfigurerade säkerhetskonfigurationer och för att snabbt kunna åtgärda rekommendationer för en stor mängd resurser och förbättra säkerhetspoängen kan du använda Snabbåtgärd reparation.

Med den här åtgärden kan du välja de resurser som du vill tillämpa reparationen på och starta en reparationsåtgärd som konfigurerar inställningen för din räkning.

Snabbkorrigering är i dag allmänt tillgänglig för kunder som en del Security Center sidan med rekommendationer.

Se vilka rekommendationer som har snabbkorrigering aktiverat i [referensguiden för säkerhetsrekommendationer.](recommendations-reference.md)


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Genomsök containeravbildningar efter säkerhetsrisker (förhandsversion)

Azure Security Center kan nu söka igenom containeravbildningar i Azure Container Registry efter säkerhetsrisker.

Avbildningsgenomsökningen fungerar genom att containeravbildningsfilen parsas och sedan kontrolleras om det finns några kända säkerhetsrisker (som drivs av Qualys).

Själva genomsökningen utlöses automatiskt när nya containeravbildningar skickas till Azure Container Registry. Identifierade säkerhetsrisker visas som Security Center rekommendationer och ingår i Azure Secure Score tillsammans med information om hur du korrigerar dem för att minska attackytan som de tillåts.


### <a name="additional-regulatory-compliance-standards-preview"></a>Ytterligare standarder för regelefterlevnad (förhandsversion)

Instrumentpanelen för regelefterlevnad ger insikter om din efterlevnadsstatus baserat på Security Center utvärderingar. Instrumentpanelen visar hur din miljö uppfyller kontroller och krav som anges av specifika regelstandarder och branschstandarder och tillhandahåller normativa rekommendationer för hur du ska hantera dessa krav.

Instrumentpanelen för regelefterlevnad har hittills stöd för fyra inbyggda standarder: Azure CIS 1.1.0, PCI-DSS, ISO 27001 och SOC-TSP. Vi presenterar nu den offentliga förhandsversionen av ytterligare standarder som stöds: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM och UK Official tillsammans med UK NHS. Vi släpper också en uppdaterad version av Azure CIS 1.1.0 som täcker fler kontroller från standarden och förbättrar utökningsbarheten.

[Läs mer om att anpassa uppsättningen standarder i instrumentpanelen för regelefterlevnad.](update-regulatory-compliance-packages.md)


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Threat Protection för Azure Kubernetes Service (förhandsversion)

Kubernetes håller snabbt på att bli den nya standarden för distribution och hantering av programvara i molnet. Få personer har omfattande erfarenhet av Kubernetes och många fokuserar bara på allmän teknik och administration och förbiser säkerhetsaspekten. Kubernetes-miljön måste konfigureras noggrant för att vara säker, så att inga containerfokuserade attackytans dörrar exponeras för angripare. Security Center utökar sitt stöd i containerutrymmet till en av de snabbast växande tjänsterna i Azure – Azure Kubernetes Service (AKS).

De nya funktionerna i den här offentliga förhandsversionen är:

- **Identifiering & synlighet** – kontinuerlig identifiering av hanterade AKS-instanser Security Center i de registrerade prenumerationerna.
- **Rekommendationer för säkerhetspoäng** – Användbara objekt som hjälper kunderna att följa de bästa säkerhetsmetoderna för AKS och öka sina säkerhetspoäng. Rekommendationerna omfattar objekt som "Rollbaserad åtkomstkontroll ska användas för att begränsa åtkomsten till ett Kubernetes Service-kluster".
- **Hotidentifiering** – Värd- och klusterbaserad analys, till exempel "En privilegierad container har identifierats".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Sårbarhetsbedömning av virtuell dator (förhandsversion)

Program som är installerade på virtuella datorer kan ofta ha sårbarheter som kan leda till ett intrång i den virtuella datorn. Vi meddelar att standardnivån Security Center inbyggd sårbarhetsbedömning för virtuella datorer utan extra kostnad. Med sårbarhetsbedömningen, som drivs av Qualys i den offentliga förhandsversionen, kan du kontinuerligt genomsöka alla installerade program på en virtuell dator för att hitta sårbara program och presentera resultaten i Security Center-portalens upplevelse. Security Center tar hand om alla distributionsåtgärder så att inget extra arbete krävs från användaren. Framöver planerar vi att tillhandahålla alternativ för sårbarhetsbedömning för att stödja våra kunders unika affärsbehov.

[Läs mer om sårbarhetsbedömningar för azure-Virtual Machines](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Avancerad datasäkerhet för SQL-servrar på Azure Virtual Machines (förhandsversion)

Azure Security Center stöd för hotskydd och sårbarhetsbedömning för SQL-databaser som körs på virtuella IaaS-datorer är nu i förhandsversion.

[Sårbarhetsbedömning](../azure-sql/database/sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera och som kan identifiera, spåra och hjälpa dig att åtgärda eventuella säkerhetsrisker i databasen. Den ger insyn i din säkerhetsstatus som en del av Azures säkerhetspoäng och innehåller stegen för att lösa säkerhetsproblem och förbättra databasförstärkningar.

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din SQL-server. Den övervakar kontinuerligt din databas efter misstänkta aktiviteter och tillhandahåller åtgärdsinriktade säkerhetsaviseringar om avvikande åtkomstmönster för databaser. De här aviseringarna ger information om misstänkt aktivitet och rekommenderade åtgärder för att undersöka och minimera hotet.


### <a name="support-for-custom-policies-preview"></a>Stöd för anpassade principer (förhandsversion)

Azure Security Center stöder nu anpassade principer (i förhandsversion).

Våra kunder har försökt utöka sina nuvarande säkerhetsutvärderingar i Security Center med sina egna säkerhetsutvärderingar baserat på principer som de skapar i Azure Policy. Med stöd för anpassade principer är detta nu möjligt.

Dessa nya principer kommer att ingå i Security Center, Säkerhetspoäng och instrumentpanelen för regelefterlevnadsstandarder. Med stöd för anpassade principer kan du nu skapa ett anpassat initiativ i Azure Policy och sedan lägga till det som en princip i Security Center och visualisera det som en rekommendation.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Utöka Azure Security Center täckning med plattform för communityn och partner

Använd Security Center att ta emot rekommendationer inte bara från Microsoft utan även från befintliga lösningar från partner som Check Point, Tenable och CyberArk. Många fler integreringar kommer.  Security Center:s enkla onboarding-flöde kan ansluta dina befintliga lösningar till Security Center, så att du kan visa dina rekommendationer om säkerhetsstatus på en enda plats, köra enhetliga rapporter och utnyttja alla Security Center:s funktioner mot både inbyggda rekommendationer och partnerrekommendationer. Du kan också exportera Security Center rekommendationer till partnerprodukter.

[Läs mer om Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Avancerad integrering med export av rekommendationer och aviseringar (förhandsversion)

För att aktivera scenarier på företagsnivå utöver Security Center är det nu möjligt att använda Security Center-aviseringar och rekommendationer på ytterligare platser förutom Azure Portal api:et. Dessa kan exporteras direkt till en händelsehubb och till Log Analytics-arbetsytor. Här är några arbetsflöden som du kan skapa runt dessa nya funktioner:

- Med export till Log Analytics-arbetsytan kan du skapa anpassade instrumentpaneler med Power BI.
- Med export till Event Hub kan du exportera Security Center-aviseringar och rekommendationer till SIEM från tredje part, till en lösning från tredje part i realtid eller Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Registrera de virtuella servrarna för att Security Center från Windows Admin Center (förhandsversion)

Windows Admin Center är en hanteringsportal för Windows-servrar som inte har distribuerats i Azure och som erbjuder flera Azure-hanteringsfunktioner, till exempel säkerhetskopiering och systemuppdateringar. Vi har nyligen lagt till en möjlighet att registrera dessa icke-Azure-servrar för att skyddas av ASC direkt från Windows Admin Center upplevelsen.

Med den här nya upplevelsen kommer användarna att registrera en WAC-server för Azure Security Center och aktivera visning av säkerhetsaviseringar och rekommendationer direkt i Windows Admin Center upplevelsen.


## <a name="september-2019"></a>September 2019

Uppdateringar i september är:

 - [Hantera regler med förbättringar av anpassningsbara programkontroller](#managing-rules-with-adaptive-application-controls-improvements)
 - [Kontrollera rekommendationen för containersäkerhet med hjälp av Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Hantera regler med förbättringar av anpassningsbara programkontroller

Hanteringen av regler för virtuella datorer med anpassningsbara programkontroller har förbättrats. Azure Security Center anpassningsbara programkontroller hjälper dig att styra vilka program som kan köras på dina virtuella datorer. Förutom en allmän förbättring av regelhantering kan du med en ny fördel styra vilka filtyper som ska skyddas när du lägger till en ny regel.

[Läs mer om anpassningsbara programkontroller.](security-center-adaptive-application.md)


### <a name="control-container-security-recommendation-using-azure-policy"></a>Kontrollera säkerhetsrekommendation för containrar med Azure Policy

Azure Security Center rekommendation att åtgärda sårbarheter i containersäkerheten kan nu aktiveras eller inaktiveras via Azure Policy.

Om du vill visa dina aktiverade säkerhetsprinciper Security Center du sidan Säkerhetsprincip.


## <a name="august-2019"></a>Augusti 2019

Uppdateringar i augusti omfattar:

 - [Jit-åtkomst till virtuella just-in-time-datorer för Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Reparation med ett enda klick för att förbättra din säkerhetsstatus (förhandsversion)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Hantering av flera klienter](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Jit-åtkomst till virtuella just-in-time-datorer för Azure Firewall 

JIT-åtkomst (Just-in-time) för virtuella datorer Azure Firewall nu allmänt tillgänglig. Använd den för att Azure Firewall dina skyddade miljöer utöver dina NSG-skyddade miljöer.

JIT VM-åtkomst minskar exponeringen för nätverksvolymetriska attacker genom att ge kontrollerad åtkomst till virtuella datorer endast när det behövs, med hjälp av din NSG Azure Firewall regler.

När du aktiverar JIT för dina virtuella datorer skapar du en princip som bestämmer vilka portar som ska skyddas, hur länge portarna ska vara öppna och godkända IP-adresser som portarna kan nås från. Den här principen hjälper dig att hålla koll på vad användarna kan göra när de begär åtkomst.

Begäranden loggas i Azure-aktivitetsloggen så att du enkelt kan övervaka och granska åtkomst. Just-in-time-sidan hjälper dig också att snabbt identifiera befintliga virtuella datorer som har JIT aktiverat och virtuella datorer där JIT rekommenderas.

[Läs mer om Azure Firewall](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Reparation med ett enda klick för att förbättra din säkerhetsstatus (förhandsversion)

Säkerhetspoäng är ett verktyg som hjälper dig att utvärdera din arbetsbelastnings säkerhetsstatus. Den granskar dina säkerhetsrekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer du bör utföra först. Detta hjälper dig att hitta de allvarligaste säkerhetsriskerna för att prioritera undersökningen.

För att förenkla reparationen av felaktiga säkerhetskonfigurationer och hjälpa dig att snabbt förbättra dina säkerhetspoäng har vi lagt till en ny funktion som gör att du kan åtgärda en rekommendation för en stor mängd resurser med ett enda klick.

Med den här åtgärden kan du välja de resurser som du vill tillämpa reparationen på och starta en reparationsåtgärd som konfigurerar inställningen för din räkning.

Se vilka rekommendationer som har snabbkorrigering aktiverat i [referensguiden för säkerhetsrekommendationer.](recommendations-reference.md)


### <a name="cross-tenant-management"></a>Hantering av flera klienter

Security Center stöder nu hanteringsscenarier mellan klientorganisationen som en del av Azure Lighthouse. På så sätt kan du få insyn i och hantera säkerhetspositionen för flera klienter i Security Center. 

[Läs mer om hanteringsupplevelser mellan klienter.](security-center-cross-tenant-management.md)


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Uppdateringar av nätverksrekommendationer

Azure Security Center (ASC) har lanserat nya nätverksrekommendationer och förbättrat några befintliga. Med hjälp av Security Center nu ännu bättre nätverksskydd för dina resurser. 

[Läs mer om nätverksrekommendationer.](recommendations-reference.md#recs-networking)


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Anpassningsbar nätverkshärdning – allmänt tillgängligt

En av de största angreppsytorna för arbetsbelastningar som körs i det offentliga molnet är anslutningar till och från det offentliga Internet. Våra kunder har svårt att veta vilka regler för nätverkssäkerhetsgrupp (NSG) som ska finnas för att se till att Azure-arbetsbelastningar endast är tillgängliga för nödvändiga källintervall. Med den här funktionen Security Center nätverkstrafik och anslutningsmönster för Azure-arbetsbelastningar och tillhandahåller NSG-regelrekommendationer för Internetuppkopplade virtuella datorer. Detta hjälper våra kunder att bättre konfigurera sina principer för nätverksåtkomst och begränsa deras exponering för attacker. 

[Läs mer om anpassningsbar nätverkshärdning](security-center-adaptive-network-hardening.md).