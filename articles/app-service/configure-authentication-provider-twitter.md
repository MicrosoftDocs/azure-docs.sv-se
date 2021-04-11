---
title: Konfigurera Twitter-autentisering
description: Lär dig hur du konfigurerar Twitter-autentisering som identitets leverantör för din App Service eller Azure Functions app.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077983"
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

   > [!IMPORTANT]
   > Nyckeln för API-hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den med din app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Lägg till Twitter-information i ditt program

1. Logga in på [Azure Portal] och navigera till din app.
1. Välj **autentisering** i menyn till vänster. Klicka på **Lägg till identitets leverantör**.
1. Välj **Twitter** i list rutan identitets leverantör. Klistra in `API key` värdena och `API secret key` som du har fått tidigare.

    Hemligheten kommer att lagras som en plats-trög [program inställning](./configure-common.md#configure-app-settings) med namnet `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . Du kan uppdatera den inställningen senare om du vill använda [Key Vault referenser](./app-service-key-vault-references.md) om du vill hantera hemligheten i Azure Key Vault.

1. Om det här är den första identitetsprovider som kon figurer ATS för programmet visas även avsnittet **App Service autentiseringsinställningar** . Annars kan du gå vidare till nästa steg.
    
    De här alternativen avgör hur programmet svarar på oautentiserade begär Anden och standard valet dirigerar om alla begär Anden att logga in med den nya providern. Du kan ändra det här beteendet nu eller ändra inställningarna senare från skärmen main **Authentication** genom att välja **Redigera** bredvid **autentiseringsinställningar**. Mer information om de här alternativen finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Klicka på **Lägg till**.

Du är nu redo att använda Twitter för autentisering i din app. Providern visas på skärmen **autentisering** . Därifrån kan du redigera eller ta bort den här providerns konfiguration.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure-portalen]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
