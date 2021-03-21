---
title: Azures säkerhets bas linje för Azure Kubernetes-tjänsten
description: Säkerhets bas linjen för Azure Kubernetes service ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: de32de5fc78919154bade3895cab6da82b175a93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740634"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azures säkerhets bas linje för Azure Kubernetes-tjänsten

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Azure Kubernetes. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Azure Kubernetes. **Kontroller** som inte gäller för Azure-Kubernetes har uteslutits.

 
Om du vill se hur Azure-Kubernetes helt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga mappnings filen för Azure Kubernetes Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: som standard skapas en nätverks säkerhets grupp och en routningstabell automatiskt med att skapa ett AKS-kluster (Microsoft Azure Kubernetes service). AKS ändrar automatiskt nätverks säkerhets grupper för lämpligt trafikflöde när tjänster skapas med belastningsutjämnare, Port mappningar eller ingångs vägar. Nätverks säkerhets gruppen associeras automatiskt med de virtuella nätverkskorten på kundnoder och i routningstabellen med under nätet i det virtuella nätverket. 

Använd AKS nätverks principer för att begränsa nätverks trafiken genom att definiera regler för ingångs-och utgående trafik mellan Linux-poddar i ett kluster baserat på val av namn områden och etikett väljare. Nätverks princip användningen kräver plugin-programmet Azure CNI med definierade virtuella nätverk och undernät och kan bara aktive ras när klustret skapas. De kan inte distribueras i ett befintligt AKS-kluster.

Du kan implementera ett privat AKS-kluster för att säkerställa att nätverks trafiken mellan din AKS API-Server och resurspooler bara finns i det privata nätverket. Kontroll planet eller API-servern finns i en AKS-hanterad Azure-prenumeration och använder interna (RFC1918) IP-adresser, medan kundens kluster eller Node är i en egen prenumeration. Servern och klustret eller noden kommunicerar med varandra med hjälp av tjänsten Azure Private Link i det virtuella nätverkets API-Server och en privat slut punkt som exponeras i under nätet för kundens AKS-kluster.  Du kan också använda en offentlig slut punkt för AKS-API-servern men begränsa åtkomsten med AKS-API-serverns auktoriserade IP-adressintervall. 

- [Säkerhetsbegrepp för program och kluster i AKS (Azure Kubernetes Service)](concepts-security.md)

- [Skydda trafik mellan poddar med hjälp av nätverks principer i Azure Kubernetes service (AKS)](use-network-policies.md)

