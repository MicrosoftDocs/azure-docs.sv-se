---
title: Hantera användar åtkomst i Azure Active Directory B2C | Microsoft Docs
description: Lär dig att identifiera minderåriga, samla in datum för födelse-och lands-/region data och få godkännande av användnings villkor i ditt program med hjälp av Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ee26e7fe74d87f7b20f9a28b049b8043b376273
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518065"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Hantera användar åtkomst i Azure Active Directory B2C

Den här artikeln beskriver hur du hanterar användar åtkomst till dina program med hjälp av Azure Active Directory B2C (Azure AD B2C). Åtkomst hantering i programmet innehåller:

- Identifiera minderåriga och kontrol lera användar åtkomst till ditt program.
- Kräver överordnat medgivande för att minderåriga ska kunna använda dina program.
- Samlar in födelse-och lands-/region data från användare.
- Samla in ett avtal för användnings villkor och hantera-åtkomst.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kontrol lera mindre åtkomst

Program och organisationer kan välja att blockera minderåriga från att använda program och tjänster som inte är riktade till den här mål gruppen. Det kan också hända att program och organisationer bestämmer sig för att godkänna minderåriga och sedan hantera föräldra medgivande och leverera tillåtna erfarenheter av minderåriga enligt affärs regler och som tillåts av förordningen.

Om en användare identifieras som en mindre, kan du ställa in användar flödet i Azure AD B2C till något av tre alternativ:

- **Skicka en signerad JWT-id_token tillbaka till programmet**: användaren är registrerad i katalogen och en token returneras till programmet. Programmet fortsätter sedan genom att tillämpa affärs regler. Programmet kan till exempel fortsätta med en överordnad medgivande process. Om du vill använda den här metoden väljer du att ta emot **ageGroup** -och **consentProvidedForMinor** -anspråk från programmet.

- **Skicka en osignerad JSON-token till programmet**: Azure AD B2C meddelar programmet att användaren är en mindre och ger status för användarens föräldra medgivande. Programmet fortsätter sedan genom att tillämpa affärs regler. En JSON-token Slutför inte en lyckad autentisering med programmet. Programmet måste bearbeta den oautentiserade användaren enligt de anspråk som ingår i JSON-token, som kan innehålla **namn**, **e-post**, **ageGroup** och **consentProvidedForMinor**.

- **Blockera användaren**: om en användare är en mindre, och det inte finns något överordnat medgivande, kan Azure AD B2C meddela användaren att de är blockerade. Ingen token har utfärdats, åtkomst är blockerad och användar kontot skapas inte under en registrerings resa. Om du vill implementera det här meddelandet anger du en lämplig HTML/CSS-innehålls sida för att informera användaren och presentera lämpliga alternativ. Ingen ytterligare åtgärd krävs av programmet för nya registreringar.

## <a name="get-parental-consent"></a>Få föräldra medgivande

Beroende på program reglering kan föräldra medgivande behöva beviljas av en användare som är verifierad som vuxen. Azure AD B2C ger ingen erfarenhet av att verifiera en enskild persons ålder och gör det möjligt för en verifierad vuxen att ge ett överordnat medgivande till en mindre. Den här funktionen måste tillhandahållas av programmet eller någon annan tjänst leverantör.

Följande är ett exempel på ett användar flöde för att samla in föräldra godkännande:

1. En [Microsoft Graph API](/graph/use-the-api) -åtgärd identifierar användaren som en del och returnerar användar data till programmet i form av en OSIGNERAd JSON-token.

2. Programmet bearbetar JSON-token och visar en skärm som är mindre, och meddelar dem att det krävs ett överordnat medgivande och begär medgivande för en överordnad online.

3. Azure AD B2C visar en inloggnings resa som användaren kan logga in på normalt och utfärdar en token till det program som är inställt på att inkludera **legalAgeGroupClassification = "minorWithParentalConsent"**. Programmet samlar in e-postadressen för den överordnade och kontrollerar att den överordnade är en vuxen. För att göra det använder den en betrodd källa, till exempel ett nationellt ID-kontor, licens verifiering eller kredit korts bevis. Om verifieringen lyckas, begär programmet den minderårige att logga in med hjälp av Azure AD B2C användar flödet. Om medgivande nekas (till exempel om **legalAgeGroupClassification = "minorWithoutParentalConsent"**) Azure AD B2C returnerar en JSON-token (inte en inloggning) till programmet för att starta om medgivande processen. Det är möjligt att anpassa användar flödet så att en mindre eller vuxen kan få åtkomst till ett mindre konto genom att skicka en registrerings kod till den minderåriges e-postadress eller barnets e-postadress på posten.

4. Programmet erbjuder ett alternativ till den mindre för att återkalla medgivande.

