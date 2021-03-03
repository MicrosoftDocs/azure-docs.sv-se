---
title: Migrera programautentisering till Azure Active Directory
description: Det här dokumentet innehåller information om planering och förmåner vid migrering av programautentisering till Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c1a0be0b7834632ba79af7dfe6c3a4fa25c0316
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645519"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrera programautentisering till Azure Active Directory

## <a name="about-this-paper"></a>Om det här dokumentet

Det här dokumentet innehåller information om planering och förmåner vid migrering av programautentisering till Azure AD. Den är utformad för Azure-administratörer och identitets personal.

Genom att dela upp processen i fyra faser, var och en med detaljerade planerings-och avslutnings kriterier, är det utformat för att hjälpa dig att planera din strategi för migrering och förstå hur Azure AD-autentisering stöder organisationens mål.

## <a name="introduction"></a>Introduktion

I dag kräver din organisation en sväng av appar (appar) för att användarna ska få jobbet gjort. Du kommer troligen att fortsätta att lägga till, utveckla eller ta ur bruk varje dag. Användare kommer åt dessa program från en mängd olika företags-och personliga enheter och platser. De öppnar appar på många sätt, inklusive:

- via företagets start sida eller Portal

- genom att förse sina webbläsare med bok märken

- via en leverantörs URL för SaaS-appar (program vara som en tjänst)

- Länkar skickas direkt till användarens skriv bord eller mobila enheter via en mobil enhet/program hanterings lösning (MDM/MAM)

Dina program använder förmodligen följande typer av autentisering:

- Lokala Federations lösningar (till exempel Active Directory Federation Services (AD FS) (ADFS) och ping)

- Active Directory (till exempel Kerberos-autentisering och Windows-integrerad autentisering)

- Andra molnbaserad lösningar för identitets-och åtkomst hantering (IAM) (till exempel okta eller Oracle)

- Lokal webb infrastruktur (till exempel IIS och Apache)

- Moln värd infrastruktur (till exempel Azure och AWS)

**För att säkerställa att användarna enkelt och säkert kan komma åt program, är målet att ha en enda uppsättning åtkomst kontroller och principer i dina lokala miljöer och moln miljöer.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) erbjuder en Universal Identity-plattform som ger dina personer, partners och kunder en enda identitet för att komma åt de program som de vill ha och samar beta från vilken plattform och enhet som helst.

