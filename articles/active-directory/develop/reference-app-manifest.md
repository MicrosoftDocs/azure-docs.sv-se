---
title: Förstå Azure Active Directory app-manifestet
titleSuffix: Microsoft identity platform
description: Detaljerad täckning av Azure Active Directory app-manifestet som representerar ett programs identitets konfiguration i en Azure AD-klient och används för att under lätta OAuth-auktorisering, medgivande upplevelse och mycket annat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 02/02/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 47215070893c6fefd42554fdf4514e4aa8b45047
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103353"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-appmanifest

Applikations manifestet innehåller en definition av alla attribut för ett program objekt i Microsoft Identity Platform. Den fungerar också som en mekanism för att uppdatera programobjektet. Mer information om programentiteten och dess schema finns i dokumentationen för [Graph API application Entity](/graph/api/resources/application).

Du kan konfigurera en Apps attribut via Azure Portal eller program mässigt med hjälp av [REST API](/graph/api/resources/application) eller [PowerShell](/powershell/module/azuread#applications). Det finns dock vissa scenarier där du måste redigera appens manifest för att konfigurera appens attribut. Några vanliga scenarier:

* Om du har registrerat appen som Azure AD-konto för flera klienter och personliga Microsoft-konton kan du inte ändra de Microsoft-konton som stöds i användar gränssnittet. I stället måste du använda program manifest redigeraren för att ändra den konto typ som stöds.
* Om du vill definiera behörigheter och roller som appen stöder måste du ändra applikations manifestet.

## <a name="configure-the-app-manifest"></a>Konfigurera app-manifestet

Så här konfigurerar du applikations manifestet:

1. Gå till <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>. Sök efter och välj tjänsten **Azure Active Directory** .
1. Välj **Appregistreringar**.
1. Välj den app som du vill konfigurera.
1. På appens **översiktssida** väljer du avsnittet **Manifest**. En webbaserad manifest redigerare öppnas, så att du kan redigera manifestet i portalen. Alternativt kan du välja **Ladda ned** för att redigera manifestet lokalt och sedan använda **upload** för att tillämpa det igen på ditt program.

## <a name="manifest-reference"></a>Manifest referens

I det här avsnittet beskrivs de attribut som finns i applikations manifestet.

### <a name="id-attribute"></a>ID-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| id | Sträng |

Den unika identifieraren för appen i katalogen. Detta ID är inte den identifierare som används för att identifiera appen i någon protokoll transaktion. Den används för att referera till objektet i katalog frågor.

Exempel:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| accessTokenAcceptedVersion | Nullable Int32 |

Anger den åtkomsttoken som förväntas av resursen. Den här parametern ändrar versionen och formatet på det JWT-tillverkade oberoende av slut punkten eller klienten som används för att begära åtkomsttoken.

Slut punkten som används, v 1.0 eller v 2.0 väljs av klienten och påverkar endast versionen av id_tokens. Resurser måste uttryckligen konfigureras `accesstokenAcceptedVersion` för att ange det åtkomsttoken som stöds.

Möjliga värden `accesstokenAcceptedVersion` är 1, 2 eller null. Om värdet är null är parametern standardvärdet 1, som motsvarar v 1.0-slutpunkten.

Om `signInAudience` är `AzureADandPersonalMicrosoftAccount` , måste värdet vara `2` .

Exempel:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>Addins-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| Tillägg | Samling |

Definierar ett anpassat beteende som en förbruknings tjänst kan använda för att anropa en app i vissa sammanhang. Till exempel kan program som kan rendera fil strömmar ange `addIns` egenskapen för dess "FileHandler"-funktion. Med den här parametern kan tjänster som Microsoft 365 anropa programmet i kontexten för ett dokument som användaren arbetar med.

Exempel:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| allowPublicClient | Boolesk |

Anger typ av återställnings program. Azure AD härleder program typen från replyUrlsWithType som standard. Det finns vissa scenarier där Azure AD inte kan fastställa klientens app-typ. Ett sådant scenario är till exempel det [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) -flöde där http-begäran sker utan URL-omdirigering). I dessa fall tolkar Azure AD program typen baserat på värdet för den här egenskapen. Om det här värdet är inställt på Sant anges typ av återställnings program som offentlig klient, till exempel en installerad app som körs på en mobil enhet. Standardvärdet är false, vilket innebär att återställnings program typen är konfidentiell klient, till exempel Web App.

