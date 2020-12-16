---
title: Lägg till användarattribut och anpassa användarindata
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användarindata och lägger till användarattribut till inloggnings-eller inloggnings resan i Azure Active Directory B2C.
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
ms.openlocfilehash: eb7cba1de280793a1ca98687c71355c1ea702d4c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585232"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Lägg till användarattribut och anpassa användarindata i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

I den här artikeln samlar du in ett nytt attribut under din inloggnings resa i Azure Active Directory B2C (Azure AD B2C). Du får användarnas stad, konfigurerar den som en nedrullningsbar listruta och anger om den måste anges.

## <a name="prerequisites"></a>Krav

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Lägg till användarattribut i användar flödet

1. Välj **användar flöden** i Azure AD B2C klient.
1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **användarattribut och välj sedan attributet användare (** till exempel "ort"). 
1. Välj **Spara**.

## <a name="provide-optional-claims-to-your-app"></a>Tillhandahålla valfria anspråk till din app

Program anspråk är värden som returneras till programmet. Uppdatera ditt användar flöde så att det innehåller önskade anspråk.

1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **Programanspråk**.
1. Välj attribut som du vill skicka tillbaka till ditt program (till exempel "ort").
1. Välj **Spara**.
 
## <a name="configure-user-attributes-input-type"></a>Konfigurera Indatatyp för användarattribut

1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **sidlayouter**.
1. Välj **inloggnings sida för lokalt konto**.
1. Under **användarattribut** väljer du **ort**.
    1. I list rutan **typ av Indatatyp** väljer du **DropdownSingleSelect**.
    1. I den **valfria** List rutan väljer du **Nej**.
1. Välj **Spara**. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Ange en lista med värden med hjälp av lokaliserade samlingar

För att tillhandahålla en uppsättning med värden för attributet City: 

