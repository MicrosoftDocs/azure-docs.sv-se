---
title: Azures säkerhets bas linje för Azure IoT Hub
description: Azure IoT Hub Security-datalinjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2947faa8c7b56fdbe4cf549529b63d506b810750
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570856"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Azures säkerhets bas linje för Azure IoT Hub

Azures säkerhets bas linje för Microsoft Azure IoT Hub innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution. Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer. Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: som standard mappas IoT Hubs värdnamn till en offentlig slut punkt med en offentligt DIRIGERAd IP-adress via Internet. Olika kunder delar den här IoT Hub offentliga slut punkten och IoT-enheter i över WAN-nätverk och lokala nätverk kan komma åt dem.

IoT Hub funktioner som meddelanderoutning, fil uppladdning och Mass import/export kräver även anslutning från IoT Hub till en Azure-resurs som ägs av en kund via den offentliga slut punkten. Dessa anslutnings vägar utgör tillsammans den utgående trafiken från IoT Hub till kund resurser.

Rekommendera att begränsa anslutningen till dina Azure-resurser (inklusive Azure IoT Hub) via ett virtuellt nätverk som du äger och arbetar för att minska risken för anslutning i ett isolerat nätverk och möjliggör lokal nätverks anslutning direkt till Azure stamnät nätverket. Använd Azures privata länk och privat Azure-slutpunkt, där det är möjligt, för att aktivera privat åtkomst till dina tjänster från andra virtuella nätverk.

Behåll öppna maskin varu portar i dina enheter så att de inte är minimala för att undvika oönskad åtkomst. Dessutom kan du bygga mekanismer för att förhindra eller identifiera fysisk manipulering av enheten.

