---
title: Språk anpassning i Azure Active Directory B2C
description: Lär dig mer om att anpassa språk upplevelsen i dina användar flöden i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686418"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Språk anpassning i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Med språk anpassning i Azure Active Directory B2C (Azure AD B2C) kan ditt användar flöde anpassa olika språk så att de passar dina kund behov. Microsoft tillhandahåller översättningarna för [36-språk](#supported-languages), men du kan också tillhandahålla egna översättningar för alla språk. Även om din upplevelse bara är avsedd för ett enda språk kan du anpassa valfri text på sidorna.

## <a name="how-language-customization-works"></a>Så här fungerar språk anpassning

Du kan använda språk anpassning för att välja vilka språk som ditt användar flöde är tillgängligt i. När funktionen är aktive rad kan du ange frågesträngparametern, `ui_locales` från ditt program. När du anropar Azure AD B2C översätts sidan till det språk som du har angett. Den här typen av konfiguration ger dig fullständig kontroll över språken i ditt användar flöde och ignorerar språk inställningarna i kundens webbläsare.

Du kanske inte behöver den kontroll nivån över vilka språk som din kund ser. Om du inte anger en `ui_locales` parameter, styrs kundens upplevelse av webbläsarens inställningar. Du kan fortfarande styra vilka språk som ditt användar flöde ska översättas till genom att lägga till det som ett språk som stöds. Om en kunds webbläsare är inställd på att visa ett språk som du inte vill ha stöd för, visas det språk som du valde som standard i en kultur som stöds i stället.

* **UI – lokala språk**: när du har aktiverat språk anpassning översätts ditt användar flöde till det språk som anges här.
* **Webbläsare-begärt språk**: om ingen `ui_locales` parameter har angetts översätts ditt användar flöde till det begärda språket, *om språket stöds*.
* **Standard språk för principen**: om webbläsaren inte anger något språk, eller om det anger ett språk som inte stöds, översätts användar flödet till standard språket för användar flödet.

