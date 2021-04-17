---
title: Azure-säkerhetsbaslinje för Azure IoT Hub
description: Säkerhetsbaslinjen Azure IoT Hub innehåller procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99243dbfac7fddb8a4fe9d64ed64ab706245ec3c
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587637"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Azure-säkerhetsbaslinje för Azure IoT Hub

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Microsoft Azure IoT Hub. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas av de säkerhetskontroller **som definieras** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure IoT Hub. **Kontroller** som inte är Azure IoT Hub har undantagits.

 
Om du vill Azure IoT Hub mappa helt till Azure Security Benchmark kan du se den fullständiga [Azure IoT Hub säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** IoT Hub är en plattform som en tjänst (PaaS) för flera innehavare och delar samma pool med maskinvaruresurser för beräkning, nätverk och lagring. IoT Hub värdnamn mappar till en offentlig slutpunkt med en offentligt dirigerbar IP-adress via Internet. Olika kunder delar den IoT Hub offentliga slutpunkten, och IoT-enheter i över breda nätverk och lokala nätverk kan komma åt den. Microsoft har utformat tjänsten för fullständig isolering mellan varje klients data och arbetar kontinuerligt för att säkerställa det här resultatet.

IoT Hub funktioner som meddelanderoutning, filuppladdning och massimport/massexport av enheter kräver också anslutning från IoT Hub till en kundägd Azure-resurs via dess offentliga slutpunkt. Dessa anslutningsvägar utgör tillsammans utgående trafik från IoT Hub till kundresurser.

Rekommenderar att du begränsar anslutningen till dina Azure-resurser (inklusive Azure IoT Hub) via ett virtuellt nätverk som du äger och arbetar för att minska exponeringen för anslutningar i ett isolerat nätverk och aktivera lokal nätverksanslutning direkt till Azure-stamnätverket. Använd Azure Private Link och azure privat slutpunkt, där det är möjligt, för att aktivera privat åtkomst till dina tjänster från andra virtuella nätverk. 

När privat åtkomst har upprättats inaktiverar du offentlig nätverksåtkomst för IoT Hub för ytterligare säkerhet. Den här kontrollen på nätverksnivå tillämpas på en specifik IoT Hub-resurs, vilket säkerställer isolering. För att tjänsten ska fortsätta att vara aktiv för andra kundresurser med hjälp av den offentliga sökvägen förblir den offentliga slutpunkten matchningsbar, IP-adresser kan upptäckas och portar förblir öppna. Detta är inget problem eftersom Microsoft integrerar flera säkerhetslager för att säkerställa fullständig isolering mellan klienter.

Håll öppna maskinvaruportar på dina enheter på ett minimum för att undvika oönskad åtkomst. Skapa dessutom mekanismer för att förhindra eller identifiera fysisk manipulering av enheten.

- [Stöd för virtuella IoT-nätverk](virtual-network-support.md)

- [Hantera offentlig nätverksåtkomst för IoT Hub](iot-hub-public-network-access.md)

- [Klientisolering i Azure](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [bästa praxis för loT-nätverk](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [Azure Private Link översikt](../private-link/private-link-overview.md)

- [Azure-nätverkssäkerhetsgrupp](../virtual-network/network-security-groups-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverkskort

**Vägledning:** Använd Azure Security Center och följ nätverksskyddsrekommendationerna för att skydda dina Azure-nätverksresurser. Aktivera flödesloggar för nätverkssäkerhetsgruppen och skicka loggarna till ett Azure Storage konto för granskning. Du kan också skicka flödesloggarna till en Log Analytics-arbetsyta och sedan använda Trafikanalys för att ge insikter om trafikmönster i Ditt Azure-moln. Några fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet, identifiera hotpunkter och säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.
 
- [Så här aktiverar du flödesloggar för nätverkssäkerhetsgrupp](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)
 
- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Blockera kända skadliga IP-adresser IoT Hub IP-filterregler. Skadliga försök registreras och varnas också via Azure Security Center för IoT.

Azure DDoS Protection Basic är redan aktiverat och tillgängligt utan extra kostnad som en del av IoT Hub. Alltid på trafikövervakning och riskreducering i realtid för vanliga attacker på nätverksnivå ger samma försvar som används av Microsofts onlinetjänster. Hela skalan för Azures globala nätverk kan användas för att distribuera och minimera angreppstrafik mellan regioner.

- [IoT Hub IP-filter](iot-hub-ip-filtering.md)

- [Azure Security Center för misstänkt IP-adresskommunikation i IoT](/azure/asc-for-iot/concept-security-alerts)

- [Hantera Azure DDoS Protection Basic](../ddos-protection/ddos-protection-overview.md)

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Ej tillämpligt; Den här rekommendationen är avsedd för erbjudanden som producerar nätverkspaket som kan registreras och visas av kunder. IoT Hub skapar inte nätverkspaket som är kundriktade och är inte utformade för att distribueras direkt till virtuella Azure-nätverk.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Välj ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för nyttolastgranskning.  När nyttolastgranskning inte är ett krav Azure Firewall hotinformation användas. Azure Firewall filtrering baserad på hotinformation används för att varna om och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera valfri brandväggslösning vid var och en av organisationens nätverksgränser för att identifiera och/eller blockera skadlig trafik. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du aviseringar med Azure Firewall](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** För resurser som behöver åtkomst till Azure IoT Hub kan du använda Virtual Network för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (till exempel AzureIoTHub) i rätt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Så här använder du tjänsttaggar för Azure IoT](iot-hub-understand-ip-address.md)
- [Mer information om hur du använder tjänsttaggar](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade Azure IoT Hub nätverksnamnrymder med Azure Policy. Använd Azure Policy alias i namnrymderna "Microsoft.Devices" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för Machine Learning namnområden. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverksresurser som är associerade Azure IoT Hub din distribution för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till Azure IoT Hub. Skapa aviseringar inom Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Skapa aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Mata in loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av Azure IoT Hub. I Azure Monitor du Använda Log Analytics-arbetsytor för att köra frågor mot och utföra analyser och använda lagringskonton för långsiktig lagring/arkiveringslagring. Du kan också aktivera och ta med data för att Azure Sentinel eller en säkerhetsincident- och händelsehantering från tredje part (SIEM).

- [Konfigurera Azure IoT-loggar](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera Azure IoT-diagnostikinställningar för Azure-resurser för åtkomst till gransknings-, säkerhets- och resursloggar. Aktivitetsloggar, som är automatiskt tillgängliga, inkluderar händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element.

- [Konfigurera Azure IoT Hub loggar](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Förstå loggning och olika loggtyper i Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Devices**:

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** I Azure Monitor anger du loggens kvarhållningsperiod för Log Analytics-arbetsytor som är associerade med dina Azure IoT Hub-instanser enligt organisationens efterlevnadsregler.

- [Så här anger du parametrar för loggbevarande](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultaten från Azure IoT Hub. Använd Azure Monitor och en Log Analytics-arbetsyta för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och ta med data till Azure Sentinel eller en SIEM från tredje part. 

- [Övervaka Azure IoT-hälsa](monitor-iot-hub.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Komma igång med Log Analytics-frågor](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Azure Security Center för IoT med en Log Analytics-arbetsyta för övervakning och avisering om avvikande aktivitet som finns i säkerhetsloggar och händelser. Du kan också aktivera och ta med data för att Azure Sentinel. Du kan också definiera driftaviseringar med Azure Monitor som kan ha säkerhetskonsekvenser, till exempel när trafiken sjunker oväntat.

- [Övervaka Azure IoT Hub hälsotillstånd](monitor-iot-hub.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Azure Security Center för IoT-aviseringar](/azure/asc-for-iot/concept-security-alerts)

- [Så här varnar du om loggdata i Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning:** Inte tillämpligt; Azure IoT Hub inte bearbetar eller producerar loggar relaterade till skadlig kod.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning:** Ej tillämpligt; Azure IoT Hub inte bearbetar eller producerar DNS-relaterade loggar.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Med rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du hantera åtkomsten till Azure IoT Hub via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper av tjänstens huvudnamn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller efterfrågas via verktyg som Azure CLI eller Azure PowerShell eller Azure Portal.

- [Så här får du en katalogroll i Azure Active Directory (Azure AD) med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Åtkomsthantering till Azure IoT Hub-resurser styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standardlösenord.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton.

Du kan också aktivera just-in-time-åtkomst till administrativa konton med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management och Azure Resource Manager.

- [Läs mer om Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning:** För användare som har IoT Hub använder du Azure Active Directory (Azure AD) enkel inloggning. Använd Azure Security Center rekommendationer för identitet och åtkomst.

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering för att skydda din övergripande Azure-klientorganisation och dra nytta av alla tjänster. IoT Hub-tjänsten har inte stöd för multifaktorautentisering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd en arbetsstation med säker privilegierad åtkomst (PAW) för administrativa uppgifter som kräver utökade privilegier.

- [Förstå säkra arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du Azure Active Directory multifaktorautentisering (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och avisering om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) säkerhetsrapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Hantera Azure-resurser endast från godkända platser

**Vägledning:** För användare som IoT Hub åtkomst stöds inte villkorlig åtkomst. Du kan åtgärda detta genom att använda Azure Active Directory (Azure AD) namngivna platser för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner för din övergripande Azure-klientorganisation, vilket drar nytta av alla tjänster, inklusive IoT Hub.

- [Så här konfigurerar du azure AD-namngivna platser](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** För användaråtkomst till IoT Hub, Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för vilodata och under överföring. Azure AD använder också salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

För enhets- och tjänståtkomst använder IoT Hub säkerhetstoken och SAS-token (signatur för delad åtkomst) för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. 

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub säkerhetstoken](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure AD-identitet och åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Använd Azure AD Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring/)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Distribuera Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Du har åtkomst till Azure Active Directory(Azure AD) inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med alla SIEM-/övervakningsverktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar på Log Analytics-arbetsytan.

Användaren Azure Monitor resursloggar för att övervaka obehöriga anslutningsförsök i kategorin Anslutningar.

- [Integrera Azure-aktivitetsloggar med Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Konfigurera resursloggar för IoT Hub](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Avisering om kontoinloggningens beteendeavvikelse

**Vägledning:** Använd Azure Active Directory (Azure AD) Identity Protection för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [ Så här visar du riskfyllda inloggningar i Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [ Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [ Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** I supportscenarier där Microsoft behöver åtkomst till kunddata begärs det direkt från kunden.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.
 
- [ Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera isolering med separata prenumerationer och hanteringsgrupper för enskilda säkerhetsdomäner, till exempel miljötyp och känslighetsnivå för data. Du kan begränsa åtkomstnivån till dina Azure-resurser som dina program och företagsmiljöer kräver. Du kan styra åtkomsten till Azure-resurser via Azure RBAC.
  
- [ Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [ Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [ Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Använd en lösning från tredje part från Azure Marketplace i nätverksnätverket för att övervaka obehörig överföring av känslig information och blockera sådana överföringar samtidigt som informationssäkerhetspersonal varnas.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och skyddar mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och -tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen versionerna 1.0, 1.1 och 1.2. Vi rekommenderar starkt att du använder TLS 1.2 som önskad TLS-version vid anslutning till IoT Hub.

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, om tillämpligt.

- [TLS-stöd i IoT Hub](iot-hub-tls-support.md)
- [Förstå kryptering under överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure IoT Hub. Implementera en lösning från tredje part om det behövs i efterlevnadssyfte.

För den underliggande Azure-plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och tar lång tid att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning:** För kontrollplansanvändaråtkomst till IoT Hub använder du Azure RBAC för att styra åtkomsten. För dataplansåtkomst till IoT Hub använder du principer för delad åtkomst för IoT Hub.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure IoT Hub och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Händelser i Azure-aktivitetsloggen](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuera en automatiserad korrigeringshanteringslösning för programvarutitlar från tredje part

**Vägledning:** Inte tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka-till-tillbaka-sårbarhetsgenomsökningar

**Vägledning:** Inte tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Ej tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Inte tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser (inte alla resurser stöder taggar, men de flesta) för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra tillgångar. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.
  
- [ Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla en förteckning över godkända Azure-resurser

**Vägledning:** Skapa en förteckning över godkända Azure-resurser och godkänd programvara för beräkningsresurser enligt organisationens behov.

Varje IoT Hub har ett identitetsregister som kan användas för att skapa resurser per enhet i tjänsten. Enskilda eller grupper av enhetsidentiteter kan läggas till i en lista över tillåtna, eller en blockeringslista, vilket ger fullständig kontroll över enhetsåtkomst.

- [IoT Hub identity registry](iot-hub-devguide-identity-registry.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer. 

Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna.  Se till att alla Azure-resurser som finns i miljön godkänns. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka för program som inte är godkända i beräkningsresurser

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända program

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Skapa frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Upprätthålla en förteckning över godkända programvarutitlar

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd Azure Active Directory (Azure AD) villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [ Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera program med hög risk

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för din Azure Iot Hub-tjänst med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.Devices" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure IoT Hub tjänster.

Azure Resource Manager har möjlighet att exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhetskraven för din organisation.

Du kan också använda rekommendationerna från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta säkra operativsystemkonfigurationer

**Vägledning:** Ej tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Upprätthålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser. Dessutom kan du använda Azure Resource Manager för att upprätthålla säkerhetskonfigurationen för dina Azure-resurser som krävs av din organisation.  
 
- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)
- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)
- [översikt Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Upprätthåll säkra operativsystemkonfigurationer

**Vägledning:** Ej tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure Policy för dina Azure IoT Hub relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure Repos](/azure/devops/repos)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning:** Inte tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.Devices" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Så här använder du alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera konfigurationshanteringsverktyg för operativsystem

**Vägledning:** Ej tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina Azure-resurser. Du kan också använda Azure Policy för att varna och granska Azure-resurskonfigurationer. 
 
- [ Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning:** Inte tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** IoT Hub använder säkerhetstoken och SAS-token (signatur för delad åtkomst) för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. 

Använd hanterade identiteter tillsammans med Azure Key Vault för att förenkla hemlighetshanteringen för dina molnprogram.

- [IoT Hub säkerhetstoken](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Använda hanterade identiteter för IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [Så här skapar du ett nyckelvalv](../key-vault/general/quick-create-portal.md)

- [Så här tillhandahåller du Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** IoT Hub använder säkerhetstoken och SAS-token (signatur för delad åtkomst) för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket.

Använd hanterade identiteter för att förse Azure-tjänster med en automatiskt hanterad identitet Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

- [IoT Hub säkerhetstoken](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Så här konfigurerar du hanterade identiteter för IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 
 
- [  Så här ställer du in skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska laddas upp till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Microsoft Anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure IoT Hub), men den körs inte på kundinnehåll.

Det är ditt ansvar att försöka allt innehåll som laddas upp till icke-beräkningsbaserade Azure-resurser. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kundinnehåll för din räkning.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Azure IoT Hub-tjänsten tillhandahåller metoder och ramverk för att ge dina IoT Hub hög tillgängliga och återställningsbara efter katastrofer baserat på specifika affärsmål. 

- [Hög tillgänglighet och haveriberedskap för IoT Hub](iot-hub-ha-dr.md)

- [Så här klonar du IoT Hub](iot-hub-how-to-clone.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Azure IoT Hub rekommenderar att den sekundära IoT-hubben måste innehålla alla enhetsidentiteter som kan ansluta till lösningen. Lösningen bör behålla geo-replikerade säkerhetskopior av enhetsidentiteter och ladda upp dem till den sekundära IoT-hubben innan du växlar den aktiva slutpunkten för enheterna. Exportfunktionen för enhetsidentiteter i IoT Hub användbar i det här sammanhanget.

- [Hög tillgänglighet och haveriberedskap för IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub exportera enhetsidentitet](iot-hub-bulk-identity-mgmt.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Azure IoT Hub rekommenderar att den sekundära IoT-hubben måste innehålla alla enhetsidentiteter som kan ansluta till lösningen. Lösningen bör behålla geo-replikerade säkerhetskopior av enhetsidentiteter och ladda upp dem till den sekundära IoT-hubben innan du växlar den aktiva slutpunkten för enheterna. Exportfunktionen för enhetsidentiteter i IoT Hub användbar i det här sammanhanget.

Utför regelbundet dataåterställning av innehåll i en säkerhetskopia. Se till att du kan återställa säkerhetskopierade kund hanterade nycklar.

- [Hög tillgänglighet och haveriberedskap för IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub exportera enhetsidentitet](iot-hub-bulk-identity-mgmt.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Aktivera mjuk borttagning och rensningsskydd i Key Vault att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra säkerhetskopior aktiverar du mjuk borttagning för att spara och återställa data när blobar eller blobögonblicksbilder tas bort.

 
- [Förstå Azure RBAC](../role-based-access-control/overview.md)

- [Mjuk borttagning för Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning:** Utveckla en guide för incidenter för din organisation. Se till att det finns skriftliga planer för incidenthantering som definierar alla roller för personal samt faserna för incidenthantering och incidenthantering från identifiering till granskning efter incident. 
  
- [ Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Använd NIST:s guide för hantering av säkerhetsincidenter för att hjälpa dig att skapa en egen plan för incidenthantering](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller det analytiskt använda för att utfärda aviseringen samt konfidensnivån för att det fanns en skadlig avsikt bakom aktiviteten som ledde till aviseringen.

  
 Markera dessutom prenumerationer med hjälp av taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data. Det är ditt ansvar att prioritera åtgärder för aviseringar baserat på allvarligheten i de Azure-resurser och den miljö där incidenten inträffade.
  
- [ Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets incidentsvarsfunktioner för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan din åtgärdsplan efter behov.
  
- [ NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig eller obehörig part. Granska incidenter i efterhand för att se till att problemen är lösta.
  
- [ Så här ställer du Azure Security Center säkerhetskontakten](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen för löpande export för att identifiera risker för Azure-resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.
  
- [ Så här konfigurerar du löpande export](../security-center/continuous-export.md)
 
- [ Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen för automatisering av arbetsflöden Azure Security Center att automatiskt utlösa svar på säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser.
  
- [ Så här konfigurerar du arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att intrångstesterna inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
