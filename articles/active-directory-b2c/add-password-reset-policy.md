---
title: Konfigurera ett flöde för återställning av lösen ord i Azure AD B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ett flöde för återställning av lösen ord i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fa34e8ea71c307b75a3f345861f8ed99d131b3fd
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447936"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Konfigurera ett flöde för återställning av lösen ord i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Flöde för lösen ords återställning

Med [inloggnings-och inloggnings resan](add-sign-up-and-sign-in-policy.md) kan användarna återställa sina egna lösen ord med hjälp av länken **glömt lösen ordet?** . Flödet för lösen ords återställning omfattar följande steg:

1. På sidan för registrering och inloggning klickar användaren på länken **glömt ditt lösen ord?** . Azure AD B2C initierar flödet för lösen ords återställning. 
2. Användaren anger och verifierar sin e-postadress med en tids gräns för en tidpunkt.
3. Användaren kan sedan ange ett nytt lösen ord.

![Flöde för lösen ords återställning](./media/add-password-reset-policy/password-reset-flow.png)

Flödet för lösen ords återställning gäller lokala konton i Azure AD B2C som använder en [e-postadress](identity-provider-local.md#email-sign-in) eller ett [användar namn](identity-provider-local.md#username-sign-in) med ett lösen ord för inloggning.

En vanlig metod när du migrerar användare till Azure AD B2C med slumpmässiga lösen ord är att användarna ska verifiera sina e-postadresser och återställa sina lösen ord under den första inloggningen. Det är också vanligt att tvinga användaren att återställa sitt lösen ord när en administratör har ändrat sitt lösen ord. Se [Framtvinga lösen ords återställning](force-password-reset.md) för att aktivera den här funktionen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Lösen ords återställning via självbetjäning (rekommenderas)

Den nya upplevelsen för lösen ords återställning är nu en del av registrerings-eller inloggnings principen. När användaren väljer länken **glömt ditt lösen ord?** skickas den omedelbart till den glömt lösen ords upplevelsen. Ditt program behöver inte längre hantera AADB2C90118- [felkoden](#password-reset-policy-legacy)och du behöver inte en separat princip för lösen ords återställning.

::: zone pivot="b2c-user-flow"

Lösen ords återställning via självbetjäning kan konfigureras för **inloggning (rekommenderas)** eller **Registrera dig och logga in (rekommenderas)** användar flöden. Om du inte har ett sådant användar flöde skapar du en [Logga in och registrerar](add-sign-up-and-sign-in-policy.md) användar flödet. 

Aktivera självbetjäning för återställning av lösen ord för registrerings-eller inloggnings användar flödet:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj ett användar flöde för registrering eller inloggning (av typen **rekommenderas**) som du vill anpassa.
1. Under **Inställningar** i den vänstra menyn väljer du **Egenskaper**.
1. Under **lösen ords komplexitet** väljer du **självbetjäning för återställning av lösen ord**.
1. Välj **Spara**.
1. Välj **sidlayouter** under **Anpassa** på den vänstra menyn.
1. I **versionen för sidlayouten** väljer du **2.1.2-Current** eller ovan.
1. Välj **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

I följande avsnitt beskrivs hur du lägger till en självbetjänings lösen ords upplevelse i en anpassad princip. Exemplet baseras på de principfiler som ingår i [Start paketet för den anpassade principen](./custom-policy-get-started.md). 

> [!TIP]
> Du kan hitta ett fullständigt exempel på principen "Registrera dig eller logga in med lösen ords återställning" på [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Ange att en användare har valt glömt ditt lösen ord? Operationsföljdslänkkod

Om du vill ange den princip som användaren har valt länken **glömt ditt lösen ord?** , definierar du ett booleskt anspråk. Detta anspråk kommer att användas för att dirigera användar resan till den lösen ords profil som har glömt lösen ord. Detta anspråk kan också utfärdas till token så att programmet är medveten om att användaren har loggat in via det glömt lösen ords flödet.

Du deklarerar dina anspråk i [anspråks schemat](claimsschema.md). Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i **ClaimsSchema** -elementet. 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Uppgradera sidlayouten

Sidlayout [version](contentdefinitions.md#migrating-to-page-layout) `2.1.2` krävs för att aktivera flödet för lösen ords återställning via självbetjäning inom registreringen eller inloggnings resan.

1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ContentDefinitions](contentdefinitions.md) -elementet. Om elementet inte finns lägger du till det.
1. Ändra **DataURI** -elementet i **ContentDefinition** -elementet med ID- **API. signuporsignin** enligt bilden nedan.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

För att initiera `isForgotPassword` anspråket används en teknisk profil för anspråks omvandling. Den här tekniska profilen kommer att refereras senare. När det anropas anges värdet för `isForgotPassword` anspråket till `true` . Hitta `ClaimsProviders` elementet. Om elementet inte finns lägger du till det. Lägg sedan till följande anspråks leverantör:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

Den `SelfAsserted-LocalAccountSignin-Email` tekniska profilen kan användas för att `setting.forgotPasswordLinkOverride` använda anspråk för lösen ords återställning för att utföras i din användar resa. 

### <a name="add-the-password-reset-sub-journey"></a>Lägg till under resan för lösen ords återställning

Din resa inkluderar nu möjligheten för användaren att logga in, registrera sig och återställa lösen ord. För att bättre organisera användar resan kan en [under resa](subjourneys.md) användas för att hantera flödet för lösen ords återställning.

Under resan kommer att anropas från användar resan och utför de steg som krävs för att leverera lösen ords återställningen till användaren. Använd `Call` typen under resa så att när under resan har slutförts returneras kontrollen till Orchestration-steget som initierade under resan.

Hitta `SubJourneys` elementet. Om elementet inte finns lägger du till det efter `User Journeys` elementet. Lägg sedan till följande under resa:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Förbered din användar resa

Du måste ansluta det **glömt lösen ordet?** länkar till den glömt lösen ordet under resan. Det gör du genom att referera till under res sen för det glömt lösen ordet i **ClaimsProviderSelection** -elementet i **CombinedSignInAndSignUp** -steget.

Om du inte har en egen anpassad användar resa med ett **CombinedSignInAndSignUp** -steg kan du använda följande procedur för att duplicera en befintlig inloggnings-eller inloggnings användar resa. Annars fortsätter du till nästa avsnitt.

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
3. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Skapa ett underordnat element för **UserJourneys** -elementet genom att klistra in hela innehållet i elementet **UserJourney** som du kopierade i steg 2.
5. Byt namn på användar resans ID. Till exempel `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Anslut länken för glömt lösen ord till under resan för glömt lösen ord 

I din användar resa kan du representera det glömt lösen ordet under resan som en **ClaimsProviderSelection**. Om du lägger till det här elementet ansluts det **glömt lösen ordet?** länkar till den glömt lösen ordet under resan.

1. I användar resan, leta upp steg-elementet för Orchestration som innehåller `Type="CombinedSignInAndSignUp"` eller `Type="ClaimsProviderSelection"` . Det är vanligt vis det första Orchestration-steget. **ClaimsProviderSelections** -elementet innehåller en lista över identitets leverantörer som en användare kan använda för att logga in. Lägg till följande rad:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. I nästa Orchestration-steg lägger du till ett **ClaimsExchange** -element. Lägg till följande rad:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Lägg till följande Orchestration-steg mellan det aktuella steget och nästa steg. Det nya Orchestration-steget som du lägger till kontrollerar om det `isForgotPassword` finns ett anspråk. Om anspråket finns anropas [under resan för lösen ords återställning](#add-the-password-reset-sub-journey). 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. När du har lagt till det nya Orchestration-steget, numrera om stegen sekventiellt utan att hoppa över heltal från 1 till N.

### <a name="set-the-user-journey-to-be-executed"></a>Ange att användar resan ska köras

Nu när du har ändrat eller skapat en användar resa anger du i avsnittet **förlitande part** den resa som Azure AD B2C ska köras för den här anpassade principen. Leta upp elementet **DefaultUserJourney** i [RelyingParty](relyingparty.md) -elementet. Uppdatera  **DefaultUserJourney-ReferenceId** för att matcha ID för användar resan där du lade till **ClaimsProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Ange det glömt lösen ordet till din app

Ditt program kan behöva identifiera om användaren har loggat in via det glömt lösen ordet för användar flödet. **IsForgotPassword** -anspråket innehåller ett booleskt värde som indikerar detta, vilket kan utfärdas i den token som skickas till ditt program. Om det behövs lägger `isForgotPassword` du till de utgående anspråken i avsnittet **förlitande part** . Ditt program kan kontrol lera `isForgotPassword` kravet för att avgöra om användaren återställer lösen ordet.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Överför den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **överför anpassad princip** och överför sedan de två principfiler som du ändrade i följande ordning:
   1. Tilläggs principen, till exempel `TrustFrameworkExtensions.xml` .
   2. Principen för förlitande part, till exempel `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>Testa flödet för lösen ords återställning

1. Välj ett användar flöde för registrering eller inloggning (av typen rekommenderas) som du vill testa.
1. Välj **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj **Kör användar flöde**.
1. På sidan registrering eller inloggning väljer du **glömt lösen ordet?**.
1. Kontrol lera e-postadressen för det konto som du skapade tidigare och välj sedan **Fortsätt**.
1. Nu har du möjlighet att ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.
1. Kontrol lera anspråk svärdet för retur-token `isForgotPassword` . Om finns och är inställt på sant, betyder det att användaren har återställt lösen ordet.

## <a name="password-reset-policy-legacy"></a>Princip för lösen ords återställning (bakåtkompatibelt)

Om [lösen ords återställning med självbetjäning](#self-service-password-reset-recommended) inte är aktive rad utlöses inte automatiskt ett användar flöde för återställning av lösen ord när du klickar på den här länken. I stället returneras felkoden `AADB2C90118` till ditt program. Programmet måste hantera den här felkoden genom att initiera om autentiseringsinställningarna för att autentisera ett Azure AD B2C användar flöde för återställning av lösen ord.

I följande diagram:

1. Användaren klickar på Logga in i programmet. Appen initierar en auktoriseringsbegäran och tar användaren att Azure AD B2C för att slutföra inloggningen. Auktoriseringsbegäran anger namnet på registrerings-eller inloggnings principen, t. ex. **B2C_1_signup_signin**.
1. Användaren väljer länken **glömt ditt lösen ord?** . Azure AD B2C returnerar fel koden för AADB2C90118 till programmet.
1. Programmet hanterar felkoden och initierar en ny begäran om auktorisering. Auktoriseringsbegäran anger namnet på principen för lösen ords återställning, t. ex. **B2C_1_pwd_reset**.

![Användar flöde för återställning av äldre lösen ord](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Om du vill se ett exempel kan du titta på ett [enkelt ASP.net-exempel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)som visar hur användar flöden länkas.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Om du vill låta användare av ditt program återställa sitt lösen ord skapar du ett användar flöde för återställning av lösen ord.

1. I menyn Azure AD B2C klient översikt väljer du **användar flöden** och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **lösen ords återställning** användar flöde. 
1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**.
1. Ange ett **Namn** för användarflödet. Till exempel *passwordreset1*.
1. För **identitets leverantörer** aktiverar du **Återställ lösen ord med e-postadress**.
1. Under **program anspråk** väljer du **Visa fler** och väljer de anspråk som du vill ska returneras i de autentiseringstoken som skickas tillbaka till ditt program. Välj till exempel **Användarobjekt-id**.
1. Välj **OK**.
1. Välj **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* läggs automatiskt till i namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**, verifiera e-postadressen för det konto som du skapade tidigare och välj sedan **Fortsätt**.
1. Du kan nu ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Skapa en princip för återställning av lösenord

Anpassade principer är en uppsättning XML-filer som du överför till din Azure AD B2C-klient för att definiera användar resor. Vi tillhandahåller start paket med flera fördefinierade principer, inklusive: registrering och inloggning, lösen ords återställning och profil redigerings princip. Mer information finns i [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Nästa steg

Konfigurera en [tvingande lösen ords återställning](force-password-reset.md).