5. När antingen den mindre eller den vuxen återkallar medgivande kan Microsoft Graph-API: et användas för att ändra **consentProvidedForMinor** till **nekad**. Alternativt kan programmet välja att ta bort en mindre person vars medgivande har återkallats. Det är möjligt att anpassa användar flödet så att den autentiserade mindre (eller överordnade som använder del kontot) kan återkalla medgivande. Azure AD B2C poster **consentProvidedForMinor** som **nekad**.

Mer information om **legalAgeGroupClassification**, **consentProvidedForMinor** och **ageGroup** finns i [användar resurs typ](/graph/api/resources/user). Mer information om anpassade attribut finns i [använda anpassade attribut för att samla in information om dina användare](user-flow-custom-attributes.md). När du hanterar utökade attribut med hjälp av Microsoft Graph-API: et måste du använda den långa versionen av attributet, till exempel *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Samla in datum för födelse-och lands-/region data

Program kan förlita sig på Azure AD B2C att samla in födelse datum och lands-/region information från alla användare under registreringen. Om den här informationen inte redan finns kan programmet begära det från användaren under nästa autentisering (inloggning)-resa. Användarna kan inte fortsätta utan att ange information om DOB och land/region. Azure AD B2C använder informationen för att avgöra om den enskilda personen anses vara en lägre enligt regelverket för landet/regionen.

Ett anpassat användar flöde kan samla in information om DOB och land/region och använda Azure AD B2C Claims-transformering för att fastställa **ageGroup** och bevara resultatet (eller spara informationen om DOB och land/region direkt) i katalogen.

Följande steg visar vilken logik som används för att beräkna **ageGroup** från användarens födelse datum:

1. Försök att hitta landet/regionen med lands-/region koden i listan. Om landet/regionen inte hittas, kan du återgå till **standard**.

2. Om noden **MinorConsent** finns i elementet land/region:

    a. Beräkna det datum då användaren måste vara föddes för att anses vara vuxen. Om det aktuella datumet till exempel är 14 mars 2015 och **MinorConsent** är 18, får födelse datumet inte vara senare än 14 mars 2000.

    b. Jämför det minsta födelse datumet med det faktiska födelse datumet. Om det minsta födelse datumet infaller före användarens födelse datum, returnerar beräkningen **mindre** som ålders grupps beräkning.

3. Om noden **MinorNoConsentRequired** finns i elementet land/region upprepar du steg 2a och 2b med värdet från **MinorNoConsentRequired**. Resultatet av 2b returnerar **MinorNoConsentRequired** om det minsta födelse datumet infaller före användarens födelse datum.

4. Om ingen av beräkningarna returnerar true returnerar beräkningen **vuxen**.

Om ett program har samlat in information om DOB eller land/region på andra sätt kan programmet använda Graph API för att uppdatera användar posten med den här informationen. Exempel:

- Om en användare är känd som vuxen, uppdaterar du **ageGroup** med värdet **vuxen**.
- Om en användare är känd som en mindre uppdaterar du **ageGroup** med värdet **moll** och anger **consentProvidedForMinor**, efter behov.

## <a name="minor-calculation-rules"></a>Mindre beräknings regler

Ålders hantera omfattar två ålders värden: den ålder som någon inte längre anses vara mindre och den tid då en mindre måste ha ett överordnat medgivande. I följande tabell visas de ålders regler som används för att definiera en mindre och en mindre som kräver medgivande.

| Land/region | Land/region namn | Liten medgivande ålder | Mindre ålder |
| -------------- | ------------------- | ----------------- | --------- |
| Standardvärde | Inga | Inga | 18 |
| AE | Förenade Arabemiraten | Inget | 21 |
| AT | Österrike | 14 | 18 |
| BE | Belgien | 14 | 18 |
| BG | Bulgarien | 16 | 18 |
| BH | Bahrain | Inget | 21 |
| CM | Kamerun | Inget | 21 |
| CY | Cypern | 16 | 18 |
| CZ | Tjeckien | 16 | 18 |
| DE | Tyskland | 16 | 18 |
| DK | Danmark | 16 | 18 |
| EE | Estland | 16 | 18 |
| EG | Egypten | Inget | 21 |
| ES | Spanien | 13 | 18 |
| FR | Frankrike | 16 | 18 |
| GB | Storbritannien | 13 | 18 |
| GR | Grekland | 16 | 18 |
| HR | Kroatien | 16 | 18 |
| HU | Ungern | 16 | 18 |
| IE | Irland | 13 | 18 |
| IT | Italien | 16 | 18 |
| KR | Sydkorea | 14 | 18 |
| LT | Litauen | 16 | 18 |
| LU | Luxemburg | 16 | 18 |
| LV | Lettland | 16 | 18 |
| MT | Malta | 16 | 18 |
| NA | Namibia | Inget | 21 |
| NL | Nederländerna | 16 | 18 |
| PL | Polen | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Rumänien | 16 | 18 |
| SE | Sverige | 13 | 18 |
| SG | Singapore | Inget | 21 |
| SI | Slovenien | 16 | 18 |
| SK | Slovakien | 16 | 18 |
| TD | Tchad | Inget | 21 |
| TH | Thailand | Inget | 20 |
| TW | Taiwan | Inget | 20 |
| USA | USA | 13 | 18 |



