---
title: 'Självstudie: Använd läget delad enhet med Microsoft Authentication Library (MSAL) för Android | Azure'
titleSuffix: Microsoft identity platform
description: I den här självstudien får du lära dig hur du förbereder en Android-enhet för att köras i delat läge och kör en app i den första raden.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6a173ed4dae9237d8aae991c943817ed70246eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649063"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Självstudie: Använd läget delad enhet i din Android-app

I den här självstudien får du lära sig att Android-utvecklare och Azure Active Directory (Azure AD)-klient administratörer lär sig om koden, Authenticator-appen och klient inställningarna som krävs för att aktivera läget delad enhet för en Android-app.

I de här självstudierna har du

> [!div class="checklist"]
> * Hämta ett kod exempel
> * Aktivera och identifiera läge för delad enhet
> * Identifiera ett läge för enkel eller flera konton
> * Identifiera en användar växel och aktivera global inloggning och utloggning
> * Konfigurera klient organisation och registrera programmet i Azure Portal
> * Konfigurera en Android-enhet i delat enhets läge
> * Kör exempelappen

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="developer-guide"></a>Utvecklarguide

I det här avsnittet av självstudien får du vägledning för utvecklare om hur du implementerar delad enhet i ett Android-program med hjälp av Microsoft Authentication Library (MSAL). I [själv studie kursen om MSAL Android](./tutorial-v2-android.md) kan du se hur du integrerar MSAL med din Android-app. Logga in en användare, anropa Microsoft Graph och logga ut en användare.

### <a name="download-the-sample"></a>Ladda ned exemplet

