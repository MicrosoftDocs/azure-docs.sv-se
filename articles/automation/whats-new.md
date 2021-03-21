---
title: Vad är nytt i Azure Automation
description: Viktiga uppdateringar av Azure Automation uppdateras varje månad.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 899249c98c3ce0fdf061b1e689182f71c120aa13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729409"
---
# <a name="whats-new-in-azure-automation"></a>Vad är nytt i Azure Automation?

Azure Automation tar emot förbättringar regelbundet. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar

Den här sidan uppdateras varje månad, så du kan uppdatera den regelbundet.

## <a name="february-2021"></a>Februari 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Stöd för automatisering och tillstånds konfiguration som har deklarerats som GA i Japan, väst

**Typ:** Ny funktion

Automation-konto och tillstånds konfigurations tillgänglighet i regionen Japan, västra. Mer information hittar du i [meddelande](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Introducerade anpassad Azure Policy kompatibilitet för att verkställa Runbook-körning på Hybrid Worker

**Typ:** Ny funktion

Du kan använda regeln ny Azure Policy för att skapa jobb, Webhooks och jobb scheman som bara körs på Hybrid Worker grupper.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Uppdateringshantering tillgänglighet i regionerna östra USA, Frankrike, centrala och Europa, norra

**Typ:** Ny funktion

Automation Uppdateringshantering-funktionen finns i regionerna östra USA, centrala Frankrike och Europa, norra. Se den [region mappning som stöds](how-to/region-mappings.md) för uppdateringar av dokumentationen som reflekterar den här ändringen.

## <a name="january-2021"></a>Januari 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Stöd för automatisering och tillstånds konfiguration deklarerad GA i Schweiz, västra

**Typ:** Ny funktion

Automation-konto och tillstånds konfigurations tillgänglighet i Schweiz, västra regionen. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Har lagt till python 3-skript för att importera modulen med flera beroenden

**Typ:** Ny funktion

Skriptet är tillgängligt för nedladdning från vår [GitHub-lagringsplats](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py). 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Hybrid Runbook Worker roll stöd för CentOS 8. x/RHEL 8. x/SLES 15

**Bastyp.** Ny funktion

Hybrid Runbook Worker-funktionen stöder CentOS 8. x, REHL 8. x och SLES 15 distributioner för enbart process automatisering i hybrid Runbook Worker.  Se [vilka operativ system som stöds](automation-linux-hrw-install.md#supported-linux-operating-systems) för uppdateringar av dokumentationen för att avspegla dessa ändringar.

### <a name="update-management--change-tracking-availability-in-australia-east-east-asia-west-us--central-us-regions"></a>Uppdateringshantering & Ändringsspårning tillgänglighet i östra Australien, Asien, östra, västra USA & Central USA-regioner

**Typ:** Ny funktion

Automation-konto, Ändringsspårning och inventering och Uppdateringshantering är tillgängliga i östra Australien, Asien, östra, västra USA & centrala USA-regioner. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Lanserade en offentlig för hands version av python 3-Runbooks i det amerikanska myndighets molnet

**Typ:** Med den nya funktionen Azure Automation införs stöd för offentliga för hands versioner av python 3 Cloud och hybrid Run Book körning i amerikanska myndigheter i molnet.  Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation runbooks som har flyttats från TechNet Script Center till GitHub

**Typ:** Planera för ändring

TechNets skript Center tas ur bruk och alla Runbooks som finns i Runbook-galleriet har flyttats till vår [Automation GitHub-organisation](https://github.com/azureautomation). Mer information finns [i Azure Automation runbooks som flyttas till GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="december-2020"></a>December 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation och Uppdateringshantering privat länk GA

**Typ:** Ny funktion

Azure Automation och Uppdateringshantering support lanseras som GA för Azures globala och offentliga moln. Azure Automation aktiverat stöd för privat länk för säker körning av en Runbook i en hybrid Worker-roll, med Uppdateringshantering för att korrigera datorer, anropa en Runbook via en webhook och använda tillstånds konfigurations tjänsten för att hålla dina datorer klagomål. Mer information finns i [stöd för Azure Automation privat länk](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation klassificeras som klass-C-certifierad för hjälpmedel

**Typ:** Ny funktion

Hjälpmedels funktionerna i Microsofts produkter hjälper dig att hantera globala tillgänglighets krav. På sidan [blogg meddelande](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) söker du efter **Azure Automation** för att läsa rapporten om tillgänglighets överensstämmelse för Automation-tjänsten.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Stöd för automatisering och tillstånds konfiguration GA i Förenade Arabemiraten Nord

**Typ:** Ny funktion

Tillgänglighet för Automation-konto och tillstånds konfiguration i regionen Förenade Arabemiraten Nord. Mer information hittar du i [meddelande](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Stöd för automatisering och tillstånds konfiguration GA i Tyskland, västra centrala

**Typ:** Ny funktion

Automation-konto och tillstånds konfigurations tillgänglighet i Tyskland västra region. Mer information hittar du i [meddelande](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>DSC-stöd för Oracle 6 och 7

**Typ:** Ny funktion

Hantera Oracle Linux 6-och 7-datorer med automatiserings tillstånds konfiguration. Se [Linux-distributioner som stöds](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) för uppdateringar i dokumentationen för att avspegla dessa ändringar.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Offentlig för hands version för python3-Runbooks i Automation

**Typ:** Ny funktion

Azure Automation stöder nu python 3 Cloud & hybrid Runbook-körning i offentlig för hands version i alla regioner i Azure Global Cloud. Se [meddelande] (( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) om du vill ha mer information.

## <a name="november-2020"></a>November 2020

### <a name="dsc-support-for-ubuntu-1804"></a>DSC-stöd för Ubuntu 18,04

**Typ:** Ny funktion

Se [Linux-distributioner som stöds](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) för uppdateringar i dokumentationen som återspeglar ändringarna.

## <a name="october-2020"></a>Oktober 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Stöd för automatisering och tillstånds konfiguration GA i Schweiz, norra

**Typ:** Ny funktion

Automation-konto och tillstånds konfigurations tillgänglighet i Schweiz, norra. Mer information hittar du i [meddelande](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Stöd för automatisering och tillstånds konfiguration GA i södra Brasilien, sydöstra

**Typ:** Ny funktion

Automation-konto och tillstånds konfigurations tillgänglighet i södra Brasilien. Mer information hittar du i [meddelande](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Uppdateringshantering tillgänglighet i södra centrala USA

**Typ:** Ny funktion

Azure Automation regions mappning har uppdaterats för att stödja Uppdateringshantering funktionen i södra centrala USA-regionen. Se den [region mappning som stöds](how-to/region-mappings.md#supported-mappings) för uppdateringar av dokumentationen för att avspegla den här ändringen.

## <a name="september-2020"></a>September 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Starta/stoppa virtuella datorer när de inte används Runbooks som har uppdaterats för att använda Azure AZ-moduler

**Typ:** Ny funktion

Starta/stoppa VM-Runbooks har uppdaterats för att använda AZ-moduler i stället för Azure Resource Manager moduler. Se [Starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management.md) översikt för uppdateringar av dokumentationen för att avspegla dessa ändringar.

## <a name="august-2020"></a>Augusti 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Publicerat DSC-tillägget för att stödja Azure-bågen

**Typ:** Ny funktion

Använd Azure Automation tillstånds konfiguration för att centralt lagra konfigurationer och upprätthålla det önskade läget för Hybrid anslutna datorer som är aktiverade via Azure Arc-aktiverade servrar DSC VM-tillägg. Mer information finns i [Översikt över virtuella Arc-aktiverade servrar för VM-tillägg](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Juli 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Introducerade offentlig för hands version av stöd för privata länkar i Automation

**Typ:** Ny funktion

Använd Azures privata länk för att ansluta virtuella nätverk på ett säkert sätt till Azure Automation med hjälp av privata slut punkter. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Hybrid Runbook Worker stöd för Windows Server 2008 R2

**Typ:** Ny funktion

Automation Hybrid Runbook Worker stöder operativ systemet Windows Server 2008 R2. Se [vilka operativ system som stöds](automation-windows-hrw-install.md#supported-windows-operating-system) för uppdateringar av dokumentationen för att avspegla dessa ändringar.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Uppdateringshantering stöd för Windows Server 2008 R2

**Typ:** Ny funktion

Uppdateringshantering stöder utvärdering och korrigering av operativ systemet Windows Server 2008 R2. Se [vilka operativ system som stöds](update-management/overview.md#clients) för uppdateringar av dokumentationen för att avspegla dessa ändringar.

### <a name="automation-diagnostic-logs-schema-update"></a>Uppdatering av Automation-diagnostikloggar schema uppdatering

**Typ:** Ny funktion

Schemat för Azure Automation loggdata har ändrats i Log Analyticss tjänsten. Mer information finns i [vidarebefordra Azure Automation jobb data till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse stöder Automation Uppdateringshantering

**Typ:** Ny funktion

Azure Lighthouse möjliggör delegerad resurs hantering med Uppdateringshantering för tjänste leverantörer och kunder. Läs mer [här](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Juni 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatisering och Uppdateringshantering tillgänglighet i US Gov, Arizona region

**Typ:** Ny funktion

Automation-konto och Uppdateringshantering är tillgängliga i US Gov, Arizona. Mer information finns i [meddelande](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Hybrid Runbook Worker onboarding-skriptet har uppdaterats för användning av AZ-moduler

**Typ:** Ny funktion

New-OnPremiseHybridWorker Runbook har uppdaterats till att stödja AZ-moduler. Mer information finns i paketet i [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Uppdateringshantering tillgänglighet i Kina, östra 2

**Typ:** Ny funktion

Azure Automation regions mappning har uppdaterats för att stödja Uppdateringshantering funktionen i Kina, östra 2-regionen. Se den [region mappning som stöds](how-to/region-mappings.md#supported-mappings) för uppdateringar av dokumentationen för att avspegla den här ändringen.

## <a name="may-2020"></a>Maj 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Uppdaterade Automation service DNS-poster från landsspecifika till Automation-användarspecifika URL: er

**Typ:** Ny funktion

Azure Automation DNS-poster har uppdaterats för att stödja privata länkar. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>Extra kapacitet för att hålla Automation-runbooks & DSC-skript som är krypterade som standard

**Typ:** Ny funktion

Förutom att förbättra säkerheten för till gångar krypteras även Runbooks & DSC-skript för att förbättra Azure Automation säkerhet.

## <a name="april-2020"></a>April 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Dra tillbaka av Automation Watcher-uppgiften

**Typ:** Planera för ändring

Azure Logic Apps är nu det rekommenderade och stödda sättet att övervaka för händelser, schemalägga återkommande uppgifter och utlösa åtgärder. Det kommer inte att finnas fler investeringar i bevakad aktivitets funktion. Mer information finns i [schemalägga och köra återkommande automatiserade uppgifter med Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Mars 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Stöd för beräknings isolering på effekt nivå 5 (IL5) i Azures moln för kommersiella och myndigheter

**Bastyp**

Azure Automation Hybrid Runbook Worker kan användas i Azure Government för att stödja påverkan på nivå 5. Mer information finns i vår [dokumentation](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Februari 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Stöd för Azure Virtual Network Service-Taggar har introducerats

**Typ:** Ny funktion

Automation-stöd för service märken tillåter eller nekar trafiken för Automation-tjänsten för en delmängd av scenarier. Mer information finns i [dokumentationen](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Aktivera TLS 1,2-stöd för Azure Automation tjänst

**Typ:** Planera för ändring

Azure Automation fullständigt stöd för TLS 1,2 och alla klient anrop (via Webhooks, DSC-noder och hybrid Worker). TLS 1,1 och TLS 1,0 stöds fortfarande för bakåtkompatibilitet med äldre klienter tills kunderna standardiseras och migreras fullständigt till TLS 1,2.

## <a name="january-2020"></a>Januari 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Introducerade offentlig för hands version av Kundhanterade nycklar för Azure Automation

**Typ:** Ny funktion

Kunder kan hantera och säkra kryptering av Azure Automation till gångar med sina egna hanterade nycklar. Mer information finns i [använda Kundhanterade nycklar](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Dra tillbaka REST-API: er för Azure Service Management (ASM) för Azure Automation

**Typ:** Pensions

REST-API: er för Azure Service Management (ASM) för Azure Automation kommer att dras tillbaka och stöds inte längre efter den 30 januari 2020. Mer information finns i [meddelandet](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Nästa steg

Om du vill bidra till Azure Automation-dokumentationen kan du läsa [guiden dokument bidrags givare](/contribute/).
