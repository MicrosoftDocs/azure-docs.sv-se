---
title: Utveckla säkra program på Microsoft Azure
description: Den här artikeln beskriver metod tips som du bör tänka på under implementerings-och verifierings faserna i ditt webb program projekt.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8e69803f4740a58adb4230bd82cc723221762cb4
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576624"
---
# <a name="develop-secure-applications-on-azure"></a>Utveckla säkra program på Azure
I den här artikeln presenterar vi säkerhets aktiviteter och kontroller för att tänka på när du utvecklar program för molnet. Säkerhets frågor och koncept som du bör tänka på under implementerings-och verifierings faserna i Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att utveckla ett säkrare program.

Följande SDL-faser beskrivs i den här artikeln:

- Implementering
- Verifiering

## <a name="implementation"></a>Implementering
Fokus i implementerings fasen är att upprätta bästa praxis för tidig förebyggande skydd och identifiera och ta bort säkerhets problem från koden.
Anta att ditt program kommer att användas på sätt som du inte avsåg att använda. Detta hjälper dig att skydda mot oavsiktlig eller avsiktlig missbruk av ditt program.

### <a name="perform-code-reviews"></a>Utföra kod granskningar

Innan du checkar in kod kan du göra [kod granskningar](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) för att öka den övergripande kod kvaliteten och minska risken för att skapa buggar. Du kan använda [Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) för att hantera kod gransknings processen.

### <a name="perform-static-code-analysis"></a>Utföra analys av statisk kod

