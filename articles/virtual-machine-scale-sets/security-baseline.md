---
title: Azures säkerhets bas linje för Virtual Machine Scale Sets
description: Virtual Machine Scale Sets säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2d902bbdc03596fe246fc36813895e72c53da05a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571399"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Azures säkerhets bas linje för Virtual Machine Scale Sets

Azures säkerhets bas linje för Virtual Machine Scale Sets innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: när du skapar en virtuell dator i Azure måste du skapa ett virtuellt nätverk eller använda ett befintligt virtuellt nätverk och konfigurera den virtuella datorn med ett undernät. Se till att alla distribuerade undernät har en nätverks säkerhets grupp som tillämpas med nätverks åtkomst kontroller som är speciella för dina programs betrodda portar och källor.

Alternativt, om du har ett särskilt användnings fall för en centraliserad brand vägg kan Azure-brandväggen även användas för att uppfylla dessa krav.

* [Nätverk för skalningsuppsättningar för virtuella Azure-datorer](./virtual-machine-scale-sets-networking.md)

* [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: Använd Azure Security Center för att identifiera och följa rekommendationer för nätverks skydd för att skydda dina virtuella Azure-resurser i Azure. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning för de virtuella datorerna för ovanlig aktivitet.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: om du använder din skalnings uppsättning för virtuella datorer (VMSS) som värd för webb program, använder du en nätverks säkerhets grupp (NSG) på VMSS-undernätet för att begränsa vilken nätverks trafik, vilka portar och protokoll som tillåts kommunicera. Följ en princip för minsta möjliga nätverks användning när du konfigurerar din NSG: er så att den endast tillåter obligatorisk trafik till ditt program.

Du kan också Distribuera Azure Web Application Firewall (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställningar för WAF och mata in loggar till ett lagrings konto, en Event Hub-eller Log Analytics-arbetsyta.

* [Nätverk för skalningsuppsättningar för virtuella Azure-datorer](./virtual-machine-scale-sets-networking.md)

* [Skapa en Programgateway med en brand vägg för webbaserade program med hjälp av Azure Portal](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS-standardskydd (distributed denial of Service) på de virtuella nätverken för att skydda mot DDoS-attacker. Med Azure Security Center integrerad Hot information kan du övervaka kommunikation med kända skadliga IP-adresser. Konfigurera Azure-brandväggen på var och en av dina Virtual Network segment, med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

Du kan använda Azure Security Center just-in-Time-åtkomst för att begränsa exponeringen av Virtuella Windows-datorer till godkända IP-adresser under en begränsad period. Använd också Azure Security Center anpassad nätverks härdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information.

* [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

* [Förstå Azure Security Center anpassad nätverks härdning](../security-center/security-center-adaptive-network-hardening.md)

* [Förstå Azure Security Center just-in-Time-nätverk Access Control](../security-center/security-center-just-in-time.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: du kan registrera NSG Flow-loggar i ett lagrings konto för att generera flödes poster för Azure-Virtual Machines. När du undersöker avvikande aktivitet kan du aktivera Network Watcher paket avbildning så att nätverks trafiken kan granskas för ovanlig och oväntad aktivitet.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: genom att kombinera paket avbildningar som tillhandahålls av Network Watcher och ett ID-verktyg med öppen källkod kan du utföra identifiering av nätverks intrång för en mängd olika hot. Du kan också Distribuera Azure-brandväggen på Virtual Network segmenten efter behov, med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

* [Utför identifiering av nätverks intrång med Network Watcher och verktyg med öppen källkod](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: om du använder skalnings uppsättningen för virtuella datorer (VMSS) som värd för webb program kan du Distribuera Azure Application Gateway för webb program med HTTPS/SSL aktiverat för betrodda certifikat. Med Azure Application Gateway dirigerar du din program webb trafik till specifika resurser genom att tilldela lyssnare till portar, skapa regler och lägga till resurser till en backend-pool som VMSS osv.

* [Så här distribuerar du Application Gateway](../application-gateway/quick-create-portal.md)

* [Så här konfigurerar du Application Gateway att använda HTTPS](../application-gateway/create-ssl-portal.md)

* [Skapa en skalningsuppsättning som refererar till en programgateway](./virtual-machine-scale-sets-networking.md#create-a-scale-set-that-references-an-application-gateway)

* [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](../application-gateway/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS för dina virtuella Azure-datorer. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

* [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure Virtual Machine Scale Sets med Azure policy. Du kan också använda Azure-ritningar för att förenkla storskaliga distributioner av virtuella Azure-datorer genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, roll tilldelningar och Azure Policy tilldelningar, i en enda skiss definition. Du kan använda skissen på prenumerationer och aktivera resurs hantering via skiss version.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Lär dig mer om mallar för skalnings uppsättningar för virtuella datorer](./virtual-machine-scale-sets-mvss-start.md)

* [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

* [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: du kan använda taggar för nätverks säkerhets grupper (NSG) och andra resurser som rör nätverks säkerhets-och trafikflödet som kon figurer ATS för dina virtuella Windows-datorer. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet för alla regler som tillåter trafik till/från ett nätverk.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets loggen för att övervaka ändringar i konfigurationer för nätverks resurser som är relaterade till skalnings uppsättningen för den virtuella Azure-datorn. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks inställningar eller resurser sker.

Använd Azure Policy för att verifiera (och/eller åtgärda) konfigurationer för nätverks resurser som är relaterade till skalnings uppsättningen för den virtuella datorn.

* [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser, men du har möjlighet att hantera tidssynkroniserings inställningarna för din Virtual Machines.

* [Så här konfigurerar du tidssynkronisering för Azure Windows Compute-resurser](../virtual-machines/windows/time-sync.md)

* [Så här konfigurerar du tidssynkronisering för Azure Linux-beräknings resurser](../virtual-machines/linux/time-sync.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: aktivitets loggar kan användas för att granska åtgärder och åtgärder som utförs på virtuella datorers skalnings uppsättnings resurser. Aktivitets loggen innehåller alla Skriv åtgärder (placera, skicka och ta bort) för dina resurser, förutom Läs åtgärder (GET). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

Du kan aktivera och on-Board-loggdata som skapats från Azure aktivitets loggar eller virtuella dator resurser till Azure Sentinel eller en SIEM för central hantering av säkerhets loggar.

Använd Azure Security Center för att tillhandahålla övervakning av säkerhets händelse logg för Azure Virtual Machines. Utifrån den data volym som säkerhets händelse loggen genererar, lagras den inte som standard.

Om din organisation vill behålla säkerhets händelse logg data från den virtuella datorn, kan den lagras på en Log Analytics arbets yta på den önskade data insamlings nivån som kon figurer ATS i Azure Security Center.

* [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Datainsamling i Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Övervaka virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: aktivitets loggar kan användas för att granska åtgärder och åtgärder som utförs på virtuella datorers skalnings uppsättnings resurser. Aktivitets loggen innehåller alla Skriv åtgärder (placera, skicka och ta bort) för dina resurser, förutom Läs åtgärder (GET). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

Aktivera insamlingen av gäst operativ systemets diagnostikdata genom att distribuera Diagnostic-tillägget på din Virtual Machines (VM). Du kan använda Diagnostics-tillägget för att samla in diagnostikdata som program loggar eller prestanda räknare från en virtuell Azure-dator.

För avancerad insyn i de program och tjänster som stöds av skalnings uppsättningen för virtuella Azure-datorer kan du aktivera både Azure Monitor for VMs och Application Insights. Med Application Insights kan du övervaka ditt program och avbilda telemetri, till exempel HTTP-begäranden, undantag osv. så att du kan korrelera problem mellan de virtuella datorerna och ditt program.

* [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Övervaka virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

* [Översikt över Application Insights](../azure-monitor/app/app-insights-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: Använd Azure Security Center för att tillhandahålla övervakning av säkerhets händelse logg för Azure Virtual Machines. Utifrån den data volym som säkerhets händelse loggen genererar, lagras den inte som standard.

Om din organisation vill behålla säkerhets händelse logg data från den virtuella datorn, kan den lagras på en Log Analytics arbets yta på den önskade data insamlings nivån som kon figurer ATS i Azure Security Center.

* [Datainsamling i Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Övervaka virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: kontrol lera att alla lagrings konton eller Log Analytics arbets ytor som används för att lagra loggar för virtuella datorer har den logg kvarhållningsperiod som anges enligt organisationens regler för efterlevnad.

* [Övervaka virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

* [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../azure-monitor/logs/manage-cost-storage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor för att granska loggar och köra frågor om loggdata.

Du kan också aktivera och inaktivera data till Azure Sentinel eller från SIEM för att övervaka och granska dina loggar.

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Förstå Log Analytics arbets yta](../azure-monitor/logs/log-analytics-tutorial.md)

* [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Security Center som kon figurer ATS med en Log Analytics arbets yta för övervakning och aviseringar om avvikande aktivitet i säkerhets loggar och händelser för Azure-Virtual Machines.

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller från en SIEM för att ställa in aviseringar för avvikande aktivitet.

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: du kan använda Microsofts program mot skadlig kod för Azure Cloud Services och Virtual Machines och konfigurera dina virtuella Windows-datorer så att de loggar händelser till ett Azure Storage-konto. Konfigurera en Log Analytics arbets yta för att mata in händelser från lagrings kontona och skapa aviseringar där det är lämpligt. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps". För virtuella Linux-datorer behöver du ett verktyg från tredje part för sårbarhets igenkänning av skadlig kod.

* [Så här konfigurerar du Microsofts program mot skadlig kod för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

* [Aktivera övervakning på gästnivå för Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

* [Instruktioner för att publicera Linux-servrar till Azure Security Center](../security-center/quickstart-onboard-machines.md)

* [Följande länk innehåller Microsofts rekommenderade säkerhets rikt linjer, som kan fungera som en villkors lista för det sårbarhets program som valts](../virtual-machines/security-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part från Azure Marketplace för DNS-loggning av lösningar enligt organisationens behov.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Azure Security Center tillhandahåller övervakning av säkerhets händelse logg för Azure Virtual Machines (VM). Security Center etablerar Microsoft Monitoring Agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om automatisk etablering är aktiverat eller om du kan installera agenten manuellt. Agenten gör det möjligt för processen att skapa händelse 4688 och fältet kommandorad i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av EventLog och övervakas av Security Centers identifierings tjänster.

För virtuella Linux-datorer kan du manuellt konfigurera konsol loggning per nod och använda syslogs för att lagra data. Använd också Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor på syslog-data från virtuella Azure-datorer.

* [Datainsamling i Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

* [Syslog-datakällor i Azure Monitor](../azure-monitor/agents/data-sources-syslog.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: medan Azure Active Directory är den rekommenderade metoden för att administrera användar åtkomst kan Azure Virtual Machines ha lokala konton. Både lokala konton och domän konton bör granskas och hanteras, vanligt vis med minsta möjliga storlek. Använd dessutom Azure Privileged Identity Management för administrativa konton som används för att komma åt resurserna för virtuella datorer.

* [Information om lokala konton finns på](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [Information om Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: skalnings uppsättningen för virtuella Azure-datorer och Azure Active Directory saknar begreppet standard lösen ord. Kund ansvarig för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton som har åtkomst till dina virtuella datorer. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton. Alla administratörs konton som används för att komma åt resurser för virtuella Azure-datorer kan också hanteras av Azure Privileged Identity Management (PIM). Azure Privileged Identity Management tillhandahåller flera alternativ, till exempel bara i tids ökning, vilket kräver Multi-Factor Authentication innan du antar en roll och Delegerings alternativ så att behörigheter bara är tillgängliga för vissa tids ramar och kräver en god kännare.

* [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

* [Information om Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: där det är möjligt kan du använda SSO med Azure Active Directory snarare än att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Azure Security Center identitets-och åtkomst hantering.

* [Enkel inloggning till program i Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering.

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure AD PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd identifieringar av Azure AD-risker för att visa aviseringar och rapporter om riskfyllda användar beteenden. Alternativt kan kunden hämta Azure Security Center risk identifierings aviseringar till Azure Monitor och konfigurera anpassade aviseringar/aviseringar med hjälp av åtgärds grupper.

* [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Förstå Azure Security Center risk identifieringar (misstänkt aktivitet)](../active-directory/identity-protection/overview-identity-protection.md)

* [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Konfigurera åtgärds grupper för anpassad avisering och avisering](../azure-monitor/alerts/action-groups.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd Azure Active Directory principer för villkorlig åtkomst och namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt. Du kan använda hanterade identiteter för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden. Din kod som körs på en virtuell dator kan använda sin hanterade identitet för att begära åtkomsttoken för tjänster som stöder Azure AD-autentisering.

* [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

* [Översikt över hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Använd dessutom Azure Active Directory identitets åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. När du använder virtuella Azure-datorer måste du granska de lokala säkerhets grupperna och användarna för att se till att det inte finns några oväntade konton som kan äventyra systemet.

* [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Konfigurera diagnostikinställningar för Azure Active Directory att skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan också använda Azure Monitor för att granska loggar och köra frågor om loggdata från virtuella Azure-datorer.

* [Förstå Log Analytics arbets yta](../azure-monitor/logs/log-analytics-tutorial.md)

* [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

* [Övervaka virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directorys risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör dina lagrings konto resurser. Du bör aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar.

* [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: i support scenarier där Microsoft behöver åtkomst till kund information (t. ex. under en supportbegäran) använder Customer lockbox för virtuella Azure-datorer för att granska och godkänna eller avvisa förfrågningar om kund data åtkomst.

* [Förstå Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra virtuella Azure-datorer som lagrar eller bearbetar känslig information.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas inom en nätverks säkerhets grupp (NSG) eller av en Azure-brandvägg. För Virtual Machines lagring eller bearbetning av känsliga data implementerar du principer och procedurer för att inaktivera dem när de inte används.

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

**Vägledning**: implementera en lösning från tredje part på nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar vid avisering av information säkerhets tekniker.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: data som överförs till, från och mellan Virtual Machines (VM) som kör Windows krypteras på flera olika sätt, beroende på anslutningens typ, till exempel vid anslutning till en virtuell dator i en RDP-eller ssh-session.

Microsoft använder TLS-protokollet (Transport Layer Security) för att skydda data när de reser mellan moln tjänster och kunder.

* [Kryptering under överföring i virtuella datorer](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: Använd ett externt identifierings verktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens teknik system, inklusive de som finns på plats eller på en fjärran sluten tjänst leverantör och uppdatera organisationens känsliga informations lager.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du åtskilja uppgifter i teamet och endast bevilja åtkomst till användare på den virtuella datorn (VM) som de behöver för att utföra sina jobb. I stället för att ge alla obegränsade behörigheter på den virtuella datorn kan du bara tillåta vissa åtgärder. Du kan konfigurera åtkomst kontroll för den virtuella datorn i Azure Portal, med hjälp av Azure CLI eller Azure PowerShell.

* [Azure RBAC](../role-based-access-control/overview.md)

* [Inbyggda roller i Azure](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: implementera ett verktyg från tredje part, till exempel en automatiserad värd lösning för data förlust skydd, för att genomdriva åtkomst kontroller för att minimera risken för data intrång.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: virtuella diskar på Virtual Machines (VM) krypteras i vila med hjälp av antingen kryptering på Server sidan eller med hjälp av Azure Disk Encryption (ade). Azure Disk Encryption utnyttjar funktionen DM-Crypt i Linux för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn. Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.

* [Azure Disk Encryption för Virtual Machine Scale Sets](./disk-encryption-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i skalnings uppsättningar för virtuella datorer och relaterade resurser.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

* [Analysloggning i Azure Storage](../storage/common/storage-analytics-logging.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar på Azure-Virtual Machines. Använd Azure Security-Rekommenderad eller en lösning från tredje part för att utföra sårbarhets bedömningar för dina virtuella datorer.

* [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Aktivera automatiska OS-uppgraderingar för operativ system versioner som stöds eller för anpassade avbildningar som lagras i ett delat avbildnings Galleri.

* [Automatiska operativ system uppgraderingar för skalnings uppsättningar för virtuella datorer i Azure](./virtual-machine-scale-sets-automatic-upgrade.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: Azure Virtual Machine Scale Sets (VMSS) kan använda automatiska uppgraderingar av operativ Systems avbildningar. Du kan använda Azures tillägg för önskad tillstånds konfiguration (DSC) för de underliggande virtuella datorerna i VMSS. DSC används för att konfigurera de virtuella datorerna när de är online så att de kör önskad program vara.

* [Använda Virtual Machine Scale Sets med Azure DSC-tillägget](./virtual-machine-scale-sets-dsc.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: exportera skannings resultat med jämna mellanrum och jämför resultaten för att verifiera att sårbarheter har åtgärd ATS. När du använder rekommendationen för sårbarhets hantering som föreslås av Azure Security Center kan kunden pivotera in den valda lösningens Portal för att visa historiska skannings data.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd standard risk klassificeringarna (säkra poäng) som tillhandahålls av Azure Security Center.

* [Förstå Azure Security Center säkra Poäng](../security-center/secure-score-security-controls.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (inklusive virtuella datorer) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

* [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

* [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem enligt en taxonomi.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra Virtual Machines skalnings uppsättningar och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt våra organisations behov.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna. Detta kan hjälpa till med hög säkerhetsbaserade miljöer, till exempel de med lagrings konton.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Utnyttja inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara på Virtual Machines. Obs!: program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. För att få åtkomst till installations datum och annan information, krävs kunden för att aktivera diagnostik på gästnivå och ta med Windows-händelseloggen till en Log Analytics-arbetsyta.

För närvarande är anpassningsbara program kontroller inte tillgängliga för Virtual Machine Scale Sets.

* [En introduktion till Azure Automation](../automation/automation-intro.md)

* [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Du kan använda Ändringsspårning för att identifiera all program vara som är installerad på Virtual Machines. Du kan implementera en egen process eller använda Azure Automation tillstånds konfiguration för att ta bort otillåten program vara.

* [En introduktion till Azure Automation](../automation/automation-intro.md)

* [Spåra ändringar i din miljö med Ändringsspårning-lösningen](../automation/change-tracking/overview.md)

* [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: för närvarande är adaptiva program kontroller inte tillgängliga för Virtual Machine Scale Sets. Använd program från tredje part för att kontrol lera användningen av enbart godkända program.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: för närvarande är adaptiva program kontroller inte tillgängliga för Virtual Machine Scale Sets. Implementera en lösning från tredje part om detta inte uppfyller organisationens krav.

* [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: beroende på typen av skript kan du använda operativ systemets speciella konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.

* [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program med hög risk som distribueras i din Azure-miljö kan isoleras med hjälp av virtuella nätverk, undernät, prenumerationer, hanterings grupper osv. och tillräckligt säkra med antingen en Azure-brandvägg, en brand vägg för webbaserade program (WAF) eller nätverks säkerhets grupp (NSG).

* [Virtuella nätverk och virtuella datorer i Azure](../virtual-machines/network-overview.md)

* [Översikt över Azure Firewall](../firewall/overview.md)

* [Översikt över brand väggen för webb program](../web-application-firewall/overview.md)

* [Översikt över Network-säkerhet](../virtual-network/network-security-groups-overview.md)

* [Översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

* [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)

* [Beslutsguide för prenumerationer](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: använda Azure Policy eller Azure Security Center för att underhålla säkerhetskonfigurationer för alla Azure-resurser. Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för ditt företag.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Information om hur du hämtar mallen för virtuella datorer](/previous-versions/azure/virtual-machines/windows/download-template)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Använd Azure Security Center rekommendation [åtgärda sårbarheter i säkerhetskonfigurationer på din Virtual Machines] för att underhålla säkerhetskonfigurationer för alla beräknings resurser.

* [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)

* [Så här åtgärdar du Azure Security Center rekommendationer](../security-center/security-center-remediate-recommendations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure Resource Manager mallar och Azure-principer för att konfigurera Azure-resurser som är associerade med Virtual Machines skalnings uppsättningar på ett säkert sätt. Azure Resource Manager mallar är JSON-baserade filer som används för att distribuera den virtuella datorn tillsammans med Azure-resurser och anpassad mall måste behållas. Microsoft utför underhållet på bas mallarna. Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

* [Information om hur du skapar Azure Resource Manager mallar](../virtual-machines/windows/ps-template.md)

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: det finns flera alternativ för att underhålla en säker konfiguration för Azure Virtual Machines (VM) för distribution:

1. Azure Resource Manager mallar: dessa är JSON-baserade filer som används för att distribuera en virtuell dator från Azure Portal, och den anpassade mallen måste behållas. Microsoft utför underhållet på bas mallarna.

2. Anpassad virtuell hård disk (VHD): i vissa fall kan det krävas att anpassade VHD-filer används, till exempel vid hantering av komplexa miljöer som inte kan hanteras på annat sätt.

3. Azure Automation tillstånds konfiguration: när det grundläggande operativ systemet har distribuerats kan du använda det för att få mer detaljerad kontroll över inställningarna och verkställas genom automatiserings ramverket.

I de flesta fall kan de virtuella bas-VM-mallarna tillsammans med Azure Automation önskad tillstånds konfiguration hjälpa till att möta och upprätthålla säkerhets kraven.

* [Information om hur du hämtar mallen för virtuella datorer](/previous-versions/azure/virtual-machines/windows/download-template)

* [Information om hur du skapar ARM-mallar](../virtual-machines/windows/ps-template.md)

* [Ladda upp en anpassad virtuell hård disk till Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar, önskade tillstånds konfigurations skript osv.  För att få åtkomst till de resurser som du hanterar i Azure DevOps, till exempel kod, build-och arbets spårning, måste du ha behörighet för dessa specifika resurser. De flesta behörigheter beviljas via inbyggda säkerhets grupper enligt beskrivningen i behörigheter och åtkomst. Du kan bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

* [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: om du använder anpassade avbildningar (t. ex. virtuell hård disk) använder du rollbaserad åtkomst kontroll i Azure (t. ex. virtuell hård disk) för att säkerställa att endast behöriga användare får åtkomst till avbildningarna.

* [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: utnyttja Azure policy för att varna, granska och genomdriva system konfigurationer för dina virtuella datorer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett.

* [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](../automation/automation-dsc-onboarding.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: utnyttja Azure Security Center för att utföra bas linje genomsökningar för dina virtuella Azure-datorer. Ytterligare metoder för automatisk konfiguration är att använda Azure Automation tillstånds konfiguration.

* [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Komma igång med konfiguration av Azure Automation tillstånd](../automation/automation-dsc-getting-started.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett.

* [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](../automation/automation-dsc-onboarding.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

* [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

* [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

* [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

* [Konfigurera hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd en centralt hanterad program vara mot skadlig kod

**Vägledning**: Använd Microsoft Antimalware för virtuella Azure Windows-datorer för att kontinuerligt övervaka och försvara dina resurser. Du behöver ett verktyg från tredje part för skydd mot skadlig kod i virtuella Azure Linux-datorer.

* [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: gäller inte för virtuella Azure-datorer eftersom det är en beräknings resurs.

**Azure Security Center-övervakning**: Ja

**Ansvars område**: inte tillämpligt

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: när du har distribuerat för virtuella Windows-datorer installerar Microsoft Antimalware för Azure automatiskt de senaste signaturerna, plattformen och motorns uppdateringar som standard. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps" för att se till att alla slut punkter är uppdaterade med de senaste signaturerna. Windows OS kan skyddas ytterligare med ytterligare säkerhet för att begränsa risken för virus-eller skadlig kodbaserade attacker med tjänsten Microsoft Defender Avancerat skydd som kan integreras med Azure Security Center.

Du behöver ett verktyg från tredje part för skydd mot skadlig kod i virtuella Azure Linux-datorer.

* [Så här distribuerar du Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](../virtual-machines/security-recommendations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: skapa ögonblicks bild av den virtuella Azure-datorns skalnings uppsättnings instans eller hanterad disk som är ansluten till instansen med POWERSHELL eller REST API Du kan också använda Azure Automation för att köra säkerhets kopierings skripten med jämna mellanrum.

* [Ta en ögonblicks bild av en instans av en skalnings uppsättning för virtuella datorer och en hanterad disk](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Introduktion till Azure Automation](../automation/automation-intro.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: skapa ögonblicks bilder av dina virtuella Azure-datorer eller de hanterade diskar som är kopplade till dessa instanser med PowerShell-eller REST-API: er. Säkerhetskopiera alla Kundhanterade nycklar inom Azure Key Vault.

Aktivera Azure Backup och mål Azure-Virtual Machines (VM) samt önskad frekvens och kvarhållningsperiod. Detta inkluderar fullständig säkerhets kopiering av system tillstånd. Om du använder Azure Disk Encryption hanterar Azure VM Backup automatiskt säkerhets kopieringen av Kundhanterade nycklar.

* [Säkerhetskopiera virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)

* [Översikt över säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)

* [Ta en ögonblicks bild av en instans av en skalnings uppsättning för virtuella datorer och en hanterad disk](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Säkerställ att du regelbundet kan utföra Data återställning av den hanterade disken inom Azure Backup. Om det behövs kan du testa att återställa innehållet till ett isolerat virtuellt nätverk eller en prenumeration. Kunden att testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

Om du använder Azure Disk Encryption kan du återställa dina skalnings uppsättningar för virtuella datorer med disk krypterings nycklarna. När du använder disk kryptering kan du återställa den virtuella Azure-datorn med disk krypterings nycklarna.

* [Säkerhetskopiera virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)

* [Återställa en disk och skapa en återställd virtuell dator i Azure](../backup/tutorial-restore-disk.md)

* [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Aktivera disk kryptering för Azure Virtual Machine Scale Sets](./disk-encryption-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Aktivera borttagning av skydd för hanterad disk med lås. Aktivera Soft-Delete och rensa skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

* [Låsa resurser för att förhindra oväntade ändringar](../azure-resource-manager/management/lock-resources.md)

* [Översikt över Azure Key Vault mjuk borttagning och rensning](../key-vault/general/soft-delete-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-övervakning**: Ja

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

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

* [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

* [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)