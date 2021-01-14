---
title: Azures säkerhetsbaslinje för Key Vault
description: Azures säkerhetsbaslinje för Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad47ac7d51de6ab497d7061961c14b5324fab931
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202461"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azures säkerhetsbaslinje för Key Vault

Azures säkerhets bas linje för Key Vault innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: integrera Azure Key Vault med en privat Azure-länk. 

Azure Private Link service ger dig åtkomst till Azure-tjänster (till exempel Azure Key Vault) och Azure-värdbaserade kund-/partner tjänster över en privat slut punkt i det virtuella nätverket.

En privat Azure-slutpunkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Så här integrerar du Key Vault med en privat Azure-länk:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och följ rekommendationer för nätverks skydd för att skydda dina Key Vault-konfigurerade resurser i Azure. 

För ytterligare information om nätverks säkerhet som tillhandahålls av Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera Azure DDoS Protection standard på de virtuella Azure-nätverk som är kopplade till dina Key Vault instanser för skydd mot distribuerade DOS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

 
Hantera Azure DDoS Protection standard med hjälp av Azure Portal: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Hot identifiering för Azure Service Layer i Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: Azure Key Vault inte använder nätverks säkerhets grupper (NSG) och flödes loggar för Azure Key Vault samlas inte in. Använd i stället Azure Private-länken för att skydda dina Azure Key Vault-instanser och aktivera diagnostikinställningar för att registrera mått och gransknings händelser.

Integrera Key Vault med en privat Azure-länk:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault loggning: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: detta krav kan uppfyllas genom att konfigurera Avancerat hot Protection (ATP) för Azure Key Vault. ATP ger ett extra lager av säkerhets information. Det här verktyget identifierar potentiellt skadliga försök att komma åt eller utnyttja Azure Key Vault-konton.

När Azure Security Center identifierar avvikande aktivitet visas aviseringar. Den skickar också prenumerations administratören med information om den misstänkta aktiviteten och rekommendationerna för att undersöka och åtgärda de identifierade hoten.

Konfigurera Avancerat skydd för Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till dina Azure Key Vault-instanser använder du Azure Service-taggar för Azure Key Vault för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Översikt över Azure Service-Taggar: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardkonfigurationer för nätverks resurser som är kopplade till dina Azure Key Vault-instanser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. nyckel valv" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Key Vault-instanser. Du kan också använda inbyggda princip definitioner som är relaterade till Azure Key Vault, till exempel:

Key Vault bör använda en tjänst slut punkt för virtuellt nätverk

Självstudie: skapa och hantera principer för att genomdriva efterlevnad:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exempel:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Snabb start: definiera och tilldela en skiss i portalen:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för resurser som rör nätverks säkerhets-och trafikflödet för dina Azure Key Vault-instanser för att tillhandahålla metadata och logisk organisation.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

Använd taggar för att organisera dina Azure-resurser:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Key Vault-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

Visa och hämta Azure aktivitets logg händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Skapa, Visa och hantera aktivitets logg aviseringar genom att använda Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: ej tillämpligt; Microsoft underhåller den tids källa som används för Azure-resurser, till exempel Azure Key Vault, för tidsstämplar i loggarna.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure Key Vault. I Azure Monitor använder du Azure Log Analytics-arbetsytan för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. 

Azure Key Vault loggning:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Snabb start: så här gör du Azure-kontroll på kort:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar på Azure Key Vault instanser för åtkomst till gransknings-, säkerhets-och diagnostikloggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

Azure Key Vault loggning:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor för arbets ytan Log Analytics som används för att hålla dina Azure Key Vault loggar, anger du kvarhållningsperioden enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

Ändra data lagrings perioden: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet för dina Azure Key Vault-skyddade resurser. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part. 

Snabb start: intern Azure-kontroll:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Kom igång med Log Analytics i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Kom igång med logg frågor i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: i Azure Security Center aktiverar du Avancerat hot Protection (ATP) för Key Vault. Aktivera diagnostikinställningar i Azure Key Vault och skicka loggar till en Log Analytics arbets yta. Publicera din Log Analytics-arbetsyta till Azure-kontroll när den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem.

