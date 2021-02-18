---
title: Azures säkerhets bas linje för virtuellt WAN
description: Den virtuella WAN-säkerhetslösningen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6f487467b08332eea4ee19a7fb8836d843bd254f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582653"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Azures säkerhets bas linje för virtuellt WAN

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) för att Microsoft Azure virtuella WAN. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definierats av Azures säkerhets benchmark och relaterade rikt linjer som gäller för virtuella WAN-nätverk. **Kontroller** som inte gäller för virtuellt WAN har uteslutits.

Om du vill se hur det virtuella WAN-nätverket mappas fullständigt till Azures säkerhets benchmark, se den [fullständiga virtuella WAN-filen för bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: Microsoft Azure virtuella WAN-nätverk tillhandahåller anpassade routningsfunktioner och ger kryptering för din ExpressRoute-trafik. All väg hantering tillhandahålls av den virtuella Hub-routern, som också möjliggör överförings anslutning mellan virtuella nätverk. Kryptering av din ExpressRoute-trafik med Virtual WAN tillhandahåller en krypterad överföring mellan de lokala nätverken och virtuella Azure-nätverk över ExpressRoute, utan att gå över det offentliga Internet eller använda offentliga IP-adresser. 

- [Kryptering av ExpressRoute-trafik](virtual-wan-about.md#encryption)

- [Använd privat länk i virtuellt WAN](howto-private-link.md)

- [Scenarier för anpassad routning](scenario-any-to-any.md)

- [Dokumentation om anpassad routningstabell](how-to-virtual-hub-routing.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra 

**Vägledning**: Microsoft Azure ExpressRoute erbjuder privat anslutning till Azure Virtual WAN. Eftersom ExpressRoute-anslutningarna inte går via det offentliga Internet, ger ExpressRoute mer tillförlitlighet, snabbare hastighet och lägre fördröjning än vanliga Internet anslutningar. Du kan också använda ett virtuellt privat nätverk för att ansluta till Azure via antingen plats-till-plats (S2S) VPN eller punkt-till-plats (P2S) VPN.

- [ExpressRoute i virtuellt WAN](virtual-wan-expressroute-portal.md)

- [Översikt över webbplats-till-plats-VPN](virtual-wan-site-to-site-portal.md)

- [Översikt över peka till plats-VPN](virtual-wan-point-to-site-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: Virtual WAN exponerar inte några slut punkter för externa nätverk som kräver att de skyddas med konventionella nätverks skydd. Du är kostnads fri att skydda resurser i ekrar virtuella nätverk (alla virtuella nätverk som är anslutna till en virtuell hubb) med hjälp av virtuella nätverks skydds tjänster. 

Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. 

Välj Azure-tillhandahållen DDoS Protection för att skydda dina till gångar mot attacker på dina virtuella Azure-nätverk. Använd Azure Security Center för att identifiera risker för felaktig konfiguration som är relaterade till dina nätverks resurser.

- [Dokumentation om Azure Firewall](../firewall/index.yml)

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](../ddos-protection/manage-ddos-protection.md) 

- [Rekommendationer för Azure Security Center](../security-center/recommendations-reference.md#recs-networking)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Virtual WAN är en Microsoft-hanterad tjänst. Den erbjuder inte inbyggd intrångs identifiering eller funktioner för intrångs skydd. Det finns dock säkerhetsfunktioner som tillhandahålls för virtuella WAN-nätverk via Azure-brandväggen för att möjliggöra en enhetlig punkt för princip kontroll. Du kan skapa en Azure Firewall-princip och länka principen till en virtuell WAN-hubb så att den befintliga virtuella WAN-hubben fungerar som en säker virtuell hubb, med de nödvändiga Azure Firewall-resurserna distribuerade.

- [Konfigurera Azure-brandväggen i en virtuell WAN-hubb](howto-firewall.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: förenkla nätverks säkerhets regler genom att använda Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange namnet på service tag i fältet käll-eller mål för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

- [Förstå och använda program säkerhets grupper](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Dokumentation om Azure Firewall](../firewall/index.yml)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: säker Domain Name Service (DNS)

**Vägledning**: säkra DNS-funktioner tillhandahålls för virtuella WAN-nätverk med Azure-brandväggen. Konfigurera Azure-brandväggen så att den fungerar som en DNS-proxy som blir mellanhand för DNS-begäranden från virtuella klient datorer till en DNS-server. För anpassade DNS-serverkonfigurationer aktiverar du DNS-proxy för att undvika matchning av DNS-matchning och möjliggör fullständigt kvalificerat domän namns filtrering i nätverks reglerna. 

- [Dokumentation om Azure Firewall](../firewall/index.yml)

- [DNS-inställningar för Azure-brandväggen](../firewall/dns-settings.md)

- [Använd Azure-brandväggen som en DNS-vidarebefordrare med privat länk](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt system för identiteter och autentisering

**Vägledning**: Azure Active Directory (Azure AD) är standard tjänsten för identitets-och åtkomst hantering för Azure-tjänster. inklusive virtuellt WAN. Standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, Azure Virtual Machines (Linux och Windows), Azure Key Vault, plattform som en tjänst (PaaS) och program vara som en tjänst (SaaS).
- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser

Skydda Azure AD som en hög prioritet i din organisations säkerhets praxis för molnet. Utvärdera dina identitets-och säkerhets position med säkerhets poängen från Security Center för att mäta hur nära konfigurationen matchar Microsofts rekommendationer för bästa praxis. Vid behov implementerar du Microsofts rekommendationer för bästa praxis för förbättringar av din säkerhets position.

Azure AD stöder också externa identiteter, vilket gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet. 

Läs informationen om hur du använder Azure AD i punkt-till-plats-VPN-scenarier på de refererade länkarna.

- [Skapa en Azure Active Directory-klient (AD) för P2S OpenVPN-protokoll anslutningar](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurera Azure Active Directory autentisering för användar-VPN](virtual-wan-point-to-site-azure-ad.md)

- [Klientorganisation i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd kraftfulla verifieringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: för närvarande tillhandahålls Azure Active Directory (Azure AD)-autentisering via integrering med virtuella WAN-platser med virtuella WAN-nätverk.

Azure Active Directory (Azure AD) är standard tjänsten för identitets-och åtkomst hantering för Azure-tjänster. Azure AD stöder starka verifierings kontroller med multifaktorautentisering och starka metoder för lösen ords kryptering.

Azure AD rekommenderar följande för starka verifierings kontroller:

- Multifaktorautentisering – aktivera Azure AD multifaktor-autentisering och följ rekommendationer för identitets-och åtkomst hantering i Azure Security Center för rekommenderade säkerhets metoder. Tillämpa multifaktorautentisering på alla, Välj användare eller per användar nivå baserat på inloggnings villkor och risk faktorer

- Autentisering med lösen ord – tre alternativ för lösen ords avföljer är tillgängliga. Dessa inkluderar, Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort

Se till att den högsta nivån av metoden stark autentisering används för administratörer och privilegierade användare, följt av en samlad princip för en stark autentiseringsprincip för andra användare.

- [Så här aktiverar du MFA i P2S VPN för virtuellt WAN](openvpn-azure-ad-mfa.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering för VPN-användare (punkt-till-plats) med hjälp av Azure AD-autentisering. Konfigurera multifaktorautentisering på basis av varje användare eller utnyttja multifaktorautentisering med villkorlig åtkomst. Villkorlig åtkomst ger en detaljerad kontroll över hur en andra faktor ska höjas. Den kan tillåta tilldelning av multifaktorautentisering till endast VPN och utesluta andra program som är kopplade till Azure AD-klienten. 

Observera att Azure AD-autentisering endast är tillgängligt för gateways med OpenVPN-protokoll och klienter som kör Windows.

- [Vad är villkorsstyrd åtkomst](../active-directory/conditional-access/overview.md)

- [Konfigurera Azure Active Directory autentisering för användar-VPN](virtual-wan-point-to-site-azure-ad.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning**: plats-till-plats-VPN i Virtual WAN använder i förväg delade nycklar (PSK) som identifieras, skapas och hanteras av kunden i sina Azure Key Vault. Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

För GitHub kan du använda funktionen för identifiering av interna hemligheter till att identifiera autentiseringsuppgifter och andra hemligheter i koden.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Identifiering av hemligheter i GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning**: Azure Virtual WAN använder Azures rollbaserade åtkomst kontroller (Azure RBAC) för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som beviljas privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Begränsa också åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän kontrollanter, säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access) 

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Kryptera känslig information under överföring

**Vägledning**: använda punkt-till-plats-VPN, plats-till-plats-VPN och krypterad Express väg med virtuellt WAN för dina anslutnings krav. VPN-kryptering skyddar data under överföring från "out of band"-attacker (till exempel trafik avbildning) för att säkerställa att angripare inte kan läsa eller ändra data. 

- [Punkt-till-plats-VPN](virtual-wan-point-to-site-portal.md)

- [Plats-till-plats-VPN](virtual-wan-site-to-site-portal.md)

- [Krypterad ExpressRoute](vpn-over-expressroute.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security Benchmark: Tillgångshantering](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Med detta måste säkerhets insikter och risker alltid aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Obs! Ytterligare behörigheter kan krävas för att få insyn i arbetsbelastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Se till att säkerhetsteamet har åtkomst till tillgångsinventering och metadata

**Vägledning**: Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön. Azure Virtual WAN stöder även Azure Resource Manager-baserade resurs distributioner med vilka du kan exportera till gångs mallar. 

- [Beslutsguide för namngivning och taggning av resurser](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd bara godkända Azure-tjänster

**Vägledning**: Använd Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks. Det virtuella WAN-nätverket ger tillsammans många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda drift gränssnitt. Virtuella WAN-gatewayer, ExpressRoute-gatewayer och Azure-brandväggen har loggning och mått som är tillgängliga via Azure Monitor. 
 

- [Virtuella WAN-loggar och-mått](logs-metrics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig Azure-åtkomst för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: punkt-till-plats-VPN med virtuellt WAN är integrerat med Azure Active Directory (Azure AD). Azure AD innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel, SIEM eller övervaknings verktyg för mer avancerade hot övervakning och analys användnings fall. Dessa är:

- Logga in – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – ett riskfylldt inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Använd Azure Security Center för att skapa aviseringar för vissa misstänkta aktiviteter, till exempel ett ovanligt antal misslyckade autentiseringsförsök, inklusive föråldrade konton i prenumerationen. Förutom den grundläggande säkerhetsövervakningen kan du använda Threat Protection-modulen från Security Center för att samla in mer djupgående säkerhets aviseringar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du få insyn i kontoavvikelser i de enskilda resurserna.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurera Azure-brandväggen i en virtuell WAN-hubb](howto-firewall.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering i hanteringen av identiteter och åtkomst i Azure

**Vägledning**: punkt-till-plats-VPN med virtuellt WAN är integrerat med Azure Active Directory (Azure AD). Azure AD innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel, SIEM eller övervaknings verktyg för mer avancerade hot övervakning och analys användnings fall. Dessa är:

- Logga in – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – ett riskfylldt inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Använd Azure Security Center för att skapa aviseringar för vissa misstänkta aktiviteter, till exempel ett ovanligt antal misslyckade autentiseringsförsök, inklusive föråldrade konton i prenumerationen. Förutom den grundläggande säkerhetsövervakningen kan du använda Threat Protection-modulen från Security Center för att samla in mer djupgående säkerhets aviseringar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du få insyn i kontoavvikelser i de enskilda resurserna.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurera Azure-brandväggen i en virtuell WAN-hubb](howto-firewall.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning av nätverksaktiviteter i Azure

**Vägledning**: övervaka Azure Virtual WAN med Azure Monitor. Det virtuella WAN-nätverket ger tillsammans många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda drift gränssnitt. Virtuella WAN-gatewayer, ExpressRoute-gatewayer och Azure-brandväggen har loggning och mått som är tillgängliga via Azure Monitor. Aktivitets logg poster samlas in som standard och kan visas i Azure Portal. Du kan använda Azure aktivitets loggar (tidigare kallade drift loggar och gransknings loggar) för att visa alla åtgärder som skickats till din Azure-prenumeration.

En rad olika diagnostikloggar är också tillgängliga för virtuella WAN-nätverk och kan konfigureras för den virtuella WAN-resursen med Azure Portal.  Du kan välja att skicka till Log Analytics, strömma till en händelsehubben eller att bara arkivera till ett lagrings konto. 
 

- [Virtuella WAN-loggar och-mått](logs-metrics.md)

- [Azure Firewall-loggar och mått](../firewall/logs-and-metrics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: Azure aktivitets loggar, aktive ras automatiskt, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina virtuella Azure-resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel under fel sökning eller för att övervaka hur en användare i organisationen ändrade en resurs.

Aktivera Azures resurs loggar för virtuellt WAN-nätverk. Du kan använda Azure Security Center och Azure Policy för att aktivera resurs loggar och insamling av logg data. Dessa loggar kan vara kritiska för senare utredning av säkerhets incidenter och utföra kriminal tekniska övningar.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

**Vägledning**: Aktivera säkerhets loggning för virtuellt WAN-nätverk med Azure Monitor. Det virtuella WAN-nätverket ger tillsammans många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda drift gränssnitt. Virtuella WAN-gatewayer, ExpressRoute-gatewayer och Azure-brandväggen har loggning och mått som är tillgängliga via Azure Monitor. Aktivitets logg poster samlas in som standard och kan visas i Azure Portal. Du kan använda Azure aktivitets loggar (tidigare kallade drift loggar och gransknings loggar) för att visa alla åtgärder som skickats till din Azure-prenumeration. 

En rad olika diagnostikloggar är också tillgängliga för virtuella WAN-nätverk och kan konfigureras för den virtuella WAN-resursen med Azure Portal. Skicka till Log Analytics, strömma till en händelsehubben eller att bara arkivera till ett lagrings konto. Du kan också aktivera och publicera data till Azure Sentinel eller en säkerhets informations-och händelse hanterings lösning från tredje part. 
 

- [Virtuella WAN-loggar och-mått](logs-metrics.md)

- [Azure Firewall-loggar och mått](../firewall/logs-and-metrics.md)

Azure Virtual WAN-säkerhet tillhandahålls via Azure-brandväggen. 

- [Dokumentation om Azure Firewall](../firewall/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av loggar

**Vägledning**: Konfigurera logg kvarhållning enligt dina krav på efterlevnad, reglering och affärs verksamhet. I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center aviseringar och rekommendationer exportera](../security-center/continuous-export.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

**Vägledning**: Se till att organisationen har processer för svar på säkerhetsincidenter, att processerna är uppdaterade för Azure och att de tränas regelbundet för att säkerställa beredskapen.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

**Vägledning**: Konfigurera kontaktinformation vid säkerhetsincidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Riktlinjer**: Se till att du har en process för att skapa aviseringar med hög kvalitet och att mäta kvaliteten på aviseringar. På så sätt kan du dra slutsatser från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar genom att slå samman och korrelera olika signalkällor. 

Azure Security Center tillhandahåller aviseringar med hög kvalitet för flera olika typer av Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Riktlinjer**: Se till att analytiker kan fråga och använda olika datakällor när de undersöker potentiella incidenter, så att de kan skapa en fullständig överblick över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighetsgraden baseras på hur tillförlitligt Security Center är i sökandet eller på den analys som användes för att utfärda aviseringen, samt konfidensnivån att det fanns en skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

**Vägledning**: Automatisera manuella återkommande uppgifter för att korta ned svarstiderna och underlätta för analytikerna. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="posture-and-vulnerability-management"></a>Hantering av säkerhetsposition och säkerhetsrisker

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: Utför intrångstester och ”red team”-aktiviteter för dina Azure-resurser och åtgärda alla kritiska säkerhetsbrister som upptäcks.
Se till att följa reglerna för intrångstester i Microsoft Cloud så att dina tester inte strider mot Microsofts policyer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Intrångstester i Azure](../security/fundamentals/pen-testing.md)

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="endpoint-security"></a>Slut punkts säkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: slut punkts säkerhet](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Använd slut punkts identifiering och-svar (EDR)

**Vägledning**: kunder har inte uttryckligen tillåtelse att konfigurera inställningar för slut punkts identifiering och-svar. Virtual Machines som används i Azure Virtual WAN-erbjudandet använder dock dessa funktioner. Läs mer om de här allmänna funktionerna på de länkar som refereras till. 

- [Översikt över Microsoft Defender Avancerat skydd](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-tjänsten för Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP-tjänsten för icke-Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security Benchmark: Styrning och strategi](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera en strategi för tillgångshantering och dataskydd 

**Vägledning**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   En standard för dataklassificering i enlighet med affärsrisker

-   Säkerhetsorganisationens insyn i risker och tillgångsinventering 

-   Säkerhetsorganisationens godkännande av de Azure-tjänster som används 

-   Tillgångars säkerhet genom hela livscykeln

-   Strategi för nödvändig åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av inbyggda säkerhetsfunktioner för Azure- och tredje partsdata

-   Krypteringskrav för användningsfall med data under transport och i vila

-   Lämpliga kryptografiska standarder

Läs mer i följande referenser:
- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – regelverk kring datasäkerhet och kryptering i Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – hantering av tillgångar](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – dataskydd](../security/benchmarks/security-controls-v2-data-protection.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera företagets segmenteringsstrategi 

**Vägledning**: Upprätta en strategi för hela företaget gällande att segmentera åtkomsten till tillgångar utifrån en kombination av identiteter, nätverk, program, prenumerationer, hanteringsgrupper och andra kontroller.

Du måste noga avväga behovet av separationsskyddet med behovet att underlätta den dagliga driften av de system som måste kommunicera med varandra och komma åt data.

Se till att segmenteringsstrategin implementeras konsekvent för olika kontrolltyper som nätverkssäkerhet, modeller för identiteter och åtkomst, modeller för appbehörighet och appåtkomst samt kontroller för mänskliga processer.

- [Vägledning om segmenteringsstrategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segmenteringsstrategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverkssegmenteringen efter företagets segmenteringsstrategi](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiera en strategi för hantering av säkerhetspositionen

**Vägledning**: Mät och minimera risker gällande enskilda tillgångar och miljöerna där de finns regelbundet. Prioritera värdefulla tillgångar och attackytor med stor exponering, som publicerade appar, in- och utgångar i nätverket och slutpunkter för användare och administratörer.

- [Azure Security Benchmark – hantering av säkerhetspositionen och säkerhetsrisker](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Justera organisationens roller och ansvarsområden

**Vägledning**: Dokumentera och förmedla en tydlig strategi gällande säkerhetsorganisationens roller och ansvarsområden. Prioritera att delegera ett tydligt ansvar för olika säkerhetsbeslut och utbilda alla kring modellen med gemensamt ansvar, och ge de tekniska teamen den utbildning som behövs kring tekniken för att skydda molnet.

- [Regelverk för Azure-säkerhet 1 – personal: utbilda teamen om molnsäkerhetsresan](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Regelverk för Azure-säkerhet 2 – personal: utbilda teamen om molnsäkerhetstekniken](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Regelverk för Azure-säkerhet 3 – personal: tilldela ansvar för molnsäkerhetsbeslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiera en strategi för nätverkssäkerhet

**Vägledning**: Upprätta en strategi för nätverkssäkerhet i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Centraliserade ansvarsområden kring nätverkshantering och säkerhet

-   Modell för segmentering av virtuella nätverk anpassad efter företagets segmenteringsstrategi

-   Åtgärdsstrategi för olika hot- och angreppsscenarier

-   Strategi för kantenheter på internet samt in- och utgångar

-   Strategi för hybridmoln och lokala anslutningar

-   Aktuella nätverkssäkerhetsartefakter (som nätverksdiagram och referensnätverksarkitekturer)

Läs mer i följande referenser:
- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – nätverkssäkerhet](../security/benchmarks/security-controls-v2-network-security.md)

- [Översikt över nätverkssäkerhet i Azure](../security/fundamentals/network-overview.md)

- [En strategi för företagets nätverksarkitektur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiera en strategi för identiteter och privilegierad åtkomst

**Vägledning**: Upprätta en strategi för identiteter och privilegierad åtkomst i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Ett centraliserat system för identiteter och autentisering och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användningsfall och scenarier

-   Skydda och användare med hög behörighet

-   Övervaka och hantera avvikande användaraktiviteter  

-   Process för att granska och stämma av identiteter och åtkomstbehörighet

Läs mer i följande referenser:

- [Azure Security Benchmark – hantering av identiteter](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – privilegierad åtkomst](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över säker identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera en strategi för loggning och hotåtgärder

**Riktlinjer**: Upprätta en strategi för loggning och hothantering för att snabbt upptäcka och åtgärda hot och samtidigt uppfylla kraven för efterlevnad. Prioritera att ge analytiker högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för på integrering och manuella åtgärder. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Säkerhetsorganisationens roller och ansvarsområden 

-   En väldefinierad process för incidentsvar anpassad efter NIST eller något annat branschramverk 

-   Insamling och kvarhållning av loggar som stöd till hotidentifiering, incidentsvar och efterlevnadskrav

-   Central insyn i och korrelerande information om olika hot med hjälp av SIEM, interna Azure-funktioner och andra källor 

-   Plan för kommunikation med kunder, leverantörer och offentliga intressenter

-   Användning av plattformar för incidenthantering i Azure och från tredje part, till exempel för loggning och hotidentifiering, datautredning och motverkande åtgärder

-   Processer för hantering av incidenter och efterföljande aktiviteter som hantering av lärdomar och bevis

Läs mer i följande referenser:

- [Azure Security Benchmark – loggning och hotidentifiering](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – svar på incidenter](../security/benchmarks/security-controls-v2-incident-response.md)

- [Regelverk för Azure-säkerhet 4 – process: uppdatera processer kring incidentsvar för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, guide till beslut om loggning och rapporter](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Hantering och övervakning i företagsskala i Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
