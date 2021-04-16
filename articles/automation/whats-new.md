---
title: Vad är nytt i Azure Automation
description: Betydande uppdateringar av Azure Automation uppdateras varje månad.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531071"
---
# <a name="whats-new-in-azure-automation"></a>Vad är nytt i Azure Automation?

Azure Automation får kontinuerligt förbättringar. Den här artikeln innehåller information om hur du håller dig uppdaterad med den senaste utvecklingen:

- De senaste versionerna
- Kända problem
- Felkorrigeringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet.

## <a name="march-2021"></a>Mars 2021

### <a name="new-azure-automation-built-in-policies"></a>Nya Azure Automation inbyggda principer

**Typ:** Ny funktion

Azure Automation har lagt till 5 nya inbyggda principer:

- Automation-konton bör inaktivera offentlig nätverksåtkomst,
- Azure Automation bör använda kund hanterade nycklar för att kryptera vilodata
- Konfigurera Azure Automation-konton för att inaktivera åtkomst till offentligt nätverk
- Konfigurera privata slutpunktsanslutningar på Azure Automation konton
- Privata slutpunktsanslutningar på Automation-konton ska vara aktiverade.

Mer [information finns i](./policy-reference.md) referensartikeln om policyer.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Stöd för Automatisering och State Configuration deklarerad GA i Indien, södra

**Typ:** Ny funktion

Använd funktioner för processautomatisering och tillståndskonfiguration i Indien, södra. Mer information [finns](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) i tillkännagivandet.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Stöd för Automatisering och State Configuration deklarerad GA i Storbritannien, västra

**Typ:** Ny funktion

Använd funktioner för processautomatisering och tillståndskonfiguration i Storbritannien, västra. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Stöd för Automatisering och State Configuration ga-deklarerad i Förenade Arabemiraten, centrala

**Typ:** Ny funktion

Använd funktioner för processautomatisering och tillståndskonfiguration i Förenade Arabemiraten, centrala. Mer information [finns](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) i tillkännagivandet.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Stöd för Automation och State Configuration i Australien, centrala 2, Västra Tyskland och Frankrike, södra

**Typ:** Ny funktion

