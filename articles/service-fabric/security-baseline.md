---
title: Azures säkerhets bas linje för Service Fabric
description: Service Fabric säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ee294cff85bb71b5c2a238fcf985fc870ed32618
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285470"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Azures säkerhets bas linje för Service Fabric

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark version 1.0](../security/benchmarks/overview-v1.md) till Service Fabric. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Service Fabric.

> [!NOTE]
> **Kontroller** som inte är tillämpliga på Service Fabric, eller för vilka ansvaret är Microsofts, har uteslutits. Om du vill se hur Service Fabric helt mappar till Azures säkerhets mätning, se den **[fullständiga Service Fabric mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/service-fabric-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: kontrol lera att alla distributioner av Virtual Network undernät har en nätverks säkerhets grupp som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. 

- [Distribuera Azure Firewall med hjälp av en mall](../firewall/deploy-template.md)

- [Skapa perimeter-nätverk med hjälp av Azure nätverks säkerhets grupper (NSG: er)](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices#use-network-isolation-and-security-with-azure-service-fabric)

- [Integrera ditt Azure Service Fabric-kluster med ett befintligt virtuellt nätverk](service-fabric-patterns-networking.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Security Center och åtgärda rekommendationer för nätverks skydd för det virtuella nätverket, under nätet och nätverks säkerhets gruppen som används för att skydda ditt Azure Service Fabric-kluster. Aktivera flödes loggar för nätverks säkerhets grupper och skicka loggar till ett Azure Storage konto till trafik granskning. Du kan också skicka nätverks säkerhets grupper flödes loggar till en Azure Log Analytics-arbetsyta och använda Azure Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Azure Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Azure Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: tillhandahålla en frontend-Gateway för att tillhandahålla en enda punkt för ingångar för användare, enheter eller andra program. Azure API Management integreras direkt med Service Fabric, så att du kan skydda åtkomsten till backend-tjänster, förhindra DOS-attacker genom att använda begränsning och verifiera API-nycklar, JWT-token, certifikat och andra autentiseringsuppgifter.

Överväg att distribuera Azure Web Application Firewall (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställningar för WAF och mata in loggar till ett lagrings konto, en Event Hub-eller Log Analytics-arbetsyta.

- [Service Fabric med Azure API Management-översikt](service-fabric-api-management-overview.md)

- [Integrera API Management i ett internt VNET med Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: för skydd mot DDoS-attacker aktiverar du Azure DDoS standard-skydd på det virtuella nätverk där Azure Service Fabric-klustret distribueras. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera nätverks säkerhets grupp flödes loggar för de nätverks säkerhets grupper som är kopplade till det undernät som används för att skydda ditt Service Fabric-kluster. Registrera NSG flödes loggar i ett Azure Storage-konto för att generera flödes poster. Om det behövs för att undersöka avvikande aktivitet aktiverar du insamlingen av Azure Network Watcher-paket.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

- [Använd trafik analys för att visualisera NSG flödes loggar](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll.  Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. 

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att upptäcka och/eller neka skadlig trafik. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md) 

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: Distribuera Azure Application Gateway för webb program med HTTPS/SSL aktiverat för betrodda certifikat. 

- [Så här distribuerar du Application Gateway](../application-gateway/quick-create-portal.md)

- [Så här konfigurerar du Application Gateway att använda HTTPS](../application-gateway/create-ssl-portal.md)

- [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](../application-gateway/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper (NSG) som är kopplade till under nätet som Azure Service Fabric-klustret distribueras i. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Tjänst taggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

- [Metod tips för Service Fabric nätverk](service-fabric-best-practices-networking.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är relaterade till ditt Azure Service Fabric-kluster. Använd Azure Policy alias i namn områdena "Microsoft. ServiceFabric" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för ditt Azure Service Fabric-kluster.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, Azure RBAC-kontroller och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper och andra resurser som rör nätverks säkerhets-och trafikflöden som är associerade med ditt Service Fabric-kluster. Använd fältet Beskrivning för enskilda regler för nätverks säkerhets grupper för att ange affärs behov, varaktighet och så vidare, för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure kommando rads gränssnitt (CLI) för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Service Fabric-distributioner. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: du kan publicera ditt Azure Service Fabric-kluster för att Azure Monitor för att samla in säkerhets data som genereras av klustret. Se exempel på diagnostiska problem och lösningar med Service Fabric.

- [Konfigurera Azure Monitor loggar integration med Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurera Azure Monitor loggar för att övervaka behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Diagnostisera vanliga Service Fabric scenarier](service-fabric-diagnostics-common-scenarios.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera Azure Monitor för Service Fabric-klustret, dirigera det till en Log Analytics-arbetsyta. Detta kommer att logga relevant kluster information och OS-mått för alla Azure Service Fabric-klusternoder.

- [Konfigurera Azure Monitor loggar integration med Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurera Azure Monitor loggar för att övervaka behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Så här distribuerar du Log Analytics agent på noderna](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics loggs ökningar](/azure/azure-monitor/log-query/log-query-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: publicera Azure Service Fabric-klustret för att Azure Monitor. Se till att den Log Analytics arbets ytan som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad.

- [Konfigurera Azure Monitor loggar integration med Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurera Azure Monitor loggar för att övervaka behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Så här distribuerar du Log Analytics agent på noderna](service-fabric-diagnostics-oms-agent.md) 

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](/azure/azure-monitor/platform/manage-cost-storage)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: publicera Service Fabric-klustret för att Azure Monitor. Se till att den Log Analytics arbets ytan som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad.

- [Konfigurera Azure Monitor loggar integration med Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Konfigurera Azure Monitor loggar för att övervaka behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Så här distribuerar du Log Analytics agent på noderna](service-fabric-diagnostics-oms-agent.md)

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](/azure/azure-monitor/platform/manage-cost-storage)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: använd Azure Log Analytics Workspace-frågor för att skicka frågor till Azure Service Fabric-loggar.

- [Log Analytics loggs ökningar](/azure/azure-monitor/log-query/log-query-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: använd Azure Log Analytics-arbetsytan för övervakning och aviseringar om avvikande aktiviteter i säkerhets loggar och händelser relaterade till ditt Azure Service Fabric-kluster.

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](/azure/azure-monitor/learn/tutorial-response)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: som standard installeras Windows Defender på windows Server 2016. Se dokumentationen för din antihandjur för konfigurations regler om du inte använder Windows Defender. Windows Defender stöds inte i Linux.

- [Mer information finns i Windows Defender Antivirus på Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part för DNS-loggning.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Konfigurera konsol loggning manuellt per nod.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla posten för det lokala administratörs kontot som skapas vid kluster etablering av Azure Service Fabric-kluster samt andra konton som du skapar. Om Azure Active Directory (Azure AD)-integrering används, har Azure AD dessutom inbyggda roller som måste tilldelas explicit och som därför kan frågas. Använd Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: när du konfigurerar ett kluster kräver Azure att du skapar nya lösen ord för webb portalen. Det finns inga standard lösen ord att ändra, men du kan ange olika lösen ord för webb Portal åtkomst.

- [Skapa i Azure-portalen](service-fabric-cluster-creation-via-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: integrera autentisering för Service Fabric med Azure Active Directory (Azure AD). Skapa principer och procedurer kring användningen av dedikerade administrativa konton.

Dessutom kan du använda rekommendationer för Security Center identitets-och åtkomst hantering.

- [Konfigurera Azure AD-klientautentisering](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster#set-up-azure-active-directory-client-authentication)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: där det är möjligt använder du Azure Active Directory (Azure AD) SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Security Center identitets-och åtkomst hantering. 

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Security Center Följ rekommendationerna för identitets-och åtkomst hantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Privileged Access Workstation (Paw) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Service Fabric-kluster och relaterade resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner. 

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system för att skydda åtkomsten till hanterings slut punkter för Service Fabric kluster. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Konfigurera Azure AD för Service Fabric klientautentisering](service-fabric-cluster-creation-setup-aad.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/azure/security-center/security-center-recommendations). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/azure/security-center/azure-defender) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: använda Azure Active Directory (Azure AD)-autentisering med ditt Service Fabric-kluster. Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-alert-on-account-login-behavior-deviation"></a>3,11: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (Azure AD)-inloggning och gransknings loggar för att övervaka försök att komma åt inaktiverade konton. dessa loggar kan integreras i alla SIEM/övervaknings verktyg från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton, skicka gransknings loggarna och inloggnings loggarna till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Azure Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för resurser som är relaterade till Service Fabric kluster distributioner för att kunna spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser bör åtskiljas av Virtual Network eller undernät, taggas på lämpligt sätt och skyddas av nätverks säkerhets grupper eller Azure-brandväggen. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade. För Virtual Machines lagra eller behandla känsliga data implementerar du principer och procedurer för att inaktivera dem när de inte används. 

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du aviseringen eller aviseringen och nekar med Azure-brandväggen](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Distribuera ett automatiserat verktyg på nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar vid avisering av information säkerhets tekniker. 

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS 1,2 eller senare. 

Använd ett X. 509-certifikat för Server identitet och TLS-kryptering av http-kommunikation för att Service Fabric ömsesidig autentisering från klient till nod. Valfritt antal ytterligare certifikat kan installeras i ett kluster för program säkerhets syfte, inklusive kryptering och dekryptering av program konfigurations värden och data mellan noder under replikeringen.
Följ Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall. 

- [Förstå kryptering i överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Service Fabric kluster säkerhets scenarier](service-fabric-cluster-security.md)

- [Service Fabric fel söknings guide för TLS-konfiguration](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: om du vill Service Fabric kluster som lagrar eller bearbetar känslig information markerar du klustret och relaterade resurser som känsliga med taggar. Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Använd kryptering i vila på alla Azure-resurser. Microsoft rekommenderar att Azure hanterar dina krypterings nycklar, men det finns möjlighet att hantera dina egna nycklar i vissa instanser. 

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)  

- [Så här konfigurerar du Kundhanterade krypterings nycklar](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Aktivera disk kryptering för Azure Service Fabric klusternoder i Windows](service-fabric-enable-azure-disk-encryption-windows.md)

- [Aktivera disk kryptering för Azure Service Fabric klusternoder i Linux](service-fabric-enable-azure-disk-encryption-linux.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/azure/security-center/security-center-recommendations). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/azure/security-center/azure-defender) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i kritiska Azure-resurser. 

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: kör regelbundet Service Fabric fel analys tjänsten och kaos-tjänsterna för att simulera fel i klustret för att bedöma om tjänsterna är stabila och tillförlitliga.

Följ rekommendationerna från Security Center om att utföra sårbarhets bedömningar på dina virtuella Azure-datorer och behållar avbildningar. 

Använd en lösning från tredje part för att utföra sårbarhets bedömningar på nätverks enheter och webb program. När du utför fjärrgenomsökningar ska du inte använda ett enda, beständig, administrativt konto. Överväg att implementera JIT-etablering för skannings kontot. Autentiseringsuppgifterna för skannings kontot bör skyddas, övervakas och används endast för sårbarhets skanning. 

- [Introduktion till Service Fabric fel analys tjänsten](service-fabric-testability-overview.md)

- [Inducera kontrollerade kaos i Service Fabric kluster](service-fabric-controlled-chaos.md)

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Aktivera automatisk uppgradering av OS-avbildningar på de virtuella datorernas skalnings uppsättningar i Service Fabric klustret. 

Om du vill testa OS-korrigeringsfiler först innan du går vidare till produktion använder du den manuella utlösaren för operativ Systems avbildnings uppgraderingar av din skalnings uppsättning. Observera att alternativet för manuell utlösare inte tillhandahåller inbyggd återställning. Övervaka OS-korrigeringsfiler med Uppdateringshantering från Azure Automation. 

- [Uppdaterings hantering för Service Fabric klusternoder](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatisk uppgradering av OS-avbildningar med skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Så här tar du med virtuella datorer uppdaterade med den senaste skalnings uppsättnings modellen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

- [Översikt över Azure Automation Uppdateringshantering](/azure/automation/update-management/update-mgmt-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: Aktivera automatisk uppgradering av OS-avbildningar på de virtuella datorernas skalnings uppsättningar för Azure Service Fabric-klustret. POA (patch Orchestration Application) är en alternativ lösning som är avsedd för Service Fabric kluster som finns utanför Azure. POA kan användas med Azure-kluster, med vissa ytterligare värd kostnader.

- [Uppdaterings hantering för Service Fabric klusternoder](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatisk uppgradering av OS-avbildningar med skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Konfigurera operativ systemets uppdaterings schema för Service Fabric kluster](service-fabric-patch-orchestration-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: exportera skannings resultat med jämna mellanrum och jämför resultaten för att verifiera att sårbarheter har åtgärd ATS. När du använder rekommendationer för sårbarhets hantering som föreslås av Security Center kan du pivotera in den valda lösningens Portal för att visa historiska skannings data.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av genomsöknings verktyget från tredje part.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: definiera godkända Azure-resurser och godkänd program vara för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

Otillåtna resurstyper

Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: implementera en lösning från tredje part för att övervaka klusternoder för program som inte är godkända.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.), inklusive Service Fabric kluster, i dina prenumerationer. Ta bort alla ej godkända Azure-resurser som du identifierar. För Service Fabric klusternoder implementerar du en lösning från tredje part för att ta bort eller varna på ej godkänd program vara.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: för Service Fabric klusternoder implementerar du en lösning från tredje part för att förhindra att obehöriga program körs.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att begränsa vilka tjänster du kan etablera i din miljö.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: för Azure Service Fabric-klusternoder implementerar du en lösning från tredje part för att förhindra att obehöriga filtyper körs.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". 

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: Använd konfigurationer för operativ system eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.

- [Till exempel hur du styr körning av PowerShell-skript i Windows-miljöer](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program vara som krävs för affärs åtgärder, men som kan ådra sig högre risk för organisationen, bör isoleras inom den egna virtuella datorn och/eller det virtuella nätverket och tillräckligt säkert med antingen en Azure-brandvägg eller en nätverks säkerhets grupp. 

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md) 

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. ServiceFabric" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för ditt Service Fabric-kluster.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Service Fabric operativ Systems avbildningar hanteras och underhålls av Microsoft. Kund som ansvarar för att implementera säkra konfigurationer för klusternodernas operativ system.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns]-effekter för att framtvinga säkra inställningar för Azure Service Fabric-kluster och relaterade resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Service Fabric kluster operativ Systems avbildningar som hanteras och underhålls av Microsoft. Kunden ansvarar för att implementera tillstånds konfigurationen på operativ system nivå.

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentation om Azure databaser](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: om du använder anpassade avbildningar använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) så att endast behöriga användare får åtkomst till avbildningarna. För behållar avbildningar lagrar du dem i Azure Container Registry och utnyttjar Azure RBAC för att se till att endast behöriga användare kan komma åt avbildningarna. 

- [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Lär dig mer om Azure RBAC för Container Registry](../container-registry/container-registry-roles.md) 

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. ServiceFabric" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. ServiceFabric" om du vill skapa anpassade principer för granskning eller framtvinga konfigurationen av ditt Service Fabric-kluster.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: Använd Security Center för att utföra bas linje genomsökningar för OS-och Docker-inställningar för behållare. 

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program. 

- [Använda hanterade identiteter för Azure med Service Fabric](concepts-managed-identity.md)

- [Konfigurera stöd för hanterad identitet för ett nytt Service Fabric-kluster](configure-new-azure-service-fabric-enable-managed-identity.md)

- [Använd hanterad identitet med ett Service Fabric program](how-to-managed-identity-service-fabric-app-code.md)

- [KeyVaultReference-stöd för Service Fabric program](service-fabric-keyvault-references.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: hanterade identiteter kan användas i Azure-distribuerade Service Fabric-kluster och för program som distribueras som Azure-resurser. Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure Active Directory (Azure AD)-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i din kod.

- [Använda hanterade identiteter för Azure med Service Fabric](concepts-managed-identity.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: om du använder en kod som är relaterad till din Azure Service Fabric-distribution kan du implementera autentiseringsuppgifterna för autentisering för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

Använd Azure Key Vault för att rotera Service Fabric kluster certifikat automatiskt.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Certifikat hantering i Service Fabric kluster](https://docs.microsoft.com/azure/service-fabric/cluster-security-certificate-management#certificate-rotation)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: som standard installeras Windows Defender Antivirus på windows Server 2016. Användargränssnittet installeras som standard på vissa SKU:er, men det krävs inte.

Se dokumentationen för program mot skadlig kod för konfigurations regler om du inte använder Windows Defender. Windows Defender stöds inte i Linux.

- [Förstå Windows Defender Antivirus på Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: säkerhets kopierings-och återställnings tjänsten i Service Fabric möjliggör enkel och automatisk säkerhets kopiering av information som lagras i tillstånds känsliga tjänster. Att säkerhetskopiera program data på regelbunden basis är grundläggande för att skydda mot data förlust och tjänster som inte är tillgängliga. Service Fabric tillhandahåller en valfri säkerhets kopierings-och återställnings tjänst som gör att du kan konfigurera regelbunden säkerhets kopiering av tillstånds känsliga Reliable Services (inklusive aktör tjänster) utan att behöva skriva någon ytterligare kod. Den gör det också lättare att återställa tidigare säkerhets kopior.

- [Periodisk säkerhets kopiering och återställning i ett Azure Service Fabric-kluster](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Aktivera Backup Restore service i Service Fabric-klustret och skapa säkerhets kopierings principer för att säkerhetskopiera tillstånds känsliga tjänster regelbundet och på begäran. Säkerhetskopiera Kundhanterade nycklar inom Azure Key Vault.

- [Periodisk säkerhets kopiering och återställning i ett Azure Service Fabric-kluster](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

- [Förstå regelbunden konfiguration av säkerhets kopiering i Azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: se till att du kan utföra återställning från tjänsten för säkerhets kopierings återställning genom att regelbundet granska konfigurations informationen för säkerhets kopieringen och tillgängliga säkerhets kopior. Testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

- [Förstå regelbunden konfiguration av säkerhets kopiering i Azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Återställa säkerhets kopia i Azure Service Fabric](service-fabric-backup-restore-service-trigger-restore.md)

- [Återställa Key Vault-nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: säkerhets kopior från Service Fabric Backup Restore service använd ett Azure Storage konto i din prenumeration. Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklar kan du ange Kundhanterade nycklar för kryptering av lagrings data.

Om du använder Kundhanterade nycklar kontrollerar du att Soft-Delete i Key Vault är aktiverat för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Azure Storage-kryptering i vila](../storage/common/storage-service-encryption.md)

- [Så här aktiverar du Soft-Delete i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att hitta eller det mått som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom kan du markera prenumerationer med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade. 

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov. 

- [NIST-guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta. 

- [Konfigurera en säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Security Center Data Connector för att strömma aviseringarna till kontroll.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