## <a name="capture-terms-of-use-agreement"></a>Sammanställ användnings villkors avtal

När du utvecklar ditt program fångar du normalt användarnas godkännande av användnings villkoren i sina program utan eller bara mindre, medverkan från användar katalogen. Det är möjligt att använda ett Azure AD B2C användar flöde för att samla in en användares godkännande av användnings villkor, begränsa åtkomsten om godkännande inte beviljas och verkställa godkännande av framtida ändringar i användnings villkoren, baserat på datum för senaste godkännande och datum för den senaste versionen av användnings villkoren.

**Användnings villkoren** kan också innehålla "samtycke att dela data med tredje part". Beroende på lokala regler och affärs regler kan du samla in en användares godkännande av båda villkoren tillsammans, eller så kan du tillåta att användaren accepterar ett villkor och inte det andra.

Följande steg beskriver hur du kan hantera användnings villkor:

1. Registrera godkännandet av användnings villkoren och datum för godkännande med hjälp av Graph API och utökade attribut. Du kan göra detta med hjälp av både inbyggda och anpassade användar flöden. Vi rekommenderar att du skapar och använder **extension_termsOfUseConsentDateTime** och **extension_termsOfUseConsentVersion** attribut.

2. Skapa en obligatorisk kryss ruta med namnet "acceptera användnings villkor" och registrera resultatet under registreringen. Du kan göra detta med hjälp av både inbyggda och anpassade användar flöden.

3. Azure AD B2C lagrar villkoren för användnings villkoren och användarens godkännande. Du kan använda Graph API för att fråga efter status för en användare genom att läsa attributet för tillägg som används för att registrera svaret (till exempel läsa **termsOfUseTestUpdateDateTime**). Du kan göra detta med hjälp av både inbyggda och anpassade användar flöden.

4. Kräv godkännande av uppdaterade användnings villkor genom att jämföra datum för godkännande till datumet för den senaste versionen av användnings villkoren. Du kan bara jämföra datumen med hjälp av ett anpassat användar flöde. Använd **extension_termsOfUseConsentDateTime** för utökade attribut och jämför värdet med anspråket för **termsOfUseTextUpdateDateTime**. Om godkännandet är gammalt tvingar du ett nytt godkännande genom att visa en egen kontrollerad skärm. Annars blockerar du åtkomst med hjälp av princip logik.

5. Kräv godkännande av uppdaterade användnings villkor genom att jämföra versions numret för godkännandet med det senaste godkända versions numret. Du kan bara jämföra versions nummer genom att använda ett anpassat användar flöde. Använd **extension_termsOfUseConsentDateTime** för utökade attribut och jämför värdet med **extension_termsOfUseConsentVersion**-anspråket. Om godkännandet är gammalt tvingar du ett nytt godkännande genom att visa en egen kontrollerad skärm. Annars blockerar du åtkomst med hjälp av princip logik.

Du kan samla in användnings villkoren i följande scenarier:

- En ny användare registrerar sig. Användnings villkoren visas och godkännande resultatet lagras.
- En användare loggar in som tidigare har godkänt de senaste eller aktiva användnings villkoren. Användnings villkoren visas inte.
- En användare loggar in som inte redan har accepterat de senaste eller aktiva användnings villkoren. Användnings villkoren visas och godkännande resultatet lagras.
- En användare loggar in som redan har accepterat en äldre version av användnings villkoren, som nu har uppdaterats till den senaste versionen. Användnings villkoren visas och godkännande resultatet lagras.

Följande bild visar det rekommenderade användar flödet:

![Flödes diagram diagram som visar det rekommenderade användar flödet för godkännande](./media/manage-user-access/user-flow.png)

Följande är ett exempel på en datum-baserade användnings villkor i ett anspråk. Om `extension_termsOfUseConsentDateTime` anspråket är äldre än `2025-01-15T00:00:00` , tvinga ett nytt godkännande genom att kontrol lera det `termsOfUseConsentRequired` booleska anspråket och visa en egen kontrollerad skärm. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Följande är ett exempel på en versions beroende användnings villkor i ett anspråk. Om `extension_termsOfUseConsentVersion` anspråket inte är lika med `V1` , tvinga ett nytt godkännande genom att kontrol lera det `termsOfUseConsentRequired` booleska anspråket och visa en egen kontrollerad skärm.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Nästa steg

- [Aktivera ålders hantera i Azure AD B2C](age-gating.md).
- Information om hur du tar bort och exporterar användar data finns i [hantera användar data](manage-user-data.md).
- Ett exempel på en anpassad princip som implementerar en användnings villkors fråga finns i [en B2C IEF anpassad princip, registrera dig och logga in med "användnings villkor"](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).