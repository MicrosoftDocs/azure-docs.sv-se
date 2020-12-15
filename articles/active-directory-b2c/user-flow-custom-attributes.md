---
title: Definiera anpassade attribut i Azure Active Directory B2C | Microsoft Docs
description: Definiera anpassade attribut för ditt program i Azure Active Directory B2C om du vill samla in information om dina kunder.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9759c1109c7be279520fa187bd3366bcac505d46
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503751"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiera anpassade attribut i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

I artikeln [Lägg till anspråk och anpassa användarindata med hjälp av anpassade principer](configure-user-input.md) får du lära dig hur du använder inbyggda [användar profil attribut](user-profile-attributes.md). I den här artikeln aktiverar du ett anpassat attribut i Azure Active Directory B2C-katalogen (Azure AD B2C). Senare kan du använda det nya attributet som ett anpassat anspråk i [användar flöden](user-flow-overview.md) eller [anpassade principer](custom-policy-get-started.md) samtidigt.

Din Azure AD B2C katalog levereras med en [inbyggd uppsättning attribut](user-profile-attributes.md). Du behöver dock ofta skapa egna attribut för att hantera ditt specifika scenario, till exempel när:

* Ett kundriktat program måste spara ett **LoyaltyId** -attribut.
* En identitets leverantör har en unik användar identifierare, **uniqueUserGUID**, som måste vara bestående.
* En anpassad användar resa måste behålla användarens tillstånd, **migrationStatus**, för att den andra logiken ska kunna användas.

Med Azure AD B2C kan du utöka uppsättningen med attribut som lagras för varje användar konto. Du kan också läsa och skriva attributen med hjälp av [Microsoft Graph-API: et](manage-user-accounts-graph-api.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **användarattribut och välj sedan** **Lägg till**.
1. Ange ett **namn** för det anpassade attributet (till exempel "ShoeSize")
1. Välj en **datatyp**. Endast **String**, **Boolean** och **int** är tillgängliga.
1. Alternativt kan du ange en **Beskrivning** i informations syfte.
1. Klicka på **Skapa**.

Det anpassade attributet är nu tillgängligt i listan över **användarattribut** och för användning i dina användar flöden. Ett anpassat attribut skapas bara första gången det används i alla användar flöden och inte när du lägger till **det i listan med användarattribut**.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Använd ett anpassat attribut i ditt användar flöde

1. Välj **användar flöden** i Azure AD B2C klient.
1. Välj din princip (till exempel "B2C_1_SignupSignin") för att öppna den.
1. Välj **användarattribut och välj** sedan det anpassade attributet (till exempel "ShoeSize"). Klicka på **Spara**.
1. Välj **program anspråk** och välj sedan det anpassade attributet.
1. Klicka på **Spara**.

När du har skapat en ny användare med hjälp av ett användar flöde som använder det nyligen skapade anpassade attributet, kan du frågas om objektet i [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Du kan också använda funktionen [Kör användar flöde](./tutorial-create-user-flows.md) i användar flödet för att verifiera kund upplevelsen. Nu bör du se **ShoeSize** i listan över attribut som samlats in under registreringen och se den i den token som skickas tillbaka till ditt program.

::: zone-end

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

## <a name="using-custom-attribute-with-ms-graph-api"></a>Använda anpassat attribut med MS Graph API

Microsoft Graph API har stöd för att skapa och uppdatera en användare med attribut för tillägg. Attribut för tillägg i Graph API namnges med hjälp av konventionen `extension_ApplicationClientID_attributename` , där `ApplicationClientID` är **programmets (klient) ID** för `b2c-extensions-app` programmet. Observera att **program-ID: t (klient)** som det visas i attributets namn för tillägg inte innehåller några bindestreck. Exempel:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
``` 

::: zone pivot="b2c-custom-policy"

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

Du kan skapa de här attributen med hjälp av portalens användar gränssnitt innan du använder dem i dina anpassade principer. När du skapar ett attribut **loyaltyId** i portalen måste du referera till det enligt följande:

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

::: zone-end

## <a name="next-steps"></a>Nästa steg

Följ anvisningarna för hur du [lägger till anspråk och anpassa användarindata med anpassade principer](configure-user-input.md). I det här exemplet används en inbyggd anspråks ort. Om du vill använda ett anpassat attribut ersätter du "ort" med dina egna anpassade attribut.