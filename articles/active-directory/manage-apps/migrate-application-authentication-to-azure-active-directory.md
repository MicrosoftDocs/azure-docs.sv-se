---
title: Migrera programautentisering till Azure Active Directory
description: Det här whitepaper beskriver planering för och fördelar med att migrera din programautentisering till Azure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3458f358c12ef33a337e50066e83b6e59273ccf1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376757"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrera programautentisering till Azure Active Directory

## <a name="about-this-paper"></a>Om det här dokumentet

Det här whitepaper beskriver planering för och fördelar med att migrera din programautentisering till Azure AD. Den är utformad för Azure-administratörer och identitetsexperter.

Processen kan brytas upp i fyra faser, var och en med detaljerade planerings- och avslutskriterier. Den är utformad för att hjälpa dig att planera din migreringsstrategi och förstå hur Azure AD-autentisering stöder organisationens mål.

## <a name="introduction"></a>Introduktion

I dag kräver din organisation en mängd program (appar) för att användarna ska kunna arbeta. Du fortsätter förmodligen att lägga till, utveckla eller dra tillbaka appar varje dag. Användare kommer åt dessa program från en mängd olika företagsenheter och personliga enheter och platser. De öppnar appar på många sätt, bland annat:

- via företagets startsida eller portal

- genom att bokmärka i webbläsaren

- via en leverantörs URL för SaaS-appar (programvara som en tjänst)

- länkar som skickas direkt till användarens stationära datorer eller mobila enheter via en lösning för hantering av mobila enheter/program (MDM/MAM)

Dina program använder troligen följande typer av autentisering:

- Lokala federationslösningar (till exempel Active Directory Federation Services (AD FS) (ADFS) och Ping)

- Active Directory (till exempel Kerberos Auth och Windows-Integrated Auth)

- Andra molnbaserade lösningar för identitets- och åtkomsthantering (IAM) (till exempel Okta eller Oracle)

- Lokal webbinfrastruktur (till exempel IIS och Apache)

- Molnbaserad infrastruktur (till exempel Azure och AWS)

**För att säkerställa att användarna enkelt och säkert kan komma åt program är målet att ha en enda uppsättning åtkomstkontroller och principer i dina lokala och molnbaserade miljöer.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) erbjuder en universell identitetsplattform som ger dina personer, partner och kunder en enda identitet för att få åtkomst till de program de vill ha och samarbeta från valfri plattform och enhet.