- [Stöd för virtuella IoT-nätverk](virtual-network-support.md)
- [metod tips för stor nätverk](../iot-fundamentals/security-recommendations.md?context=azure%2fiot-hub%2frc%2frc#networking)
- [Översikt över Azure privat länk](../private-link/private-link-overview.md)
- [Azure nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och följ rekommendationerna för nätverks skydd för att skydda dina Azure-nätverks resurser. Aktivera flödes loggar för nätverks säkerhets grupper och skicka loggarna till ett Azure Storage konto för granskning. Du kan också skicka flödes loggarna till en Log Analytics arbets yta och sedan använda Trafikanalys för att ge insikter om trafik mönster i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet, identifiera frekventa fläckar och säkerhetshot, förstå trafikflödes mönster och hitta nätverks-felkonfigurationer.
 
- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)
 
- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: blockera kända skadliga IP-adresser med IoT Hub IP-filter regler. Skadliga försök registreras också via Azure Security Center för IoT.

Azure DDoS Protection Basic är redan aktiverat och tillgängligt utan ytterligare kostnad som en del av IoT Hub. Always on trafikövervakning och real tids minskning av vanliga attacker på nätverks nivå, ger samma försvar som Microsofts onlinetjänster. Hela skalningen av Azures globala nätverk kan användas för att distribuera och minska angrepps trafiken mellan regioner.

- [IoT Hub IP-filter](iot-hub-ip-filtering.md)

- [Azure Security Center för IoT misstänkt IP-adress kommunikation](../defender-for-iot/concept-security-alerts.md)

- [Hantera Azure DDoS Protection Basic](../ddos-protection/ddos-protection-overview.md)

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för erbjudanden som producerar nätverks paket som kan registreras och visas av kunder. IoT Hub skapar inte nätverks paket som är riktade mot kunden och är inte utformade för att distribueras direkt till virtuella Azure-nätverk.

**Azure Security Center övervakning**: Nej

**Ansvars område**: inte tillämpligt

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll.  När nytto lasts granskning inte är ett krav kan du använda Azure Firewall Threat intelligence. Azure Firewall Threat Intelligence-baserad filtrering används för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att identifiera och/eller blockera skadlig trafik. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till din Azure-IoT Hub använder du Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (till exempel AzureIoTHub) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Använda service märken för Azure IoT](iot-hub-understand-ip-address.md)
- [Mer information om att använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är kopplade till dina Azure IoT Hub-namnområden med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. Devices" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för dina Machine Learning namn områden. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks resurser som är associerade med din Azure IoT Hub-distribution för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure IoT Hub. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar den tids källa som används för Azure-resurser, till exempel Azure IoT Hub för tidsstämplar i loggarna.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure IoT Hub. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda lagrings konton för långsiktig/lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM).

- [Konfigurera Azure IoT-loggar](monitor-iot-hub-reference.md#resource-logs)
- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera Azure IoT Diagnostic-inställningar på Azure-resurser för åtkomst till gransknings-, säkerhets-och resurs loggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

- [Konfigurera Azure IoT Hub loggar](monitor-iot-hub-reference.md#resource-logs)

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperioden för Log Analytics arbets ytor som är kopplade till dina Azure IoT Hub-instanser enligt organisationens regler för efterlevnad.

- [Ange parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet från Azure-IoT Hub. Använd Azure Monitor och en Log Analytics arbets yta för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och inaktivera data till Azure Sentinel eller en SIEM från tredje part. 

- [Övervaka Azure IoT Health](monitor-iot-hub.md)
- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Komma igång med Log Analytics frågor](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Security Center för IoT med en Log Analytics arbets yta för övervakning och avisering om avvikande aktivitet i säkerhets loggar och händelser. Du kan också aktivera och inaktivera data till Azure Sentinel. Du kan också definiera drifts aviseringar med Azure Monitor som kan ha säkerhets effekter, till exempel när trafiken ignoreras oväntad.

- [Övervaka Azure IoT Hub Health](monitor-iot-hub.md)
- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)
- [Azure Security Center för IoT-aviseringar](../defender-for-iot/concept-security-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Azure IoT Hub bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure IoT Hub bearbetar eller skapar inte DNS-relaterade loggar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure rollbaserad åtkomst kontroll (Azure RBAC) gör att du kan hantera åtkomst till Azure IoT Hub via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, eller Azure PowerShell eller Azure Portal. 

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: åtkomst hantering till Azure IoT Hub-resurser styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standard lösen ord.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton.

Du kan också aktivera just-in-Time-åtkomst till administrativa konton genom att använda Azure AD Privileged Identity Management och Azure Resource Manager.

- [Läs mer om Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd Azure Active Directory SSO för användare som använder IoT Hub. Använd Azure Security Center identitets-och åtkomst rekommendationer. 

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure AD MFA för att skydda din totala Azure-klient, och dra nytta av alla tjänster. Det finns ingen MFA-support för IoT Hubs tjänsten.  

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station eller Paw) för administrativa uppgifter som kräver förhöjda privilegier.

- [Förstå säkra, Azure-hanterade arbets stationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)
- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: för användare som har åtkomst till IoT Hub stöds inte villkorlig åtkomst. För att undvika detta kan du använda Azure AD-namngivna platser för att endast tillåta åtkomst från särskilda logiska grupperingar av IP-adressintervall eller länder/regioner för din övergripande Azure-klient, får alla tjänster, inklusive IoT Hub. 

- [Så här konfigurerar du Azure AD-namngivna platser](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system för användar åtkomst till IoT Hub. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

För enhets-och tjänst åtkomst använder IoT Hub säkerhetstoken och SAS-token (signatur för delad åtkomst) för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. 

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub säkerhetstoken](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure AD-identitets-och åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

Använd Azure AD Privileged Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)
- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)
- [Distribuera Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till inloggnings aktiviteter, gransknings-och risk händelse logg källor i Azure AD, vilket gör att du kan integrera med alla Siem/övervaknings verktyg. 

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan. 

Användare Azure Monitor resurs loggar för att övervaka otillåtna anslutnings försök i anslutnings kategorin.

- [Så här integrerar du Azure-aktivitets loggar med Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Konfigurera resurs loggar för IoT Hub](monitor-iot-hub.md#collection-and-routing)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure AD Identity Protection funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.
  
- [ Visa Azure AD-riskfyllda inloggningar](../active-directory/identity-protection/overview-identity-protection.md)
  
- [ Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: i support scenarier där Microsoft behöver åtkomst till kund information kommer den att begäras direkt från kunden. 

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.
 
- [ Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner, till exempel miljö typ och data känslighets nivå. Du kan begränsa åtkomst nivån till dina Azure-resurser som dina program och företags miljöer kräver. Du kan styra åtkomsten till Azure-resurser via Azure RBAC.
  
- [ Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)
- [ Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)
- [ Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Använd en lösning från tredje part från Azure Marketplace i nätverks omkretser för att övervaka obehörig överföring av känslig information och blockera sådana överföringar samtidigt som du varnar information om informations säkerhet.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och-tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen version 1,0, 1,1 och 1,2. Vi rekommenderar starkt att du använder TLS 1,2 som den önskade TLS-versionen när du ansluter till IoT Hub.

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall.

- [TLS-stöd i IoT Hub](iot-hub-tls-support.md)
- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure IoT Hub. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande Azure-plattformen som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och ger bra längd för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: du kan använda Azure RBAC för att kontrol lera åtkomsten för användar åtkomst i kontroll planet till IoT Hub. För data Plans åtkomst till IoT Hub använder du delade åtkomst principer för IoT Hub.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för Azure IoT Hub och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: IoT Hub stöder kryptering av data i vila med Kundhanterade nycklar (CMK), även kallat "ta med din egen nyckel" (BYOK).

Azure IoT Hub tillhandahåller kryptering av data i vila och under överföring som de skrivs i våra data Center och dekrypterar det åt dig när du har åtkomst till den. Som standard använder IoT Hub Microsoft-hanterade nycklar för att kryptera data i vila.

- [Kryptering av data i vila med Kundhanterade nycklar för IoT Hub](iot-hub-customer-managed-keys.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure IoT Hub och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure IoT Hub.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; Microsoft utför korrigerings hantering på de underliggande system som har stöd för Azure IoT Hub. 

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera en automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för Azure-resurser (inte alla resurser som stöder taggar, men de flesta) för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.
  
- [ Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)
  
- [ Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)
  
- [ Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

Varje IoT Hub har ett identitets register som kan användas för att skapa resurser per enhet i tjänsten. Enskilda eller grupper av enhets identiteter kan läggas till i en lista över tillåtna eller blockerade enheter, vilket möjliggör fullständig kontroll över enhets åtkomsten.

- [IoT Hub identitets register](iot-hub-devguide-identity-registry.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer. 

Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna.  Se till att alla Azure-resurser som finns i miljön är godkända. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure AD för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".
  
- [ Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standard säkerhetskonfigurationer för tjänsten Azure IoT Hub med Azure policy. Använd Azure Policy alias i namn rymden "Microsoft. Devices" för att skapa anpassade principer för att granska eller tillämpa konfigurationen för Azure IoT Hub-tjänsterna.

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhets kraven för din organisation.

Du kan också använda rekommendationerna från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser. Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation.  
 
- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)
- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)
- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för Azure IoT Hub eller relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn rymden "Microsoft. Devices" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure Security Center för att utföra bas linjes ökningar för dina Azure-resurser. Du kan också använda Azure Policy för att varna och granska Azure-resursfiler. 
 
- [ Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: IoT Hub använder SÄKERHETSTOKEN och SAS-token (signatur för delad åtkomst) för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. 

Använd hanterade identiteter tillsammans med Azure Key Vault för att förenkla hemlig hantering för dina moln program.

- [IoT Hub säkerhetstoken](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Använda hanterade identiteter för IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [Så här skapar du ett nyckel valv](../key-vault/general/quick-create-portal.md)
- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: IoT Hub använder SÄKERHETSTOKEN och SAS-token (signatur för delad åtkomst) för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. 

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [IoT Hub säkerhetstoken](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Konfigurera hanterade identiteter för IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 
 
- [  Så här konfigurerar du en inloggnings skanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Använd centralt hanterad program vara för program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure App Service), men det körs inte på kund innehållet.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure IoT Hub), men det körs inte på kund innehåll.

Det är ditt ansvar att i förväg genomsöka allt innehåll som laddas upp till Azure-resurser som inte är Compute. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kund innehåll för program mot skadlig kod.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för program mot skadlig kod uppdateras

**Vägledning**: ej tillämpligt; Detta riktmärke är avsett för beräknings resurser. Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster, men det körs inte på kund innehållet.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Azure IoT Hub-tjänsten tillhandahåller metoder och ramverk för att göra dina IoT Hub-tjänster hög tillgängliga och kan återvinnas från katastrofer baserat på specifika affärs mål. 

- [Hög tillgänglighet och haveriberedskap för IoT Hub](iot-hub-ha-dr.md)

- [Klona IoT Hub](iot-hub-how-to-clone.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Azure IoT Hub rekommenderar att den sekundära IoT Hub måste innehålla alla enhets identiteter som kan ansluta till lösningen. Lösningen bör behålla geo-replikerade säkerhets kopior av enhets identiteter och överföra dem till den sekundära IoT-hubben innan du växlar den aktiva slut punkten för enheterna. Funktionen för export av enhets identiteter i IoT Hub är användbar i den här kontexten.

- [Hög tillgänglighet och haveriberedskap för IoT Hub](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exportera IoT Hub enhets identitet](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Azure IoT Hub rekommenderar att den sekundära IoT Hub måste innehålla alla enhets identiteter som kan ansluta till lösningen. Lösningen bör behålla geo-replikerade säkerhets kopior av enhets identiteter och överföra dem till den sekundära IoT-hubben innan du växlar den aktiva slut punkten för enheterna. Funktionen för export av enhets identiteter i IoT Hub är användbar i den här kontexten.

Utför regelbundet data återställning av innehåll i säkerhets kopian. Se till att du kan återställa säkerhetskopierade nycklar som hanteras av kunden.

- [Hög tillgänglighet och haveriberedskap för IoT Hub](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exportera IoT Hub enhets identitet](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Aktivera mjuk borttagning och rensning av skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra säkerhets kopior aktiverar du mjuk borttagning för att spara och återställa data när blobbar eller BLOB-ögonblicksbilder tas bort.
 
 
- [Förstå Azure RBAC](../role-based-access-control/overview.md)
- [Mjuk borttagning för Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten. 
  
- [ Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller den analytiskt som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

  
 Dessutom kan du markera prenumerationer med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.
  
- [ Säkerhets aviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Använd taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan svars planen efter behov.
  
- [ NISTs publikation – guide för att testa, träna och träna program för IT-planer och-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.
  
- [ Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.
  
- [ Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)
 
- [ Strömma aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöde Azure Security Center för att automatiskt utlösa svar på säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.
  
- [ Konfigurera automatisering av arbets flöden i Security Center](../security-center/workflow-automation.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)