---
title: Attribut för användar profil i Azure Active Directory B2C
description: Lär dig mer om de attribut för användar resurs typ som stöds av användar profilen för Azure AD B2C Directory. Lär dig mer om inbyggda attribut, tillägg och hur mappar mappas till Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500537"
---
# <a name="user-profile-attributes"></a>Attribut för användarprofil

Din Azure Active Directory (Azure AD) B2C-katalogens användar profil innehåller en inbyggd uppsättning attribut, till exempel namn, efter namn, stad, post nummer och telefonnummer. Du kan utöka användar profilen med dina egna program data utan att kräva ett externt data lager. 

De flesta av de attribut som kan användas med Azure AD B2C användar profiler stöds också av Microsoft Graph. I den här artikeln beskrivs stöd för Azure AD B2C användar profil. Den noterar också de attribut som inte stöds av Microsoft Graph, samt Microsoft Graph attribut som inte ska användas med Azure AD B2C.

> [!IMPORTANT]
> Du bör inte använda inbyggda eller tilläggsfiler för att lagra känsliga person uppgifter, t. ex. kontoautentiseringsuppgifter, myndighets identifierings nummer, kort data, ekonomiska konto data, hälso vård information eller känslig bakgrunds information.

Du kan också integrera med externa system. Du kan till exempel använda Azure AD B2C för autentisering, men delegera till en extern kund Relations hantering (CRM) eller kund förmåns databas som auktoritativ källa till kunddata. Mer information finns i [fjär profil](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) lösningen.

## <a name="azure-ad-user-resource-type"></a>Resurs typ för Azure AD-användare

I tabellen nedan visas de egenskaper för [användar resurs typ](/graph/api/resources/user) som stöds av användar profilen för Azure AD B2C Directory. Den innehåller följande information om varje attribut:

- Attributnamn som används av Azure AD B2C (följt av Microsoft Graph namn inom parentes, om annat)
- Attributets datatyp
- Beskrivning av attribut
- Om attributet är tillgängligt i Azure Portal
- Om attributet kan användas i ett användar flöde
- Om attributet kan användas i en anpassad princip för [Azure AD-teknisk profil](active-directory-technical-profile.md) och i vilket avsnitt ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; eller &lt; PersistedClaims &gt; )