Klona [exempel programmet](https://github.com/Azure-Samples/ms-identity-android-java/) från GitHub. Exemplet har möjlighet att arbeta i [ett läge med enkel eller flera konton](./single-multi-account.md).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Lägg till MSAL SDK i din lokala maven-lagringsplats

Om du inte använder exempel appen lägger du till MSAL-biblioteket som ett beroende i filen build. gradle, så här:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Konfigurera appen så att den använder delad enhets läge

Mer information om hur du konfigurerar konfigurations filen hittar du i [konfigurations dokumentationen](./msal-configuration.md) .

Ange `"shared_device_mode_supported"` som `true` i konfigurations filen för MSAL.

Du kanske inte planerar att stödja läge för flera konton. Det kan bero på att du inte använder en delad enhet och att användaren kan logga in på appen med mer än ett konto på samma tid. I så fall, ange `"account_mode"` till `"SINGLE"` . Detta garanterar att din app alltid får `ISingleAccountPublicClientApplication` och fören klar din MSAL-integrering avsevärt. Standardvärdet `"account_mode"` är `"MULTIPLE"` , så det är viktigt att ändra det här värdet i konfigurations filen om du använder `"single account"` läge.

Här är ett exempel på den auth_config.jsfilen som ingår i **appens** > **primära** > **res** > **RAW** -katalog i exempel appen:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Identifiera läge för delad enhet

Med delad enhets läge kan du konfigurera Android-enheter så att de delas av flera anställda, samtidigt som du tillhandahåller Microsoft Identity-baserad hantering av enheten. Anställda kan logga in på sina enheter och få åtkomst till kund information snabbt. När de är klara med Skift eller uppgift kan de logga ut från alla appar på den delade enheten med en enkel klickning och enheten är omedelbart klar för nästa medarbetare att använda.

Använd `isSharedDevice()` för att avgöra om en app körs på en enhet som är i delat enhets läge. Din app kan använda den här flaggan för att avgöra om den ska ändra UX enligt detta.

Här är ett kodfragment som visar hur du kan använda `isSharedDevice()` .  Det är från- `SingleAccountModeFragment` klassen i exempel appen:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ? "Shared" : "Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Initiera PublicClientApplication-objektet

Om du ställer in `"account_mode":"SINGLE"` i MSAL konfigurations filen kan du på ett säkert sätt skicka det returnerade programobjektet som en `ISingleAccountPublicCLientApplication` .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Identifiera enskilt vs. läge för flera konton

Om du skriver en app som endast ska användas för första rad arbetare på en delad enhet rekommenderar vi att du skriver appen så att den endast stöder enanvändarläge. Detta inkluderar de flesta program som är aktiviteter fokuserade som medicinska register appar, faktura program och de flesta branschspecifika appar. Detta fören klar utvecklingen eftersom många funktioner i SDK inte behöver hanteras.

Om din app har stöd för flera konton och läget för delad enhet måste du utföra en typ kontroll och omvandla det till lämpligt gränssnitt enligt bilden nedan.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Hämta den inloggade användaren och ta reda på om en användare har ändrats på enheten

`loadAccount`Metoden hämtar kontot för den inloggade användaren. `onAccountChanged`Metoden avgör om den inloggade användaren har ändrat, och i så fall rensa:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Logga in en användare globalt

Följande tecken i en användare på enheten till andra appar som använder MSAL med Authenticator-appen:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Logga ut en användare globalt

Följande tar bort det inloggade kontot och rensar cachelagrade token från inte bara appen utan även från enheten som är i läget för delad enhet:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Administratörsguide

Följande steg beskriver hur du konfigurerar programmet i Azure Portal och placerar enheten i läget för delad enhet.

### <a name="register-your-application-in-azure-active-directory"></a>Registrera ditt program i Azure Active Directory

Registrera först ditt program i din organisations klient. Ange värdena nedan i auth_config.jsför för att programmet ska köras på rätt sätt.

Information om hur du gör detta finns i [Registrera ditt program](./tutorial-v2-android.md#register-your-application).

> [!NOTE]
> När du registrerar din app ska du använda snabb starts guiden till vänster och sedan välja **Android**. Detta leder till en sida där du uppmanas att ange **paket namn** och **signatur-hash** för din app. Detta är mycket viktigt för att se till att din app-konfiguration fungerar. Du får sedan ett konfigurations objekt som du kan använda för din app som du kommer att klippa ut och klistra in i auth_config.jspå filen.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Sidan konfigurera din Android-app i Azure Portal snabb start":::

Välj **gör den här ändringen för mig** och ange sedan de värden som snabb starten frågar efter i Azure Portal. När det är färdigt kommer vi att generera alla konfigurationsfiler som du behöver.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Konfigurera din projekt sida i Azure Portal snabb start":::

## <a name="set-up-a-tenant"></a>Konfigurera en klient

I test syfte ställer du in följande i din klient organisation: minst två anställda, en moln enhets administratör och en global administratör. I Azure Portal anger du moln enhets administratören genom att ändra organisatoriska roller. I Azure Portal kan du komma åt dina organisations roller genom att välja **Azure Active Directory**  >  **roller och administratörer**  >  **moln enhets administratör**. Lägg till användare som kan lagra en enhet i delat läge.

## <a name="set-up-an-android-device-in-shared-mode"></a>Konfigurera en Android-enhet i delat läge

### <a name="download-the-authenticator-app"></a>Hämta Authenticator-appen

Hämta Microsoft Authenticator-appen från Google Play-butiken. Om du redan har hämtat appen kontrollerar du att den är den senaste versionen.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Verifierings program inställningar & att registrera enheten i molnet

Starta Authenticator-appen och gå till huvud konto sidan. När du ser sidan **Lägg till konto** är du redo att göra enheten delad.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Skärmen Lägg till konto för autentiserare":::

Gå till fönstret **Inställningar** med hjälp av den högra meny raden. Välj **enhets registrering** under **arbets & skol konton**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Skärm för verifierings inställningar":::

När du klickar på den här knappen uppmanas du att ge åtkomst till enhets kontakter. Detta beror på Android: s konto integrering på enheten. Välj **Tillåt**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Skärmen Tillåt åtkomst bekräftelse för autentiseraren":::

Moln enhets administratören bör ange sin organisations e-postadress under **eller registrera sig som en delad enhet**. Klicka sedan på knappen **Registrera som delad enhet** och ange deras autentiseringsuppgifter.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Enhets registrerings skärmen i appen":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="App-skärm bild som visar Microsoft inloggnings sida":::

Enheten är nu i delat läge.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="App skärm som visar delat enhets läge aktiverat":::

 Alla inloggningar och inloggningar på enheten är globala, vilket innebär att de gäller för alla appar som är integrerade med MSAL och Microsoft Authenticator på enheten. Nu kan du distribuera program till enheten som använder funktioner för delad enhets läge.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Visa den delade enheten i Azure Portal

När du har satt en enhet i delat läge, blir den känd för din organisation och spåras i din organisations klient. Du kan visa dina delade enheter genom att titta på **kopplings typen** i Azure Active Directory bladet i Azure Portal.

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Fönstret alla enheter visas i Azure Portal":::

## <a name="running-the-sample-app"></a>Köra exempel appen

Exempel programmet är en enkel app som anropar Graph API i din organisation. Vid första körningen uppmanas du att godkänna att programmet är nytt för ditt medarbetar konto.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Skärmen information om program konfiguration":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att arbeta med Microsoft Authentication Library och Shared Device Mode på Android-enheter:

> [!div class="nextstepaction"]
> [Läge för delad enhet för Android-enheter](msal-android-shared-devices.md)