Se mer information på [sidan Datahemhemlighet](https://azure.microsoft.com/global-infrastructure/data-residency/) genom att välja geografi för varje region.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Nya skript har lagts till för att installera Hybrid Worker i Windows och Linux

**Typ:** Ny funktion

Två nya skript har lagts till i Azure Automation [GitHub-lagringsplatsen](https://github.com/azureautomation) som adresserar ett av Azure Automation:s viktiga scenarier för att konfigurera en Hybrid Runbook Worker på en Windows- eller Linux-dator. Skriptet skapar en ny virtuell dator eller använder en befintlig, skapar en Log Analytics-arbetsyta vid behov, installerar Log Analytics-agenten för Windows eller Log Analytics-agenten för Linux och registrerar datorn på Log Analytics-arbetsytan. Windows-skriptet heter **Create Automation Windows HybridWorker** och Linux-skriptet är Create Automation Linux **HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Anropa runbook via en Azure Resource Manager-mallwebhook

**Typ:** Ny funktion

Mer [information finns i Använda en webhook från en ARM-mall.](./automation-webhooks.md#use-a-webhook-from-an-arm-template)

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Uppdateringshantering stöder nu Centos 8.x, Red Hat Enterprise Linux Server 8.x och SUSE Linux Enterprise Server 15

**Typ:** Ny funktion

Mer information [finns i den](./update-management/overview.md#supported-operating-systems) fullständiga listan över Linux-operativsystem som stöds.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Stöd för datahemlighet i regionen för Brasilien, södra och Asien, sydöstra 

**Typ:** Ny funktion

I alla regioner utom Brasilien, södra och Sydostasien lagras Azure Automation data i en annan region (länkad Azure-region) för att tillhandahålla affärskontinuum och haveriberedskap (BCDR). Endast för Brasilien och Sydostasien, lagrar vi nu Azure Automation data i samma region för att tillgodose kraven på datahemhemlighet för dessa regioner. Se [Geo-replikering i Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation) mer information.

## <a name="february-2021"></a>Februari 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Stöd för automatisering och State Configuration ga-deklarerad i Japan, västra

**Typ:** Ny funktion

Automation-konto och State Configuration tillgänglighet i regionen Japan, västra. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Introducerade anpassad Azure Policy för att framtvinga runbook-körning på Hybrid Worker

**Skriv :** Ny funktion

Du kan använda den nya Azure Policy för att tillåta att jobb, webhooks och jobbscheman bara körs på Hybrid Worker grupper.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Uppdateringshantering tillgänglighet i regionerna USA, östra, Frankrike, centrala och Europa, norra

**Typ:** Ny funktion

Automation Uppdateringshantering är tillgänglig i regionerna USA, östra, Frankrike, centrala och Europa, norra. Uppdateringar [av dokumentationen som återspeglar](how-to/region-mappings.md) den här ändringen finns i Mappning av regioner som stöds.

## <a name="january-2021"></a>Januari 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Stöd för Automatisering och State Configuration deklarerad GA i Schweiz, västra

**Typ:** Ny funktion

Automation-konto State Configuration tillgänglighet i regionen Schweiz, västra. Mer information finns i [tillkännagivandet](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Python 3-skript har lagts till för att importera en modul med flera beroenden

**Typ:** Ny funktion

Skriptet är tillgängligt för nedladdning från vår [GitHub-lagringsplats.](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py) 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Hybrid Runbook Worker stöd för Centos 8.x/RHEL 8.x/SLES 15

**Typ.** Ny funktion

Funktionen Hybrid Runbook Worker har stöd för distributionerna CentOS 8.x, REHL 8.x och SLES 15 för endast processautomatisering i Hybrid Runbook Workers. Uppdateringar [av dokumentationen som återspeglar](automation-linux-hrw-install.md#supported-linux-operating-systems) dessa ändringar finns i Operativsystem som stöds.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Uppdateringshantering och Ändringsspårning tillgänglighet i regionerna Australien, östra, Asien, östra, USA, västra och USA, centrala

**Typ:** Ny funktion

Automation-konto, Ändringsspårning och inventering och Uppdateringshantering är tillgängliga i regionerna Australien, östra, Asien, östra, USA, västra och USA, centrala. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Introducerade offentlig förhandsversion av Python 3-runbooks i US Government-molnet

**Typ:** Nya funktioner Azure Automation offentlig förhandsversion av stöd för Python 3-moln och hybridkörning av runbooks i us government-molnregioner. Mer information finns i [tillkännagivandet](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation har flyttats från TechNet Script Center till GitHub

**Typ:** Planera för förändring

TechNet Script Center går i pension och alla runbooks som finns i Runbook-galleriet har flyttats till [vår Automation GitHub-organisation](https://github.com/azureautomation). Mer information finns i Azure Automation [runbooks flyttar till GitHub.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="december-2020"></a>December 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation och Uppdateringshantering Private Link GA

**Typ:** Ny funktion

Azure Automation och Uppdateringshantering tillkännagivit som GA för globala Azure- och Government-moln. Azure Automation har Private Link stöd för säker körning av en runbook på en Hybrid Worker-roll, med hjälp av Uppdateringshantering för att korrigera datorer, anrop av en runbook via en webhook och användning av State Configuration-tjänsten för att skydda dina datorer. Mer information finns i [Azure Automation Private Link support](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation klassificerat som grade-C certified on Accessibility

**Typ:** Ny funktion

Hjälpmedelsfunktionerna i Microsofts produkter hjälper myndigheter att hantera globala tillgänglighetskrav. På [bloggmeddelandesidan](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) söker du efter **Azure Automation** för att läsa rapporten om tillgänglighetsöverensstämmelse för Automation-tjänsten.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Stöd för Automation och State Configuration GA i Förenade Arabemiraten, norra

**Typ:** Ny funktion

Automation-konto och State Configuration tillgänglighet i Förenade Arabemiraten, norra. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Stöd för Automatisering och State Configuration GA i Tyskland, västra centrala

**Typ:** Ny funktion

Automation-konto och State Configuration tillgänglighet i regionen Tyskland, västra. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>DSC-stöd för Oracle 6 och 7

**Typ:** Ny funktion

Hantera Oracle Linux 6 och 7 datorer med Automation State Configuration. Uppdateringar [av dokumentationen som återspeglar dessa](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) ändringar finns i Linux-distributioner som stöds.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Offentlig förhandsversion för Python3-runbooks i Automation

**Typ:** Ny funktion

Azure Automation stöder nu körning av Python 3-moln- och hybrid-runbooks i offentlig förhandsversion i alla regioner i det globala Azure-molnet. Se [announcement](( för https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) mer information.

## <a name="november-2020"></a>November 2020

### <a name="dsc-support-for-ubuntu-1804"></a>DSC-stöd för Ubuntu 18.04

**Typ:** Ny funktion

Uppdateringar [av dokumentationen som återspeglar dessa](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) ändringar finns i Linux-distributioner som stöds.

## <a name="october-2020"></a>Oktober 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Stöd för Automation och State Configuration GA i Schweiz, norra

**Typ:** Ny funktion

Automation-konto och State Configuration tillgänglighet i Schweiz, norra. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Stöd för Automation och State Configuration GA i Brasilien, sydöstra

**Typ:** Ny funktion

Automation-konto State Configuration tillgänglighet i Brasilien, sydöstra. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Uppdateringshantering tillgänglighet i USA, södra centrala

**Typ:** Ny funktion

Azure Automation har uppdaterats för att stödja Uppdateringshantering i regionen USA, södra centrala. Uppdateringar [av dokumentationen som återspeglar](how-to/region-mappings.md#supported-mappings) den här ändringen finns i Mappning av regioner som stöds.

## <a name="september-2020"></a>September 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Starta/stoppa virtuella datorer när de inte används har uppdaterats för att använda Azure Az-moduler

**Typ:** Ny funktion

Starta/stoppa VM-runbooks har uppdaterats för att använda Az-moduler i stället för Azure Resource Manager moduler. I [Starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management.md) översikten finns uppdateringar av dokumentationen som återspeglar dessa ändringar.

## <a name="august-2020"></a>Augusti 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Publicerade DSC-tillägget för att stödja Azure Arc

**Typ:** Ny funktion

Använd Azure Automation State Configuration att lagra konfigurationer centralt och upprätthålla det önskade tillståndet för hybridanslutna datorer som är aktiverade via de Azure Arc-aktiverade servrarna DSC VM-tillägget. Mer information finns i Översikt [över VM-tillägg för Arc-aktiverade servrar.](../azure-arc/servers/manage-vm-extensions.md)

### <a name="july-2020"></a>Juli 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Den offentliga förhandsversionen av Private Link stöd i Automation

**Typ:** Ny funktion

Använd Azure Private Link för att på ett säkert sätt ansluta virtuella nätverk till Azure Automation med privata slutpunkter. Mer information finns i [tillkännagivandet](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Hybrid Runbook Worker stöd för Windows Server 2008 R2

**Typ:** Ny funktion

Automation Hybrid Runbook Worker har stöd för operativsystemet Windows Server 2008 R2. Uppdateringar [av dokumentationen som återspeglar](automation-windows-hrw-install.md#supported-windows-operating-system) dessa ändringar finns i Operativsystem som stöds.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Uppdateringshantering stöd för Windows Server 2008 R2

**Typ:** Ny funktion

Uppdateringshantering stöder utvärdering och korrigering av operativsystemet Windows Server 2008 R2. Uppdateringar [av dokumentationen som återspeglar](update-management/overview.md#clients) dessa ändringar finns i Operativsystem som stöds.

### <a name="automation-diagnostic-logs-schema-update"></a>Schemauppdatering för automationsdiagnostikloggar

**Typ:** Ny funktion

Ändrade schemat för Azure Automation loggdata i Log Analytics-tjänsten. Mer information finns i Vidarebefordra [Azure Automation jobbdata till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse har stöd för Automation Uppdateringshantering

**Typ:** Ny funktion

Azure Lighthouse möjliggör delegerad resurshantering med Uppdateringshantering för tjänstleverantörer och kunder. Läs mer [här](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Juni 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatisering och Uppdateringshantering tillgänglighet i den US Gov, Arizona regionen

**Typ:** Ny funktion

Automation-konto och Uppdateringshantering är tillgängliga i US Gov, Arizona. Mer information finns i [meddelande](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Hybrid Runbook Worker onboarding-skript som har uppdaterats för att använda Az-moduler

**Typ:** Ny funktion

Den New-OnPremiseHybridWorker runbooken har uppdaterats för att stödja Az-moduler. Mer information finns i paketet i [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Uppdateringshantering tillgänglighet i Kina, östra 2

**Typ:** Ny funktion

Azure Automation regionsmappning har uppdaterats för att stödja Uppdateringshantering funktion i Kina, östra 2 region. Uppdateringar [av dokumentationen som återspeglar](how-to/region-mappings.md#supported-mappings) den här ändringen finns i Mappning av regioner som stöds.

## <a name="may-2020"></a>Maj 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Uppdaterade AUTOMATION-tjänstens DNS-poster från regionspecifika till Automation-kontospecifika URL:er

**Typ:** Ny funktion

Azure Automation DNS-poster har uppdaterats för att stödja privata länkar. Mer information finns i [tillkännagivandet](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Funktioner har lagts till för att hålla Automation-runbooks och DSC-skript krypterade som standard

**Typ:** Ny funktion

Förutom att förbättra säkerheten för tillgångar krypteras även runbooks och DSC-skript för att förbättra Azure Automation säkerheten.

## <a name="april-2020"></a>April 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Tillbakadragande av Automation-bevakaruppgiften

**Typ:** Planera för förändring

Azure Logic Apps är nu det rekommenderade och stödda sättet att övervaka händelser, schemalägga återkommande aktiviteter och utlösa åtgärder. Det kommer inte att göras några ytterligare investeringar i Watcher-uppgiftsfunktioner. Mer information finns i Schemalägga [och köra återkommande automatiserade uppgifter med Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Mars 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Stöd för beräkningsisolering på effektnivå 5 (IL5) i azures kommersiella moln och myndighetsmoln

**Typ:**

Azure Automation Hybrid Runbook Worker kan användas i Azure Government för att stödja arbetsbelastningar på effektnivå 5. Mer information finns i vår [dokumentation.](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)

## <a name="february-2020"></a>Februari 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Stöd för tjänsttaggar för virtuella Azure-nätverk har introducerats

**Typ:** Ny funktion

Automatiseringsstöd för tjänsttaggar tillåter eller nekar trafik för Automation-tjänsten för en delmängd av scenarier. Mer information finns i [dokumentationen](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Aktivera TLS 1.2-stöd för Azure Automation tjänsten

**Typ:** Planera för förändring

Azure Automation fullständigt stöd för TLS 1.2 och alla klientanrop (via webhooks, DSC-noder och Hybrid Worker). TLS 1.1 och TLS 1.0 stöds fortfarande för bakåtkompatibilitet med äldre klienter tills kunderna standardiserar och migrerar fullständigt till TLS 1.2.

## <a name="january-2020"></a>Januari 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Den allmänt använda förhandsversionen av kund hanterade nycklar för Azure Automation

**Typ:** Ny funktion

Kunder kan hantera och skydda kryptering av Azure Automation med sina egna hanterade nycklar. Mer information finns i [Använda kund hanterade nycklar.](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Rest-API:er för Azure Service Management (ASM) för Azure Automation

**Typ:** Pension

REST-API:er för Azure Service Management (ASM) för Azure Automation kommer att dras tillbaka och stöds inte längre efter den 30 januari 2020. Mer information finns i [tillkännagivandet](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Nästa steg

Om du vill bidra till att Azure Automation dokumentation kan du läsa [Deltagarguide för Docs.](/contribute/)
