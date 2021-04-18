---
title: Princip för anspråksmappning
titleSuffix: Microsoft identity platform
description: Lär dig mer om principtypen för anspråksmappning, som används för att ändra anspråk som genereras i token som utfärdats för specifika program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 04/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 56e855bafa70360711f3e30a7c4527091af7b34c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601248"
---
# <a name="claims-mapping-policy-type"></a>Principtyp för anspråksmappning

I Azure AD representerar ett **principobjekt** en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje typ av princip har en unik struktur med en uppsättning egenskaper som sedan tillämpas på objekt som de har tilldelats.

En princip för anspråksmappning är en typ av **principobjekt** som ändrar anspråken som genereras i [token som utfärdats](active-directory-claims-mapping.md) för specifika program.

## <a name="claim-sets"></a>Anspråksuppsättningar

Det finns vissa uppsättningar anspråk som definierar hur och när de används i token.

| Anspråksuppsättning | Description |
|---|---|
| Grundläggande anspråksuppsättning | Finns i varje token oavsett princip. Dessa anspråk anses också vara begränsade och kan inte ändras. |
| Grundläggande anspråksuppsättning | Innehåller anspråk som genereras som standard för token (utöver den grundläggande anspråksuppsättningen). Du kan utelämna eller ändra grundläggande anspråk med hjälp av anspråksmappningsprinciperna. |
| Begränsad anspråksuppsättning | Det går inte att ändra med hjälp av en princip. Datakällan kan inte ändras och ingen transformering tillämpas när dessa anspråk genereras. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabell 1: JSON Web Token (JWT) begränsad anspråksuppsättning

| Anspråkstyp (namn) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Skådespelare |
| actortoken |
| Aio |
| altsecid |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| Appid |
| appidacr |
| Påstående |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| Cnf |
| kod |
| kontroller |
| credential_keys |
| Csr |
| csr_type |
| Deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-post |
| slutpunkt |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| Diagram |
| group_sids |
| grupper |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| Iat |
| identityprovider |
| Idp |
| in_corp |
| Instans |
| ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| Nbf |
| netbios_name |
| Nonce |
| Oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| roll |
| roller |
| omfång |
| Scp |
| Sid |
| Signatur |
| signin_state |
| src1 |
| src2 |
| Sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| Upn |
| user_setting_sync_url |
| användarnamn |
| Uvi |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabell 2: SAML-begränsad anspråksuppsättning

