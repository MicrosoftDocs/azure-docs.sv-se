---
title: Azure-säkerhetsbaslinje för Azure Kubernetes Service
description: Säkerhetsbaslinjen Azure Kubernetes Service innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0564f1f39ac9d492dfffdf0e7adacdde08db0874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769597"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure-säkerhetsbaslinje för Azure Kubernetes Service

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Azure Kubernetes. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas av de säkerhetskontroller **som definieras** av Azure Security Benchmark och tillhörande vägledning för Azure Kubernetes. **Kontroller** som inte är tillämpliga för Azure Kubernetes, eller för vilka ansvaret är Microsofts, har undantagits.

Om du vill se hur Azure Kubernetes helt mappar till Azure Security Benchmark kan du se den fullständiga mappningsfilen för [säkerhetsbaslinjen i Azure Kubernetes.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Som standard skapas en nätverkssäkerhetsgrupp och en vägtabell automatiskt när du skapar ett Microsoft Azure Kubernetes Service-kluster (AKS). AKS ändrar automatiskt nätverkssäkerhetsgrupper för lämpligt trafikflöde när tjänster skapas med lastbalanserare, portmappningar eller ingressvägar. Nätverkssäkerhetsgruppen associeras automatiskt med de virtuella nätverkskorten på kundnoder och vägtabellen med undernätet i det virtuella nätverket. 

Använd AKS-nätverksprinciper för att begränsa nätverkstrafik genom att definiera regler för in- och utgående trafik mellan Linux-poddar i ett kluster baserat på val av namnrymder och etikettväljare. Användning av nätverksprincip kräver Azure CNI plugin-program med definierade virtuella nätverk och undernät och kan endast aktiveras när klustret skapas. De kan inte distribueras i ett befintligt AKS-kluster.

Du kan implementera ett privat AKS-kluster för att säkerställa att nätverkstrafiken mellan AKS API-servern och nodpoolerna bara finns i det privata nätverket. Kontrollplanet eller API-servern finns i en AKS-hanterad Azure-prenumeration och använder interna (RFC1918) IP-adresser, medan kundens kluster eller nodpool finns i sin egen prenumeration. Servern och klustret eller nodpoolen kommunicerar med varandra med hjälp av Azure Private Link-tjänsten i det virtuella API-servernätverket och en privat slutpunkt som exponeras i undernätet i kundens AKS-kluster.  Du kan också använda en offentlig slutpunkt för AKS API-servern men begränsa åtkomsten med AKS API-serverns funktion auktoriserade IP-intervall. 

- [Säkerhetsbegrepp för program och kluster i AKS (Azure Kubernetes Service)](concepts-security.md)

- [Skydda trafik mellan poddar med hjälp av nätverksprinciper i Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Skapa ett privat Azure Kubernetes Service kluster](private-clusters.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverkskort

**Vägledning:** Använd Security Center och följ dess rekommendationer för nätverksskydd för att skydda de nätverksresurser som används av dina Azure Kubernetes Service-kluster (AKS). 

Aktivera flödesloggar för nätverkssäkerhetsgruppen och skicka loggarna till ett Azure Storage konto för granskning. Du kan också skicka flödesloggarna till en Log Analytics-arbetsyta och sedan använda Trafikanalys för att ge insikter om trafikmönster i Azure-molnet för att visualisera nätverksaktivitet, identifiera hotpunkter och säkerhetshot, förstå trafikflödesmönster och hitta felaktiga nätverkskonfigurationer.

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Så här aktiverar du flödesloggar för nätverkssäkerhet](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Använd en Azure Application Gateway-aktiverad Web Application Firewall (WAF) framför ett AKS-kluster för att ge ett extra säkerhetslager genom att filtrera inkommande trafik till dina webbprogram. Azure WAF använder en uppsättning regler som tillhandahålls av The Open Web Application Security Project (OWASP) för attacker, till exempel skriptangrepp mellan webbplatser eller cookie-gifter mot den här trafiken. 

Använd en API-gateway för autentisering, auktorisering, begränsning, cachelagring, transformering och övervakning av API:er som används i AKS-miljön. En API-gateway fungerar som en ytterdörr till mikrotjänster, frikopplar klienter från dina mikrotjänster och minskar komplexiteten för dina mikrotjänster genom att ta bort belastningen på hanteringen av övergripande problem.

- [Förstå metodtips för nätverksanslutning och säkerhet i AKS](operator-best-practices-network.md)

- [Application Gateway ingress-kontrollant ](../application-gateway/ingress-controller-overview.md)

- [Använda Azure API Management med mikrotjänster som distribuerats i Azure Kubernetes Service](../api-management/api-management-kubernetes.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera Microsoft Distributed Denial-of-Service (DDoS) Standardskydd på de virtuella nätverk där Azure Kubernetes Service-komponenter (AKS) distribueras för skydd mot DDoS-attacker.

Installera nätverksprincipmotorn och skapa Kubernetes-nätverksprinciper för att styra trafikflödet mellan poddar i AKS som standard tillåts all trafik mellan dessa poddar. Nätverksprincipen bör endast användas för Linux-baserade noder och poddar i AKS. Definiera regler som begränsar poddkommunikationen för bättre säkerhet. 

Välj att tillåta eller neka trafik baserat på inställningar som tilldelade etiketter, namnområde eller trafikport. De nätverksprinciper som krävs kan tillämpas automatiskt när poddar skapas dynamiskt i ett AKS-kluster. 

- [Skydda trafik mellan poddar med hjälp av nätverksprinciper i Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Använd Network Watcher paketinfångst som krävs för att undersöka avvikande aktivitet. 

Network Watcher aktiveras automatiskt i det virtuella nätverkets region när du skapar eller uppdaterar ett virtuellt nätverk i din prenumeration. Du kan också skapa nya instanser av Network Watcher med hjälp av PowerShell, Azure CLI, REST API eller Azure Resource Manager Client-metoden

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Skydda ditt Azure Kubernetes Service (AKS)-kluster med en Azure Application Gateway aktiverad med en Web Application Firewall (WAF). 

Om intrångsidentifiering och/eller skydd baserat på nyttolastgranskning eller beteendeanalys inte är ett krav, kan en Azure Application Gateway med WAF användas och konfigureras i "identifieringsläge" för att logga aviseringar och hot, eller "skyddsläge" för att aktivt blockera identifierade intrång och attacker.

- [Förstå metodtips för att skydda ditt AKS-kluster med en WAF](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Så här distribuerar Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** Använd tjänsttaggar för virtuella nätverk för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper som är associerade Azure Kubernetes Service instanser (AKS). Tjänsttaggar kan användas i stället för specifika IP-adresser när du skapar säkerhetsregler för att tillåta eller neka trafik för motsvarande tjänst genom att ange tjänsttaggnamnet. 

Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Använd en Azure-tagg för nodpooler i ditt AKS-kluster. De skiljer sig från de virtuella nätverkstjänsttaggarna och tillämpas på varje nod i nodpoolen och bevaras genom uppgraderingar. 

- [Förstå och använda tjänsttaggar](../virtual-network/service-tags-overview.md)

- [Förstå NSG:er för AKS](support-policies.md)

- [Kontrollera utgående trafik för klusternoder i Azure Kubernetes Service (AKS)](limit-egress-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer med Azure Policy nätverksresurser som är associerade med dina Azure Kubernetes Service-kluster (AKS). 

Använd Azure Policy alias i namnrymderna "Microsoft.ContainerService" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina AKS-kluster. 

Använd också inbyggda principdefinitioner som är relaterade till AKS, till exempel:

- Auktoriserade IP-intervall ska definieras på Kubernetes Services

- Använder inkommande HTTPS i Kubernetes-kluster

- Se till att tjänsterna endast lyssnar på tillåtna portar i Kubernetes-kluster

Ytterligare information finns på de refererade länkarna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverkssäkerhetsgrupper och andra resurser för trafikflöde till och Azure Kubernetes Service kluster (AKS). Använd fältet "Beskrivning" för enskilda regler för nätverkssäkerhetsgrupp för att ange affärs behov och/eller varaktighet och så vidare för regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy taggningsrelaterade definitionerna, till exempel "Kräv tagg och dess värde" som ser till att alla resurser skapas med taggar och för att ta emot meddelanden om befintliga otaggade resurser.

Välj att tillåta eller neka specifika nätverkssökvägar i klustret baserat på namnområden och etikettväljare med nätverksprinciper. Använd dessa namnrymder och etiketter som beskrivningar för trafikkonfigurationsregler. Använd Azure PowerShell azure-kommandoradsgränssnittet (CLI) för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Azure Policy med CLI](/cli/azure/policy)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till Azure Kubernetes Service-kluster (AKS). 

Skapa aviseringar i Azure Monitor som utlöses när ändringar av viktiga nätverksresurser sker. Alla poster från AzureContainerService-användaren i aktivitetsloggarna loggas som plattformsåtgärder. 

Använd Azure Monitor för att aktivera och fråga loggarna från AKS huvudkomponenterna, kube-apiserver och kube-controller-manager. Skapa och hantera noderna som kör kubelet med containerkörning och distribuera sina program via den hanterade Kubernetes API-servern. 

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Aktivera och granska Kubernetes huvudnodloggar i Azure Kubernetes Service (AKS)](/azure/aks/view-master-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning:** Azure Kubernetes Service (AKS) använder ntp.ubuntu.com för tidssynkronisering, tillsammans med UDP-port 123 och NETWORK Time Protocol (NTP). 

Se till att NTP-servrarna är tillgängliga för klusternoderna om du använder anpassade DNS-servrar. 

- [Förstå NTP-domän- och portkrav för AKS-klusternoder](limit-egress-traffic.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Aktivera granskningsloggar från Huvudkomponenter för Azure Kubernetes Services (AKS), kube-apiserver och kube-controller-manager, som tillhandahålls som en hanterad tjänst. 

- kube-auditaksService: Visningsnamnet i granskningsloggen för kontrollplansåtgärden (från hcpService) 

- masterclient: Visningsnamnet i granskningsloggen för MasterClientCertificate, certifikatet du får från az aks get-credentials 

- nodeclient: Visningsnamnet för ClientCertificate, som används av agentnoder

Aktivera även andra granskningsloggar, till exempel kube-audit. 

Exportera loggarna till Log Analytics eller någon annan lagringsplattform. I Azure Monitor du Använda Log Analytics-arbetsytor för att köra frågor mot och utföra analyser och använda Azure Storage för långsiktig lagring och arkiveringslagring.

Aktivera och publicera dessa data för att Azure Sentinel SIEM från tredje part baserat på organisationens affärskrav.

- [Granska loggschemat inklusive loggroller här](/azure/aks/view-master-logs)

- [Förstå Azure Monitor för containrar](/azure/azure-monitor/insights/container-insights-overview)

- [Så här aktiverar du Azure Monitor för containrar](/azure/azure-monitor/insights/container-insights-onboard)

- [Aktivera och granska Kubernetes huvudnodloggar i Azure Kubernetes Service (AKS)](/azure/aks/view-master-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Använd aktivitetsloggar för att övervaka åtgärder på Azure Kubernetes Service(AKS) för att visa alla aktiviteter och deras status. Bestäm vilka åtgärder som har vidtagits på resurserna i din prenumeration med aktivitetsloggar: 

- vem som startade åtgärden
- när åtgärden inträffade
- status för åtgärden
- värdena för andra egenskaper som kan hjälpa dig att undersöka åtgärden

Hämta information från aktivitetsloggen via Azure PowerShell, Azure-kommandoradsgränssnittet (CLI), Azure REST API eller Azure Portal. 

Aktivera granskningsloggar på AKS-huvudkomponenter, till exempel: 

- kube-auditaksService: Visningsnamnet i granskningsloggen för kontrollplansåtgärden (från hcpService) 

- masterclient: Visningsnamnet i granskningsloggen för MasterClientCertificate, certifikatet du får från az aks get-credentials 

- nodeclient: Visningsnamnet för ClientCertificate, som används av agentnoder

Aktivera även andra granskningsloggar, till exempel kube-audit. 

- [Så här aktiverar och granskar du Kubernetes-huvudnodloggar i AKS](/azure/aks/view-master-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning:** Aktivera automatisk installation av Log Analytics-agenter för att samla in data från AKS-klusternoderna. Aktivera automatisk etablering av Azure Log Analytics Monitoring Agent från Azure Security Center som standard är automatisk etablering inaktiverad. Agenten kan också installeras manuellt. När automatisk etablering är på Security Center Log Analytics-agenten på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Security Center samlar in data från Azure Virtual Machines (VM), VM-skalningsuppsättningar och IaaS-containrar, till exempel Kubernetes-klusternoder, för att övervaka säkerhetsproblem och hot. Data samlas in med hjälp av Azure Log Analytics-agenten, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys. 

Datainsamling krävs för att ge insyn i saknade uppdateringar, felkonfigurerade säkerhetsinställningar för operativsystemet, status för slutpunktsskydd samt hälso- och hotidentifiering.

- [Så här aktiverar du automatisk etablering av Log Analytics-agenten](../security-center/security-center-enable-data-collection.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Registrera dina Azure Kubernetes Service-instanser (AKS) till Azure Monitor och ange motsvarande kvarhållningsperiod för Azure Log Analytics-arbetsytan enligt din organisations efterlevnadskrav. 

- [Så här anger du parametrar för loggbevarande för Log Analytics-arbetsytor](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Registrera dina Azure Kubernetes Service instanser (AKS) för Azure Monitor konfigurera diagnostikinställningar för klustret. 

Använd Azure Monitor Log Analytics-arbetsytan för att granska loggar och köra frågor på loggdata. Azure Monitor-loggar aktiveras och hanteras i Azure Portal eller via CLI och fungerar med både Kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC), Azure RBAC och icke-RBAC-aktiverade AKS-kluster.

Visa loggarna som genererats av AKS-huvudkomponenterna (kube-apiserver och kube-controllermanager) för felsökning av program och tjänster. Aktivera och publicera data för att Azure Sentinel siem från tredje part för centraliserad logghantering och övervakning.

- [Så här aktiverar och granskar du Kubernetes-huvudnodloggar i AKS](/azure/aks/view-master-logs)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Azure Kubernetes Service (AKS) tillsammans med Security Center för att få djupare insyn i AKS-noder. 

Granska Security Center aviseringar om hot och skadlig aktivitet som har identifierats på värden och på klusternivå. Security Center kontinuerlig analys av råa säkerhetshändelser som inträffar i ett AKS-kluster, till exempel nätverksdata, skapande av processer och Kubernetes-granskningsloggen. Kontrollera om den här aktiviteten är förväntat beteende eller om programmet beter sig felaktigt. Använd mått och loggar i Azure Monitor att bekräfta dina resultat. 

- [Förstå Integrering av Azure Kubernetes Services med Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [Så här aktiverar du Azure Security Center Standard-nivå](../security-center/security-center-get-started.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning:** Installera och aktivera Microsoft Anti-malware för Azure för Azure Kubernetes Service (AKS) virtuella datorer och vm-skalningsuppsättningsnoder. Granska aviseringar i Security Center för reparation.

- [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Referensguide för säkerhetsaviseringar](../security-center/alerts-reference.md)

- [Aviseringar för containrar – Azure Kubernetes Service kluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning:** Azure Kubernetes Service (AKS) använder CoreDNS-projektet för hantering och lösning av kluster-DNS.

Aktivera DNS-frågeloggning genom att tillämpa dokumenterad konfiguration i din coredns-custom ConfigMap. 

- [Anpassa CoreDNS med Azure Kubernetes Service](coredns-custom.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning:** Använd kubectl, en kommandoradsklient i Azure Kubernetes Service (AKS) för att hantera ett Kubernetes-kluster och hämta dess loggar från AKS-noden i felsökningssyfte. Kubectl har redan installerats om du använder Azure Cloud Shell. Om du vill installera kubectl lokalt använder du cmdleten Install-AzAksKubectl.

- [Snabbstart – Distribuera ett Azure Kubernetes Service-kluster med PowerShell](kubernetes-walkthrough-powershell.md)

- [Hämta kubelet-loggar från AKS-klusternoder (Azure Kubernetes Service)](kubelet-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Kubernetes Service (AKS) tillhandahåller inte en identitetshanteringslösning som lagrar vanliga användarkonton och lösenord. Med Azure Active Directory (Azure AD) kan du ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namnområde eller i klustret.

Köra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa AKS-grupper med Azure AD PowerShell-modulen

Använd Azure CLI för åtgärder som "Hämta autentiseringsuppgifter för åtkomst för ett hanterat Kubernetes-kluster" för att underlätta regelbunden avstämning av åtkomst. Implementera den här processen för att hålla en uppdaterad inventering av tjänstkontona, som är en annan primär användartyp i AKS. Tillämpa Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här integrerar du AKS med Azure AD](azure-ad-integration-cli.md)

- [Hämta medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord om tillämpligt

**Vägledning:** Azure Kubernetes Service (AKS) har inte begreppet vanliga standardlösenord och tillhandahåller inte en identitetshanteringslösning där vanliga användarkonton och lösenord kan lagras. Med Azure Active Directory (Azure AD) kan du bevilja rollbaserad åtkomst till AKS-resurser inom ett namnområde eller i klustret. 

Köra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa AKS-grupper med Azure AD PowerShell-modulen

- [Förstå åtkomst- och identitetsalternativ för AKS](concepts-identity.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Integrera användarautentisering för dina Azure Kubernetes Service-kluster (AKS) med Azure Active Directory (Azure AD). Logga in på ett AKS-kluster med en Azure AD-autentiseringstoken. Konfigurera kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) för administrativ åtkomst till Kubernetes-konfigurationsinformation (kubeconfig) information och behörigheter, namnrymder och klusterresurser. 

Skapa principer och procedurer kring användningen av dedikerade administrativa konton. Implementera Security Center rekommendationer för identitets- och åtkomsthantering.

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

- [Kontrollera åtkomsten till klusterresurser](azure-ad-rbac.md)

- [Använda rollbaserade åtkomstkontroller i Azure](control-kubeconfig-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning:** Använd enkel inloggning för Azure Kubernetes Service (AKS) med Azure Active Directory (Azure AD) integrerad autentisering för ett AKS-kluster.

- [Visa Kubernetes-loggar, händelser och poddmått i realtid](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomst

**Vägledning:** Integrera autentisering för Azure Kubernetes Service (AKS) med Azure Active Directory (Azure AD). 

Aktivera multifaktorautentisering i Azure AD och följ Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd en PAW (Privileged Access Workstation) med multifaktorautentisering (MFA), konfigurerad för att logga in på dina angivna Azure Kubernetes Service-kluster (AKS) och relaterade resurser.

- [Läs mer om arbetsstationer för privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du Multi-Factor Authentication (MFA) i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och avisering om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använda Azure Active Directory (Azure AD) säkerhetsrapporter med Azure AD-integrerad autentisering för Azure Kubernetes Service (AKS). Aviseringar kan genereras när misstänkt eller osäker aktivitet inträffar i miljön. Använd Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Övervaka användarnas identitets- och åtkomstaktivitet i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Hantera Azure-resurser endast från godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att tillåta åtkomst till Azure Kubernetes Service-kluster (AKS) från endast specifika logiska gruppningar av IP-adressintervall eller länder/regioner. Detta kräver integrerad autentisering för AKS med Azure Active Directory (Azure AD).

Begränsa åtkomsten till AKS API-servern från en begränsad uppsättning IP-adressintervall, eftersom den tar emot begäranden om att utföra åtgärder i klustret för att skapa resurser eller skala antalet noder. 

- [Säker åtkomst till API-servern med auktoriserade IP-adressintervall i Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering för Azure Kubernetes Service (AKS). Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring och salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Använd de inbyggda AKS-rollerna med rollbaserad åtkomstkontroll i Azure (Azure RBAC) – Resursprincipdeltagare och ägare för principtilldelningsåtgärder till kubernetes-klustret

- [Översikt över Azure Policy](../governance/policy/overview.md)

- [Så här integrerar du Azure AD med AKS](azure-ad-integration-cli.md)

- [Integrera AKS-hanterad Azure AD](managed-aad.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Använda Azure Active Directory (Azure AD) säkerhetsrapporter med Azure AD-integrerad autentisering för Azure Kubernetes Service (AKS). Sök i Azure AD-loggar för att identifiera inaktuella konton. 

Utför Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Åtgärda rekommendationer för identitet och åtkomst från Security Center.

Tänk på vilka roller som används i support- eller felsökningssyften. Klusteråtgärder som vidtas av Microsoft Support (med användarmedgivande) görs till exempel under en inbyggd Kubernetes-"redigera"-roll med namnet aks-support-rolebinding. AKS-stöd har aktiverats med den här rollen för att redigera klusterkonfiguration och resurser för att felsöka och diagnostisera klusterproblem. Den här rollen kan dock inte ändra behörigheter eller skapa roller eller rollbindningar. Den här rollåtkomsten aktiveras endast under aktiva supportärenden med JIT-åtkomst (just-in-time).
 
- [Åtkomst och identitetsalternativ för Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

- [Övervaka användarens identitets- och åtkomstaktivitet i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Integrera användarautentisering för Azure Kubernetes Service (AKS) med Azure Active Directory (Azure AD). Skapa diagnostikinställningar för Azure AD och skicka gransknings- och inloggningsloggarna till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel när ett inaktiverat konto försöker logga in) på en Azure Log Analytics-arbetsyta.
- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Skapa, visa och hantera logga aviseringar med hjälp av Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Avisering om kontoinloggningens beteendeavvikelse

**Vägledning:** Integrera användarautentisering för Azure Kubernetes Service (AKS) med Azure Active Directory (Azure AD). Använd funktionen Riskidentifiering och Identity Protection i Azure AD för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Mata in data i Azure Sentinel ytterligare undersökningar baserat på affärsbehov.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för resurser relaterade till Azure Kubernetes Service-distributioner (AKS) för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Uppdatera taggar för hanterade kluster](/rest/api/aks/managedclusters/updatetags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Isolera team och arbetsbelastningar logiskt i samma kluster med Azure Kubernetes Service (AKS) för att tillhandahålla det minsta antalet privilegier, begränsat till de resurser som krävs av varje team. 

Använd namnområdet i Kubernetes för att skapa en gräns för logisk isolering. Överväg att implementera ytterligare Kubernetes-funktioner för isolering och flera innehavare, till exempel schemaläggning, nätverk, autentisering/auktorisering och containrar.

Implementera separata prenumerationer och/eller hanteringsgrupper för utvecklings-, test- och produktionsmiljöer. Separera AKS-kluster med nätverk genom att distribuera dem till distinkta virtuella nätverk, som taggas på rätt sätt.

- [Lär dig mer om metodtips för klusterisolering i AKS](operator-best-practices-cluster-isolation.md)

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Förstå metodtips för nätverksanslutning och säkerhet i AKS](operator-best-practices-network.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Använd en lösning från tredje part från Azure Marketplace i nätverksnätverket som övervakar obehörig överföring av känslig information och blockerar sådana överföringar samtidigt som informationssäkerhetspersonal varnas.

Microsoft hanterar den underliggande plattformen och behandlar allt kundinnehåll som känsligt och går mycket långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Lista över portar, adresser och domännamn som krävs för AKS-funktioner](limit-egress-traffic.md)

- [Så här konfigurerar du diagnostikinställningar för Azure Firewall](../firewall/firewall-diagnostics.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Skapa en HTTPS-ingresskontrollant och använd dina egna TLS-certifikat (eller eventuellt Let's Encrypt) för dina Azure Kubernetes Service-distributioner (AKS). 

Utgående Kubernetes-trafik krypteras via HTTPS/TLS som standard. Granska eventuell okrypterad utgående trafik från dina AKS-instanser för ytterligare övervakning. Detta kan omfatta NTP-trafik, DNS-trafik, HTTP-trafik för att hämta uppdateringar i vissa fall. 

- [Så här skapar du en HTTPS-ingresskontrollant på AKS och använder dina egna TLS-certifikat](ingress-own-tls.md)

- [Så här skapar du en HTTPS-ingresskontrollant på AKS med Let's Encrypt](ingress-tls.md)

- [Lista över potentiella ut-going portar och protokoll som används av AKS](limit-egress-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure Storage eller beräkningsresurser. Implementera en lösning från tredje part om det behövs i efterlevnadssyfte.
Microsoft hanterar den underliggande plattformen och behandlar allt kundinnehåll som känsligt och tar lång tid att skydda mot förlust och exponering av kunddata. 

För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning:** Använd Azures auktoriseringssystem för rollbaserad åtkomstkontroll (Azure RBAC) som bygger på Azure Resource Manager för att tillhandahålla mer information om åtkomsthantering av Azure-resurser.

Konfigurera Azure Kubernetes Service (AKS) för att använda Azure Active Directory (Azure AD) för användarautentisering. Logga in på ett AKS-kluster med azure AD-autentiseringstoken med den här konfigurationen. 

Använd de inbyggda AKS-rollerna med Azure RBAC – Resursprincipdeltagare och Ägare för principtilldelningsåtgärder till ditt AKS-kluster

- [Så här styr du åtkomsten till klusterresurser med hjälp av Azure RBAC och Azure AD-identiteter i AKS](azure-ad-rbac.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustskydd för att framtvinga åtkomstkontroll

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure Storage eller beräkningsresurser. Implementera en lösning från tredje part om det behövs i efterlevnadssyfte.
Microsoft hanterar den underliggande plattformen och behandlar allt kundinnehåll som känsligt och sträcker sig långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** De två primära typerna av lagring som tillhandahålls för volymer i Azure Kubernetes Service (AKS) backas upp av Azure-diskar eller Azure Files. Båda typerna av lagring använder Azure Storage Service Encryption (SSE), som krypterar vilodata för att förbättra säkerheten. Som standard krypteras data med Microsoft-hanterade nycklar.

Kryptering i vila med kund hanterade nycklar är tillgängligt för kryptering av både operativsystemet och datadiskar i AKS-kluster för ytterligare kontroll över krypteringsnycklar. Kunder äger ansvaret för nyckelhanteringsaktiviteter som säkerhetskopiering och rotation av nycklar. Diskar kan för närvarande inte krypteras med Azure Disk Encryption på AKS-nodnivå.

- [Metodtips för lagring och säkerhetskopiering i Azure Kubernetes Service (AKS)](operator-best-practices-storage.md)

- [BYOK (Bring Your Own Keys) med Azure-diskar i Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor för containrar för att övervaka prestanda för containerarbetsbelastningar som distribueras till hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). 

Konfigurera aviseringar för proaktiv avisering eller skapande av logg när processor- och minnesanvändningen på noder eller containrar överskrider definierade tröskelvärden, eller när en ändring av hälsotillståndet inträffar i klustret vid den samlade infrastrukturen eller noderna. 

Använd Azure-aktivitetsloggen för att övervaka dina AKS-kluster och relaterade resurser på hög nivå. Integrera med Prometheus för att visa program- och arbetsbelastningsmått som samlas in från noder och Kubernetes med hjälp av frågor för att skapa anpassade aviseringar, instrumentpaneler och detaljerad utföra detaljerad analys.

- [Förstå Azure Monitor för containrar](/azure/azure-monitor/insights/container-insights-overview)

- [Så här aktiverar du Azure Monitor för containrar](/azure/azure-monitor/insights/container-insights-onboard)

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Köra automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Använd Security Center för att övervaka dina Azure Container Registry inklusive Azure Kubernetes Service-instanser (AKS) efter säkerhetsrisker. Aktivera containerregister-paketet i Security Center för att säkerställa Security Center är redo att skanna avbildningar som push-skickas till registret.

Bli meddelad i Security Center när problem påträffas när Security Center genomsöker bilden med Qualys. Paketfunktionen Container Registries ger djupare insyn i sårbarheter i avbildningarna som används i Azure Resource Manager register. 

Använd Security Center rekommendationer för varje säkerhetsrisk. Dessa rekommendationer innehåller en allvarlighetsgrad och vägledning för reparation. 

- [Metodtips för hantering av containeravbildningar och säkerhet i Azure Kubernetes Service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Förstå metodtips för hantering och säkerhet för containeravbildningar i AKS](operator-best-practices-container-image-management.md)

- [Förstå container Registry-integrering med Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera automatisk lösning för korrigeringshantering av operativsystem

**Vägledning:** Säkerhetsuppdateringar tillämpas automatiskt på Linux-noder för att skydda Azure Kubernetes Service kluster (AKS). De här uppdateringarna omfattar os-säkerhetskorrigeringar eller kerneluppdateringar. 

Observera att processen för att hålla Windows Server-noder uppdaterade skiljer sig från noder som kör Linux eftersom Windows Server-noder inte får dagliga uppdateringar. I stället måste kunderna uppgradera Windows Server-nodpoolerna i sina AKS-kluster som distribuerar nya noder med den senaste grundläggande Windows Server-avbildningen och korrigeringar med hjälp av Azure-kontrollpanelen eller Azure CLI. De här uppdateringarna innehåller säkerhets- eller funktionsförbättringar för AKS.

- [Förstå hur uppdateringar tillämpas på AKS-klusternoder som kör Linux](node-updates-kured.md)

- [Uppgradera en AKS-nodpool för AKS-kluster som använder Windows Server-noder](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Azure Kubernetes Service (AKS)-nodavbildningsuppgraderingar](node-image-upgrade.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuera en automatiserad korrigeringshanteringslösning för programvarutitlar från tredje part

**Vägledning:** Implementera en manuell process för att säkerställa att Azure Kubernetes Service(AKS)-klusternodens program från tredje part förblir korrigerade under hela klustrets livslängd. Detta kan kräva att du aktiverar automatiska uppdateringar, övervakar noderna eller utför regelbundna omstarter.

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka-till-tillbaka-sårbarhetsgenomsökningar

**Vägledning:** Exportera Security Center genomsökningsresultat med konsekventa intervall och jämför resultaten för att verifiera att sårbarheter har åtgärdats.

Använd PowerShell-cmdleten "Get-AzSecurityTask" för att automatisera hämtningen av säkerhetsaktiviteter som Security Center rekommenderar att du utför för att förbättra din säkerhetsstatus och åtgärda sårbarhetsgenomsökningsresultat.

- [Så här använder du PowerShell för att visa sårbarheter som upptäckts av Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder vid identifierade säkerhetsrisker

**Vägledning:** Använd den allvarlighetsgrad som tillhandahålls Security Center att prioritera åtgärder för sårbarheter. 

Använd Common Vulnerability Scoring System (CVSS) (eller andra bedömningssystem som tillhandahålls av ditt skanningsverktyg) om du använder ett inbyggt verktyg för sårbarhetsbedömning (till exempel Qualys eller Rapid7, som erbjuds av Azure).

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk och så vidare) i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och att du kan räkna upp alla Azure-prenumerationer och resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan Resource Graph via en Azure Resource Manager bör du skapa och använda Azure Resource Manager-baserade resurser i framtiden.

- [Skapa frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser med metadata för att logiskt ordna dem i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra tillgångar. 

Använd taints, etiketter eller taggar när du skapar en Azure Kubernetes Service(AKS)-nodpool. Alla noder i nodpoolen ärver även den taint-, etikett- eller taggen.

Taints, etiketter eller taggar kan användas för att stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationer inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa taggar för användare och](../azure-resource-manager/management/tag-resources.md)

- [Hanterade kluster – Uppdateringstaggar](/rest/api/aks/managedclusters/updatetags)

- [Ange en taint, etikett eller tagg för en nodpool](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla en förteckning över godkända Azure-resurser

**Vägledning:** Definiera en lista över godkända Azure-resurser och godkänd programvara för beräkningsresurser baserat på organisationens affärsbehov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:
-   Otillåtna resurstyper 

-   Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns baserat på organisationens affärskrav.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka för program som inte är godkända i beräkningsresurser

**Vägledning:** Använd Azure Automation Ändringsspårning och inventering för att ta reda på programvara som är installerad i din miljö. 

Samla in och visa inventering för programvara, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer och övervaka för program som inte är godkända. 

Spåra konfigurationerna av dina datorer för att identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Använd Azure Automation Ändringsspårning och inventering för att ta reda på programvara som är installerad i din miljö. 

Samla in och visa inventering för programvara, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer och övervaka för program som inte är godkända. 

Spåra konfigurationerna av dina datorer för att identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

- [Så här använder du övervakning av filintegritet](../security-center/security-center-file-integrity-monitoring.md)

- [Förstå Azure Ändringsspårning](../automation/change-tracking/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända program

**Vägledning:** Använd Azure Automation Ändringsspårning och inventering för att ta reda på programvara som är installerad i din miljö. 

Samla in och visa inventering för programvara, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer och övervaka för program som inte är godkända. 

Spåra konfigurationerna av dina datorer för att identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

Aktivera anpassningsbar programanalys i Security Center för program som finns i din miljö.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

- [Så här använder du Azure Security Center anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Upprätthålla en förteckning över godkända programvarutitlar

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer med hjälp av inbyggda principdefinitioner.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för Microsoft Azure-hanteringsappen.
- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning:** Azure Kubernetes Service (AKS) tillhandahåller inte en identitetshanteringslösning där vanliga användarkonton och lösenord lagras. Använd i stället Azure Active Directory (Azure AD) som integrerad identitetslösning för dina AKS-kluster.

Ge användare eller grupper åtkomst till Kubernetes-resurser inom ett namnområde eller i klustret med hjälp av Azure AD-integrering.

Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i dina administrativa AKS-grupper och använda den för att stämma av åtkomst regelbundet. Använd Azure CLI för åtgärder som "Hämta autentiseringsuppgifter för åtkomst för ett hanterat Kubernetes-kluster. Implementera Security Center rekommendationer för identitets- och åtkomsthantering.

- [Hantera AKS med Azure CLI](/cli/azure/aks)

- [Förstå AKS- och Azure AD-integrering](concepts-identity.md)

- [Så här integrerar du AKS med Azure AD](azure-ad-integration-cli.md)

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Hämta medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera program med hög risk

**Vägledning:** Använd Azure Kubernetes Service-funktioner (AKS) för att logiskt isolera team och arbetsbelastningar i samma kluster för minsta antal privilegier, begränsat till de resurser som krävs av varje team. 

Implementera namnrymd i Kubernetes för att skapa en logisk isoleringsgräns. Använd Azure Policy alias i namnområdet "Microsoft.ContainerService" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Azure Kubernetes Service-instanser (AKS). 

Granska och implementera ytterligare Kubernetes-funktioner och överväganden för isolering och flera innehavare för att inkludera följande: schemaläggning, nätverk, autentisering/auktorisering och containrar. Använd även separata prenumerationer och hanteringsgrupper för utveckling, testning och produktion. Separera AKS-kluster med virtuella nätverk, undernät som taggas på rätt sätt och skyddas med en Web Application Firewall (WAF).

- [Lär dig mer om metodtips för klusterisolering i AKS](operator-best-practices-cluster-isolation.md)

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Förstå metodtips för nätverksanslutning och säkerhet i AKS](operator-best-practices-network.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.ContainerService" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Azure Kubernetes Service instanser (AKS). Använd inbyggda Azure Policy definitioner.

Exempel på inbyggda principdefinitioner för AKS är:

- Använder inkommande HTTPS i Kubernetes-kluster

- Auktoriserade IP-intervall ska definieras på Kubernetes Services

- Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services

- Ser till att endast tillåtna containeravbildningar finns i Kubernetes-klustret

Exportera en mall för din AKS-konfiguration i JavaScript Object Notation (JSON) med Azure Resource Manager. Granska den regelbundet för att säkerställa att dessa konfigurationer uppfyller säkerhetskraven för din organisation. Använd rekommendationerna från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser. 

- [Så här konfigurerar och hanterar du AKS-poddsäkerhetsprinciper](use-pod-security-policies.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta säkra operativsystemkonfigurationer

**Vägledning:** AKS-kluster (Azure Kubernetes Clusters) distribueras på virtuella värddatorer med ett säkerhetsoptimerat operativsystem. Värdoperativsystemet har ytterligare säkerhetshärdningssteg som ingår i det för att minska angreppsytan och gör det möjligt att distribuera containrar på ett säkert sätt. 

Azure tillämpar dagliga korrigeringar (inklusive säkerhetskorrigeringar) på virtuella AKS-värdar med vissa korrigeringar som kräver en omstart. Kunderna ansvarar för att schemalägga omstarter av den virtuella AKS-datorn enligt behov. 

- [Säkerhetshärdning för AKS-agentnodens värdoperativsystem](security-hardened-vm-host-image.md)

- [Förstå säkerhetshärdning i virtuella AKS-värdar](security-hardened-vm-host-image.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Skydda ditt Azure Kubernetes Service (AKS)-kluster med hjälp av poddsäkerhetsprinciper.  Begränsa vilka poddar som kan schemaläggas för att förbättra säkerheten för klustret. 

Poddar som begär resurser som inte tillåts kan inte köras i AKS-klustret. 

Använd även Azure Policy [neka] och [distribuera om det inte finns] effekter för att framtvinga säkra inställningar för Azure-resurser som är relaterade till dina AKS-distributioner (till exempel virtuella nätverk, undernät, Azure Firewalls, lagringskonton och så vidare). 

Skapa anpassade Azure Policy definitioner med alias från följande namnområden: 

- Microsoft.ContainerService

- Microsoft.Network

Ytterligare information finns på de refererade länkarna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhålla säkra operativsystemkonfigurationer

**Vägledning:** Azure Kubernetes Service (AKS) distribueras på virtuella värddatorer med ett säkerhetsoptimerat operativsystem. Värdoperativsystemet har ytterligare säkerhetshärdningssteg som ingår i det för att minska angreppsytan och gör det möjligt att distribuera containrar på ett säkert sätt. 

Se listan över CIS-kontroller (Center for Internet Security) som är inbyggda i värdoperativsystemet.  

- [Säkerhetshärdning för värdoperativsystem för AKS-agentnod](security-hardened-vm-host-image.md)

- [Förstå tillståndskonfiguration för AKS-kluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Förstå säkerhetshärdning i värdar för virtuella AKS-datorer](security-hardened-vm-host-image.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Använd Azure Repos för att lagra och hantera dina konfigurationer på ett säkert sätt om du använder anpassade Azure Policy definitioner. Exportera en mall för din Azure Kubernetes Service (AKS)-konfiguration i JavaScript Object Notation (JSON) med Azure Resource Manager. Granska den regelbundet för att säkerställa att konfigurationerna uppfyller säkerhetskraven för din organisation.

Implementera lösningar från tredje part, till exempel Terraform, för att skapa en konfigurationsfil som deklarerar resurserna för Kubernetes-klustret. Du kan härda AKS-distributionen genom att implementera metodtips för säkerhet och lagra konfigurationen som kod på en skyddad plats.

- [Definiera ett Kubernetes-kluster](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Säkerhetshärdning för AKS-agentnodens värdoperativsystem](security-hardened-vm-host-image.md)

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning:** Gäller inte för Azure Kubernetes Service (AKS). AKS tillhandahåller ett säkerhetsoptimerat värdoperativsystem (OS) som standard. Det finns inget aktuellt alternativ för att välja ett alternativt eller anpassat operativsystem.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy för att ange begränsningar för vilken typ av resurser som kan skapas i prenumerationer med hjälp av inbyggda principdefinitioner samt Azure Policy-alias i namnområdet "Microsoft.ContainerService". 

Skapa anpassade principer för granskning och framtvinga systemkonfigurationer. Utveckla en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här använder du alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera konfigurationshanteringsverktyg för operativsystem

**Vägledning:** Azure Kubernetes Service (AKS) distribueras på virtuella värddatorer med ett säkerhetsoptimerat operativsystem. Värdoperativsystemet har ytterligare säkerhetshärdningssteg som ingår i det för att minska angreppsytan och gör det möjligt att distribuera containrar på ett säkert sätt. 

Se listan över CIS-kontroller (Center for Internet Security) som är inbyggda i AKS-värdar.  

- [Säkerhetshärdning för värdoperativsystem för AKS-agentnod](security-hardened-vm-host-image.md)

- [Förstå säkerhetshärdning i värdar för virtuella AKS-datorer](security-hardened-vm-host-image.md)

- [Förstå tillståndskonfiguration för AKS-kluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Security Center för att utföra baslinjesökningar för resurser relaterade till dina Azure Kubernetes Service-distributioner (AKS). Exempelresurser omfattar men är inte begränsade till själva AKS-klustret, det virtuella nätverket där AKS-klustret distribuerades, det Azure Storage-konto som används för att spåra Terraform-tillstånd eller Azure Key Vault-instanser som används för krypteringsnycklarna för AKS-klustrets operativsystem- och datadiskar.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning:** Använd Security Center i avsnittet "Beräkningsappar" för att utföra baslinjegenomsökningar &amp; för dina AKS Azure Kubernetes Service kluster (Azure Kubernetes Service). 

Få ett meddelande Security Center instrumentpanelen när konfigurationsproblem eller sårbarheter hittas. Detta kräver att du aktiverar det valfria containerregisterpaketet som gör Security Center att genomsöka avbildningen.  

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Integrera Azure Key Vault med ett Azure Kubernetes Service-kluster (AKS) med en FlexVolume-enhet. Använd Azure Key Vault att lagra och regelbundet rotera hemligheter, till exempel autentiseringsuppgifter, lagringskontonycklar eller certifikat. FlexVolume-drivrutinen gör att AKS-klustret kan hämta autentiseringsuppgifter från Key Vault och på ett säkert sätt endast tillhandahålla dem till den begärande podden. Använd en podd-hanterad identitet för att begära åtkomst Key Vault och hämta de autentiseringsuppgifter som krävs via FlexVolume-drivrutinen. Kontrollera Key Vault mjuk borttagning är aktiverat. 

Begränsa exponering av autentiseringsuppgifter genom att inte definiera autentiseringsuppgifter i programkoden. 

Undvik att använda fasta eller delade autentiseringsuppgifter. 

- [Säkerhetsbegrepp för program och kluster i AKS (Azure Kubernetes Service)](concepts-security.md)

- [Använda Key Vault med ditt AKS-kluster](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Definiera inte autentiseringsuppgifter i programkoden som en säkerhetsmetod. Använd hanterade identiteter för Azure-resurser för att låta en podd autentisera sig mot alla tjänster i Azure som stöder det, inklusive Azure Key Vault. Podden tilldelas en Azure-identitet för att autentisera till Azure Active Directory (Azure AD) och ta emot en digital token som kan visas för andra Azure-tjänster som kontrollerar om podden har behörighet att komma åt tjänsten och utföra nödvändiga åtgärder.

Observera att podd-hanterade identiteter endast är avsedda att användas med Linux-poddar och containeravbildningar. Etablera Azure Key Vault att lagra och hämta digitala nycklar och autentiseringsuppgifter. Nycklar, till exempel de som används för att kryptera OS-diskar, AKS-klusterdata kan lagras i Azure Key Vault.

Tjänstens huvudnamn kan också användas i AKS-kluster. Kluster som använder tjänstens huvudnamn kan dock så småningom nå ett tillstånd där tjänstens huvudnamn måste förnyas för att klustret ska fungera. Hanteringen av tjänstens huvudnamn ökar komplexiteten, vilket är anledningen till att det är enklare att använda hanterade identiteter i stället. Samma behörighetskrav gäller för både tjänstens huvudnamn och hanterade identiteter.

- [Förstå hanterade identiteter och Key Vault med Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Azure AD-poddidentitet](https://github.com/Azure/aad-pod-identity)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Med skannern för autentiseringsuppgifter kan du även flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault med rekommendationer.

Begränsa exponeringen av autentiseringsuppgifter genom att inte definiera autentiseringsuppgifter i programkoden. och undvik att använda delade autentiseringsuppgifter. Azure Key Vault ska användas för att lagra och hämta digitala nycklar och autentiseringsuppgifter. Använd hanterade identiteter för Azure-resurser för att ge podden åtkomst till andra resurser. 

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Metodtips för poddsäkerhet för utvecklare](developer-best-practices-pod-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig programvara

**Vägledning:** AKS hanterar livscykeln och driften av agentnoder åt dig – det går inte att ändra de IaaS-resurser som är associerade med agentnoderna. För Linux-noder kan du dock använda daemonuppsättningar för att installera anpassad programvara, till exempel en lösning mot skadlig kod.

- [Referensguide för säkerhetsaviseringar](../security-center/alerts-reference.md)

- [Aviseringar för containrar – Azure Kubernetes Service kluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Delat AKS-ansvar och Daemon-uppsättningar](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska överföras till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Skanna alla filer som laddas upp till dina AKS-resurser i förväg. Använd Security Center hotidentifiering för datatjänster för att identifiera skadlig kod som överförs till lagringskonton om du använder ett Azure Storage-konto som ett datalager eller spårar Terraform-tillstånd för ditt AKS-kluster. 

- [Förstå Azure Security Center hotidentifiering för datatjänster](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Se till att program mot skadlig programvara och signaturer uppdateras

**Vägledning:** AKS hanterar livscykeln och driften av agentnoder åt dig – det går inte att ändra de IaaS-resurser som är associerade med agentnoderna. För Linux-noder kan du dock använda daemonuppsättningar för att installera anpassad programvara, till exempel en lösning mot skadlig kod.

- [Referensguide för säkerhetsaviseringar](../security-center/alerts-reference.md)

- [Aviseringar för containrar – Azure Kubernetes Service kluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Delat AKS-ansvar och Daemon-uppsättningar](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Back up your data using an appropriate tool for your storage type such as Velero, which can back up persistent volumes along with additional cluster resources and configurations. Kontrollera regelbundet integriteten och säkerheten för dessa säkerhetskopior. 

Ta bort tillstånd från dina program före säkerhetskopiering. I fall där detta inte kan göras kan du återkommande data från beständiga volymer och regelbundet testa återställningsåtgärder för att verifiera dataintegriteten och de processer som krävs.

- [Metodtips för lagring och säkerhetskopiering i AKS](operator-best-practices-storage.md)

- [Metodtips för affärskontinui och haveriberedskap i AKS](operator-best-practices-multi-region.md)

- [Förstå Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Konfigurera Velero på Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Back up your data using an appropriate tool for your storage type such as Velero, which can back up persistent volumes along with additional cluster resources and configurations. 

Utför regelbundna automatiserade säkerhetskopieringar av Key Vault certifikat, nycklar, hanterade lagringskonton och hemligheter med PowerShell-kommandon. 

- [Säkerhetskopiera Key Vault certifikat](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Säkerhetskopiera Key Vault nycklar](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Så här säkerhetskopierar du Key Vault hanterade lagringskonton](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Säkerhetskopiera Key Vault hemligheter](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Så här aktiverar du Azure Backup](/azure/backup/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Utför regelbundet dataåterställning av innehåll i Velero Backup. Om det behövs kan du testa återställningen till ett isolerat virtuellt nätverk.

Utför regelbundet dataåterställning av Key Vault certifikat, nycklar, hanterade lagringskonton och hemligheter med PowerShell-kommandon.

- [Så här återställer du Key Vault certifikat](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Så här återställer du Key Vault nycklar](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Så här återställer du Key Vault hanterade lagringskonton](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Återställa Key Vault hemligheter](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Återställa filer från säkerhetskopiering av virtuella Azure-datorer](/azure/backup/backup-azure-restore-files-from-vm)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Back up your data using an appropriate tool for your storage type such as Velero, which can back up persistent volumes along with additional cluster resources and configurations. 

Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning om Azure Key Vault används med för Azure Kubernetes Service-distributioner (AKS).

- [Förstå Azure Storage-tjänstkryptering](../storage/common/storage-service-encryption.md)

- [Så här aktiverar du Soft-Delete i Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en incidentsvarsguide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST:s guide för hantering av säkerhetsincidenter](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för bedömning och prioritering av incidenter

**Vägledning:** Prioritera vilka aviseringar som måste undersökas först med Security Center tilldelad allvarlighetsgrad till aviseringar. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller den analys som används för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.
Markera prenumerationer tydligt (till exempel produktion, icke-produktion) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor och ändra planer för incidenter efter behov.

- [Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en obehörig part. 

Granska incidenter i efterhand för att säkerställa att problemen är lösta.

- [Ställa in Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidenter

**Vägledning:** Exportera Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. 

Välj anslutningsappen Security Center data för att strömma aviseringarna till Azure Sentinel, enligt behov och baserat på organisationens affärskrav.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Prestandatest för Azure-säkerhet: Intrångstester och Red Team-övningar.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Ytterligare information om Microsofts strategi och körning av red teaminsamling och intrångstestning av live-webbplatser mot Microsoft-hanterad molninfrastruktur, -tjänster och -program, på de refererade länkarna.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
