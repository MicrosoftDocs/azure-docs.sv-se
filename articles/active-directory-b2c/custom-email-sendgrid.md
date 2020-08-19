---
title: Anpassad e-postverifiering med SendGrid
titleSuffix: Azure AD B2C
description: Lär dig att integrera med SendGrid för att anpassa verifierings-e-postmeddelandet som skickas till kunderna när de registrerar sig för att använda dina Azure AD B2C-aktiverade program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 952659746bbb99108c6177166ad60ad2272cbce6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584947"
---
# <a name="custom-email-verification-with-sendgrid"></a>Anpassad e-postverifiering med SendGrid

Använd anpassad e-post i Azure Active Directory B2C (Azure AD B2C) för att skicka anpassat e-postmeddelande till användare som registrerar sig för att använda dina program. Genom att använda [DisplayControls](display-controls.md) (för närvarande i för hands version) och e-postprovidern från tredje part kan du använda din egen e-postmall och *från:* adress och ämne, samt stöd för lokalisering och anpassad eng ång slö sen ord.

Anpassad e-postverifiering kräver att en e-postleverantör från tredje part används, t. ex. [SendGrid](https://sendgrid.com), [MailJet](https://Mailjet.com)eller [Spark post](https://sparkpost.com), en anpassad REST API eller någon http-baserad e-postprovider (inklusive din egen). I den här artikeln beskrivs hur du konfigurerar en lösning som använder SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto

Om du inte redan har en, kan du börja med att konfigurera ett SendGrid-konto (Azure-kunder kan låsa upp 25 000 kostnads fria e-postmeddelanden varje månad). Installations anvisningar finns i avsnittet [skapa ett SendGrid-konto](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) i [skicka e-post med hjälp av SendGrid med Azure](../sendgrid-dotnet-how-to-send-email.md).

Se till att slutföra det avsnitt där du [skapar en SendGrid-API-nyckel](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Registrera API-nyckeln för användning i ett senare steg.

## <a name="create-azure-ad-b2c-policy-key"></a>Skapa Azure AD B2C princip nyckel

Sedan lagrar du SendGrid API-nyckeln i en Azure AD B2C princip nyckel för dina principer till referens.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj din Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ**väljer du **manuell**.
1. Ange ett **namn** för princip nyckeln. Till exempel `SendGridSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
1. I **hemlighet**anger du din klient hemlighet som du tidigare har spelat in.
1. För **nyckel användning**väljer du **signatur**.
1. Välj **Skapa**.

## <a name="create-sendgrid-template"></a>Skapa SendGrid-mall

När ett SendGrid-konto har skapats och SendGrid API-nyckeln lagras i en Azure AD B2C-princip nyckel, skapar du en SendGrid [dynamisk transaktions mal len](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Öppna sidan [transaktionella mallar](https://sendgrid.com/dynamic_templates) på SendGrid-webbplatsen och välj **Skapa mall**.
1. Ange ett unikt mallnamn som `Verification email` och välj sedan **Spara**.
1. Om du vill börja redigera din nya mall väljer du **Lägg till version**.
1. Välj **kod redigeraren** och **Fortsätt**sedan.
1. I HTML-redigeraren klistrar du in följande HTML-mall eller använder din egen. `{{otp}}`Parametrarna och `{{email}}` kommer att ersättas dynamiskt med eng ång slö sen ord och användarens e-postadress.

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
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
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

1. Expandera **Inställningar** till vänster och ange i **e-postämne** `{{subject}}` .
1. Välj **Spara mall**.
1. Gå tillbaka till sidan **transaktionella mallar** genom att välja bakåt-pilen.
1. Anteckna **ID** för mallen som du skapade för användning i ett senare steg. Till exempel `d-989077fbba9746e89f3f6411f596fb96`. Du anger detta ID när du [lägger till anspråks omvandlingen](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Lägg till Azure AD B2C anspråks typer

I principen lägger du till följande anspråks typer i- `<ClaimsSchema>` elementet i `<BuildingBlocks>` .

Dessa anspråks typer är nödvändiga för att generera och verifiera e-postadressen med hjälp av en eng ång slö sen ord.

```xml
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>SendGrid request body</DisplayName>
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

Sedan behöver du en anspråks omvandling för att mata ut ett JSON-sträng anspråk som kommer att bli bröd texten i begäran som skickas till SendGrid.

JSON-objektets struktur definieras av ID: n i punkt notation för indataparametrarna och TransformationClaimTypes för InputClaims. Siffror i punkt notation innebär att matriser. Värdena hämtas från InputClaims-värdena och värdet för indataparametrar. Mer information om JSON-anspråk omvandlingar finns i [JSON-anspråk omvandlingar](json-transformations.md).

Lägg till följande Claims-omvandling i- `<ClaimsTransformations>` elementet i `<BuildingBlocks>` . Gör följande uppdateringar i XML-omvandlingen för anspråk:

* Uppdatera `template_id` InputParameter-värdet med ID: t för den transaktions mal len SendGrid som du skapade tidigare i [create SendGrid-mallen](#create-sendgrid-template).
* Uppdatera `from.email` värdet för adress. Använd en giltig e-postadress för att förhindra att verifierings meddelandet markeras som skräp post.
* Uppdatera värdet för `personalizations.0.dynamic_template_data.subject` indataports parametern för ämnes raden med en ämnes linje som passar din organisation.

```xml
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Lägg till DataUri innehålls definition

Under anspråks omvandlingarna i `<BuildingBlocks>` lägger du till följande [ContentDefinition](contentdefinitions.md) för att referera till versions-2.0.0 data-URI:

```xml
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

![Skicka verifierings kod e-poståtgärd](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

Under innehålls definitioner, fortfarande inom `<BuildingBlocks>` , lägger du till [DisplayControl](display-controls.md) följande visare av typen [VerificationControl](display-control-verification.md) i principen.

```xml
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

```xml
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

Den här REST API tekniska profilen genererar e-postinnehållet (med SendGrid-formatet). Mer information om tekniska profiler för RESTful finns i [definiera en RESTful teknisk profil](restful-technical-profile.md).

Lägg till följande tekniska profiler i-elementet som med de tekniska profilerna för eng ång slö sen ord `<ClaimsProviders>` .

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
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

I det sista steget lägger du till en referens till den visare som du skapade. Ersätt dina befintliga `LocalAccountSignUpWithLogonEmail` och `LocalAccountDiscoveryUsingEmailAddress` självkontrollerade tekniska profiler med följande. Om du använde en tidigare version av Azure AD B2Cs principen. Dessa tekniska profiler använder `DisplayClaims` med en referens till Visa-visaren.

Mer information finns i [självkontrollerad teknisk profil](restful-technical-profile.md) och visare [DisplayControl](display-controls.md).

```xml
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

För att lokalisera e-postmeddelandet måste du skicka lokaliserade strängar till SendGrid eller din e-postprovider. Du kan till exempel lokalisera e-postmeddelandets ämne, brödtext, ditt kod meddelande eller signaturen för e-postmeddelandet. Om du vill göra det kan du använda [GetLocalizedStringsTransformation](string-transformations.md) Claims-omvandling för att kopiera lokaliserade strängar till anspråks typer. `GenerateEmailRequestBody`Omvandlingen av anspråk, som genererar JSON-nyttolasten, använder ingångs anspråk som innehåller de lokaliserade strängarna.

1. I principen definierar du följande sträng anspråk: ämne, meddelande, codeIntro och signatur.
1. Definiera en [GetLocalizedStringsTransformation](string-transformations.md) -anspråks omvandling för att ersätta lokaliserade sträng värden i anspråken från steg 1.
1. Ändra `GenerateEmailRequestBody` anspråks omvandlingen så att den använder indata-anspråk med följande XML-kodfragment.
1. Uppdatera din SendGrid-mall för att använda dynamiska parametrar i stället för alla strängar som ska lokaliseras av Azure AD B2C.

    ```xml
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
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
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
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. Lägg till referenser till LocalizedResources-elementen genom att uppdatera [ContentDefinitions](contentdefinitions.md) -elementet.

    ```XML
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

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="next-steps"></a>Nästa steg

Du hittar ett exempel på en anpassad princip för e-postverifiering på GitHub:

- [Anpassad e-postverifiering – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Information om hur du använder en anpassad REST API eller en HTTP-baserad SMTP e-postprovider finns i [definiera en RESTful teknisk profil i en Azure AD B2C anpassad princip](restful-technical-profile.md).