Exempel:

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>appId-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| appId | Sträng |

Anger den unika identifieraren för den app som är tilldelad en app av Azure AD.

Exempel:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| appRoles | Samling |

Anger den samling roller som en app kan deklarera. Dessa roller kan tilldelas till användare, grupper eller tjänstens huvud namn. Fler exempel och information finns i [lägga till app-roller i ditt program och ta emot dem i token](howto-add-app-roles-in-azure-ad-apps.md).

Exempel:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="errorurl-attribute"></a>errorUrl-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| errorUrl | Sträng |

Som inte stöds.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
|groupMembershipClaims | Sträng |

Konfigurerar det `groups` anspråk som utfärdats i en användare eller OAuth 2,0-åtkomsttoken som appen förväntar sig. Ange det här attributet genom att använda något av följande giltiga sträng värden:

- `"None"`
- `"SecurityGroup"` (för säkerhets grupper och Azure AD-roller)
- `"ApplicationGroup"` (det här alternativet inkluderar endast grupper som är kopplade till programmet)
- `"DirectoryRole"` (hämtar de Azure AD-katalog roller som användaren är medlem i)
- `"All"` (detta kommer att hämta alla säkerhets grupper, distributions grupper och Azure AD-katalog roller som den inloggade användaren är medlem i).

Exempel:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>optionalClaims-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| optionalClaims | Sträng |

De valfria anspråk som returneras i token av säkerhetstokentjänsten för den här speciella appen.

För närvarande kan appar som stöder både personliga konton och Azure AD (registrerade via appens registrerings Portal) inte använda valfria anspråk. Appar som registrerats för bara Azure AD med hjälp av v 2.0-slutpunkten kan dock hämta de valfria anspråk som de begär i manifestet. Mer information finns i [valfria anspråk](active-directory-optional-claims.md).

Exempel:

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>identifierUris-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| identifierUris | Sträng mat ris |

Användardefinierade URI: er som unikt identifierar en webbapp i sin Azure AD-klient eller inom en verifierad anpassad domän om appen är flera innehavare.

Exempel:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| informationalUrls | Sträng |

Anger länkar till appens användnings villkor och sekretess policy. Användnings villkoren och sekretess policyn är tilldelad till användarna genom användar medgivande upplevelsen. Mer information finns i [så här gör du: Lägg till användnings villkor och sekretess policy för registrerade Azure AD-appar](howto-add-terms-of-service-privacy-statement.md).

Exempel:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>attribut för autentiseringsuppgifter

| Nyckel | Värdetyp |
| :--- | :--- |
| keyCredentials | Samling |

Innehåller referenser till app-tilldelade autentiseringsuppgifter, strängbaserade delade hemligheter och X. 509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomsttoken (när appen fungerar som en klient, i stället för en resurs).

Exempel:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| knownClientApplications | Sträng mat ris |

Används för att ge bunt om du har en lösning som innehåller två delar: en klient app och en anpassad webb-API-app. Om du anger appID för-klient programmet i det här värdet behöver användaren bara godkänna en gång till klient programmet. Azure AD är medveten om att samtycka till klienten innebär att implicit samtycka till webb-API: et. Tjänsten etablerar automatiskt tjänstens huvud namn för både klient-och webb-API: n på samma tidpunkt. Både klienten och webb-API-appen måste vara registrerade i samma klient organisation.

Exempel:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| logoUrl | Sträng |

Skrivskyddat värde som pekar på CDN-URL: en till en logo typ som laddades upp i portalen.

Exempel:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| logoutUrl | Sträng |

URL: en för att logga ut från appen.

Exempel:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>namnattribut

| Nyckel | Värdetyp |
| :--- | :--- |
| name | Sträng |

Appens visnings namn.

