---
title: 'Självstudie: autentisera användare i ett internt klient program'
titleSuffix: Azure AD B2C
description: Självstudiekurs som lär dig hur du använder Azure Active Directory B2C för att tillhandahålla användarinloggning i ett .NET-program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "78186207"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Självstudie: autentisera användare i en intern Skriv bords klient med hjälp av Azure Active Directory B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att logga in och registrera användare i en Windows Presentation Foundation (WPF) Desktop-program. Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till det interna klientprogrammet
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet.
- Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med **.net Desktop development** **-och ASP.net-och webb utvecklings** arbets belastningar.

## <a name="add-the-native-client-application"></a>Lägga till det interna klientprogrammet

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registrera **program-ID: t (Client)** för användning i ett senare steg.

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. Exempel programmet WPF Desktop visar registrering, inloggning och kan anropa ett skyddat webb-API i Azure AD B2C. [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [bläddra på lagringsplatsen](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Så här uppdaterar du programmet så att det fungerar med din Azure AD B2C klient och anropar dess användar flöden i stället för de i standard-demo-klienten:

1. Öppna **Active-Directory-B2C-WPF-** lösningen ( `active-directory-b2c-wpf.sln` ) i Visual Studio.
2. Öppna filen *app. XAML. cs* i **Active-Directory-B2C-WPF-** projektet och leta upp följande variabel definitioner. Ersätt `{your-tenant-name}` med ditt Azure AD B2C klient namn och `{application-ID}` med det program-ID som du registrerade tidigare.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Uppdatera variablerna för princip namn med namnen på de användar flöden som du skapade som en del av förutsättningarna. Exempel:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Kör exemplet

Tryck på **F5** för att skapa och köra exemplet.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Välj **Logga** in för att registrera dig som en användare. Då används användarflödet **B2C_1_signupsignin1**.
2. Azure AD B2C visar en inloggnings sida med länken **Registrera dig nu** . Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu** .
3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ställ in ett lösenord. Ange värden för de begärda attributen.

    ![Registrerings sidan visas som en del av inloggnings-och registrerings arbets flödet](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Välj **skapa** för att skapa ett lokalt konto i Azure AD B2C klient organisationen.

Användaren kan nu använda sin e-postadress för att logga in och använda Skriv bords programmet. Efter en lyckad registrering eller inloggning visas information om token i det nedre fönstret i WPF-appen.

![Information om token visas i det nedre fönstret av WPF Desktop-programmet](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Om du väljer knappen **anropa API** visas ett **fel meddelande** . Felet uppstår eftersom programmet försöker komma åt ett API som skyddas av demo klienten, i det aktuella läget `fabrikamb2c.onmicrosoft.com` . Eftersom din åtkomsttoken bara är giltig för din Azure AD B2C klient, är API-anropet därför obehörigt.

Fortsätt till nästa självstudie för att registrera ett skyddat webb-API i din egen klient organisation och aktivera API-funktionen för **anrop** .

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till det interna klientprogrammet
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

För att aktivera funktionen **anropa API** -knappen ger du åtkomst till WPF Desktop-programmet åtkomst till ett webb-API som registrerats i din egen Azure AD B2C klient:

> [!div class="nextstepaction"]
> [Självstudie: bevilja åtkomst till ett Node.js webb-API från en desktop-app >](tutorial-desktop-app-webapi.md)