- [Skapa ett privat Azure Kubernetes service-kluster](private-clusters.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. container service**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Security Center och följ sina nätverks skydds rekommendationer för att skydda de nätverks resurser som används av dina Azure Kubernetes service-kluster (AKS). 

Aktivera flödes loggar för nätverks säkerhets grupper och skicka loggarna till ett Azure Storage konto för granskning. Du kan också skicka flödes loggarna till en Log Analytics arbets yta och sedan använda Trafikanalys för att ge insikter om trafik mönster i ditt Azure-moln för att visualisera nätverks aktivitet, identifiera frekventa fläckar och säkerhetshot, förstå trafikflödes mönster och hitta nätverks konfigurations problem.

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Så här aktiverar du nätverks säkerhets flödes loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: använd en Azure Application Gateway-aktiverad brand vägg för webbaserade program (WAF) framför ett AKS-kluster för att tillhandahålla ett extra säkerhets lager genom att filtrera inkommande trafik till dina webb program. Azure-WAF använder en uppsättning regler, som tillhandahålls av det öppna webb programmet säkerhets projekt (OWASP), för attacker, till exempel Cross Site-skript eller cookie-förgiftning mot den här trafiken. 

Använd en API-Gateway för autentisering, auktorisering, begränsning, cachelagring, omvandling och övervakning för API: er som används i din AKS-miljö. En API-gateway fungerar som en frontend-dörr för mikrotjänster, tar bort klienter från dina mikrotjänster och minskar komplexiteten för mikrotjänster genom att ta bort belastningen vid hantering av överskotts problem.

- [Förstå metod tips för nätverks anslutning och säkerhet i AKS](operator-best-practices-network.md)

- [Application Gateway ingångs kontroll ](../application-gateway/ingress-controller-overview.md)

- [Använda Azure API Management med mikrotjänster som distribueras i Azure Kubernetes-tjänsten](../api-management/api-management-kubernetes.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera standard skydd i Microsoft distributed denial-of-service (DDoS) på de virtuella nätverk där Azure Kubernetes service (AKS)-komponenter distribueras för skydd mot DDoS-attacker.

Installera nätverks princip motorn och skapa Kubernetes nätverks principer för att styra flödet av trafik mellan poddar i AKS som standard är all trafik tillåten mellan dessa poddar. Nätverks principen bör endast användas för Linux-baserade noder och poddar i AKS. Definiera regler som begränsar Pod-kommunikation för förbättrad säkerhet. 

Välj att tillåta eller neka trafik baserat på inställningar som tilldelad etiketter, namnrymd eller trafik port. De nödvändiga nätverks principerna kan tillämpas automatiskt när poddar skapas dynamiskt i ett AKS-kluster. 

- [Skydda trafik mellan poddar med hjälp av nätverks principer i Azure Kubernetes service (AKS)](use-network-policies.md)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Använd Network Watcher paket avbildning som krävs för att undersöka avvikande aktivitet. 

Network Watcher aktive ras automatiskt i det virtuella nätverkets region när du skapar eller uppdaterar ett virtuellt nätverk i din prenumeration. Du kan också skapa nya instanser av Network Watcher med PowerShell, Azure CLI, REST API eller Azure Resource Manager klient metod

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: skydda ditt Azure Kubernetes service-kluster (AKS) med en Azure Application-Gateway som är aktive rad med en brand vägg för webbaserade program (WAF). 

Om intrångs identifiering och/eller skydd som baseras på nytto Last kontroll eller beteende analys inte är ett krav, kan en Azure Application Gateway med WAF användas och konfigureras i "identifierings läge" för att logga aviseringar och hot, eller "skydds läge" för att aktivt blockera identifierade intrång och attacker.

- [Lär dig metod tips för att skydda ditt AKS-kluster med en WAF](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Så här distribuerar du Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper som är associerade med Azure Kubernetes service-instanser (AKS). Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler för att tillåta eller neka trafiken för motsvarande tjänst genom att ange namnet på service tag-namnet. 

Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Använd en Azure-tagg för resurspooler i ditt AKS-kluster. De skiljer sig från det virtuella nätverkets tjänst Taggar och tillämpas på varje nod i noden och behålls genom uppgraderingar. 

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

- [Förstå NSG: er för AKS](support-policies.md)

- [Styra utgående trafik för klusternoder i Azure Kubernetes service (AKS)](limit-egress-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardkonfigurationer med Azure policy för nätverks resurser som är associerade med dina Azure Kubernetes service-kluster (AKS). 

Använd Azure Policy alias i namn områdena "Microsoft. container service" och "Microsoft. Network" om du vill skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina AKS-kluster. 

Använd också inbyggda princip definitioner som är relaterade till AKS, till exempel:

- Auktoriserade IP-intervall ska definieras på Kubernetes Services

- Använder inkommande HTTPS i Kubernetes-kluster

- Se till att tjänsterna bara lyssnar på tillåtna portar i Kubernetes-kluster

Ytterligare information finns på de länkar som refereras till.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: använda taggar för nätverks säkerhets grupper och andra resurser för trafikflöde till och från Azure Kubernetes service-kluster (AKS). Använd fältet Beskrivning för enskilda regler för nätverks säkerhets grupper för att ange affärs behov och/eller varaktighet, och så vidare, för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda definitionerna för Azure Policy taggar, till exempel "Kräv tagg och dess värde" som säkerställer att alla resurser skapas med taggar och att de kan ta emot meddelanden om befintliga otaggade resurser.

Välj att tillåta eller neka vissa nätverks Sök vägar i klustret baserat på namn områden och etikett väljare med nätverks principer. Använd dessa namn rymder och etiketter som beskrivningar för trafik konfigurations regler. Använd Azure PowerShell eller Azure kommando rads gränssnitt (CLI) för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Azure Policy med CLI](/cli/azure/policy)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure Kubernetes service (AKS)-kluster. 

Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker. Alla poster från AzureContainerService-användaren i aktivitets loggarna loggas som plattforms åtgärder. 

Använd Azure Monitor loggar för att aktivera och skicka frågor till loggarna från AKS huvud komponenter, Kube-apiserver och Kube-Controller Manager. Skapa och hantera noderna som kör kubelet med container Runtime och distribuera sina program via den hanterade Kubernetes API-servern. 

- [Visa och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Aktivera och granska Kubernetes huvudnodloggar i Azure Kubernetes Service (AKS)](/azure/aks/view-master-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: noder i Azure Kubernetes service (AKS) använder NTP.Ubuntu.com för tidssynkronisering, tillsammans med UDP-port 123 och Network Time Protocol (NTP). 

Se till att NTP-servrarna är tillgängliga för klusternoderna om du använder anpassade DNS-servrar. 

- [Förstå NTP-domännamn och port krav för AKS-klusternoder](limit-egress-traffic.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera gransknings loggar från AKS-huvudkomponenter (Azure Kubernetes Services), Kube-apiserver och Kube-Controller-Manager, som tillhandahålls som en hanterad tjänst. 

- Kube-auditaksService: visnings namnet i gransknings loggen för kontroll Plans åtgärden (från hcpService) 

- MasterClient: visnings namnet i gransknings loggen för MasterClientCertificate, certifikatet som du får från AZ AKS get-credentials 

- nodeclient: visnings namnet för ClientCertificate, som används av agentens noder

Aktivera även andra gransknings loggar som Kube-Audit. 

Exportera loggarna till Log Analytics eller till en annan lagrings plattform. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Aktivera och inaktivera dessa data till Azure Sentinel eller en SIEM utifrån organisationens affärs behov.

- [Granska logg schemat inklusive logg roller här](/azure/aks/view-master-logs)

- [Förstå Azure Monitor för behållare](/azure/azure-monitor/insights/container-insights-overview)

- [Aktivera Azure Monitor för behållare](/azure/azure-monitor/insights/container-insights-onboard)

- [Aktivera och granska Kubernetes huvudnodloggar i Azure Kubernetes Service (AKS)](/azure/aks/view-master-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Använd aktivitets loggar för att övervaka åtgärder på Azure Kubernetes service (AKS)-resurser för att visa all aktivitet och deras status. Ta reda på vilka åtgärder som har vidtagits för resurserna i din prenumeration med aktivitets loggar: 

- Vem startade åtgärden
- När åtgärden utfördes
- åtgärdens status
- värdena för andra egenskaper som kan hjälpa dig att undersöka åtgärden

Hämta information från aktivitets loggen via Azure PowerShell, kommando rads gränssnittet för Azure (CLI), Azure-REST API eller Azure Portal. 

Aktivera gransknings loggar på AKS huvud komponenter, t. ex.: 

- Kube-auditaksService: visnings namnet i gransknings loggen för kontroll Plans åtgärden (från hcpService) 

- MasterClient: visnings namnet i gransknings loggen för MasterClientCertificate, certifikatet som du får från AZ AKS get-credentials 

- nodeclient: visnings namnet för ClientCertificate, som används av agentens noder

Aktivera även andra gransknings loggar som Kube-Audit. 

- [Så här aktiverar och granskar du Kubernetes huvud Node-loggar i AKS](/azure/aks/view-master-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: Aktivera automatisk installation av Log Analytics agenter för att samla in data från AKS-klusternoderna. Aktivera automatisk etablering av Azure Log Analytics Monitoring Agent från Azure Security Center, som standard är automatisk etablering avstängd. Agenten kan också installeras manuellt. Med automatisk etablering på distribuerar Security Center Log Analytics agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Security Center samlar in data från Azure Virtual Machines (VM), skalnings uppsättningar för virtuella datorer och IaaS-behållare, till exempel Kubernetes-klusternoder, för att övervaka säkerhets problem och hot. Data samlas in med hjälp av Azure Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys. 

Data insamling krävs för att ge insyn i uppdateringar som saknas, felkonfigurerade säkerhets inställningar, Endpoint Protection-status och hälso-och hot identifiering.

- [Så här aktiverar du automatisk etablering av Log Analytics agent](../security-center/security-center-enable-data-collection.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: publicera dina Azure Kubernetes service-instanser (AKS) för att Azure Monitor och ange motsvarande lagrings period för Azure Log Analytics-arbetsytan enligt organisationens krav på efterlevnad. 

- [Ange logg lagrings parametrar för Log Analytics arbets ytor](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: publicera dina Azure Kubernetes service-instanser (AKS) för att Azure Monitor och konfigurera diagnostikinställningar för klustret. 

Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata. Azure Monitor loggar aktive ras och hanteras i Azure Portal eller genom CLI och fungerar med både Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC), Azure RBAC-och icke-RBAC-aktiverade AKS-kluster.

Se de loggar som genereras av AKS Master Components (Kube-apiserver och Kube-controllermanager) för fel sökning av program och tjänster. Aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM för centraliserad logg hantering och övervakning.

- [Så här aktiverar och granskar du Kubernetes huvud Node-loggar i AKS](/azure/aks/view-master-logs)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Kubernetes service (AKS) tillsammans med Security Center för att få djupare insyn i AKS-noder. 

Granska Security Center aviseringar om hot och skadlig aktivitet som identifierats på värden och på kluster nivå. Security Center implementerar kontinuerlig analys av rå säkerhets händelser som inträffar i ett AKS-kluster, till exempel nätverks data, process skapande och Kubernetes Gransknings logg. Avgöra om den här aktiviteten är förväntad eller om programmet är felgenererat. Använd mått och loggar i Azure Monitor för att styrka dina resultat. 

- [Förstå Azure Kubernetes Services-integrering med Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [Så här aktiverar du Azure Security Center standard nivån](../security-center/security-center-get-started.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: installera och aktivera Microsofts program mot skadlig kod för Azure till Azure Kubernetes service (AKS) virtuella datorer och noder för skalnings uppsättning för virtuella datorer. Granska aviseringar i Security Center för reparation.

- [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Referens guide för säkerhets varningar](../security-center/alerts-reference.md)

- [Aviseringar för behållare – Azure Kubernetes service-kluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: Azure Kubernetes service (AKS) använder CoreDNS-projektet för DNS-hantering och-matchning i kluster.

Aktivera loggning av DNS-frågor genom att använda dokumenterad konfiguration i din coredns-anpassade ConfigMap. 

- [Anpassa CoreDNS med Azure Kubernetes Service](coredns-custom.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Använd kubectl, en kommando rads klient, i Azure Kubernetes service (AKS) för att hantera ett Kubernetes-kluster och hämta dess loggar från AKS-noden i fel söknings syfte. Kubectl är redan installerat om du använder Azure Cloud Shell. Om du vill installera kubectl lokalt använder du cmdleten Install-AzAksKubectl.

- [Snabb start – distribuera ett Azure Kubernetes service-kluster med PowerShell](kubernetes-walkthrough-powershell.md)

- [Hämta kubelet-loggar från AKS-klusternoder (Azure Kubernetes Service)](kubelet-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: i själva Azure Kubernetes service (AKS) finns ingen lösning för identitets hantering som lagrar vanliga användar konton och lösen ord. Med Azure Active Directory-integration (Azure AD) kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namn område eller över klustret.

Utför ad hoc-frågor för att identifiera konton som är medlemmar i AKS administrativa grupper med Azure AD PowerShell-modulen

Använd Azure CLI för åtgärder som "Hämta autentiseringsuppgifter för ett hanterat Kubernetes-kluster" för att hjälpa till med att stämma av åtkomsten med jämna mellanrum. Implementera den här processen för att behålla en uppdaterad inventering av tjänst kontona, som är en annan primär användar typ i AKS. Använd Security Centers rekommendationer för identitets-och åtkomst hantering.

- [Integrera AKS med Azure AD](azure-ad-integration-cli.md)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Kubernetes service (AKS) har inte begreppet vanliga standard lösen ord och ger ingen identitets hanterings lösning där vanliga användar konton och lösen ord kan lagras. Med Azure Active Directory (Azure AD)-integration kan du ge rollbaserad åtkomst till AKS-resurser inom ett namn område eller mellan klustret. 

Utför ad hoc-frågor för att identifiera konton som är medlemmar i AKS administrativa grupper med Azure AD PowerShell-modulen

- [Förstå åtkomst-och identitets alternativ för AKS](concepts-identity.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: integrera användarautentisering för dina Azure Kubernetes service-kluster (AKS) med Azure Active Directory (Azure AD). Logga in på ett AKS-kluster med en Azure AD-autentiseringstoken. Konfigurera Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) för administrativ åtkomst till Kubernetes konfiguration (kubeconfig) information och behörigheter, namn rymder och kluster resurser. 

Skapa principer och procedurer kring användningen av dedikerade administrativa konton. Implementera rekommendationer för Security Center identitets-och åtkomst hantering.

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

- [Kontrol lera åtkomst till kluster resurser](azure-ad-rbac.md)

- [Använda Azures rollbaserade åtkomst kontroller](control-kubeconfig-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd enkel inloggning för Azure Kubernetes service (AKS) med Azure Active Directory (Azure AD) integrerad autentisering för ett AKS-kluster.

- [Så här visar du Kubernetes-loggar, händelser och Pod-mått i real tid](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: integrera autentisering för Azure Kubernetes service (AKS) med Azure Active Directory (Azure AD). 

Aktivera Azure AD multifaktor-autentisering och följ Security Center rekommendationer för identitets-och åtkomst hantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Multi-Factor Authentication (MFA) som kon figurer ATS för att logga in på dina angivna Azure Kubernetes service (AKS)-kluster och relaterade resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera Multi-Factor Authentication (MFA) i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter med Azure AD-integrerad autentisering för Azure Kubernetes service (AKS). Aviseringar kan genereras när misstänkt eller osäker aktivitet inträffar i miljön. Använd Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: använda villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Kubernetes service-kluster (AKS) från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner. Detta kräver integrerad autentisering för AKS med Azure Active Directory (Azure AD).

Begränsa åtkomsten till AKS-API-servern från en begränsad uppsättning IP-adressintervall, eftersom den tar emot begär Anden om att utföra åtgärder i klustret för att skapa resurser eller skala antalet noder. 

- [Säker åtkomst till API-servern med behöriga IP-adressintervall i Azure Kubernetes service (AKS)](api-server-authorized-ip-ranges.md)

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som den centrala autentiserings-och auktoriserings systemet för Azure Kubernetes service (AKS). Azure AD skyddar data med stark kryptering för data i vila och under överföring och salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Använd de inbyggda rollerna AKS med rollbaserad åtkomst kontroll i Azure (Azure RBAC) – resurs princip deltagare och ägare för princip tilldelnings åtgärder till ditt Kubernetes-kluster

- [Översikt över Azure Policy](../governance/policy/overview.md)

- [Så här integrerar du Azure AD med AKS](azure-ad-integration-cli.md)

- [Integrera AKS-hanterad Azure AD](managed-aad.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter med Azure AD-integrerad autentisering för Azure Kubernetes service (AKS). Sök i Azure AD-loggar för att identifiera inaktuella konton. 

Utför granskningar av Azure Identity Access för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Åtgärda identitets-och åtkomst rekommendationer från Security Center.

Tänk på vilka roller som används för support eller fel söknings syfte. Till exempel görs alla kluster åtgärder som Microsoft-supporten har vidtagit (med användar medgivande) under en inbyggd Kubernetes "redigera"-roll för namnet AKS-support-rolebinding. Stöd för AKS har Aktiver ATS med den här rollen för att redigera kluster konfiguration och resurser för att felsöka och diagnostisera kluster problem. Den här rollen kan dock inte ändra behörigheter eller skapa roller eller roll bindningar. Den här roll åtkomsten är endast aktive rad under aktiva support biljetter med JIT-åtkomst (just-in-Time).
 
- [Åtkomst och identitetsalternativ för Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

- [Så här övervakar du användarens identitets-och åtkomst aktivitet i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: integrera användarautentisering för Azure Kubernetes service (AKS) med Azure Active Directory (Azure AD). Skapa diagnostiska inställningar för Azure AD och skicka gransknings-och inloggnings loggarna till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel när ett inaktiverat konto försöker logga in) i en Azure Log Analytics-arbetsyta.
- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: integrera användarautentisering för Azure Kubernetes service (AKS) med Azure Active Directory (Azure AD). Använd Azure ADs funktion för identifiering av risker och identitets skydd för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Mata in data i Azure Sentinel för ytterligare undersökningar baserat på affärs behov.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för resurser som är relaterade till AKS-distributioner (Azure Kubernetes service) som hjälper dig att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Uppdatera taggar för hanterade kluster](/rest/api/aks/managedclusters/updatetags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: det är logiskt att isolera team och arbets belastningar i samma kluster med Azure Kubernetes service (AKS) för att ge det minsta antalet behörigheter som är begränsat till de resurser som krävs av varje team. 

Använd namn området i Kubernetes för att skapa en logisk isolerings gränser. Överväg att implementera ytterligare Kubernetes-funktioner för isolering och flera innehavare, till exempel schemaläggning, nätverk, autentisering/auktorisering och behållare.

Implementera separata prenumerationer och/eller hanterings grupper för utvecklings-, test-och produktions miljöer. Separera AKS-kluster med nätverk genom att distribuera dem till specifika virtuella nätverk, som är korrekt märkta.

- [Lär dig mer om metod tips för kluster isolering i AKS](operator-best-practices-cluster-isolation.md)

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Förstå metod tips för nätverks anslutning och säkerhet i AKS](operator-best-practices-network.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Använd en lösning från tredje part från Azure Marketplace om nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar, samtidigt som du varnar information om säkerhets tekniker.

Microsoft hanterar den underliggande plattformen och hanterar allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Lista över obligatoriska portar, adresser och domän namn för AKS-funktioner](limit-egress-traffic.md)

- [Konfigurera diagnostikinställningar för Azure-brandväggen](../firewall/firewall-diagnostics.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: skapa en https-styrenhet för ingångs kontroll och Använd dina egna TLS-certifikat (Alternativt kan vi kryptera) för distributioner av Azure Kubernetes service (AKS). 

Kubernetes utgående trafik krypteras via HTTPS/TLS som standard. Granska eventuell okrypterad utgående trafik från AKS-instanserna för ytterligare övervakning. Detta kan omfatta NTP-trafik, DNS-trafik, HTTP-trafik för att hämta uppdateringar i vissa fall. 

- [Så här skapar du en HTTPS ingress-styrenhet på AKS och använder dina egna TLS-certifikat](ingress-own-tls.md)

- [Så här skapar du en HTTPS ingångs kontroll på AKS med kryptera](ingress-tls.md)

- [Lista över potentiella portar och protokoll som används av AKS](limit-egress-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.
Microsoft hanterar den underliggande plattformen och hanterar allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. 

För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: Använd det Azure-auktoriserings system för rollbaserad åtkomst kontroll (Azure RBAC) som byggts på Azure Resource Manager för att ge detaljerad åtkomst hantering av Azure-resurser.

Konfigurera Azure Kubernetes service (AKS) för att använda Azure Active Directory (Azure AD) för användarautentisering. Logga in på ett AKS-kluster med Azure AD-autentiseringstoken med den här konfigurationen. 

Använd de inbyggda rollerna AKS med Azure RBAC – resurs princip deltagare och ägare för princip tilldelnings åtgärder till ditt AKS-kluster

- [Så här kontrollerar du åtkomsten till kluster resurser med Azure RBAC-och Azure AD-identiteter i AKS](azure-ad-rbac.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. container service**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.
Microsoft hanterar den underliggande plattformen och hanterar allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: de två primära typerna av lagring som tillhandahålls för volymer i Azure Kubernetes service (AKS) backas upp av Azure-diskar eller Azure Files. Båda typerna av lagring använder Azure Storage tjänst kryptering (SSE) som krypterar data i vila för att förbättra säkerheten. Som standard krypteras data med Microsoft-hanterade nycklar.

Kryptering – rest med Kundhanterade nycklar är tillgängligt för kryptering av både operativ system-och data diskar på AKS-kluster för ytterligare kontroll över krypterings nycklar. Kunder äger ansvaret för viktiga hanterings aktiviteter, till exempel säkerhets kopiering och rotation av nycklar. Diskar kan för närvarande inte krypteras med hjälp av Azure Disk Encryption på AKS Node-nivå.

- [Metod tips för lagring och säkerhets kopiering i Azure Kubernetes service (AKS)](operator-best-practices-storage.md)

- [Ta med dina egna nycklar (BYOK) med Azure-diskar i Azure Kubernetes service (AKS)](azure-disk-customer-managed-keys.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor för behållare för att övervaka prestanda för behållar arbets belastningar som distribueras till hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). 

Konfigurera aviseringar för proaktiv avisering eller logg skapande när processor-och minnes användning på noder eller behållare överskrider definierade tröskelvärden, eller när en hälso tillstånds ändring sker i klustret på infrastruktur eller noders hälso insamling. 

Använd Azure aktivitets logg för att övervaka AKS-kluster och relaterade resurser på en hög nivå. Integrera med Prometheus för att visa program-och arbets belastnings mått som samlas in från noder och Kubernetes med hjälp av frågor för att skapa anpassade aviseringar, instrument paneler och detaljerad analys.

- [Förstå Azure Monitor för behållare](/azure/azure-monitor/insights/container-insights-overview)

- [Aktivera Azure Monitor för behållare](/azure/azure-monitor/insights/container-insights-onboard)

- [Visa och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Använd Security Center för att övervaka Azure Container Registry inklusive Azure Kubernetes service (AKS)-instanser för sårbarheter. Aktivera paket för behållar register i Security Center för att säkerställa att Security Center är redo att skanna bilder som skickas till registret.

Bli informerad i Security Center instrument panelen när problem upptäcks när Security Center skannar avbildningen med Qualys. Paket funktionen för behållar register ger djupare insyn i sårbarheter av de avbildningar som används i Azure Resource Managerbaserade register. 

Använd Security Center för åtgärds bara rekommendationer för varje sårbarhet. Dessa rekommendationer innehåller en allvarlighets grad och vägledning för reparation. 

- [Metod tips för hantering av behållar avbildningar och säkerhet i Azure Kubernetes service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Lär dig metod tips för hantering av behållar avbildningar och säkerhet i AKS](operator-best-practices-container-image-management.md)

- [Förstå behållar register integrering med Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: säkerhets uppdateringar tillämpas automatiskt på Linux-noder för att skydda kundens Azure Kubernetes service-kluster (AKS). Dessa uppdateringar omfattar säkerhets korrigeringar för operativ system eller kernel-uppdateringar. 

Observera att processen att behålla Windows Server-noderna skiljer sig från noder som kör Linux eftersom Windows Server-noder inte får dagliga uppdateringar. Kunder måste i stället utföra en uppgradering på Windows Server-nodkonfigurationer i sina AKS-kluster som distribuerar nya noder med den senaste Server avbildningen och korrigeringarna för bas fönstret med hjälp av Azures kontroll panel eller Azure CLI. De här uppdateringarna innehåller förbättringar av säkerhet eller funktioner för AKS.

- [Förstå hur uppdateringar tillämpas på AKS-klusternoder som kör Linux](node-updates-kured.md)

- [Så här uppgraderar du en AKS Node-pool för AKS-kluster som använder Windows Server-noder](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Uppgraderingar för Azure Kubernetes service (AKS) Node-avbildning](node-image-upgrade.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera en automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: implementera en manuell process för att säkerställa att program från tredje part i Azure Kubernetes service (AKS) har uppdaterats under klustrets giltighets tid. Detta kan kräva att du aktiverar automatiska uppdateringar, övervakar noderna eller utför regelbunden omstarter.

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. container service**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: exportera Security Center Sök resultat med jämna mellanrum och jämför resultaten för att kontrol lera att sårbarheter har åtgärd ATS.

Använd PowerShell-cmdleten "Get-AzSecurityTask" för att automatisera hämtningen av säkerhets uppgifter som Security Center rekommenderar att du utför för att förbättra din säkerhets position och korrigerings genomsöknings resultat.

- [Så här använder du PowerShell för att Visa sårbarheter som identifierats av Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd den allvarlighets grad som tillhandahålls av Security Center för att prioritera reparationer av sårbarheter. 

Använd vanliga CVSS (sårbarhets bedömnings system) (eller ett annat bedömnings system som tillhandahålls av ditt skannings verktyg) om du använder ett inbyggt verktyg för sårbarhets bedömning (till exempel Qualys eller Rapid7, som erbjuds av Azure).

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk och så vidare) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager-baserade resurser som går framåt.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser med metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. 

Använd utsmaker, etiketter eller taggar när du skapar en Azure Kubernetes service (AKS)-Node-pool. Alla noder i den noden kommer också att ärva den här typen, etiketten eller taggen.

Bismakar, etiketter eller taggar kan användas för att stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationer inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Hanterade kluster – uppdatera Taggar](/rest/api/aks/managedclusters/updatetags)

- [Ange en smak, etikett eller tagg för en Node-pool](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: definiera en lista över godkända Azure-resurser och godkänd program vara för beräknings resurser baserat på organisationens affärs behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
-   Otillåtna resurstyper 

-   Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns baserat på organisationens affärs behov.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: använd Azure Automation ändringsspårning-och inventerings funktioner för att ta reda på program vara som är installerad i din miljö. 

Samla in och Visa inventering för program vara, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer och övervakaren för program som inte godkänts. 

Spåra konfigurationerna för dina datorer för att hjälpa till med att hitta operativa problem i din miljö och bättre förstå datorernas tillstånd.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: använd Azure Automation ändringsspårning-och inventerings funktioner för att ta reda på program vara som är installerad i din miljö. 

Samla in och Visa inventering för program vara, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer och övervakaren för program som inte godkänts. 

Spåra konfigurationerna för dina datorer för att hjälpa till med att hitta operativa problem i din miljö och bättre förstå datorernas tillstånd.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

- [Så här använder du övervakning av fil integritet](../security-center/security-center-file-integrity-monitoring.md)

- [Förstå Azure Ändringsspårning](../automation/change-tracking/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: använd Azure Automation ändringsspårning-och inventerings funktioner för att ta reda på program vara som är installerad i din miljö. 

Samla in och Visa inventering för program vara, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer och övervakaren för program som inte godkänts. 

Spåra konfigurationerna för dina datorer för att hjälpa till med att hitta operativa problem i din miljö och bättre förstå datorernas tillstånd.

Aktivera anpassningsbar program analys i Security Center för program som finns i din miljö.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer med hjälp av inbyggda princip definitioner.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".
- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: Azure Kubernetes service (AKS) tillhandahåller inte en identitets hanterings lösning där vanliga användar konton och lösen ord lagras. Använd i stället Azure Active Directory (Azure AD) som integrerad identitets lösning för dina AKS-kluster.

Bevilja användare eller grupper åtkomst till Kubernetes-resurser inom ett namn område eller över klustret med hjälp av Azure AD-integrering.

Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i dina AKS-administrativa grupper och Använd dem för att stämma av åtkomsten med jämna mellanrum. Använd Azure CLI för åtgärder som till exempel get Access-autentiseringsuppgifter för ett hanterat Kubernetes-kluster. Implementera rekommendationer för Security Center identitets-och åtkomst hantering.

- [Hantera AKS med Azure CLI](/cli/azure/aks)

- [Förstå AKS och Azure AD-integrering](concepts-identity.md)

- [Integrera AKS med Azure AD](azure-ad-integration-cli.md)

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: Använd Azure Kubernetes service (AKS)-funktioner för att logiskt isolera team och arbets belastningar i samma kluster för det minsta antalet privilegier, som är begränsade till de resurser som krävs av varje team. 

Implementera namn område i Kubernetes för att skapa en logisk isolerings gränser. Använd Azure Policy alias i namn området "Microsoft. container service" om du vill skapa anpassade principer för att granska eller framtvinga konfigurationen av AKS-instanser (Azure Kubernetes service). 

Granska och implementera ytterligare Kubernetes-funktioner och överväganden för isolering och flera innehavare för att inkludera följande: schemaläggning, nätverk, autentisering/auktorisering och behållare. Använd också separata prenumerationer och hanterings grupper för utveckling, testning och produktion. Separera AKS-kluster med virtuella nätverk, undernät som är korrekt taggade och säkra med en brand vägg för webbaserade program (WAF).

- [Lär dig mer om metod tips för kluster isolering i AKS](operator-best-practices-cluster-isolation.md)

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Förstå metod tips för nätverks anslutning och säkerhet i AKS](operator-best-practices-network.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. container service" om du vill skapa anpassade principer för att granska eller framtvinga konfigurationen av AKS-instanser (Azure Kubernetes service). Använd inbyggda Azure Policys definitioner.

Exempel på inbyggda princip definitioner för AKS är:

- Använder inkommande HTTPS i Kubernetes-kluster

- Auktoriserade IP-intervall ska definieras på Kubernetes Services

- Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services

- Ser till att endast tillåtna containeravbildningar finns i Kubernetes-klustret

Exportera en mall med AKS-konfigurationen i JavaScript Object Notation (JSON) med Azure Resource Manager. Granska den regelbundet för att säkerställa att dessa konfigurationer uppfyller säkerhets kraven för din organisation. Använd rekommendationerna från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser. 

- [Så här konfigurerar och hanterar du AKS-Pod säkerhets principer](use-pod-security-policies.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Azure Kubernetes Clusters (AKS)-kluster distribueras på virtuella dator värdar med ett säkerhetsoptimerat operativ system. Värd operativ systemet har ytterligare säkerhets härdnings steg som ingår i IT för att minska angrepps områdets yta och tillåta distribution av behållare på ett säkert sätt. 

Azure använder dagliga korrigeringar (inklusive säkerhets korrigeringar) för att AKS virtuella dator värdar med vissa korrigeringar som kräver en omstart. Kunderna ansvarar för schemaläggning av AKS virtuella dator värden efter behov. 

- [Säkerhets härdning för AKS-nodens värd operativ system](security-hardened-vm-host-image.md)

- [Förstå säkerhets härdning i AKS virtuella dator värdar](security-hardened-vm-host-image.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: skydda ditt Azure Kubernetes service-kluster (AKS) med hjälp av Pod säkerhets principer.  Begränsa vilka poddar som kan schemaläggas för att förbättra säkerheten för klustret. 

Poddar som begär resurser som inte tillåts kan inte köras i AKS-klustret. 

Använd också Azure Policy [neka] och [distribuera om det inte finns]-effekter för att framtvinga säkra inställningar för Azure-resurser som är relaterade till dina AKS-distributioner (till exempel virtuella nätverk, undernät, Azure-brandväggar, lagrings konton och så vidare). 

Skapa anpassade Azure Policy definitioner med hjälp av alias från följande namn rymder: 

- Microsoft. container service

- Microsoft.Network

Ytterligare information finns på de länkar som refereras till.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Azure Kubernetes service-kluster (AKS) distribueras på virtuella värd datorer med ett säkerhetsoptimerat operativ system. Värd operativ systemet har ytterligare säkerhets härdnings steg som ingår i IT för att minska angrepps områdets yta och tillåta distribution av behållare på ett säkert sätt. 

Se listan över Center for Internet Security (CIS)-kontroller som är inbyggda i värd operativ systemet.  

- [Säkerhets härdning för AKS-nodens värd operativ system](security-hardened-vm-host-image.md)

- [Förstå tillstånds konfigurationen för AKS-kluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Förstå säkerhets härdning i AKS virtuella dator värdar](security-hardened-vm-host-image.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-databaser för att lagra och hantera dina konfigurationer på ett säkert sätt om du använder anpassade Azure policys definitioner. Exportera en mall för din Azure Kubernetes service-konfiguration (AKS) i JavaScript Object Notation (JSON) med Azure Resource Manager. Granska den regelbundet för att se till att konfigurationerna uppfyller säkerhets kraven för din organisation.

Implementera lösningar från tredje part, till exempel terraform, för att skapa en konfigurations fil som deklarerar resurserna för Kubernetes-klustret. Du kan förstärka din AKS-distribution genom att implementera rekommenderade säkerhets metoder och lagra konfigurationen som kod på en säker plats.

- [Definiera ett Kubernetes-kluster](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Säkerhets härdning för AKS-nodens värd operativ system](security-hardened-vm-host-image.md)

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: gäller inte för Azure Kubernetes service (AKS). AKS tillhandahåller ett säkerhetsoptimerat värd operativ system (OS) som standard. Det finns inget aktuellt alternativ för att välja ett alternativt eller anpassat operativ system.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i prenumerationer med hjälp av inbyggda princip definitioner samt Azure policy alias i namn området "Microsoft. container service". 

Skapa anpassade principer för granskning och Använd system konfigurationer. Utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: Azure Kubernetes service-kluster (AKS) distribueras på virtuella värd datorer med ett säkerhetsoptimerat operativ system. Värd operativ systemet har ytterligare säkerhets härdnings steg som ingår i IT för att minska angrepps områdets yta och tillåta distribution av behållare på ett säkert sätt. 

Se listan över Center for Internet Security (CIS)-kontroller som är inbyggda i AKS-värdar.  

- [Säkerhets härdning för AKS-nodens värd operativ system](security-hardened-vm-host-image.md)

- [Förstå säkerhets härdning i AKS virtuella dator värdar](security-hardened-vm-host-image.md)

- [Förstå tillstånds konfigurationen för AKS-kluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Security Center för att utföra bas linje genomsökningar för resurser som är relaterade till dina AKS-distributioner (Azure Kubernetes service). Exempel resurserna inkluderar, men är inte begränsade till själva AKS-klustret, det virtuella nätverket där AKS-klustret distribuerades, det Azure Storage konto som används för att spåra terraform-tillstånd eller Azure Key Vault instanser som används för krypterings nycklarna för ditt AKS-klusters operativ system och data diskar.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: Använd Security Center container rekommendationer under avsnittet "Compute &amp; Apps" för att utföra bas linje genomsökningar för dina Azure Kubernetes service-kluster (AKS). 

Få ett meddelande i Security Center-instrumentpanelen när det finns konfigurations problem eller säkerhets problem. Detta kräver att du aktiverar de valfria paketen för behållar register som gör det möjligt att Security Center Skanna avbildningen.  

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: integrera Azure Key Vault med ett Azure Kubernetes service-kluster (AKS) med hjälp av en FlexVolume-enhet. Använd Azure Key Vault för att lagra och regelbundet rotera hemligheter som autentiseringsuppgifter, lagrings konto nycklar eller certifikat. Med FlexVolume-drivrutinen kan AKS-klustret Hämta autentiseringsuppgifter från Key Vault och på ett säkert sätt förse dem med den begär ande pod. Använd en POD-hanterad identitet för att begära åtkomst till Key Vault och hämta de autentiseringsuppgifter som krävs via FlexVolume-drivrutinen. Se till att Key Vault mjuk borttagning har Aktiver ATS. 

Begränsa exponering av autentiseringsuppgifter genom att inte definiera autentiseringsuppgifter i program koden. 

Undvik att använda fasta eller delade autentiseringsuppgifter. 

- [Säkerhetsbegrepp för program och kluster i AKS (Azure Kubernetes Service)](concepts-security.md)

- [Använda Key Vault med ditt AKS-kluster](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: definiera inte autentiseringsuppgifter i program koden som en säkerhets metod. Använd hanterade identiteter för Azure-resurser för att låta en POD autentisera sig mot alla tjänster i Azure som stöder det, inklusive Azure Key Vault. Pod tilldelas en Azure-identitet för att autentisera till Azure Active Directory (Azure AD) och ta emot en digital token som kan visas för andra Azure-tjänster som kontrollerar om Pod har behörighet att komma åt tjänsten och utföra de åtgärder som krävs.

Observera att Pod Managed Identities endast är avsedda för användning med Linux poddar och behållar avbildningar. Etablera Azure Key Vault för att lagra och hämta digitala nycklar och autentiseringsuppgifter. Nycklar, till exempel de som används för att kryptera OS-diskar, AKS kluster data kan lagras i Azure Key Vault.

Tjänstens huvud namn kan också användas i AKS-kluster. Kluster som använder tjänstens huvud namn kan dock komma att uppnå ett tillstånd där tjänstens huvud namn måste förnyas för att hålla klustret igång. Hantering av tjänstens huvud namn ökar komplexiteten, vilket innebär att det är lättare att använda hanterade identiteter i stället. Samma behörighets krav gäller för både tjänstens huvud namn och hanterade identiteter.

- [Förstå hanterade identiteter och Key Vault med Azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Azure AD Pod-identitet](https://github.com/Azure/aad-pod-identity)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Med scannern för autentiseringsuppgifter kan du också flytta identifierade autentiseringsuppgifter till säkrare platser, till exempel Azure Key Vault med rekommendationer.

Begränsa exponering av autentiseringsuppgifter genom att inte definiera autentiseringsuppgifter i program koden. och Undvik att använda delade autentiseringsuppgifter. Azure Key Vault ska användas för att lagra och hämta digitala nycklar och autentiseringsuppgifter. Använd hanterade identiteter för Azure-resurser för att låta din POD begära åtkomst till andra resurser. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Metod tips för utvecklare för Pod-säkerhet](developer-best-practices-pod-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Använd centralt hanterad program vara för program mot skadlig kod

**Vägledning**: AKS hanterar livs cykeln och åtgärder för agent-noderna för din räkning – det går inte att ändra de IaaS-resurser som är kopplade till agent-noderna. För Linux-noder kan du dock använda daemon-uppsättningar för att installera anpassad program vara som en lösning mot skadlig kod.

- [Referens guide för säkerhets varningar](../security-center/alerts-reference.md)

- [Aviseringar för behållare – Azure Kubernetes service-kluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS delat ansvar och daemon-uppsättningar](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: för skanning av filer som laddas upp till dina AKS-resurser. Använd Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton om ett Azure Storage konto används som ett data lager eller för att spåra terraform tillstånd för ditt AKS-kluster. 

- [Förstå Azure Security Centers hot identifiering för data tjänster](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för program mot skadlig kod uppdateras

**Vägledning**: AKS hanterar livs cykeln och åtgärder för agent-noderna för din räkning – det går inte att ändra de IaaS-resurser som är kopplade till agent-noderna. För Linux-noder kan du dock använda daemon-uppsättningar för att installera anpassad program vara som en lösning mot skadlig kod.

- [Referens guide för säkerhets varningar](../security-center/alerts-reference.md)

- [Aviseringar för behållare – Azure Kubernetes service-kluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS delat ansvar och daemon-uppsättningar](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: säkerhetskopiera dina data med hjälp av ett lämpligt verktyg för din lagrings typ, till exempel Velero, som kan säkerhetskopiera beständiga volymer tillsammans med ytterligare kluster resurser och konfigurationer. Kontrol lera regelbundet integriteten och säkerheten för dessa säkerhets kopior. 

Ta bort tillstånd från dina program innan du säkerhetskopierar. I de fall där det inte går att göra kan du säkerhetskopiera data från permanenta volymer och regelbundet testa återställnings åtgärderna för att kontrol lera data integriteten och de processer som krävs.

- [Metod tips för lagring och säkerhets kopiering i AKS](operator-best-practices-storage.md)

- [Metod tips för verksamhets kontinuitet och haveri beredskap i AKS](operator-best-practices-multi-region.md)

- [Förstå Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Konfigurera Velero på Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: säkerhetskopiera dina data med hjälp av ett lämpligt verktyg för din lagrings typ, till exempel Velero, som kan säkerhetskopiera beständiga volymer tillsammans med ytterligare kluster resurser och konfigurationer. 

Utföra vanliga automatiserade säkerhets kopieringar av Key Vault certifikat, nycklar, hanterade lagrings konton och hemligheter med PowerShell-kommandon. 

- [Säkerhetskopiera Key Vault certifikat](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Säkerhetskopiera Key Vault nycklar](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Säkerhetskopiera Key Vault hanterade lagrings konton](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Säkerhetskopiera Key Vault hemligheter](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Så här aktiverar du Azure Backup](/azure/backup/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: regelbundet utföra Data återställning av innehåll i Velero-säkerhetskopiering. Om det behövs kan du testa att återställa till ett isolerat virtuellt nätverk.

Regelbundet utföra Data återställning av Key Vault certifikat, nycklar, hanterade lagrings konton och hemligheter med PowerShell-kommandon.

- [Återställa Key Vault-certifikat](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

- [Återställa Key Vault nycklar](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Återställa Key Vault hanterade lagrings konton](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Så här återställer du Key Vault hemligheter](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-4.8.0&amp;preserve-view=true)

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](/azure/backup/backup-azure-restore-files-from-vm)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: säkerhetskopiera dina data med hjälp av ett lämpligt verktyg för din lagrings typ, till exempel Velero, som kan säkerhetskopiera beständiga volymer tillsammans med ytterligare kluster resurser och konfigurationer. 

Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning om Azure Key Vault används med för Azure Kubernetes service (AKS)-distributioner.

- [Förstå Azure Storage tjänst kryptering](../storage/common/storage-service-encryption.md)

- [Så här aktiverar du Soft-Delete i Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST för dator säkerhets incident hanterings guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: prioritera vilka aviseringar som måste undersökas först med Security Center tilldelade allvarlighets grad till aviseringar. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.
Markera prenumerationer tydligt (till exempel produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems svar på incident hantering vid en vanlig takt. Identifiera svaga punkter och luckor och omarbeta incident svars planer efter behov.

- [Guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. 

Granska incidenter, efter faktum, för att säkerställa att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av den löpande export funktionen. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. 

Välj Security Center Data Connector för att strömma aviseringarna till Azure Sentinel, enligt behov och utifrån organisationens affärs behov.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Ytterligare information om Microsofts strategi och körning av röda sambands-och inträngande webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program, på de länkar som refereras till.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