Exempel:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolesk |

Anger om den här webbappen kan begära OAuth 2.0 implicit flödes åtkomst-token. Standardvärdet är False. Den här flaggan används för webbläsarbaserade appar, t. ex. java script-appar med en sida. Mer information finns `OAuth 2.0 implicit grant flow` i innehålls förteckningen och i avsnitten om implicit flöde.

Exempel:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolesk |

Anger om den här webbappen kan begära OAuth 2.0-token för implicit flödes-ID. Standardvärdet är False. Den här flaggan används för webbläsarbaserade appar, t. ex. java script-appar med en sida.

Exempel:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2Permissions | Samling |

Anger samlingen OAuth 2,0-behörighets omfattningar som webb-API: n (resurs) visar för klient program. Dessa behörighets omfattningar kan beviljas till klient program under medgivande.

Exempel:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolesk |

Anger om, som en del av OAuth 2,0-Tokenbegäran, tillåter Azure AD att begär Anden skickas, i stället för att hämta förfrågningar. Standardvärdet är false, vilket anger att endast GET-begäranden ska tillåtas.

Exempel:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| parentalControlSettings | Sträng |

- `countriesBlockedForMinors` anger de länder/regioner där appen blockeras för minderåriga.
- `legalAgeGroupRule` anger den juridiska ålders grupps regel som gäller för användare av appen. Kan ställas in på,,, `Allow` `RequireConsentForPrivacyServices` `RequireConsentForMinors` `RequireConsentForKids` eller `BlockMinors` .

Exempel:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| passwordCredentials | Samling |

Se beskrivningen av `keyCredentials` egenskapen.

Exempel:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| preAuthorizedApplications | Samling |

Visar en lista över program och begärda behörigheter för implicit medgivande. Kräver att en administratör har gett sitt medgivande till programmet. preAuthorizedApplications kräver inte att användaren godkänner de begärda behörigheterna. Behörigheter som anges i preAuthorizedApplications kräver inte användar medgivande. Alla ytterligare begärda behörigheter som inte listas i preAuthorizedApplications kräver dock användar medgivande.

Exempel:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publisherdomain-attribute"></a>publisherDomain-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| publisherDomain | Sträng |

Den verifierade Publisher-domänen för programmet. Skrivskyddad.

Exempel:

```json
    "publisherDomain": "{tenant}.onmicrosoft.com",
```

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| replyUrlsWithType | Samling |

Den här egenskapen för flera värden innehåller en lista över registrerade redirect_uri värden som Azure AD accepterar som mål när token returneras. Varje URI-värde ska innehålla ett värde för en associerad app-typ. Typ värden som stöds är:

- `Web`
- `InstalledClient`
- `Spa`

Mer information finns i [begränsningar och begränsningar för replyUrl](./reply-url.md).

Exempel:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| requiredResourceAccess | Samling |

Med dynamiskt medgivande bevarar `requiredResourceAccess` Administratörs upplevelsen och användar medgivande upplevelsen för användare som använder det statiska godkännandet. Den här parametern styr dock inte användar medgivande upplevelsen för det allmänna fallet.

- `resourceAppId` är den unika identifieraren för den resurs som appen kräver åtkomst till. Värdet ska vara lika med det appId som deklarerats för mål resurs appen.
- `resourceAccess` är en matris som visar behörighets omfattningarna för OAuth 2.0 och de app-roller som appen kräver från den angivna resursen. Innehåller `id` värdena och `type` för de angivna resurserna.

Exempel:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| samlMetadataUrl | Sträng |

URL: en till SAML metadata för appen.

Exempel:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| signInUrl | Sträng |

Anger webb adressen till appens start sida.

Exempel:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| signInAudience | Sträng |

Anger vilka Microsoft-konton som stöds för det aktuella programmet. De värden som stöds är:
- `AzureADMyOrg` – Användare med ett arbets-eller skol konto för Microsoft i min organisations Azure AD-klient (till exempel en enskild klient)
- `AzureADMultipleOrgs` – Användare med ett arbets-eller skol konto för Microsoft i en organisations Azure AD-klient (till exempel flera innehavare)
- `AzureADandPersonalMicrosoftAccount` – Användare med en personlig Microsoft-konto eller ett arbets-eller skol konto i en organisations Azure AD-klient
- `PersonalMicrosoftAccount` – Personliga konton som används för att logga in på tjänster som Xbox och Skype.