Snabb start: intern Azure-kontroll:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Hantera och svara på säkerhets aviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Svara på händelser med Azure Monitor aviseringar:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Azure Key Vault bearbetar eller skapar inte relaterade loggar mot skadlig kod.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Key Vault bearbetar eller skapar inte DNS-relaterade loggar.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla en inventering av dina Azure Active Directory-registrerade program, samt alla användar konton som har åtkomst till dina Azure Key Vault nycklar, hemligheter och certifikat. Du kan använda antingen Azure Portal eller PowerShell för att fråga efter och stämma av Key Vault åtkomst. Använd följande kommando för att visa åtkomst i PowerShell:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Egenskaper. AccessPolicies

Registrera ett program med Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Säker åtkomst till ett nyckel valv:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: ej tillämpligt; Azure Key Vault har inte begreppet standard lösen ord som autentisering tillhandahålls av Active Directory och skyddas med rollbaserad åtkomst kontroll i Azure (Azure RBAC).


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton som har åtkomst till dina Azure Key Vault-instanser. Använd Azure Security Center identitets-och åtkomst hantering (för närvarande i för hands version) för att övervaka antalet aktiva administratörs konton.

Övervaka identitet och åtkomst (för hands version):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd ett tjänst huvud namn i Azure tillsammans med AppId, TenantID och ClientSecret för att sömlöst autentisera ditt program och hämta token som ska användas för att få åtkomst till dina Azure Key Vault hemligheter.

Tjänst-till-tjänst-autentisering för Azure Key Vault med .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: aktivera Azure Active Directory Multi-Factor Authentication och följ Azure Security Center identitets-och åtkomst hantering (för närvarande i för hands version) rekommendationer för att skydda dina Event Hub-aktiverade resurser.

Planera en molnbaserad Azure AD Multi-Factor Authentication-distribution:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst (för hands version):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Azure AD Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera Key Vault-aktiverade resurser. 

Arbets stationer med privilegie rad åtkomst: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/ 

Planera en molnbaserad Azure AD Multi-Factor Authentication-distribution: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd identifieringar av Azure AD-risker för att visa aviseringar och rapporter om riskfyllda användar beteenden. Om du vill ha ytterligare loggning kan du skicka aviseringar om Azure Security Center risk identifiering till Azure Monitor och konfigurera anpassade aviseringar/meddelanden med hjälp av åtgärds grupper.

Aktivera avancerat skydd (ATP) för Azure Key Vault att generera aviseringar för misstänkt aktivitet.

Distribuera Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Konfigurera Avancerat skydd för Azure Key Vault (för hands version): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Aviseringar för Azure Key Vault (för hands version): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory risk identifieringar: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Skapa och hantera åtgärds grupper i Azure Portal: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Konfigurera plats villkoret för en princip för villkorlig åtkomst och hantera dina namngivna platser. Med namngivna platser kan du skapa logiska grupperingar av IP-adressintervall eller länder och regioner. Du kan begränsa åtkomsten till känsliga resurser, till exempel Key Vault hemligheter, till dina konfigurerade namngivna platser.

Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system för Azure-resurser som Key Vault. Detta möjliggör Azure-rollbaserad åtkomst kontroll (Azure RBAC) för att administrera känsliga resurser.

 

Snabb start: skapa en ny klient i Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: granska Azure Active Directory (Azure AD)-loggar för att identifiera inaktuella konton med Azure Key Vault administrativa roller. Dessutom kan du använda Azure AD åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program som kan användas för att få åtkomst till Azure Key Vault och roll tilldelningar. Användar åtkomsten bör granskas regelbundet, till exempel var 90: e dag och se till att endast rätt användare har fortsatt åtkomst.

Azure Active Directory rapporter och övervaknings dokumentation:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Vad är Azure AD Access-granskningar?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: Aktivera diagnostikinställningar för Azure Key Vault och Azure Active Directory och skicka alla loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel försök att komma åt inaktiverade hemligheter) i Log Analytics.

Integrera Azure AD-loggar med Azure Monitor loggar: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrera från den gamla Key Vault-lösningen: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directorys funktioner för identitets skydd och identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör dina Azure Key Vault skyddade resurser. Du bör aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar. 

Rapporten om riskfyllda inloggningar i Azure Active Directory-portalen: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Gör så här: Konfigurera och aktivera risk principer: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Publicera Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ej tillämpligt; Customer Lockbox stöds inte för Azure Key Vault.

Tjänster och scenarier som stöds i allmän tillgänglighet: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information på Azure Key Vault aktiverade resurser. 

Använd taggar för att organisera dina Azure-resurser: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: du kan skydda åtkomsten till Azure Key Vault genom att använda tjänst slut punkter för virtuella nätverk som kon figurer ATS för att begränsa åtkomsten till vissa undernät.

