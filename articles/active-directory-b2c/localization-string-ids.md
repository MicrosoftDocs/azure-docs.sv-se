---
title: 'Lokaliserings Strängs-ID: n Azure Active Directory B2C | Microsoft Docs'
description: 'Ange ID: n för en innehålls definition med ID: t för API. signuporsignin i en anpassad princip i Azure Active Directory B2C.'
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b60b447d8302b89813ca462c3220603ef926eb26
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631191"
---
# <a name="localization-string-ids"></a>Sträng-ID för lokalisering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokaliserings** elementet gör att du kan stödja flera språk eller språk i principen för användar resan. Den här artikeln innehåller en lista över lokaliserings-ID: n som du kan använda i principen. För att bekanta dig med användar gränssnitts lokalisering, se [lokalisering](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Sid element för registrering eller inloggning

Följande ID: n används för en innehålls definition med ID `api.signuporsignin` och [självkontrollerad teknisk profil](self-asserted-technical-profile.md).

| ID | Standardvärde | Sidlayout version |
| -- | ------------- | ------ |
| **forgotpassword_link** | Glömt ditt lösenord? | `All` |
| **createaccount_intro** | Har du inte något konto? | `All` |
| **button_signin** | Logga in | `All` |
| **social_intro** | Logga in med ditt sociala konto | `All` |
| **remember_me** |Håll mig inloggad. | `All` |
| **unknown_error** | Vi har problem med att logga in dig. Försök igen senare. | `All` |
| **divider_title** | ELLER | `All` |
| **local_intro_email** | Logga in med ditt befintliga konto | `< 2.0.0` |
| **logonIdentifier_email** | E-postadress | `< 2.0.0` |
| **requiredField_email** | Ange din e-postadress | `< 2.0.0` |
| **invalid_email** | Ange en giltig e-postadress | `< 2.0.0` |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' \* +/=? ^ \_ \` { \| } ~-] + @ [a-za-Z0-9-] + (?: \\ . [ a-zA-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | Logga in med ditt användar namn | `< 2.0.0` |
| **logonIdentifier_username** | Användarnamn | `< 2.0.0` |
| **requiredField_username** | Ange ditt användar namn | `< 2.0.0` |
| **lösenord** | Lösenord | `< 2.0.0` |
| **requiredField_password** | Ange ditt lösen ord | `< 2.0.0` |
| **createaccount_link** | Registrera dig nu | `< 2.0.0` |
| **cancel_message** | Användaren har glömt sitt lösen ord | `< 2.0.0` |
| **invalid_password** | Det angivna lösen ordet har inte det förväntade formatet. | `< 2.0.0` |
| **createaccount_one_link** | Registrera dig nu | `>= 2.0.0` |
| **createaccount_two_links** | Registrera dig med {0} eller {1} | `>= 2.0.0` |
| **createaccount_three_links** | Registrera dig med {0} , {1} eller {2} | `>= 2.0.0` |
| **local_intro_generic** | Logga in med din {0} | `>= 2.1.0` |
| **requiredField_generic** | Ange din {0} | `>= 2.1.0` |
| **invalid_generic** | Ange ett giltigt {0} | `>= 2.1.1` |
| **post** | Logga in | `>= 2.1.1` |


