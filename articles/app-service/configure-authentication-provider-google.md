---
title: Konfigurera Google-autentisering
description: Lär dig hur du konfigurerar Google-autentisering som identitets leverantör för din App Service-eller Azure Functions-app.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078000"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Konfigurera din App Service-eller Azure Functions-app för att använda Google-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service eller Azure Functions för att använda Google som en autentiseringsprovider.

För att slutföra proceduren i det här avsnittet måste du ha ett Google-konto som har en verifierad e-postadress. Gå till [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registrera ditt program med Google

1. Följ Google-dokumentationen på [google Sign-In för appar på Server sidan](https://developers.google.com/identity/sign-in/web/server-side-flow) för att skapa ett klient-ID och klient hemlighet. Du behöver inte göra några kod ändringar. Använd bara följande information:
    - För **behöriga JavaScript-ursprung** använder `https://<app-name>.azurewebsites.net` du med namnet på din app i *\<app-name>* .
    - Använd för **auktoriserad omdirigerings-URI** `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Kopiera app-ID och appens hemliga värden.

    > [!IMPORTANT]
    > Appens hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Lägga till Google information till ditt program

1. Logga in på [Azure Portal] och navigera till din app.
1. Välj **autentisering** i menyn till vänster. Klicka på **Lägg till identitets leverantör**.
1. Välj **Google** i list rutan identitets leverantör. Klistra in i app-ID och appens hemliga värden som du har fått tidigare.

    Hemligheten kommer att lagras som en plats-trög [program inställning](./configure-common.md#configure-app-settings) med namnet `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . Du kan uppdatera den inställningen senare om du vill använda [Key Vault referenser](./app-service-key-vault-references.md) om du vill hantera hemligheten i Azure Key Vault.

1. Om det här är den första identitetsprovider som kon figurer ATS för programmet visas även avsnittet **App Service autentiseringsinställningar** . Annars kan du gå vidare till nästa steg.
    
    De här alternativen avgör hur programmet svarar på oautentiserade begär Anden och standard valet dirigerar om alla begär Anden att logga in med den nya providern. Du kan ändra det här beteendet nu eller ändra inställningarna senare från skärmen main **Authentication** genom att välja **Redigera** bredvid **autentiseringsinställningar**. Mer information om de här alternativen finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Valfritt Klicka på **Nästa: omfattningar** och Lägg till eventuella omfattningar som programmet behöver. De kommer att begäras vid inloggnings tiden för webbläsarbaserade flöden.
1. Klicka på **Lägg till**.

Du är nu redo att använda Google för autentisering i din app. Providern visas på skärmen **autentisering** . Därifrån kan du redigera eller ta bort den här providerns konfiguration.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portalen]: https://portal.azure.com/