![Ett diagram över Azure Active Directory anslutning](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD har en [fullständig uppsättning identitetshanteringsfunktioner.](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad) Genom att standardisera din appautentisering och auktorisering till Azure AD får du de fördelar som dessa funktioner ger.

Du hittar fler migreringsresurser på [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Fördelar med att migrera appautentisering till Azure AD

Genom att flytta appautentisering till Azure AD kan du hantera risker och kostnader, öka produktiviteten och uppfylla efterlevnads- och styrningskrav.

### <a name="manage-risk"></a>Hantera risk

För att skydda dina appar måste du ha en fullständig vy över alla riskfaktorer. När du migrerar dina appar till Azure AD konsolideras dina säkerhetslösningar. Med den kan du:

- Förbättra säker användaråtkomst till program och associerade företagsdata med principer för villkorsstyrd [åtkomst,](../conditional-access/overview.md)multifaktorautentisering och riskbaserade [Identity Protection-tekniker i](../identity-protection/overview-identity-protection.md) realtid. [](../authentication/concept-mfa-howitworks.md)

- Skydda privilegierad användares åtkomst till din miljö med [just-in-time-administratörsåtkomst.](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)

- Använd Utformningen [av Azure AD med geo-distribuerad och hög tillgänglighet](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)för flera innehavare för dina viktigaste affärsbehov.

- Skydda dina äldre program med någon av våra [säkra integreringar av hybridåtkomstpartner som](https://aka.ms/secure-hybrid-access) du kanske redan har distribuerat.

### <a name="manage-cost"></a>Hantera kostnader

Din organisation kan ha flera IAM-lösningar (Identity Access Management). Att migrera till en Azure AD-infrastruktur är en möjlighet att minska beroenden av IAM-licenser (lokalt eller i molnet) och infrastrukturkostnader. I fall där du kanske redan har betalat för Azure AD via Microsoft 365-licenser finns det ingen anledning att betala den extra kostnaden för en annan IAM-lösning.

**Med Azure AD kan du minska infrastrukturkostnaderna genom att:**

- Tillhandahålla säker fjärråtkomst till lokala appar med hjälp av [Azure AD Programproxy](./application-proxy.md).

- Frikoppla appar från metoden med autentiseringsuppgifter på plats i klientorganisationen genom att konfigurera Azure AD som betrodd [universell identitetsprovider.](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)

### <a name="increase-productivity"></a>Öka produktiviteten

Ekonomi- och säkerhetsfördelar gör att organisationer kan införa Azure AD, men fullständig implementering och efterlevnad är troligare om användarna också har nytta av det. Med Azure AD kan du:

- Förbättra slutanvändarupplevelsen Sign-On enkel inloggning [(SSO)](./what-is-single-sign-on.md) via sömlös och säker åtkomst till alla program, från valfri enhet och valfri plats.

- Använd IAM-funktioner för självbetjäning, till exempel [lösenordsåterställning](../authentication/concept-sspr-howitworks.md) via självbetjäning och [grupphantering via självbetjäning.](../enterprise-users/groups-self-service-management.md)

- Minska det administrativa arbetet genom att endast hantera en enda identitet för varje användare i molnet och lokala miljöer:

  - [Automatisera etablering av användarkonton](../app-provisioning/user-provisioning.md) (i [Azure AD-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) baserat på Azure AD-identiteter
  - Få åtkomst till alla dina appar från panelen MyApps i [Azure Portal ](https://portal.azure.com/)

- Gör det möjligt för utvecklare att skydda åtkomsten till sina appar och förbättra slutanvändaren genom att använda [Microsoft Identity Platform](../develop/v2-overview.md) med Microsoft Authentication Library (MSAL).

- Ge dina partner åtkomst till molnresurser med [hjälp av Azure AD B2B-samarbete.](../external-identities/what-is-b2b.md) Molnresurser tar bort arbetet med att konfigurera punkt-till-punkt-federation med dina partner.

### <a name="address-compliance-and-governance"></a>Hantera efterlevnad och styrning

Säkerställa efterlevnad av regelkrav genom att tillämpa företagets åtkomstprinciper och övervaka användaråtkomst till program och associerade data med integrerade granskningsverktyg och API:er. Med Azure AD kan du övervaka programinloggar via rapporter som använder [SIEM-verktyg (Security Incident and Event Monitoring).](../reports-monitoring/plan-monitoring-and-reporting.md) Du kan komma åt rapporterna från portalen eller API:er och programmässigt granska vem som har åtkomst till dina program och ta bort åtkomsten till inaktiva användare via åtkomstgranskningar.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planera migreringsfaserna och projektstrategin

När teknikprojekt misslyckas beror det ofta på felmatchade förväntningar, rätt intressenter är inte inblandade eller brist på kommunikation. Se till att du lyckas genom att planera själva projektet.

### <a name="the-phases-of-migration"></a>Migreringsfaserna

Innan vi går in på verktygen bör du förstå hur du ska tänka igenom migreringsprocessen. Vi rekommenderar följande fyra faser via flera workshoppar för direkt till kund:

![Ett diagram över migreringsfaserna](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Sätt ihop projektteamet

Programmigrering är ett teamarbete och du måste se till att du har alla viktiga positioner ifyllda. Stöd från seniora företagsledare är viktigt. Se till att du involverar rätt uppsättning chefer, beslutsfattare och ämnesexperter (SMF).

Under migreringsprojektet kan en person uppfylla flera roller, eller så kan flera personer uppfylla varje roll, beroende på organisationens storlek och struktur. Du kan också vara beroende av andra team som spelar en viktig roll i ditt säkerhetslandskap.

Följande tabell innehåller viktiga roller och deras bidrag:

| Roll          | Bidrag                                              |
| ------------- | ---------------------------------------------------------- |
| **Projektledare** | Projektledare som ansvarar för att vägleda projektet, inklusive:<br /> – få chefsstöd<br /> – Ta med intressenter<br /> – hantera scheman, dokumentation och kommunikation |
| **Identitetsarkitekt/Azure AD App administratör** | De ansvarar för följande:<br /> – utforma lösningen i samarbete med intressenter<br /> – dokumentera lösningsdesignen och operativa procedurer för övergivning till driftsteamet<br /> – hantera förproduktions- och produktionsmiljöerna |
| **Lokalt AD-driftteam** | Organisationen som hanterar de olika lokala identitetskällorna, till exempel AD-skogar, LDAP-kataloger, HR-system osv.<br /> – utföra de reparationsåtgärder som krävs innan du synkroniserar<br /> – Ange de tjänstkonton som krävs för synkronisering<br /> – ge åtkomst för att konfigurera federation till Azure AD |
| **IT-supportansvarig** | En representant från IT-supportorganisationen som kan ge feedback om support för den här ändringen ur ett supportperspektiv. |
| **Säkerhetsägare**  | En representant från säkerhetsteamet som kan se till att planen uppfyller organisationens säkerhetskrav. |
| **Tekniska ägare av program** | Innehåller tekniska ägare av de appar och tjänster som ska integreras med Azure AD. De tillhandahåller programmens identitetsattribut som ska ingå i synkroniseringsprocessen. De har vanligtvis en relation med CSV-representanter. |
| **Programföretagsägare** | Representativa kollegor som kan ge indata om användarupplevelsen och användbarheten för den här ändringen ur en användares perspektiv och äger den övergripande affärsaspekten av programmet, vilket kan omfatta hantering av åtkomst. |
| **Pilotgrupp med användare** | Användare som testar som en del av sitt dagliga arbete, pilotupplevelsen och ger feedback för att vägleda resten av distributionerna. |

### <a name="plan-communications"></a>Planera kommunikation

Ett effektivt affärsinteragemang och kommunikation är nyckeln till framgång. Det är viktigt att ge intressenter och slutanvändare ett sätt att få information och hålla dig informerad om schemauppdateringar. Utbilda alla om värdet av migreringen, vad de förväntade tidslinjerna är och hur du planerar för eventuella tillfälliga verksamhetsavbrott. Använd flera vägar, till exempel sessionssessioner, e-postmeddelanden, ett-till-en-möten, banderoller och stadshallar.

Baserat på den kommunikationsstrategi som du har valt för appen kanske du vill påminna användarna om den väntande stilleståndstiden. Du bör också kontrollera att det inte finns några nya ändringar eller affärspåverkan som skulle kräva att distributionen skjuts upp.

I följande tabell hittar du den minsta föreslagna kommunikationen för att hålla intressenterna informerade:

**Planera faser och projektstrategi:**

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| Medvetenhet och affärsvärde/tekniskt värde för projektet | Alla utom slutanvändare |
| Begäran för pilotappar | – Appföretagsägare<br />– Tekniska ägare av appar<br />– Arkitekter och identitetsteam |

**Fas 1 – Identifiera och omfång:**

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| – Begäran om programinformation<br />– Resultat av omfångsövning | – Tekniska ägare av appar<br />– Appföretagsägare |

**Fas 2 – Klassificera appar och planera pilottest:**

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| – Resultatet av klassificeringar och vad det innebär för migreringsschemat<br />– Preliminärt migreringsschema | – Tekniska ägare av appar<br /> – Appföretagsägare |

**Fas 3 – Planera migrering och testning:**

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| – Resultat av testning av programmigrering | – Tekniska ägare av appar<br />– Appföretagsägare |
| – Meddelande om att migrering kommer och förklaring av resulterande slutanvändarupplevelser.<br />– Stilleståndstid kommer och fullständig kommunikation, inklusive vad de nu bör göra, feedback och hur du får hjälp | – Slutanvändare (och alla andra) |

**Fas 4 – Hantera och få insikter:**

| Kommunikation      | Målgrupp                                          |
| ------------------ | ------------------------------------------------- |
| Tillgängliga analyser och hur du kommer åt | – Tekniska ägare av appar<br />– Appföretagsägare |

### <a name="migration-states-communication-dashboard"></a>Kommunikationsinstrumentpanel för migrerings tillstånd

Det är viktigt att kommunicera det övergripande tillståndet för migreringsprojektet eftersom det visar förloppet och hjälper appägare vars appar kommer att migreras för att förbereda för flytten. Du kan skapa en enkel instrumentpanel med hjälp Power BI eller andra rapporteringsverktyg för att ge insyn i statusen för program under migreringen.

De migrerings tillstånd som du kan överväga att använda är följande:

| Migrerings tillstånd       | Åtgärdsplan                                   |
| ---------------------- | --------------------------------------------- |
| **Inledande begäran** | Leta upp appen och kontakta ägaren för mer information |
| **Utvärderingen är klar** | Appägaren utvärderar appkraven och returnerar appformuläret</td>
| **Konfigurationen pågår** | Utveckla de ändringar som krävs för att hantera autentisering mot Azure AD |
| **Testkonfigurationen lyckades** | Utvärdera ändringarna och autentisera appen mot Azure AD-testklienten i testmiljön |
| **Produktionskonfigurationen lyckades** | Ändra konfigurationerna så att de fungerar mot AD-produktionsklienten och utvärdera appautentisering i testmiljön |
| **Slutföra/signera** | Distribuera ändringarna för appen till produktionsmiljön och kör mot Azure AD-produktionsklienten |

Detta säkerställer att appägare vet vad appmigrerings- och testschemat är när deras appar är upp för migrering och vilka resultat som kommer från andra appar som redan har migrerats. Du kan också överväga att tillhandahålla länkar till din buggspårardatabas så att ägare kan skapa och visa problem för appar som migreras.

### <a name="best-practices"></a>Bästa praxis

Här följer våra kund- och partnerberättelser och rekommenderade metodtips:

- [Fem tips för att förbättra migreringsprocessen för att Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) av Patriot Consulting, medlem i vårt partnernätverk som fokuserar på att hjälpa kunder att distribuera Microsofts molnlösningar på ett säkert sätt.

- [Utveckla en riskhanteringsstrategi för azure](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) AD-programmigrering av Edgile, en partner som fokuserar på IAM och riskhanteringslösningar.

## <a name="phase-1-discover-and-scope-apps"></a>Fas 1: Identifiera och begränsa omfång för appar

**Identifiering och analys av program är en grundläggande övning för att ge dig en bra start.** Du kanske inte känner till allt så var beredd på att hantera de okända apparna.

### <a name="find-your-apps"></a>Hitta dina appar

Den första beslutspunkten i en programmigrering är vilka appar som ska migreras, vilka om några som ska finnas kvar och vilka appar som ska bli inaktuella. Det finns alltid en möjlighet att göra de appar som du inte kommer att använda i din organisation inaktuella. Det finns flera sätt att hitta appar i din organisation. **När du identifierar appar bör du se till att du inkluderar underutveckling och planerade appar. Använd Azure AD för autentisering i alla framtida appar.**

**Använda Active Directory Federation Services (AD FS) (AD FS) för att samla in rätt appinventering:**

- **Använd Azure AD Connect Health.** Om du har Azure AD Premium licens rekommenderar vi att [du Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) att analysera appanvändningen i din lokala miljö. Du kan använda [ADFS-programrapporten](./migrate-adfs-application-activity.md) (förhandsversion) för att identifiera ADFS-program som kan migreras och utvärdera om programmet är redo att migreras. När du har slutfört migreringen [distribuerar Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) som gör att du kontinuerligt kan övervaka Skugg-IT i din organisation när du är i molnet.

- **AD FS loggen parsar**. Om du inte har några Azure AD Premium rekommenderar vi att du använder ADFS till Azure AD-appmigreringsverktyg baserat på [PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration). Se [lösningsguiden:](./migrate-adfs-apps-to-azure.md)

[Migrera appar från Active Directory Federation Services (AD FS) (AD FS) till Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Använda andra identitetsproviders (IdP:er)

För andra identitetsproviders (till exempel Okta eller Ping) kan du använda deras verktyg för att exportera programinventeringen. Du kan överväga att titta på tjänstprinciper som är registrerade i Active Directory och som motsvarar webbapparna i din organisation.

### <a name="using-cloud-discovery-tools"></a>Använda cloud discovery-verktyg

I molnmiljön behöver du omfattande synlighet, kontroll över dataresor och avancerad analys för att hitta och bekämpa cyberhot i alla dina molntjänster. Du kan samla in molnappinventeringen med hjälp av följande verktyg:

- **Cloud Access Security Broker (CASB)**– En [CASB](/cloud-app-security/) fungerar vanligtvis tillsammans med brandväggen för att ge insyn i dina anställdas användning av molnprogram och hjälper dig att skydda företagets data mot cyberhot. CASB-rapporten kan hjälpa dig att fastställa de mest använda apparna i din organisation och de tidiga målen för att migrera till Azure AD.

- **Cloud Discovery** – Genom [att konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)får du insyn i molnappanvändningen och kan identifiera osanktionerade appar eller Shadow IT-appar.

- **API:er** – För appar som är anslutna till molninfrastrukturen kan du använda API:erna och verktygen på dessa system för att börja göra en inventering av värdbaserade appar. I Azure-miljön:

  - Använd [cmdleten Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) för att hämta information om Azure-webbplatser.

  - Använd [cmdleten Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) för att hämta information om din Azure-Web Apps.
D
  - Du hittar alla appar som körs på Microsoft IIS från Windows-kommandoraden med hjälp [ avAppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Använd [program](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) och [tjänstens huvudnamn](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) för att hämta information om en app- och appinstans i en katalog i Azure AD.

### <a name="using-manual-processes"></a>Använda manuella processer

När du har tagit de automatiserade metoder som beskrivs ovan har du ett bra sätt att hantera dina program. Du kan dock överväga att göra följande för att säkerställa att du har god täckning för alla användaråtkomstområden:

- Kontakta de olika företagsägarna i din organisation för att hitta de program som används i din organisation.

- Kör ett HTTP-inspektionsverktyg på proxyservern eller analysera proxyloggar för att se var trafiken ofta dirigeras.

- Granska webbloggar från populära företagsportalwebbplatser för att se vilka länkar användarna har åtkomst till mest.

- Kontakta chefer eller andra viktiga affärsmedlemmar för att se till att du har gått in på de affärskritiska apparna.

### <a name="type-of-apps-to-migrate"></a>Typ av appar som ska migreras

När du har hittat dina appar identifierar du de här typerna av appar i din organisation:

- Appar som redan använder moderna autentiseringsprotokoll

- Appar som använder äldre autentiseringsprotokoll som du väljer att modernisera

- Appar som använder äldre autentiseringsprotokoll som du väljer ATT INTE modernisera

- Nya verksamhetsradsappar (LoB)

### <a name="apps-that-use-modern-authentication-already"></a>Appar som redan använder modern autentisering

De redan moderniserade apparna är mest sannolika att flyttas till Azure AD. De här apparna använder redan moderna autentiseringsprotokoll (till exempel SAML eller OpenID Connect) och kan konfigureras om för att autentisera med Azure AD.

Förutom alternativen i [Azure AD-appgalleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) kan dessa vara appar som redan finns i din organisation eller andra tredjepartsappar från en leverantör som inte är en del av Azure[AD-galleriet ( icke-galleriprogram).](./add-application-portal.md)

Äldre appar som du väljer att modernisera

För äldre appar som du vill modernisera kan du flytta till Azure AD för kärnautentisering och auktorisering för att få tillgång till all den kraft och den datarikhet som [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) [och Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) har att erbjuda.

Vi **rekommenderar** att du uppdaterar autentiseringsstackkoden för dessa program från det äldre protokollet (till exempel Windows-Integrated-autentisering, Kerberos-begränsad delegering, HTTP-huvudbaserad autentisering) till ett modernt protokoll (till exempel SAML eller OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Äldre appar som du väljer ATT INTE modernisera

För vissa appar som använder äldre autentiseringsprotokoll är ibland modernisering av deras autentisering inte rätt sak att göra av affärsskäl. Dessa omfattar följande typer av appar:

- Appar som förvaras lokalt av efterlevnads- eller kontrollskäl.

- Appar som är anslutna till en lokal identitet eller federationsprovider som du inte vill ändra.

- Appar som har utvecklats med lokala autentiseringsstandarder som du inte har några planer på att flytta

Azure AD kan ge bra fördelar med dessa äldre appar eftersom du kan aktivera moderna säkerhets- och styrningsfunktioner i [](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) Azure AD som [Multi-Factor Authentication,](../authentication/concept-mfa-howitworks.md) [Villkorsstyrd](../conditional-access/overview.md)åtkomst, [Identitetsskydd,](../identity-protection/index.yml) [Delegerad](./access-panel-manage-self-service-access.md)programåtkomst och Åtkomstgranskningar mot dessa appar utan att röra appen alls!

Börja med **att** utöka dessa appar till molnet med Azure AD [Programproxy](./application-proxy-configure-single-sign-on-password-vaulting.md) med hjälp av enkel autentisering (till exempel [Lösenordsvalv)](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) för att snabbt migrera dina användare eller via våra partnerintegreringar med programleveranskontrollanter som du kanske redan har distribuerat.

### <a name="new-line-of-business-lob-apps"></a>Nya Verksamhetsradsappar (LoB)

Du utvecklar vanligtvis LoB-appar för din organisations användning internt. Om du har nya appar i pipelinen rekommenderar vi att du använder [Microsoft Identity Platform för](../develop/v2-overview.md) att implementera OpenID Connect.

### <a name="apps-to-deprecate"></a>Appar som ska bli inaktuella

Appar utan tydliga ägare och rensat underhåll och övervakning utgör en säkerhetsrisk för din organisation. Överväg att göra program inaktuella när:

- deras **funktioner är mycket redundanta** med andra system • det finns ingen **företagsägare**

- det finns helt klart **ingen användning**.

Vi rekommenderar att **du inte inaktuella affärskritiska program med hög påverkan.** I dessa fall bör du samarbeta med företagsägare för att fastställa rätt strategi.

### <a name="exit-criteria"></a>Avslutningsvillkor

Du lyckas i den här fasen med:

- En god förståelse för de system som omfattas av migreringen (som du kan dra tillbaka när du har flyttat till Azure AD)

- En lista över appar som innehåller:

  - Vilka system dessa appar ansluter till
  - Var och på vilka enheter användarna kommer åt dem
  - Om de kommer att migreras, bli inaktuella eller anslutas [till Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> Du kan ladda ned [kalkylbladet för programidentifiering](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) för att registrera de program som du vill migrera till Azure AD-autentisering och de som du vill lämna men hantera med hjälp [av Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fas 2: Klassificera appar och planera pilottest

Att klassificera migreringen av dina appar är en viktig övning. Alla appar behöver inte migreras och överföras samtidigt. När du har samlat in information om var och en av apparna kan du rationalisera vilka appar som ska migreras först och vilka som kan ta längre tid.

### <a name="classify-in-scope-apps"></a>Klassificera omfångsappar

Ett sätt att tänka på detta är längs axlarna för affärskritiskhet, användning och livslängd, som var och en är beroende av flera faktorer.

### <a name="business-criticality"></a>Verksamhetskritisk

Affärskritiskhet har olika dimensioner för varje företag, men de två mått som du bör tänka på är **funktioner och** **användarprofiler.** Tilldela appar med unika funktioner ett högre punktvärde än de med redundanta eller föråldrade funktioner.

![Ett diagram över spektrumet av funktioner & funktioner och användarprofiler](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Användning

Program med **höga användningsnummer** bör få ett högre värde än appar med låg användning. Tilldela ett högre värde till appar med externa användare, chefer eller säkerhetsteamanvändare. Slutför dessa utvärderingar för varje app i din migreringsportfölj.

![Ett diagram över spektrumet av användarvolym och användarlängd](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

När du har fastställt värden för affärskritiskhet och användning kan du sedan fastställa **programmets** livslängd och skapa en matris med prioritet. Se en sådan matris nedan:

![Ett triangeldiagram som visar relationerna mellan användning, förväntad livslängd och affärskritiskhet](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Prioritera appar för migrering

Du kan välja att påbörja appmigrering med appar med lägst prioritet eller appar med högst prioritet baserat på organisationens behov.

I ett scenario där du kanske inte har erfarenhet av Azure AD och Identity Services bör du överväga att flytta dina appar med **lägst prioritet** till Azure AD först. Detta minimerar din verksamhetspåverkan och du kan skapa arbete. När du har flyttat apparna och fått intressenternas förtroende kan du fortsätta att migrera de andra apparna.

Om det inte finns någon tydlig prioritet bör du överväga att flytta apparna som finns i [Azure AD-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) först och stödja flera identitetsproviders (ADFS eller Okta) eftersom de är enklare att integrera. Det är troligt att de här apparna **har högst prioritet i** din organisation. För att hjälpa till att integrera dina SaaS-program med Azure AD har vi utvecklat en samling [självstudier](../saas-apps/tutorial-list.md) som steg för steg väg för konfiguration.

När du har en tidsgräns för att migrera apparna tar dessa appar med högst prioritet den större arbetsbelastningen. Du kan så småningom välja appar med lägre prioritet eftersom de inte ändrar kostnaden trots att du har flyttat tidsgränsen. Även om du måste förnya licensen gäller den för en liten mängd.

Utöver den här klassificeringen och beroende på hur brådskande migreringen  är kan du även överväga att lägga upp ett migreringsschema där appägare måste engagera sig för att migrera sina appar. I slutet av den här processen bör du ha en lista över alla program i prioriterade buckets för migrering.

### <a name="document-your-apps"></a>Dokumentera dina appar

Börja med att samla in viktig information om dina program. Kalkylbladet [application discovery hjälper](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)dig att snabbt fatta dina migreringsbeslut och få en rekommendation till din affärsgrupp på nolltid.

Information som är viktig för att fatta ditt migreringsbeslut är:

- **Appnamn** – vad kallas den här appen för företaget?

- **Apptyp** – är det en SaaS-app från tredje part? En anpassad verksamhetsanpassad webbapp? Ett API?

- **Affärskritiskhet** – är den mycket kritisk? Låg? Eller någonstans däremellan?

- **Volym för användaråtkomst** – kommer alla åt den här appen eller bara några få personer?

- **Planerad livslängd – hur** länge kommer den här appen att finnas? Mindre än sex månader? Mer än två år?

- **Aktuell identitetsprovider** – vad är den primära IdP:n för den här appen? Eller förlitar den sig på lokal lagring?

- **Autentiseringsmetod –** autentiserar appen med öppna standarder?

- **Om du planerar att uppdatera appkoden** – är appen under planerad eller aktiv utveckling?

- **Om du planerar att behålla appen lokalt –** vill du behålla appen i ditt datacenter på lång sikt?

- **Om appen är beroende av andra appar eller API:er** – anropar appen för närvarande andra appar eller API:er?

- **Om appen finns i Azure AD-galleriet – är** appen redan integrerad med [Azure AD-galleriet?](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)

Andra data som kan hjälpa dig senare, men som du inte behöver fatta ett beslut om omedelbar migrering är:

- **App-URL** – var går användarna för att få åtkomst till appen?

- **Appbeskrivning** – vad är en kort beskrivning av vad appen gör?

- **Appägare** – vem i verksamheten är appens huvud-POC?

- **Allmänna kommentarer eller anteckningar** – all annan allmän information om appen eller företagsägarskapet

När du har klassificerat ditt program och dokumenterat informationen ska du se till att få företagsägare att köpa in din planerade migreringsstrategi.

### <a name="plan-a-pilot"></a>Planera ett pilotprojekt

De appar som du väljer för piloten ska representera de viktigaste identitets- och säkerhetskraven för din organisation och du måste ha ett tydligt inköp från programägare. Piloter körs vanligtvis i en separat testmiljö. Se [metodtips för piloter](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) på sidan distributionsplaner.

**Glöm inte om dina externa partner.** Se till att de deltar i migreringsscheman och testning. Kontrollera slutligen att de har ett sätt att komma åt supporten om det har varit problem.

### <a name="plan-for-limitations"></a>Planera för begränsningar

Vissa appar är enkla att migrera, men andra kan ta längre tid på grund av flera servrar eller instanser. SharePoint-migrering kan till exempel ta längre tid på grund av anpassade inloggningssidor.

Många SaaS-appleverantörer debiterar för att ändra anslutningen för enkel inloggning. Kontrollera med dem och planera för detta.

Azure AD har också [tjänstbegränsningar och begränsningar](../enterprise-users/directory-service-limits-restrictions.md) som du bör känna till.

### <a name="app-owner-sign-off"></a>Signering av appägare

Affärskritiska och universellt använda program kan behöva en grupp pilotanvändare för att testa appen i pilotfasen. När du har testat en app i förproduktions- eller pilotmiljön bör du se till att appens företagsägare signering av prestanda före migreringen av appen och alla användare till produktionsanvändningen av Azure AD för autentisering.

### <a name="plan-the-security-posture"></a>Planera säkerhetsstatus

Innan du påbörjar migreringsprocessen bör du ta dig tid att ta hänsyn till den säkerhetsstatus som du vill utveckla för företagets identitetssystem. Detta baseras på insamling av dessa värdefulla **informationsuppsättningar: Identiteter, enheter och platser som har åtkomst till dina data.**

### <a name="identities-and-data"></a>Identiteter och data

De flesta organisationer har särskilda krav på identiteter och dataskydd som varierar beroende på branschsegment och efter jobbfunktioner inom organisationer. Se [konfigurationer för identitets- och](/microsoft-365/enterprise/microsoft-365-policies-configurations) enhetsåtkomst för våra rekommendationer, inklusive en föreskriven uppsättning principer [för villkorlig åtkomst](../conditional-access/overview.md) och relaterade funktioner.

Du kan använda den här informationen för att skydda åtkomsten till alla tjänster som är integrerade med Azure AD. Dessa rekommendationer är anpassade till Microsoft Secure Score och [identitetspoängen i Azure AD.](../fundamentals/identity-secure-score.md) Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus

- Planera förbättringar i identitetssäkerheten

- Granska framgången för dina förbättringar

Detta hjälper dig också att implementera de [fem stegen för att skydda identitetsinfrastrukturen.](../../security/fundamentals/steps-secure-identity.md) Använd vägledningen som utgångspunkt för din organisation och justera principerna så att de uppfyller organisationens specifika krav.

### <a name="who-is-accessing-your-data"></a>Vem har åtkomst till dina data?

Det finns två huvudkategorier av användare av dina appar och resurser som Azure AD stöder:

- **Internt:** Anställda, leverantörer och leverantörer som har konton inom din identitetsprovider. Detta kan behöva ytterligare pivoter med olika regler för chefer eller chefer jämfört med andra anställda.

- **Extern:** Leverantörer, leverantörer, distributörer eller andra affärspartner som interagerar med din organisation i regelbunden verksamhet med [Azure AD B2B-samarbete.](../external-identities/what-is-b2b.md)

Du kan definiera grupper för dessa användare och fylla dessa grupper på olika sätt. Du kan välja att en administratör manuellt måste lägga till medlemmar i en grupp, eller så kan du aktivera gruppmedlemskap med självbetjäning. Regler kan upprättas som automatiskt lägger till medlemmar i grupper baserat på de angivna kriterierna med [hjälp av dynamiska grupper.](../enterprise-users/groups-dynamic-membership.md)

Externa användare kan också referera till kunder. [Azure AD B2C](../../active-directory-b2c/overview.md)stöder en separat produkt kundautentisering. Det ligger dock utanför omfånget för det här dokumentet.

### <a name="devicelocation-used-to-access-data"></a>Enhet/plats som används för att komma åt data

Enheten och platsen som en användare använder för att komma åt en app är också viktiga. Enheter som är fysiskt anslutna till företagets nätverk är säkrare. Anslutningar utanför nätverket via VPN kan behöva granskas.

![Ett diagram som visar relationen mellan användarplats och dataåtkomst](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Med dessa aspekter av resurs, användare och enhet i åtanke kan du välja att använda funktioner för [villkorsstyrd åtkomst i Azure AD.](../conditional-access/overview.md) Villkorlig åtkomst går utöver användarbehörigheter: den baseras på en kombination av faktorer, till exempel identiteten för en användare eller grupp, det nätverk som användaren är ansluten till, den enhet och det program som de använder samt den typ av data som de försöker komma åt. Åtkomsten som beviljas till användaren anpassas till den här bredare uppsättningen villkor.

### <a name="exit-criteria"></a>Avslutningsvillkor

Du lyckas i den här fasen när du:

- Känna till dina appar
  - Ha fullständigt dokumenterat de appar som du planerar att migrera
  - Ha prioriterade appar baserat på affärskritiskhet, användningsvolym och livslängd

- Har valt appar som representerar dina krav för en pilot

- Köp av företagsägare till din prioritering och strategi

- Förstå dina säkerhetshållningsbehov och hur du implementerar dem

## <a name="phase-3-plan-migration-and-testing"></a>Fas 3: Planera migrering och testning

När du har skaffat dig ett företagsköp är nästa steg att börja migrera dessa appar till Azure AD-autentisering.

### <a name="migration-tools-and-guidance"></a>Migreringsverktyg och vägledning

Använd verktygen och vägledningen nedan för att följa de exakta steg som krävs för att migrera dina program till Azure AD:

- **Allmän migreringsvägledning** – Använd faktabladet, verktygen, e-postmallarna och programformuläret i [Azure AD-appmigreringsverktygen](./migration-resources.md) för att identifiera, klassificera och migrera dina appar.

- **SaaS-program** – Se vår lista över hundratals Självstudier för [SaaS-appar](../saas-apps/tutorial-list.md) och den fullständiga [distributionsplanen](https://aka.ms/ssodeploymentplan) för enkel inloggning med Azure AD för att gå igenom hela processen.

- **Program som körs lokalt – Lär** dig allt om Azure [AD-Programproxy](./application-proxy.md) och använd den fullständiga [Distributionsplanen för Azure AD Programproxy](https://aka.ms/AppProxyDPDownload) för att komma igång snabbt.

- **Appar som du utvecklar** – Läs vår stegvisa vägledning för [integrering](../develop/quickstart-register-app.md) [och](../develop/quickstart-register-app.md) registrering.

Efter migreringen kan du välja att skicka kommunikation som informerar användarna om den lyckade distributionen och påminner dem om eventuella nya steg som de behöver vidta.

### <a name="plan-testing"></a>Planera testning

Under migreringsprocessen kanske appen redan har en testmiljö som används under vanliga distributioner. Du kan fortsätta att använda den här miljön för migreringstestning. Om en testmiljö inte är tillgänglig för närvarande kan du konfigurera en med hjälp av Azure App Service eller Azure Virtual Machines, beroende på programmets arkitektur. Du kan välja att konfigurera en separat testklient för Azure AD som ska användas när du utvecklar appkonfigurationerna. Den här klientorganisationen startar i ett rent tillstånd och konfigureras inte att synkronisera med något system.

Du kan testa varje app genom att logga in med en testanvändare och kontrollera att alla funktioner är desamma som före migreringen. Om du under testningen fastställer att användarna måste uppdatera [sina MFA-](/azure/active-directory/authentication/howto-mfa-userstates) eller [SSPR-inställningar,](../authentication/tutorial-enable-sspr.md)eller om du lägger till den här funktionen under migreringen, måste du lägga till den i kommunikationsplanen för slutanvändaren. Se [MFA-](https://aka.ms/mfatemplates) [och SSPR-kommunikationsmallar](https://aka.ms/ssprtemplates) för slutanvändare.

När du har migrerat apparna går du till [Azure Portal](https://aad.portal.azure.com/) för att testa om migreringen lyckades. Följ anvisningarna nedan:

- Välj **&gt; Företagsprogram Alla program** och leta upp din app i listan.

- Välj **Hantera användare och &gt; grupper** för att tilldela minst en användare eller grupp till appen.

- Välj **Hantera &gt; villkorlig åtkomst.** Granska listan över principer och se till att du inte blockerar åtkomsten till programmet med en princip [för villkorlig åtkomst.](../conditional-access/overview.md)

Kontrollera att enkel inloggning fungerar korrekt beroende på hur du konfigurerar din app.

| Autentiseringstyp      | Testning                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Välj **Behörigheter för &gt; företagsprogram** och se till att du har samtyckt till att programmet ska användas i din organisation i användarinställningarna för din app. |
| **SAML-baserad SSO** | Använd knappen [Testa SAML-inställningar](./debug-saml-sso-issues.md) under **Enkel inloggning.** |
| **Lösenordsbaserad enkel inloggning** | Ladda ned och installera [tillägget för säker inloggning för MyApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Det här tillägget hjälper dig att starta någon av organisationens molnappar som kräver att du använder en SSO-process. |

| **[Programproxy](./application-proxy.md)** | Se till att anslutningsappen körs och tilldelas till ditt program. Besök [felsökningsguiden Programproxy om du](./application-proxy-troubleshoot.md) vill ha mer hjälp. |

### <a name="troubleshoot"></a>Felsöka

Om du får problem kan du läsa vår [felsökningsguide för appar för](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) att få hjälp. Du kan också läsa våra felsökningsartiklar i [Problem med att logga in på SAML-baserade](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)appar med enkel inloggning.

### <a name="plan-rollback"></a>Planera återställning

Om migreringen misslyckas är den bästa strategin att återställa och testa. Här är de steg som du kan vidta för att åtgärda migreringsproblem:

- **Ta skärmbilder** av appens befintliga konfiguration. Du kan titta tillbaka om du måste konfigurera om appen igen.

- Du kan också överväga **att tillhandahålla länkar till den äldre autentiseringen** om det har varit problem med molnautentisering.

- Innan du slutför migreringen ska **du inte ändra din befintliga konfiguration med** den tidigare identitetsprovidern.

- Börja med att migrera de **appar som stöder flera IDE:er.** Om något går fel kan du alltid ändra till önskad IdP-konfiguration.

- Se till att din appupplevelse har **en feedbackknapp** eller pekare till **dina problem med supporten.**

### <a name="exit-criteria"></a>Avslutningsvillkor

Du lyckas i den här fasen när du har:

- Bestämma hur varje app ska migreras

- Granskat migreringsverktygen

- Planerat testningen, inklusive testmiljöer och grupper

- Planerad återställning

## <a name="phase-4-plan-management-and-insights"></a>Fas 4: Planera hantering och insikter

När apparna har migrerats måste du se till att:

- Användare kan komma åt och hantera på ett säkert sätt

- Du kan få rätt insikter om användning och apphälsa

Vi rekommenderar att du vidtar följande åtgärder efter behov för din organisation.

### <a name="manage-your-users-app-access"></a>Hantera användarnas appåtkomst

När du har migrerat apparna kan du utöka användarupplevelsen på många sätt

**Göra appar så att de kan upptäckas**

**Peka användaren till** [MyApps-portalen.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Här kan de komma åt alla molnbaserade appar, appar som du gör tillgängliga med [hjälp av Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)och appar som använder [Programproxy](./application-proxy.md) förutsatt att de har behörighet att komma åt dessa appar.


Du kan hjälpa användarna att identifiera sina appar:

- Använd funktionen [befintlig enkel inloggning för](./view-applications-portal.md) att länka dina användare till alla **appar**


- Aktivera [programåtkomst via självbetjäning](./manage-self-service-access.md)till en app **och låt användarna lägga till appar som du kan använda**

- [Dölj program från slutanvändare (standardappar](./hide-application-from-user-portal.md) från Microsoft eller andra appar) för **att göra de appar som de behöver mer identifieringsbara**

### <a name="make-apps-accessible"></a>Göra appar tillgängliga

**Låt användarna komma åt appar från sina mobila enheter.** Användare kan komma åt MyApps-portalen med Intune-hanterad webbläsare på [sina iOS](./hide-application-from-user-portal.md) 7.0- eller senare- eller [Android-enheter.](./hide-application-from-user-portal.md)

Användare kan ladda ned en **Intune-hanterad webbläsare:**

- **För Android-enheter**, från [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **För Apple-enheter,** från [Apple-App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) eller så kan [de ladda Mina appar mobilappen för iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Låt användarna öppna sina appar från ett webbläsartillägg.**

Användare kan ladda ned tillägget för säker inloggning i [MyApps](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) i [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) [eller Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) och kan starta appar direkt från deras webbläsarfält för att:

- **Sök efter deras appar och se till att deras senast använda appar visas**

- **Konvertera automatiskt interna URL:er** som du har konfigurerat [i Programproxy](./application-proxy.md) till lämpliga externa URL:er. Användarna kan nu arbeta med de länkar som de är bekanta med oavsett var de befinner sig.

**Låt användarna öppna sina appar från Office.com.**

Användarna kan gå till  [Office.com](https://www.office.com/) för att söka efter sina appar och visa sina senast använda appar åt dem direkt där de arbetar.

### <a name="secure-app-access"></a>Skydda appåtkomst

Azure AD tillhandahåller en central plats för åtkomst för att hantera dina migrerade appar. Gå till [Azure Portal](https://portal.azure.com/) och aktivera följande funktioner:

- **Skydda användaråtkomst till appar.** Aktivera [principer för villkorsstyrd](../conditional-access/overview.md) [åtkomst eller Identity Protection](../identity-protection/overview-identity-protection.md)för att skydda användaråtkomsten till program baserat på enhetens tillstånd, plats med mera.

- **Automatisk etablering.** Konfigurera automatisk [etablering av användare med olika](../app-provisioning/user-provisioning.md) SaaS-appar från tredje part som användarna behöver åtkomst till. Förutom att skapa användaridentiteter omfattar det underhåll och borttagning av användaridentiteter när status eller roller ändras.

- **Delegera hantering av** **användaråtkomst.** Aktivera efter behov programåtkomst via självbetjäning till dina appar och tilldela *en företags godkännare för att godkänna åtkomst till dessa appar.* Använd [grupphantering med självbetjäning](../enterprise-users/groups-self-service-management.md)för grupper som tilldelats till samlingar av appar.

- **Delegera administratörsåtkomst.** använda **Katalogroll** för att tilldela en administratörsroll (till exempel Programadministratör, Cloud Programadministratör eller Programutvecklare) till användaren.

### <a name="audit-and-gain-insights-of-your-apps"></a>Granska och få insikter om dina appar

Du kan också använda [Azure Portal](https://portal.azure.com/) för att granska alla dina appar från en central plats,

- **Granska din app med** hjälp av **Företagsprogram, Granska eller få åtkomst till samma information från [Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md) för att integrera i dina favoritverktyg.

- **Visa behörigheter för en app med** hjälp av **företagsprogram, behörigheter för** appar som använder OAuth/OpenID Connect.

- **Få inloggningsinformation med** **hjälp av Företagsprogram, Inloggningar**. Få åtkomst till samma information från [Azure AD Reporting-API:et.](../reports-monitoring/concept-reporting-api.md)

- **Visualisera appens användning från** Azure [AD Power BI-innehållspaketet](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Avslutningsvillkor

Du lyckas i den här fasen när du:

- Ge användarna säker appåtkomst

- Hantera för att granska och få insikter om de migrerade apparna

### <a name="do-even-more-with-deployment-plans"></a>Gör ännu mer med distributionsplaner

Distributionsplaner visar affärsvärde, planering, implementeringssteg och hantering av Azure AD-lösningar, inklusive appmigreringsscenarier. De sammanför allt du behöver för att börja distribuera och få ut värde av Azure AD-funktionerna. Distributionsguiderna innehåller innehåll som Microsofts rekommenderade metodtips, slutanvändarkommunikation, planeringsguider, implementeringssteg, testfall med mera.

Många [distributionsplaner](../fundamentals/active-directory-deployment-plans.md) är tillgängliga för din användning och vi gör alltid mer!

### <a name="contact-support"></a>Kontakta supporten

Gå till följande supportlänkar för att skapa eller spåra supportbiljett och övervaka hälsotillståndet.

- **Azure Support:** Du kan anropa [Microsoft Support och](https://azure.microsoft.com/support) öppna ett ärende för alla Azure-tjänster

Problem med identitetsdistribution beroende på ditt Enterprise-avtal med Microsoft.

- **FastTrack:** Om du har köpt Enterprise Mobility and Security (EMS) eller Azure AD Premium-licenser är du berättigad till distributionshjälp från [FastTrack-programmet.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Engagera produktteknikteamet:** Om du arbetar med en större kunddistribution med miljontals användare är du berättigad till support från Microsoft-konto-teamet eller ditt molnteam Lösningsarkitekt. Baserat på projektets distributionskomplexitet kan du arbeta direkt med [Azure Identity Product Engineering-teamet.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Azure AD Identity-blogg:** Prenumerera på [Azure AD Identity-bloggen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) för att hålla dig uppdaterad med alla de senaste produktmeddelandena, djupdykningarna och översiktsinformationen som tillhandahålls direkt av Identity Engineering-teamet.
