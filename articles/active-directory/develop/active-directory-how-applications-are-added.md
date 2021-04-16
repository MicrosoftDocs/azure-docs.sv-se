---
title: Hur och varför program läggs till i Azure AD
titleSuffix: Microsoft identity platform
description: Vad innebär det att ett program läggs till i Azure AD och hur kommer de dit?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: ac02638dfdef4867e93e277175df82be18be66a7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530093"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hur och varför program läggs till i Azure AD

Det finns två representationer av program i Azure AD:

* [Programobjekt](app-objects-and-service-principals.md#application-object) – Även om det [finns](#notes-and-exceptions)undantag kan programobjekt betraktas som definitionen av ett program.
* [Tjänstens huvudnamn](app-objects-and-service-principals.md#service-principal-object) – Kan betraktas som en instans av ett program. Tjänstens huvudnamn refererar vanligtvis till ett programobjekt, och ett programobjekt kan refereras till av flera tjänsthuvudnamn i olika kataloger.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Vad är programobjekt och var kommer de från?

Du kan hantera [programobjekt](app-objects-and-service-principals.md#application-object) i Azure Portal via [appregistreringar.](https://aka.ms/appregistrations) Programobjekt beskriver programmet i Azure AD och kan betraktas som definitionen av programmet, så att tjänsten kan veta hur token ska utfärdas till programmet baserat på dess inställningar. Programobjektet finns bara i dess hemkatalog, även om det är ett program för flera innehavare som stöder tjänstens huvudnamn i andra kataloger. Programobjektet kan innehålla något av följande (samt ytterligare information som inte nämns här):

* Namn, logotyp och utgivare
* Omdirigerings-URI:er
* Hemligheter (symmetriska och/eller asymmetriska nycklar som används för att autentisera programmet)
* API-beroenden (OAuth)
* Publicerade API:er/resurser/omfång (OAuth)
* Approller (RBAC)
* Metadata och konfiguration för enkel inloggning
* Metadata och konfiguration för användareablering
* Proxymetadata och konfiguration

Programobjekt kan skapas via flera vägar, inklusive:

* Programregistreringar i Azure Portal
* Skapa ett nytt program med hjälp Visual Studio konfigurera det för att använda Azure AD-autentisering
* När en administratör lägger till ett program från appgalleriet (som även skapar ett huvudnamn för tjänsten)
* Använda API Microsoft Graph eller PowerShell för att skapa ett nytt program
* Många andra, inklusive olika utvecklarupplevelser i Azure och API Explorer-upplevelser i olika utvecklarcenter

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Vad är tjänstens huvudnamn och var kommer de från?

Du kan hantera [tjänstens huvudnamn](app-objects-and-service-principals.md#service-principal-object) i Azure Portal via [enterprise-programupplevelsen.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Tjänstens huvudnamn styr ett program som ansluter till Azure AD och kan betraktas som instansen av programmet i din katalog. För ett visst program kan det ha högst ett programobjekt (som är registrerat i en "hemkatalog") och ett eller flera objekt för tjänstens huvudnamn som representerar instanser av programmet i varje katalog där det fungerar. 

Tjänstens huvudnamn kan innehålla:

* En referens tillbaka till ett programobjekt via egenskapen program-ID
* Poster över tilldelningar av lokala användare och gruppprogramroller
* Poster över lokala användar- och administratörsbehörigheter som beviljats för programmet
  * Till exempel: behörighet för programmet att komma åt en viss användares e-post
* Poster över lokala principer, inklusive princip för villkorsstyrd åtkomst
* Poster över alternativa lokala inställningar för ett program
  * Anspråksomvandlingsregler
  * Attributmappningar (användareablering)
  * Katalogspecifika approller (om programmet stöder anpassade roller)
  * Katalogspecifikt namn eller logotyp

Precis som programobjekt kan tjänstens huvudnamn också skapas via flera vägar, inklusive:

* När användare loggar in på ett program från tredje part som är integrerat med Azure AD
  * Under inloggningen uppmanas användarna att ge programmet behörighet att komma åt sin profil och andra behörigheter. Den första personen som ger medgivande gör att ett huvudnamn för tjänsten som representerar programmet läggs till i katalogen.
* När användare loggar in på Microsoft onlinetjänster som [Microsoft 365](https://products.office.com/)
  * När du prenumererar på Microsoft 365 eller påbörjar en utvärderingsversion skapas ett eller flera huvudnamn för tjänsten i katalogen som representerar de olika tjänster som används för att leverera alla funktioner som är associerade med Microsoft 365.
  * Vissa Microsoft 365 tjänster som SharePoint skapar tjänsthuvudnamn regelbundet för att tillåta säker kommunikation mellan komponenter, inklusive arbetsflöden.
* När en administratör lägger till ett program från appgalleriet (detta skapar även ett underliggande appobjekt)
* Lägga till ett program för att använda [Azure AD-Programproxy](../manage-apps/application-proxy.md)
* Ansluta ett program för enkel inloggning med SAML eller enkel inloggning med lösenord (SSO)
* Programmatiskt via Microsoft Graph API eller PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hur är programobjekt och tjänstens huvudnamn relaterade till varandra?

Ett program har ett programobjekt i sin hemkatalog som refereras av en eller flera tjänsthuvudnamn i var och en av de kataloger där det fungerar (inklusive programmets hemkatalog).

![Visar relationen mellan appobjekt och tjänstens huvudnamn][apps_service_principals_directory]

I diagrammet ovan underhåller Microsoft två kataloger internt (visas till vänster) som används för att publicera program:

* En för Microsoft Apps (Microsoft-tjänster katalog)
* Ett för förintegrerade program från tredje part (appgallerikatalog)

Utgivare/leverantörer av program som integrerar med Azure AD måste ha en publiceringskatalog (visas till höger som "Vissa SaaS-kataloger").

Program som du lägger till själv **(visas som App (din)** i diagrammet) är:

* Appar som du har utvecklat (integrerade med Azure AD)
* Appar som du har anslutit för enkel inloggning
* Appar som du har publicerat med Azure AD-programproxyn

### <a name="notes-and-exceptions"></a>Kommentarer och undantag

* Alla tjänsthuvudnamn pekar inte tillbaka till ett programobjekt. När Azure AD ursprungligen skapades var de tjänster som tillhandahölls till program mer begränsade och tjänstens huvudnamn var tillräckligt för att upprätta en programidentitet. Det ursprungliga tjänsthuvudkontot var närmare det ursprungliga Windows Server Active Directory tjänstkontot. Därför är det fortfarande möjligt att skapa tjänstens huvudnamn via olika vägar, till exempel genom att använda Azure AD PowerShell, utan att först skapa ett programobjekt. Det Microsoft Graph API:et kräver ett programobjekt innan du skapar ett huvudnamn för tjänsten.
* För närvarande exponeras inte all information som beskrivs ovan programmässigt. Följande är endast tillgängliga i användargränssnittet:
  * Anspråksomvandlingsregler
  * Attributmappningar (användareablering)
* Mer detaljerad information om tjänstens huvudnamn och programobjekt finns i referensdokumentationen Microsoft Graph API:
  * [Program](/graph/api/resources/application)
  * [Tjänstens huvudnamn](/graph/api/resources/serviceprincipal)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Varför integreras program med Azure AD?

Program läggs till i Azure AD för att utnyttja en eller flera av de tjänster som det tillhandahåller, inklusive:

* Programautentisering och -auktorisering
* Användarautentisering och auktorisering
* Enkel inloggning med federation eller lösenord
* Användareablering och synkronisering
* Rollbaserad åtkomstkontroll – Använd katalogen för att definiera programroller för att utföra rollbaserade auktoriseringskontroller i ett program
* OAuth-auktoriseringstjänster – används av Microsoft 365 och andra Microsoft-program för att ge åtkomst till API:er/resurser
* Programpublicering och proxy – Publicera ett program från ett privat nätverk till Internet
* Attribut för katalogschematillägg [– Utöka schemat för tjänstens huvudnamn och användarobjekt för](active-directory-schema-extensions.md) att lagra ytterligare data i Azure AD 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Vem har behörighet att lägga till program i min Azure AD-instans?

Det finns vissa uppgifter som endast globala administratörer kan utföra (till exempel att lägga till program från appgalleriet och konfigurera ett program att använda Programproxy) som standard alla användare i din katalog har behörighet att registrera programobjekt som de utvecklar och diskretion över vilka program som de delar/ger åtkomst till organisationens data via medgivande. Om en person är den första användaren i din katalog som loggar in på ett program och beviljar medgivande, skapas ett huvudnamn för tjänsten i din klientorganisation. I annat fall lagras informationen om beviljande av medgivande på det befintliga tjänstens huvudnamn.

Att tillåta användare att registrera och godkänna program kan till en början låta angående, men tänk på följande:


* Program har kunnat utnyttja Windows Server Active Directory för användarautentisering i många år utan att programmet behöver registreras eller registreras i katalogen. Nu får organisationen bättre insyn i exakt hur många program som använder katalogen och i vilket syfte.
* Att delegera dessa ansvarsområden till användare negerar behovet av en administratörsdriven programregistrerings- och publiceringsprocess. Med Active Directory Federation Services (AD FS) (ADFS) var det troligt att en administratör var tvungen att lägga till ett program som en förlitande part åt sina utvecklare. Nu kan utvecklare självbetjäning.
* Det är bra att användare loggar in i program med sina organisationskonton i affärssyfte. Om de senare lämnar organisationen förlorar de automatiskt åtkomsten till sitt konto i det program som de använde.
* Det är bra att ha en post över vilka data som har delats med vilket program. Data är mer transporterbara än någonsin och det är bra att ha en tydlig post över vem som delat vilka data med vilka program.
* API-ägare som använder Azure AD för OAuth bestämmer exakt vilka behörigheter användarna kan bevilja till program och vilka behörigheter som en administratör måste godkänna. Endast administratörer kan godkänna större omfång och större behörigheter, medan användarmedgivande är begränsad till användarnas egna data och funktioner.
* När en användare lägger till eller tillåter att ett program får åtkomst till sina data kan händelsen granskas så att du kan visa granskningsrapporterna i Azure Portal för att avgöra hur ett program har lagts till i katalogen.

Om du fortfarande vill hindra användare i din katalog från att registrera program och logga in i program utan administratörsgodkännande finns det två inställningar som du kan ändra för att inaktivera dessa funktioner:

* Så här förhindrar du att användare godkänner program på egen hand:
  1. I Azure Portal går du till [avsnittet Användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) under Företagsprogram.
  2. Ändra **Användare kan samtycka till att appar får åtkomst till företagsdata för deras räkning** till **Nej.**
     
     > [!NOTE]
     > Om du bestämmer dig för att stänga av användarens medgivande måste en administratör godkänna alla nya program som en användare behöver använda.

* Så här förhindrar du att användare registrerar sina egna program:
  1. I Azure Portal går du till [avsnittet Användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) under Azure Active Directory
  2. Ändra **Användare kan registrera program** till **Nej.**

> [!NOTE]
> Microsoft använder standardkonfigurationen med användare som kan registrera program och godkänna program för sina egna räkning.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
