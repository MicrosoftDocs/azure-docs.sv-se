---
title: Azures säkerhets bas linje för Azure DevTest Labs
description: Azure DevTest Labs säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 27a0d5b809480b2ce4aff36c5acd43c149ed5bb3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562842"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azures säkerhets bas linje för Azure DevTest Labs

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Azure DevTest Labs. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure DevTest Labs. **Kontroller** som inte är tillämpliga på Azure DevTest Labs har uteslutits.

Om du vill se hur Azure DevTest Labs helt mappar till Azures säkerhets mätning, se den [fullständiga Azure DevTest Labs mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: när du distribuerar Azure DevTest Labs-resurser måste du skapa eller använda ett befintligt virtuellt nätverk. Se till att det valda virtuella nätverket har en nätverks säkerhets grupp som tillämpas på dess undernät och nätverks åtkomst kontroller som kon figurer ATS specifika för programmets betrodda portar och källor. När en labb resurs konfigureras med ett virtuellt nätverk, är de inte offentligt adresser bara och kan bara nås från det virtuella nätverket. Du kan också välja att skapa ett nätverk, isolerat labb, där förutom labb miljöer, labb resurser som lagrings konto och nyckel valv kommer också att vara helt isolerade och endast tillgängliga via angivna slut punkter. 

Beroende på organisationens behov använder du Azure Firewall-tjänsten för att centralt skapa, tillämpa och logga program-och nätverks anslutnings principer över prenumerationer och virtuella nätverk.

- [Så här konfigurerar du ett virtuellt nätverk för Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Så här skapar du en isolerad DevTest Labs-instans](network-isolation.md)

- [Så här skapar du en nätverks säkerhets grupp med säkerhets regler](../virtual-network/tutorial-filter-network-traffic.md)

- [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Distribuera en nätverks säkerhets grupp på nätverket som Azure DevTest Labs resurser distribueras till. Aktivera flödes loggar för nätverks säkerhets grupper i dina nätverks säkerhets grupper för trafik granskning.

Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) framför kritiska webb program som distribueras med hjälp av Azure Resource Manager mallar för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställningar för WAF och mata in loggar till ett lagrings konto, en Event Hub-eller Log Analytics-arbetsyta.

- [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Distribuera ett Azure-Virtual Network (VNet) för ett labb förbättrar säkerheten och isoleringen för ditt labb. Undernät, åtkomst kontroll principer och andra funktioner kan också hjälpa till att begränsa åtkomsten. När det distribueras i ett VNet, Azure DevTest Labs inte offentligt adresserat och kan endast nås från virtuella datorer och program i VNet.

Aktivera DDoS standard skydd på dina virtuella Azure-nätverk för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga IP-adresser.

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik. Använd Azure Security Center just-in-Time Network Access för att konfigurera NSG: er för att begränsa exponering av slut punkter till godkända IP-adresser under en begränsad period. Använd Azure Security Center anpassad nätverks härdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information.

- [Så här konfigurerar du ett virtuellt nätverk för Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

- [Förstå Azure Security Center anpassad nätverks härdning](../security-center/security-center-adaptive-network-hardening.md)

- [Förstå Azure Security Center just-in-Time-nätverk Access Control](../security-center/security-center-just-in-time.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera Network Watcher paket insamling på ditt virtuella labb nätverk för att undersöka avvikande aktiviteter. 

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Använd en Azure-brandvägg som distribueras i det virtuella nätverket med hot information aktive rad. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. Om din organisation behöver ytterligare funktioner ovanpå vad Azure-brandväggen kan tillhandahålla, väljer du ett lämpligt erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att upptäcka och/eller neka skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: när du arbetar med DevTest Labs Azure Resource Manager baserade miljöer som innehåller webb program, distribuerar du en Azure Application Gateway med https/TLS aktiverat för betrodda certifikat.

- [Så här distribuerar du Application Gateway](../application-gateway/quick-create-portal.md)

- [Så här konfigurerar du Application Gateway att använda HTTPS](../application-gateway/create-ssl-portal.md)

- [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](../application-gateway/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS för dina DevTest Labs-resurser. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Du kan också använda program säkerhets grupper för att förenkla komplex säkerhets konfiguration. Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

- [Förstå och använda program säkerhets grupper](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser med Azure policy.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resources Manager-mallar, RBAC-kontroller och-principer, i en enda skiss definition. Du kan använda skissen för nya prenumerationer och finjustera kontroll och hantering genom versions hantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks resurser som är associerade med din Azure DevTest Labs-distribution för att logiskt organisera dem i en taxonomi. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i dina Azure-resurser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser. Du kan dock hantera tids inställningar för synkronisering för dina beräknings resurser.

- [Tidssynkronisering för virtuella Windows-datorer i Azure](../virtual-machines/windows/time-sync.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure DevTest Labs instanser på hanterings Plans nivå. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på hanterings Plans nivå för dina DevTest Labs-instanser.

- [Skapa diagnostikinställningar för att skicka plattformsloggar och mått till olika målplatser](../azure-monitor/essentials/diagnostic-settings.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure DevTest Labs instanser på hanterings Plans nivå. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på hanterings Plans nivå för dina DevTest Labs-instanser.

- [Skapa diagnostikinställningar för att skicka plattformsloggar och mått till olika målplatser](../azure-monitor/essentials/diagnostic-settings.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: Azure DevTest Labs virtuella datorer (VM) skapas och ägs av kunden. Det är därför organisationens ansvar att övervaka den. Du kan använda Azure Security Center för att övervaka beräknings-OS. Data som samlas in av Security Center från operativ systemet innehåller OS-typ och version, OS (Windows-händelseloggen), processer som körs, dator namn, IP-adresser och inloggad användare. Log Analytics agent samlar även in krasch dum par-filer.

Mer information finns i följande artiklar:

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till dina Azure DevTest Labs instanser enligt organisationens regler för efterlevnad.

- [Mer information finns i följande artikel](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta. Kör frågor i Log Analytics till att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på de aktivitets logg data som kan ha samlats in för Azure DevTest Labs.

Mer information finns i följande artiklar:

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/diagnostic-settings.md)

- [Samla in och analysera Azure-aktivitets loggar i Log Analytics arbets yta i Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: använd Azure Log Analytics-arbetsytan för att övervaka och Avisera om avvikande aktiviteter i säkerhets loggar och händelser relaterade till din Azure DevTest Labs.

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att köra Ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure DevTest Labs roller](devtest-lab-add-devtest-user.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory (Azure AD) har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord kräver att ett lösen ord skapas med krav på komplexitet och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du är ansvarig för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

DevTest Labs har inte begreppet standard lösen ord.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:

- Det bör finnas fler än en ägare som tilldelats din prenumeration

- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure DevTest Labs roller](devtest-lab-add-devtest-user.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: DevTest Labs använder tjänsten Azure Active Directory (Azure AD) för identitets hantering. Tänk på följande två viktiga aspekter när du ger användarna åtkomst till en miljö baserat på DevTest Labs:
- Resurs hantering: den ger till gång till Azure Portal för att hantera resurser (skapa virtuella datorer, skapa miljöer, starta, stoppa, starta om, ta bort och tillämpa artefakter och så vidare). Resurs hantering görs i Azure med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Du tilldelar roller till användare och anger behörigheter för resurs-och åtkomst nivå.
- Virtuella datorer (nätverks nivå): i standard konfigurationen använder virtuella datorer ett lokalt administratörs konto. Om det finns en tillgänglig domän (Azure Active Directory Domain Services (Azure AD DS), en lokal domän eller en molnbaserad domän), kan datorer anslutas till domänen. Användare kan sedan använda sina domänbaserade identiteter med hjälp av domän anslutningens artefakt för att ansluta till datorerna.

- [Referens arkitektur för DevTest Labs](./devtest-lab-reference-architecture.md#architecture)

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Privileged Access-arbetsstationer (Paw) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser. 

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Använd också granskning av Azure Identity Access för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/overview-reports.md)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla/Monitoring-verktyg för säkerhets information och händelse hantering (Siem).

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra åtkomsten till labb i Azure DevTest Labs.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Förstå roller i DevTest Labs](devtest-lab-add-devtest-user.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i DevTest Labs-instanser och andra viktiga eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

- [Skapa aviseringar för DevTest Labs aktivitets logg händelser](create-alerts.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive DevTest Labs-resurser) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer och-resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem enligt en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Konfigurera taggar för DevTest Labs](devtest-lab-add-tag.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer och separata labb om det behövs för att organisera och spåra labb-och labbbaserade resurser. Stäm av inventering med jämna mellanrum och se till att obehöriga resurser tas bort från prenumerationen snabbt.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Så här skapar du ett labb med DevTest Labs](devtest-lab-create-lab.md)

- [Skapa och använda Taggar](devtest-lab-add-tag.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov. Som prenumerations administratör kan du också använda anpassningsbara program kontroller, en funktion i Azure Security Center som hjälper dig att definiera en uppsättning program som tillåts köras på konfigurerade grupper av labb datorer. Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer.

- [Aktivera adaptiv program kontroll](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd också Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna. Den kan hjälpa till med hög säkerhets-baserade miljöer, till exempel de med lagrings konton.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Som prenumerations administratör kan du använda inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara i DevTest Labs-datorer i din prenumeration. Egenskaperna för program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. För att få åtkomst till installations datum och annan information, krävs kunden för att aktivera diagnostik på gästnivå och ta med Windows-händelseloggen till en Log Analytics-arbetsyta.

Förutom att använda Ändringsspårning för övervakning av program vara kan anpassningsbara program kontroller i Azure Security Center använda Machine Learning för att analysera de program som körs på dina datorer och skapa en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla principer för att tillåta listor, vilket gör att du kan undvika att oönskad program vara används i din miljö. Du kan konfigurera gransknings läge eller framtvinga läge. Gransknings läget granskar endast aktiviteten på de skyddade virtuella datorerna. Tvingande läge tvingar fram reglerna och kontrollerar att program som inte tillåts att köra är blockerade. 

- [En introduktion till Azure Automation](../automation/automation-intro.md)

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

- [Förstå anpassningsbara program kontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Som prenumerations administratör kan du använda Ändringsspårning för att identifiera all program vara som är installerad på virtuella datorer som finns i DevTest Labs. Du kan implementera en egen process eller använda Azure Automation tillstånds konfiguration för att ta bort otillåten program vara.

- [En introduktion till Azure Automation](../automation/automation-intro.md)

- [Spåra ändringar i din miljö med Ändringsspårning-lösningen](../automation/change-tracking/overview.md)

- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: som prenumerations administratör kan du använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserad program vara körs, och all obehörig program vara blockeras från att köras på virtuella Azure-datorer som finns i DevTest Labs.

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Referensmaterial:

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: anpassningsbar program kontroll är en intelligent, automatiserad lösning från slut punkt till slut punkt från Azure Security Center, som hjälper dig att styra vilka program som kan köras på dina Azure-och icke-Azure-datorer (Windows och Linux), som finns i DevTest Labs. Observera att du måste vara prenumerations administratör för att konfigurera den här inställningen för de underliggande beräknings resurserna i DevTest Labs. Implementera en lösning från tredje part om den här inställningen inte uppfyller organisationens krav.

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom **att konfigurera blockera åtkomst** för **Microsoft Azure hanterings** appen.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: beroende på typen av skript kan du använda konfigurationer för operativ system eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript på de virtuella datorer som finns i DevTest Labs. Du kan också använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserad program vara körs och all obehörig program vara blockeras från att köras på de underliggande virtuella Azure-datorerna.

- [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&view=powershell-7)

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program med hög risk som distribueras i din Azure-miljö kan isoleras med hjälp av virtuellt nätverk, undernät, prenumerationer, hanterings grupper och så vidare. och tillräckligt säkert med antingen en Azure-brandvägg, en brand vägg för webbaserade program (WAF) eller en nätverks säkerhets grupp (NSG).

- [Konfigurera virtuellt nätverk för DevTest Labs](devtest-lab-configure-vnet.md)

- [Översikt över Azure Firewall](../web-application-firewall/overview.md)

- [Översikt över brand väggen för webb program](../virtual-network/network-security-groups-overview.md)

- [Översikt över Network-säkerhet](security-baseline.md)

- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)

- [Beslutsguide för prenumerationer](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias för att skapa anpassade principer för att granska eller tillämpa konfigurationen av dina Azure-resurser som skapats som en del av DevTest Labs. Du kan också använda inbyggda Azure Policy definitioner.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Använd Azure Security Center rekommendationer för att underhålla säkerhetskonfigurationer på alla underliggande beräknings resurser som skapats som en del av DevTest Labs. Dessutom kan du använda anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration eller DevTest labb artefakter för att upprätta säkerhets konfigurationen för det operativ system som krävs av din organisation.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)

- [Ladda upp en virtuell hård disk och Använd den för att skapa nya virtuella Windows-datorer i Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI](../virtual-machines/linux/upload-vhd.md)

- [Skapa och distribuera anpassade avbildningar till flera DevTest Labs](image-factory-save-distribute-custom-images.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy **neka** och **distribuera om de inte finns** regler för att framtvinga säkra inställningar i Azure-resurser som skapats som en del av DevTest Labs. Du kan också använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation.

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Följ rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar på dina underliggande Azure Compute-resurser som skapas som en del av ett labb. Du kan också använda Azure Resource Manager mallar, anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration för att underhålla säkerhets konfigurationen för det operativ system som krävs av din organisation. Du kan också använda avbildnings fabriks lösningen, som är en lösning för konfigurations-som-kod som skapar och distribuerar bilder automatiskt regelbundet med alla önskade konfigurationer.

Dessutom hanteras och underhålls avbildningar av virtuella Azure Marketplace-datorer som publicerats av Microsoft.

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)

- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)

- [Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [Så här skapar du en avbildnings fabrik i DevTest Labs](image-factory-create.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps.

- [Azure databaser git-självstudie](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper](/azure/devops/organizations/security/about-permissions?preserve-view=true&tabs=preview-page&view=azure-devops)

- [Integrering mellan Azure DevTest Labs-och Azure DevOps-arbetsflöde](devtest-lab-dev-ops.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: om du använder anpassade avbildningar använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) så att endast behöriga användare får åtkomst till avbildningarna. Med hjälp av ett delat avbildnings Galleri kan du dela dina avbildningar till vissa labb som behöver det. För behållar avbildningar lagrar du dem i Azure Container Registry och använder Azure RBAC för att se till att endast behöriga användare kan komma åt avbildningarna.

- [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Konfigurera ett delat avbildnings Galleri för en DevTest Labs](configure-shared-image-gallery.md)

- [Lär dig mer om Azure RBAC för Container Registry](../container-registry/container-registry-roles.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser som skapats under DevTest Labs. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser. Dessutom kan du använda Azure Automation för att distribuera konfigurations ändringar.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett. Du kan också skriva en anpassad artefakt som kan installeras på alla labb datorer för att se till att de följer organisations principer. 

- [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](../automation/automation-dsc-onboarding.md)

- [Skapa anpassade artefakter för virtuella DevTest Labs-datorer](devtest-lab-artifact-author.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure Security Center för att utföra bas linjes ökningar för dina Azure-resurser som skapats under DevTest Labs. Du kan också använda Azure Policy för att varna och granska Azure-resursfiler.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: Använd Azure Security Center för att utföra bas linje genomsökningar för OS-och Docker-inställningar för behållare som körs i labbet.

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

- [Konfigurera hanterad identitet för att distribuera Azure Resource Manager miljöer i DevTest Labs](use-managed-identities-environments.md)

- [Konfigurera hanterad identitet för att distribuera virtuella datorer i DevTest Labs](enable-managed-identities-lab-vms.md)

- [Så här skapar du ett nyckel valv](../key-vault/general/quick-create-portal.md)

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/authentication.md)

- [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [Konfigurera hanterad identitet för att distribuera Azure Resource Manager miljöer i DevTest Labs](use-managed-identities-environments.md)

- [Konfigurera hanterad identitet för att distribuera virtuella datorer i DevTest Labs](enable-managed-identities-lab-vms.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Så här konfigurerar du en inloggnings skanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Använd centralt hanterad program vara för program mot skadlig kod

**Vägledning**: Använd Microsoft Antimalware för Azure för att kontinuerligt övervaka och försvara dina resurser. För Linux använder du en lösning från tredje part mot skadlig kod.  Använd också Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton. 

- [Så här konfigurerar du Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md) 

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure App Service som finns i ett labb), men det körs inte på ditt innehåll. 

Förgenomsöka filer som har överförts till icke-Compute Azure-resurser, till exempel App Service, Data Lake Storage, Blob Storage och så vidare. 

Använd Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton. 

- [Förstå Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md) 

- [Förstå Azure Security Centers hot identifiering för data tjänster](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för program mot skadlig kod uppdateras

**Vägledning**: när du har distribuerat installeras automatiskt de senaste signatur-, plattforms-och motor uppdateringar som standard i Microsoft Antimalware för Azure. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps" för att se till att alla slut punkter för de underliggande beräknings resurserna för DevTest Labs är uppdaterade med de senaste signaturerna. Windows OS kan skyddas ytterligare med ytterligare säkerhet för att begränsa risken för virus-eller skadlig kodbaserade attacker med tjänsten Microsoft Defender Avancerat skydd som kan integreras med Azure Security Center.

- [Så här distribuerar du Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: för närvarande har Azure DevTest Labs inte stöd för säkerhets kopiering och ögonblicks bilder av virtuella datorer. Du kan dock aktivera och konfigurera Azure Backup på de underliggande virtuella Azure-datorer som finns i DevTest Labs. Du kan också konfigurera önskad frekvens och kvarhållningsperiod för automatiska säkerhets kopieringar så länge du har tillräcklig åtkomst till de underliggande beräknings resurserna. 

- [En översikt över säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)

- [Säkerhetskopiera en virtuell Azure-dator från VM-inställningarna](../backup/backup-azure-vms-first-look-arm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: för närvarande har Azure DevTest Labs inte stöd för säkerhets kopiering och ögonblicks bilder av virtuella datorer. Du kan dock skapa ögonblicks bilder av dina underliggande virtuella Azure-datorer som finns i DevTest Labs eller de hanterade diskarna som är kopplade till dessa instanser med PowerShell eller REST-API: er så länge du har tillräcklig åtkomst till de underliggande beräknings resurserna. Du kan också säkerhetskopiera alla Kundhanterade nycklar i Azure Key Vault.

Aktivera Azure Backup på virtuella Azure-datorer och den önskade frekvensen och Retentions perioden. Den innehåller fullständig säkerhets kopiering av system tillstånd. Om du använder Azure Disk Encryption hanterar Azure VM Backup automatiskt säkerhets kopieringen av Kundhanterade nycklar.

- [Säkerhetskopiera virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)

- [Översikt över säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)

- [En översikt över säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-vms-introduction.md)

- [Säkerhetskopiera Key Vault nycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Säkerställ att du regelbundet kan utföra Data återställning av innehåll inom Azure Backup. Vid behov kan du prova att återställa innehåll till ett isolerat virtuellt nätverk eller en virtuell prenumeration. Du kan också testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

Om du använder Azure Disk Encryption kan du återställa den virtuella Azure-datorn med disk krypterings nycklarna. När du använder disk kryptering kan du återställa den virtuella Azure-datorn med disk krypterings nycklarna.

- [Säkerhetskopiera virtuella Azure-datorer som använder kryptering](../backup/backup-azure-vms-encryption.md)

- [Återställa filer från Azure VM backup](../backup/backup-azure-restore-files-from-vm.md)

- [Återställa Key Vault-nycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Säkerhetskopiera och återställa en krypterad virtuell dator](../backup/backup-azure-vms-encryption.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: när du säkerhetskopierar hanterade diskar med Azure Backup krypteras virtuella datorer i vila med kryptering för lagringstjänst (SSE). Azure Backup kan också säkerhetskopiera virtuella Azure-datorer som krypteras med hjälp av Azure Disk Encryption. Azure Disk Encryption integreras med BitLocker-BEKs (-krypterings nycklar) som skyddas i ett nyckel valv som hemligheter. Azure Disk Encryption integreras också med Azure Key Vault Key Encryption Keys (KeyExchange). Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Mjuk borttagning för virtuella datorer](../backup/soft-delete-virtual-machines.md)

- [Översikt av mjuk borttagning för Azure Key Vault](../key-vault/general/soft-delete-overview.md)

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

**Vägledning**: Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom kan du markera prenumerationer med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade. 

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan svars planen efter behov. 

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta. 

- [Konfigurera en säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel. 

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md) 

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöde Azure Security Center för att automatiskt utlösa svar på säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser. 

- [Konfigurera automatisering av arbets flöden i Security Center](../security-center/workflow-automation.md)

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

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
