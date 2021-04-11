---
title: Konfigurera Facebook-autentisering
description: Lär dig hur du konfigurerar Facebook-autentisering som identitets leverantör för din App Service-eller Azure Functions-app.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078017"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Konfigurera din App Service-eller Azure Functions-app för att använda Facebook-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions att använda Facebook som autentiseringsprovider.

För att kunna slutföra proceduren i den här artikeln behöver du ett Facebook-konto som har en verifierad e-postadress och ett mobiltelefon nummer. Om du vill skapa ett nytt Facebook-konto går du till [Facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrera ditt program med Facebook

1. Gå till [Facebook-utvecklarens] webbplats och logga in med dina Facebook-kontoautentiseringsuppgifter.

   Om du inte har ett Facebook för utvecklare-konto väljer du **Kom igång** och följer registrerings stegen.
1. Välj **Mina appar**  >  **Lägg till ny app**.
1. I fältet **visnings namn** :
   1. Ange ett unikt namn för din app.
   1. Ange din **kontakt-e-postadress**.
   1. Välj **skapa app-ID**.
   1. Slutför säkerhets kontrollen.

   Developer-instrumentpanelen för din nya Facebook-app öppnas.
1. Välj   >  **Facebook**  >  -kontrollpanel-inloggning **Konfigurera**  >  **webb**.
1. I det vänstra navigerings fältet under **Facebook-inloggning** väljer du **Inställningar**.
1. I fältet **giltiga OAuth-omdirigerings-URI** anger du `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Kom ihåg att ersätta `<app-name>` med namnet på din Azure App Service-app.
1. Välj **Spara ändringar**.
1. I det vänstra fönstret väljer du **Inställningar**  >  **Basic**. 
1. I fältet **app Secret** väljer du **Visa**. Kopiera värdena för **app-ID** och **app Secret**. Du kan använda dem senare för att konfigurera din App Service-app i Azure.

   > [!IMPORTANT]
   > Appens hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.
   >

1. Det Facebook-konto som du använde för att registrera programmet är en administratör för appen. I det här läget kan endast administratörer logga in till det här programmet.

   Om du vill autentisera andra Facebook-konton väljer du **granskning av program** och gör det möjligt för allmänheten att göra det möjligt för allmänheten att komma åt appen med hjälp av Facebook-autentisering. **\<your-app-name>**

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Lägga till Facebook-information i ditt program

1. Logga in på [Azure Portal] och navigera till din app.
1. Välj **autentisering** i menyn till vänster. Klicka på **Lägg till identitets leverantör**.
1. Välj **Facebook** i list rutan identitets leverantör. Klistra in i app-ID och appens hemliga värden som du har fått tidigare.

    Hemligheten kommer att lagras som en plats-trög [program inställning](./configure-common.md#configure-app-settings) med namnet `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . Du kan uppdatera den inställningen senare om du vill använda [Key Vault referenser](./app-service-key-vault-references.md) om du vill hantera hemligheten i Azure Key Vault.

1. Om det här är den första identitetsprovider som kon figurer ATS för programmet visas även avsnittet **App Service autentiseringsinställningar** . Annars kan du gå vidare till nästa steg.
    
    De här alternativen avgör hur programmet svarar på oautentiserade begär Anden och standard valet dirigerar om alla begär Anden att logga in med den nya providern. Du kan ändra det här beteendet nu eller ändra inställningarna senare från skärmen main **Authentication** genom att välja **Redigera** bredvid **autentiseringsinställningar**. Mer information om de här alternativen finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Valfritt Klicka på **Nästa: omfattningar** och Lägg till eventuella omfattningar som programmet behöver. De kommer att begäras vid inloggnings tiden för webbläsarbaserade flöden.
1. Klicka på **Lägg till**.

Du är nu redo att använda Facebook för autentisering i din app. Providern visas på skärmen **autentisering** . Därifrån kan du redigera eller ta bort den här providerns konfiguration.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portalen]: https://portal.azure.com/
