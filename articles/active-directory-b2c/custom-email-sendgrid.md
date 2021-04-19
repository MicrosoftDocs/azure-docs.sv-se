---
title: Anpassad e-postverifiering med SendGrid
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar med SendGrid för att anpassa verifieringsmeddelandet som skickas till dina kunder när de registrerar sig för att använda Azure AD B2C-aktiverade program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2c4dcaaa1deaa50d620e7c24dacbe56fa91c217f
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713452"
---
# <a name="custom-email-verification-with-sendgrid"></a>Anpassad e-postverifiering med SendGrid

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Använd anpassad e-post Azure Active Directory B2C (Azure AD B2C) för att skicka anpassad e-post till användare som registrerar sig för att använda dina program. Med hjälp av tredjeparts-e-postleverantören SendGrid kan du använda din egen e-postmall och *Från:* adress och ämne, samt stöd för lokalisering och anpassade engångslösenordsinställningar (OTP).

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Anpassad e-postverifiering kräver användning av en tredjeparts-e-postleverantör som [SendGrid,](https://sendgrid.com) [Mailjet](https://Mailjet.com)eller [SparkPost,](https://sparkpost.com)en anpassad REST API eller en HTTP-baserad e-postleverantör (inklusive din egen). I den här artikeln beskrivs hur du ställer in en lösning som använder SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto

Om du inte redan har ett kan du börja med att konfigurera ett SendGrid-konto (Azure-kunder kan låsa upp 25 000 kostnadsfria e-postmeddelanden varje månad). Installationsanvisningar finns i avsnittet [Skapa ett SendGrid-konto i](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) Skicka e-post med [SendGrid med Azure](../sendgrid-dotnet-how-to-send-email.md).

Se till att slutföra avsnittet där du skapar en [SendGrid API-nyckel.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) Registrera API-nyckeln för användning i ett senare steg.

> [!IMPORTANT]
> SendGrid ger kunderna möjlighet att skicka e-postmeddelanden från delade IP-adresser och [dedikerade IP-adresser.](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/) När du använder dedikerade IP-adresser måste du skapa ditt eget rykte korrekt med en IP-adress som värmer upp. Mer information finns i [Värme upp en IP-adress.](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/)

## <a name="create-azure-ad-b2c-policy-key"></a>Skapa Azure AD B2C principnyckel

Lagra sedan SendGrid API-nyckeln i en Azure AD B2C principnyckel som dina principer ska referera till.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C klientorganisation. Välj filtret **Katalog +** prenumeration på den översta menyn och välj Azure AD B2C katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar och** välj sedan Lägg **till**.
1. För **Alternativ** väljer du **Manuell.**
1. Ange ett **namn** för principnyckeln. Till exempel `SendGridSecret`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på din nyckel.
1. I **Hemlighet** anger du den SendGrid API-nyckel som du antecknade tidigare.
1. För **Nyckelanvändning** väljer du **Signatur.**
1. Välj **Skapa**.

## <a name="create-sendgrid-template"></a>Skapa SendGrid-mall

När ett SendGrid-konto har skapats och SendGrid API-nyckeln lagras i en Azure AD B2C-principnyckel skapar du en dynamisk [SendGrid-transaktionsmall.](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)

1. På SendGrid-webbplatsen öppnar du [sidan med transaktionsmallar](https://sendgrid.com/dynamic_templates) och väljer **Skapa mall.**
1. Ange ett unikt mallnamn som `Verification email` och välj sedan **Spara.**
1. Börja redigera den nya mallen genom att välja Lägg **till version**.
1. Välj **Kodredigeraren** och sedan **Fortsätt.**
1. I HTML-redigeraren klistrar du in följande HTML-mall eller använder en egen. Parametrarna `{{otp}}` `{{email}}` och ersätts dynamiskt med värdet för ett lösenord och användarens e-postadress.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
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

1. Expandera **Inställningar** till vänster och för E-postämne anger du  `{{subject}}` .
1. Välj **Spara mall.**
1. Gå tillbaka till **sidan Transaktionsmallar** genom att välja bakåtpilen.
1. Registrera **ID:t** för mallen som du skapade för användning i ett senare steg. Till exempel `d-989077fbba9746e89f3f6411f596fb96`. Du anger detta ID när du lägger [till anspråksomvandlingen](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Lägga Azure AD B2C anspråkstyper

I din princip lägger du till följande anspråkstyper i `<ClaimsSchema>` elementet i `<BuildingBlocks>` .

Dessa anspråkstyper är nödvändiga för att generera och verifiera e-postadressen med hjälp av en engångslösenordskod (OTP).

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
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
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>Lägga till anspråksomvandlingen

Därefter behöver du en anspråksomvandling för att mata ut ett JSON-stränganspråk som ska vara brödtexten i begäran som skickas till SendGrid.

JSON-objektets struktur definieras av -ID:erna i punkt-notationen för InputParameters och TransformationClaimTypes för InputClaims. Tal i punkt notationen innebär matriser. Värdena kommer från InputClaims-värdena och InputParameters-egenskaperna "Value". Mer information om JSON-anspråksomvandlar finns i [JSON-anspråksomvandlar](json-transformations.md).

Lägg till följande anspråksomvandling till `<ClaimsTransformations>` elementet i `<BuildingBlocks>` . Gör följande uppdateringar av XML-filen för anspråksomvandling:

* Uppdatera `template_id` InputParameter-värdet med ID:t för den SendGrid-transaktionsmall som du skapade tidigare i [Skapa SendGrid-mall.](#create-sendgrid-template)
* Uppdatera `from.email` adressvärdet. Använd en giltig e-postadress för att förhindra att verifieringsmeddelandet markeras som skräppost.
* Uppdatera värdet för ämnesradens `personalizations.0.dynamic_template_data.subject` indataparameter med en ämnesrad som är lämplig för din organisation.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
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
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>Lägga till DataUri-innehållsdefinition

Under anspråksomvandlarna i lägger du till följande ContentDefinition för att referera `<BuildingBlocks>` till data-URI:t version 2.1.2: [](contentdefinitions.md)

```xml
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>Skapa en DisplayControl

En verifieringsvisningskontroll används för att verifiera e-postadressen med en verifieringskod som skickas till användaren.

Den här exempelvisningskontrollen är konfigurerad för att:

1. Samla in `email` adressens anspråkstyp från användaren.
1. Vänta tills användaren anger `verificationCode` anspråkstypen med koden som skickas till användaren.
1. Gå tillbaka `email` till den självkontrollerade tekniska profilen som har en referens till den här visningskontrollen.
1. Med åtgärden `SendCode` genererar du en OTP-kod och skickar ett e-postmeddelande med OTP-koden till användaren.

![Skicka e-poståtgärd för verifieringskod](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

Under innehållsdefinitioner, fortfarande `<BuildingBlocks>` i , lägger du till följande [DisplayControl](display-controls.md) av typen [VerificationControl](display-control-verification.md) i principen.

```xml
<!--
<BuildingBlocks> -->
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
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>Lägga till tekniska profiler för engångslösenord

Den `GenerateOtp` tekniska profilen genererar en kod för e-postadressen. Den `VerifyOtp` tekniska profilen verifierar koden som är associerad med e-postadressen. Du kan ändra formatets konfiguration och lösenordets giltighetstid. Mer information om tekniska profiler för engångslösenord finns i Definiera en teknisk [profil för engångslösenord.](one-time-password-technical-profile.md)

Lägg till följande tekniska profiler i `<ClaimsProviders>` elementet .

```xml
<!--
<ClaimsProviders> -->
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
          <Item Key="NumRetryAttempts">5</Item>
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
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>Lägga till en REST API teknisk profil

Den REST API tekniska profilen genererar e-postinnehållet (med sendGrid-formatet). Mer information om RESTful-tekniska profiler finns i [Definiera en teknisk RESTful-profil.](restful-technical-profile.md)

Precis som med tekniska profiler för engångslösenord lägger du till följande tekniska profiler i `<ClaimsProviders>` elementet .

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

## <a name="make-a-reference-to-the-displaycontrol"></a>Skapa en referens till DisplayControl

I det sista steget lägger du till en referens till den DisplayControl som du skapade. Ersätt dina `LocalAccountSignUpWithLogonEmail` befintliga `LocalAccountDiscoveryUsingEmailAddress` och självinfogade tekniska profiler med följande. Om du använde en tidigare version Azure AD B2C princip. Dessa tekniska profiler `DisplayClaims` använder med en referens till DisplayControl.

Mer information finns i [Självkontrollerad teknisk profil och](restful-technical-profile.md) [DisplayControl.](display-controls.md)

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
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
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
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

## <a name="optional-localize-your-email"></a>[Valfritt] Lokalisera din e-post

För att lokalisera e-postmeddelandet måste du skicka lokaliserade strängar till SendGrid eller din e-postleverantör. Du kan till exempel lokalisera e-postämnet, brödtexten, kodmeddelandet eller signaturen för e-postmeddelandet. Om du vill göra det kan du använda [omvandlingen GetLocalizedStringsTransformation-anspråk](string-transformations.md) för att kopiera lokaliserade strängar till anspråkstyper. `GenerateEmailRequestBody`Anspråksomvandlingen, som genererar JSON-nyttolasten, använder inkommande anspråk som innehåller de lokaliserade strängarna.

1. Definiera följande stränganspråk i principen: ämne, meddelande, codeIntro och signatur.
1. Definiera en [GetLocalizedStringsTransformation-anspråksomvandling](string-transformations.md) för att ersätta lokaliserade strängvärden i anspråken från steg 1.
1. Ändra `GenerateEmailRequestBody` anspråksomvandlingen för att använda inkommande anspråk med följande XML-kodfragment.
1. Uppdatera SendGrid-mallen så att den använder dynamiska parametrar i stället för alla strängar som ska lokaliseras av Azure AD B2C.

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

1. Lägg till följande [lokaliseringselement.](localization.md)

    ```xml
    <!--
    <BuildingBlocks> -->
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
    <!--
    </BuildingBlocks> -->
    ```

1. Lägg till referenser till localizedResources-elementen genom att uppdatera [elementet ContentDefinitions.](contentdefinitions.md)

    ```XML
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Slutligen lägger du till följande omvandling av inkommande anspråk i `LocalAccountSignUpWithLogonEmail` de tekniska `LocalAccountDiscoveryUsingEmailAddress` profilerna och .

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>[Valfritt] Lokalisera användargränssnittet

Med lokaliseringselementet kan du stödja flera språk eller språk i principen för användarresor. Med lokaliseringsstödet i principer kan du ange språkspecifika strängar för både elementen i användargränssnittet för verifieringskontroll [och](localization-string-ids.md#verification-display-control-user-interface-elements) [felmeddelanden för lösenord en gång.](localization-string-ids.md#one-time-password-error-messages) Lägg till följande LocalizedString i LocalizedResources. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
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
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

När du har lagt till de lokaliserade strängarna tar du bort metadata för OTP-verifieringsfelmeddelanden från de tekniska profilerna LocalAccountSignUpWithLogonEmail och LocalAccountDiscoveryUsingEmailAddress.

## <a name="next-steps"></a>Nästa steg

Du hittar ett exempel på en anpassad princip för e-postverifiering på GitHub:

- [Anpassad e-postverifiering – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Information om hur du använder en anpassad REST API eller en HTTP-baserad SMTP-e-postleverantör finns i Definiera en [teknisk RESTful-profil](restful-technical-profile.md)i en anpassad Azure AD B2C en anpassad princip.

::: zone-end
