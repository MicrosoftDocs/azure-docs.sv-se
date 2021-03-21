---
title: ClaimsSchema – Azure Active Directory B2C | Microsoft Docs
description: Ange ClaimsSchema-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ff43408cfa6d95dbd5a235a950269c47d57a416
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654038"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsSchema**-elementet definierar de anspråkstyper som refereras till som en del av principen. Anspråksschemat är den plats där du deklarerar dina anspråk. Ett anspråk kan vara förnamn, efter namn, visnings namn, telefonnummer och mycket annat. ClaimsSchema-elementet innehåller en lista över **claimType** -element. Elementet **claimType** innehåller **ID-** attributet, som är anspråks namnet.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

Elementet **claimType** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för anspråks typen. Andra element kan använda den här identifieraren i principen. |

Elementet **claimType** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Rubriken som visas för användare på olika skärmar. Värdet kan [lokaliseras](localization.md). |
| DataType | 1:1 | Anspråkets typ. |
| DefaultPartnerClaimTypes | 0:1 | Partnerns standard anspråks typer som används för ett angivet protokoll. Värdet kan skrivas över i **PartnerClaimType** som anges i **InputClaim** -eller **OutputClaim** -elementen. Använd det här elementet för att ange standard namnet för ett protokoll.  |
| Mask | 0:1 | En valfri sträng med maskering av tecken som kan användas när anspråket visas. Telefonnumret 324-232-4343 kan till exempel maskeras som XXX-XXX-4343. |
| UserHelpText | 0:1 | En beskrivning av anspråks typen som kan vara till hjälp för användarna att förstå syftet. Värdet kan [lokaliseras](localization.md). |
| UserInputType | 0:1 | Den typ av inmatnings kontroll som ska vara tillgänglig för användaren när anspråks data anges manuellt för anspråks typen. Se de indatatyps typer som definierats senare på den här sidan. |
| AdminHelpText | 0:1 | En beskrivning av anspråks typen som kan vara till hjälp för administratörer att förstå dess syfte. |
| Begränsning | 0:1 | Värde begränsningarna för detta anspråk, till exempel ett reguljärt uttryck (regex) eller en lista med acceptabla värden. Värdet kan [lokaliseras](localization.md). |
PredicateValidationReference| 0:1 | En referens till ett **PredicateValidationsInput** -element. Med **PredicateValidationReference** -elementen kan du utföra en verifierings process för att säkerställa att endast korrekt utformade data anges. Mer information finns i [predikat](predicates.md). |



### <a name="datatype"></a>DataType

Elementet **datatype** stöder följande värden:

| Typ | Beskrivning |
| ------- | ----------- |
|boolean|Representerar ett booleskt `true` värde (eller `false` ).|
|date| Representerar en omedelbar tid, vanligt vis uttrycks som ett datum på en dag. Värdet för datumet följer ISO 8601-konventionen.|
|dateTime|Representerar en omedelbar tid, vanligt vis uttryckt som datum och tid på dagen. Värdet för datumet följer ISO 8601-konventionen.|
|varaktighet|Representerar ett tidsintervall i år, månader, dagar, timmar, minuter och sekunder. Formatet är `PnYnMnDTnHnMnS` , där `P` anger positiv eller `N` för negativt värde. `nY` är antalet år följt av en literal `Y` . `nMo` är antalet månader följt av en literal `Mo` . `nD` är antalet dagar följt av en literal `D` . Exempel: `P21Y` representerar 21 år. `P1Y2Mo` representerar ett år och två månader. `P1Y2Mo5D` representerar ett år, två månader och fem dagar.  `P1Y2M5DT8H5M620S` representerar ett år, två månader, fem dagar, åtta timmar, fem minuter och tjugo sekunder.  |
|phoneNumber|Representerar ett telefonnummer. |
|int| Representerar talet mellan-2 147 483 648 och 2 147 483 647|
|long| Representerar talet mellan-9 och 9 223 372 036 854 775 807 |
|sträng| Representerar text som en sekvens med UTF-16-kodade enheter.|
|stringCollection|Representerar en samling `string` .|
|userIdentity| Representerar en användar identitet.|
|userIdentityCollection|Representerar en samling `userIdentity` .|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** kan innehålla följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Protokoll | 1: n | Lista med protokoll med sitt standard namn för partner anspråks typ. |

