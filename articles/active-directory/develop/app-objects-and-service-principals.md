---
title: Appar & huvudnamn för tjänsten i Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om relationen mellan program- och tjänstens huvudnamnsobjekt i Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: fc1b5356ab607ecb60a457a7295831958e6815e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727068"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objekt för program och tjänstens huvudnamn i Azure Active Directory

Den här artikeln beskriver programregistrering, programobjekt och tjänsthuvudnamn i Azure Active Directory: vad de är, hur de används och hur de är relaterade till varandra. Ett exempelscenario med flera klienter visas också för att illustrera relationen mellan ett programobjekt och motsvarande objekt för tjänstens huvudnamn.

## <a name="application-registration"></a>Programregistrering
För att kunna delegera identitets- och åtkomsthanteringsfunktioner till Azure AD måste ett program registreras med en Azure [AD-klientorganisation.](developer-glossary.md#tenant) När du registrerar ditt program med Azure AD skapar du en identitetskonfiguration för ditt program som gör att det kan integreras med Azure AD. När du registrerar en app i [Azure Portal][AZURE-Portal]väljer du om det är en enda klient (endast tillgänglig i din klient) eller flera klienter (tillgänglig i andra klienter) och kan även ange en omdirigerings-URI (där åtkomsttoken skickas till).

Stegvisa instruktioner om hur du registrerar en app finns i [snabbstarten för appregistrering.](quickstart-register-app.md)

När du har slutfört appregistreringen har du en globalt unik instans av appen (programobjektet [)](#application-object)som finns i din hemklient eller katalog.  Du har också ett globalt unikt ID för din app (appen eller klient-ID:t).  I portalen kan du sedan lägga till hemligheter eller certifikat och omfång för att få appen att fungera, anpassa appanpassad anpassning i inloggningsdialogrutan med mera.

Om du registrerar ett program i portalen skapas automatiskt ett programobjekt och ett objekt för tjänstens huvudnamn i din hemklientorganisation.  Om du registrerar/skapar ett program med hjälp Microsoft Graph API:er är det ett separat steg att skapa objektet för tjänstens huvudnamn.

## <a name="application-object"></a>Programobjekt
Ett Azure AD-program definieras av ett enda programobjekt, som finns i den Azure AD-klientorganisation där programmet registrerades (kallas programmets "startklient").  Ett programobjekt används som en mall eller skiss för att skapa ett eller flera objekt för tjänstens huvudnamn.  Ett huvudnamn för tjänsten skapas i varje klientorganisation där programmet används. Precis som en klass i objektorienterad programmering har programobjektet vissa statiska egenskaper som tillämpas på alla skapade tjänsthuvudnamn (eller programinstanser).

Programobjektet beskriver tre aspekter av ett program: hur tjänsten kan utfärda token för att få åtkomst till programmet, resurser som programmet kan behöva åtkomst till och de åtgärder som programmet kan vidta.

Bladet **Appregistreringar** i [Azure Portal][AZURE-Portal] används för att lista och hantera programobjekten i din hemklientorganisation.

![Appregistreringar blad](./media/app-objects-and-service-principals/app-registrations-blade.png)

Den Microsoft Graph [Application-entiteten][MS-Graph-App-Entity] definierar schemat för ett programobjekts egenskaper.

## <a name="service-principal-object"></a>Objekt för tjänstens huvudnamn
För att få åtkomst till resurser som skyddas av en Azure AD-klientorganisation måste entiteten som kräver åtkomst representeras av ett säkerhetsobjekt. Det här kravet gäller för både användare (användarens huvudnamn) och program (tjänstens huvudnamn). Säkerhetsobjekt definierar åtkomstprincipen och behörigheterna för användaren/programmet i Azure AD-klientorganisationen. Detta möjliggör grundläggande funktioner, till exempel autentisering av användare/program under inloggning och auktorisering under resursåtkomst.

Det finns tre typer av tjänstens huvudnamn: program, hanterad identitet och äldre.

Den första typen av tjänsthuvudnamn är den lokala representationen, eller programinstansen, av ett globalt programobjekt i en enda klient eller katalog. I det här fallet är tjänstens huvudnamn en konkret instans som skapats från programobjektet och ärver vissa egenskaper från det programobjektet. Ett huvudnamn för tjänsten skapas i varje klientorganisation där programmet används och refererar till det globalt unika appobjektet.  Objektet för tjänstens huvudnamn definierar vad appen faktiskt kan göra i den specifika klientorganisationen, vem som kan komma åt appen och vilka resurser som appen kan komma åt.

När ett program ges behörighet att komma åt resurser i en klientorganisation (vid registrering eller [medgivande)](developer-glossary.md#consent)skapas ett objekt för tjänstens huvudnamn. Du kan också skapa objekt för tjänstens huvudnamn [i en klientorganisation med hjälp Azure PowerShell,](howto-authenticate-service-principal-powershell.md) [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli) [Microsoft Graph,](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http) [Azure Portal][AZURE-Portal]och andra verktyg. När du använder portalen skapas ett huvudnamn för tjänsten automatiskt när du registrerar ett program.

Den andra typen av tjänsthuvudnamn används för att representera en [hanterad identitet](/azure/active-directory/managed-identities-azure-resources/overview). Hanterade identiteter eliminerar behovet av att utvecklare hanterar autentiseringsuppgifter. Hanterade identiteter tillhandahåller en identitet som program kan använda när de ansluter till resurser som stöder Azure AD-autentisering. När en hanterad identitet är aktiverad skapas ett huvudnamn för tjänsten som representerar den hanterade identiteten i din klientorganisation. Tjänstens huvudnamn som representerar hanterade identiteter kan beviljas åtkomst och behörigheter, men kan inte uppdateras eller ändras direkt.

Den tredje typen av tjänsthuvudnamn representerar en äldre app (en app som skapats innan appregistreringar introducerades eller skapades via äldre upplevelser). Ett äldre tjänsthuvudnamn kan ha autentiseringsuppgifter, namn på tjänstens huvudnamn, svars-URL:er och andra egenskaper som kan redigeras av en behörig användare, men som inte har någon associerad appregistrering. Tjänstens huvudnamn kan bara användas i klientorganisationen där det skapades.

Den Microsoft Graph [ServicePrincipal-entiteten][MS-Graph-Sp-Entity] definierar schemat för egenskaperna för ett tjänsthuvudnamnsobjekt.

Bladet **Företagsprogram** i portalen används för att visa och hantera tjänstens huvudnamn i en klientorganisation. Du kan se behörigheter för tjänstens huvudnamn, användarbehörigheter, vilka användare som har gjort det medgivandet, inloggningsinformation med mera.

![Bladet Företagsappar](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Relation mellan programobjekt och tjänstens huvudnamn

Programobjektet är den *globala representationen* av ditt program för användning i alla klienter och tjänstens huvudnamn är den lokala *representationen* för användning i en specifik klientorganisation.

Programobjektet fungerar som mallen som vanliga och standardinställda egenskaper *härleds* för användning när motsvarande objekt för tjänstens huvudnamn skapas. Ett programobjekt har därför en 1:1-relation med programprogrammet och en 1:många-relation med motsvarande objekt för tjänstens huvudnamn.

Ett tjänsthuvudnamn måste skapas i varje klientorganisation där programmet används, så att det kan upprätta en identitet för inloggning och/eller åtkomst till resurser som skyddas av klienten. Ett program för enskild klient har bara ett tjänsthuvudnamn (i dess startklientorganisation), som skapas och godkänns vid programregistrering. Ett program för flera innehavare har också ett tjänsthuvudnamn som skapats i varje klientorganisation där en användare från den klientorganisationen har samtyckt till dess användning.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Konsekvenser av att ändra och ta bort program
Ändringar som du gör i programobjektet visas också i dess tjänsthuvudnamnsobjekt endast i programmets hemklientorganisation (den klientorganisation där det registrerades). Det innebär att om du tar bort ett programobjekt tas även dess hemklienttjänstobjekt bort.  Om du återställer programobjektet återställs dock inte dess motsvarande huvudnamn för tjänsten. För program med flera klientorganisationer återspeglas inte ändringar i programobjektet i några konsumentklientorganisationers tjänsthuvudnamnsobjekt förrän åtkomsten tas bort via [programmets Åtkomstpanelen](https://myapps.microsoft.com) beviljas igen.

## <a name="example"></a>Exempel

Följande diagram illustrerar relationen mellan ett program-programobjekt och motsvarande objekt för tjänstens huvudnamn, i kontexten för ett exempelprogram för flera innehavare som **kallas HR-app**. Det finns tre Azure AD-klienter i det här exempelscenariot:

- **Adatum** – Den klientorganisation som används av företaget som utvecklade **HR-appen**
- **Contoso** – Den klientorganisation som används av Contoso-organisationen, som är konsument av **HR-appen**
- **Fabrikam** – Den klientorganisation som används av Fabrikam-organisationen, som också använder **HR-appen**

![Relation mellan appobjekt och tjänstens huvudnamnsobjekt](./media/app-objects-and-service-principals/application-objects-relationship.svg)

I det här exempelscenariot:

| Steg | Beskrivning |
|------|-------------|
| 1    | Är processen att skapa program- och tjänstens huvudnamnsobjekt i programmets hemklientorganisation. |
| 2    | När Contoso- och Fabrikam-administratörerna har gett sitt medgivande skapas ett objekt för tjänstens huvudnamn i företagets Azure AD-klientorganisation och tilldelas de behörigheter som administratören har beviljat. Observera också att HR-appen kan konfigureras/utformas för att tillåta användarnas medgivande för individuell användning. |
| 3    | Konsumentklienterna för HR-programmet (Contoso och Fabrikam) har sina egna objekt för tjänstens huvudnamn. Var och en representerar deras användning av en instans av programmet vid körning, som styrs av de behörigheter som godkänns av respektive administratör. |

## <a name="next-steps"></a>Nästa steg

- Du kan använda Microsoft Graph [Explorer för](https://developer.microsoft.com/graph/graph-explorer) att fråga både programmet och tjänstens huvudnamnsobjekt.
- Du kan komma åt ett programprogramobjekt med hjälp av Microsoft Graph-API:et, [Azure Portal:s][AZURE-Portal] programmanifestredigerare eller [Azure AD PowerShell-cmdlets](/powershell/azure/), som representeras av dess OData-programentitet . [][MS-Graph-App-Entity]
- Du kan komma åt ett programobjekt för tjänstens huvudnamn via Microsoft Graph API eller [Azure AD PowerShell-cmdlets](/powershell/azure/), som representeras av dess OData [ServicePrincipal-entitet][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
