---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: e8ef354480c69fa9b0b5407c88209b368485127d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729770"
---
Azure Communication Services- **gruppen som anropar ett hjältes exempel för Android** visar hur kommunikations tjänsterna som anropar Android SDK kan användas för att bygga en grupp samtals upplevelse som innehåller röst och video. I den här snabb starten får du lära dig hur du konfigurerar och kör exemplet. En översikt över exemplet finns för kontext.

## <a name="download-code"></a>Ladda ned kod

Hitta den färdiga koden för den här snabb starten på [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero).

## <a name="overview"></a>Översikt

Exemplet är ett inbyggt Android-program som använder Azure Communication Services Android-SDK: er för att bygga en anrops upplevelse som har funktioner för både röst-och video samtal. Programmet använder en komponent på Server sidan för att etablera åtkomsttoken som sedan används för att initiera Azure Communication Services SDK. Om du vill konfigurera den här komponenten på Server sidan kan du följa den [Betrodda tjänsten med Azure Functions](../../tutorials/trusted-service-tutorial.md) själv studie kursen.

Exemplet ser ut så här:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Skärm bild som visar landnings sidan för exempel programmet.":::

När du trycker på knappen "Starta nytt samtal" skapar Android-programmet ett nytt samtal och kopplar det. Programmet låter dig också ansluta till ett befintligt Azure Communication Services-samtal genom att ange det befintliga anropets ID.

När du har anslutit till ett samtal uppmanas du att ge programmet behörighet att få åtkomst till din kamera och mikrofon. Du uppmanas också att ange ett visnings namn.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Skärm bild som visar för anrops skärmen i exempel programmet.":::

När du har konfigurerat ditt visnings namn och dina enheter kan du ansluta till anropet. Du ser huvud anrops arbets ytan där den grundläggande anrops upplevelsen finns.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Skärm bild som visar exempel programmets huvud skärm.":::

Komponenter för den huvudsakliga uppringnings skärmen:

- **Medie Galleri**: huvud steget där deltagarna visas. Om en deltagare har sin kamera aktive rad visas deras video flöde här. Varje deltagare har en enskild panel som visar visnings namn och video ström (när det finns en). Galleriet har stöd för flera deltagare och uppdateras när deltagarna läggs till eller tas bort i anropet.
- **Åtgärds fält**: det är här som de primära anrops kontrollerna finns. Med de här kontrollerna kan du förvandla din video och mikrofon på/av, dela din skärm och lämna samtalet.

Nedan hittar du mer information om förutsättningar och steg för att konfigurera exemplet.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) körs på datorn
- En Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../quickstarts/create-communication-resource.md).
- En Azure-funktion som kör [autentiseringens slut punkt](../../tutorials/trusted-service-tutorial.md) för att hämta åtkomsttoken.

## <a name="running-sample-locally"></a>Kör exempel lokalt

Den grupp som anropar samplet kan köras lokalt med Android Studio. Utvecklare kan antingen använda sin fysiska enhet eller en emulator för att testa programmet.

### <a name="before-running-the-sample-for-the-first-time"></a>Innan du kör exemplet för första gången

1. Öppna Android Studio och välj `Open an Existing Project`
2. Öppna `AzureCalling` mappen i en Hämtad version av exemplet.
3. Expandera appar/till gångar att uppdatera `appSettings.properties` . Ställ in värdet för nyckeln `communicationTokenFetchUrl` som URL för din autentiserings slut punkt som en förutsättning.

### <a name="run-sample"></a>Kör exempel

Skapa och kör exemplet i Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>Valfritt Skydda en autentiserings slut punkt

I demonstrations syfte använder det här exemplet en offentligt tillgänglig slut punkt som standard för att hämta en Azure Communication Services-token. För produktions scenarier rekommenderar vi att du använder din egen säkrade slut punkt för att tillhandahålla dina egna tokens.

Med ytterligare konfiguration har det här exemplet stöd för anslutning till en skyddad **Azure Active Directory** -slutpunkt (Azure AD) så att användaren måste logga in för att appen ska kunna hämta en Azure Communication Services-token. Se stegen nedan:

1. Aktivera Azure Active Directory autentisering i din app.  
   - [Registrera din app under Azure Active Directory (med Android-plattformens inställningar)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Konfigurera din App Service-eller Azure Functions-app för att använda Azure AD-inloggning](../../../app-service/configure-authentication-provider-aad.md)

2. Gå till sidan registrerad app-översikt under Azure Active Directory app-registreringar. Anteckna `Package name` , `Signature hash` , `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure Active Directory konfiguration på Azure Portal.":::

3. Redigera `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` och Ställ in `isAADAuthEnabled` för att aktivera Azure Active Directory
4. Redigera `AndroidManifest.xml` och Ställ in `android:path` på hash för nyckel Arkivets signatur. Valfritt. Det aktuella värdet använder hash från den sammanställda fel sökningen. nyckel arkivet. Om olika nyckel lagrings typer används måste detta uppdateras.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Kopiera MSAL Android-konfiguration från Azure Portal och klistra in till `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . Inkludera "account_mode": "enstaka"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Redigera `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` och ange värdet för nyckeln `communicationTokenFetchUrl` som URL för din säkra slut punkt för autentisering.
7. Redigera `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` och Ställ in värdet för nyckeln `aadScopes` från `Azure Active Directory` `Expose an API` omfattningar

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
>[Hämta exemplet från GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Mer information finns i följande artiklar:

- Bekanta dig med [att använda den anropande SDK: n](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Läs mer om [hur du anropar Works](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Mer att läsa

- [Azure Communication-GitHub](https://github.com/Azure/communication) – Hitta fler exempel och information på den officiella GitHub-Sidan
- [Exempel](./../overview.md) – Hitta fler exempel och exempel på översikts sidan exempel.
- [Funktioner i Azure-kommunikation som anropar](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) -för att lära dig mer om att anropa Android SDK –[Azure-kommunikation Android-anrop SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