![Ett diagram över Azure Active Directory anslutning](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD har en [fullständig uppsättning funktioner för identitets hantering](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad). Genom att standardisera din app-autentisering och-auktorisering till Azure AD kan du få de fördelar som dessa funktioner ger.

Se ytterligare migrerings resurser på [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Fördelar med migrering av app-autentisering till Azure AD

Genom att flytta app-autentisering till Azure AD kan du hantera risker och kostnader, öka produktiviteten och uppfylla krav på efterlevnad och styrning.

### <a name="manage-risk"></a>Hantera risk

Att skydda dina appar kräver att du har en fullständig översikt över alla riskfaktorer. Att migrera dina appar till Azure AD konsoliderar dina säkerhetslösningar. Med den kan du:

- Förbättra säker användar åtkomst till program och associerade företags data med hjälp av [principer för villkorlig åtkomst](../conditional-access/overview.md), [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)och skydds tekniker för Real tidsbaserade [identiteter](../identity-protection/overview-identity-protection.md) .

- Skydda privilegie rad användares åtkomst till din miljö med [just-in-Time](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) admin-åtkomst.

- Använd den [geo-distribuerade och hög tillgänglighets designen för flera innehavare för Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)för dina mest kritiska affärs behov.

- Skydda dina äldre program med en av våra [partners integreringar för säker hybrid åtkomst](https://aka.ms/secure-hybrid-access) som du kanske redan har distribuerat.

### <a name="manage-cost"></a>Hantera kostnad

Din organisation kan ha flera lösningar för identitets åtkomst hantering (IAM) på plats. Att migrera till en Azure AD-infrastruktur är en möjlighet att minska beroenden för IAM-licenser (lokalt eller i molnet) och infrastruktur kostnader. I de fall där du redan har betalat för Azure AD via M365-licenser, finns det ingen anledning att betala den extra kostnaden för en annan IAM-lösning.

**Med Azure AD kan du minska infrastruktur kostnaderna genom att:**

- Tillhandahålla säker fjärråtkomst till lokala appar med hjälp av [Azure AD-programproxy](./application-proxy.md).

- Att koppla ifrån appar från den lokal Credential-metoden i din klient organisation genom att [Konfigurera Azure AD som den betrodda Universal Identity-providern](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Öka produktiviteten

Ekonomi och säkerhets förmåner bedriver organisationer för att kunna använda Azure AD, men det är mer sannolikt att alla användare får till gång till fullständigt införande och efterlevnad. Med Azure AD kan du:

- Förbättra upplevelsen av [enkel Sign-On (SSO)](./what-is-single-sign-on.md) genom sömlös och säker åtkomst till alla program, från valfri enhet och valfri plats.

- Använd självbetjänings-IAM-funktioner, till exempel självbetjäning för [återställning av lösen ord](../authentication/concept-sspr-howitworks.md) och [självbetjänings grupp hantering](../enterprise-users/groups-self-service-management.md).

- Minska administrationen genom att bara hantera en enda identitet för varje användare i molnet och i lokala miljöer:

  - [Automatisera etablering](../app-provisioning/user-provisioning.md) av användar konton (i [Azure AD-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) baserat på Azure AD-identiteter
  - Gå till alla appar från panelen Mina appar i [Azure Portal ](https://portal.azure.com/)

- Gör det möjligt för utvecklare att skydda åtkomsten till sina appar och förbättra slutanvändarens upplevelse genom att använda [Microsoft Identity Platform](../develop/v2-overview.md) med Microsoft Authentication Library (MSAL).

- Ge dina partner åtkomst till moln resurser med [Azure AD B2B-samarbete](../external-identities/what-is-b2b.md). Detta tar bort behovet av att konfigurera punkt-till-punkt-Federation med dina partner.

### <a name="address-compliance-and-governance"></a>Hantera efterlevnad och styrning

Se till att reglerna efterlevs genom att tvinga företags åtkomst principer och övervaka användar åtkomst till program och associerade data med hjälp av integrerade gransknings verktyg och API: er. Med Azure AD kan du övervaka program inloggningar via rapporter som utnyttjar [SIEM-verktyg (säkerhets incidenter och händelse övervakning)](../reports-monitoring/plan-monitoring-and-reporting.md). Du kan komma åt rapporterna från portalen eller API: er och granska program mässigt som har åtkomst till dina program och ta bort åtkomsten till inaktiva användare via åtkomst granskningar.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planera dina migrerings faser och projekt strategi

När teknik projekt inte fungerar beror det ofta på att förväntningar, rätt intressenter inte berörs eller bristande kommunikation. Se till att du har lyckats genom att planera själva projektet.

### <a name="the-phases-of-migration"></a>Fasen av migreringen

Innan vi får till gång till verktygen bör du förstå hur du kan tänka igenom migreringsprocessen. Vi rekommenderar följande fyra faser i flera workshops för direkt till kunder:

![Ett diagram över migreringens faser](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Sätt samman projekt gruppen

Programmigreringen är en grupp ansträngning och du måste se till att alla viktiga platser är fyllda. Support från erfarna affärs ledare är viktigt. Se till att du har rätt uppsättning av exekutiva sponsorer, affärs besluts fattare och ämnes experter (små och andra)

Under migreringen kan en person uppfylla flera roller, eller flera personer som uppfyller varje roll, beroende på organisationens storlek och struktur. Du kan också ha ett beroende på andra team som spelar en viktig roll i ditt säkerhets landskap.

Följande tabell innehåller viktiga roller och deras bidrag:

| Roll          | Bidrag                                              |
| ------------- | ---------------------------------------------------------- |
| **Projektledare** | Projektets turist klass för att kunna identifiera projektet, inklusive:<br /> – få professionell support<br /> – ta med intressenter<br /> – hantera scheman, dokumentation och kommunikation |
| **Identitets arkitekt/Azure AD App administratör** | De är ansvariga för följande:<br /> – utforma lösningen i samarbete med intressenter<br /> – dokumentera lösningens utformning och operativa procedurer för leverans till drift teamet<br /> – hantera för produktions-och produktions miljöer |
| **Lokala AD Operations-team** | Organisationen som hanterar de olika lokala identitets källorna, till exempel AD-skogar, LDAP-kataloger, HR-system osv.<br /> – utföra alla åtgärder som krävs innan du synkroniserar<br /> -Ange de tjänst konton som krävs för synkronisering<br /> -ge åtkomst till att konfigurera Federation till Azure AD |
| **IT-support ansvarig** | En representant från IT-supporten som kan tillhandahålla insikter om den här förändringen från ett support perspektiv. |
| **Säkerhets ägare**  | En representant från säkerhets teamet som kan se till att planen uppfyller organisationens säkerhets krav. |
| **Program tekniska ägare** | Innehåller tekniska ägare till de appar och tjänster som ska integreras med Azure AD. De tillhandahåller de program identitets attribut som ska ingå i synkroniseringsprocessen. De har vanligt vis en relation med CSV-representanter. |
| **Program företags ägare** | Representativa kollegor som kan ge information om användar upplevelsen och användbarhet på den här ändringen från en användares perspektiv och äger den övergripande affärs aspekten av programmet, som kan innehålla hanterings åtkomst. |
| **Pilot grupp för användare** | Användare som kommer att testa som en del av sitt dagliga arbete, pilot erfarenheten och ge feedback för att vägleda resten av distributionerna. |

### <a name="plan-communications"></a>Planera kommunikation

Effektiv affärs engagemang och kommunikation är nyckeln till framgång. Det är viktigt att ge intressenter och slutanvändare en minimering för att få information och hålla dig informerad om schemalagda uppdateringar. Utbilda alla om migreringens värde, vad de förväntade tids linjerna är och hur du planerar för eventuella tillfälliga affärs störningar. Använd flera vägar, till exempel korta sessioner, e-postmeddelanden, ett-till-ett-möte, banderoller och townhalls.

Baserat på den kommunikations strategi som du har valt för appen kanske du vill påminna användare om väntande stillestånd. Du bör också kontrol lera att det inte finns några nya ändringar eller verksamhets effekter som skulle kräva att du skjuter upp distributionen.

I följande tabell hittar du den minsta föreslagna kommunikation som hjälper dig att hålla dina intressenter informerade:

**Planera faser och projekt strategi**:

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| Medvetenhet och affärs-/tekniskt värde för projekt | Alla utom slutanvändare |
| Inbjudning för pilotbaserade appar | – Appens affärs ägare<br />– Teknisk ägare till appen<br />– Arkitekter och identitets team |

**Fas 1 – identifiering och omfattning**:

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| -Inbjudnings program information<br />– Resultat av omfångs övning | – Teknisk ägare till appen<br />– Appens affärs ägare |

**Fas 2 – klassificera appar och planera pilot**:

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| – Resultat av klassificeringar och vad det innebär för schema för migrering<br />-Preliminärt schema för migrering | – Teknisk ägare till appen<br /> – Appens affärs ägare |

**Fas 3 – Planera migrering och testning**:

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| – Resultat av program migrations testning | – Teknisk ägare till appen<br />– Appens affärs ägare |
| – Meddelande om migreringen kommer och beskriver de resulterande slut användar upplevelsen.<br />– Drift stopp och fullständig kommunikation, inklusive vad de bör göra, feedback och hur du får hjälp | -Slutanvändare (och alla andra) |

**Fas 4 – hantera och få insikter**:

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| Tillgänglig analys och åtkomst | – Teknisk ägare till appen<br />– Appens affärs ägare |

### <a name="migration-states-communication-dashboard"></a>Överförings tillstånd kommunikations instrument panel

Att kommunicera det övergripande läget för migreringsjobbet är viktigt, eftersom det visar förloppet och hjälper app-ägare vars appar kommer att migreras för att förbereda för flytten. Du kan samla in en enkel instrument panel med hjälp av Power BI eller andra rapporterings verktyg för att ge insyn i status för program under migreringen.

De migrations tillstånd du kan använda är följande:

| Migrations tillstånd       | Åtgärdsplan                                   |
| ---------------------- | --------------------------------------------- |
| **Första begäran** | Hitta appen och kontakta ägaren för mer information |
| **Utvärderingen har slutförts** | Appens ägare utvärderar appens krav och returnerar appens enkät</td>
| **Konfiguration pågår** | Utveckla de ändringar som krävs för att hantera autentisering mot Azure AD |
| **Test konfigurationen lyckades** | Utvärdera ändringarna och autentisera appen mot Azure AD-klienten i test miljön |
| **Produktions konfigurationen lyckades** | Ändra konfigurationen så att den fungerar mot produktions AD-klienten och utvärdera appens autentisering i test miljön |
| **Slutför/logga ut** | Distribuera ändringarna för appen till produktions miljön och kör mot produktions-Azure AD-klienten |

Detta säkerställer att appens ägare vet vad appens migrering och testning av schemat är när deras appar är upp för migrering och vilka resultat som kommer från andra appar som redan har migrerats. Du kan också överväga att tillhandahålla länkar till din databas för fel Spårare för att ägarna ska kunna komma åt och Visa problem för appar som migreras.

### <a name="best-practices"></a>Bästa praxis

Följande är våra kunders och partners framgångs berättelser och rekommenderade metoder:

- [Fem tips för att förbättra migreringsprocessen till Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) av Patriot Consulting, en medlem i vårt partner nätverk som fokuserar på att hjälpa kunder att distribuera Microsofts moln lösningar på ett säkert sätt.

- [Utveckla en riskhanterings strategi för din Azure AD-programmigrering](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) av Edgile, en partner som fokuserar på IAM-och riskhanterings lösningar.

## <a name="phase-1-discover-and-scope-apps"></a>Fas 1: identifiera och omfångs appar

**Program identifiering och-analys är en grundläggande övning för att ge dig en bättre start.** Du kanske inte vet allt så var för beredd för att hantera de okända apparna.

### <a name="find-your-apps"></a>Hitta dina appar

Det första besluts punkten i en programmigrering är vilka appar som ska migreras, vilket om de ska vara kvar och vilka appar som ska föråldras. Det finns alltid en möjlighet att föråldra appar som du inte kommer att använda i din organisation. Det finns flera sätt att hitta appar i din organisation. **När du identifierar appar bör du se till att du inkluderar utveckling och planerade appar. Använd Azure AD för autentisering i alla kommande appar.**

**Använda Active Directory Federation Services (AD FS) (AD FS) för att samla in rätt app-inventering:**

- **Använd Azure AD Connect Health.** Om du har en Azure AD Premium-licens rekommenderar vi att du distribuerar [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) för att analysera app-användningen i din lokala miljö. Du kan använda [ADFS-programrapporten](./migrate-adfs-application-activity.md) (för hands version) för att identifiera ADFS-program som kan migreras och utvärdera beredskap för det program som ska migreras. När du har slutfört migreringen distribuerar du [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) som gör att du kontinuerligt kan övervaka skugg it i din organisation när du befinner dig i molnet.

- **AD FS logg parsning**. Om du inte har Azure AD Premium licenser rekommenderar vi att du använder ADFS till Azure AD-Migreringsverktyg för app som baseras på [PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration). Se [lösnings guide](./migrate-adfs-apps-to-azure.md):

[Migrera appar från Active Directory Federation Services (AD FS) (AD FS) till Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Använda andra identitets leverantörer (IDP: er)

För andra identitets leverantörer (till exempel okta eller ping) kan du använda deras verktyg för att exportera program lagret. Du kan överväga att titta på tjänst principer som är registrerade på Active Directory som motsvarar webbapparna i din organisation.

### <a name="using-cloud-discovery-tools"></a>Använda Cloud Discovery-verktyg

I moln miljön behöver du omfattande synlighet, kontroll över data resor och avancerad analys för att hitta och bekämpa cyberhot-hot i alla dina moln tjänster. Du kan samla in din Cloud App Inventory med följande verktyg:

- **Cloud Access Security Broker (CASB**) – en [CASB](/cloud-app-security/) fungerar vanligt vis tillsammans med brand väggen för att ge insyn i dina anställdas moln program användning och hjälper dig att skydda dina företags data från cybersäkerhet hot. CASB-rapporten kan hjälpa dig att fastställa de mest använda apparna i din organisation och de tidiga målen för att migrera till Azure AD.

- **Cloud Discovery** – genom att konfigurera [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)får du insyn i användningen av molnappar och kan identifiera ej sanktionerade eller skuggade IT-appar.

- **API: er** – för appar som är anslutna till moln infrastrukturen kan du använda API: er och verktyg på dessa system för att börja ta en inventering av värdbaserade appar. I Azure-miljön:

  - Använd cmdleten [Get-AzureWebsite](/powershell/module/servicemanagement/azure/get-azurewebsite?view=azuresmps-4.0.0&redirectedfrom=MSDN&preserve-view=true)för att hämta information om Azure Websites.

  - Använd cmdleten [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.2.0&preserve-view=true)för att få information om din Azure-Web Apps.

  - Du kan hitta alla appar som körs på Microsoft IIS från kommando raden i Windows med hjälp av [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Använd [program](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) och [tjänstens huvud namn](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) för att få information om en app-och App-instans i en katalog i Azure AD.

### <a name="using-manual-processes"></a>Använda manuella processer

När du har vidtagit de automatiserade metoder som beskrivs ovan får du ett utmärkt handtag på dina program. Du kan dock överväga att göra följande för att se till att du har en bra täckning för alla användar åtkomst områden:

- Kontakta de olika företags ägarna i din organisation för att hitta de program som används i din organisation.

- Kör ett verktyg för HTTP-kontroll på proxyservern eller analysera proxy-loggar för att se var trafiken ofta dirigeras.

- Granska blogg TVF från populära företags Portal webbplatser för att se vilka länkar användarna har åtkomst till mest.

- Kontakta chefer eller andra viktiga företags medlemmar för att säkerställa att du har täckt affärs kritiska appar.

### <a name="type-of-apps-to-migrate"></a>Typ av appar som ska migreras

När du har hittat dina appar kommer du att identifiera dessa typer av appar i din organisation:

- Appar som använder moderna autentiseringsprotokoll redan

- Appar som använder bakåtkompatibla autentiseringsprotokoll som du väljer att modernisera

- Appar som använder bakåtkompatibla autentiseringsprotokoll som du väljer att inte modernisera

- Nya LoB-appar (Line of Business)

### <a name="apps-that-use-modern-authentication-already"></a>Appar som använder modern autentisering redan

De redan moderna apparna är mest sannolika att flyttas till Azure AD. Dessa appar använder redan moderna autentiseringsprotokoll (till exempel SAML eller OpenID Connect) och kan konfigureras om så att de autentiseras med Azure AD.

Förutom valen i [Azure AD App-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) kan det bero på att det finns appar som redan finns i din organisation eller appar från tredje part från en leverantör som inte är en del av Azure AD-galleriet ([ej Galleri program)](./add-application-portal.md).

Äldre appar som du väljer att modernisera

För äldre appar som du vill modernisera för att flytta till Azure AD för grundläggande autentisering och auktorisering, låser du upp alla kraft-och data informationen som [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) och [intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) måste erbjuda.

Vi rekommenderar att du **uppdaterar stack koden för autentisering** för dessa program från det äldre protokollet (t. ex. Windows-integrerad autentisering, Kerberos-begränsad delegering, http-huvudbaserad autentisering) till ett modernt protokoll (till exempel SAML eller OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Äldre appar som du väljer att inte modernisera

För vissa appar som använder äldre autentiseringsprotokoll är det ibland inte rätt att göra av affärs skäl. Dessa omfattar följande typer av appar:

- Appar som hålls lokalt för efterlevnad eller kontroll orsaker.

- Appar som är anslutna till en lokal identitet eller Federations leverantör som du inte vill ändra.

- Appar som har utvecklats med lokala autentiseringsmetoder som du inte har några planer att flytta

Azure AD kan ge fantastiska förmåner till dessa äldre appar, eftersom du kan aktivera moderna funktioner för Azure AD-säkerhet och-styrning som [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), [villkorlig åtkomst](../conditional-access/overview.md), [identitets skydd](../identity-protection/index.yml), [delegerad program åtkomst](./access-panel-manage-self-service-access.md)och [åtkomst granskningar](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) mot dessa appar utan att behöva röra appen alls!

Börja med att **utöka de här apparna till molnet** med Azure AD [Application Proxy](./application-proxy-configure-single-sign-on-password-vaulting.md) med enkla autentiserings medel (t. ex. lösen ords valv) för att hjälpa dina användare att migreras snabbt, eller via våra [partner integrationer](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) med program leverans styrenheter som du kanske har distribuerat redan.

### <a name="new-line-of-business-lob-apps"></a>Nya LoB-appar (Line of Business)

Du utvecklar vanligt vis LoB-appar för organisationens interna användning. Om du har nya appar i pipelinen rekommenderar vi att du använder [Microsoft Identity Platform](../develop/v2-overview.md) för att implementera OpenID Connect.

### <a name="apps-to-deprecate"></a>Appar som ska föråldras

Appar utan tydliga ägare och rensa underhåll och övervakning utgör en säkerhets risk för din organisation. Överväg att använda inaktuella program när:

- deras **funktioner är mycket redundanta** med andra system • det finns **ingen företags ägare**

- **ingen användning** är klart.

Naturligtvis bör **du inte föråldra hög påverkan, affärs kritiska program**. I dessa fall arbetar du med företags ägare för att fastställa rätt strategi.

### <a name="exit-criteria"></a>Avslutnings villkor

Du har slutfört i den här fasen med:

- En god förståelse för systemen i omfånget för migreringen (som du kan dra tillbaka när du har flyttat till Azure AD)

- En lista över appar som innehåller:

  - Vilka system de apparna ansluter till o från var och på vilka enheter användarna kommer åt dem

  - Om de ska migreras, föråldras eller anslutas till [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> Du kan hämta [program identifierings kalkyl bladet](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) för att registrera de program som du vill migrera till Azure AD-autentisering, och de som du vill lämna men hantera med hjälp av [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fas 2: klassificera appar och planera pilot

Att klassificera migreringen av dina appar är en viktig övning. Alla appar behöver inte migreras och överföras samtidigt. När du har samlat in information om var och en av apparna kan du rationalisera vilka appar som ska migreras först och som kan ta extra tid.

### <a name="classify-in-scope-apps"></a>Klassificera appar i omfång

Ett sätt att tänka på detta är utmed axlarna för affärs kritiskhet, användning och livs längd, som var och en är beroende av flera faktorer.

### <a name="business-criticality"></a>Verksamhetskritisk

Affärs kritiska faktorer tar olika dimensioner för varje företag, men de två mått som du bör tänka på är **funktioner och funktioner** och **användar profiler**. Tilldela appar med unika funktioner ett högre punkt värde än de med redundanta eller föråldrade funktioner.

![Ett diagram över de olika spektrumen av funktioner & funktioner och användar profiler](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Användning

Program med **höga användnings nummer** bör få ett högre värde än appar med låg användning. Tilldela ett högre värde till appar med externa användare, chefer eller säkerhets team. Slutför dessa utvärderingar för varje app i din migrerings portfölj.

![Ett diagram över spektrumen för användar volym och användar bredd](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

När du har fastställt värdena för affärs kritiskhet och användning kan du kontrol lera **programmets livs längd** och skapa en matris med prioritet. Se en sådan matris nedan:

![Ett Triangl-diagram som visar relationerna mellan användningen, förväntad livs längd och affärs kritiskhet](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Prioritera appar för migrering

Du kan välja att påbörja migreringen av appen med antingen de lägsta prioritets apparna eller appar med högst prioritet baserat på organisationens behov.

I ett scenario där du kanske inte har erfarenhet av att använda Azure AD och identitets tjänster bör du överväga att flytta dina **lägsta prioritets program** till Azure AD först. Detta minimerar ditt företags påverkan och du kan bygga ett moment. När du har flyttat de här apparna och fått från intressenter förtroende kan du fortsätta att migrera de andra apparna.

Om det inte finns någon tydlig prioritet bör du överväga att flytta apparna som finns i [Azure AD-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) först och stödja flera identitets leverantörer (ADFS eller okta) eftersom de är enklare att integrera. De här apparna är förmodligen de **mest prioriterade apparna** i din organisation. Vi har utvecklat en samling med [självstudier](../saas-apps/tutorial-list.md) som vägleder dig genom konfigurationen för att integrera dina SaaS-program med Azure AD.

När du har en tids gräns för att migrera apparna, tar de här Bucket för appar med högst prioritet den stora arbets belastningen. Du kan slutligen välja de lägre prioritets apparna eftersom de inte kommer att ändra kostnaden även om du har flyttat tids gränsen. Även om du måste förnya licensen, är det ett litet belopp.

Förutom den här klassificeringen och, beroende på hur brådskande migreringen är, kan du även överväga att lägga upp ett **schema för migrering** inom vilka app-ägare måste ha sina appar migrerade. I slutet av den här processen bör du ha en lista över alla program i prioriterade buckets för migrering.

### <a name="document-your-apps"></a>Dokumentera dina appar

Börja med att samla in viktig information om dina program. I [kalkyl bladet för program identifiering](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)kan du snabbt och enkelt få en rekommendation till din affärs grupp.

Information som är viktig för att fatta beslut om migrering inkluderar:

- **Namn på appen** – vilken är den här appen som kallas för företaget?

- **Typ av app** – är det en SaaS-app från tredje part? En anpassad webbapp? En API?

- **Affärs kritiskitet** – är dess höga allvarlighets grad? Börjar? Eller någonstans i mellan?

- **Användarens åtkomst volym** – har alla åtkomst till den här appen eller bara några få personer?

- **Planerad livs längd** – hur lång tid tar den här appen runt? Mindre än 6 månader? Över 2 år?

- **Aktuell identitetsprovider** – vad är den primära IDP för den här appen? Eller använder den lokalt lagrings utrymme?

- **Autentiseringsmetoden** – autentiserar appen med öppna standarder?

- **Om du planerar att uppdatera app-koden** – är appen under planerad eller aktiv utveckling?

- **Oavsett om du planerar att hålla appen lokalt** – vill du behålla appen i ditt data Center långsiktigt?

- **Oavsett om appen är beroende av andra appar eller API: er** , anropas appen för närvarande i andra appar eller API: er?

- **Om appen finns i Azure AD-galleriet** – är appen för närvarande redan integrerad med [Azure AD-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Andra data som hjälper dig senare, men som du inte behöver göra ett omedelbart beslut om migreringen innehåller:

- **App-URL** – var ska användarna få åtkomst till appen?

- **Beskrivning av app** – vad är en kort beskrivning av vad appen gör?

- **Ägare av appar** – vem i verksamheten är huvud POC för appen?

- **Allmänna kommentarer eller anteckningar** – övrig allmän information om appen eller företagets ägarskap

När du har klassificerat ditt program och dokumenterat informationen, kan du se till att få företags ägare att betala till din planerade migrerings strategi.

### <a name="plan-a-pilot"></a>Planera en pilot

Apparna som du väljer för piloten ska representera nyckel identiteten och säkerhets kraven för din organisation och du måste ha tydliga köp från program ägare. Piloterna körs vanligt vis i en separat test miljö. Se [metod tips för piloter](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) på sidan distributions planer.

**Glöm inte om dina externa partner.** Se till att de deltar i migrerings scheman och testning. Se slutligen till att de har ett sätt att få åtkomst till supportavdelningen vid eventuella problem.

### <a name="plan-for-limitations"></a>Planera för begränsningar

Även om vissa appar är enkla att migrera, kan andra ta längre tid på grund av flera servrar eller instanser. Till exempel kan SharePoint-migrering ta längre tid på grund av anpassade inloggnings sidor.

Många leverantörer av SaaS-appar debiteras för att ändra SSO-anslutningen. Kontrol lera med dem och planera för detta.

Azure AD har också [tjänst begränsningar och begränsningar](../enterprise-users/directory-service-limits-restrictions.md) som du bör känna till.

### <a name="app-owner-sign-off"></a>Signering av app-ägare

Affärs kritiska och universellt använda program kan behöva en grupp pilot användare för att testa appen i pilot skedet. När du har testat en app i för produktions-eller pilot miljön ser du till att appens affärs ägare loggar ut på prestanda innan migreringen av appen och alla användare till produktions användningen av Azure AD för autentisering.

### <a name="plan-the-security-posture"></a>Planera säkerhets position

Innan du påbörjar migreringsprocessen tar du tid till att fullständigt beakta de säkerhets position som du vill utveckla för ditt företags identitets system. Detta baseras på att samla in dessa värdefulla informations uppsättningar: **identiteter och data som har åtkomst till dina data och enheter och platser**.

### <a name="identities-and-data"></a>Identiteter och data

De flesta organisationer har särskilda krav på identiteter och data skydd som varierar beroende på bransch segment och jobb funktioner inom organisationer. Se [konfigurationer för identitets-och enhets åtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations) för våra rekommendationer, inklusive en före skriven uppsättning [principer för villkorlig åtkomst](../conditional-access/overview.md) och relaterade funktioner.

Du kan använda den här informationen för att skydda åtkomsten till alla tjänster som är integrerade med Azure AD. Dessa rekommendationer är justerade med Microsofts säkra poäng samt [identitets poängen i Azure AD](../fundamentals/identity-secure-score.md). Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus

- Planera förbättringar i identitetssäkerheten

- Granska framgången för dina förbättringar

Detta hjälper dig också att implementera de [fem stegen för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md). Använd vägledningen som utgångs punkt för din organisation och justera principerna för att uppfylla organisationens särskilda krav.

### <a name="who-is-accessing-your-data"></a>Vem har åtkomst till dina data?

Det finns två huvudsakliga kategorier av användare av dina appar och resurser som stöds av Azure AD:

- **Internt:** Anställda, entreprenörer och leverantörer som har konton inom din identitets leverantör. Detta kan kräva ytterligare pivotering med olika regler för chefer eller ledarskap jämfört med andra anställda.

- **Externt:** Leverantörer, leverantörer, distributörer eller andra affärs partner som interagerar med din organisation i den ordinarie verksamheten med [Azure AD B2B-samarbete.](../external-identities/what-is-b2b.md)

Du kan definiera grupper för dessa användare och fylla i grupperna på olika sätt. Du kan välja att en administratör manuellt ska lägga till medlemmar i en grupp, eller så kan du aktivera självbetjänings grupp medlemskap. Regler kan upprättas som automatiskt lägger till medlemmar i grupper baserat på de angivna kriterierna med [dynamiska grupper](../enterprise-users/groups-dynamic-membership.md).

Externa användare kan också hänvisa till kunder som kräver särskild uppmärksamhet. [Azure AD B2C](../../active-directory-b2c/overview.md)är en separat produkt som stöder kundautentisering. Det finns dock utanför det här dokumentets omfattning.

### <a name="devicelocation-used-to-access-data"></a>Enhet/plats som används för att komma åt data

Den enhet och plats som en användare använder för att få åtkomst till en app är också viktiga. Enheter som är fysiskt anslutna till företagets nätverk är säkrare. Anslutningar utanför nätverket via VPN kan behöva granskning.

![Ett diagram som visar relationen mellan användar plats och data åtkomst](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Med dessa aspekter av resurs, användare och enhet i åtanke kan du välja att använda funktioner för [villkorlig åtkomst i Azure AD](../conditional-access/overview.md) . Villkorlig åtkomst går utöver användar behörigheter: det baseras på en kombination av faktorer, till exempel identiteten för en användare eller grupp, nätverket som användaren är ansluten till, den enhet och det program som de använder och vilken typ av data som de försöker komma åt. Åtkomsten som beviljas användaren anpassas till den här bredare uppsättning villkor.

### <a name="exit-criteria"></a>Avslutnings villkor

Du är klar med den här fasen när du:

- Bekanta dig med dina appar
  - Har fullständigt dokumenterat apparna som du vill migrera
  - Ha prioriterade appar baserat på affärs kritiskhet, användnings volym och livs längd

- Har valt appar som motsvarar dina krav för en pilot

- Affärs ägar köp till din prioritering och strategi

- Förstå dina säkerhets position behov och hur du implementerar dem

## <a name="phase-3-plan-migration-and-testing"></a>Fas 3: Planera migrering och testning

När du har fått företags köpet är nästa steg att börja migrera de här apparna till Azure AD-autentisering.

### <a name="migration-tools-and-guidance"></a>Verktyg och vägledning för migrering

Använd verktygen och anvisningarna nedan för att följa de exakta steg som krävs för att migrera dina program till Azure AD:

- **Allmän vägledning för migrering** – Använd formulären whitepaper, verktyg, e-postmallar och program i [Azure AD Apps Migration Toolkit](./migration-resources.md) för att identifiera, klassificera och migrera dina appar.

- **SaaS-program** – se vår lista över [hundratals självstudier med SaaS-appar](../saas-apps/tutorial-list.md) och den fullständiga [DISTRIBUTIONS planen för Azure AD SSO](https://aka.ms/ssodeploymentplan) för att gå igenom processen från slut punkt till slut punkt.

- **Program som körs lokalt** – lär dig allt [om Azure AD-programproxy](./application-proxy.md) och Använd den fullständiga [distributions planen för Azure AD-programproxy](https://aka.ms/AppProxyDPDownload) för att komma igång snabbt.

- **Appar som du utvecklar** – Läs våra steg för steg-vägledning om [integrering](../develop/quickstart-register-app.md) och [registrering](../develop/quickstart-register-app.md) .

Efter migreringen kan du välja att skicka kommunikationen informerar användarna om distributionen och påminna dem om eventuella nya steg som de måste vidta.

### <a name="plan-testing"></a>Planera testning

Under migreringen kanske appen redan har en test miljö som används vid vanliga distributioner. Du kan fortsätta att använda den här miljön för migrations testning. Om en test miljö inte är tillgänglig för närvarande kan du konfigurera en med Azure App Service eller Azure Virtual Machines, beroende på programmets arkitektur. Du kan välja att konfigurera en separat test-Azure AD-klient som ska användas när du utvecklar dina AppData. Den här klienten startar i ett rent tillstånd och kommer inte att konfigureras för synkronisering med några system.

Du kan testa varje app genom att logga in med en test användare och se till att alla funktioner är desamma som innan migreringen. Om du bestämmer under testningen att användarna måste uppdatera sina [MFA](/active-directory/authentication/howto-mfa-userstates) -eller [SSPR](../authentication/tutorial-enable-sspr.md)-inställningar eller om du lägger till den här funktionen under migreringen ska du se till att lägga till den i kommunikations planen för slutanvändare. Se [MFA](https://aka.ms/mfatemplates) -och [SSPR](https://aka.ms/ssprtemplates) för slut användar kommunikation.

När du har migrerat apparna går du till [Azure-portalen](https://aad.portal.azure.com/) för att testa om migreringen lyckades. Följ anvisningarna nedan:

- Välj **företags program &gt; alla program** och hitta din app från listan.

- Välj **hantera &gt; användare och grupper** för att tilldela minst en användare eller grupp till appen.

- Välj **hantera &gt; villkorlig åtkomst**. Granska listan med principer och se till att du inte blockerar åtkomsten till programmet med en [princip för villkorlig åtkomst](../conditional-access/overview.md).

Beroende på hur du konfigurerar din app, kontrol lera att SSO fungerar korrekt.

| Autentiseringstyp      | Testning                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Välj **företags program &gt; behörigheter** och se till att du har samtyckt till programmet som ska användas i din organisation i användar inställningarna för din app. |
| **SAML-baserad SSO** | Använd knappen [testa SAML-inställningar](./debug-saml-sso-issues.md) under **enkel inloggning.** |
| **Lösenordsbaserad SSO** | Hämta och installera mina [appars säkra inloggnings tillägg](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Med det här tillägget kan du starta valfri organisations molnappar som kräver att du använder en SSO-process. |

| **[Programproxy](./application-proxy.md)** | Se till att din anslutning körs och är tilldelad till ditt program. Besök [fel söknings guiden för programproxy](./application-proxy-troubleshoot.md) för ytterligare hjälp. |

### <a name="troubleshoot"></a>Felsöka

Om du stöter på problem kan du gå till [fel söknings guiden för appar](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) för att få hjälp. Se även [problem med att logga in till ett anpassat, utvecklat program](./application-sign-in-problem-federated-sso-gallery.md).

### <a name="plan-rollback"></a>Planera återställning

Om migreringen Miss lyckas är den bästa strategin att återställa och testa. Här följer de steg som du kan vidta för att undvika problem med migreringen:

- **Ta skärm bilder** av appens befintliga konfiguration. Du kan se tillbaka om du måste konfigurera om appen en gång till.

- Du kan också överväga att **tillhandahålla länkar till äldre autentisering**, i händelse av problem med molnbaserad autentisering.

- Innan du Slutför migreringen ska du **inte ändra den befintliga konfigurationen** med den tidigare identitets leverantören.

- Börja med att migrera **appar som stöder flera IDP: er**. Om något går fel kan du alltid ändra till den önskade IdP-konfigurationen.

- Se till att din app-upplevelse har en **feedback-knapp** eller pekare till **supportavdelningen** i händelse av problem.

### <a name="exit-criteria"></a>Avslutnings villkor

Du är klar med den här fasen när du har:

- Fastställt hur varje app kommer att migreras

- Granskat Migreringsverktyg

- Planerat testning, inklusive test miljöer och-grupper

- Planerad återställning

## <a name="phase-4-plan-management-and-insights"></a>Fas 4: planera hantering och insikter

När appar migreras måste du se till att:

- Användare kan komma åt och hantera på ett säkert sätt

- Du kan få lämpliga insikter om användnings-och app-hälsa

Vi rekommenderar att du vidtar följande åtgärder som är relevanta för din organisation.

### <a name="manage-your-users-app-access"></a>Hantera användarnas app-åtkomst

När du har migrerat apparna kan du förbättra användar upplevelsen på många sätt

**Gör det lättare att identifiera appar**

**Peka användaren** på Portal upplevelsen för mina [appar](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Här har de åtkomst till alla molnbaserade appar, appar som du gör tillgängliga med hjälp av [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)och appar som använder [programproxy](./application-proxy.md) , förutsatt att de har behörighet att komma åt dessa appar.


Du kan hjälpa dina användare att identifiera sina appar:

- Använd den [befintliga funktionen för enkel inloggning](./view-applications-portal.md) för att **länka dina användare till valfri app**


- Aktivera självbetjäning för [program åtkomst](./manage-self-service-access.md)till en app och **Låt användarna lägga till appar som du kan granska**

- [Dölj program från slutanvändare](./hide-application-from-user-portal.md) (standardappar eller andra appar i Microsoft) så att **apparna behöver bli mer synliga**

### <a name="make-apps-accessible"></a>Gör appar tillgängliga

**Låt användarna komma åt appar från sina mobila enheter**. Användare kan komma åt mina apps-portalen med Intune-hanterad webbläsare på sina [iOS](./hide-application-from-user-portal.md) 7,0-eller senare-eller [Android](./hide-application-from-user-portal.md) -enheter.

Användare kan ladda ned en **Intune-hanterad webbläsare**:

- **För Android-enheter** från [Google Play-butiken](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)

- **För Apple-enheter** kan du från [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) eller ladda ned [appen Mina appar-mobilapp för iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Låt användarna öppna sina appar från ett webb läsar tillägg.**

Användare kan [Ladda ned säkra inloggnings tillägg](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) för Mina appar i [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) eller [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) och kan starta appar direkt från webbläsarens fält till:

- **Sök efter sina appar och se till att de mest nyligen använda apparna visas**

- **Konvertera automatiskt interna URL: er** som du har konfigurerat i [Application Proxy](./application-proxy.md) till lämpliga externa URL: er. Dina användare kan nu arbeta med de länkar som de är bekanta med oavsett var de befinner sig.

**Låt användarna öppna sina appar från Office.com.**

Användarna kan gå till [Office.com](https://www.office.com/) för att **söka efter sina appar och se till att de mest nyligen använda apparna visas** för dem direkt från var de fungerar.

### <a name="secure-app-access"></a>Säker åtkomst till appar

Azure AD tillhandahåller en central åtkomst plats för hantering av dina migrerade appar. Gå till [Azure Portal](https://portal.azure.com/) och aktivera följande funktioner:

- **Skydda användarens åtkomst till appar.** Aktivera [principer för villkorlig åtkomst](../conditional-access/overview.md)eller [identifiera identitets skydd](../identity-protection/overview-identity-protection.md)för att skydda användarnas åtkomst till program baserat på enhetens tillstånd, plats med mera.

- **Automatisk etablering.** Konfigurera [Automatisk etablering av användare](../app-provisioning/user-provisioning.md) med en mängd SaaS-appar från tredje part som användare behöver åtkomst till. Förutom att skapa användar identiteter, inkluderar den underhåll och borttagning av användar identiteter som status eller roller ändras.

- **Delegera** **hantering** av användar åtkomst. Om det behövs kan du aktivera självbetjänings program åtkomst till dina appar och *tilldela en affärs god kännare för att godkänna åtkomst till dessa appar*. Använd [grupp hantering](../enterprise-users/groups-self-service-management.md)via självbetjäning för grupper som tilldelats samlingar av appar.

- **Delegera administratörs åtkomst.** använda **katalog rollen** för att tilldela en administratörs roll (till exempel program administratör, moln program administratör eller programutvecklare) till din användare.

### <a name="audit-and-gain-insights-of-your-apps"></a>Granska och få insikter om dina appar

Du kan också använda [Azure Portal](https://portal.azure.com/) för att granska alla dina appar från en central plats,

- **Granska din app** med **företags program, granska** eller komma åt samma information från [Azure AD repor ting-API: n](../reports-monitoring/concept-reporting-api.md) för att integrera med dina favorit verktyg.

- **Visa behörigheterna för en app** med **företags program, behörigheter** för appar med OAuth/OpenID Connect.

- **Få inloggnings** information med hjälp av **företags program, inloggnings** program. Få åtkomst till samma information från [Azure AD repor ting-API: et.](../reports-monitoring/concept-reporting-api.md)

- **Visualisera appens användning** från [Azure AD PowerBI-innehålls paketet](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Avslutnings villkor

Du är klar med den här fasen när du:

- Ge användarna säker åtkomst till appen

- Hantera till granskning och få insikter om de migrerade apparna

### <a name="do-even-more-with-deployment-plans"></a>Gör ännu mer med distributions planer

Distributions planer vägleder dig genom affärs värde, planering, implementerings steg och hantering av Azure AD-lösningar, inklusive migrering av appar. De samlar allt du behöver för att börja distribuera och få ut värdet från Azure AD-funktioner. Distributions guiderna innehåller innehåll som Microsoft rekommenderar bästa praxis, slutanvändarens kommunikation, planerings guider, implementerings steg, test ärenden och mycket annat.

Många [distributions planer](../fundamentals/active-directory-deployment-plans.md) är tillgängliga för din användning och vi har alltid mer!

### <a name="contact-support"></a>Kontakta supporten

Besök följande support länkar om du vill skapa eller spåra support ärenden och övervaka hälso tillstånd.

- **Support för Azure:** Du kan anropa [Microsoft Support](https://azure.microsoft.com/support) och öppna ett ärende för alla Azure

Problem med identitets distribution, beroende på din Enterprise-avtal med Microsoft.

- **FastTrack**: om du har köpt Enterprise Mobility and Security (EMS) eller Azure AD Premium licenser kan du få distributions hjälp från [FastTrack-programmet.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Engagera produkt teknik teamet:** Om du arbetar med en större kund distribution med miljon tals användare är du berättigad till support från Microsoft-konto-teamet eller din moln lösnings arkitekt. Utifrån projektets distributions komplexitet kan du arbeta direkt med [Azure Identity Product Engineering-teamet.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Azure AD Identity-blogg:** Prenumerera på [Azure AD Identity-bloggen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) för att hålla dig uppdaterad med alla de senaste produkt meddelandena, djupgående dykningar och översikts information som tillhandahålls direkt av identitets tekniks teamet.
