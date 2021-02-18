---
title: Azures säkerhets bas linje för Azure Load Balancer
description: Azure Load Balancer säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3701d9dde569432d8bf1910f04395df0b916b75a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575161"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azures säkerhets bas linje för Azure Load Balancer

Azures säkerhets bas linje för Microsoft Azure Load Balancer innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution. Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer. Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Använd interna Azure Load Balancer för att bara tillåta trafik till Server dels resurser från vissa virtuella nätverk eller peer-kopplat virtuella nätverk utan att exponera Internet. Implementera en extern Load Balancer med käll översättning av nätverks adresser (SNAT) för att maskera IP-adresserna för Server dels resurser för skydd mot direkt Internet exponering.

Azure erbjuder två typer av Load Balancer erbjudanden, standard och Basic. Använd Standard Load Balancer för alla produktions arbets belastningar. Implementera nätverks säkerhets grupper och Tillåt endast åtkomst till programmets betrodda portar och IP-adressintervall. I de fall där det inte finns någon nätverks säkerhets grupp tilldelad till backend-undernätet eller NIC för de virtuella server dels datorerna, tillåts inte trafik till dessa resurser från belastningsutjämnaren. Med standard belastnings utjämning anger du utgående regler för att definiera utgående NAT med en nätverks säkerhets grupp. Granska dessa utgående regler för att finjustera beteendet för utgående anslutningar. 

Användning av en Standard Load Balancer rekommenderas för produktions arbets belastningar och vanligt vis används Basic-Load Balancer endast för testning eftersom Basic-typen är öppen för anslutningar från Internet som standard, och inte kräver nätverks säkerhets grupper för åtgärden. 

- [Utgående anslutningar i Azure](load-balancer-outbound-connections.md)