> [!NOTE]
> * Plats hållare som {0} fylls i automatiskt med `DisplayName` värdet för `ClaimType` . 
> * Om du vill veta mer om lokalisering `ClaimType` , se [registrering eller inloggnings exempel](#signupsigninexample).

I följande exempel visas användningen av några av användar gränssnitts elementen på sidan för registrering eller inloggning:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="Skärm bild som visar registrerings-eller inloggnings sidans U X-element.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Registrerings-eller inloggnings identitets leverantörer

ID: t för identitets leverantörerna konfigureras i användar resan  **ClaimsExchange** -elementet. Vid lokalisering av identitets leverantörens rubrik är **ElementType** inställt på `ClaimsProvider` , medan **StringId** anges till ID för `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

I följande exempel lokaliserar Facebook Identity Provider till arabiska:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Fel meddelanden vid registrering eller inloggning

| ID | Standardvärde |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Lösen ordet är felaktigt. |
| **UserMessageIfPasswordExpired**| Ditt lösen ord har upphört att gälla.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Vi kan inte hitta ditt konto. |
| **UserMessageIfOldPasswordUsed** | Det verkar som om du använde ett gammalt lösen ord. |
| **DefaultMessage** | Ogiltigt användar namn eller lösen ord. |
| **UserMessageIfUserAccountDisabled** | Ditt konto har låsts. Kontakta supporten för att låsa upp den och försök sedan igen. |
| **UserMessageIfUserAccountLocked** | Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare. |
| **AADRequestsThrottled** | Det finns för många begär Anden just nu. Vänta en stund och försök igen. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Registrerings-eller inloggnings exempel

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Användar gränssnitts element för registrering och självkontrollerade sidor

Följande är ID: n för en innehålls definition med ID för `api.localaccountsignup` eller en innehålls definition som börjar med `api.selfasserted` , till exempel och och `api.selfasserted.profileupdate` `api.localaccountpasswordreset` [självkontrollerad teknisk profil](self-asserted-technical-profile.md).

| ID | Standardvärde |
| -- | ------------- |
| **ver_sent** | Verifierings koden har skickats till: |
| **ver_but_default** | Standardvärde |
| **cancel_message** | Användaren har avbrutit registrering av självkontrollerad information |
| **preloader_alt** | Vänta |
| **ver_but_send** | Skicka verifierings kod |
| **alert_yes** | Ja |
| **error_fieldIncorrect** | Ett eller flera fält har fyllts i felaktigt. Kontrol lera dina poster och försök igen. |
| **år** | Year |
| **verifying_blurb** | Vänta medan vi bearbetar din information. |
| **button_cancel** | Avbryt |
| **ver_fail_no_retry** | Du har gjort för många felaktiga försök. Försök igen senare. |
| **månaderna** | Månad |
| **ver_success_msg** | E-postadress verifierad. Nu kan du fortsätta. |
| **months** | Januari, februari, mars, april, maj, juni, juli, augusti, september, oktober, november, december |
| **ver_fail_server** | Vi har problem med att verifiera din e-postadress. Ange en giltig e-postadress och försök igen. |
| **error_requiredFieldMissing** | Ett obligatoriskt fält saknas. Fyll i alla obligatoriska fält och försök igen. |
| **initial_intro** | Ange följande information. |
| **ver_but_resend** | Skicka ny kod |
| **button_continue** | Skapa |
| **error_passwordEntryMismatch** | Fälten för lösen ords inmatning stämmer inte överens. Ange samma lösen ord i båda fälten och försök igen. |
| **ver_incorrect_format** | Felaktigt format. |
| **ver_but_edit** | Ändra e-post |
| **ver_but_verify** | Verifiera koden |
| **alert_no** | Inga |
| **ver_info_msg** | Verifierings koden har skickats till din inkorg. Kopiera den till inmatade rutan nedan. |
| **dagen** | Dag |
| **ver_fail_throttled** | Det finns för många begär Anden att verifiera den här e-postadressen. Vänta en stund och försök sedan igen. |
| **helplink_text** | Vad är detta? |
| **ver_fail_retry** | Den koden är felaktig. Försök igen. |
| **alert_title** | Avbryt inmatning av information |
| **required_field** | Den här informationen krävs. |
| **alert_message** | Är du säker på att du vill avbryta inmatningen av din information? |
| **ver_intro_msg** | Verifiering är nödvändig. Klicka på knappen Skicka. |
| **ver_input** | Verifieringskod |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Fel meddelanden för registrering och självkontrollerade sidor

| ID | Standardvärde |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Det finns redan en användare med det angivna ID: t. Välj en annan. |
| **UserMessageIfClaimNotVerified** | Anspråk har inte verifierats: {0} |
| **UserMessageIfIncorrectPattern** | Felaktigt mönster för: {0} |
| **UserMessageIfMissingRequiredElement** | Element som krävs saknas: {0} |
| **UserMessageIfValidationError** | Fel vid verifiering av: {0} |
| **UserMessageIfInvalidInput** | {0} innehåller ogiltiga indatatyper. |
| **ServiceThrottled** | Det finns för många begär Anden just nu. Vänta en stund och försök igen. |

I följande exempel visas användningen av några av användar gränssnitts elementen på registrerings sidan:

![Registrerings sida med dess GRÄNSSNITTs element namn märkta](./media/localization-string-ids/localization-sign-up.png)

I följande exempel visas användningen av några av användar gränssnitts elementen på registrerings sidan när användaren klickar på knappen Skicka verifierings kod:

![Registrerings sidan för e-postverifiering av sidan UX-element](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Exempel på registrering och självkontrollerade sidor

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Användar gränssnitts element för sidan med telefon faktorns autentisering

Följande är ID: n för en innehålls definition med ID `api.phonefactor` och [teknisk profil för telefon faktorn](phone-factor-technical-profile.md).

| ID | Standardvärde |
| -- | ------------- |
| **button_verify** | Ring mig |
| **country_code_label** | Landskod |
| **cancel_message** | Användaren har avbrutit Multi-Factor Authentication |
| **text_button_send_second_code** | Skicka en ny kod |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Vi har följande nummer för att registrera dig. Vi kan skicka en kod via SMS eller telefon för att autentisera dig. |
| **intro_mixed_p** | Vi har följande nummer för att registrera dig. Välj ett nummer som vi kan ringa eller skicka en kod via SMS för att autentisera dig. |
| **button_verify_code** | Verifiera kod |
| **requiredField_code** | Ange den verifierings kod du fick |
| **invalid_code** | Ange den 6-siffriga kod du fick |
| **button_cancel** | Avbryt |
| **local_number_input_placeholder_text** | Telefonnummer |
| **button_retry** | Försök igen |
| **alternative_text** | Jag har inte min telefon |
| **intro_phone_p** | Vi har följande nummer för att registrera dig. Välj ett tal som vi kan använda för att autentisera dig. |
| **intro_phone** | Vi har följande nummer för att registrera dig. Vi kommer att autentisera dig. |
| **enter_code_text_intro** | Ange din verifierings kod nedan eller  |
| **intro_entry_phone** | Ange ett nummer nedan så att vi kan autentisera dig. |
| **intro_entry_sms** | Ange ett nummer nedan så att vi kan skicka en kod via SMS för att autentisera dig. |
| **button_send_code** | Skicka kod |
| **invalid_number** | Ange ett giltigt telefonnummer |
| **intro_sms** | Vi har följande nummer för att registrera dig. Vi kommer att skicka en kod via SMS för att autentisera dig. |
| **intro_entry_mixed** | Ange ett nummer nedan som vi kan skicka en kod via SMS eller telefon för att autentisera dig. |
| **number_pattern** | ^\\+ (?: [0-9] [ \\ x20-]?) {6,14} [0-9] $ |
| **intro_sms_p** |Vi har följande nummer för att registrera dig. Välj ett nummer som vi kan skicka en kod via SMS för att autentisera dig. |
| **requiredField_countryCode** | Välj landskod |
| **requiredField_number** | Ange ditt telefonnummer |
| **country_code_input_placeholder_text** |Land eller region |
| **number_label** | Telefonnummer |
| **error_tryagain** | Det angivna telefonnumret är upptaget eller inte tillgängligt. Kontrol lera numret och försök igen. |
| **error_incorrect_code** | Den verifierings kod som du har angett matchar inte våra poster. Försök igen eller begär en ny kod. |
| **countryList** | Se [listan över länder](#phone-factor-authentication-page-example). |
| **error_448** | Det angivna telefonnumret kan inte kontaktas. |
| **error_449** | Användaren har överskridit antalet nya försök. |
| **verification_code_input_placeholder_text** | Verifieringskod |

I följande exempel visas användningen av några av användar gränssnitts elementen på sidan MFA-registrering:

![Registrerings GRÄNSSNITTs element för telefon faktor](./media/localization-string-ids/localization-mfa1.png)

I följande exempel visas användningen av vissa användar gränssnitts element på sidan MFA-verifiering:

![GRÄNSSNITT för verifiering av telefon faktor](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Exempel på sidan med telefon faktorns autentisering

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Verifiering Visa kontroll element för användar gränssnitt

Följande är ID: n för en [verifierings visnings kontroll](display-control-verification.md) med version 2.1.0 eller högre i [sidlayouten](page-layout.md) .

| ID | Standardvärde |
| -- | ------------- |
|intro_msg <sup>*</sup>| Verifiering är nödvändig. Klicka på knappen Skicka.|
|success_send_code_msg | Verifierings koden har skickats. Kopiera den till inmatade rutan nedan.|
|failure_send_code_msg | Vi har problem med att verifiera din e-postadress. Ange en giltig e-postadress och försök igen.|
|success_verify_code_msg | E-postadress verifierad. Nu kan du fortsätta.|
|failure_verify_code_msg | Vi har problem med att verifiera din e-postadress. Försök igen.|
|but_send_code | Skicka verifierings kod|
|but_verify_code | Verifiera koden|
|but_send_new_code | Skicka ny kod|
|but_change_claims | Ändra e-post|

Obs: `intro_msg` elementet är dolt och visas inte på den själv kontrollerade sidan. Om du vill göra det synligt använder du [HTML-customiztion](customize-ui-with-html.md) med sammanhängande formatmallar. Exempel:
    
```css
.verificationInfoText div{display: block!important}
```

### <a name="verification-display-control-example"></a>Exempel på verifiering av visnings kontroll

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Verifiering Visa kontroll element för användar gränssnitt (inaktuellt)

Följande är ID: n för en [verifierings visnings kontroll](display-control-verification.md) med [Page Layout version](page-layout.md) 2.0.0.

| ID | Standardvärde |
| -- | ------------- |
|verification_control_but_change_claims |Ändra |
|verification_control_fail_send_code |Det gick inte att skicka koden, försök igen senare. |
|verification_control_fail_verify_code |Det gick inte att verifiera koden, försök igen senare. |
|verification_control_but_send_code |Skicka kod |
|verification_control_but_send_new_code |Skicka ny kod |
|verification_control_but_verify_code |Verifiera kod |
|verification_control_code_sent| Verifierings koden har skickats. Kopiera den till inmatade rutan nedan. |

### <a name="verification-display-control-example-deprecated"></a>Exempel på verifiering av visnings kontroll (inaktuell)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Fel meddelanden för RESTful-tjänsten

Följande är ID: n för [RESTful-tjänstens tekniska profil](restful-technical-profile.md) fel meddelanden:

| ID | Standardvärde |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Det gick inte att upprätta en anslutning till RESTful-tjänstens slut punkt. RESTful tjänst-URL: {0} |
|UserMessageIfCircuitOpen | {0} RESTful tjänst-URL: {1} |
|UserMessageIfDnsResolutionFailed | Det gick inte att matcha värd namnet för RESTful-tjänstens slut punkt. RESTful tjänst-URL: {0} |
|UserMessageIfRequestTimeout | Det gick inte att upprätta en anslutning till RESTful-tjänstens slut punkt inom tids gränsen på {0} sekunder. RESTful tjänst-URL: {1} |


### <a name="restful-service-example"></a>RESTful service-exempel

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Fel meddelanden i Azure AD MFA

Följande är ID: n för ett fel meddelande i [Azure AD MFA Technical Profile](multi-factor-auth-technical-profile.md) :

| ID | Standardvärde |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Det går inte att skicka SMS till telefonen. försök med ett annat telefonnummer. |
|UserMessageIfInvalidFormat | Telefonnumret har ett ogiltigt format, korrigera det och försök igen.|
|UserMessageIfMaxAllowedCodeRetryReached | Fel kod angavs för många gånger, försök igen senare.|
|UserMessageIfServerError | Det går inte att använda MFA-tjänsten, försök igen senare.|
|UserMessageIfThrottled | Din begäran har begränsats, försök igen senare.|
|UserMessageIfWrongCodeEntered|Fel kod angavs, försök igen.|

### <a name="azure-ad-mfa-example"></a>Exempel på Azure AD MFA

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD-SSPR

Följande är ID: n för fel meddelanden i [Azure AD SSPR Technical Profile](aad-sspr-technical-profile.md) :

| ID | Standardvärde |
| -- | ------------- |
|UserMessageIfChallengeExpired | Koden har upphört att gälla.|
|UserMessageIfInternalError | Ett internt fel uppstod i e-posttjänsten, försök igen senare.|
|UserMessageIfThrottled | Du har skickat för många begär Anden, försök igen senare.|
|UserMessageIfVerificationFailedNoRetry | Du har överskridit det maximala antalet verifierings försök.|
|UserMessageIfVerificationFailedRetryAllowed | Verifieringen misslyckades, försök igen.|


### <a name="azure-ad-sspr-example"></a>Azure AD SSPR-exempel

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Ett lösen ord fel meddelanden

Följande är ID: n för ett [eng ång slö sen ord –](one-time-password-technical-profile.md) fel meddelanden i teknisk profil

| ID | Standardvärde |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Verifieringen av ett angivet lösen ord har överskridit maximalt antal försök |
|UserMessageIfSessionDoesNotExist |En session med lösen ords verifiering har upphört att gälla |
|UserMessageIfSessionConflict |En session med lösen ords verifiering har en konflikt |
|UserMessageIfInvalidCode |Ett lösen ord som angetts för verifiering är felaktigt |
|UserMessageIfVerificationFailedRetryAllowed |Den koden är felaktig. Försök igen. | 

### <a name="one-time-password-example"></a>Exempel på lösen ord för en tidpunkt

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Fel meddelanden för anspråks omvandlingar

Följande är ID: n för fel meddelanden för anspråks omvandlingar:

| ID | Anspråkstransformering | Standardvärde |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Jämförelse av värde för boolesk anspråk misslyckades för anspråks typen "inputClaim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Jämförelse av anspråks värde misslyckades: den angivna vänstra operanden är större än den högra operanden.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Jämförelse av anspråks värde misslyckades med StringComparison "OrdinalIgnoreCase".|

### <a name="claims-transformations-example"></a>Exempel på anspråk omvandlingar

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om lokaliserings exempel:

- [Språk anpassning med anpassad princip i Azure Active Directory B2C](language-customization.md)
- [Språk anpassning med användar flöden i Azure Active Directory B2C](language-customization.md)