När brand Väggs reglerna är aktiva kan du bara utföra Azure Key Vault data Plans åtgärder när din begäran kommer från tillåtna undernät eller IP-adressintervall. Detta gäller även för Azure Key Vault åtkomst i Azure Portal. Även om du kan bläddra till ett nyckel valv från Azure Portal kanske du inte kan lista nycklar, hemligheter eller certifikat om klient datorn inte finns med i listan över tillåtna. Detta påverkar också Azure Key Vault väljaren och andra Azure-tjänster. Du kanske kan se listor över nyckel valv, men inte lista nycklar, om brand Väggs reglerna förhindrar att klient datorn gör det.

Konfigurera Azure Key Vault brand väggar och virtuella nätverk: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Tjänst slut punkter för virtuella nätverk för Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: alla data som lagras i Azure Key Vault betraktas som känsliga. Använd Azure Key Vault data Plans åtkomst kontroller för att kontrol lera åtkomsten till Azure Key Vault hemligheter. Du kan också använda Key Vault inbyggda brand väggen för att kontrol lera åtkomsten till nätverks lagret. Om du vill övervaka åtkomsten till Azure Key Vault aktiverar du Key Vault diagnostikinställningar och skickar loggar till ett Azure Storage konto eller Log Analytics arbets yta.

Säker åtkomst till ett nyckel valv: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Konfigurera Azure Key Vault brand väggar och virtuella nätverk: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault loggning: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: all trafik som ska Azure Key Vault för åtkomst till autentisering, hantering och data plan krypteras och går över https: port 443. (Det är dock ibland HTTP [port 80] trafik för CRL.) Azure Key Vault fortsätter att tillåta att TLS 1,1 och TLS 1,0-data matas in. Data kan begränsas till TLS 1,2 genom konfiguration på klient sidan.



Åtkomst Azure Key Vault bakom en brand vägg: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: ej tillämpligt; alla data i Azure Key Vault (hemligheter, nycklar och certifikat) anses vara känsliga.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: säker åtkomst till hanterings-och data planet för dina Azure Key Vault-instanser.

Säker åtkomst till ett nyckel valv:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: Microsoft hanterar den underliggande infrastrukturen för Azure Key Vault och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Vad är Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azures kund data skydd:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: alla hanterade objekt (nyckel, certifikat och hemligheter) krypteras i vila i Azure Key Vault.

Support dokumentation:

- [Krypterings modell och nyckel hanterings tabell](../../security/fundamentals/encryption-atrest.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure Key Vault Analytics-lösningen i Azure Monitor för att granska Azure Key Vault gransknings händelse loggar.

Azure Key Vault Analytics-lösning i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Key Vault.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; Microsoft utför korrigerings hantering på de underliggande system som har stöd för Key Vault.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Key Vault.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd standard risk klassificeringarna (säkra poäng) som tillhandahålls av Azure Security Center.

Förbättra dina säkra poäng i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure Key Vault instanser) i din prenumeration. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Hämta prenumerationer som det aktuella kontot har åtkomst till.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för att Azure Key Vault resurser som ger metadata till att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra Azure Key Vault instanser och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Skapa ytterligare en Azure-prenumeration:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Skapa hanterings grupper för resurs organisation och hantering:

https://docs.microsoft.com/azure/governance/management-groups/create

Använd taggar för att organisera dina Azure-resurser: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

**Vägledning**: definiera listan över godkända Azure-resurser och godkänd program vara för dina beräknings resurser

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure-principer för att ställa in begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

Självstudie: skapa och hantera principer för att genomdriva efterlevnad: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för Azure som helhet samt beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure-principer för att ställa in begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Självstudie: skapa och hantera principer för att genomdriva efterlevnad: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exempel: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: begränsa användarnas möjlighet att interagera med AzureResources Manager via skript

**Vägledning**: Använd den villkorliga Azure-åtkomsten för att begränsa användarnas möjlighet att interagera med Azure Resource Manager (arm) genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". Detta kan förhindra att resurser skapas och ändras i en hög säkerhets miljö, till exempel med Key Vault konfiguration.

Hantera åtkomst till Azure-hantering med villkorlig åtkomst:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. nyckel valv" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för dina Azure Key Vault-instanser. Du kan också använda inbyggda Azure Policy definitioner för Azure Key Vault till exempel:

Key Vault objekt ska vara återställnings bara

Distribuera diagnostikinställningar för Key Vault till Log Analytics arbets yta

Diagnostikloggar i Key Vault ska vara aktive rad

