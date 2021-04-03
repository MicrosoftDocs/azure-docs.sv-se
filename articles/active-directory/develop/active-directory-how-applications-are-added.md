---
title: Hur och varför program läggs till i Azure AD
titleSuffix: Microsoft identity platform
description: Vad innebär det att ett program läggs till i Azure AD och hur kommer de att få dit?
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
ms.openlocfilehash: 1f6fd0160988802e198ff9388cfeb3232b34b100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861127"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hur och varför program läggs till i Azure AD

Det finns två representationer av program i Azure AD:

* [Program objekt](app-objects-and-service-principals.md#application-object) – även om det finns [undantag](#notes-and-exceptions)kan program objekt anses vara definitionen av ett program.
* [Tjänstens huvud namn](app-objects-and-service-principals.md#service-principal-object) – kan betraktas som en instans av ett program. Tjänstens huvud namn refererar vanligt vis till ett program objekt, och ett program objekt kan refereras till av flera huvud namn för tjänsten mellan kataloger.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Vad är program objekt och var de kommer från?

Du kan hantera [program objekt](app-objects-and-service-principals.md#application-object) i Azure Portal via [appens registrerings](https://aka.ms/appregistrations) upplevelse. Program objekt beskriver programmet till Azure AD och kan betraktas som definition av programmet, vilket gör att tjänsten vet hur token ska utfärdas till programmet baserat på dess inställningar. Programobjektet finns bara i sin Hem Katalog, även om det är ett program med flera innehavare som stöder tjänstens huvud namn i andra kataloger. Programobjektet kan innehålla något av följande (och ytterligare information som inte nämns här):

* Namn, logo typ och utgivare
* Omdirigera URI: er
* Hemligheter (symmetriska och/eller asymmetriska nycklar som används för att autentisera programmet)
* API-beroenden (OAuth)
* Publicerade API: er/resurser/scope (OAuth)
* App-roller (RBAC)
* SSO-metadata och konfiguration
* Användar etablering av metadata och konfiguration
* Metadata och konfiguration för proxy

Program objekt kan skapas via flera vägar, inklusive:

* Program registreringar i Azure Portal
* Skapa ett nytt program med Visual Studio och konfigurera det för att använda Azure AD-autentisering
* När en administratör lägger till ett program från App-galleriet (som också kommer att skapa ett huvud namn för tjänsten)
* Använda Microsoft Graph API eller PowerShell för att skapa ett nytt program
* Många andra, inklusive olika utvecklares upplevelser i Azure och i API Explorer-upplevelser i olika utvecklings Center

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Vad är tjänstens huvud namn och var de kommer från?

Du kan hantera [tjänstens huvud namn](app-objects-and-service-principals.md#service-principal-object) i Azure Portal via [företags program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) upplevelsen. Tjänstens huvud namn är det som styr ett program som ansluter till Azure AD och som kan anses vara instansen av programmet i din katalog. För ett angivet program kan det ha högst ett program objekt (som är registrerat i en "Hem Katalog") och ett eller flera tjänst huvud objekt som representerar instanser av programmet i varje katalog där det fungerar. 

Tjänstens huvud namn kan innehålla:

* En referens tillbaka till ett program objekt via egenskapen program-ID
* Poster för program för lokala användare och grupper-roll tilldelningar
* Poster för lokala användar-och administratörs behörigheter som beviljats för programmet
  * Till exempel: behörighet för programmet för åtkomst till en viss användares e-post
* Poster för lokala principer inklusive princip för villkorlig åtkomst
* Poster över alternativa lokala inställningar för ett program
  * Omvandlings regler för anspråk
  * Mappningar av attribut (användar etablering)
  * Katalogaktiverade app-roller (om programmet stöder anpassade roller)
  * Katalog-/regionsspecifika namn eller logo typ

Precis som program objekt kan tjänstens huvud namn också skapas via flera vägar, inklusive:

* När användarna loggar in på ett program från tredje part som är integrerat med Azure AD
  * Under inloggningen uppmanas användarna att ge programmet behörighet att få åtkomst till sin profil och andra behörigheter. Den första personen som ger sitt medgivande orsakar ett huvud namn för tjänsten som representerar det program som ska läggas till i katalogen.
* När användarna loggar in på Microsoft onlinetjänster som [Microsoft 365](https://products.office.com/)
  * När du prenumererar på Microsoft 365 eller startar en utvärderings version skapas ett eller flera tjänst huvud namn i katalogen som representerar de olika tjänsterna som används för att leverera alla funktioner som är kopplade till Microsoft 365.
  * Vissa Microsoft 365 tjänster som SharePoint skapar tjänstens huvud namn kontinuerligt för att tillåta säker kommunikation mellan komponenter, inklusive arbets flöden.
* När en administratör lägger till ett program från App-galleriet (då skapas även ett underliggande app-objekt)
* Lägg till ett program för att använda [Azure-AD-programproxy](../manage-apps/application-proxy.md)
* Ansluta ett program för enkel inloggning med hjälp av SAML eller lösen ord enkel inloggning (SSO)
* Via programmering via Microsoft Graph API eller PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hur är program objekt och tjänstens huvud namn relaterade till varandra?

Ett program har ett program objekt i sin hem katalog som refereras till av ett eller flera tjänst huvud namn i var och en av de kataloger där det fungerar (inklusive programmets Hem Katalog).

![Visar förhållandet mellan app-objekt och tjänstens huvud namn][apps_service_principals_directory]

I föregående diagram behåller Microsoft två kataloger internt (visas till vänster) som används för att publicera program:

* En för Microsoft-appar (Microsoft Services-katalogen)
* En för Förintegrerad program vara från tredje part (App Gallery-katalog)

Program utgivare/-leverantörer som integreras med Azure AD måste ha en publicerings katalog (visas till höger som "vissa SaaS-kataloger").

Program som du lägger till själv (representeras som **app (dina)** i diagrammet) inkluderar:

* Appar som du har utvecklat (integrerat med Azure AD)
* Appar som du har anslutit för enkel inloggning
* Appar som du har publicerat med Azure AD Application Proxy

### <a name="notes-and-exceptions"></a>Anteckningar och undantag

* Alla tjänst huvud namn pekar inte tillbaka till ett program objekt. När Azure AD ursprungligen byggdes de tjänster som tillhandahölls för program var mer begränsad och tjänstens huvud namn var tillräckligt för att upprätta en program identitet. Det ursprungliga tjänst huvud namnet var närmare i form till Windows Server Active Directory-tjänstkontot. Därför är det fortfarande möjligt att skapa tjänstens huvud namn via olika vägar, till exempel att använda Azure AD PowerShell, utan att först skapa ett program objekt. Microsoft Graph-API: et kräver ett program objekt innan du skapar ett huvud namn för tjänsten.
* All information som beskrivs ovan exponeras för närvarande inte program mässigt. Följande är endast tillgängligt i användar gränssnittet:
  * Omvandlings regler för anspråk
  * Mappningar av attribut (användar etablering)
* Mer detaljerad information om tjänstens huvud namn och program objekt finns i referens dokumentationen för Microsoft Graph API:
  * [Program](/graph/api/resources/application)
  * [Tjänstens huvudnamn](/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Varför integreras program med Azure AD?

Program läggs till i Azure AD för att utnyttja en eller flera av de tjänster som tillhandahålls, inklusive:

* Programautentisering och-auktorisering
* Användarautentisering och auktorisering
* Enkel inloggning med federation eller lösen ord
* Användar etablering och synkronisering
* Rollbaserad åtkomst kontroll – Använd katalogen för att definiera program roller för att utföra rollbaserade verifierings kontroller i ett program
* OAuth-Authorization Services – används av Microsoft 365 och andra Microsoft-program för att ge åtkomst till API: er/resurser
* Program publicering och proxy – publicera ett program från ett privat nätverk till Internet
* Attribut för katalog schema tillägg – [utöka schemat för tjänstens huvud namn och användar objekt](active-directory-schema-extensions.md) för att lagra ytterligare data i Azure AD 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Vem har behörighet att lägga till program till min Azure AD-instans?

Det finns vissa aktiviteter som bara globala administratörer kan göra (till exempel att lägga till program från App-galleriet och konfigurera ett program för att använda programproxyn) som standard, men alla användare i katalogen har behörighet att registrera program objekt som de utvecklar och gör det möjligt att registrera program som de delar/ger åtkomst till sina organisations data via medgivande. Om en person är den första användaren i din katalog för att logga in i ett program och bevilja medgivande, skapas ett huvud namn för tjänsten i din klient organisation. Annars lagras medgivande beviljad information på det befintliga huvud namnet för tjänsten.

Att tillåta att användare registrerar och samtycker till program kan ha ett inlednings vis ljud om, men tänk på följande i åtanke:


* Program har kunnat utnyttja Windows Server Active Directory för användarautentisering under många år utan att programmet måste registreras eller registreras i katalogen. Organisationen har nu förbättrad insyn för exakt hur många program som använder katalogen och för vilket syfte.
* Genom att delegera dessa ansvars områden till användare kan du negera behovet av en administratörs driven registrerings-och publicerings process. Med Active Directory Federation Services (AD FS) (ADFS) var det troligt att en administratör skulle lägga till ett program som en förlitande part åt deras utvecklare. Nu kan utvecklare själv underhålla.
* Användare som loggar in på program som använder sina organisations konton i affärs syfte är en bra sak. Om de senare lämnar organisationen kommer de automatiskt att förlora åtkomsten till sitt konto i programmet som de använde.
* En förteckning över vilka data som delats med vilket program som är en bra sak. Data är mer spridda än någonsin och det är praktiskt att ha en tydlig post över vem som delat vilka data med vilka program.
* API-ägare som använder Azure AD för OAuth Bestäm exakt vilka behörigheter användare kan ge till gång till program och vilka behörigheter som krävs för att en administratör ska acceptera. Endast administratörer kan samtycka till större omfattningar och viktiga behörigheter, medan användar medgivande begränsas till användarnas egna data och funktioner.
* När en användare lägger till eller tillåter ett program att komma åt sina data, kan händelsen granskas så att du kan visa gransknings rapporterna i Azure Portal för att avgöra hur ett program har lagts till i katalogen.

Om du fortfarande vill förhindra att användare i din katalog registrerar program och loggar in på program utan Administratörs godkännande, finns det två inställningar som du kan ändra för att inaktivera dessa funktioner:

* För att förhindra att användare samtycker till program på egen ställe:
  1. I Azure Portal går du till avsnittet [användar inställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) under företags program.
  2. Ändra **användarnas tillstånd till appar som har åtkomst till företags data för deras räkning** till **Nej**.
     
     > [!NOTE]
     > Om du väljer att inaktivera användar medgivande krävs en administratör för att godkänna alla nya program som en användare behöver använda.

* Förhindra att användare registrerar sina egna program:
  1. I Azure Portal går du till avsnittet [användar inställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) under Azure Active Directory
  2. Ändra **användare kan registrera program** på **Nej**.

> [!NOTE]
> Microsoft använder sig av standard konfigurationen med användare som kan registrera program och samtycka till program för egen räkning.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
