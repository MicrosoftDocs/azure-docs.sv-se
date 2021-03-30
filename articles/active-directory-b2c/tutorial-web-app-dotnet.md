---
title: 'Självstudie: aktivera autentisering i ett webb program'
titleSuffix: Azure AD B2C
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ASP.NET-webbapp.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c3c63b6116e02e8a742b69e90c11e182d72ab2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953040"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Självstudie: aktivera autentisering i ett webb program med hjälp av Azure Active Directory B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att logga in och registrera användare i ett ASP.NET-webbprogram. Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> I den här självstudien används ett ASP.NET exempel-webbprogram. Andra exempel program (inklusive ASP.NET Core, Node.js, python och mer) finns i [Azure Active Directory B2C kod exempel](code-samples.md).

## <a name="prerequisites"></a>Förutsättningar

* [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet.
* Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med arbets belastningen **ASP.net och webb utveckling** .

## <a name="update-the-application-registration"></a>Uppdatera program registreringen

I självstudien som du avslutade som en del av förutsättningarna registrerade du ett webb program i Azure AD B2C. Om du vill aktivera kommunikation med exemplet i den här självstudien måste du lägga till en omdirigerings-URI och skapa en klient hemlighet (nyckel) för det registrerade programmet.

### <a name="add-a-redirect-uri-reply-url"></a>Lägg till en omdirigerings-URI (svars-URL)

Om du vill uppdatera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**, Välj fliken **ägda program** och välj sedan *webapp1* -programmet.
1. Under **webb** väljer du länken **Lägg till URI** , anger `https://localhost:44316` och väljer sedan **Spara**.
1. Välj **Översikt**.
1. Registrera **program-ID (Client)** för användning i ett senare steg när du konfigurerar webb programmet.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **program (bakåtkompatibelt)** och välj sedan *webapp1* -programmet.
1. Under **Svars-URL** lägger du till `https://localhost:44316`.
1. Välj **Spara**.
1. På sidan Egenskaper registrerar du program-ID för användning i ett senare steg när du konfigurerar webb programmet.

* * *

### <a name="create-a-client-secret"></a>Skapa en klient hemlighet

Skapa sedan en klient hemlighet för det registrerade webb programmet. Kod exemplet för webb program använder detta för att bevisa sin identitet när du begär token.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. Exemplet använder ASP.NET för att tillhandahålla en enkel att göra-lista. I exemplet används [Microsoft OWIN-mellanprogramkomponenter](/aspnet/aspnet/overview/owin-and-katana/). [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exemplet från GitHub. Se till att extrahera exempelfilen i en mapp där sökvägens totala teckenlängd är mindre än 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Följande två projekt finns i exempellösningen:

* **TaskWebApp** – Skapa och redigera en uppgiftslista. Exemplet använder användar flödet för **registrering eller inloggning** för att registrera dig och logga in användare.
* **TaskService** – Har stöd för att skapa, läsa, uppdatera och ta bort funktionen för uppgiftslistor. API:et skyddas av Azure AD B2C och anropas av TaskWebApp.

Du ändrar exemplet till att använda det program som är registrerat i din klientorganisation, vilket innefattar det program-ID och den nyckel som du tidigare registrerade. Du konfigurerar även de användarflöden som du skapade. Exemplet definierar konfigurations värden som inställningar i *Web.config* -filen.

Uppdatera inställningarna i Web.config-filen så att de fungerar med ditt användar flöde:

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.
1. I projektet **TaskWebApp** öppnar du **Web.config**-filen.
    1. Uppdatera värdet för `ida:Tenant` och `ida:AadInstance` med namnet på Azure AD B2C klienten som du skapade. Ersätt till exempel `fabrikamb2c` med `contoso` .
    1. Ersätt värdet för `ida:TenantId` med katalog-ID: t, som du hittar i egenskaperna för din Azure B2C-klient (i Azure Portal under **Azure Active Directory**  >  **Egenskaper**  >  **katalog-ID**).
    1. Ersätt värdet för `ida:ClientId` med det program-ID som du har spelat in.
    1. Ersätt värdet för `ida:ClientSecret` med den nyckel som du registrerade. Om klient hemligheten innehåller fördefinierade XML-entiteter, till exempel mindre än ( `<` ), större än ( `>` ), et-tecken ( `&` ) eller dubbelt citat `"` tecken (), måste du undanta dessa tecken genom XML-kodning av klient hemligheten innan du lägger till den i Web.config.
    1. Ersätt värdet för `ida:SignUpSignInPolicyId` med `b2c_1_signupsignin1` .
    1. Ersätt värdet för `ida:EditProfilePolicyId` med `b2c_1_profileediting1` .
    1. Ersätt värdet för `ida:ResetPasswordPolicyId` med `b2c_1_passwordreset1` .

## <a name="run-the-sample"></a>Kör exemplet

1. I Solution Explorer högerklickar du på projektet **TaskWebApp** och klickar sedan på **Set as StartUp Project** (Konfigurera som StartUp-projekt).
1. Tryck på **F5**. Standardwebbläsaren öppnas med den lokala webbplatsadressen `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Välj **Registrera dig/logga** in för att registrera dig som en användare av programmet. Användarflödet **b2c_1_signupsignin1** används.
1. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto än väljer du **Registrera dig nu**. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.
1. Använd en giltig e-postadress och verifiera med verifieringskoden. Ställ in ett lösenord. Ange värden för de begärda attributen.

    ![Registrerings sidan visas som en del av inloggnings-och registrerings arbets flödet](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Välj **skapa** för att skapa ett lokalt konto i Azure AD B2C klient organisationen.

Program användaren kan nu använda sin e-postadress för att logga in och använda webb programmet.

Men **för att göra-listan** fungerar inte förrän du har slutfört nästa självstudie i serien, [Självstudier: använda Azure AD B2C för att skydda ett ASP.net webb-API](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Gå vidare till nästa självstudie för att aktivera funktionen **att göra-listan** i webb programmet. I den registrerar du ett webb-API-program i din egen Azure AD B2C klient och ändrar sedan kod exemplet så att det använder klienten för API-autentisering.

> [!div class="nextstepaction"]
> [Självstudie: använda Azure Active Directory B2C för att skydda en ASP.NET Web API->](tutorial-web-api-dotnet.md)