Key Vault bör använda en tjänst slut punkt för virtuellt nätverk

Distribuera diagnostikinställningar för Key Vault till Händelsehubben

Använd rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina Azure Key Vault-instanser.

Så här visar du tillgängliga Azure Policy alias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Självstudie: skapa och hantera principer för att genomdriva efterlevnad:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att genomdriva säkra inställningar för dina Azure Key Vault-aktiverade resurser. 

Självstudie: skapa och hantera principer för att genomdriva efterlevnad:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Förstå Azure Policys effekter: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Azure Key Vault-aktiverade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Dokumentation om Azure databaser: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. nyckel valv" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure Security Center för att utföra bas linje genomsökningar för dina Azure Key Vault-skyddade resurser 

  

Så här åtgärdar du rekommendationer i Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; Detta riktmärke är avsett för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program. Se till att Azure Key Vault mjuk borttagning har Aktiver ATS.

Så här integrerar du med Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du en Key Vault:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Så här autentiserar du till Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Så här tilldelar du en Key Vault åtkomst princip:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program. 

  

* [Integrera med Azure Managed Identities](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Så här skapar du en Key Vault](quick-create-portal.md)

* [Så här autentiserar du till Key Vault](authentication.md)

* [Så här tilldelar du en Key Vault åtkomst princip](assign-access-policy-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.  
  
 Konfigurera inloggnings skannern: https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som stöder Azure-tjänster (till exempel Azure Key Vault), men den körs inte på kund innehållet.

Skanna allt innehåll som laddas upp eller skickas till icke-Compute Azure-resurser som Azure Key Vault. Microsoft kan inte komma åt dina data i dessa instanser.

Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: säkerställa vanliga automatiserade säkerhets kopieringar av Key Vault certifikat, nycklar, hanterade lagrings konton och hemligheter, med följande PowerShell-kommandon:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Alternativt kan du lagra dina Key Vault säkerhets kopior inom Azure Backup.

Så här säkerhetskopierar du Key Vault certifikat: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Så här säkerhetskopierar du Key Vault nycklar: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Säkerhetskopiera Key Vault hanterade lagrings konton: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Säkerhetskopiera Key Vault hemligheter: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Så här aktiverar du Azure Backup: https://docs.microsoft.com/azure/backup



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: säkerhetskopiera Key Vault certifikat, nycklar, hanterade lagrings konton och hemligheter med följande PowerShell-kommandon:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Alternativt kan du lagra dina Key Vault säkerhets kopior inom Azure Backup.

Så här säkerhetskopierar du Key Vault certifikat: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Så här säkerhetskopierar du Key Vault nycklar: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Säkerhetskopiera Key Vault hanterade lagrings konton: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Säkerhetskopiera Key Vault hemligheter: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Så här aktiverar du Azure Backup: https://docs.microsoft.com/azure/backup



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: regelbundet utföra Data återställning av Key Vault certifikat, nycklar, hanterade lagrings konton och hemligheter, med följande PowerShell-kommandon:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Så här återställer du Key Vault certifikat:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Så här återställer du Key Vault nycklar: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Så här återställer du Key Vault hanterade lagrings konton: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Så här återställer du Key Vault hemligheter: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: se till att mjuk borttagning är aktiverat för Azure Key Vault. Med mjuk borttagning kan du återställa borttagna nyckel valv och valv objekt, till exempel nycklar, hemligheter och certifikat. 

Använda Azure Key Vault ' Soft Delete ': 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten. Dessa processer bör ha fokus på att skydda känsliga system, till exempel de som använder Key Vault hemligheter.

Konfigurera automatisering av arbets flöden i Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Vägledning om hur du skapar en egen svars process för säkerhets incident:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center: s uppbyggnad av en incident:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data som Azure Key Vault hemligheter.


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att skydda dina Azure Key Vault-instanser och relaterade resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

Så här ställer du in Azure Security Center säkerhets kontakt:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure Key Vault-aktiverade resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.  Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel. 

 

Så här konfigurerar du kontinuerlig export: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Strömma aviseringar till Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure Key Vault-skyddade resurser. 

 

Konfigurera automatisering av arbets flöde och Logic Apps: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: du behöver inte utföra Penn testning på den Azure Key Vault tjänsten direkt, men vi rekommenderar att du testar dina Azure-resurser som använder Key Vault för att säkerställa säkerheten för hemligheterna.

Du måste följa Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program, här: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../../security/benchmarks/security-baselines-overview.md)