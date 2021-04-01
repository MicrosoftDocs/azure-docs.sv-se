---
title: Konfigurera Twitter-autentisering
description: Lär dig hur du konfigurerar Twitter-autentisering som identitets leverantör för din App Service eller Azure Functions app.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80519914"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Konfigurera din App Service-eller Azure Functions-app för att använda Twitter-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions att använda Twitter som en autentiseringsprovider.

För att kunna slutföra proceduren i den här artikeln behöver du ett Twitter-konto som har en verifierad e-postadress och ett telefonnummer. Om du vill skapa ett nytt Twitter-konto går du till [Twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registrera ditt program med Twitter

1. Logga in på [Azure Portal] och gå till ditt program. Kopiera din **URL**. Du använder den för att konfigurera din Twitter-app.
1. Gå till webbplatsen för [Twitter-utvecklare] och logga in med dina Twitter-kontoautentiseringsuppgifter och välj **skapa en app**.
1. Ange **appens namn** och **program beskrivningen** för den nya appen. Klistra in programmets **URL** i fältet **webbplats-URL** . I avsnittet **callback-URL: er** anger du HTTPS-URL: en för din app service-app och lägger till sökvägen `/.auth/login/twitter/callback` . Till exempel `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Längst ned på sidan skriver du minst 100 tecken i **berätta för oss hur den här appen ska användas**. Välj sedan **skapa**. Klicka på **skapa** igen i popup-fönstret. Programinformationen visas.
1. Välj fliken **nycklar och åtkomst-token** .

   Anteckna dessa värden:
   - API-nyckel
   - Nyckel för API-hemlighet

   > [!NOTE]
   > Nyckeln för API-hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den med din app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Lägg till Twitter-information i ditt program

1. Gå till ditt program i [Azure Portal].
1. Välj **Inställningar**  >  **autentisering/auktorisering** och se till att **App Service autentisering** är **aktiverat**.
1. Välj **Twitter**.
1. Klistra in `API key` värdena och `API secret key` som du har fått tidigare.
1. Välj **OK**.

   ![Skärm bild av Twitter-inställningar för mobilapp][1]

   Som standard tillhandahåller App Service autentisering, men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din app-kod.

1. Valfritt Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiserats av Twitter, anger **du åtgärd som ska vidtas när förfrågan inte autentiseras** till **Twitter**. När du ställer in den här funktionen kräver appen att alla begär Anden ska autentiseras. Det omdirigerar också alla oautentiserade begär anden till Twitter för autentisering.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program **tillåts anonyma begär Anden (ingen åtgärd)** , så att appen manuellt startar själva autentiseringen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Twitter för autentisering i din app.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure-portalen]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
