---
title: Azures säkerhets bas linje för Azure Automation
description: Azures säkerhets bas linje för Automation
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 79282e99894bccad4f3c265cdffe4bee2e465950
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052761"
---
# <a name="azure-security-baseline-for-automation"></a>Azures säkerhets bas linje för Automation

Azures säkerhets bas linje för Automation innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Azure Automation kontot har ännu inte stöd för en privat Azure-länk för att begränsa åtkomsten till tjänsten via privata slut punkter. Runbooks som autentiserar och kör mot resurser i Azure körs på en Azure-Sandbox och utnyttjar delade server resurser, som Microsoft ansvarar för att isolera från varandra. nätverket är obegränsat och kan komma åt offentliga resurser. Azure Automation har för närvarande inte integrering med virtuella nätverk för privata nätverk Utöver stödet för Hybrid Runbook Worker. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

För att få ytterligare isolering för dina runbooks kan du använda hybrid Runbook Worker som körs på virtuella Azure-datorer. När du skapar en virtuell Azure-dator måste du skapa ett virtuellt nätverk (VNet) eller använda ett befintligt VNet och konfigurera dina virtuella datorer med ett undernät. Se till att alla distribuerade undernät har en nätverks säkerhets grupp som tillämpas med nätverks åtkomst kontroller som är speciella för dina programs betrodda portar och källor. Information om tjänstspecifika krav finns i säkerhets rekommendationer för den aktuella tjänsten.

Alternativt, om du har ett speciellt krav, kan Azure-brandväggen också användas för att uppfylla den.

* [Virtuella nätverk och virtuella datorer i Azure](../virtual-machines/network-overview.md)