|Namn     |Typ     |Beskrivning|Azure Portal|Användarflöden|Anpassad princip|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolesk|Om användar kontot är aktiverat eller inaktiverat: **Sant** om kontot är aktiverat, annars **falskt**.|Ja|Inga|Bestående, utdata|
|ageGroup        |Sträng|Användarens ålders grupp. Möjliga värden: null, odefinierad, minor, vuxen, NotAdult.|Ja|Inga|Bestående, utdata|
|alternativeSecurityId ([identiteter](#identities-attribute))|Sträng|En enskild användar identitet från den externa identitets leverantören.|Inga|Inga|Indata, bestående, utdata|
|alternativeSecurityIds ([identiteter](#identities-attribute))|alternativ securityId-samling|En samling användar identiteter från externa identitets leverantörer.|Inga|Inga|Bestående, utdata|
|city            |Sträng|Den ort där användaren befinner sig. Maxlängd 128.|Ja|Ja|Bestående, utdata|
|consentProvidedForMinor|Sträng|Om medgivande har angetts för en mindre. Tillåtna värden: null, beviljat, nekat eller notRequired.|Ja|Inga|Bestående, utdata|
|land         |Sträng|Landet/regionen där användaren finns. Exempel: "oss" eller "Storbritannien". Maxlängd 128.|Ja|Ja|Bestående, utdata|
|createdDateTime|DateTime|Det datum då användarobjektet skapades. Skrivskyddad.|Inga|Inga|Bestående, utdata|
|creationType    |Sträng|Om användar kontot har skapats som ett lokalt konto för en Azure Active Directory B2C klient, är värdet LocalAccount eller nameCoexistence. Skrivskyddad.|Inga|Inga|Bestående, utdata|
|dateOfBirth     |Datum|Födelsedatum.|Inga|Inga|Bestående, utdata|
|avdelning      |Sträng|Namnet på den avdelning där användaren arbetar. Maxlängd 64.|Ja|Inga|Bestående, utdata|
|displayName     |Sträng|Användarens visnings namn. Maxlängd 256.|Ja|Ja|Bestående, utdata|
|facsimileTelephoneNumber<sup>1</sup>|Sträng|Telefonnumret till användarens företags Fax maskin.|Ja|Inga|Bestående, utdata|
|förnamn       |Sträng|Användarens förnamn (förnamn). Maxlängd 64.|Ja|Ja|Bestående, utdata|
|jobTitle        |Sträng|Användarens jobb titel. Maxlängd 128.|Ja|Ja|Bestående, utdata|
|immutableId     |Sträng|En identifierare som vanligt vis används för användare som migrerats från lokala Active Directory.|Inga|Inga|Bestående, utdata|
|legalAgeGroupClassification|Sträng|Juridisk ålders grupps klassificering. Skrivskyddad och beräknas utifrån egenskaperna ageGroup och consentProvidedForMinor. Tillåtna värden: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult och vuxen.|Ja|Inga|Bestående, utdata|
|legalCountry<sup>1</sup>  |Sträng|Land/region för juridiskt syfte.|Inga|Inga|Bestående, utdata|
|e-post            |Sträng|SMTP-adressen till användaren, till exempel " bob@contoso.com ". Skrivskyddad.|Inga|Inga|Bestående, utdata|
|smeknamn för e-post    |Sträng|Användarens e-postalias. Maxlängd 64.|Inga|Inga|Bestående, utdata|
|mobil (mobilePhone) |Sträng|Det primära mobiltelefon telefonnumret för användaren. Maxlängd 64.|Ja|Inga|Bestående, utdata|
|netId           |Sträng|NET ID.|Inga|Inga|Bestående, utdata|
|objectId        |Sträng|En globalt unik identifierare (GUID) som är den unika identifieraren för användaren. Exempel: 12345678-9ABC-def0-1234-56789abcde. Skrivskyddad, oföränderlig.|Skrivskyddad|Ja|Indata, bestående, utdata|
|otherMails      |Sträng samling|En lista över andra e-postadresser för användaren. Exempel: [" bob@contoso.com ", " Robert@fabrikam.com "].|Ja (alternativ e-post)|Inga|Bestående, utdata|
|password        |Sträng|Lösen ordet för det lokala kontot när användaren skapas.|Inga|Inga|Beständiga|
|passwordPolicies     |Sträng|Princip för lösen ordet. Det är en sträng som består av ett annat princip namn, avgränsat med kommatecken. Till exempel "DisablePasswordExpiration, DisableStrongPassword".|Inga|Inga|Bestående, utdata|
|physicalDeliveryOfficeName (officeLocation)|Sträng|Arbets platsen på användarens arbets plats. Maxlängd 128.|Ja|Inga|Bestående, utdata|
|postalCode      |Sträng|Post numret för användarens post adress. Post numret är speciellt för användarens land/region. I USA i Amerika innehåller det här attributet post numret. Maxlängd 40.|Ja|Inga|Bestående, utdata|
|preferredLanguage    |Sträng|Det föredragna språket för användaren. Det önskade språk formatet baseras på RFC 4646. Namnet är en kombination av en versal i ISO 639 2-tecken som är associerad med språket och en ISO 3166 2-bokstav under kultur kod som är associerad med landet eller regionen. Exempel: "en-US" eller "es-ES".|Inga|Inga|Bestående, utdata|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|Alla uppdateringstoken som utfärdats före den här tiden är ogiltiga och program får ett fel meddelande när en ogiltig uppdateringstoken används för att hämta en ny åtkomsttoken. Om detta händer måste programmet skaffa en ny uppdateringstoken genom att göra en begäran till behörighets slut punkten. Skrivskyddad.|Inga|Inga|Utdata|
|signInNames ([identiteter](#identities-attribute)) |Sträng|Det unika inloggnings namnet för den lokala konto användaren av valfri typ i katalogen. Använd det här attributet för att få en användare med inloggnings värde utan att ange den lokala konto typen.|Inga|Inga|Indata|
|signInNames. userName ([identiteter](#identities-attribute)) |Sträng|Unikt användar namn för den lokala konto användaren i katalogen. Använd det här attributet för att skapa eller hämta en användare med ett särskilt inloggnings användar namn. Om du anger detta i PersistedClaims under korrigerings åtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också behålla befintlig signInNames.|Inga|Inga|Indata, bestående, utdata|
|signInNames. telefonnummer ([identiteter](#identities-attribute)) |Sträng|Det unika telefonnumret för den lokala konto användaren i katalogen. Använd det här attributet för att skapa eller hämta en användare med ett särskilt inloggnings telefonnummer. Om du anger det här attributet i PersistedClaims under korrigerings åtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också behålla befintlig signInNames.|Inga|Inga|Indata, bestående, utdata|
|signInNames. emailAddress ([identiteter](#identities-attribute))|Sträng|Den unika e-postadressen för den lokala konto användaren i katalogen. Använd detta för att skapa eller hämta en användare med en speciell e-postadress för inloggning. Om du anger det här attributet i PersistedClaims under korrigerings åtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också behålla befintlig signInNames.|Inga|Inga|Indata, bestående, utdata|
|state           |Sträng|Region i användarens adress. Maxlängd 128.|Ja|Ja|Bestående, utdata|
|streetAddress   |Sträng|Gatuadressen till användarens arbets plats. Maxlängd 1024.|Ja|Ja|Bestående, utdata|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Sträng|Användarens sekundära telefonnummer, som används för Multi-Factor Authentication.|Ja|Inga|Bestående, utdata|
|strongAuthenticationEmailAddress<sup>1</sup>|Sträng|SMTP-adressen för användaren. Exempel: " bob@contoso.com " Detta attribut används för inloggning med användar namn princip för att lagra användarens e-postadress. E-postadressen används sedan i ett flöde för återställning av lösen ord.|Ja|Inga|Bestående, utdata|
|strongAuthenticationPhoneNumber<sup>2</sup>|Sträng|Användarens primära telefonnummer, som används för Multi-Factor Authentication.|Ja|Inga|Bestående, utdata|
|surname         |Sträng|Användarens efter namn (familje namn eller efter namn). Maxlängd 64.|Ja|Ja|Bestående, utdata|
|telephoneNumber (första posten i businessPhones)|Sträng|Det primära telefonnumret till användarens arbets plats.|Ja|Inga|Bestående, utdata|
|userPrincipalName    |Sträng|Användarens huvudnamn (UPN). UPN är ett inloggnings namn för användaren som baseras på Internet standard RFC 822. Domänen måste finnas i klientens samling med verifierade domäner. Den här egenskapen krävs när ett konto skapas. Inte kan ändras.|Inga|Inga|Indata, bestående, utdata|
|usageLocation   |Sträng|Krävs för användare som ska tilldelas licenser på grund av juridiskt krav för att kontrol lera tillgängligheten för tjänster i länder/regioner. Kan inte ha värdet null. En lands-/regionkod i två bokstäver (ISO standard 3166). Exempel: "US", "JP" och "GB".|Ja|Inga|Bestående, utdata|
|userType        |Sträng|Ett sträng värde som kan användas för att klassificera användar typer i din katalog. Värdet måste vara medlem. Skrivskyddad.|Skrivskyddad|Inga|Bestående, utdata|
|userState (externalUserState)<sup>3</sup>|Sträng|För Azure AD B2B-konto anger du om inbjudan är PendingAcceptance eller accepterad.|Inga|Inga|Bestående, utdata|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Visar tidsstämpeln för den senaste ändringen av egenskapen UserState.|Inga|Inga|Bestående, utdata|

<sup>1 </sup> Stöds inte av Microsoft Graph<br><sup>2 </sup> Mer information finns i [attribut för MFA-telefonnummer](#mfa-phone-number-attribute)<br><sup>3 </sup> Ska inte användas med Azure AD B2C

## <a name="display-name-attribute"></a>Attribut för visnings namn

`displayName`Är namnet som ska visas i Azure Portal användar hantering för användaren och i åtkomsttoken Azure AD B2C återgår till programmet. Den här egenskapen är obligatorisk.

## <a name="identities-attribute"></a>Identities-attribut

Ett kund konto, som kan vara en konsument, partner eller medborgare, kan associeras med dessa identitets typer:

- **Lokal** identitet – användar namn och lösen ord lagras lokalt i Azure AD B2C-katalogen. Vi refererar ofta till dessa identiteter som "lokala konton".
- **Federerad** identitet – även kallat *sociala* eller *företags* konton, användarens identitet hanteras av en federerad identitets leverantör som Facebook, Microsoft, ADFS eller Salesforce.

En användare med ett kund konto kan logga in med flera identiteter. Till exempel användar namn, e-post, medarbetar-ID, myndighets-ID och andra. Ett enda konto kan ha flera identiteter, både lokala och sociala, med samma lösen ord. 

I Microsoft Graph API lagras både lokala och federerade identiteter i `identities` attributet User, som är av typen [objectIdentity](/graph/api/resources/objectidentity). `identities`Samlingen representerar en uppsättning identiteter som används för att logga in på ett användar konto. Den här samlingen gör att användaren kan logga in på användar kontot med någon av dess associerade identiteter. Attributet identiteter kan innehålla upp till tio [objectIdentity](/graph/api/resources/objectidentity) -objekt. Varje-objekt innehåller följande egenskaper:

| Namn   | Typ |Beskrivning|
|:---------------|:--------|:----------|
|signInType|sträng| Anger användarnas inloggnings typer i din katalog. För lokalt konto:,,,,  `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3`  `userName` eller någon annan typ som du vill ha. Socialt konto måste anges till  `federated` .|
|utfärdare|sträng|Anger utfärdarens identitet. För lokala konton (där **signInType** inte är `federated` det) är den här egenskapen namnet på den lokala B2C-klientens standard domän, till exempel `contoso.onmicrosoft.com` . För sociala identiteter (där **signInType** är  `federated` ) är värdet namnet på utfärdaren, till exempel `facebook.com`|
|issuerAssignedId|sträng|Anger den unika identifierare som användaren har tilldelats av utfärdaren. Kombinationen av **utfärdare** och **issuerAssignedId** måste vara unik inom din klient organisation. För lokalt konto, när **signInType** är inställt på `emailAddress` eller `userName` , representerar det inloggnings namnet för användaren.<br>När **signInType** är inställt på: <ul><li>`emailAddress` (eller börjar med `emailAddress` gilla `emailAddress1` ) **issuerAssignedId** måste vara en giltig e-postadress</li><li>`userName` (eller något annat värde) måste **issuerAssignedId** vara en giltig [lokal del av en e-postadress](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`representerar **issuerAssignedId** det federerade kontots unika identifierare</li></ul>|

Följande **Identities** -attribut, med en lokal konto identitet med ett inloggnings namn, en e-postadress som inloggning och med en social identitet. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

För federerade identiteter, beroende på identitets leverantören, är **issuerAssignedId** ett unikt värde för en viss användare per program eller utvecklings konto. Konfigurera Azure AD B2C principen med samma program-ID som tidigare har tilldelats av den sociala leverantören eller något annat program inom samma utvecklings konto. 

## <a name="password-profile-property"></a>Egenskap för lösen ords profil

För en lokal identitet krävs attributet **passwordProfile** och det innehåller användarens lösen ord. `forceChangePasswordNextSignIn`Attributet anger om en användare måste återställa lösen ordet vid nästa inloggning. Om du vill hantera en Tvingad återställning av lösen ord [ställer du in flödet för Tvingad återställning av lösen ord](force-password-reset.md)

För en federerad (social) identitet krävs inte attributet **passwordProfile** .

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Attribut för lösen ords princip

Azure AD B2C lösen ords princip (för lokala konton) baseras på principen för Azure Active Directory [stark lösen ords styrka](../active-directory/authentication/concept-sspr-policy.md) . Principerna Azure AD B2C för registrering eller inloggning och lösen ords återställning kräver den här starka lösen ords styrkan och upphör aldrig att gälla lösen ord.

Om de konton som du vill migrera har en mindre lösen ords styrka än den [starka lösen ords styrkan](../active-directory/authentication/concept-sspr-policy.md) som tillämpas av Azure AD B2C, kan du inaktivera kravet på starkt lösen ord i scenarier med användar migrering. Ange attributet till om du vill ändra standard lösen ords principen `passwordPolicies` `DisableStrongPassword` . Du kan till exempel ändra begäran om att skapa användare på följande sätt:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Attribut för MFA-telefonnummer

När du använder en telefon för Multi-Factor Authentication (MFA) används mobil telefonen för att verifiera användar identiteten. Om du vill [lägga till](/graph/api/authentication-post-phonemethods) ett nytt telefonnummer program mässigt, [Uppdatera](/graph/api/b2cauthenticationmethodspolicy-update), [Hämta](/graph/api/b2cauthenticationmethodspolicy-get)eller [ta bort](/graph/api/phoneauthenticationmethod-delete) telefonnumret, använder du [metoden MS Graph API Phone Authentication](/graph/api/resources/phoneauthenticationmethod).

I Azure AD B2C [anpassade principer](custom-policy-overview.md)är telefonnumret tillgängligt via `strongAuthenticationPhoneNumber` anspråks typ.

## <a name="extension-attributes"></a>Attribut för tillägg

Varje kundriktad app har unika krav för att informationen ska samlas in. Din Azure AD B2C-klient har en inbyggd uppsättning information som lagras i egenskaper, till exempel namn, efter namn och post nummer. Med Azure AD B2C kan du utöka uppsättningen med egenskaper som lagras i varje kund konto. Mer information finns i [lägga till användarattribut och anpassa användarindata i Azure Active Directory B2C](configure-user-input.md)

Utökade attribut [utökar schemat](/graph/extensibility-overview#schema-extensions) för användar objekt i katalogen. Attributen för tillägg kan bara registreras på ett program objekt, även om de kan innehålla data för en användare. Attributet Extension är kopplat till programmet som kallas `b2c-extensions-app` . Ändra inte det här programmet eftersom det används av Azure AD B2C för att lagra användar data. Du kan hitta det här programmet under Azure Active Directory Appregistreringar.

> [!NOTE]
> - Attributen upp till 100 kan skrivas till alla användar konton.
> - Om programmet B2C-Extensions-app tas bort, tas dessa tilläggsfiler bort från alla användare tillsammans med alla data som de innehåller.
> - Om ett attribut för tillägg tas bort av programmet tas det bort från alla användar konton och värdena tas bort.

Attribut för tillägg i Graph API namnges med hjälp av konventionen `extension_ApplicationClientID_AttributeName` , där `ApplicationClientID` är **programmets (klient) ID** för `b2c-extensions-app` programmet (hittas i **Appregistreringar**  >  **alla program** i Azure Portal). Observera att **program-ID: t (klient)** som det visas i attributets namn för tillägg inte innehåller några bindestreck. Exempel:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Följande data typer stöds när du definierar ett attribut i ett schema tillägg:

|Typ |Kommentarer  |
|--------------|---------|
|Boolesk    | Möjliga värden: **True** eller **false**. |
|DateTime   | Måste anges i formatet ISO 8601. Kommer att lagras i UTC.   |
|Integer    | 32-bitars värde.               |
|Sträng     | högst 256 tecken.     |

## <a name="next-steps"></a>Nästa steg

Läs mer om attribut för tillägg:

- [Schemautökningar](/graph/extensibility-overview#schema-extensions)
- [Definiera anpassade attribut](user-flow-custom-attributes.md)