> [!NOTE]
> Om du använder anpassade användarattribut måste du ange dina egna översättningar. Mer information finns i [Anpassa dina strängar](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

Lokalisering kräver tre steg: 

1. Konfigurera den explicita listan över språk som stöds
1. Tillhandahåll språkspecifika strängar och samlingar
1. Redigera [innehålls definitionen](contentdefinitions.md) för sidan. 

::: zone-end 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Stöd för begärda språk för ui_locales

Principer som skapades innan den allmänna tillgängligheten för språk anpassning måste aktivera den här funktionen först. Principer och användar flöden som har skapats efter att språk anpassning har Aktiver ATS som standard.

När du aktiverar språk anpassning för ett användar flöde kan du kontrol lera språket för användar flödet genom att lägga till `ui_locales` parametern.

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj **Aktivera språk anpassning**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Välj vilka språk i ditt användar flöde som är aktiverade

Aktivera en uppsättning språk som ditt användar flöde ska översättas till när den begärs av webbläsaren utan `ui_locales` parametern.

1. Se till att ditt användar flöde har aktiverat språk anpassning från föregående instruktioner.
1. På sidan **språk** för användar flödet väljer du ett språk som du vill stödja.
1. I fönstret Egenskaper ändrar du **aktiverat** till **Ja**.
1. Välj **Spara** överst i fönstret Egenskaper.

>[!NOTE]
>Om en `ui_locales` parameter inte anges översätts sidan till kundens webb läsar språk endast om den är aktive rad.
>

## <a name="customize-your-strings"></a>Anpassa dina strängar

Med språk anpassning kan du anpassa en sträng i ditt användar flöde.

1. Se till att ditt användar flöde har aktiverat språk anpassning från föregående instruktioner.
1. På sidan **språk** för användar flödet väljer du det språk som du vill anpassa.
1. Under **filer på sidan filnivå – resurser** väljer du den sida som du vill redigera.
1. Välj **Hämta standardvärden** (eller **Hämta åsidosättningar** om du tidigare har redigerat det här språket).

De här stegen ger dig en JSON-fil som du kan använda för att börja redigera dina strängar.

### <a name="change-any-string-on-the-page"></a>Ändra valfri sträng på sidan

1. Öppna JSON-filen som hämtats från tidigare instruktioner i en JSON-redigerare.
1. Hitta det element som du vill ändra. Du kan söka efter `StringId` den sträng som du söker eller leta efter det `Value` attribut som du vill ändra.
1. Uppdatera `Value` attributet med det du vill visa.
1. Ändra till för varje sträng som du vill ändra `Override` `true` .
1. Spara filen och överför ändringarna. (Du kan hitta överförings kontrollen på samma plats som du laddade ned JSON-filen.)

> [!IMPORTANT]
> Om du behöver åsidosätta en sträng, se till att ange `Override` värdet till `true` . Om värdet inte ändras ignoreras posten.

### <a name="change-extension-attributes"></a>Ändra attribut för tillägg

Om du vill ändra strängen för ett anpassat användarattribut, eller om du vill lägga till ett i JSON, är det i följande format:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Ersätt `<ExtensionAttribute>` med namnet på ditt anpassade användar-attribut.

Ersätt `<ExtensionAttributeValue>` med den nya strängen som ska visas.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Ange en lista med värden med hjälp av LocalizedCollections

Om du vill ange en uppsättning värden för svar måste du skapa ett- `LocalizedCollections` attribut. `LocalizedCollections` är en matris med `Name` och `Value` par. Ordningen för objekten visas i den ordning som de visas. Använd följande format för att lägga till `LocalizedCollections` :

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` är det användar-attribut som det här `LocalizedCollections` attributet är ett svar på.
* `Name` är värdet som visas för användaren.
* `Value` är vad som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Överför dina ändringar

1. När du har slutfört ändringarna i JSON-filen går du tillbaka till B2C-klienten.
1. Välj **användar flöden** och klicka på det användar flöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj det språk som du vill översätta till.
1. Välj den sida där du vill ge översättningar.
1. Välj mappikonen och välj den JSON-fil som ska överföras.

Ändringarna sparas automatiskt i ditt användar flöde.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Anpassa sid gränssnittet med hjälp av språk anpassning

Det finns två sätt att lokalisera ditt [HTML-innehåll](customize-ui-with-html.md). Ett sätt är att aktivera [språk anpassning](language-customization.md). Om du aktiverar den här funktionen kan Azure AD B2C vidarebefordra OpenID Connect-parametern `ui-locales` till din slut punkt. Din innehålls Server kan använda den här parametern för att tillhandahålla anpassade HTML-sidor som är språkspecifika.

Du kan också hämta innehåll från olika platser baserat på de nationella inställningar som används. I din CORS-aktiverade slut punkt kan du ställa in en mappstruktur som värd för innehåll för vissa språk. Du anropar rätt ett om du använder jokertecknet `{Culture:RFC5646}` . Anta till exempel att det här är din anpassade sid-URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Du kan läsa in sidan i `fr` . När sidan hämtar HTML-och CSS-innehåll, hämtas de från:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Lägg till anpassade språk

Du kan också lägga till språk som Microsoft för närvarande inte tillhandahåller översättningar för. Du måste ange översättningarna för alla strängar i användar flödet. Språk-och lands koder är begränsade till dem i ISO 639-1-standarden. Det nationella kod formatet ska vara "ISO_639-1_code"-"CountryCode", till exempel `en-GB` . Mer information om språkvariant-ID-format finns i https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. Välj **användar flöden** i Azure AD B2C klient.
2. Klicka på det användar flöde där du vill lägga till anpassade språk och klicka sedan på **språk**.
3. Välj **Lägg till anpassat språk** överst på sidan.
4. I kontext fönstret som öppnas identifierar du vilket språk som du tillhandahåller översättningar för genom att ange en giltig språkkod.
5. För varje sida kan du ladda ned en uppsättning åsidosättningar för engelska och arbeta med översättningarna.
6. När du är klar med JSON-filerna kan du ladda upp dem för varje sida.
7. Välj **Aktivera** och ditt användar flöde kan nu visa det här språket för dina användare.
8. Spara språket.

>[!IMPORTANT]
>Du måste antingen aktivera de anpassade språken eller överföra åsidosättningar för det innan du kan spara.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Konfigurera en lista över språk som stöds

Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till `Localization` elementet med de språk som stöds: engelska (standard) och spanska.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Ange språkspecifika etiketter

[LocalizedResources](localization.md#localizedresources) för `Localization` elementet innehåller listan över lokaliserade strängar. Elementet lokaliserade resurser har en identifierare som används för att identifiera lokaliserade resurser unikt. Den här identifierare används senare i [innehålls definitions](contentdefinitions.md) elementet.

Du konfigurerar lokaliserade resurs element för innehålls definitionen och valfritt språk som du vill stödja. Om du vill anpassa enhetliga registrerings-eller inloggnings sidor för engelska och spanska lägger du till följande `LocalizedResources` element efter slutet av `</SupportedLanguages>` elementet.

> [!NOTE]
> I följande exempel har vi lagt till pund `#` symbolen i början av varje rad, så att du enkelt kan hitta de lokaliserade etiketterna på skärmen.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Redigera innehålls definitionen med lokalisering

Klistra in hela innehållet i ContentDefinitions-elementet som du kopierade som ett underordnat objekt till BuildingBlocks-elementet.

I följande exempel läggs de engelska (en) och spanska (ES) anpassade strängarna till i registrerings-eller inloggnings sidan och på registrerings sidan för lokalt konto. **LocalizedResourcesReferenceId** för varje **LocalizedResourcesReference** är detsamma som deras nationella inställningar, men du kan använda valfri sträng som identifierare. För varje språk-och sid kombination pekar du på motsvarande **LocalizedResources** som du skapade tidigare.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Ladda upp och testa din uppdaterade anpassade princip

### <a name="upload-the-custom-policy"></a>Överför den anpassade principen

1. Spara tilläggs filen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **överför anpassad princip**.
1. Ladda upp tilläggs filen som du har ändrat tidigare.

### <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen med hjälp av **Kör nu**

1. Välj den princip som du överförde och välj sedan **Kör nu**.
1. Du bör kunna se den lokaliserade registrerings-eller inloggnings sidan.
1. Klicka på registrerings länken och du bör kunna se den lokaliserade anmälnings sidan.
1. Ändra webbläsarens standard språk till spanska. Du kan också lägga till frågesträngparametern `ui_locales` för begäran. Exempel: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Ytterligare information

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Sid gränssnitt anpassnings etiketter som åsidosättningar

När du aktiverar språk anpassning behålls dina tidigare redigeringar av etiketter med anpassning av sid gränssnitt i en JSON-fil för engelska (en). Du kan fortsätta att ändra dina etiketter och andra strängar genom att överföra språk resurser i språk anpassning.

::: zone-end

### <a name="up-to-date-translations"></a>Aktuella översättningar

Microsoft strävar efter att tillhandahålla de senaste översättningarna för din användning. Microsoft förbättrar kontinuerligt översättningarna och ser till att de är kompatibla. Microsoft kommer att identifiera buggar och ändringar i global terminologi och göra uppdateringar som fungerar sömlöst i ditt användar flöde.

### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk

Microsoft tillhandahåller för närvarande inte stöd för språk som skrivs från höger till vänster. Du kan göra detta med hjälp av anpassade språk och använda CSS för att ändra hur strängarna visas. Om du behöver den här funktionen kan du rösta på [Azure feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Översättningar av sociala identitets leverantörer

Microsoft tillhandahåller `ui_locales` OIDC-parametern för sociala inloggningar. Men vissa sociala identitets leverantörer, inklusive Facebook och Google, följer inte dem.

### <a name="browser-behavior"></a>Webb läsar beteende

Chrome och Firefox båda förfrågningarna om deras inställda språk. Om det är ett språk som stöds visas det före standardvärdet. Microsoft Edge begär för närvarande inte ett språk och går direkt till standard språket.

## <a name="supported-languages"></a>Språk som stöds

Azure AD B2C har stöd för följande språk. Användar flödes språk tillhandahålls av Azure AD B2C. Meddelande språken för Multi-Factor Authentication (MFA) tillhandahålls av [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Språk              | Språkkod | Användarflöden         | MFA-meddelanden  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabiska                | bakre            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Bulgariska             | Bulgarien            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Bangla                | bn            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Katalanska               | onlinecertifikatutfärdare            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Tjeckiska                 | östasiatisk            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Danska                | da            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Tyska                | de            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Grekiska                 | El            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Engelska               | en            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Spanska               | es            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Estniska              | ge            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Baskiska                | gemenskaps            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Finska               | fi            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Franska                | fr            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Galiciska              | huvud            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Hebreiska                | producenten            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Hej            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kroatiska              | tim            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Ungerska             | HU            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Indonesiska            | id            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Italienska               | it            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Japanska              | ja            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kazakiska                | kk            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kannada               | 5,0            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Koreanska                | Ko            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Litauiska            | lt            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Lettiska               | LV            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Marathi               | Mr            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Malajiska                 | millisekund            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Norska bokmål      | Anm            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Nederländska                 | nl            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Norska             | nej            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | PA            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Polska                | pl            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Portugisiska – Brasilien   | pt-br         | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Portugisiska – Portugal | pt-pt         | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Rumänska              | ro            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Ryska               | ru            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Slovakiska                | sk            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Slovenska             | SL            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Serbiska (kyrillisk)    | SR-cryl-CS    | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Serbiska (latinsk)       | SR-latn-CS    | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Svenska               | sv            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Tamilska                 | layout            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Telugu                | &            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![X som anger Nej.](./media/user-flow-language-customization/no.png) |
| Thailändska                  | i:te            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Turkiska               | TR            | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Ukrainska             | Storbritannien            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Vietnamesiska            | Vi            | ![X som anger Nej.](./media/user-flow-language-customization/no.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kinesiska (förenklad)  | zh-hans       | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |
| Kinesiska (traditionell) | zh-Hant       | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) | ![Grön bock markering.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Nästa steg

::: zone pivot="b2c-user-flow"

Mer information om hur du kan anpassa användar gränssnittet för dina program finns i [Anpassa användar gränssnittet för ditt program i Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Läs mer om [lokaliserings](localization.md) ELEMENTET i IEF-referensen.
- Se listan över [lokaliserings Strängs-ID: n](localization-string-ids.md) som finns i Azure AD B2C.

::: zone-end 