* [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Körnings miljö för Runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Azure Automation för närvarande inte har någon integrering av virtuella nätverk för privata nätverk Utöver stödet för Hybrid Runbook Worker. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

Om du använder hybrid Runbook Worker som backas upp av virtuella Azure-datorer kontrollerar du att under nätet som innehåller dessa arbetare är aktiverade med en nätverks säkerhets grupp (NSG) och konfigurerar flödes loggar för att vidarebefordra loggar till ett lagrings konto för trafik granskning. Du kan också vidarebefordra NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Även om NSG-regler och användardefinierade vägar inte gäller för privata slut punkter stöds fortfarande flödes loggar och övervaknings information för utgående anslutningar och kan användas.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Azure Automation för närvarande inte har en virtuell nätverks integrering för privata nätverk utöver stöd för Hybrid Runbook Worker. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

Om du använder hybrid Runbook Worker som backas upp av virtuella Azure-datorer aktiverar du DDoS-standardskyddet (distributed denial of Service) på dina virtuella nätverk som fungerar som värd för dina hybrid Runbook Worker-angrepp mot DDoS-attacker. Genom att använda Azure Security Center integrerad Hot information kan du neka kommunikation med kända skadliga IP-adresser. Konfigurera Azure-brandväggen på var och en av Virtual Network segment, med hot information aktive rad, och konfigurera för att **Varna och neka** för skadlig nätverks trafik.

Du kan använda Azure Security Center just nu nätverks åtkomst för att begränsa exponeringen för virtuella Windows-datorer till godkända IP-adresser under en begränsad tids period. Använd också Azure Security Center anpassade nätverks härdnings rekommendationer för NSG-konfigurationer för att begränsa portar och käll-IP-adresser baserat på faktisk trafik och hot information.

* [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

* [Förstå Azure Security Center anpassad nätverks härdning](../security-center/security-center-adaptive-network-hardening.md)

* [Förstå Azure Security Center just-in-Time-nätverk Access Control](../security-center/security-center-just-in-time.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Azure Automation för närvarande inte har någon integrering av virtuella nätverk för privata nätverk Utöver stödet för Hybrid Runbook Worker, gäller inte den här kontrollen om du använder den färdiga tjänsten utan hybrid arbetare.

Om du använder hybrid Runbook Worker som backas upp av virtuella Azure-datorer kan du registrera NSG Flow-loggar i ett lagrings konto för att generera flödes poster för Azure-Virtual Machines som fungerar som Runbook Worker. När du undersöker avvikande aktivitet kan du aktivera Network Watcher paket avbildning så att nätverks trafiken kan granskas för ovanlig och oväntad aktivitet.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Azure Automation för närvarande inte har en virtuell nätverks integrering för privata nätverk utöver stöd för Hybrid Runbook Worker. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

Om du använder hybrid Runbook Worker som finns på Azure Virtual Machines kan du kombinera paket fångster som tillhandahålls av Network Watcher och ID-verktyg med öppen källkod för att utföra identifiering av nätverks intrång för ett brett utbud av hot mot dessa arbets datorer. Du kan också Distribuera Azure-brandväggen till Virtual Network segmenten efter behov, med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

* [Utför identifiering av nätverks intrång med Network Watcher och verktyg för öppen källkod](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS i Azure och som kräver åtkomst till dina Automation-resurser. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (till exempel GuestAndHybridManagement) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

* [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som används av Azure Automation med Azure policy.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resources Manager-mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition. Du kan använda skissen för nya prenumerationer och finjustera kontroll och hantering genom versions hantering.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

* [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för NSG: er och andra resurser som är relaterade till nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i dina nätverks resurser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska resurser sker.

* [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser. Du kan dock välja att hantera inställningarna för tidssynkronisering för alla hybrid Runbook-anställda som körs på virtuella Windows-datorer.

* [Så här konfigurerar du tidssynkronisering för Azure Compute-resurser](../virtual-machines/windows/time-sync.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: vidarebefordra loggdata till Azure Monitor loggar till sammanställda säkerhets data som genereras av Azure Automation resurser. I Azure Monitor ska du använda logg frågor för att söka efter och utföra analyser och använda Azure Storage konton för långsiktig lagring. Azure Automation kan skicka status för Runbook-jobb, jobb strömmar, konfigurations data för automatiserings tillstånd, uppdaterings hantering och ändrings spårning eller inventerings loggar till din Log Analytics-arbetsyta. Den här informationen visas i API för Azure Portal, Azure PowerShell och Azure Monitor loggar som gör det möjligt att utföra enkla undersökningar.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar](./automation-manage-send-joblogs-log-analytics.md)

* [Integrera DSC med Azure Monitor loggar](./automation-dsc-diagnostics.md)

* [Regioner som stöder länkade Log Analytics-arbetsytor](./how-to/region-mappings.md)

* [Fråga Uppdateringshantering loggar](./update-management/query-logs.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera Azure Monitor för åtkomst till dina gransknings-och aktivitets loggar som innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

* [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: när du använder Azure Automation med Runbook Worker för flera innehavare, gäller inte den här kontrollen och plattformen hanterar de underliggande virtuella datorerna.

När du använder funktionen Hybrid Runbook Worker kan Azure Security Center tillhandahålla säkerhets händelse logg övervakning för virtuella Windows-datorer. Om din organisation vill behålla säkerhets händelse logg data, kan den lagras inom en data insamlings nivå, där den kan frågas i Log Analytics. Det finns olika nivåer: minimalt, gemensamt och alla, som beskrivs i följande länk.

* [Konfigurera data insamlings nivå i Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

* [Ändra data lagrings perioden i Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Information om data kvarhållning för Automation-konton](./automation-managing-data.md#data-retention)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor logg frågor för att granska loggar och köra frågor om loggdata.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Förstå logg frågor i Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Security Center med Azure Monitor för övervakning och aviseringar om avvikande aktivitet i säkerhets loggar och händelser.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel.

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Avisering om Azure Monitor loggdata](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: när du använder Azure Automation med Runbook Worker med flera innehavare, är den här kontrollen inte tillämplig och plattformen hanterar de underliggande virtuella datorerna.

Men när du använder funktionen Hybrid Runbook Worker kan du använda Microsofts program mot skadlig kod för Azure Cloud Services och virtuella datorer. Konfigurera dina virtuella datorer att logga händelser till ett Azure Storage konto. Konfigurera en Log Analytics arbets yta för att mata in händelser från lagrings kontona och skapa aviseringar där det är lämpligt. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps".

* [Så här konfigurerar du Microsofts program mot skadlig kod för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

* [Aktivera övervakning på gästnivå för virtuella datorer](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part från Azure Marketplace för DNS-loggning av lösningar enligt organisationens behov.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: när du använder Azure Automation med Runbook Worker för flera innehavare, gäller inte den här kontrollen och plattformen hanterar de underliggande virtuella datorerna.

Men när du använder funktionen Hybrid Runbook Worker kan Azure Security Center tillhandahålla säkerhets händelse logg övervakning för virtuella Azure-datorer. Security Center etablerar Log Analytics-agenten på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om automatisk etablering är aktiverat. Eller så kan du installera agenten manuellt. Agenten gör det möjligt för processen att skapa händelse 4688 och fältet kommandorad i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av händelse logg och övervakas av Security Centers identifierings tjänster.

* [Datainsamling i Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Använd Azure Active Directory inbyggda administratörs roller som kan tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper. När du använder kör som-konton för Automation-konton för dina runbooks, ser du till att dessa tjänst huvud namn också spåras i inventeringen eftersom de ofta har förhöjd behörighet. Ta bort eventuella oanvända kör som-konton för att minimera din exponerade attack yta.

* [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

* [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](./delete-run-as-account.md)

* [Hantera ett Kör som-konto för Azure Automation](./manage-runas-account.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Automation kontot har inte begreppet standard lösen ord. Kunder är ansvariga för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord som körs ovanpå tjänsten eller dess hybrid Runbook Worker.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton. När du använder kör som-konton för Automation-konton för dina runbooks, ser du till att dessa tjänst huvud namn också spåras i inventeringen eftersom de ofta har förhöjd behörighet. Omfånget de här identiteterna med de minst privilegierade behörigheter som krävs för att dina runbooks ska kunna utföra sin automatiserade process. Ta bort eventuella oanvända kör som-konton för att minimera din exponerade attack yta.

Du kan också aktivera en just-in-Time/bara-tillräcklig-åtkomst genom att använda Azure AD Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure Resource Manager.

* [Läs mer om Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](./delete-run-as-account.md)

* [Hantera ett Kör som-konto för Azure Automation](./manage-runas-account.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: där det är möjligt kan du använda SSO med Azure Active Directory snarare än att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Azure Security Center identitets-och åtkomst hantering.

* [Enkel inloggning till program i Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

* [Använda Azure AD för att autentisera mot Azure](./automation-use-azure-ad.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure AD Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer för alla administrativa uppgifter

**Vägledning**: Använd Paw med Multi-Factor Authentication som kon figurer ATS för att logga in på och konfigurera Azure Automation konto resurser i produktions miljöer.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden. Om du vill kan kunden vidarebefordra Azure Security Center risk identifierings aviseringar till Azure Monitor och konfigurera anpassade aviseringar/aviseringar med hjälp av åtgärds grupper.

* [Förstå Azure Security Center risk identifieringar (misstänkt aktivitet)](../active-directory/identity-protection/overview-identity-protection.md)

* [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Konfigurera åtgärds grupper för anpassad avisering och avisering](../azure-monitor/platform/action-groups.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Vi rekommenderar att du använder namngivna platser för villkorlig åtkomst för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure AD som den centrala autentiserings-och auktoriserings systemet. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt. Om du använder hybrid Runbook Worker kan du använda hanterade identiteter i stället för kör som-konton för att möjliggöra mer sömlöst skyddade behörigheter.

* [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

* [Använd Runbook-autentisering med hanterade identiteter](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. När du använder kör som-konton för Automation-konton för dina runbooks ser du till att dessa tjänst huvud namn också spåras i inventeringen eftersom de ofta har förhöjd behörighet. Ta bort eventuella oanvända kör som-konton för att minimera din exponerade attack yta.

* [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

* [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

* [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](./delete-run-as-account.md)

* [Hantera ett Kör som-konto för Azure Automation](./manage-runas-account.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har till gång till inloggnings aktiviteter, gransknings-och risk händelse logg källor i Azure AD, vilket gör att du kan integrera med alla Siem/övervaknings verktyg.

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

* [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd funktioner i Azure AD-risk och identitets skydd för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter för din nätverks resurs. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

* [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: för Azure Automation-konton kan Microsoft Support komma åt plattforms resursens metadata under ett öppet support ärende utan användning av ett annat verktyg.

Men när du använder hybrid Runbook Worker som backas upp av virtuella Azure-datorer och en tredje part behöver åtkomst till kunddata (t. ex. under en supportbegäran) ska du använda Customer Lockbox (för hands version) för Azure Virtual Machines för att granska och godkänna eller avvisa förfrågningar om kund data åtkomst.

* [Förstå Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure Automation resurser som lagrar eller bearbetar känslig information.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Isolera miljöer genom att använda separata resurser för Automation-kontot. Resurser som hybrid Runbook Worker bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas inom en nätverks säkerhets grupp (NSG) eller Azure-brandvägg. För virtuella datorer som lagrar eller bearbetar känsliga data implementerar du principer och procedurer för att inaktivera dem när de inte används.

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Så här konfigurerar du aviseringen eller aviseringen och nekar med Azure-brandväggen](../firewall/threat-intel.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: när du använder hybrid Runbook Worker-funktionen kan du använda en lösning från tredje part från Azure Marketplace på nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar, samtidigt som du varnar information om information om säkerhets tekniker.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure-resurser i Azure Virtual Networks kan förhandla TLS 1,2 eller högre. Azure Automation fullständigt stöd för och framtvingar Transport Layer (TLS) 1,2 och alla klient anrop eller senare versioner för alla externa HTPS-slutpunkter (via Webhooks, DSC-noder, hybrid Runbook Worker).

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall.

* [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure Automation TLS 1,2-tvång](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: Använd ett externt identifierings verktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens teknik system, inklusive de som finns på plats eller på en fjärran sluten tjänst leverantör och uppdatera organisationens känsliga informations lager.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd Azures rollbaserad åtkomst kontroll (Azure RBAC) för att kontrol lera åtkomsten till Azure Automation resurser med hjälp av de inbyggda roll definitionerna, tilldela åtkomst för användare som har åtkomst till dina Automation-resurser efter en minst privilegie rad eller "just-tillräckligt" åtkomst modell. När du använder hybrid Runbook Worker kan du använda hanterade identiteter för de virtuella datorerna för att undvika att använda tjänstens huvud namn, när du använder både flera innehavare eller hybrid Runbook-arbetare för att se till att använda korrekt begränsade Azure RBAC-behörigheter på identiteten för Runbook Worker.

* [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Runbook-behörigheter för en Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [Hantera rollbehörigheter och säkerhet](./automation-role-based-access-control.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

Om du använder hybrid Runbook Worker som backas upp av virtuella Azure-datorer måste du använda en värdbaserade lösning för data förlust skydd från tredje part för att genomdriva åtkomst kontroller till dina värdbaserade Hybrid Runbook Worker virtuella datorer.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: använda Kundhanterade nycklar med Azure Automation. Azure Automation stöder användning av Kundhanterade nycklar för att kryptera alla "säkra till gångar", som: autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Använd krypterade variabler med dina runbooks för alla dina beständiga varierande söknings behov för att förhindra oavsiktlig exponering.

När du använder hybrid Runbook-arbetare, krypteras de virtuella diskarna på de virtuella datorerna i vila med hjälp av antingen kryptering på Server sidan eller med hjälp av Azure Disk Encryption (ADE). Azure Disk Encryption utnyttjar BitLocker-funktionen i Windows för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn. Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.

* [Kryptering på Server sidan av Azure Managed disks](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption för virtuella Windows-datorer](../virtual-machines/windows/disk-encryption-overview.md)

* [Användning av Kundhanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Hanterade variabler i Azure Automation](./shared-resources/variables.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i kritiska Azure-resurser som nätverks komponenter, Azure Automation konton och Runbooks.

* [Diagnostisk loggning för en nätverks säkerhets grupp](../private-link/private-link-overview.md#logging-and-monitoring)

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar på dina Azure-resurser

* [Säkerhetsrekommendationer i Azure Security Center](../security-center/security-center-recommendations.md)

* [Referens för Security Center rekommendation](../security-center/recommendations-reference.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

Om du använder hybrid Runbook Worker som backas upp av virtuella Azure-datorer kan du använda Azure Uppdateringshantering för att hantera uppdateringar och korrigeringar för dina virtuella datorer. Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att korrigera Windows-system som stöds. Med verktyg som System Center Updates Publisher (Updates Publisher) kan du publicera anpassade uppdateringar i Windows Server Update Services (WSUS). Med det här scenariot kan Uppdateringshantering korrigera datorer som använder Configuration Manager som sitt uppdaterings lager med program vara från tredje part.

* [Uppdateringshantering i Azure](./update-management/overview.md)

* [Hantera uppdateringar och korrigeringar för dina virtuella datorer](./update-management/manage-updates-for-vm.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

Om du använder hybrid Runbook Worker som backas upp av virtuella Azure-datorer kan du använda Azure Uppdateringshantering för att hantera uppdateringar och korrigeringar för dina virtuella datorer. Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att korrigera Windows-system som stöds. Med verktyg som System Center Updates Publisher (Updates Publisher) kan du publicera anpassade uppdateringar i Windows Server Update Services (WSUS). Med det här scenariot kan Uppdateringshantering korrigera datorer som använder Configuration Manager som sitt uppdaterings lager med program vara från tredje part.

* [Uppdateringshantering lösning i Azure](./update-management/overview.md)

* [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](./update-management/manage-updates-for-vm.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: exportera skannings resultat med jämna mellanrum och jämför resultaten för att verifiera att sårbarheter har åtgärd ATS. När du använder rekommendationen för sårbarhets hantering som föreslås av Azure Security Center kan kunden pivotera in den valda lösningens Portal för att visa historiska skannings data.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd standard risk klassificeringarna (säkra poäng) som tillhandahålls av Azure Security Center för att prioritera reparation av identifierade sårbarheter.

* [Förstå Azure Security Center säkra Poäng](../security-center/secure-score-security-controls.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla Azure Automation resurser i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

* [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

* [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra Azure Automation resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid. Ta bort eventuella oanvända kör som-konton för att minimera din exponerade attack yta.

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](./delete-run-as-account.md)

* [Hantera ett Kör som-konto för Azure Automation](./manage-runas-account.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: du måste skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationer. Detta kan hjälpa till med hög säkerhetsbaserade miljöer, till exempel de med lagrings konton.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Azure Policy exempel på inbyggda program för Azure Automation](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: det Azure Automation erbjudandet exponerar för närvarande inte den underliggande Runbook Worker för flera innehavare och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid arbetare. Det är dock möjligt att installera, ta bort och hantera PowerShell-eller python-moduler som Runbooks har åtkomst till via portalen eller cmdlets. Ej godkänd eller gammal modul bör tas bort eller uppdateras för Runbooks.

Om du använder hybrid Runbook Worker som backas upp av Azure Virtual Machines ger Azure Automation fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Utnyttja inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara på Virtual Machines. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. För att få åtkomst till installations datum och annan information, krävs kunden för att aktivera diagnostik på gästnivå och ta med Windows-händelseloggen till en Log Analytics-arbetsyta.

* [En introduktion till Azure Automation](./automation-intro.md)

* [Så här aktiverar du inventering av virtuella Azure-datorer](./automation-tutorial-installed-software.md)

* [Hantera modul i Azure Automation](./shared-resources/modules.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: kunden kan förhindra att resurser skapas eller används med Azure policy som krävs av kundens företags rikt linjer. Du kan implementera en egen process för att ta bort obehöriga resurser. Inom Azure Automation-erbjudandet är det möjligt att installera, ta bort och hantera PowerShell-eller python-moduler som Runbooks har åtkomst till via portalen eller cmdlets. Ej godkänd eller gammal modul bör tas bort eller uppdateras för Runbooks.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Hantera modul i Azure Automation](./shared-resources/modules.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: när du använder hybrid Runbook Worker-funktionen kan du använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure-Virtual Machines.

* [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: när du använder hybrid Runbook Worker-funktionen kan du använda funktionen Azure Security Center adaptiva program kontroller med dina virtuella datorer i hybrid Worker.

Anpassningsbar program kontroll är en intelligent, automatiserad lösning från slut punkt till slut punkt från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina Azure-och icke-Azure-datorer (Windows och Linux). Implementera en lösning från tredje part om detta inte uppfyller organisationens krav.

* [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd Azures principer för villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering" från oskyddade eller ej godkända platser eller enheter.

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: när du använder hybrid Runbook Worker-funktionen, och beroende på typen av skript, kan du använda operativ systemets speciella konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser. Du kan också använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

* [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program med hög risk som distribueras i din Azure-miljö kan isoleras med hjälp av separata nätverks-och resurs behållare med hjälp av konstruktioner som virtuella nätverk, undernät, prenumerationer, hanterings grupper, de kan skyddas med antingen en Azure-brandvägg, en brand vägg för webbaserade program (WAF) eller nätverks säkerhets grupp (NSG).

* [Virtuella nätverk och virtuella datorer i Azure](../virtual-machines/network-overview.md)

* [Översikt över Azure Firewall](../firewall/overview.md)

* [Översikt över Azure Web Application-brandvägg](../web-application-firewall/overview.md)

* [Nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md)

* [Översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

* [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)

* [Beslutsguide för prenumerationer](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias för att skapa anpassade principer för att granska eller tillämpa konfigurationen för dina Azure Automation och relaterade resurser. Du kan också använda inbyggda Azure Policy definitioner.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

* [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

* [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy exempel på inbyggda program för Azure Automation](./policy-reference.md)

* [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare eller operativ system. Detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

När du använder funktionen Hybrid Runbook Worker använder du Azure Security Center rekommendation [åtgärda sårbarheter i säkerhetskonfigurationer på din Virtual Machines] för att underhålla säkerhetskonfigurationer på dina virtuella datorer.

* [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)

* [Så här åtgärdar du Azure Security Center rekommendationer](../security-center/security-center-remediate-recommendations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure Resource Manager mallar och Azure policy för att konfigurera Azure-resurser som är kopplade till Azure Automation på ett säkert sätt. Azure Resource Manager mallar är JSON-baserade filer som används för att distribuera Azure-resurser och alla anpassade mallar måste lagras och bevaras säkert i en kod lagrings plats. Använd funktionen för käll kontrolls integrering för att hålla dina Runbooks i ditt Automation-konto uppdaterat med skript i lagrings platsen för käll kontroll. Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

* [Använda källkontrollsintegrering](./source-control-integration.md)

* [Information om hur du skapar Azure Resource Manager mallar](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

* [Distribuera ett Automation-konto med hjälp av en Azure Resource Manager mall](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure Policy exempel på inbyggda program för Azure Automation](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare eller operativ system, och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

När du använder Hybrid Runbook Worker-funktionen finns det flera alternativ för att underhålla en säker konfiguration för virtuella Azure-datorer för distribution:

- Azure Resource Manager mallar: dessa är JSON-baserade filer som används för att distribuera en virtuell dator från Azure Portal, och den anpassade mallen måste behållas. Microsoft utför underhållet på bas mallarna.
- Anpassad virtuell hård disk (VHD): i vissa fall kan det krävas att anpassade VHD-filer används, till exempel vid hantering av komplexa miljöer som inte kan hanteras på annat sätt.
- Azure Automation tillstånds konfiguration: när det grundläggande operativ systemet har distribuerats kan du använda det för att få mer detaljerad kontroll över inställningarna och verkställas genom automatiserings ramverket.

I de flesta fall kan de virtuella Microsoft Base-mallarna tillsammans med Azure Automation tillstånds konfiguration hjälpa till att möta och upprätthålla säkerhets kraven.

* [Information om hur du hämtar mallen för virtuella datorer](/previous-versions/azure/virtual-machines/windows/download-template)

* [Information om hur du skapar ARM-mallar](../virtual-machines/windows/ps-template.md)

* [Ladda upp en anpassad virtuell hård disk till Azure](/azure-stack/operator/azure-stack-add-vm-image)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory om de är integrerade med Azure DevOps eller Active Directory om det är integrerat med TFS. Använd funktionen för käll kontrolls integrering för att hålla dina Runbooks i ditt Automation-konto uppdaterat med skript i lagrings platsen för käll kontroll.

* [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Använda källkontrollsintegrering](./source-control-integration.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare eller operativ system, och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

Se till att du begränsar åtkomsten till den anpassade OS-avbildningen som finns i ditt lagrings konto, så att endast behöriga användare kan komma åt avbildningen när du använder den Hybrid Runbook Worker funktionen.

* [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure Policy exempel på inbyggda program för Azure Automation](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare eller operativ system, och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

När du använder Hybrid Runbook Worker-funktionen använder du Azure Automation tillstånds konfiguration i Runbook Worker som är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla molnbaserade eller lokala data Center. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett.

* [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](./automation-dsc-onboarding.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använda Azure policy för att varna och granska Azure-resursfiler kan du använda principer för att identifiera viss resurs som inte har kon figurer ATS med en privat slut punkt.

När du använder funktionen Hybrid Runbook Worker använder du Azure Security Center för att utföra bas linje genomsökningar för dina virtuella Azure-datorer. Ytterligare metoder för automatisk konfiguration inkluderar konfiguration av Azure Automation tillstånd.

* [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Komma igång med konfiguration av Azure Automation tillstånd](./automation-dsc-getting-started.md)

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy exempel på inbyggda program för Azure Automation](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: Azure Automation erbjudandet exponerar för närvarande inte den underliggande Runbook Worker för flera klienter eller operativ systemet och hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid arbetare.

När du använder funktionen Hybrid Runbook Worker använder du Azure Automation tillstånds konfiguration för Runbook Worker som är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i ett moln eller lokalt Data Center. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett.

* [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](./automation-dsc-onboarding.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

* [Användning av Kundhanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Använd Runbook-autentisering med hanterade identiteter](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

* [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

* [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

* [Konfigurera hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Användning av Kundhanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: Azure Automation erbjudandet exponerar för närvarande inte den underliggande Runbook Worker för flera klienter eller operativ systemet och hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

När du använder funktionen Hybrid Runbook Worker använder du Microsofts program mot skadlig kod för virtuella Azure Windows-datorer för att kontinuerligt övervaka och försvara dina Runbook Worker-resurser.

* [Så här konfigurerar du Microsofts program mot skadlig kod för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: ej tillämpligt; Azure Automation som en tjänst lagrar inte filer. Microsoft Antimalware är aktiverat på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Automation), men det körs inte på ditt innehåll.

* [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: Azure Automation visar för närvarande inte underliggande Runbook Worker för flera innehavare eller operativ system, och detta hanteras av plattformen. Den här kontrollen kan inte användas om du använder den färdiga Box-tjänsten utan hybrid Runbook Worker.

När du använder funktionen Hybrid Runbook Worker använder du Microsoft Antimalware för Azure för att automatiskt installera de senaste signaturerna, plattformarna och motorns uppdateringar som standard på din Runbook Worker. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps" för att se till att alla slut punkter är uppdaterade med de senaste signaturerna. Windows OS kan skyddas ytterligare med ytterligare säkerhet för att begränsa risken för virus-eller skadlig kodbaserade attacker med tjänsten Microsoft Defender Avancerat skydd som kan integreras med Azure Security Center.

* [Så här distribuerar du Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Använd Azure Resource Manager för att distribuera Azure Automation-konton och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhets kopior för att återställa Azure Automation-konton och relaterade resurser. Använd Azure Automation för att anropa export-API: t för Azure Resource Manager Template regelbundet.

Använd funktionen för käll kontrolls integrering för att hålla dina Runbooks i ditt Automation-konto uppdaterat med skript i lagrings platsen för käll kontroll.

* [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager mal len referens för Azure Automation resurser](/azure/templates/microsoft.automation/allversions)

* [Skapa ett Automation-konto med hjälp av en Azure Resource Manager mall](./quickstart-create-automation-account-template.md)

* [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Resurs grupper – exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

* [Introduktion till Azure Automation](./automation-intro.md)

* [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [Användning av Kundhanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Använda källkontrollsintegrering](./source-control-integration.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Använd Azure Resource Manager för att distribuera Azure Automation-konton och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhets kopior för att återställa Azure Automation-konton och relaterade resurser. Använd Azure Automation för att anropa export-API: t för Azure Resource Manager Template regelbundet. Säkerhetskopiera Kundhanterade nycklar inom Azure Key Vault. Du kan exportera dina runbooks till skriptfiler med antingen Azure Portal eller PowerShell.

* [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager mal len referens för Azure Automation resurser](/azure/templates/microsoft.automation/allversions)

* [Skapa ett Automation-konto med hjälp av en Azure Resource Manager mall](./quickstart-create-automation-account-template.md)

* [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Resurs grupper – exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

* [Introduktion till Azure Automation](./automation-intro.md)

* [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [Användning av Kundhanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure Data Backup för Automation-konton](./automation-managing-data.md#data-backup)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Säkerställ att du regelbundet kan utföra distribution av Azure Resource Manager mallar regelbundet till en isolerad prenumeration om det behövs. Testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

* [Distribuera resurser med ARM-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

* [Användning av Kundhanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. Azure Resource Manager mallar. För att skydda resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory om de är integrerade med Azure DevOps eller Active Directory om det är integrerat med TFS.

Använd funktionen för käll kontrolls integrering för att hålla dina Runbooks i ditt Automation-konto uppdaterat med skript i lagrings platsen för käll kontroll.

* [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Använda källkontrollsintegrering](./source-control-integration.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

* [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

* [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

* [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

* [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

* [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

* [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

* [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

* [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)