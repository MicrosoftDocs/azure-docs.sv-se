---
title: Använd Azure Active Directory för att autentisera hanterings lösningar för batch
description: Utforska med Azure Active Directory för att autentisera från program som använder .NET-biblioteket för batch Management.
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: ff49d5e88df7c56ed4dee0685f09e45eb372aa5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930221"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autentisera lösningar för batch-hantering med Active Directory

Program som anropar tjänsten Azure Batch hantering autentiseras med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Azure använder sig av Azure AD för att autentisera sina kunder, tjänst administratörer och organisations användare.

Batch Management .NET-biblioteket exponerar typer för att arbeta med batch-konton, konto nycklar, program och programpaket. Batch Management .NET-biblioteket är en Azure Resource Provider-klient och används tillsammans med [Azure Resource Manager](../azure-resource-manager/management/overview.md) för att hantera dessa resurser program mässigt. Azure AD krävs för att autentisera begär Anden som görs via en Azure Resource Provider-klient, inklusive batch Management .NET-biblioteket och via Azure Resource Manager.

I den här artikeln går vi igenom hur du använder Azure AD för att autentisera från program som använder .NET-biblioteket för batch Management. Vi visar hur du använder Azure AD för att autentisera en prenumerations administratör eller medadministratör med integrerad autentisering. Vi använder [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) -exempelprojektet, som finns på GitHub, för att gå igenom användningen av Azure AD med batch Management .net-biblioteket.

Mer information om hur du använder batch Management .NET-biblioteket och AccountManagement-exemplet finns i [Hantera batch-konton och kvoter med klient biblioteket för batch-hantering för .net](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrera ditt program med Azure AD

[Azure Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) ger ett programmerings gränssnitt till Azure AD för användning i dina program. Om du vill anropa ADAL från ditt program måste du registrera ditt program i en Azure AD-klient. När du registrerar ditt program anger du Azure AD med information om ditt program, inklusive ett namn för det i Azure AD-klienten. Azure AD tillhandahåller sedan ett program-ID som du använder för att associera ditt program med Azure AD vid körning. Mer information om program-ID finns [i program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Registrera AccountManagement-exempel programmet genom att följa stegen i avsnittet [lägga till ett program](../active-directory/develop/quickstart-register-app.md) i [integrera program med Azure Active Directory](../active-directory/develop/quickstart-register-app.md). Ange ett **internt klient program** för typen av program. Bransch standard OAuth 2,0-URI för **omdirigerings-URI: n** är `urn:ietf:wg:oauth:2.0:oob` . Du kan dock ange en giltig URI (till exempel `http://myaccountmanagementsample` ) för **omdirigerings-URI**, eftersom den inte behöver vara en riktig slut punkt.

![Lägga till ett program](./media/batch-aad-auth-management/app-registration-management-plane.png)

När du har slutfört registrerings processen ser du program-ID: t och det ID för objektet (tjänstens huvud namn) som anges för ditt program.

![Slutförd registrerings process](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Bevilja Azure Resource Manager API-åtkomst till ditt program

Därefter måste du delegera åtkomst till ditt program till Azure Resource Manager API. Azure AD-identifieraren för Resource Manager API är **Windows Azure Service Management-API**.

Följ dessa steg i Azure-portalen:

1. I det vänstra navigerings fönstret i Azure Portal väljer du **alla tjänster**, klickar på app- **registreringar** och klickar på **Lägg till**.
2. Sök efter namnet på ditt program i listan med app-registreringar:

    ![Sök efter ditt program namn](./media/batch-aad-auth-management/search-app-registration.png)

3. Visa bladet **Inställningar** . I avsnittet **API-åtkomst** väljer du **nödvändiga behörigheter**.
4. Klicka på **Lägg** till för att lägga till en ny nödvändig behörighet.
5. I steg 1 anger du **Windows Azure Service Management-API**, väljer detta API i listan över resultat och klickar på knappen **Välj** .
6. I steg 2 markerar du kryss rutan bredvid åtkomst till den **klassiska Azure-distributions modellen som organisations användare** och klickar på knappen **Välj** .
7. Klicka på knappen **OK** .

Bladet **nödvändiga behörigheter** visar nu att behörigheterna till ditt program beviljas både ADAL-och Resource Manager-API: er. Behörigheter beviljas till ADAL som standard när du först registrerar din app med Azure AD.

![Delegera behörigheter till Azure Resource Manager API](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-slutpunkter

Du behöver två välkända slut punkter för att kunna autentisera dina batch Management-lösningar med Azure AD.

- Den **vanliga slut punkten för Azure AD** tillhandahåller ett allmänt gränssnitt för insamling av autentiseringsuppgifter när en särskild klient inte tillhandahålls, som i fallet med integrerad autentisering:

    `https://login.microsoftonline.com/common`

- **Azure Resource Manager slut punkten** används för att hämta en token för att autentisera begär anden till batch Management-tjänsten:

    `https://management.core.windows.net/`

Exempel programmet AccountManagement definierar konstanter för dessa slut punkter. Lämna dessa konstanter oförändrade:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referera till ditt program-ID

Klient programmet använder program-ID: t (kallas även klient-ID) för att få åtkomst till Azure AD vid körning. När du har registrerat ditt program i Azure Portal uppdaterar du koden för att använda det program-ID som tillhandahålls av Azure AD för ditt registrerade program. I exempel programmet AccountManagement kopierar du ditt program-ID från Azure Portal till lämplig konstant:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Kopiera också omdirigerings-URI: n som du angav under registrerings processen. Den omdirigerings-URI som anges i din kod måste matcha den omdirigerings-URI som du angav när du registrerade programmet.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

När du har registrerat AccountManagement-exemplet i Azure AD-klienten och uppdaterat exempel käll koden med dina värden, är exemplet redo att autentisera med hjälp av Azure AD. När du kör exemplet försöker ADAL hämta en autentiseringstoken. I det här steget uppmanas du att ange dina Microsoft-autentiseringsuppgifter:

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

När du har angett dina autentiseringsuppgifter kan exempel programmet fortsätta att utfärda autentiserade begär anden till batch Management-tjänsten.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du kör [exempel programmet AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement)finns i [Hantera batch-konton och kvoter med klient biblioteket för batch-hantering för .net](batch-management-dotnet.md).
- Mer information om Azure AD finns i Azure Active Directory- [dokumentationen](../active-directory/index.yml).
- Djupgående exempel som visar hur du använder ADAL finns i [Azure kod exempel](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteket.
- Information om hur du autentiserar batch-tjänstprogram med hjälp av Azure AD finns i [autentisera batch service-lösningar med Active Directory](batch-aad-auth.md).