[Statisk kod analys](https://owasp.org/www-community/controls/Static_Code_Analysis) (även kallat *käll kods analys*) utförs vanligt vis som en del av en kod granskning. Statisk kod analys avser ofta att köra statiska kod analys verktyg för att hitta potentiella sårbarheter i kod som inte körs genom att använda metoder som [bismaks kontroll](https://en.wikipedia.org/wiki/Taint_checking) och [data flödes analys](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace erbjuder [utvecklingsverktyg](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) som utför statisk kod analys och hjälper till med kod granskningar.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Verifiera och sanera varje indatamängd för ditt program

Behandla alla inmatade som ej betrodda för att skydda ditt program mot de vanligaste sårbarheterna för webb program. Ej betrodda data är ett fordon för injektions angrepp. Indata för ditt program innehåller parametrar i URL: en, indata från användaren, data från databasen eller från ett API och allt som skickas till en användare som kan manipulera. Ett program bör [Verifiera](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) att data är syntaktiskt och semantiskt giltiga innan programmet använder data på något sätt (inklusive att Visa tillbaka till användaren).

Verifiera indata tidigt i data flödet för att säkerställa att endast korrekt utformade data anges i arbets flödet. Du vill inte att felaktiga data ska sparas i databasen eller utlösa fel i en underordnad komponent.

Blocklisting och allowlisting är två allmänna metoder för att utföra verifiering av indatamängds-syntax:

  - Blocklisting försöker kontrol lera att en viss användarindata inte innehåller innehåll som är känt som skadligt.

  - Allowlisting försöker kontrol lera att en viss användar inmatning matchar en uppsättning "kända fungerande" indata. Character-baserad allowlisting är en form av allowlisting där ett program kontrollerar att användarindata endast innehåller "kända fungerande" tecken eller att indata matchar ett känt format.
    Detta kan t. ex. innebära en kontroll av att ett användar namn bara innehåller alfanumeriska tecken eller att det innehåller exakt två siffror.

Allowlisting är det bästa sättet att skapa säkra program.
Blocklisting kan inte användas eftersom det är omöjligt att tänka på en fullständig lista över potentiellt Felaktiga indatatyper.

Detta fungerar på servern, inte på klient sidan (eller på-servern och på klient sidan).

### <a name="verify-your-applications-outputs"></a>Verifiera programmets utdata

Alla utdata som du visar antingen visuellt eller i ett dokument bör alltid kodas och undantas. [Undantag](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29), även kallat *utkodning av utdata*, används för att säkerställa att ej betrodda data inte är ett fordon för inmatning-angrepp. Undantag, kombinerat med data verifiering, tillhandahåller försvars försvars nivåer för att öka säkerheten för systemet som helhet.

Med undantag ser du till att allt visas som *utdata.* Med hjälp av undantag kan tolkaren veta att data inte är avsedda att utföras och detta förhindrar attacker från att fungera. Detta är en annan vanlig angrepps teknik som kallas XSS ( *Cross-Site Scripting* ).

Om du använder ett webb ramverk från en tredje part kan du kontrol lera alternativen för utmatnings kodning på webbplatser med hjälp av [OWASP XSS-lathund bladet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Använd parametriserade frågor när du kontaktar databasen

Skapa aldrig en infogad databas fråga "i farten" i koden och skicka den direkt till-databasen. Skadlig kod som infogats i programmet kan orsaka att databasen blir stulen, rensad eller modifierad. Programmet kan också användas för att köra skadliga operativ system kommandon på det operativ system som är värd för din databas.

Använd i stället parametriserade frågor eller lagrade procedurer. När du använder parametriserade frågor kan du anropa proceduren från koden på ett säkert sätt och skicka den till en sträng utan att oroa dig för att den kommer att behandlas som en del av frågeuttrycket.

### <a name="remove-standard-server-headers"></a>Ta bort standard server rubriker

Rubriker som server, X-Powered-by och X-ASPNET-version avslöjar information om servern och underliggande tekniker. Vi rekommenderar att du undertrycker dessa huvuden för att undvika finger avtryck av programmet.
Se [ta bort standard server rubriker på Azure Websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Åtskilj dina produktions data

Dina produktions data eller "verkliga" data bör inte användas för utveckling, testning eller något annat ändamål än vad företaget avsåg. En maskerad ([anonymiserats](https://en.wikipedia.org/wiki/Data_anonymization)) data uppsättning ska användas för all utveckling och testning.

Det innebär att färre personer har till gång till dina verkliga data, vilket minskar risken för attacker. Det innebär också att färre anställda ser person uppgifter, vilket eliminerar potentiella intrång i konfidentialiteten.

### <a name="implement-a-strong-password-policy"></a>Implementera en princip för starka lösen ord

Du måste implementera en stark lösen ords princip för att säkerställa att användarna skapar ett komplext lösen ord (till exempel 12 tecken och som kräver alfanumeriska tecken och specialtecken) för att skydda dig mot brute-och ordbaserade gissning.

Du kan använda ett identitets ramverk för att skapa och genomdriva lösen ords principer. Azure AD B2C hjälper dig med lösen ords hantering genom att tillhandahålla [inbyggda principer](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [återställning av lösen ord med självbetjäning](../../active-directory-b2c/user-flow-self-service-password-reset.md)och mycket mer.

Kontrol lera att alla nycklar och lösen ord är utbytbara och att de genereras eller ersätts när du har installerat resurser för att skydda mot attacker på standard konton.

Om programmet måste generera lösen ord automatiskt måste du se till att de genererade lösen orden är slumpmässiga och att de har hög entropi.

### <a name="validate-file-uploads"></a>Verifiera fil överföringar

Om ditt program tillåter [fil överföringar](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)bör du överväga de försiktighets åtgärder som du kan vidta för den här riskfyllda aktiviteten. Det första steget i många attacker är att hämta skadlig kod i ett system som är utsatt för angrepp. Genom att använda en fil uppladdning kan angriparen utföra detta. OWASP erbjuder lösningar för att verifiera en fil för att säkerställa att filen som du laddar upp är säker.

Skydd mot skadlig kod hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Du kan installera [Microsoft Antimalware](../fundamentals/antimalware.md) eller en Microsoft partners slut punkts skydds lösning ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)och [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](../fundamentals/antimalware.md) innehåller funktioner som real tids skydd, schemalagd genomsökning, reparation av skadlig kod, signaturkrav, uppdatering av motor, exempel rapportering och insamling av undantags händelser. Du kan integrera Microsofts lösningar för program mot skadlig kod och partner med [Azure Security Center](../../security-center/security-center-partner-integration.md) för enkel distribution och inbyggd identifiering (aviseringar och incidenter).

### <a name="dont-cache-sensitive-content"></a>Cachelagra inte känsligt innehåll

Cachelagra inte känsligt innehåll i webbläsaren. Webbläsare kan lagra information för cachelagring och historik. Cachelagrade filer lagras i en mapp, t. ex. mappen Temporary Internet Files, i Internet Explorer. När dessa sidor refereras igen, visar webbläsaren sidorna från cacheminnet. Om känslig information (adress, kreditkorts information, person nummer, användar namn) visas för användaren, kan informationen lagras i webbläsarens cacheminne och kunna hämtas genom att undersöka webbläsarens cacheminne eller genom att helt enkelt trycka på webbläsarens **bak** -knapp.

## <a name="verification"></a>Verifiering
I verifierings fasen ingår en omfattande ansträngning för att säkerställa att koden uppfyller Tenets för säkerhet och sekretess som har fastställts i föregående faser.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Hitta och åtgärda säkerhets risker i dina program beroenden

Du genomsöker ditt program och dess beroende bibliotek för att identifiera eventuella kända sårbara komponenter. Produkter som är tillgängliga för att utföra den här genomsökningen omfattar [OWASP beroende kontroll](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)och [svart ankor](https://www.blackducksoftware.com/).

Sårbarhets genomsökning som drivs av [TINFOIL Security](https://www.tinfoilsecurity.com/) är tillgänglig för Azure App Service Web Apps. [TINFOIL säkerhets genomsökning via App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) erbjuder utvecklare och administratörer ett snabbt, integrerat och ekonomiskt sätt att identifiera och åtgärda sårbarheter innan en skadlig aktör kan dra nytta av dem.

> [!NOTE]
> Du kan också [integrera TINFOIL-säkerhet med Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Att integrera TINFOIL Security med Azure AD ger följande fördelar:
>  - I Azure AD kan du kontrol lera vem som har åtkomst till TINFOIL-säkerhet.
>  - Användarna kan loggas in automatiskt på TINFOIL Security (enkel inloggning) med hjälp av deras Azure AD-konton.
>  - Du kan hantera dina konton på en enda, central plats, Azure Portal.

### <a name="test-your-application-in-an-operating-state"></a>Testa programmet i ett operativ tillstånd

Dynamic App Security Testing (DAST) är en process för att testa ett program i ett operativ tillstånd för att hitta säkerhets risker. DAST-verktyg analyserar program när de körs för att hitta säkerhets sårbarheter, till exempel minnes skada, osäker Server konfiguration, skript över flera webbplatser, användar privilegier, SQL-inmatning och andra viktiga säkerhets problem.

DAST skiljer sig från SAST (static App Security testing). SAST-verktyg analyserar käll kod eller kompilerade versioner av kod när koden inte körs för att hitta säkerhets fel.

Utför DAST, helst med hjälp av en säkerhets tekniker (en [utträngande testare](../fundamentals/pen-testing.md) eller sårbarhets bedömare). Om en säker person inte är tillgänglig kan du utföra DAST med en webbproxy-skanner och lite utbildning. Anslut en DAST-skanner tidigt för att se till att du inte introducerar tydliga säkerhets problem i din kod. Se [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) -webbplatsen för en lista över säkerhets skannrar för webb program.

### <a name="perform-fuzz-testing"></a>Utför fuzz-testning

I [fuzz-testningen](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)inducerar du program fel genom att avsiktligt introducera felaktiga eller slumpmässiga data till ett program. Inducing program fel hjälper till att upptäcka potentiella säkerhets problem innan programmet släpps.

[Identifiering av säkerhets risker](https://www.microsoft.com/en-us/security-risk-detection/) är Microsofts unika fuzz testing-tjänst för att hitta säkerhets kritiska buggar i program varan.

### <a name="conduct-attack-surface-review"></a>Genomför granskning av attack ytan

Genom att granska attack ytan efter kod kompletteringen ser du till att alla design-eller implementerings ändringar av ett program eller system har beaktats. Den hjälper till att se till att alla nya angrepps vektorer som har skapats på grund av ändringarna, inklusive hot modeller, har granskats och minimerats.

Du kan bygga en bild av attack ytan genom att genomsöka programmet. Microsoft erbjuder ett analys verktyg för angrepps ytan som kallas [analys av attack ytan](https://www.microsoft.com/download/details.aspx?id=58105). Du kan välja bland många dynamiska, dynamiska testnings-och sårbarhets skannings verktyg eller-tjänster, inklusive [OWASP okaliserad-angrepps-proxy-projekt](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)och [w3af](http://w3af.sourceforge.net/). De här sökverktygen söker igenom din app och mappar de delar av programmet som är tillgängliga via webben. Du kan också söka i Azure Marketplace efter liknande [utvecklarverktyg](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Utför testning av säkerhets inträngning

Att se till att ditt program är säkert är lika viktigt som att testa andra funktioner. Gör [inträngande och testa](../fundamentals/pen-testing.md) en standard del av bygg-och distributions processen. Schemalägga vanliga säkerhetstester och sårbarhets sökning på distribuerade program och övervaka för öppna portar, slut punkter och attacker.

### <a name="run-security-verification-tests"></a>Köra tester för säkerhets verifiering

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) innehåller SVTs för flera tjänster i Azure-plattformen. Du kör dessa SVTs regelbundet för att se till att din Azure-prenumeration och de olika resurser som utgör ditt program är i säkert tillstånd. Du kan också automatisera de här testerna med hjälp av funktionen för att använda funktionen för kontinuerlig integrering/-distribution (CI/CD) i AzSK, vilket gör SVTs tillgängligt som ett Visual Studio-tillägg.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhets kontroller och aktiviteter som kan hjälpa dig att utforma och distribuera säkra program.

- [Utforma säkra program](secure-design.md)
- [Distribuera säkra program](secure-deploy.md)