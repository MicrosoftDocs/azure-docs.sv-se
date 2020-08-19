---
title: Anpassad e-postverifiering med MailJet
titleSuffix: Azure AD B2C
description: Lär dig att integrera med MailJet för att anpassa verifierings-e-postmeddelandet som skickas till kunderna när de registrerar sig för att använda dina Azure AD B2C-aktiverade program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29e82a67b85356cfc15e806bb331330b3f272a04
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584972"
---
# <a name="custom-email-verification-with-mailjet"></a>Anpassad e-postverifiering med MailJet

Använd anpassad e-post i Azure Active Directory B2C (Azure AD B2C) för att skicka anpassat e-postmeddelande till användare som registrerar sig för att använda dina program. Genom att använda [DisplayControls](display-controls.md) (för närvarande i för hands version) och e-postprovidern från tredje part kan du använda din egen e-postmall och *från:* adress och ämne, samt stöd för lokalisering och anpassad eng ång slö sen ord.

Anpassad e-postverifiering kräver att en e-postleverantör från tredje part används, t. ex. [MailJet](https://Mailjet.com), [SendGrid](custom-email.md)eller [Spark post](https://sparkpost.com), en anpassad REST API eller någon http-baserad e-postprovider (inklusive din egen). I den här artikeln beskrivs hur du konfigurerar en lösning som använder MailJet.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Skapa ett MailJet-konto

Om du inte redan har en, kan du börja med att konfigurera ett MailJet-konto (Azure-kunder kan låsa upp 6 000 e-postmeddelanden med en gräns på 200 e-postmeddelanden/dag). 

1. Följ installations anvisningarna på [skapa ett MailJet-konto](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/).
1. För att kunna skicka e-post, [Registrera och verifiera](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) din avsändar-e-postadress eller domän.
2. Gå till [sidan API-nyckel hantering](https://app.mailjet.com/account/api_keys). Registrera **API-nyckeln** och den **hemliga nyckeln** för användning i ett senare steg. Båda nycklarna genereras automatiskt när ditt konto skapas.  

## <a name="create-azure-ad-b2c-policy-key"></a>Skapa Azure AD B2C princip nyckel

Sedan lagrar du MailJet API-nyckeln i en Azure AD B2C princip nyckel för dina principer till referens.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj din Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan **Översikt** väljer du **ID för identitets miljö**.
1. Välj **princip nycklar**och välj sedan **Lägg till**.
1. För **alternativ**väljer du **manuell**.
1. Ange ett **namn** för princip nyckeln. Till exempel `MailjetApiKey`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
1. I **hemlighet**anger du din MailJet **API-nyckel** som du tidigare har spelat in.
1. För **nyckel användning**väljer du **signatur**.
1. Välj **Skapa**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ**väljer du **manuell**.
1. Ange ett **namn** för princip nyckeln. Till exempel `MailjetSecretKey`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
1. I **hemlighet**anger du din MailJet- **hemliga nyckel** som du tidigare har spelat in.
1. För **nyckel användning**väljer du **signatur**.
1. Välj **Skapa**.

## <a name="create-a-mailjet-template"></a>Skapa en MailJet-mall

När ett MailJet-konto har skapats och MailJet-API-nyckeln som lagras i en Azure AD B2C-princip nyckel skapar du en MailJet [dynamisk transaktionell mall](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Öppna sidan [transaktionella mallar](https://app.mailjet.com/templates/transactional) på MailJet-webbplatsen och välj **skapa en ny mall**.
1. Välj **genom att koda den i HTML**och välj sedan **kod från början**.
1. Ange ett unikt mallnamn som `Verification email` och välj sedan **skapa**.
1. I HTML-redigeraren klistrar du in följande HTML-mall eller använder din egen. `{{var:otp:""}}`Parametrarna och `{{var:email:""}}` kommer att ersättas dynamiskt med eng ång slö sen ord och användarens e-postadress.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Expandera **Redigera ämne** till vänster-överkant
    1. Ange ett standardvärde **för ämnet.** MailJet använder det här värdet när API: et inte innehåller en subject-parameter.
    1. Som **namn**anger du företagets namn.
    1. I **adress**väljer du din e-postadress
    1. Välj **Spara**.
1. Från höger överkant väljer du **spara & publicera**, och sedan **Ja, publicera ändringar**
1. Registrera **mall-ID** för mallen som du skapade för användning i ett senare steg. Du anger detta ID när du [lägger till anspråks omvandlingen](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Lägg till Azure AD B2C anspråks typer

I principen lägger du till följande anspråks typer i- `<ClaimsSchema>` elementet i `<BuildingBlocks>` .

Dessa anspråks typer är nödvändiga för att generera och verifiera e-postadressen med hjälp av en eng ång slö sen ord.

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>Mailjet request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Lägg till anspråks omvandlingen

Sedan behöver du en anspråks omvandling för att mata ut ett JSON-sträng anspråk som kommer att bli bröd texten i begäran som skickas till MailJet.

JSON-objektets struktur definieras av ID: n i punkt notation för indataparametrarna och TransformationClaimTypes för InputClaims. Siffror i punkt notation innebär att matriser. Värdena hämtas från InputClaims-värdena och värdet för indataparametrar. Mer information om JSON-anspråk omvandlingar finns i [JSON-anspråk omvandlingar](json-transformations.md).

Lägg till följande Claims-omvandling i- `<ClaimsTransformations>` elementet i `<BuildingBlocks>` . Gör följande uppdateringar i XML-omvandlingen för anspråk:

* Uppdatera `Messages.0.TemplateID` InputParameter-värdet med ID: t för den transaktions mal len MailJet som du skapade tidigare i [skapa en MailJet-mall](#create-a-mailjet-template).
* Uppdatera `Messages.0.From.Email` värdet för adress. Använd en giltig e-postadress för att förhindra att verifierings meddelandet markeras som skräp post.
* Uppdatera värdet för `Messages.0.Subject` indataports parametern för ämnes raden med en ämnes linje som passar din organisation.

```XML
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your Mailjet template. -->
    <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
    <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

    <!-- Update with an email appropriate for your organization. -->
    <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Lägg till DataUri innehålls definition

Under anspråks omvandlingarna i `<BuildingBlocks>` lägger du till följande [ContentDefinition](contentdefinitions.md) för att referera till versions-2.0.0 data-URI:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Skapa en visare

En verifierings visnings kontroll används för att verifiera e-postadressen med en verifierings kod som skickas till användaren.

Det här exemplet på att Visa kontroll är konfigurerat för att:

1. Samla in `email` typ av adress anspråk från användaren.
1. Vänta tills användaren har angett `verificationCode` anspråks typen med den kod som skickas till användaren.
1. Gå tillbaka `email` till den självkontrollerade tekniska profilen som innehåller en referens till den här visnings kontrollen.
1. Använd `SendCode` åtgärden och generera en kod för eng ång slö sen ord och skicka ett e-postmeddelande med koden för eng ång slö sen ord till användaren.

   ![Skicka verifierings kod e-poståtgärd](media/custom-email-mailjet/display-control-verification-email-action-01.png)

Under innehålls definitioner, fortfarande inom `<BuildingBlocks>` , lägger du till [DisplayControl](display-controls.md) följande visare av typen [VerificationControl](display-control-verification.md) i principen.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Lägga till tekniska profiler

Den `GenerateOtp` tekniska profilen genererar en kod för e-postadressen. Den `VerifyOtp` tekniska profilen verifierar koden som är associerad med e-postadressen. Du kan ändra konfigurationen för formatet och förfallo datum för eng ång slö sen ord. Mer information om tekniska profiler för eng ång slö sen ord finns i [definiera en enstaka teknisk profil](one-time-password-technical-profile.md)för eng ång slö sen ord.

Lägg till följande tekniska profiler i- `<ClaimsProviders>` elementet.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Lägg till en REST API teknisk profil

Den här REST API tekniska profilen genererar e-postinnehållet (med MailJet-formatet). Mer information om tekniska profiler för RESTful finns i [definiera en RESTful teknisk profil](restful-technical-profile.md).

Lägg till följande tekniska profiler i-elementet som med de tekniska profilerna för eng ång slö sen ord `<ClaimsProviders>` .

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Referera till visaren

I det sista steget lägger du till en referens till den visare som du skapade. Ersätt dina befintliga `LocalAccountSignUpWithLogonEmail` och `LocalAccountDiscoveryUsingEmailAddress` självkontrollerade tekniska profiler med följande. Om du använde en tidigare version av Azure AD B2Cs principen. Dessa tekniska profiler använder `DisplayClaims` med en referens till Visa Visa...

Mer information finns i [självkontrollerad teknisk profil](restful-technical-profile.md) och visare [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Valfritt Lokalisera ditt e-postmeddelande

För att lokalisera e-postmeddelandet måste du skicka lokaliserade strängar till MailJet eller din e-postprovider. Du kan till exempel lokalisera e-postmeddelandets ämne, brödtext, ditt kod meddelande eller signaturen för e-postmeddelandet. Om du vill göra det kan du använda [GetLocalizedStringsTransformation](string-transformations.md) Claims-omvandling för att kopiera lokaliserade strängar till anspråks typer. `GenerateEmailRequestBody`Omvandlingen av anspråk, som genererar JSON-nyttolasten, använder ingångs anspråk som innehåller de lokaliserade strängarna.

1. I principen definierar du följande sträng anspråk: ämne, meddelande, codeIntro och signatur.
1. Definiera en [GetLocalizedStringsTransformation](string-transformations.md) -anspråks omvandling för att ersätta lokaliserade sträng värden i anspråken från steg 1.
1. Ändra `GenerateEmailRequestBody` anspråks omvandlingen så att den använder indata-anspråk med följande XML-kodfragment.
1. Uppdatera din MailJet-mall för att använda dynamiska parametrar i stället för alla strängar som ska lokaliseras av Azure AD B2C.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Lägg till följande [lokaliserings](localization.md) element.

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.custom-email.en">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. Lägg till referenser till LocalizedResources-elementen genom att uppdatera [ContentDefinitions](contentdefinitions.md) -elementet.

    ```xml
    <ContentDefinitions>
      <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
      <ContentDefinition Id="api.localaccountpasswordreset">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
    </ContentDefinitions>
    ```

1. Slutligen lägger du till följande ingångs anspråk i de `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` tekniska profilerna.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="next-steps"></a>Nästa steg

Du hittar ett exempel på en anpassad princip för e-postverifiering på GitHub:

- [Anpassad e-postverifiering – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Information om hur du använder en anpassad REST API eller en HTTP-baserad SMTP e-postprovider finns i [definiera en RESTful teknisk profil i en Azure AD B2C anpassad princip](restful-technical-profile.md).