1. [Aktivera språk anpassning i användar flödet](language-customization.md#support-requested-languages-for-ui_locales)
1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. På sidan **språk** för användar flödet väljer du det språk som du vill anpassa.
1. Under **filer på sidan filnivå – resurser** väljer du **sidan Registrera lokalt konto**.
1. Välj **Hämta standardvärden** (eller **Hämta åsidosättningar** om du tidigare har redigerat det här språket).
1. Skapa ett `LocalizedCollections` attribut.

`LocalizedCollections`Är en matris med `Name` och `Value` par. Ordningen för objekten visas i den ordning som de visas. 

* `ElementId` är det användar-attribut som det här `LocalizedCollections` attributet är ett svar på.
* `Name` är värdet som visas för användaren.
* `Value` är vad som returneras i anspråket när det här alternativet är markerat.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Överför dina ändringar

1. När du har slutfört ändringarna i JSON-filen går du tillbaka till B2C-klienten.
1. Välj **användar flöden** och välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **språk**.
1. Välj det språk som du vill översätta till.
1. Välj **sidan Registrera lokalt konto**.
1. Välj mappikonen och välj den JSON-fil som ska överföras. Ändringarna sparas automatiskt i ditt användar flöde.

## <a name="test-your-user-flow"></a>Testa ditt användar flöde

1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> I det här exemplet används det inbyggda anspråket "ort". I stället kan du välja ett av de [Azure AD B2C inbyggda attributen](user-profile-attributes.md) eller ett anpassat attribut som stöds. [Aktivera anpassade attribut](user-flow-custom-attributes.md)om du vill använda ett anpassat attribut. Om du vill använda ett annat inbyggt eller anpassat attribut ersätter du "ort" med attributet som du väljer, till exempel det inbyggda attributet *befattning* eller ett anpassat attribut som *extension_loyaltyId*.  

Du kan samla in inledande data från dina användare med hjälp av användar resan för registrering eller inloggning. Ytterligare anspråk kan samlas in senare med hjälp av en profil redigera användar resa. När Azure AD B2C samlar in information direkt från användaren interaktivt används den [självkontrollerade tekniska profilen](self-asserted-technical-profile.md). I det här exemplet:

1. Definiera ett "ort"-anspråk. 
1. Be användaren om sin stad.
1. Behåll staden till användar profilen i Azure AD B2C-katalogen.
1. Läs Orts anspråket från Azure AD B2C-katalogen vid varje inloggning.
1. Returnera staden till ditt förlitande parts program efter inloggning eller registrering.  

## <a name="define-a-claim"></a>Definiera ett anspråk

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C princip körning. [Anspråks schema](claimsschema.md) är den plats där du deklarerar dina anspråk. Följande element används för att definiera anspråket:

- **DisplayName** – en sträng som definierar den användarbaserade etiketten.
- [Datatyp](claimsschema.md#datatype) – anspråkets typ.
- **UserHelpText** – hjälper användaren att förstå vad som krävs.
- [UserInputType](claimsschema.md#userinputtype) – typ av inmatnings kontroll, till exempel text ruta, val av alternativ, list Rute lista eller flera val.

Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till Orts anspråk i **ClaimsSchema** -elementet.  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Lägg till ett anspråk i användar gränssnittet

Följande tekniska profiler är [självkontrollerade](self-asserted-technical-profile.md)och anropas när en användare förväntas tillhandahålla indata:

- **LocalAccountSignUpWithLogonEmail** – registrerings flöde för lokalt konto.
- **SelfAsserted** – federerat konto för användar inloggning första gången.
- **SelfAsserted-ProfileUpdate** – Redigera profil flöde.

Om du vill samla in Orts anspråk under registreringen måste det läggas till som ett utgående anspråk i den `LocalAccountSignUpWithLogonEmail` tekniska profilen. Åsidosätt den här tekniska profilen i tilläggs filen. Ange hela listan med utgående anspråk för att kontrol lera i vilken ordning anspråken visas på skärmen. Hitta **ClaimsProviders** -elementet. Lägg till en ny ClaimsProviders enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Om du vill samla in Orts anspråk efter första inloggningen med ett federerat konto måste det läggas till som ett utgående anspråk till den `SelfAsserted-Social` tekniska profilen. För lokala och federerade konto användare ska kunna redigera sina profil data senare genom att lägga till utgående anspråk i den `SelfAsserted-ProfileUpdate` tekniska profilen. Åsidosätt dessa tekniska profiler i tilläggs filen. Ange hela listan med de utgående anspråken för att kontrol lera i vilken ordning anspråken visas på skärmen. Hitta **ClaimsProviders** -elementet. Lägg till en ny ClaimsProviders enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Läs och skriv ett anspråk

Följande tekniska profiler är [Active Directory tekniska profiler](active-directory-technical-profile.md)som läser och skriver data till Azure Active Directory.  
Använd `PersistedClaims` för att skriva data till användar profilen och `OutputClaims` läsa data från användar profilen i respektive Active Directory tekniska profiler.

Åsidosätt dessa tekniska profiler i tilläggs filen. Hitta **ClaimsProviders** -elementet.  Lägg till en ny ClaimsProviders enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Inkludera ett anspråk i token 

Om du vill lämna tillbaka tillbaka till det förlitande part-programmet lägger du till ett utgående anspråk i <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> filen. Utgående anspråk kommer att läggas till i token efter en lyckad användar resa och skickas till programmet. Ändra det tekniska profil elementet i avsnittet förlitande part för att lägga till staden som ett utgående anspråk.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
4. Välj **ramverk för identitets upplevelse**.
5. Välj **överför anpassad princip** och ladda upp de två principfiler som du har ändrat.
2. Välj den registrerings-eller inloggnings princip som du laddade upp och klicka på knappen **Kör nu** .
3. Du bör kunna registrera dig med en e-postadress.

::: zone-end

Registrerings skärmen bör se ut ungefär som på följande skärm bild:

![Skärm bild av ändrat registrerings alternativ](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

Den token som skickas tillbaka till programmet inkluderar `city` anspråket.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Nästa steg

- Läs mer om [ClaimsSchema](claimsschema.md) -ELEMENTET i IEF-referensen.
- Lär dig hur du [använder anpassade attribut i Azure AD B2C](user-flow-custom-attributes.md).

::: zone-end