Exempel:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>Tags-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| tags | Sträng mat ris  |

Anpassade strängar som kan användas för att kategorisera och identifiera programmet.

Exempel:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Vanliga problem

### <a name="manifest-limits"></a>Manifest gränser

Ett program manifest har flera attribut som kallas samlingar. till exempel appRoles, inloggnings uppgifter, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess och oauth2Permissions. I det fullständiga applikations manifestet för ett program, har det totala antalet poster i alla samlingar i kombination varit begränsade till 1200. Om du tidigare har angett 100 omdirigerings-URI: er i applikations manifestet är du bara kvar med 1100 återstående poster som ska användas i alla andra samlingar som utgör manifestet.

> [!NOTE]
> Om du försöker lägga till fler än 1200 poster i applikations manifestet kan du se ett fel **"Det gick inte att uppdatera programmet xxxxxx. Fel information: Manifestets storlek har överskridit gränsen. Minska antalet värden och gör om begäran. "**

### <a name="unsupported-attributes"></a>Attribut som inte stöds

Applikations manifestet representerar schemat för den underliggande program modellen i Azure AD. När det underliggande schemat utvecklas kommer manifest redigeraren att uppdateras för att avspegla det nya schemat. Därför kan det hända att du upptäcker nya attribut som visas i applikations manifestet. I sällsynta fall kan du märka en syntaktisk eller semantisk ändring i befintliga attribut, eller så kan du hitta ett attribut som tidigare inte stöds längre. Till exempel visas nya attribut i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908), som är kända med ett annat namn i Appregistreringar (äldre).

| Appregistreringar (bakåtkompatibelt)| Appregistreringar           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Beskrivningar för de här attributen finns i [referens avsnittet för manifestet](#manifest-reference) .

När du försöker överföra ett tidigare nedladdat manifest kan du se något av följande fel. Det här felet beror förmodligen på att manifest redigeraren nu stöder en nyare version av schemat, som inte överensstämmer med den som du försöker ladda upp.

* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: ogiltig objekt identifierare ' undefined '. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: ett eller flera angivna egenskaps värden är ogiltiga. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: det går inte att ange availableToOtherTenants i den här API-versionen för uppdatering. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: uppdateringar av egenskapen ' replyUrls ' tillåts inte för det här programmet. Använd egenskapen ' replyUrlsWithType ' i stället. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: ett värde utan ett typ namn hittades och ingen förväntad typ är tillgänglig. När modellen anges måste varje värde i nytto lasten ha en typ som antingen kan anges i nytto lasten, uttryckligen av anroparen eller implicit härledd från det överordnade värdet. []"

När du ser något av dessa fel rekommenderar vi följande åtgärder:

1. Redigera attributen individuellt i manifest redigeraren i stället för att ladda upp ett tidigare nedladdat manifest. Använd referens tabellen i [manifestet](#manifest-reference) för att förstå syntaxen och semantiken för gamla och nya attribut så att du kan redigera de attribut som du är intresse rad av.
1. Om arbets flödet kräver att du sparar manifesten på käll lagrings platsen för att använda senare, rekommenderar vi att du bygger in de sparade manifesten i din lagrings plats med den som visas i **Appregistreringars** upplevelsen.

## <a name="next-steps"></a>Nästa steg

* Mer information om relationen mellan appens program och tjänst huvud objekt finns i [program-och tjänst huvud objekt i Azure AD](app-objects-and-service-principals.md).
* Se [ord listan för Microsoft Identity Platform Developer](developer-glossary.md) för definitioner av vissa kärn utvecklings koncept för Microsoft Identity Platform.

Använd följande kommentarer-avsnitt för att ge feedback som hjälper dig att förfina och forma vårt innehåll.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
