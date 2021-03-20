---
title: Konfigurera Microsoft-autentisering
description: Lär dig hur du konfigurerar autentisering av Microsoft-konto som en identitets leverantör för din App Service-eller Azure Functions-app.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043252"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Konfigurera din App Service-eller Azure Functions-app för att använda inloggning med Microsoft-konto

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service eller Azure Functions att använda AAD för att stödja personliga Microsoft-konto inloggningar.

> [!NOTE]
> Både personliga Microsoft-konton och organisations konton använder AAD Identity Provider. För närvarande går det inte att konfigurera den här identitets leverantören som stöd för båda typerna av inloggnings program.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrera din app med Microsoft-konto

1. Gå till [**Appregistreringar**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Portal. Om det behövs loggar du in med din Microsoft-konto.
1. Välj **ny registrering** och ange sedan ett program namn.
1. Under **konto typer som stöds** väljer du **konton i valfri organisations katalog (alla Azure AD Directory-flera klienter) och personliga Microsoft-konton (t. ex. Skype, Xbox)**
1. I **omdirigerings-URI: er** väljer du **webb** och anger sedan `https://<app-domain-name>/.auth/login/aad/callback` . Ersätt *\<app-domain-name>* med domän namnet för din app.  Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Se till att använda HTTPS-schemat i URL: en.

1. Välj **Register** (Registrera).
1. Kopiera **program-ID: t (klient)**. Du behöver det senare.
1. I den vänstra rutan väljer du **certifikat & hemligheter**  >  **ny klient hemlighet**. Ange en beskrivning, Välj giltighets tid och välj **Lägg till**.
1. Kopiera värdet som visas på sidan **certifikat & hemligheter** . När du har lämnat sidan visas den inte igen.

    > [!IMPORTANT]
    > Klientens hemliga värde (lösen ord) är en viktig säkerhets autentiseringsuppgift. Dela inte lösen ordet med någon eller distribuera det i ett klient program.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Lägg till information om Microsoft-konto till ditt App Service program

1. Gå till ditt program i [Azure Portal].
1. Välj **Inställningar**  >  **autentisering/auktorisering** och se till att **App Service autentisering** är **aktiverat**.
1. Under **autentiseringsproviders** väljer du **Azure Active Directory**. Välj **Avancerat** under **hanterings läge**. Klistra in det program (klient)-ID och klient hemlighet som du fick tidigare. Används **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** för fältet **Issuer URL** .
1. Välj **OK**.

   App Service tillhandahåller autentisering, men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din app-kod.

1. Valfritt Om du vill begränsa åtkomsten till Microsoft-konto användare anger du **åtgärd som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**. När du ställer in den här funktionen kräver appen att alla begär Anden ska autentiseras. Det omdirigerar också alla oautentiserade begär Anden om att använda AAD för autentisering. Observera att eftersom du har konfigurerat din **utfärdar-URL** till att använda Microsoft-kontots klient organisation, autentiseras bara personliga konton.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program **tillåts anonyma begär Anden (ingen åtgärd)** , så att appen manuellt startar själva autentiseringen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Microsoft-kontot för autentisering i din app.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portalen]: https://portal.azure.com/
