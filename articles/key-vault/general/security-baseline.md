---
title: Azure-säkerhetsbaslinje för Key Vault
description: Säkerhetsbaslinjen Key Vault innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f56de94df4fd5d4dd154ae8485edb9eed88364c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753352"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azure-säkerhetsbaslinje för Key Vault

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../../security/benchmarks/overview-v1.md) för Key Vault. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Key Vault. **Kontroller** som inte Key Vault, eller för vilka ansvaret är Microsofts, har undantagits.

Information om hur Key Vault mappning helt till Azure Security Benchmark finns i den fullständiga [mappningsfilen Key Vault säkerhetsbaslinje.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Integrera Azure Key Vault med Azure Private Link. Azure Private Link Service kan du komma åt Azure-tjänster (till exempel Azure Key Vault) och Azure-värdtjänster för kunder/partner via en privat slutpunkt i ditt virtuella nätverk.

En privat Azure-slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gatewayer, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontroll.

- [Så här integrerar du Key Vault med Azure Private Link](/azure/key-vault/private-link-service)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Använd Azure Security Center och följ nätverksskyddsrekommendationerna för att skydda dina Key Vault konfigurerade resurser i Azure. 

- [Mer information om nätverkssäkerhet som tillhandahålls av Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera Azure DDoS Protection Standard på de virtuella Azure-nätverk som är associerade med dina Key Vault-instanser för skydd mot distribuerade dosattacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

 
- [Hantera Azure DDoS Protection Standard med hjälp av Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Hotidentifiering för Azure-tjänstlagret i Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Det här kravet kan uppfyllas genom att konfigurera Advanced Threat Protection (ATP) för Azure Key Vault. ATP ger ett extra lager säkerhetsinformation. Det här verktyget identifierar potentiellt skadliga försök att komma åt eller utnyttja Azure Key Vault konton.

När Azure Security Center identifierar avvikande aktivitet visas aviseringar. Den skickar även ett e-postmeddelande till prenumerationsadministratören med information om den misstänkta aktiviteten och rekommendationer för hur de identifierade hoten ska undersökas och åtgärdas.

- [Konfigurera Advanced Threat Protection för Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** För resurser som behöver åtkomst till dina Azure Key Vault-instanser använder du Azure-tjänsttaggar för Azure Key Vault för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Översikt över Azure-tjänsttaggar](../../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade med Azure Key Vault instanser med Azure Policy. Använd Azure Policy alias i namnrymderna "Microsoft.KeyVault" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina Azure Key Vault-instanser. Du kan också använda inbyggda principdefinitioner relaterade till Azure Key Vault, till exempel:
- Key Vault bör använda en tjänstslutpunkt för virtuellt nätverk

Läs mer i följande referenser:

- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-exempel](/azure/governance/policy/samples)

- [Definiera och tilldela en skiss i portalen](../../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för resurser som rör nätverkssäkerhet och trafikflöde för dina Azure Key Vault instanser för att tillhandahålla metadata och logisk organisation.

Använd de inbyggda Azure Policy som rör taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser som är relaterade Azure Key Vault instanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av viktiga nätverksresurser sker.

- [Visa och hämta Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log-view)

- [Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Mata in loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av Azure Key Vault. Inom Azure Monitor kan du använda Azure Log Analytics-arbetsytan för att köra frågor mot och utföra analyser och använda Azure Storage-konton för långsiktig lagring/arkiveringslagring. Du kan också aktivera och ta med data till Azure Sentinel eller en SIEM från tredje part. 

- [Azure Key Vault loggning](/azure/key-vault/key-vault-logging)

- [Snabbstart: Så här Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera diagnostikinställningar på Azure Key Vault instanser för åtkomst till gransknings-, säkerhets- och diagnostikloggar. Aktivitetsloggar, som är automatiskt tillgängliga, inkluderar händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element.

- [Azure Key Vault loggning](/azure/key-vault/key-vault-logging)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Inom Azure Monitor anger du kvarhållningsperioden enligt organisationens efterlevnadsregler för Log Analytics-arbetsytan som används för att innehålla Azure Key Vault-loggar. Använd Azure Storage-konton för långsiktig lagring/arkiveringslagring.

- [Ändra kvarhållningsperioden för data](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultaten för dina Azure Key Vault-skyddade resurser. Använd Azure Monitor Log Analytics-arbetsytan för att granska loggar och köra frågor på loggdata. Du kan också aktivera och ta med data till Azure Sentinel siem eller siem från tredje part. 

- [On-board Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Kom igång med Log Analytics i Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Kom igång med loggfrågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** I Azure Security Center aktivera ADVANCED Threat Protection (ATP) för Key Vault. Aktivera diagnostikinställningar i Azure Key Vault och skicka loggar till en Log Analytics-arbetsyta. Registrera Log Analytics-arbetsytan för Azure Sentinel eftersom den tillhandahåller en soar-lösning (Security Orchestration Automated Response). På så sätt kan spelböcker (automatiserade lösningar) skapas och användas för att åtgärda säkerhetsproblem.

- [Snabbstart: Registrera Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Hantera och besvara säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Svara på händelser med Azure Monitor-aviseringar](/azure/azure-monitor/learn/tutorial-response)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Upprätthåll en förteckning över dina Azure Active Directory-registrerade program (Azure AD), samt alla användarkonton som har åtkomst till dina Azure Key Vault,hemligheter och certifikat. Du kan använda antingen Azure Portal eller PowerShell för att fråga och stämma av Key Vault åtkomst. Om du vill visa åtkomst i PowerShell använder du följande kommando:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Properties.AccessPolicies

- [Registrera ett program med Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Säker åtkomst till ett nyckelvalv](security-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton som har åtkomst till dina Azure Key Vault instanser. Använd Azure Security Center identitets- och åtkomsthantering (för närvarande i förhandsversion) för att övervaka antalet aktiva administrativa konton.

- [Övervaka identitet och åtkomst (förhandsversion)](../../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning:** Använd azure-tjänstens huvudnamn tillsammans med AppId, TenantID och ClientSecret för att sömlöst autentisera ditt program och hämta den token som ska användas för att komma åt dina Azure Key Vault hemligheter.

- [Tjänst-till-tjänst-autentisering för att Azure Key Vault med hjälp av .NET](/azure/key-vault/service-to-service-authentication)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory multifaktorautentisering (Azure AD) och följ rekommendationerna i Azure Security Center Identity and Access Management (för närvarande i förhandsversion) för att skydda event hub-aktiverade resurser.

- [Planera en distribution av Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst (förhandsversion)](../../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Använd säkra, Azure-hanterade arbetsstationer för administrativa uppgifter

**Vägledning:** Använda en arbetsstation för privilegierad åtkomst (PAW) med multifaktorautentisering i Azure AD konfigurerad för att logga in och konfigurera Key Vault aktiverade resurser.

- [Arbetsstationer för privilegierad åtkomst (PAW)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Planera en molnbaserad distribution av Azure AD-multifaktorautentisering](../../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure AD-riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden. För ytterligare loggning skickar du Azure Security Center riskidentifieringsaviseringar till Azure Monitor och konfigurerar anpassade aviseringar/meddelanden med hjälp av åtgärdsgrupper.

Aktivera Advanced Threat Protection (ATP) för att Azure Key Vault generera aviseringar för misstänkt aktivitet.

- [Distribuera Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Konfigurera advanced threat protection för Azure Key Vault (förhandsversion)](/azure/security-center/advanced-threat-protection-key-vault)

- [Aviseringar för Azure Key Vault (förhandsversion)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Azure AD-riskidentifiering](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Skapa och hantera åtgärdsgrupper i Azure-portalen](/azure/azure-monitor/platform/action-groups)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Konfigurera platsvillkoret för en princip för villkorsstyrd åtkomst och hantera dina namngivna platser. Med namngivna platser kan du skapa logiska gruppningar av IP-adressintervall eller länder och regioner. Du kan begränsa åtkomsten till känsliga resurser, till exempel Key Vault hemligheter, till dina konfigurerade namngivna platser.

- [Vad är platsvillkoret för villkorlig Azure Active Directory (Azure AD)?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt autentiserings- och auktoriseringssystem för Azure-resurser, till exempel Key Vault. På så sätt kan Azures rollbaserade åtkomstkontroll (Azure RBAC) administrera känsliga resurser.

- [Skapa en ny klientorganisation i Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Granska Azure Active Directory (Azure AD) för att identifiera inaktuella konton med Azure Key Vault administrativa roller. Använd dessutom Azure AD-åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram som kan användas för att komma åt Azure Key Vault och rolltilldelningar. Användaråtkomst bör regelbundet granskas, till exempel var 90:e dag, för att se till att endast rätt användare har fortsatt åtkomst.

- [Dokumentation om Azure AD-rapporter och -övervakning](/azure/active-directory/reports-monitoring/)

- [Vad är Azure AD-åtkomstgranskningar?](../../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Aktivera diagnostikinställningar för Azure Key Vault och Azure Active Directory (Azure AD), skicka alla loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel försök att komma åt inaktiverade hemligheter) i Log Analytics.

- [Integrera Azure AD-loggar med Azure Monitor loggar](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migrera från den gamla Key Vault lösningen](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontobeteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) identity protection- och riskidentifieringsfunktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till dina Azure Key Vault skyddade resurser. Du bör aktivera automatiserade svar Azure Sentinel att implementera organisationens säkerhetssvar.

- [Rapport om riskfyllda inloggningar i Azure AD-portalen](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Gör så här för att: Konfigurera och aktivera riskprinciper](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information om Azure Key Vault aktiverade resurser. 

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Du kan skydda åtkomsten till Azure Key Vault genom att använda tjänstslutpunkter för virtuellt nätverk som konfigurerats för att begränsa åtkomsten till specifika undernät.

När brandväggsreglerna har tillämpas kan du bara Azure Key Vault åtgärder på dataplanet när din begäran kommer från tillåtna undernät eller IP-adressintervall. Detta gäller även för Azure Key Vault åtkomst i Azure Portal. Även om du kan bläddra till ett nyckelvalv från Azure Portal kanske du inte kan visa nycklar, hemligheter eller certifikat om klientdatorn inte finns med i listan över tillåtna. Detta påverkar även Azure Key Vault och andra Azure-tjänster. Du kanske kan se listor över nyckelvalv, men inte lista nycklar, om brandväggsregler hindrar klientdatorn från att göra det.

- [Konfigurera Azure Key Vault brandväggar och virtuella nätverk](/azure/key-vault/key-vault-network-security)

- [Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Alla data som lagras Azure Key Vault betraktas som känsliga. Använd Azure Key Vault åtkomstkontroller på dataplanet för att styra åtkomsten till Azure Key Vault hemligheter. Du kan också Key Vault den inbyggda brandväggen för att styra åtkomsten på nätverkslagret. Om du vill övervaka åtkomst Azure Key Vault du aktivera Key Vault diagnostikinställningar och skicka loggar till ett Azure Storage konto eller En Log Analytics-arbetsyta.

- [Säker åtkomst till ett nyckelvalv](security-overview.md)

- [Konfigurera Azure Key Vault brandväggar och virtuella nätverk](/azure/key-vault/key-vault-network-security)

- [Azure Key Vault loggning](/azure/key-vault/key-vault-logging)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning:** Säker åtkomst till hanterings- och dataplanet för Azure Key Vault instanser.

- [Säker åtkomst till ett nyckelvalv](security-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Key Vault Analytics-lösningen i Azure Monitor granska Azure Key Vault granska händelseloggar.

- [Azure Key Vault Analytics-lösning i Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Använd standardriskklassificeringar (säkerhetspoäng) som tillhandahålls av Azure Security Center.

- [Förbättra dina säkerhetspoäng i Azure Security Center](/azure/security-center/security-center-secure-score)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (Azure Key Vault instanser) i din prenumeration. Se till att du har rätt (läsbehörighet) i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Kör din första Resource Graph med hjälp av Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Hämta prenumerationer som det aktuella kontot kan komma åt](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Rollbaserad åtkomstkontroll (RBAC) i Azure](../../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar för att Azure Key Vault resurser som ger metadata för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggar, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure Key Vault instanser och relaterade resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Skapa ytterligare en Azure-prenumeration](/azure/billing/billing-create-subscription)

- [Skapa hanteringsgrupper för organisering och hantering av resurser](/azure/governance/management-groups/create)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure-principer för att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Du kan också använda Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../../governance/policy/tutorials/create-and-manage.md)

- [Snabbstart: Kör din första Resource Graph med hjälp av Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure-principer för att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Läs mer i följande referenser:

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-exempel](/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management". Detta kan förhindra att resurser skapas och ändras i en miljö med hög säkerhet, till exempel de med Key Vault konfiguration.

- [Hantera åtkomst till Azure-hantering med villkorlig åtkomst](../../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.KeyVault" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure Key Vault instanser. Du kan också använda inbyggda Azure Policy definitioner för Azure Key Vault till exempel:

- Key Vault objekt ska kunna återställas

- Distribuera diagnostikinställningar för Key Vault till Log Analytics-arbetsytan

- Diagnostikloggar i Key Vault ska vara aktiverade

- Key Vault bör använda en tjänstslutpunkt för virtuellt nätverk

- Distribuera diagnostikinställningar för Key Vault till Händelsehubb

- Använd rekommendationer från Azure Security Center som en säker konfigurationsbaslinje för dina Azure Key Vault instanser.

Läs mer i följande referenser:

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Upprätthålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att tillämpa säkra inställningar för Azure Key Vault-aktiverade resurser. 

- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)

  
- [Förstå Azure Policy effekter](../../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure Policy för dina Azure Key Vault resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

- [Lagra kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentation om Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.KeyVault" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina Azure Key Vault-skyddade resurser.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlighetshanteringen för dina molnprogram. Se till Azure Key Vault mjuk borttagning är aktiverat.

- [Integrera med hanterade Azure-identiteter](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Skapa en Key Vault](quick-create-portal.md)

- [Autentisera till Key Vault](authentication.md)

- [Tilldela en Key Vault åtkomstprincip](assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlighetshantering för dina molnprogram.

  

- [Så här integrerar du med azure-hanterade identiteter](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Så här skapar du en Key Vault](quick-create-portal.md)    

- [Så här autentiserar du till Key Vault](authentication.md)

- [Tilldela en Key Vault åtkomstprincip](assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.  
  
- [ Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska överföras till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Microsofts program mot skadlig kod är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Key Vault), men den körs inte på kundinnehåll.

Skanna innehåll som laddas upp eller skickas till Azure-resurser som inte beräknas i förväg, till exempel Azure Key Vault. Microsoft kan inte komma åt dina data i dessa instanser.

- [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../../security/fundamentals/antimalware.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Säkerställ regelbundna automatiserade säkerhetskopieringar av dina Key Vault certifikat, nycklar, hanterade lagringskonton och hemligheter med följande PowerShell-kommandon:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Du kan också lagra dina Key Vault säkerhetskopior i Azure Backup.

- [Säkerhetskopiera Key Vault certifikat](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Säkerhetskopiera Key Vault nycklar](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Så här säkerhetskopierar du Key Vault hanterade lagringskonton](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Säkerhetskopiera Key Vault hemligheter](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Så här aktiverar du Azure Backup](/azure/backup)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Utför säkerhetskopieringar av dina Key Vault certifikat, nycklar, hanterade lagringskonton och hemligheter med följande PowerShell-kommandon:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Du kan också lagra dina Key Vault säkerhetskopior i Azure Backup.

- [Säkerhetskopiera Key Vault certifikat](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Säkerhetskopiera Key Vault nycklar](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Så här säkerhetskopierar du Key Vault hanterade lagringskonton](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Säkerhetskopiera Key Vault hemligheter](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Så här aktiverar du Azure Backup](/azure/backup)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Utför regelbundet dataåterställning av dina Key Vault certifikat, nycklar, hanterade lagringskonton och hemligheter med följande PowerShell-kommandon:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Läs mer i följande referenser:

- [Återställa Key Vault certifikat](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Så här återställer du Key Vault nycklar](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Så här återställer du Key Vault hanterade lagringskonton](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Återställa Key Vault hemligheter](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Se till att mjuk borttagning är aktiverat för Azure Key Vault. Mjuk borttagning möjliggör återställning av borttagna nyckelvalv och valvobjekt som nycklar, hemligheter och certifikat. 

- [Så här använder Azure Key Vault mjuk borttagning](/azure/key-vault/key-vault-soft-delete-powershell)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten. Dessa processer bör ha fokus på att skydda känsliga system, till exempel de som använder Key Vault hemligheter.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Kunden kan också använda NIST:s guide för hantering av säkerhetsincidenter för att underlätta skapandet av en egen plan för incidenthantering](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller det mått som används för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen. Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-produktion) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data, till exempel Azure Key Vault hemligheter.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att testa systemets incidentsvarsfunktioner regelbundet för att skydda dina Azure Key Vault instanser och relaterade resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST:s publikation: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig eller obehörig part.  Granska incidenter efter fakta för att se till att problemen är lösta.

- [Så här ställer du Azure Security Center säkerhetskontakt](../../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker Azure Key Vault-aktiverade resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt.  Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel. 

 

- [Så här konfigurerar du kontinuerlig export](../../security-center/continuous-export.md) 

  

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" om säkerhetsaviseringar och rekommendationer för att skydda dina Azure Key Vault-skyddade resurser. 

 

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Prestandatest för Azure-säkerhet: Intrångstester och Red Team-övningar.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