| Anspråkstyp (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Principegenskaper för anspråksmappning

Du kan styra vilka anspråk som genereras och var data kommer från genom att använda egenskaperna för en princip för anspråksmappning. Om en princip inte har angetts utfärdar systemet token som innehåller den grundläggande anspråksuppsättningen, den grundläggande [anspråksuppsättningen](active-directory-optional-claims.md) och eventuella valfria anspråk som programmet har valt att ta emot.

> [!NOTE]
> Anspråk i den grundläggande anspråksuppsättningen finns i varje token, oavsett vad den här egenskapen är inställd på.

### <a name="include-basic-claim-set"></a>Inkludera grundläggande anspråksuppsättning

**Sträng:** IncludeBasicClaimSet

**Datatyp:** Booleskt (sant eller falskt)

**Sammanfattning:** Den här egenskapen avgör om den grundläggande anspråksuppsättningen ingår i token som påverkas av den här principen.

- Om den ställs in på Sant genereras alla anspråk i den grundläggande anspråksuppsättningen i token som påverkas av principen.
- Om det är inställt på False (Falskt) finns anspråk i den grundläggande anspråksuppsättningen inte i token, såvida de inte läggs till individuellt i anspråksschemaegenskapen för samma princip.



### <a name="claims-schema"></a>Anspråksschema

**Sträng:** ClaimsSchema

**Datatyp:** JSON-blob med en eller flera anspråksschemaposter

**Sammanfattning:** Den här egenskapen definierar vilka anspråk som finns i de token som påverkas av principen, förutom den grundläggande anspråksuppsättningen och den grundläggande anspråksuppsättningen.
För varje post i anspråksschemat som definieras i den här egenskapen krävs viss information. Ange var data kommer från (**Värde,** **Källa/ID-par** eller **Source/ExtensionID-par**) och vilka anspråk som data genereras som (**Anspråkstyp**).

### <a name="claim-schema-entry-elements"></a>Element för inmatning av anspråksschema

**Värde:** Elementet Value definierar ett statiskt värde som de data som ska genereras i anspråket.

**Käll-/ID-par:** Käll- och ID-elementen definierar var data i anspråket kommer från.

**Source/ExtensionID-par:** Elementen Source och ExtensionID definierar attributet för katalogschematillägget där data i anspråket kommer från. Mer information finns i Använda [attribut för katalogschematillägg i anspråk.](active-directory-schema-extensions.md)

Ange source-elementet till något av följande värden:

- "användare": Data i anspråket är en egenskap för objektet User.
- "application": Data i anspråket är en egenskap i programmets (klientens) huvudnamn för tjänsten.
- "resurs": Data i anspråket är en egenskap för resurstjänstens huvudnamn.
- "målgrupp": Data i anspråket är en egenskap för tjänstens huvudnamn som är målgruppen för token (antingen klienten eller resurstjänstens huvudnamn).
- "företag": Data i anspråket är en egenskap för resursklientorganisationens företagsobjekt.
- "transformation": Data i anspråket kommer från anspråksomvandling (se avsnittet "Anspråksomvandling" längre fram i den här artikeln).

Om källan är transformation måste **TransformationID-elementet** även inkluderas i den här anspråksdefinitionen.

ID-elementet identifierar vilken egenskap i källan som tillhandahåller värdet för anspråket. I följande tabell visas värdena för ID:t som är giltigt för varje värde för Källa.

#### <a name="table-3-valid-id-values-per-source"></a>Tabell 3: Giltiga ID-värden per källa

| Källa | ID | Description |
|-----|-----|-----|
| Användare | surname | Familjenamn |
| Användare | givenname | Förnamn |
| Användare | displayname (visningsnamn) | Visningsnamn |
| Användare | Objectid | ObjectID |
| Användare | e-post | E-postadress |
| Användare | userprincipalname | UPN (User Principal Name) |
| Användare | avdelning|Avdelning|
| Användare | onpremisessamaccountname | Namn på lokalt SAM-konto |
| Användare | netbiosname| NetBios-namn |
| Användare | dnsdomainname | DNS-domännamn |
| Användare | onpremisesecurityidentifier | Lokal säkerhetsidentifierare |
| Användare | companyname| Organisationsnamn |
| Användare | streetaddress | Gatuadress |
| Användare | Postnummer | Postnummer |
| Användare | preferredlanguage | Önskat språk |
| Användare | onpremisesuserprincipalname | Lokalt UPN |
| Användare | mailnickname | Smeknamn för e-post |
| Användare | extensionattribute1 | Tilläggsattribut 1 |
| Användare | extensionattribute2 | Tilläggsattribut 2 |
| Användare | extensionattribute3 | Tilläggsattribut 3 |
| Användare | extensionattribute4 | Tilläggsattribut 4 |
| Användare | extensionattribute5 | Tilläggsattribut 5 |
| Användare | extensionattribute6 | Tilläggsattribut 6 |
| Användare | extensionattribute7 | Tilläggsattribut 7 |
| Användare | extensionattribute8 | Tilläggsattribut 8 |
| Användare | extensionattribute9 | Tilläggsattribut 9 |
| Användare | extensionattribute10 | Tilläggsattribut 10 |
| Användare | extensionattribute11 | Tilläggsattribut 11 |
| Användare | extensionattribute12 | Tilläggsattribut 12 |
| Användare | extensionattribute13 | Tilläggsattribut 13 |
| Användare | extensionattribute14 | Tilläggsattribut 14 |
| Användare | extensionattribute15 | Tilläggsattribut 15 |
| Användare | annan e-post | Annan e-post |
| Användare | land | Land/region |
| Användare | city | City |
| Användare | state | Tillstånd |
| Användare | jobtitle | Befattning |
| Användare | employeeid | Anställnings-ID |
| Användare | facsimiletelephonenumber | Telefaksimilt telefonnummer |
| Användare | assignedroles | lista över approller som tilldelats till användare|
| program, resurs, målgrupp | displayname (visningsnamn) | Visningsnamn |
| program, resurs, målgrupp | Objectid | ObjectID |
| program, resurs, målgrupp | tags | Tagg för tjänstens huvudnamn |
| Företag | tenantcountry | Klientorganisationens land/region |

**TransformationID:** TransformationID-elementet får bara anges om källelementet har angetts till "transformation".

- Det här elementet måste matcha ID-elementet för transformeringsposten i **egenskapen ClaimsTransformation** som definierar hur data för det här anspråket genereras.

**Anspråkstyp:** Elementen **JwtClaimType** och **SamlClaimType** definierar vilket anspråk som den här anspråksschemaposten refererar till.

- JwtClaimType måste innehålla namnet på anspråket som ska genereras i JWT.
- SamlClaimType måste innehålla URI:en för anspråket för att genereras i SAML-token.

* **onPremisesUserPrincipalName-attribut:** När du använder ett alternativt ID synkroniseras det lokala attributet userPrincipalName med Azure AD-attributet onPremisesUserPrincipalName. Det här attributet är endast tillgängligt när alternativt ID har konfigurerats men är även tillgängligt via MS Graph Beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Namn och URI:er för anspråk i den begränsade anspråksuppsättningen kan inte användas för anspråkstypelementen. Mer information finns i avsnittet "Undantag och begränsningar" senare i den här artikeln.

### <a name="claims-transformation"></a>Anspråkstransformering

**Sträng:** ClaimsTransformation

**Datatyp:** JSON-blob med en eller flera transformeringsposter

**Sammanfattning:** Använd den här egenskapen för att tillämpa vanliga transformationer på källdata för att generera utdata för anspråk som anges i anspråksschemat.

**ID:** Använd ID-elementet för att referera till den här transformeringsposten i posten TransformationID Claims Schema (Transformerings-ID-anspråksschema). Det här värdet måste vara unikt för varje transformeringspost i den här principen.

**TransformationMethod:** Elementet TransformationMethod identifierar vilken åtgärd som utförs för att generera data för anspråket.

Baserat på den valda metoden förväntas en uppsättning indata och utdata. Definiera indata och utdata med hjälp av **elementen InputClaims,** **InputParameters** **och OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabell 4: Transformeringsmetoder och förväntade indata och utdata

|TransformationMethod|Förväntade indata|Förväntad utdata|Description|
|-----|-----|-----|-----|
|Anslut|string1, string2, separator|outputClaim|Sammanfogar indatasträngar med hjälp av en avgränsare däremellan. Till exempel: string1:" foo@bar.com " , string2:"sandbox" , separator:"." results in outputClaim:" foo@bar.com.sandbox "|
|ExtractMailPrefix|E-post eller UPN|extraherad sträng|ExtensionAttributes 1–15 eller andra schematillägg som lagrar ett UPN- eller e-postadressvärde för användaren, t.ex. johndoe@contoso.com . Extraherar den lokala delen av en e-postadress. Till exempel: mail:" foo@bar.com " results in outputClaim:"foo". Om det \@ inte finns något tecken returneras den ursprungliga indatasträngen som den är.|

**InputClaims:** Använd ett InputClaims-element för att skicka data från en post i anspråksschemat till en transformering. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId är** sammanslaget med ID-elementet i anspråksschemaposten för att hitta rätt inkommande anspråk.
- **TransformationClaimType** används för att ge indata ett unikt namn. Det här namnet måste matcha en av de förväntade indata för transformeringsmetoden.

**InputParameters:** Använd ett InputParameters-element för att skicka ett konstant värde till en transformering. Den har två attribut: **Värde** och **ID**.

- **Värdet** är det faktiska konstantvärde som ska skickas.
- **ID** används för att ge indata ett unikt namn. Namnet måste matcha en av de förväntade indata för transformeringsmetoden.

**OutputClaims:** Använd ett OutputClaims-element för att lagra data som genereras av en transformering och koppla dem till en post i anspråksschemat. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId är** ansluten med ID:t för anspråksschemaposten för att hitta lämpligt utgående anspråk.
- **TransformationClaimType** används för att ge utdata ett unikt namn. Namnet måste matcha ett av de förväntade utdata för transformeringsmetoden.

### <a name="exceptions-and-restrictions"></a>Undantag och begränsningar

**SAML NameID och UPN:** Attributen som du använder som källa för NameID- och UPN-värden samt de anspråksomvandlar som tillåts är begränsade. Se tabell 5 och tabell 6 för att se de tillåtna värdena.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabell 5: Attribut som tillåts som en datakälla för SAML NameID

|Källa|ID|Description|
|-----|-----|-----|
| Användare | e-post|E-postadress|
| Användare | userprincipalname|UPN (User Principal Name)|
| Användare | onpremisessamaccountname|Namn på lokalt Sam-konto|
| Användare | employeeid|Anställnings-ID|
| Användare | extensionattribute1 | Tilläggsattribut 1 |
| Användare | extensionattribute2 | Tilläggsattribut 2 |
| Användare | extensionattribute3 | Tilläggsattribut 3 |
| Användare | extensionattribute4 | Tilläggsattribut 4 |
| Användare | extensionattribute5 | Tilläggsattribut 5 |
| Användare | extensionattribute6 | Tilläggsattribut 6 |
| Användare | extensionattribute7 | Tilläggsattribut 7 |
| Användare | extensionattribute8 | Tilläggsattribut 8 |
| Användare | extensionattribute9 | Tilläggsattribut 9 |
| Användare | extensionattribute10 | Tilläggsattribut 10 |
| Användare | extensionattribute11 | Tilläggsattribut 11 |
| Användare | extensionattribute12 | Tilläggsattribut 12 |
| Användare | extensionattribute13 | Tilläggsattribut 13 |
| Användare | extensionattribute14 | Tilläggsattribut 14 |
| Användare | extensionattribute15 | Tilläggsattribut 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabell 6: Transformeringsmetoder som tillåts för SAML NameID

| TransformationMethod | Begränsningar |
| ----- | ----- |
| ExtractMailPrefix | Inget |
| Anslut | Suffixet som ska vara ansluten måste vara en verifierad domän för resursklientorganisationen. |

### <a name="cross-tenant-scenarios"></a>Scenarier mellan klientorganisation

Anspråksmappningsprinciper gäller inte för gästanvändare. Om en gästanvändare försöker komma åt ett program med en princip för anspråksmappning som tilldelats dess huvudnamn för tjänsten utfärdas standardtoken (principen har ingen effekt).


## <a name="next-steps"></a>Nästa steg

- Information om hur du anpassar anspråk som genereras i token för ett specifikt program i deras klientorganisation med hjälp av PowerShell finns i Så här anpassar du anspråk som genereras i token för en specifik app i en [klientorganisation (förhandsversion)](active-directory-claims-mapping.md)
- Information om hur du anpassar anspråk som utfärdats i SAML-token via Azure Portal finns i Så här anpassar du anspråk som utfärdas i [SAML-token för företagsprogram](active-directory-saml-claims-customization.md)
- Mer information om tilläggsattribut finns i [Använda attribut för katalogschematillägg i anspråk.](active-directory-schema-extensions.md)