- [Uppgradera offentliga Azure-Load Balancer](./upgrade-basic-standard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Load Balancer är en direkt tjänst eftersom den förlitar sig på regler för nätverks säkerhets grupper som tillämpas på Server dels resurser och de konfigurerade utgående reglerna för att kontrol lera Internet åtkomst.

Granska de utgående regler som kon figurer ATS för din Standard Load Balancer via bladet utgående regler på bladet Load Balancer och reglerna för belastnings Utjämnings regler där du kan ha implicit utgående regler aktiverade.

Övervaka antalet utgående anslutningar och spåra hur ofta dina resurser når ut till Internet. 

Använd Security Center och följ rekommendationerna för nätverks skydd för att skydda dina Azure-nätverks resurser.

Följ säkerhets rekommendationerna för dina Server dels resurser och aktivera flödes loggar för nätverks säkerhets grupper och skicka loggarna till ett Azure Storage konto för granskning.

Skicka även flödes loggarna till en Log Analytics arbets yta och Använd Trafikanalys för att ge insikter om trafik mönster i Azure-molnet. Fördelarna med Trafikanalys inkluderar möjligheten att visualisera nätverks aktivitet, identifiera frekventa punkter och säkerhetshot, förstå trafikflödes mönster och hitta nätverks-felkonfigurationer.

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Hur gör jag för att kontrol lera statistiken för utgående anslutningar](./load-balancer-standard-diagnostics.md#how-do-i-check-my-outbound-connection-statistics)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: uttryckligen definiera Internet anslutning och giltiga käll-IP-adresser via utgående regler och nätverks säkerhets grupper med din Load Balancer att använda Microsofts Hot information för att skydda dina webb program.

- [Integrera Azure-brandväggen](../firewall/integrate-lb.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera standard skydd för Azure distributed denial of service (DDoS) på Azure Virtual Network för att skydda mot DDoS-attacker. 

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot intelligens-baserad filtrering aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

 

Använd Security Center hot skydd för att identifiera kommunikation med kända skadliga IP-adresser. 

Standard Load Balancer är utformad för att vara säker som standard och en del av en privat och isolerad Virtual Network. Den är stängd för inkommande flöden, om du inte har öppnat av nätverks säkerhets grupper för att uttryckligen tillåta tillåten trafik, och för att förhindra kända skadliga IP-adresser. Om inte en nätverks säkerhets grupp på ett undernät eller ett nätverkskort för den virtuella dator resursen finns bakom Load Balancer, tillåts inte trafik att komma åt den här resursen. 

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot intelligens-baserad filtrering aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik för att förhindra attacker från skadliga IP-adresser. 

Implementera vägledning för att integrera Azure-brandväggen med Load Balancer.

Använd Security Center hot skydds funktioner för att identifiera kommunikation med kända skadliga IP-adresser. 

Security Center (standard-nivån) tillhandahåller just-in-Time-åtkomst till virtuella datorer och konfigurerar tillåtna käll-IP-adresser så att åtkomst endast tillåts från godkända IP-adresser/intervall.
 

Använd Security Center anpassade nätverks härdnings funktionen för att rekommendera konfigurationer för nätverks säkerhets grupper som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information.
 

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall Threat Intelligence-baserad filtrering](../firewall/threat-intel.md)

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

- [Skydda dina hanteringsportar med just-in-time-åtkomst](../security-center/security-center-just-in-time.md)

- [Anpassad nätverks härdning i Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Integrera Azure-brandväggen med din Load Balancer](../firewall/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera Network Watcher paket fångst för att undersöka avvikande aktiviteter.

- [Så här skapar du en Network Watcher-instans](../network-watcher/network-watcher-create.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: implementera ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med nytto Last funktioner i Load Balancer miljön. 

Använd Azure Firewall Threat Intelligence om nytto lasts granskning inte är ett krav. Azure Firewall Threat Intelligence-baserad filtrering används för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att identifiera och/eller blockera skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: uttryckligen definiera Internet anslutning och giltiga käll-IP-adresser via utgående regler och nätverks säkerhets grupper med din Load Balancer att använda Microsofts hot informations funktioner för att skydda dina webb program.

- [Integrera Azure-brandväggen](../firewall/integrate-lb.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Ange namnet på service tag i fältet source eller destination för en regel för att tillåta eller neka trafiken för motsvarande tjänst. 

Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. 

Som standard innehåller varje nätverks säkerhets grupp service tag-AzureLoadBalancer för att tillåta hälso avsöknings trafik. 

Se Azure-dokumentationen för alla tjänst taggar som är tillgängliga för användning i regler för nätverks säkerhets grupper.

- [Tillgängliga tjänst etiketter](../virtual-network/service-tags-overview.md#available-service-tags)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser med Azure policy.

Använd Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resources Manager-mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition. 

Använd skissen på nya prenumerationer och finjustera kontroll och hantering genom versions hantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd resurs taggar för nätverks säkerhets grupper och andra resurser som är relaterade till nätverks säkerhets-och trafikflödet. 

Använd fältet Beskrivning för att dokumentera de regler som tillåter trafik till/från ett nätverk för enskilda nätverks säkerhets grupp regler.

Implementera någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde", vilket säkerställer att alla resurser skapas med taggar och för att meddela om eventuella befintliga otaggade resurser.

Använd Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Azure-Virtual Network](../virtual-network/quick-create-portal.md)

- [Filtrera nätverks trafik med regler för nätverks säkerhets grupper](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i dina Azure-resurser. 

Skapa aviseringar i Azure Monitor för att meddela dig när kritiska resurser ändras.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Granska ändringar i dina utgående regler och nätverks säkerhets grupper för dina belastningsutjämnare genom att Visa aktivitets loggen i dina prenumerationer. 

Visa interna värd loggar för att se till att Server dels resurserna är säkra.

Exportera loggarna till Log Analytics eller till en annan lagrings plattform. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Aktivera och inaktivera dessa data till Azure Sentinel eller en SIEM utifrån organisationens affärs behov.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Plattforms aktivitets loggar](../azure-monitor/essentials/activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Granska informationen om kontroll och hanterings plan och gransknings information som registrerats med aktivitets loggar för Basic-Load Balancer. Dessa infångnings inställningar är aktiverade som standard. 

Använd aktivitets loggar för att övervaka åtgärder på resurser för att visa all aktivitet och deras status. 

Ta reda på vilka åtgärder som har vidtagits för resurserna i din prenumeration med aktivitets loggar: 

- Vem startade åtgärden
- När åtgärden utfördes
- åtgärdens status

- värdena för andra egenskaper som kan hjälpa dig att undersöka åtgärden

Hämta information från aktivitets loggen via Azure PowerShell, kommando rads gränssnittet för Azure (CLI), Azure-REST API eller Azure Portal. 

Implementera multi-dimensionell diagnostik för Standard Load Balancer konfigurations funktioner med Azure Monitor.  Bland dessa ingår tillgängliga mått för säkerhet finns information om SNAT-anslutningar (Source Network Address Translation), portar. Ytterligare mått på SYN-och paket räknare (Synchronization) är också tillgängliga. 

Hämta flerdimensionella mått via programmerings gränssnitt och skriv dem till ett lagrings konto via alternativet "alla mått".

Använd innehålls paketet för Azure audit-loggar med Microsoft Power BI för att analysera dina data med förkonfigurerade instrument paneler, eller för att anpassa vyerna utifrån dina behov.

Strömma loggar till en händelsehubben eller en Log Analytics-arbetsyta. De kan också extraheras från Azure Blob Storage och visas i olika verktyg, till exempel Excel och Power BI. 

Aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part utifrån dina affärs behov.

- [Läs den här artikeln med stegvisa anvisningar för varje metod som beskrivs i gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md)

- [Azure Monitor-loggar för offentlig Basic Load Balancer](./load-balancer-monitor-log.md)

- [Visa aktivitets loggar för att övervaka åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md)

- [Hämta flerdimensionella mått via programmerings gränssnitt](./load-balancer-standard-diagnostics.md#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: aktivitets loggen är aktive rad som standard och bevaras för 90 dagar i Azures händelse logg arkiv. Ange din lagrings period för Log Analytics arbets yta enligt organisationens regler för efterlevnad i Azure Monitor. Använd Azure Storage konton för långsiktig lagring och arkivering.

- [Visa aktivitets loggar för att övervaka åtgärder på resurser-artikeln](../azure-resource-manager/management/view-activity-logs.md)

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: övervaka, hantera och Felsök standard Load Balancer-resurser med hjälp av Load Balancer sidan i Azure Portal och Resource Health sidan under Azure Monitor. Tillgängliga mått för säkerhet innefattar information om SNAT-anslutningar (Source Network Address Translation), portar. Dessutom är mått för SYN-och paket räknare tillgängliga. 

Använd Azure Monitor för att granska status för slut punkts hälso avsökning med flerdimensionella mått för standard-, externa och interna belastningsutjämnare. Samla in dessa mått program mässigt via API: er och skriva till ett lagrings konto via alternativet "alla mått".

Azure Monitors loggar är inte tillgängliga för interna Basic Load Balancer. 

Använd Azure Monitor för att se status för hälso avsökningen som sammanfattas per backend-pool för de grundläggande externa Load Balancer, till exempel antalet instanser i backend-poolen som inte tar emot begär Anden från Load Balancer på grund av hälso avsöknings fel. 

Implementera loggning med Azure Operational Insights för att tillhandahålla statistik om belastnings utjämningens hälso status. 

Använd aktivitets loggarna om du vill visa aviseringar och övervaka åtgärder på resurser och deras status i dina Azure-prenumerationer. Aktivitets loggar är aktiverade som standard och kan visas i Azure Portal. 

Använd Microsoft Power BI med innehålls paketet för Azures gransknings loggar och analysera dina data med förkonfigurerade instrument paneler. Anpassa vyerna så att de passar dina krav baserat på affärs behov. 

Strömma loggar till en händelsehubben eller en Log Analytics-arbetsyta. De kan också extraheras från Azure Blob Storage och visas i olika verktyg, till exempel Excel och Power BI. Du kan aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

- [Hälsoavsökningar i Load Balancer](./load-balancer-custom-probe-overview.md)

- [REST-API:et för Azure Monitor](/rest/api/monitor)

- [Hämta mått via REST API](/rest/api/monitor/metrics/list)

- [Standard Load Balancer diagnostik med mått, aviseringar och resurs hälsa](./load-balancer-standard-diagnostics.md)

- [Azure Monitor-loggar för offentlig Basic Load Balancer](./load-balancer-monitor-log.md)

- [Visa dina belastnings Utjämnings mått i Azure Portal](./load-balancer-standard-diagnostics.md#view-your-load-balancer-metrics-in-the-azure-portal)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Security Center med Log Analytics arbets yta för övervakning och aviseringar om avvikande aktivitet som är relaterade till Load Balancer i säkerhets loggar och händelser.

Aktivera och fordonsbaserad data till Azure Sentinel eller ett SIEM-verktyg från tredje part.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: gäller inte för Azure Load Balancer. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: gäller inte som Azure Load Balancer är en kärn nätverks tjänst som inte gör DNS-frågor.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: gäller inte Azure Load Balancer som den här rekommendationen gäller för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure rollbaserad åtkomst kontroll (Azure RBAC) gör att du kan hantera åtkomst till Azure-resurser, till exempel din Load Balancer via roll tilldelningar. Tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. 

Inventera fördefinierade och inbyggda roller för vissa resurser med verktyg som Azure CLI, Azure PowerShell eller Azure Portal.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: Använd Azure RBAC för att kontrol lera åtkomsten till dina Load Balancer-resurser.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: Load Balancer är en direkt tjänst som inte lagrar kund information. Den är en del av den underliggande plattform som hanteras av Microsoft. 

Microsoft behandlar allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot förlust och exponering av kund information. 

För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar när ändringar sker i kritiska Azure-resurser, till exempel belastningsutjämnare som används för viktiga produktions arbets belastningar.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar, protokoll och så vidare) i dina prenumerationer. Azure Resource Manager rekommenderas att skapa och använda aktuella resurser. 

Se till att du har rätt (Läs) behörigheter i din klient och räkna upp alla Azure-prenumerationer och-resurser i dina prenumerationer.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser med metadata för att logiskt organisera enligt en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra till gångar. 

Stäm av inventering med jämna mellanrum och se till att obehöriga resurser tas bort från dina prenumerationer i rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en lista över godkända Azure-resurser per organisations behov som du kan använda som en mekanism för att tillåta en lista. Detta gör det möjligt för din organisation att publicera alla nya Azure-tjänster när de formellt granskats och godkänts av organisationens typiska utvärderings processer för säkerhet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Fråga efter och identifiera resurser med Azure Resource Graph i ägda prenumerationer. 

Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure AD för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program vara som krävs för affärs åtgärder, men som kan ådra sig högre risk för organisationen, bör isoleras inom den egna virtuella datorn och/eller det virtuella nätverket och tillräckligt säkert med antingen en Azure-brandvägg eller en nätverks säkerhets grupp.

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md)

- [Så här skapar du en nätverks säkerhets grupp med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias för att skapa anpassade principer för att granska eller tillämpa konfigurationen för dina Azure-resurser. Använd inbyggda Azure Policys definitioner.

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhets kraven för din organisation.

Exportera Azure Resource Manager mallar till JavaScript Object Notation-format (JSON) och granska dem regelbundet för att säkerställa att konfigurationerna uppfyller organisationens säkerhets krav. 

Implementera rekommendationer från Security Center som en säker konfigurations bas linje för dina Azure-resurser. 

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.  Du kan också använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure policy definitioner, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. 

Bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller i Active Directory om de är integrerade med TFS.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure policy.  Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser. Implementera inbyggda princip definitioner som är relaterade till dina aktuella Azure Load Balancer resurser.  Använd också Azure Automation för att distribuera konfigurations ändringar. till dina Azure Load Balancer-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Security Center för att utföra bas linje genomsökningar för dina Azure-resurser och Azure policy för att varna och granska resurs konfigurationerna.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. 

Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera prenumerationer med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  

Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. 

Använd funktionen för kontinuerlig export i Security Center som gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. 

Använd Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Security Center för att automatiskt utlösa svar på säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöde i säkerhet ange](../security-center/workflow-automation.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)