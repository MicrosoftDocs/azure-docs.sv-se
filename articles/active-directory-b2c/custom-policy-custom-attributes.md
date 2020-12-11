---
title: Lägg till egna attribut till anpassade principer
titleSuffix: Azure AD B2C
description: En genom gång av hur du använder tilläggs egenskaper och anpassade attribut och inkluderar dem i användar gränssnittet.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ec99558f3a168b770ad19fb4f6c811a31c44f08
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108901"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: Aktivera anpassade attribut i en anpassad profil princip

I artikeln [Lägg till anspråk och anpassa användarindata med hjälp av anpassade principer](configure-user-input.md) får du lära dig hur du använder inbyggda [användar profil attribut](user-profile-attributes.md). I den här artikeln aktiverar du ett anpassat attribut i Azure Active Directory B2C-katalogen (Azure AD B2C). Senare kan du använda det nya attributet som ett anpassat anspråk i [användar flöden](user-flow-overview.md) eller [anpassade principer](custom-policy-get-started.md) samtidigt.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Förutsättningar

Följ stegen i artikeln [Azure Active Directory B2C: kom igång med anpassade principer](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Använd anpassade attribut för att samla in information om dina kunder 

Din Azure AD B2C katalog levereras med en [inbyggd uppsättning attribut](user-profile-attributes.md). Du behöver dock ofta skapa egna attribut för att hantera ditt specifika scenario, till exempel när:

* Ett kundriktat program måste spara ett **LoyaltyId** -attribut.
* En identitets leverantör har en unik användar identifierare, **uniqueUserGUID**, som måste vara bestående.
* En anpassad användar resa måste behålla användarens tillstånd, **migrationStatus**, för att den andra logiken ska kunna användas.

Med Azure AD B2C kan du utöka uppsättningen med attribut som lagras för varje användar konto. Du kan också läsa och skriva attributen med hjälp av [Microsoft Graph-API: et](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C tillägg-appen

Attribut för tillägg kan bara registreras på ett program objekt, även om de kan innehålla data för en användare. Attributet Extension är kopplat till programmet som kallas B2C-Extensions-app. Ändra inte det här programmet eftersom det används av Azure AD B2C för att lagra användar data. Du kan hitta det här programmet under Azure AD B2C, app-registreringar.

Villkors *tilläggets egenskap*, *anpassade attribut* och *anpassat anspråk* refererar till samma sak som i den här artikeln. Namnet varierar beroende på kontexten, t. ex. program, objekt eller princip.

## <a name="get-the-application-properties"></a>Hämta programmets egenskaper

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **alla program**.
1. Välj programmet `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Kopiera följande identifierare till Urklipp och spara dem:
    * **Program-ID**. Exempel: `11111111-1111-1111-1111-111111111111`.
    * **Objekt-ID**. Exempel: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Ändra den anpassade principen

Om du vill aktivera anpassade attribut i principen anger du **program-ID** och program **objekt-ID** i AAD-Common tekniska metadata för teknisk profil. *AAD – vanlig* teknisk profil finns i bas [Azure Active Directory](active-directory-technical-profile.md) teknisk profil och ger stöd för Azure AD-användar hantering. Andra tekniska profiler för Azure AD innehåller AAD-Common för att utnyttja konfigurationen. Åsidosätt AAD-Common tekniska profilen i tilläggs filen.

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Hitta ClaimsProviders-elementet. Lägg till en ny ClaimsProvider i ClaimsProviders-elementet.
1. Ersätt `ApplicationObjectId` med det objekt-ID som du tidigare har spelat in. Ersätt sedan `ClientId` med det program-ID som du tidigare spelat in i avsnittet nedan.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Ladda upp en anpassad princip

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller Azure AD B2C klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
4. Välj **ramverk för identitets upplevelse**.
5. Välj **överför anpassad princip** och ladda upp de TrustFrameworkExtensions.xml principfiler som du har ändrat.

> [!NOTE]
> Första gången som den tekniska profilen i Azure AD behåller anspråk till katalogen, kontrollerar den om det anpassade attributet finns. Om inte, skapas det anpassade attributet.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Skapa ett anpassat attribut via Azure Portal

Samma attribut för tillägg delas mellan inbyggda och anpassade principer. När du lägger till anpassade attribut via portal miljön registreras dessa attribut med hjälp av **B2C-Extensions-appen** som finns i alla B2C-klienter.

Du kan skapa de här attributen med hjälp av portalens användar gränssnitt innan du använder dem i dina anpassade principer. Följ anvisningarna för hur du [definierar anpassade attribut i Azure Active Directory B2C](user-flow-custom-attributes.md). När du skapar ett attribut **loyaltyId** i portalen måste du referera till det enligt följande:

|Namn     |Används i |
|---------|---------|
|`extension_loyaltyId`  | Anpassad princip|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

Följande exempel visar hur du använder anpassade attribut i en Azure AD B2C anpassad definition av princip anspråk.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

I följande exempel demonstreras användningen av ett anpassat attribut i Azure AD B2C anpassade principen i en teknisk profil, indata, utdata och bestående anspråk.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Använd ett anpassat attribut i en princip

Följ anvisningarna för hur du [lägger till anspråk och anpassa användarindata med anpassade principer](configure-user-input.md). I det här exemplet används en inbyggd anspråks ort. Om du vill använda ett anpassat attribut ersätter du "ort" med dina egna anpassade attribut.


## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure AD B2C attribut för användar profil](user-profile-attributes.md)
- [Definition av attribut för tillägg](user-profile-attributes.md#extension-attributes)
- [Hantera Azure AD B2C användar konton med Microsoft Graph](manage-user-accounts-graph-api.md)
