---
title: Azure-säkerhetsbaslinje för Virtual Machine Scale Sets
description: Säkerhetsbaslinjen Virtual Machine Scale Sets innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 60b1de12f031a55388960a6e3c4e7df00c43e3c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867961"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Azure-säkerhetsbaslinje för Virtual Machine Scale Sets

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Virtual Machine Scale Sets. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller som definieras av Azure Security Benchmark och tillhörande vägledning som gäller för Virtual Machine Scale Sets. 

> [!NOTE]
> **Kontroller** som inte Virtual Machine Scale Sets, eller för vilka ansvaret är Microsofts, har undantagits. Information om hur Virtual Machine Scale Sets mappning helt till Azure Security Benchmark finns i den fullständiga Virtual Machine Scale Sets **[säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-machine-scale-sets-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** När du skapar en virtuell Azure-dator (VM) måste du skapa ett virtuellt nätverk eller använda ett befintligt virtuellt nätverk och konfigurera den virtuella datorn med ett undernät. Se till att alla distribuerade undernät har en nätverkssäkerhetsgrupp tillämpad med nätverksåtkomstkontroller som är specifika för dina program betrodda portar och källor.

Om du har ett specifikt användningsfall för en centraliserad brandvägg kan Azure Firewall också användas för att uppfylla dessa krav.

- [Nätverk för skalningsuppsättningar för virtuella Azure-datorer](virtual-machine-scale-sets-networking.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Så här distribuerar och konfigurerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Använd Azure Security Center för att identifiera och följa rekommendationer för nätverksskydd för att skydda dina azure VM-resurser i Azure. Aktivera NSG-flödesloggar och skicka loggar till ett lagringskonto för trafikgranskning för de virtuella datorerna för ovanlig aktivitet.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Om du använder vm-skalningsuppsättningen som värd för webbprogram använder du en nätverkssäkerhetsgrupp (NSG) i undernätet för VM-skalningsuppsättningen för att begränsa vilken nätverkstrafik, vilka portar och protokoll som tillåts kommunicera. Följ en nätverksmetod med lägsta privilegier när du konfigurerar dina NSG:er så att endast nödvändig trafik tillåts till ditt program.

Du kan också distribuera Azure Web Application Firewall (WAF) framför kritiska webbprogram för ytterligare kontroll av inkommande trafik. Aktivera diagnostikinställning för WAF och mata in loggar till ett lagringskonto, en händelsehubb eller en Log Analytics-arbetsyta.

- [Nätverk för skalningsuppsättningar för virtuella Azure-datorer](virtual-machine-scale-sets-networking.md)

- [Skapa en programgateway med en Web Application Firewall med hjälp av Azure Portal](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera DDoS-standardskydd (Distributed Denial of Service) på de virtuella nätverken för att skydda mot DDoS-attacker. Med Azure Security Center Integrated Threat Intelligence kan du övervaka kommunikationen med kända skadliga IP-adresser.  Konfigurera Azure Firewall på vart och ett av dina Virtual Network segment med Hotinformation aktiverat och konfigurerat till "Avisering och neka" för skadlig nätverkstrafik.

Du kan använda Azure Security Center just-in-time-nätverksåtkomst för att begränsa exponeringen för Virtuella Windows-datorer till godkända IP-adresser under en begränsad period.  Använd också Azure Security Center anpassningsbar nätverkshärdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hotinformation.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Förstå Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

- [Förstå Azure Security Center anpassningsbar nätverkshärdning](../security-center/security-center-adaptive-network-hardening.md)

- [Förstå Azure Security Center just-in-time-Access Control](../security-center/security-center-just-in-time.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Du kan registrera NSG-flödesloggar till ett lagringskonto för att generera flödesposter för din Azure Virtual Machines. När du undersöker avvikande aktivitet kan du aktivera Network Watcher paketinfångst så att nätverkstrafiken kan granskas för ovanlig och oväntad aktivitet.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Genom att kombinera paketinfångst som tillhandahålls av Network Watcher och ett IDS-verktyg med öppen källkod kan du utföra identifiering av nätverksintrång för en mängd olika hot. Du kan också distribuera Azure Firewall på Virtual Network segment efter behov med Hotinformation aktiverat och konfigurerat till "Avisering och neka" för skadlig nätverkstrafik.

- [Utföra identifiering av nätverksintrång Network Watcher och verktyg med öppen källkod](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du aviseringar med Azure Firewall](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Om du använder VM-skalningsuppsättning som värd för webbprogram kan du distribuera Azure Application Gateway för webbprogram med HTTPS/SSL aktiverat för betrodda certifikat. Med Azure Application Gateway dirigerar du din programwebbtrafik till specifika resurser genom att tilldela lyssnare till portar, skapa regler och lägga till resurser i en serverdelspool som VM-skalningsuppsättning osv.

- [Så här distribuerar du Application Gateway](../application-gateway/quick-create-portal.md)

- [Så här konfigurerar du Application Gateway att använda HTTPS](../application-gateway/create-ssl-portal.md)

- [Skapa en skalningsuppsättning som refererar till en programgateway](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

- [Förstå Layer 7-belastningsutjämning med Azure Web Application Gateway](../application-gateway/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och det administrativa arbetet med nätverkssäkerhetsregler

**Vägledning:** Använd Virtual Network för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller nätverkssäkerhetsgrupper Azure Firewall konfigurerat för dina virtuella Azure-datorer. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Förstå och använda tjänsttaggar](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för Azure-Virtual Machine Scale Sets med Azure Policy. Du kan också använda Azure Blueprints för att förenkla storskaliga distributioner av virtuella Azure-datorer genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, rolltilldelningar och Azure Policy-tilldelningar, i en enda skissdefinition. Du kan tillämpa skissen på prenumerationer och aktivera resurshantering via versionshantering av skisser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Lär dig mer om mallar för VM-skalningsuppsättning](virtual-machine-scale-sets-mvss-start.md) 

- [Azure Policy exempel för nätverk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Du kan använda taggar för nätverkssäkerhetsgrupper (NSG) och andra resurser som rör nätverkssäkerhet och trafikflöde som konfigurerats för dina virtuella Windows-datorer. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärs behov och/eller varaktighet för regler som tillåter trafik till/från ett nätverk.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka ändringar i nätverksresurskonfigurationer som är relaterade till skalningsuppsättningen för virtuella Azure-datorer. Skapa aviseringar Azure Monitor som utlöses när viktiga nätverksinställningar eller resurser ändras.

Använd Azure Policy för att validera (och/eller åtgärda) konfigurationer för nätverksresurser som är relaterade till VM-skalningsuppsättningen.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.11](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-11.md)]

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning:** Microsoft underhåller tidskällor för Azure-resurser, men du kan välja att hantera inställningarna för tidssynkronisering för dina Virtual Machines.

- [Så här konfigurerar du tidssynkronisering för Azure Windows-beräkningsresurser](../virtual-machines/windows/time-sync.md)

- [Så här konfigurerar du tidssynkronisering för Azure Linux-beräkningsresurser](../virtual-machines/linux/time-sync.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Aktivitetsloggar kan användas för att granska åtgärder som utförs på vm-skalningsuppsättningsresurser. Aktivitetsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) för dina resurser förutom läsåtgärder (GET). Aktivitetsloggar kan användas för att hitta ett fel vid felsökning eller för att övervaka hur en användare i organisationen har ändrat en resurs.

Du kan aktivera och ta med loggdata som produceras från Azure-aktivitetsloggar eller virtual machine-resurser till Azure Sentinel eller en SIEM från tredje part för central säkerhetslogghantering.

Använd Azure Security Center för att tillhandahålla övervakning av säkerhetshändelseloggar för Azure Virtual Machines. Med tanke på mängden data som säkerhetshändelseloggen genererar lagras den inte som standard. 

Om din organisation vill behålla loggdata för säkerhetshändelsen från den virtuella datorn kan de lagras på en Log Analytics-arbetsyta på önskad datainsamlingsnivå som konfigurerats i Azure Security Center.

- [Så här samlar du in plattformsloggar och mått med Azure Monitor ](../azure-monitor/essentials/diagnostic-settings.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Azure Monitor SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

- [Datainsamling i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Så här övervakar du virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivitetsloggar kan användas för att granska åtgärder som utförs på vm-skalningsuppsättningsresurser. Aktivitetsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) för dina resurser förutom läsåtgärder (GET). Aktivitetsloggar kan användas för att hitta ett fel vid felsökning eller för att övervaka hur en användare i din organisation har ändrat en resurs.

Aktivera insamling av diagnostikdata för gästoperativsystem genom att distribuera diagnostiktillägget på din Virtual Machines (VM). Du kan använda diagnostiktillägget för att samla in diagnostikdata som programloggar eller prestandaräknare från en virtuell Azure-dator.

För avancerad synlighet för program och tjänster som stöds av Skalningsuppsättningen för virtuella Azure-datorer kan du aktivera både Azure Monitor for VMs och Application Insights. Med Application Insights kan du övervaka ditt program och samla in telemetri, till exempel HTTP-begäranden, undantag osv. så att du kan korrelera problem mellan de virtuella datorerna och ditt program.

- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Visa och hämta Händelser i Azure-aktivitetsloggen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Så här övervakar du virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Översikt över Application Insights](../azure-monitor/app/app-insights-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning:** Använd Azure Security Center för att tillhandahålla övervakning av säkerhetshändelseloggar för Azure Virtual Machines. Med tanke på mängden data som säkerhetshändelseloggen genererar lagras den inte som standard. 

Om din organisation vill behålla loggdata för säkerhetshändelsen från den virtuella datorn kan de lagras på en Log Analytics-arbetsyta på önskad datainsamlingsnivå som konfigurerats i Azure Security Center.

- [Datainsamling i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Så här övervakar du virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-4.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Se till att alla lagringskonton eller Log Analytics-arbetsytor som används för att lagra loggar för virtuella datorer har loggens kvarhållningsperiod inställd enligt organisationens efterlevnadsregler.

- [Så här övervakar du virtuella datorer i Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Så här konfigurerar du Kvarhållningsperiod för Log Analytics-arbetsyta](../azure-monitor/logs/manage-cost-storage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultat. Använd Azure Monitor att granska loggar och köra frågor på loggdata.

Du kan också aktivera och ta med data för att Azure Sentinel siem från tredje part för att övervaka och granska loggarna.

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Förstå Log Analytics-arbetsyta](/azure/azure-monitor/logs/get-started-queries)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Azure Security Center konfigurerat med en Log Analytics-arbetsyta för övervakning och avisering om avvikande aktivitet som finns i säkerhetsloggar och händelser för din Azure-Virtual Machines.  

Du kan också aktivera och ta med data för att Azure Sentinel siem från tredje part för att ställa in aviseringar för avvikande aktivitet.

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här varnar du om loggdata i Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning:** Du kan använda Microsofts program mot skadlig kod för Azure Cloud Services och Virtual Machines och konfigurera dina virtuella Windows-datorer för att logga händelser till ett Azure Storage konto. Konfigurera en Log Analytics-arbetsyta för att mata in händelser från lagringskontona och skapa aviseringar där det är lämpligt. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps".  För virtuella Linux-datorer behöver du ett verktyg från tredje part för sårbarhetsidentifiering mot skadlig kod. 

- [Så här konfigurerar du Microsofts program mot skadlig kod för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Aktivera övervakning på gästnivå för Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

- [Anvisningar för att registrera Linux-servrar i Azure Security Center](../security-center/quickstart-onboard-machines.md) 

- [Följande länk innehåller Microsofts rekommenderade säkerhetsriktlinjer, som kan fungera som en villkorslista för sårbarhetsprogramvaran som valts](../virtual-machines/security-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-8.md)]

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning:** Implementera en tredjepartslösning från en Azure Marketplace DNS-loggningslösning enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning:** Azure Security Center säkerhetshändelseloggövervakning för Azure Virtual Machines (VM). Security Center etablerar Microsoft Monitoring Agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om automatisk etablering är aktiverat ELLER så kan du installera agenten manuellt.  Agenten aktiverar processgenereringshändelsen 4688 och fältet CommandLine i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av EventLog och övervakas Security Center av tjänstens identifieringstjänster.

För virtuella Linux-datorer kan du manuellt konfigurera konsolloggning per nod och använda syslogs för att lagra data.  Du kan också Azure Monitor Log Analytics-arbetsytan för att granska loggar och köra frågor på syslog-data från virtuella Azure-datorer.

- [Datainsamling i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Syslog-datakällor i Azure Monitor](../azure-monitor/agents/data-sources-syslog.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Även Azure Active Directory (Azure AD) är den rekommenderade metoden för att administrera användaråtkomst, kan Azure Virtual Machines ha lokala konton. Både lokala konton och domänkonton bör granskas och hanteras, vanligtvis med ett minsta fotavtryck. Dessutom kan du använda Azure Privileged Identity Management administrativa konton som används för att få åtkomst till de virtuella datorernas resurser.

- [Information för lokala konton](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

- [Information om Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Azure VIRTUAL Machine Scale Set Azure Active Directory (Azure AD) har inte begreppet standardlösenord. Kunden ansvarar för program från tredje part och Marketplace-tjänster som kan använda standardlösenord.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton som har åtkomst till dina virtuella datorer. Använd Azure Security Center identitets- och åtkomsthantering för att övervaka antalet administrativa konton. Alla administratörskonton som används för att få åtkomst till resurser för virtuella Azure-datorer kan också hanteras av Azure Privileged Identity Management (PIM). Azure Privileged Identity Management tillhandahåller flera alternativ, till exempel just-in-time-höjning, som kräver multifaktorautentisering innan du antar en roll, och delegeringsalternativ så att behörigheter endast är tillgängliga för specifika tidsramar och kräver en godkännare.

- [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Information om Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 3.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-3-3.md)]

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory för enkel inloggning (SSO)

**Vägledning:** Om möjligt ska du använda enkel inloggning med Azure Active Directory (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Enkel inloggning till program i Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Använd säkra, Azure-hanterade arbetsstationer för administrativa uppgifter

**Vägledning:** Använda ARBETSSTATIONer (arbetsstationer för privilegierad åtkomst) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Azure-resurser.

- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure AD-riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden. Kunden kan också mata in Azure Security Center riskidentifieringsaviseringar i Azure Monitor konfigurera anpassade aviseringar/meddelanden med hjälp av åtgärdsgrupper.

- [Så här distribuerar du Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå Azure Security Center riskidentifiering (misstänkt aktivitet)](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Så här konfigurerar du åtgärdsgrupper för anpassade aviseringar och meddelanden](../azure-monitor/alerts/action-groups.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd Azure Active Directory (Azure AD) för villkorsstyrd åtkomst och namngivna platser för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för vilodata och under överföring. Azure AD använder också salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.  Du kan använda hanterade identiteter för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden. Din kod som körs på en virtuell dator kan använda dess hanterade identitet för att begära åtkomsttoken för tjänster som stöder Azure AD-autentisering.

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

- [Översikt över hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure AD-identitetsåtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. När du använder virtuella Azure-datorer måste du granska de lokala säkerhetsgrupperna och användarna för att se till att det inte finns några oväntade konton som kan kompromettera systemet.

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Konfigurera diagnostikinställningar för Azure Active Directory (Azure AD) för att skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan också Azure Monitor för att granska loggar och köra frågor på loggdata från virtuella Azure-datorer.

- [Förstå Log Analytics-arbetsyta](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Så här övervakar du virtuella datorer i Azure](/azure/azure-monitor/vm/monitor-vm-azure)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) funktioner för risk och identitetsskydd för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till dina lagringskontoresurser. Du bör aktivera automatiserade svar Azure Sentinel att implementera organisationens säkerhetssvar.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** I supportscenarier där Microsoft behöver åtkomst till kunddata (till exempel under en supportbegäran) använder du Customer Lockbox för virtuella Azure-datorer för att granska och godkänna eller avvisa begäranden om kunddataåtkomst.

- [Förstå Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra virtuella Azure-datorer som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Resurserna ska avgränsas med virtuella nätverk/undernät, taggas på rätt sätt och skyddas i en nätverkssäkerhetsgrupp (NSG) eller av en Azure Firewall. För Virtual Machines lagra eller bearbeta känsliga data implementerar du policyer och procedurer för att inaktivera dem när de inte används.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du avisering eller avisering och nekar med Azure Firewall](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Implementera en tredjepartslösning i nätverks perimeterer som övervakar obehörig överföring av känslig information och blockerar sådana överföringar samtidigt som informationssäkerhetspersonal varnas.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Data under överföring till, från och mellan Virtual Machines (VM) som kör Windows krypteras på flera olika sätt, beroende på anslutningens typ, till exempel vid anslutning till en virtuell dator i en RDP- eller SSH-session.

Microsoft använder protokollet Transport Layer Security (TLS) för att skydda data när de färdas mellan molntjänsterna och kunderna.

- [Kryptering under överföring på virtuella datorer](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Använd ett aktivt identifieringsverktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens tekniksystem, inklusive sådana som finns på plats eller hos en fjärransluten tjänstleverantör och uppdatera organisationens känsliga informationslager.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att styra åtkomsten till resurser 

**Vägledning:** Med hjälp av rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du åtseger uppgifter i ditt team och endast bevilja den åtkomst till användare på din virtuella dator (VM) som de behöver för att utföra sitt arbete. I stället för att ge alla obegränsad behörighet på den virtuella datorn kan du bara tillåta vissa åtgärder. Du kan konfigurera åtkomstkontroll för den virtuella datorn i Azure Portal, med hjälp av Azure CLI eller Azure PowerShell.

- [Azure RBAC](../role-based-access-control/overview.md)

- [Inbyggda roller i Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustskydd för att framtvinga åtkomstkontroll

**Vägledning:** Implementera ett verktyg från tredje part, till exempel en automatiserad värdbaserad lösning för skydd mot dataförlust, för att framtvinga åtkomstkontroller för att minska risken för dataintrång.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Virtuella diskar på Virtual Machines (VM) krypteras i vila med antingen kryptering på serversidan eller Azure-diskkryptering (ADE). Azure Disk Encryption utnyttjar funktionen DM-Crypt i Linux för att kryptera hanterade diskar med kund hanterade nycklar på den virtuella gästdatorn. Kryptering på serversidan med kund hanterade nycklar förbättrar ADE genom att göra det möjligt för dig att använda alla typer av operativsystem och avbildningar för dina virtuella datorer genom att kryptera data i lagringstjänsten.

- [Azure Disk Encryption för Virtual Machine Scale Sets](disk-encryption-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i VM-skalningsuppsättningar och relaterade resurser.  

- [Så här skapar du aviseringar för Händelser i Azure-aktivitetsloggen](../azure-monitor/alerts/alerts-activity-log.md)

- [Analysloggning i Azure Storage](../storage/common/storage-analytics-logging.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Följ rekommendationerna från Azure Security Center att utföra sårbarhetsbedömningar på din Azure-Virtual Machines.  Använd Azure Securitys rekommenderade eller tredje parts lösning för att utföra sårbarhetsbedömningar för dina virtuella datorer.

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-1.md)]

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera automatisk lösning för korrigeringshantering av operativsystem

**Vägledning:** Aktivera automatiska OS-uppgraderingar för operativsystemversioner som stöds eller för anpassade avbildningar som lagras i en Shared Image Gallery.

- [Automatiska OS-uppgraderingar för VM-skalningsuppsättningar i Azure](virtual-machine-scale-sets-automatic-upgrade.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-2.md)]

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuera en automatiserad korrigeringshanteringslösning för programvarutitlar från tredje part

**Vägledning:** Azure Virtual Machine Scale Sets kan använda automatiska uppgraderingar av operativsystemavbildningar. Du kan använda Tillägget Azure Desired State Configuration (DSC) för underliggande virtuella datorer i VM-skalningsuppsättningen. DSC används för att konfigurera de virtuella datorerna när de är online så att de kör önskad programvara.

- [Använda Virtual Machine Scale Sets med Azure DSC tillägget](virtual-machine-scale-sets-dsc.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka till bakåt-sårbarhetsgenomsökningar

**Vägledning:** Exportera genomsökningsresultat med konsekventa intervall och jämför resultaten för att verifiera att sårbarheter har åtgärdats. När du använder rekommendationen för sårbarhetshantering Azure Security Center kan kunden pivotera till den valda lösningens portal för att visa historiska genomsökningsdata.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Använd standardriskklassificeringar (säkerhetspoäng) som tillhandahålls av Azure Security Center.

- [Förstå Azure Security Center säkerhetspoäng](../security-center/secure-score-security-controls.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (inklusive virtuella datorer) i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser som ger metadata för att organisera dem logiskt enligt en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggar, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Virtual Machines skalningsuppsättningar och relaterade resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Skapa en förteckning över godkända Azure-resurser och godkänd programvara för beräkningsresurser enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy för att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Du kan också använda Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna. Detta kan vara till hjälp i miljöer med hög säkerhet, till exempel de med Lagringskonton.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Skapa frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka program som inte är godkända i beräkningsresurser

**Vägledning:** Azure Automation ger fullständig kontroll under distribution, drift och inaktivering av arbetsbelastningar och resurser.  Använd Azure Virtual Machine Inventory för att automatisera insamlingen av information om all programvara på Virtual Machines. Obs! Programnamn, Version, Utgivare och Uppdateringstid är tillgängliga från Azure Portal. För att få åtkomst till installationsdatum och annan information måste kunden aktivera diagnostik på gästnivå och föra Windows-händelseloggarna till en Log Analytics-arbetsyta.

För närvarande är anpassningsbara programkontroller inte tillgängliga för Virtual Machine Scale Sets.

- [En introduktion till Azure Automation](../automation/automation-intro.md)

- [Så här aktiverar du Azure VM-inventering](../automation/automation-tutorial-installed-software.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Azure Automation ger fullständig kontroll under distribution, drift och inaktivering av arbetsbelastningar och resurser.  Du kan använda Ändringsspårning för att identifiera alla program som är installerade på Virtual Machines. Du kan implementera en egen process eller använda Azure Automation State Configuration för att ta bort obehörig programvara.

- [En introduktion till Azure Automation](../automation/automation-intro.md)

- [Spåra ändringar i din miljö med Ändringsspårning lösningen](../automation/change-tracking/overview.md)

- [Azure Automation State Configuration översikt](../automation/automation-dsc-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända program

**Vägledning:** För närvarande är anpassningsbara programkontroller inte tillgängliga för Virtual Machine Scale Sets. Använd programvara från tredje part för att styra användningen till endast godkända program.

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-8.md)]

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy för att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.9](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-9.md)]

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Upprätthålla en förteckning över godkända programvarutitlar

**Vägledning:** För närvarande är anpassningsbara programkontroller inte tillgängliga för Virtual Machine Scale Sets. Implementera en lösning från tredje part om detta inte uppfyller organisationens krav.

- [Så här använder du Azure Security Center anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-10.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript inom beräkningsresurser

**Vägledning:** Beroende på typen av skript kan du använda operativsystemspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure-beräkningsresurser.

- [Så här styr du PowerShell-skriptkörning i Windows-miljöer](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera högriskprogram

**Vägledning:** Program med hög risk som distribueras i Azure-miljön kan isoleras med hjälp av virtuella nätverk, undernät, prenumerationer, hanteringsgrupper osv. och vara tillräckligt säkra med antingen en Azure Firewall, Web Application Firewall (WAF) eller en nätverkssäkerhetsgrupp (NSG). 

- [Virtuella nätverk och virtuella datorer i Azure](/azure/virtual-machines/windows/network-overview)

- [Azure Firewall översikt](../firewall/overview.md)

- [Web Application Firewall översikt](../web-application-firewall/overview.md)

- [Översikt över Network-säkerhet](../virtual-network/network-security-groups-overview.md)

- [Översikt Virtual Network Azure Virtual Network Azure](../virtual-network/virtual-networks-overview.md)

- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)

- [Beslutsguide för prenumerationer](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy eller Azure Security Center för att underhålla säkerhetskonfigurationer för alla Azure-resurser. Dessutom Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhetskraven för ditt företag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Information om hur du laddar ned VM-mallen](/previous-versions/azure/virtual-machines/windows/download-template)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta säkra operativsystemkonfigurationer

**Vägledning:** Använd Azure Security Center rekommendationen Åtgärda sårbarheter i säkerhetskonfigurationer på din Virtual Machines att underhålla säkerhetskonfigurationer på alla beräkningsresurser.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)

- [Så här åtgärdar du Azure Security Center rekommendationer](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Resource Manager och Azure-principer för att på ett säkert sätt konfigurera Azure-resurser som är Virtual Machines skalningsuppsättningar.  Azure Resource Manager är JSON-baserade filer som används för att distribuera en virtuell dator tillsammans med Azure-resurser och anpassade mallar måste underhållas.  Microsoft utför underhållet på basmallarna.  Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

- [Information om hur du Azure Resource Manager mallar](../virtual-machines/windows/ps-template.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhålla säkra operativsystemkonfigurationer

**Vägledning:** Det finns flera alternativ för att upprätthålla en säker konfiguration för Azure Virtual Machines (VM) för distribution:

1. Azure Resource Manager: Det här är JSON-baserade filer som används för att distribuera en virtuell dator från Azure Portal och anpassade mallar måste underhållas. Microsoft utför underhållet på basmallarna.

2. Anpassad virtuell hårddisk (VHD): I vissa fall kan det krävas att anpassade VHD-filer används, till exempel när du hanterar komplexa miljöer som inte kan hanteras på andra sätt.

3. Azure Automation State Configuration: När det grundläggande operativsystemet har distribuerats kan detta användas för mer detaljerad kontroll av inställningarna och framtvingas via automationsramverket.

I de flesta fall kan Microsofts grundläggande VM-mallar som kombineras Azure Automation Desired State Configuration kan hjälpa till att uppfylla och upprätthålla säkerhetskraven.

- [Information om hur du laddar ned VM-mallen](/previous-versions/azure/virtual-machines/windows/download-template)

- [Information om hur du skapar ARM-mallar](../virtual-machines/windows/ps-template.md)

- [Ladda upp en anpassad virtuell hårddisk till Azure](../virtual-machines/windows/upload-generalized-managed.md)

**Ansvar**: Delad

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-4.md)]

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning:** Använd Azure DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager-mallar, Desired State Configuration-skript osv.  För att få åtkomst till de resurser som du hanterar i Azure DevOps, till exempel din kod, dina byggen och din arbetsspårning, måste du ha behörighet för dessa specifika resurser. De flesta behörigheter beviljas via inbyggda säkerhetsgrupper enligt beskrivningen i Behörigheter och åtkomst. Du kan bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Active Directory om de är integrerade med TFS.

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning:** Om du använder anpassade avbildningar (t.ex. virtuell hårddisk) använder du rollbaserade åtkomstkontroller i Azure för att säkerställa att endast behöriga användare får åtkomst till avbildningarna.  

- [Förstå RBAC i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Så här konfigurerar du RBAC i Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy för att varna, granska och framtvinga systemkonfigurationer för dina virtuella datorer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera konfigurationshanteringsverktyg för operativsystem

**Vägledning:** Azure Automation State Configuration är en konfigurationshanteringstjänst för Desired State Configuration-noder (DSC) i alla molndatacenter eller lokala datacenter. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar varje dators kompatibilitet med det önskade tillstånd som du har angett. 

- [Registrering av datorer för hantering av Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina virtuella Azure-datorer.  Ytterligare metoder för automatisk konfiguration är att använda Azure Automation State Configuration.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Komma igång med Azure Automation State Configuration](../automation/automation-dsc-getting-started.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning:** Azure Automation State Configuration är en konfigurationshanteringstjänst för Desired State Configuration-noder (DSC) i alla molndatacenter eller lokala datacenter. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar varje dators kompatibilitet med det önskade tillstånd som du har angett.

- [Registrering av datorer för hantering av Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 7.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-10.md)]

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlighetshantering för dina molnprogram.

- [Så här integrerar du med hanterade Azure-identiteter](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

- [Tilldela en Key Vault åtkomstprincip](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter säkert och automatiskt

**Vägledning:** Använda hanterade identiteter för att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

- [Så här konfigurerar du hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig kod

**Vägledning:** Använd Microsoft Antimalware virtuella Azure Windows-datorer för att kontinuerligt övervaka och skydda dina resurser.  Du behöver ett verktyg från tredje part för skydd mot skadlig kod på den virtuella Azure Linux-datorn. 

- [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-1.md)]

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att program mot skadlig kod och signaturer uppdateras

**Vägledning:** När de distribueras för virtuella Windows-datorer Microsoft Antimalware azure automatiskt de senaste signatur-, plattforms- och motoruppdateringarna som standard. Följ rekommendationerna i Azure Security Center: "Compute Apps" för att se till att alla slutpunkter är &amp; uppdaterade med de senaste signaturerna. Windows-operativsystemet kan skyddas ytterligare med ytterligare säkerhet för att begränsa risken för virus- eller skadlig kod-baserade attacker med Microsoft Defender Avancerat skydd-tjänsten som integreras med Azure Security Center.

Du behöver ett verktyg från tredje part för skydd mot skadlig kod på den virtuella Azure Linux-datorn. 

- [Så här distribuerar du Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure) 

- [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](../virtual-machines/security-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-3.md)]

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security Benchmark: Data Recovery.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Skapa en ögonblicksbild av vm-skalningsuppsättningsinstansen i Azure eller en hanterad disk som är ansluten till instansen med hjälp av PowerShell eller REST-API:er.  Du kan också använda Azure Automation att köra säkerhetskopieringsskripten med jämna mellanrum.

- [Ta en ögonblicksbild av en vm-skalningsuppsättningsinstans och hanterad disk](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance) 

- [Introduktion till Azure Automation](../automation/automation-intro.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Skapa ögonblicksbilder av dina virtuella Azure-datorer eller hanterade diskar som är anslutna till dessa instanser med hjälp av PowerShell eller REST-API:er. Back up any customer-managed keys within Azure Key Vault.

Aktivera Azure Backup azure-Virtual Machines (VM) samt önskad frekvens och kvarhållningsperioder. Detta inkluderar fullständig säkerhetskopiering av systemtillstånd. Om du använder Azure-diskkryptering hanterar Säkerhetskopiering av virtuella Azure-datorer automatiskt säkerhetskopieringen av kund hanterade nycklar.

- [Säkerhetskopiera på virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)

- [Översikt över säkerhetskopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)

- [Så här tar du en ögonblicksbild av en instans av en VM-skalningsuppsättning och en hanterad disk](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

- [Säkerhetskopiera nyckelvalvsnycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Säkerställ möjligheten att regelbundet utföra dataåterställning av hanterade diskar inom Azure Backup. Om det behövs testar du återställning av innehåll till ett isolerat virtuellt nätverk eller en prenumeration. Kunden kan testa återställningen av säkerhetskopierade kund hanterade nycklar.

Om du använder Azure-diskkryptering kan du återställa dina VM-skalningsuppsättningar med diskkrypteringsnycklarna. När du använder diskkryptering kan du återställa den virtuella Azure-datorn med diskkrypteringsnycklarna.

- [Säkerhetskopiera på virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)

- [Återställa en disk och skapa en återställd virtuell dator i Azure](../backup/tutorial-restore-disk.md)

- [Så här återställer du nyckelvalvsnycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Så här aktiverar du diskkryptering för Azure Virtual Machine Scale Sets](disk-encryption-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Aktivera borttagningsskydd för hanterade diskar med lås. Aktivera Soft-Delete och rensa skydd i Key Vault att skydda nycklar mot oavsiktlig eller skadlig borttagning.  

- [Låsa resurser för att förhindra oväntade ändringar](../azure-resource-manager/management/lock-resources.md)

- [Azure Key Vault översikt över mjuk borttagning och rensning](../key-vault/general/soft-delete-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en incidentsvarsguide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.  

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Använd NIST:s guide för hantering av incidenter vid datorsäkerhet för att underlätta skapandet av en egen plan för incidenthantering](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för bedömning och prioritering av incidenter

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller det mått som användes för att utfärda aviseringen samt konfidensnivån att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen. 

Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-prod) med hjälp av taggar och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Utför övningar för att testa systemets incidentsvarsfunktioner regelbundet för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig eller obehörig part. Granska incidenter i efterhand för att se till att problemen är lösta.

- [Så här anger du Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure-resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser. 

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och körning av Red Teaming och intrångstester på live-webbplatser mot Microsofts hanterade molninfrastruktur, tjänster och program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
