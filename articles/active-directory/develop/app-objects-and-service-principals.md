---
title: Appar & tjänstens huvud namn i Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om relationen mellan program-och tjänst huvud objekt i Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/15/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 358e066631304e727d18d092bd4b9a5b2a0bb89a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199617"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objekt för program och tjänstens huvudnamn i Azure Active Directory

Den här artikeln beskriver program registrering, program objekt och tjänst huvud namn i Azure Active Directory: vad de är, hur de används och hur de är relaterade till varandra. Ett exempel scenario för flera klienter visas också för att illustrera förhållandet mellan ett programs program objekt och motsvarande tjänst huvud objekt.

## <a name="application-registration"></a>Programregistrering
För att kunna delegera identitets-och åtkomst hanterings funktioner till Azure AD måste ett program registreras med en Azure AD- [klient](developer-glossary.md#tenant). När du registrerar ditt program med Azure AD skapar du en identitets konfiguration för ditt program som gör det möjligt att integrera med Azure AD. När du registrerar en app i [Azure Portal][AZURE-Portal]väljer du om det är en enskild klient (endast tillgänglig i din klient) eller flera innehavare (tillgänglig i andra klienter) och om du vill kan du ange en omdirigerings-URI (där åtkomsttoken skickas till).

Steg-för-steg-instruktioner om hur du registrerar en app finns i snabb starten för [app Registration](quickstart-register-app.md).

När du har slutfört registreringen av appen har du en globalt unik instans av appen ( [programobjektet](#application-object)) som finns i din hem klient eller katalog.  Du har också ett globalt unikt ID för din app (appen eller klient-ID: t).  I portalen kan du sedan lägga till hemligheter eller certifikat och omfattningar för att din app ska fungera, anpassa appens anpassning i dialog rutan för inloggning med mera.

Om du registrerar ett program i portalen skapas ett program objekt och ett huvud namn för tjänsten automatiskt i din hem klient.  Om du registrerar/skapar ett program med hjälp av Microsoft Graph-API: erna, är det ett separat steg att skapa tjänstens huvud namns objekt.

## <a name="application-object"></a>Program objekt
Ett Azure AD-program definieras av sitt enda program objekt, som finns i Azure AD-klienten där programmet registrerades (kallas för programmets "hem").  Ett program objekt används som en mall eller skiss för att skapa ett eller flera huvud objekt för tjänsten.  Ett huvud namn för tjänsten skapas i varje klient organisation där programmet används. I likhet med en klass i objektorienterad programmering har programobjektet några statiska egenskaper som tillämpas på alla skapade tjänst huvud namn (eller program instanser).

Programobjektet beskriver tre aspekter av ett program: hur tjänsten kan utfärda token för att få åtkomst till programmet, resurser som programmet kan behöva åtkomst till och de åtgärder som programmet kan utföra.

Bladet **Appregistreringar** i [Azure Portal][AZURE-Portal] används för att visa och hantera program objekt i din hem klient organisation.

![Appregistreringar bladet](./media/app-objects-and-service-principals/app-registrations-blade.png)

[Entiteten Microsoft Graph program][MS-Graph-App-Entity] definierar schemat för ett program objekts egenskaper.

## <a name="service-principal-object"></a>Tjänstens huvud namns objekt
För att få åtkomst till resurser som skyddas av en Azure AD-klient måste den entitet som kräver åtkomst representeras av ett säkerhets objekt. Detta krav gäller både för både användare (användarens huvud namn) och program (tjänstens huvud namn). Säkerhets principen definierar åtkomst principen och behörigheter för användaren/programmet i Azure AD-klienten. Detta möjliggör kärn funktioner som autentisering av användaren/programmet vid inloggning och auktorisering under resurs åtkomst.

Ett huvud namn för tjänsten är den lokala representationen eller program instansen för ett globalt program objekt i en enda klient eller katalog. Ett huvud namn för tjänsten är en konkret instans som skapas från programobjektet och ärver vissa egenskaper från det program objektet. Ett huvud namn för tjänsten skapas i varje klient där programmet används och refererar till det globalt unika app-objektet.  Tjänstens huvud objekt definierar vad appen faktiskt kan göra i den angivna klienten, vem som har åtkomst till appen och vilka resurser appen kan komma åt.

När ett program har behörighet att komma åt resurser i en klient organisation (vid registrering eller [medgivande](developer-glossary.md#consent)) skapas ett huvud objekt för tjänsten. Du kan också skapa tjänst huvud objekt i en klient med hjälp av [Azure PowerShell](howto-authenticate-service-principal-powershell.md), [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli), [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http), [Azure Portal][AZURE-Portal]och andra verktyg. När du använder portalen skapas ett huvud namn för tjänsten automatiskt när du registrerar ett program.

Bladet **företags program** i portalen används för att visa och hantera tjänstens huvud namn i en klient organisation. Du kan se tjänstens huvud namn, användare som har skickat behörigheter, vilka användare som har gjort detta medgivande, logga in information med mera.

![Bladet företags program](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

[Entiteten Microsoft Graph ServicePrincipal][MS-Graph-Sp-Entity] definierar schemat för ett tjänst huvud objekts egenskaper.

## <a name="relationship-between-application-objects-and-service-principals"></a>Förhållandet mellan program objekt och tjänstens huvud namn

Programobjektet är den *globala* åter givningen av ditt program för användning över alla klienter, och tjänstens huvud namn är den *lokala* åter givning som används i en specifik klient.

Programobjektet fungerar som mallen som vanliga och standardinställda egenskaper *härleds* för användning när motsvarande objekt för tjänstens huvudnamn skapas. Ett program objekt har därför en 1:1-relation med program varan och en 1: många relation med motsvarande tjänst huvud objekt.

Ett huvud namn för tjänsten måste skapas i varje klient där programmet används, vilket gör det möjligt att upprätta en identitet för inloggning och/eller åtkomst till resurser som skyddas av klienten. Ett program för enskild klient har bara ett tjänsthuvudnamn (i dess startklientorganisation), som skapas och godkänns vid programregistrering. Ett program med flera klienter har också ett huvud namn för tjänsten som skapats i varje klient organisation där en användare från den klienten har samtyckt till användningen.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Konsekvenser av att ändra och ta bort program
Eventuella ändringar som du gör i program objekt avspeglas också i dess tjänst huvud objekt i programmets hem klient (den klient där det registrerades). Det innebär att om du tar bort ett program objekt tas även dess huvud objekt för hem klient tjänsten bort.  Att återställa programobjektet kommer dock inte att återställa motsvarande huvud namn för tjänsten. För program med flera klient organisationer avspeglas inte ändringar i programobjektet i någon konsument innehavares tjänst huvud objekt, tills åtkomsten tas bort via [program åtkomst panelen](https://myapps.microsoft.com) och beviljats igen.

## <a name="example"></a>Exempel

Följande diagram illustrerar förhållandet mellan ett programs program objekt och motsvarande tjänst huvud objekt, inom ramen för ett exempel på ett program för flera innehavare som kallas **HR-app**. Det finns tre Azure AD-klienter i det här exempel scenariot:

- **Adatum** – innehavare som används av företaget som utvecklade HR- **appen**
- **Contoso** – klienten som används av Contoso-organisationen, som är konsument för HR- **appen**
- **Fabrikam** – klienten som används av Fabrikam-organisationen, som också förbrukar **HR-appen**

![Förhållandet mellan app-objektet och tjänstens huvud namns objekt](./media/app-objects-and-service-principals/application-objects-relationship.svg)

I det här exempel scenariot:

| Steg | Beskrivning |
|------|-------------|
| 1    | Är processen för att skapa program-och tjänstens huvud namns objekt i programmets hem klient organisation. |
| 2    | När contoso och Fabrikam-administratörer har slutfört sitt medgivande skapas ett huvud namn för tjänsten i företagets Azure AD-klient och tilldelas de behörigheter som administratören har beviljat. Observera också att HR-appen kan konfigureras/utformas för att tillåta medgivande från användare för enskild användning. |
| 3    | Klient organisationerna för HR-programmet (contoso och Fabrikam) har sina egna tjänst huvud objekt. Varje representerar användningen av en instans av programmet vid körning, styrs av de behörigheter som har godkänts av respektive administratör. |

## <a name="next-steps"></a>Nästa steg

- Du kan använda [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) för att fråga både program-och tjänst huvud objekt.
- Du kan komma åt ett programs program objekt med hjälp av Microsoft Graph API, [Azure Portals][AZURE-Portal] program manifest redigeraren eller [Azure AD PowerShell-cmdletar](/powershell/azure/), som representeras av dess OData- [programentitet][MS-Graph-App-Entity].
- Du kan komma åt ett programs huvud objekt för tjänsten via Microsoft Graph API eller [Azure AD PowerShell-cmdletar](/powershell/azure/), som representeras av dess OData [ServicePrincipal-entitet][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
