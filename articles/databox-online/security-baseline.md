---
title: Azures säkerhets bas linje för Azure Stack Edge
description: Säkerhets bas linjen för Azure Stack Edge ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0de08c166aba7609210892a32836bee9b07d5394
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203056"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Azures säkerhets bas linje för Azure Stack Edge

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Microsoft Azure Stack Edge. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Stack Edge. **Kontroller** som inte är tillämpliga på Azure Stack Edge har uteslutits.

Om du vill se hur Azure Stack Edge är fullständigt mappad till Azures säkerhets mätning, se den [fullständiga Azure Stack gräns för säkerhets bas linje mappnings fil](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: kunderna distribuerar en Microsoft-tillhandahållen, fysisk Azure Stack Edge-enhet till sitt privata nätverk för intern åtkomst och har alternativ för att skydda den ytterligare. Azure Stack Edge-enheten kan till exempel nås via kundens interna nätverk och kräver en kundkonfigurerad IP-adress. Dessutom väljs ett åtkomst lösen ord av kunden för att komma åt enhetens användar gränssnitt. 

Intern trafik skyddas ytterligare av:

- Transport Layer Security (TLS) version 1,2 krävs för Azure Portal-och SDK-hantering av Azure Stack Edge-enheten.

- Alla klient åtkomst till enheten sker via det lokala webb gränssnittet med hjälp av standard TLS 1,2 som standard säkra protokoll.

- Endast en auktoriserad Azure Stack Edge Pro-enhet får ansluta till den Azure Stack Edge-tjänst som kundens skapa i Azure-prenumerationen.

Ytterligare information finns på de länkar som refereras till.
 
- [Konfigurera TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge Pro GPU-enhet](azure-stack-edge-j-series-configure-tls-settings.md)

- [Snabb start – kom igång med Azure Stack Edge Pro med GPU](azure-stack-edge-gpu-quickstart.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

**Vägledning**: kunderna kan välja ett virtuellt privat nätverk (VPN) från punkt till plats för att ansluta en Azure Stack Edge-enhet från det lokala privata nätverket till Azure-nätverket. VPN tillhandahåller ett sekundärt krypterings lager för data i rörelse över Transport Layer Security från kundens enhet till Azure. 

Kunder kan konfigurera ett virtuellt privat nätverk på sin Azure Stack Edge-enhet via Azure Portal eller via Azure PowerShell.

- [Konfigurera Azure VPN via Azure PowerShell skript för Azure Stack Edge Pro R och Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Konfigurera TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge Pro GPU-enhet](azure-stack-edge-j-series-configure-tls-settings.md)

- [Självstudie: Konfigurera certifikat för Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Upprätta privat nätverksåtkomst till Azure-tjänster

**Vägledning**: kunderna kan välja ett virtuellt privat nätverk (VPN) från punkt till plats för att ansluta en Azure Stack Edge-enhet från det lokala privata nätverket till Azure-nätverket. VPN tillhandahåller ett sekundärt krypterings lager för data i rörelse över Transport Layer Security från kundens enhet till Azure. 

- [Konfigurera Azure VPN via Azure PowerShell skript för Azure Stack Edge Pro R och Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Konfigurera TLS 1,2 på Windows-klienter som har åtkomst till Azure Stack Edge Pro GPU-enhet](azure-stack-edge-j-series-configure-tls-settings.md)

- [Självstudie: Konfigurera certifikat för Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: Azure Stack Edge-enheten inkluderar standard funktioner för Windows Server nätverks skydd som inte kan konfigureras av kunder.

Kunderna kan välja att skydda sitt privata nätverk som är anslutet till Azure Stack gräns enheten från externa attacker med hjälp av en virtuell nätverks installation, till exempel en brand vägg med avancerade DDoS-skydd (distributed denial of Service).

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: de slut punkter som används av Azure Stack Edge-enheten hanteras av Microsoft. Kunderna ansvarar för eventuella ytterligare kontroller som de vill distribuera till sina lokala system.

Azure Stack Edge-enheten använder sina egna intrångs identifierings funktioner för att skydda tjänsten. 

- [Förstå Azure Stack Edge Security](azure-stack-edge-security.md)

- [Portinformation för Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md)

- [Hämta loggar för maskin vara och program varu intrångs identifiering](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Hantera appidentiteter säkert och automatiskt

**Vägledning**: alla Azure Stack Edge-enheter har automatiskt en systemtilldelad hanterad identitet i Azure Active Directory (Azure AD). För närvarande används den hanterade identiteten för moln hantering av virtuella datorer som finns på Azure Stack Edge.

Azure Stack Edge-enheter startar i låst tillstånd för lokal åtkomst. För det lokala administratörs kontot för enheten måste du ansluta till din enhet via det lokala gränssnittet eller PowerShell-gränssnittet och ange ett starkt lösen ord. Lagra och hantera autentiseringsuppgifterna för enhets administratören på en säker plats, till exempel en Azure Key Vault och rotera administratörs lösen ordet enligt organisationens standarder.

- [Skydda Azure Stack Edge-enhet via lösen ord](azure-stack-edge-security.md#protect-the-device-via-password)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning i Azure AD (SSO) för åtkomst till appar

**Vägledning**: enkel inloggning stöds inte för Azure Stack Edge-slutpunkts enheter. Du kan dock välja att aktivera standard Azure Active Directory (Azure AD) baserad enkel inloggning för att skydda åtkomsten till dina Azure-moln resurser.

- [Förstå Application SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd kraftfulla verifieringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: multifaktorautentisering är en stark verifierings kontroll, men en valbar funktion för Azure Stack Edge-tjänstens användare. Det kan utnyttjas för scenarier som stöds, till exempel Edge-Management för Azure Stack gräns enheter på Azure Portal. Observera att den lokala åtkomsten till Azure Stack gräns enheterna inte stöder multifaktorautentisering.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och skicka aviseringar vid kontoavvikelser

**Vägledning**: Aktivera Azure Monitor för att samla in enhets-eller behållar loggar för din Azure Stack Edge-tjänst. Övervaka behållare, till exempel de som kör flera beräknings program i Kubernetes-klustret på din enhet.

Dessutom kan ett support paket som innehåller gransknings loggar samlas in i det lokala webb användar gränssnittet i Azure Stack Edge-enheten. Observera att support paketet inte är tillgängligt på Azure Portal.
 
- [Aktivera Azure Monitor på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Samla in ett support paket](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning**: Azure Active Directory (Azure AD) villkorlig åtkomst är en valbar funktion för autentisering till Azure Stack Edge-tjänsten. Azure Stack Edge service är en resurs i Azure Portal som låter dig hantera en Azure Stack Edge Pro-enhet på olika geografiska platser. 

- [Vad är villkorlig åtkomst](../active-directory/conditional-access/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning**: Följ de bästa metoderna för att skydda autentiseringsuppgifter, till exempel:

- Aktiverings nyckel som används för att aktivera enheten med Azure Stack Edge-resursen i Azure.
- Inloggnings uppgifter för att få åtkomst till Azure Stack Edge-enheten.
- Viktiga filer som kan under lätta återställning av Azure Stack Edge-enhet.
- Krypterings nyckel för kanal

Rotera och synkronisera dina lagrings konto nycklar regelbundet för att hjälpa till att skydda ditt lagrings konto från obehöriga användare.

- [Azure Stack Edge Pro Security och Data Protection](azure-stack-edge-security.md)

- [Synkronisera lagringsnycklar](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning**: Azure Stack Edge-lösning har flera komponenter med starka åtkomst kontroller för att begränsa åtkomsten till affärs kritiska enheter. Din organisation behöver en Enterprise-avtal (EA) eller en moln lösnings leverantör (CSP) eller en Microsoft Azure-sponsring-prenumeration för att konfigurera och hantera enheten: 

Azure Stack Edge-tjänsten skyddas av Azures säkerhetsfunktioner som en hanterings tjänst som finns i Azure. Du kan hämta krypterings nyckeln för resursen i enhets egenskaper för alla Software Development Kit hanterings åtgärder, men du kan bara Visa krypterings nyckeln om du har rätt behörigheter för resurs Graph API.

Azure Stack Edge Pro-enheten är en lokal enhet som hjälper till att transformera data genom att bearbeta den lokalt och sedan skicka den till Azure. Din enhet:

- behöver en aktiverings nyckel för att få åtkomst till tjänsten Azure Stack Edge.
- skyddas hela tiden av ett enhets lösen ord.
- har säker start aktiverat.

- är en låst enhet. Enhetens BMC (Baseboard Management Controller) och BIOS är lösenordsskyddad. BIOS skyddas av begränsad användar åtkomst.
- kör Windows Defender Device Guard. Med Device Guard kan du bara köra betrodda program som du definierar i dina kod integritets principer.

Följ de rekommenderade metoderna för att skydda alla autentiseringsuppgifter och hemligheter som används för att komma åt Azure Stack Edge. 

- [Metodtips för lösenord](azure-stack-edge-security.md#protect-the-device-via-password)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användarnas åtkomst regelbundet

**Vägledning**: Azure Stack Edge har en användare med namnet ' EdgeUser ' som kan konfigurera enheten. Den har också Azure Resource Manager användare ' EdgeArmUser ' för de lokala Azure Resource Manager-funktionerna på enheten. 

Bästa praxis bör följas för att skydda:

- Autentiseringsuppgifter som används för att komma åt den lokala enheten

- Autentiseringsuppgifter för SMB-resurs.

- Åtkomst till klient system som har kon figurer ATS för att använda NFS-resurser.

- Lokala lagrings konto nycklar som används för att komma åt de lokala lagrings kontona när du använder BLOB-REST API.

Ytterligare information finns på länken som refereras till.

- [Information om säkerhet för dina Azure Stack Edge-enheter](azure-stack-edge-security.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbetsstationer med privilegierad åtkomst

**Vägledning**: säkra och isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Använd mycket säkra användar arbets stationer med eller utan Azure-skydds för administrativa uppgifter. Använd Azure Active Directory (Azure AD), Microsoft Defender Avancerat skydd (ATP) och Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. 

Skyddade arbetsstationer kan hanteras centralt för att upprätthålla en säker konfiguration med kraftfulla baslinjer för autentisering, programvara och maskinvara samt begränsad logisk och nätverksbaserad åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Distribuera en arbetsstation med privilegierad åtkomst](/azure/active-directory/devices/howto-azure-managed-workstation)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

**Vägledning**: Azure Stack Edge behandlar alla interagerade data som känsliga med endast behöriga användare som har åtkomst till dessa data. Du bör följa bästa praxis för att skydda de autentiseringsuppgifter som används för att komma åt Azure Stack Edge-tjänsten.

- [Azure Stack Edge Pro Security och Data Protection](azure-stack-edge-security.md#protect-the-device-via-password)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Kryptera känslig information under överföring

**Vägledning**: Azure Stack Edge använder säkra kanaler för data i flygningen. Dessa är:

- Standard TLS 1,2 används för data som överförs mellan enheten och Azure-molnet. Det finns ingen återställning till TLS 1,1 och tidigare. Agent kommunikation kommer att blockeras om TLS 1,2 inte stöds. TLS 1,2 krävs också för hantering av Azure Portal och Software Development Kit (SDK).

- När klienter får åtkomst till enheten via det lokala webb användar gränssnittet i en webbläsare, används standard TLS 1,2 som standard säkra protokoll

Det bästa sättet är att konfigurera webbläsaren att använda TLS 1,2. Använd SMB 3,0 med kryptering för att skydda data när du kopierar den från dina data servrar.

- [Metod tips för att skydda data i flygning](azure-stack-edge-security.md#protect-data-in-flight)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security Benchmark: Tillgångshantering](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Observera att ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: endast auktoriserade användare, till exempel, "EdgeArmUser" kan komma åt Azure Stack Edge-API: er via den lokala Azure Resource Manager. Lösen ord för användar konton kan bara hanteras på Azure Portal. 

- [Konfigurera Azure Resource Manager-lösenord](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Använd endast godkända program i beräknings resurser

**Vägledning**: du kan ta med dina egna program att köra på alla lokalt skapade virtuella datorer. Använd PowerShell-skript för att skapa lokala virtuella Compute-datorer på din stack Edge-enhet. Vi rekommenderar starkt att du endast använder betrodda program för att köra på de lokala virtuella datorerna. 

- [Så här styr du körning av PowerShell-skript i Windows-miljön](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.1&amp;viewFallbackFrom=powershell-6&amp;preserve-view=true)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: Azure Stack Edge erbjuder inte funktioner för hot identifiering. Kunder kan dock samla in gransknings loggar i ett support paket för identifiering och analys av offline-hot. 

- [Samla in support paket för Azure Stack Edge-enhet](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning av nätverksaktiviteter i Azure

**Vägledning**: Azure Stack Edge har nätverks gransknings loggar aktiverade som en del av nedladdnings bara support paketet. Dessa loggar kan tolkas för att implementera en tids övervakning i en halv real tid för dina Azure Stack Edge-enheter.

- [Azure Stack Edge samla ett support paket](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: real tids övervakning med loggar stöds för närvarande inte för Azure Stack Edge. Det finns en funktion för att samla in ett support paket som gör att du kan analysera de olika loggar som ingår i den, till exempel brand vägg, program vara, maskin varu intrång och system händelse loggar för din Azure Stack Edge Pro-enhet. Observera att program varu intrång eller standard brand Väggs loggar samlas in för inkommande och utgående trafik.

- [Samla in ett support paket för Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

**Vägledning**: real tids övervakning med loggar stöds för närvarande inte för Azure Stack Edge. Du kan dock samla in ett support paket som gör att du kan analysera de olika loggar som ingår i den.  Support paketet är komprimerat och laddas ned till valfri sökväg. Packa upp paketet och Visa systemloggfilerna som finns i den. Du kan också skicka loggarna till ett hanterings verktyg för säkerhets information eller en annan central lagrings plats för analys.

- [Samla in ett support paket för Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av loggar

**Vägledning**: kvarhållningsperioden för logg lagring kan inte ändras i Azure Stack Edge-enheten. Äldre loggar rensas efter behov. Du kan samla in support paket från enheten med regelbundna intervall för eventuella krav för att spara loggarna under en längre tids period. 

- [Samla in ett support paket för Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: Använd godkända tids källor för synkronisering

**Vägledning**: Azure Stack Edge använder Time.Windows.com, en nätverks tids leverantörs Server av Microsoft.  Azure Stack Edge gör det också möjligt för kunden att konfigurera nätverks tids protokoll servern för att välja.

- [Konfigurera inställningar för Azure Stack gräns för enhets tid](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

**Vägledning**: kontrol lera att din organisatoriska incident svars plan innehåller processer: 

- svara på säkerhets incidenter

- som har uppdaterats för Azure-molnet
 
- utövas regelbundet för att säkerställa beredskap

Vi rekommenderar att du implementerar säkerhet med standardiserade incident svars processer i företags miljön.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

**Vägledning**: Konfigurera kontaktinformation vid säkerhetsincidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet 

**Vägledning**: se till att du har en process för att skapa aviseringar med hög kvalitet och att mäta deras kvalitet. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker för att förhindra slösad tid på att bearbeta falska positiva varningar. Skapa aviseringar med hög kvalitet baserat på dina erfarenheter från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar med loggning och korrelation för olika aviserings källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda Security Center Data Connector för att strömma aviseringarna till Azure Sentinel. Skapa avancerade aviserings regler med Azure Sentinel för att generera automatiska incidenter för en undersökning. 

Exportera dina Security Center aviseringar och rekommendationer till Azure Sentinel med export funktionen för att identifiera risker för Azure-resurser. Vi rekommenderar att du skapar en process för att exportera aviseringar och rekommendationer på ett automatiserat sätt för kontinuerlig säkerhet.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Vägledning**: se till att analytiker kan fråga och använda olika data källor för att bygga en fullständig vy över den aktivitet som inträffade vid undersökning av potentiella incidenter. Olika logg typer bör samlas in för att spåra aktiviteter för en potentiell angripare över Kill-kedjan för att undvika rullgardiner.  Se också till att insikter och kunskaper samlas in för historisk referens.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Välj operativ systemets ursprungliga minnes dumpnings funktion för att skapa en ögonblicks bild av det operativ systemets minne.

    - Använd ögonblicks bild funktionen i Azure-tjänsterna eller en program vara från tredje part för att skapa ögonblicks bilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Vägledning**: Ge analytikerna kontext kring vilka incidenter som ska prioriteras utifrån allvarlighetsgrader och hur känsliga tillgångarna är. Använd den tilldelade allvarlighets graden för varje avisering från Azure Security Center för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighetsgraden baseras på hur tillförlitligt Security Center är i sökandet eller på den analys som användes för att utfärda aviseringen, samt konfidensnivån att det fanns en skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

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

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning**: Azure Stack Edge erbjuder stöd för att skapa säkra konfigurationer för de lokala virtuella datorerna som skapas av kunderna. Vi rekommenderar starkt att du använder Microsofts rikt linjer för att upprätta säkerhets bas linjer när du skapar lokala virtuella datorer.

- [Hämta de säkerhets bas linjer som ingår i säkerhetskompatibilitets-Toolkit](/windows/security/threat-protection/windows-security-baselines)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: upprätthålla säkra konfigurationer för beräknings resurser

**Vägledning**: Azure Stack Edge erbjuder inget stöd för att upprätthålla säkra konfigurationer för de lokala virtuella datorerna som har skapats av kunderna. Vi rekommenderar starkt att kunderna använder SCT (Security Compliance Toolkit) för att upprätthålla säkra konfigurationer för sina beräknings resurser.

Värd operativ system och virtuella datorer som hanteras av Azure Stack Edge upprätthåller sina säkerhetskonfigurationer. 

- [Windows säkerhets bas linjer](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: lagra anpassade operativ system och behållar avbildningar på ett säkert sätt

**Vägledning**: värd operativ system och virtuella datorer som hanteras av Azure Stack Edge lagras på ett säkert sätt. 

Kunder kan ta med sina egna avbildningar av virtuella datorer och behållare och ansvarar för sin säkra hantering.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Åtgärda sårbarheter för programvara snabbt och automatiskt

**Vägledning**: Azure Stack Edge tillhandahåller vanliga uppdateringar och varningar om uppdateringar när sådana uppdateringar är tillgängliga för att åtgärda sårbarheter. Det är kundens ansvar att hålla sina enheter och virtuella datorer (som skapats av dem) uppdaterade med de senaste korrigeringarna.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: Utför intrångstester och ”red team”-aktiviteter för dina Azure-resurser och åtgärda alla kritiska säkerhetsbrister som upptäcks.
Se till att följa reglerna för intrångstester i Microsoft Cloud så att dina tester inte strider mot Microsofts policyer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Intrångstester i Azure](../security/fundamentals/pen-testing.md)

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="endpoint-security"></a>Slut punkts säkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: slut punkts säkerhet](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Använd slut punkts identifiering och-svar (EDR)

**Vägledning**: Azure Stack Edge stöder inte slut punkts identifiering och-svar (EDR) direkt. Du kan dock samla in ett support paket och hämta gransknings loggar. Loggarna kan sedan analyseras för att kontrol lera avvikande aktiviteter. 

- [Samla in support paket för Azure Stack Edge-enhet](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Använd centralt hanterade moderna program mot skadlig kod

**Vägledning**: Azure Stack Edge använder Windows Defender Application Control (WDAC) med en princip för strikt kod integritet som bara tillåter att fördefinierade program och skript körs. Windows Defender real tids skydd (RTP) program mot skadlig kod är också aktiverat. Kunden kan välja att köra program mot skadlig kod i de Compute-virtuella datorer som de skapar för att köras lokalt på Azure Stack Edge-enhet.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [Azure Security Benchmark: Säkerhetskopiering och återställning](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ vanliga automatiserade säkerhets kopieringar

**Vägledning**: regelbundna säkerhets kopieringar av din Azure Stack Edge-enhet rekommenderas och kan utföras med Azure Backup och andra data skydds lösningar från tredje part för att skydda de data som finns i de virtuella datorer som distribueras på enheten. 

Data skydds lösningar från tredje part, till exempel Cohesity, CommVault och Veritas, kan också tillhandahålla en säkerhets kopierings lösning för data i de lokala SMB-eller NFS-resurserna. 

Ytterligare information finns på de länkar som refereras till.

- [Förbereda för ett enhets haveri](azure-stack-edge-gpu-prepare-device-failure.md)

- [Skydda filer och mappar på virtuella datorer](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="br-2-encrypt-backup-data"></a>BR-2: kryptera säkerhets kopierings data

**Vägledning**: se till att dina säkerhets kopior skyddas mot attacker. Detta bör innefatta kryptering av säkerhets kopiorna för att skydda mot förlust av konfidentialitet.  Mer information finns i din säkerhets kopierings lösning som du väljer för mer information.

- [Skydda data i lokala Edge-resurser](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Skydda filer och mappar på virtuella datorer](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validera all säkerhetskopiering med kundhanterade nycklar

**Vägledning**: regelbundet utföra Data återställning av dina säkerhets kopior. 

- [Återställnings procedurer för Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Minska risken för förlorade nycklar

**Vägledning**: se till att alla Azure Stack Edge-säkerhetskopieringar inklusive Kundhanterade nycklar skyddas i enlighet med organisationens bästa praxis. Din Azure Stack gräns enhet är kopplad till ett Azure Storage konto som används som mål databas för dina data i Azure. 

Åtkomst till Azure Storage-kontot styrs av Azure-prenumerationer och de 2 512-bitars lagrings åtkomst nycklar som är associerade med lagrings kontot. En av åtkomst nycklarna används för autentisering när Azure Stack Edge-enheten har åtkomst till lagrings kontot. Den andra nyckeln lagras i reserverad för regelbunden nyckel rotation. Se till att rekommenderade säkerhets metoder används för nyckel rotation.

- [Skydda Azure Stack gräns enhets data i Azure Storage-konton](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security Benchmark: Styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera en strategi för tillgångshantering och dataskydd 

**Vägledning**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   En standard för dataklassificering i enlighet med affärsrisker

-   Säkerhetsorganisationens insyn i risker och tillgångsinventering 

-   Säkerhetsorganisationens godkännande av de Azure-tjänster som används 

-   Tillgångars säkerhet genom hela livscykeln

-   Nödvändig strategi för åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av dataskyddsfunktioner i Azure och från tredje part

-   Krav på datakryptering både under överföring och i vila

-   Lämpliga kryptografiska standarder

Läs mer i följande referenser:
- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – regelverk kring datasäkerhet och kryptering i Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – hantering av tillgångar](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark – dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Azure Security Benchmark – hantering av säkerhetspositionen och säkerhetsrisker](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Azure Security Benchmark – nätverkssäkerhet](/azure/security/benchmarks/)

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

- [Azure Security Benchmark – hantering av identiteter](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark – privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över säker identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera en strategi för loggning och hotåtgärder

**Vägledning**: Upprätta en strategi för loggning och hotåtgärder så att du snabbt kan identifiera och åtgärda hot och samtidigt uppfylla efterlevnadskraven. Prioritera att ge analytiker tillgång till aviseringar av hög kvalitet och en användarvänlig miljö så att de kan fokusera på hoten snarare än integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Säkerhetsorganisationens roller och ansvarsområden 

-   En väldefinierad process för incidentsvar anpassad efter NIST eller något annat branschramverk 

-   Insamling och kvarhållning av loggar som stöd till hotidentifiering, incidentsvar och efterlevnadskrav

-   Central insyn i och korrelerande information om olika hot med hjälp av SIEM, interna Azure-funktioner och andra källor 

-   Plan för kommunikation med kunder, leverantörer och offentliga intressenter

-   Användning av plattformar för incidenthantering i Azure och från tredje part, till exempel för loggning och hotidentifiering, datautredning och motverkande åtgärder

-   Processer för hantering av incidenter och efterföljande aktiviteter som hantering av lärdomar och bevis

Läs mer i följande referenser:

- [Azure Security Benchmark – loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark – svar på incidenter](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Regelverk för Azure-säkerhet 4 – process: uppdatera processer kring incidentsvar för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, guide till beslut om loggning och rapporter](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