**Protokoll** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Name | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C. Möjliga värden är: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Ja | Namnet på anspråks typen som ska användas. |

I följande exempel, när ett identitets Miljös ramverk interagerar med en SAML2-identitetsprovider **eller ett** förlitande parts program, mappas kravet till `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` , med OpenIdConnect och OAuth2, anspråket mappas till `family_name` .

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Det innebär att JWT-token som utfärdas av Azure AD B2C, genererar i `family_name` stället för efter namn på claimType-namn.

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

**Maskeringslagret** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Type` | Ja | Typ av anspråks mask. Möjliga värden: `Simple` eller `Regex` . `Simple`Värdet anger att en enkel textmask används för den inledande delen av ett sträng anspråk. `Regex`Värdet anger att ett reguljärt uttryck används för sträng anspråket som helhet.  Om `Regex` värdet har angetts måste även ett valfritt attribut definieras med det reguljära uttrycket som ska användas. |
| `Regex` | Inga | Om **`Type`** är inställt på `Regex` anger du det reguljära uttrycket som ska användas.

I följande exempel konfigureras ett **telefonnummer för telefonnummer** till `Simple` masken:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Med Identity Experience Framework återges telefonnumret när de första sex siffrorna döljs:

![Telefonnummer som visas i webbläsare med första sex siffror som maskeras av XS](./media/claimsschema/mask.png)

I följande exempel konfigureras ett **AlternateEmail** -anspråk med `Regex` masken:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

I ramverket med identitets upplevelsen återges bara den första bokstaven i e-postadressen och domän namnet för e-post:

![E-postanspråk visas i webbläsare med tecken maskerade med asterisker](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Begränsning

**Begränsnings** elementet kan innehålla följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MergeBehavior | Inga | Den metod som används för att sammanfoga uppräknings värden med en ClaimType i en överordnad princip med samma identifierare. Använd det här attributet när du skriver över ett anspråk som anges i bas principen. Möjliga värden: `Append` , `Prepend` , eller `ReplaceAll` . `Append`Värdet är en samling data som ska läggas till i slutet av den samling som anges i den överordnade principen. `Prepend`Värdet är en samling data som ska läggas till före den samling som anges i den överordnade principen. `ReplaceAll`Värdet är en samling data som anges i den överordnade principen som ska ignoreras. |

**Begränsnings** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Uppräkning | 1: n | De tillgängliga alternativen i användar gränssnittet för användaren att välja för ett anspråk, till exempel ett värde i en listruta. |
| Mönster | 1:1 | Det reguljära uttryck som ska användas. |

#### <a name="enumeration"></a>Uppräkning

**Uppräknings** elementet definierar tillgängliga alternativ för användaren att välja för ett anspråk i användar gränssnittet, t. ex. ett värde i en `CheckboxMultiSelect` , `DropdownSingleSelect` , eller `RadioSingleSelect` . Alternativt kan du definiera och lokalisera tillgängliga alternativ med [LocalizedCollections](localization.md#localizedcollections) -element. Om du vill söka efter ett objekt från en anspråks **uppräknings** samling använder du [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) Claims-transformering.

**Uppräknings** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Text | Ja | Den visnings sträng som visas för användaren i användar gränssnittet för det här alternativet. |
|Värde | Ja | Anspråks värde som är associerat med att välja det här alternativet. |
| SelectByDefault | Inga | Anger om det här alternativet ska vara markerat som standard i användar gränssnittet. Möjliga värden: true eller false. |

I följande exempel konfigureras List rutan för en **stad** med ett standardvärde som är inställt på `New York` :

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

List rutan stad med ett standardvärde som är inställt på New York:

![List Rute kontroll återges i webbläsare och visar standardvärdet](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Mönster

**Pattern** -elementet kan innehålla följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Reguljärt uttryck | Ja | Det reguljära uttrycket som anspråk av den här typen måste matcha för att vara giltigt. |
| HelpText | Inga | Ett fel meddelande för användare om den reguljära uttrycks kontrollen Miss lyckas. |

I följande exempel konfigureras ett **e-** postanspråk med text verifiering och hjälp text i reguljärt uttryck:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
  <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

Med Identity Experience Framework återges e-postadressen med e-postverifiering:

![Text ruta som visar fel meddelande som utlöses av regex-begränsning](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C stöder flera olika typer av användarindata, till exempel en text ruta, ett lösen ord och en listruta som kan användas när du anger anspråks data manuellt för anspråks typen. Du måste ange **UserInputType** när du samlar in information från användaren med hjälp av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md) och [visnings kontroller](display-controls.md).

**UserInputType** -elementet är tillgängligt för användarens indata typer:

| UserInputType | ClaimType som stöds | Beskrivning |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Listruta för flera val. Anspråks värde representeras i en kommaavgränsad sträng för de valda värdena. |
|DateTimeDropdown | `date`, `dateTime` |List rutor för att välja dag, månad och år. |
|DropdownSingleSelect |`string` |Listruta för enkel markering. Anspråks värde är det valda värdet.|
|E-postmeddelande | `string` |E-postfält. |
|Stycke | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Ett fält som endast visar text i en stycke-tagg. |
|Lösenord | `string` |Text rutan lösen ord.|
|RadioSingleSelect |`string` | Samling alternativ knappar. Anspråks värde är det valda värdet.|
|ReadOnly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Skrivskyddad text ruta. |
|TextBox |`boolean`, `int`, `string` |Text ruta med en rad. |


#### <a name="textbox"></a>TextBox

Indatatypen för **text Rute** användaren används för att ange en text ruta med en rad.

![Text ruta som visar egenskaper som anges i anspråks typ](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>E-postmeddelande

**E-postmeddelandets** Indatatyp används för att ange ett grundläggande e-postfält.

![E-postmeddelande som visar egenskaper som anges i anspråks typ](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Lösenord

**Användarens** Indatatyp används för att registrera ett lösen ord som anges av användaren.

![Använda anspråks typ med lösen ord](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

**DateTimeDropdown** -användarens Indatatyp används för att ange en uppsättning List rutor för att välja dag, månad och år. Du kan använda predikat och PredicateValidations-element för att kontrol lera minimi-och Max datum värden. Mer information finns i avsnittet **Konfigurera ett datum intervall** i [predikat och PredicateValidations](predicates.md).

![Använda anspråks typ med datetimedropdown](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

**RadioSingleSelect** -användarens Indatatyp används för att tillhandahålla en samling alternativ knappar som gör att användaren kan välja ett alternativ.

![Använda anspråks typ med radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Indatatypen **DropdownSingleSelect** används för att tillhandahålla en nedrullningsbar listruta där användaren kan välja ett alternativ.

![Använda anspråks typ med dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Indatatypen **CheckboxMultiSelect** används för att tillhandahålla en samling kryss rutor som gör det möjligt för användaren att välja flera alternativ.

![Använda anspråks typ med checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>ReadOnly

Den **skrivskyddade** användarens Indatatyp används för att tillhandahålla ett skrivskyddat fält för att Visa anspråket och värdet.

![Använda anspråks typ med ReadOnly](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Stycke

Indatatypen **stycke** användare används för att tillhandahålla ett fält som endast visar text i en stycke-tagg.  Till exempel &lt; p &gt; text &lt; /p &gt; . En indatatyp för **stycke** användare `OutputClaim` av självkontrollerad teknisk profil, måste ange `Required` attributet `false` (standard).

![Använda anspråks typ med stycke](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
