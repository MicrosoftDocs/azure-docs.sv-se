---
title: Resurser för Azure API Management-mallar | Microsoft Docs
description: Lär dig mer om vilka typer av resurser som är tillgängliga för användning i mallar för utvecklare i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84689995"
---
# <a name="azure-api-management-template-resources"></a>Resurser för Azure API Management-mallar
Azure API Management tillhandahåller följande typer av resurser som du kan använda i Developer Portal-mallarna.  
  
-   [Sträng resurser](#strings)  
  
-   [Glyph-resurser](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a> Sträng resurser  
 API Management tillhandahåller en omfattande uppsättning sträng resurser som du kan använda i Developer-portalen. De här resurserna är lokaliserade till alla språk som stöds av API Management. Standard uppsättningen mallar använder dessa resurser för sidhuvuden, etiketter och eventuella konstanta strängar som visas i Developer-portalen. Om du vill använda en sträng resurs i dina mallar, anger du prefixet för resurs strängen följt av sträng namnet, som du ser i följande exempel.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Följande exempel är från produkt list mal len och visar **produkter** överst på sidan.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Följande lokaliserings alternativ stöds:

| Nationell inställning    | Språk               |
|-----------|------------------------|
| ”en”      | Amerikanska              |
| östasiatisk      | "Čeština"              |
| tillförsel      | Deutsch              |
| ES      | "Español"              |
| fr      | Frankrike             |
| HU      | Magyar               |
| företaget      | Italiano             |
| "ja-JP"   | "日本語"                |
| Ko      | "한국어"                |
| nederländska      | Nederlands           |
| pl      | Polski               |
| "pt-br"   | "Português (Brasilien)"   |
| "pt-pt"   | "Português (Portugal)" |
| |      | "Русский"              |
| sa      | Svenska              |
| TR      | "Türkçe"               |
| "zh-hans" | "中文(简体)"           |
| "zh-Hant" | "中文(繁體)"           |

 Se följande tabeller för de sträng resurser som är tillgängliga för användning i dina Developer Portal-mallar. Använd tabell namnet som prefix för sträng resurserna i tabellen.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentation](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a> ApisStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleApis|API:er|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Program för hands version|  
|WebApplicationsRequirementsHeader|Krav|  
|WebApplicationsScreenshotAlt|Skärmbild|  
|WebApplicationsScreenshotsHeader|Skärmbilder|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Är du säker på att du vill ta bort programmet?|  
|WebDevelopersAppNotPublished|Inte publicerad|  
|WebDevelopersAppNotSubmitted|Inte skickat|  
|WebDevelopersAppTableCategoryHeader|Kategori|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|Tillstånd|  
|WebDevelopersEditLink|Redigera|  
|WebDevelopersRegisterAppLink|Registrera program|  
|WebDevelopersRemoveLink|Ta bort|  
|WebDevelopersSubmitLink|Skicka|  
|WebDevelopersYourApplicationsHeader|Dina program|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a> AppStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsHeader|Program|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a> CommonResources  
  
|Name|Text|  
|----------|----------|  
|NoItemsToDisplay|Inga resultat hittades.|  
|GeneralExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt fel eller ett fel. Försök igen.|  
|GeneralJsonExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt fel eller ett fel. Läs in sidan på nytt och försök igen.|  
|ConfirmationMessageUnsavedChanges|Det finns några ändringar som inte har sparats. Är du säker på att du vill avbryta och ta bort ändringarna?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http-frågans brödtext är för stor.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a> CommonStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelCancel|Avbryt|  
|ButtonLabelSave|Spara|  
|GeneralExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt fel eller ett fel. Försök igen.|  
|NoItemsToDisplay|Det finns inga objekt att visa.|  
|PagerButtonLabelFirst|Första|  
|PagerButtonLabelLast|Sista|  
|PagerButtonLabelNext|Nästa|  
|PagerButtonLabelPrevious|Föregående|  
|PagerLabelPageNOfM|Sida {0} av {1}|  
|PasswordTooShort|Lösen ordet är för kort|  
|EmailAsPassword|Använd inte ditt e-postmeddelande som lösen ord|  
|PasswordSameAsUserName|Ditt lösen ord får inte innehålla ditt användar namn|  
|PasswordTwoCharacterClasses|Använd olika Character-klasser|  
|PasswordTooManyRepetitions|För många upprepningar|  
|PasswordSequenceFound|Ditt lösen ord innehåller sekvenser|  
|PagerLabelPageSize|Sidstorlek|  
|CurtainLabelLoading|Läser in ...|  
|TablePlaceholderNothingToDisplay|Det finns inga data för den valda perioden och omfattningen|  
|ButtonLabelClose|Stäng|  
  
###  <a name="documentation"></a><a name="Documentation"></a> Handlingar  
  
|Name|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Ogiltigt huvud ' {0} '|  
|WebDocumentationInvalidRequestErrorMessage|Ogiltig URL för begäran|  
|TextboxLabelAccessToken|Åtkomsttoken *|  
|DropdownOptionPrimaryKeyFormat|Huvud{0}|  
|DropdownOptionSecondaryKeyFormat|Alternativ{0}|  
|WebDocumentationSubscriptionKeyText|Din prenumerations nyckel|  
|WebDocumentationTemplatesAddHeaders|Lägg till nödvändiga HTTP-huvuden|  
|WebDocumentationTemplatesBasicAuthSample|Exempel på grundläggande auktorisering|  
|WebDocumentationTemplatesCurlForBasicAuth|för grundläggande auktorisering använder du:--Användare {username}: {Password}|  
|WebDocumentationTemplatesCurlValuesForPath|Ange värden för Sök vägs parametrar (visas som {...}), din prenumerations nyckel och värden för frågeparametrar|  
|WebDocumentationTemplatesDeveloperKey|Ange din prenumerations nyckel|  
|WebDocumentationTemplatesJavaApache|I det här exemplet används Apache HTTP-klienten från HTTP-komponenter (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Ange värden för valfria parametrar efter behov|  
|WebDocumentationTemplatesPhpPackage|I det här exemplet används HTTP_Request2-paketet. (mer information: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Ange värden för Sök vägs parametrar (visas som {...}) och begär ande text om det behövs|  
|WebDocumentationTemplatesRequestBody|Ange brödtext för begäran|  
|WebDocumentationTemplatesRequiredParams|Ange värden för följande obligatoriska parametrar|  
|WebDocumentationTemplatesValuesForPath|Ange värden för Sök vägs parametrar (visas som {...})|  
|OAuth2AuthorizationEndpointDescription|Slut punkten för auktorisering används för att interagera med resurs ägaren och erhålla en auktorisering.|  
|OAuth2AuthorizationEndpointName|Behörighets slut punkt|  
|OAuth2TokenEndpointDescription|Token-slutpunkten används av klienten för att hämta en åtkomsttoken genom att presentera dess Authorization Grant-eller Refresh-token.|  
|OAuth2TokenEndpointName|Token-slutpunkt|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p \>         klienten initierar flödet genom att dirigera resurs ägarens användar agent till behörighets slut punkten.  Klienten innehåller klient-ID, begärt omfång, lokalt tillstånd och en omdirigerings-URI som auktoriseringsservern skickar tillbaka användar agenten till när åtkomst beviljas (eller nekas).     </p \>     <p \>         autentiserar auktoriseringsservern resurs ägaren (via användar agenten) och anger om resurs ägaren beviljar eller nekar klientens åtkomst förfrågan.     </p \>     <p \>         under förutsättning att resurs ägaren beviljar åtkomst, omdirigerar auktoriseringsservern tillbaka användar agenten till klienten med hjälp av den omdirigerings-URI som tillhandahölls tidigare (i förfrågan eller under klient registreringen).  Omdirigerings-URI: n innehåller en auktoriseringskod och alla lokala tillstånd som tillhandahålls av klienten tidigare.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     om användaren nekar åtkomstbegäran om begäran är ogiltig, kommer klienten att informeras med följande parametrar som läggs till i omdirigeringen: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Auktoriseringsbegäran|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p \>         klient appen måste skicka användaren till behörighets slut punkten för att kunna initiera OAuth-processen.          Användaren autentiserar och beviljar eller nekar åtkomst till appen på slut punkten för auktoriseringen.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     under förutsättning att resurs ägaren beviljar åtkomst omdirigerar auktoriseringsservern användar agenten till klienten med hjälp av den omdirigerings-URI som tillhandahölls tidigare (i förfrågan eller under klient registreringen).  Omdirigerings-URI: n innehåller en auktoriseringskod och alla lokala tillstånd som tillhandahålls av klienten tidigare. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p \>  klienten begär en åtkomsttoken från Authorization-serverns token-slutpunkt genom att inkludera den auktoriseringskod som togs emot i föregående steg.  När begäran görs autentiseras klienten med auktoriseringsservern.  Klienten inkluderar den omdirigerings-URI som används för att hämta verifierings koden. </p \> <p \>     autentiserar-klienten, verifierar auktoriseringskod och säkerställer att omdirigerings-URI: n tas emot matchar den URI som används för att omdirigera klienten i steg (C).  Om detta är giltigt, svarar auktoriseringsservern igen med en åtkomsttoken och eventuellt en uppdateringstoken. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p \>     om autentiseringen av begär ande klienten misslyckades eller är ogiltig, svarar auktoriseringsservern med status koden HTTP 400 (felaktig begäran) (om inget annat anges) och innehåller följande parametrar med svaret. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p \>   klienten gör en begäran till token-slutpunkten genom att skicka följande parametrar med hjälp av formatet "Application/x-www-form-urlencoded" med en tecken kodning av UTF-8 i entiteten http-begäran-brödtext. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p \>  -auktoriseringsservern utfärdar en åtkomsttoken och en valfri uppdateringstoken, och skapar svaret genom att lägga till följande parametrar till entitetens brödtext i HTTP-svaret med en 200 (OK)-status kod. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p \>  klienten autentiseras med auktoriseringsservern och begär en åtkomsttoken från slut punkten för token. </p \> <p \>  autentiserar auktoriseringsservern klienten, och om det är giltigt utfärdar en åtkomsttoken. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>     om begäran misslyckades med klientautentisering eller om den är ogiltig, svarar auktoriseringsservern med en HTTP 400-statuskod (felaktig begäran) (om inget annat anges) och innehåller följande parametrar med svaret. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>   klienten gör en begäran till token-slutpunkten genom att lägga till följande parametrar med hjälp av formatet "Application/x-www-form-urlencoded" med en tecken kodning av UTF-8 i entiteten http-begäran-brödtext. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>  om begäran om åtkomsttoken är giltig och auktoriserad, utfärdar auktoriseringsservern en åtkomsttoken och en valfri uppdateringstoken, och skapar svaret genom att lägga till följande parametrar till entitetens brödtext i HTTP-svaret med en 200 (OK) status kod. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p \>   klienten initierar flödet genom att dirigera resurs ägarens användar agent till behörighets slut punkten.  Klienten innehåller klient-ID, begärt omfång, lokalt tillstånd och en omdirigerings-URI som auktoriseringsservern skickar tillbaka användar agenten till när åtkomst beviljas (eller nekas). </p \> <p \>        autentiserar auktoriseringsservern resurs ägaren (via användar agenten) och anger om resurs ägaren beviljar eller nekar klientens åtkomstbegäran. </p \> <p \>        under förutsättning att resurs ägaren beviljar åtkomst, omdirigerar auktoriseringsservern tillbaka användar agenten till klienten med hjälp av den omdirigerings-URI som tillhandahölls tidigare.  URI: n för omdirigering inkluderar åtkomsttoken i URI-fragmentet. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     om resurs ägaren avvisar åtkomstbegäran eller om begäran Miss lyckas av andra orsaker än en saknad eller ogiltig omdirigerings-URI, informerar auktoriseringsservern klienten genom att lägga till följande parametrar i fragment-komponenten för omdirigerings-URI: n med hjälp av formatet "Application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p \>     klient appen måste skicka användaren till behörighets slut punkten för att kunna initiera OAuth-processen.      Användaren autentiserar och beviljar eller nekar åtkomst till appen på slut punkten för auktoriseringen. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     om resurs ägaren beviljar åtkomstbegäran, utfärdar auktoriseringsservern en åtkomsttoken och levererar den till klienten genom att lägga till följande parametrar i fragment-komponenten för omdirigerings-URI: n med hjälp av formatet "Application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Auktoriseringskod-flödet är optimerat för klienter som kan upprätthålla sekretessen för sina autentiseringsuppgifter (t. ex. webb serverprogram som implementeras med PHP, Java, python, ruby, ASP.NET osv.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Tillåt auktoriseringskod|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Flöde för klientautentiseringsuppgifter är lämpligt i fall där klienten (ditt program) begär åtkomst till de skyddade resurserna under dess kontroll. Klienten betraktas som en resurs ägare, så det krävs ingen åtgärd från slutanvändaren.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Beviljande av klientautentiseringsuppgifter|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit flöde är optimerat för klienter som inte kan bibehålla konfidentialiteten för sina autentiseringsuppgifter som är kända för att använda en viss omdirigerings-URI. Dessa klienter är vanligt vis implementerade i en webbläsare med hjälp av ett skript språk, till exempel Java Script.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit beviljande|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Flöde för autentiseringsuppgifter för resurs ägare är lämpligt i fall där resurs ägaren har en förtroende relation med klienten (ditt program), till exempel enhetens operativ system eller ett program med hög privilegier. Det här flödet är lämpligt för klienter som kan erhålla resurs ägarens autentiseringsuppgifter (användar namn och lösen ord, vanligt vis med hjälp av ett interaktivt formulär).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Tilldela autentiseringsuppgifter för resurs ägarens lösen ord|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p \>   resurs ägaren förser klienten med sitt användar namn och lösen ord. </p \> <p \> klienten begär en åtkomsttoken från Authorization-serverns token-slutpunkt genom att inkludera de autentiseringsuppgifter som tagits emot från resurs ägaren.  När begäran görs autentiseras klienten med auktoriseringsservern. </p \> <p \>     autentiserar-servern klienten och verifierar autentiseringsuppgifter för resurs ägare, och om det är giltigt utfärdar en åtkomsttoken. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>  om begäran misslyckades med klientautentisering eller om den är ogiltig, svarar auktoriseringsservern med en HTTP 400-statuskod (felaktig begäran) (om inget annat anges) och innehåller följande parametrar med svaret. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>    klienten gör en begäran till token-slutpunkten genom att lägga till följande parametrar med hjälp av formatet "Application/x-www-form-urlencoded" med en tecken kodning av UTF-8 i entiteten http-begäran-brödtext. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>   om begäran om åtkomsttoken är giltig och auktoriserad, utfärdar auktoriseringsservern en åtkomsttoken och en valfri uppdateringstoken, och skapar svaret genom att lägga till följande parametrar till entitetens brödtext i HTTP-svaret med en 200 (OK) status kod. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Begäran om åtkomsttoken|  
|OAuth2Step_AuthorizationRequest_Name|Auktoriseringsbegäran|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Kunna. Den åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Kunna. Den åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Kunna. Den åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Kunna. Den åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Kunna. Klient-ID.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|KRÄVS om klienten inte autentiseras med auktoriseringsservern.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Kunna. Klient-ID.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Kunna. Den auktoriseringskod som genereras av auktoriseringsservern.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Kunna. Auktoriseringskod som togs emot från auktoriseringsservern.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Valfritt. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Valfritt. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Valfritt. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Valfritt. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Valfritt. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Valfritt. En URI som identifierar en webb sida som är läslig med information om felet.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Valfritt. En URI som identifierar en webb sida som är läslig med information om felet.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Valfritt. En URI som identifierar en webb sida som är läslig med information om felet.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Valfritt. En URI som identifierar en webb sida som är läslig med information om felet.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Valfritt. En URI som identifierar en webb sida som är läslig med information om felet.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Kunna. En enkel ASCII-felkod från följande: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Kunna. En enkel ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Kunna. En enkel ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Kunna. En enkel ASCII-felkod från följande: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Kunna. En enkel ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Rekommenderas. Livs längden i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Rekommenderas. Livs längden i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Rekommenderas. Livs längden i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Rekommenderas. Livs längden i sekunder för åtkomsttoken.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Kunna. Värdet måste anges till authorization_code.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Kunna. Värdet måste anges till client_credentials.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Kunna. Värdet måste anges till "Password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Kunna. Resurs ägarens lösen ord.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Valfritt. Slut punktens URI för omdirigering måste vara en absolut URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|KRÄVS om parametern "redirect_uri" ingår i auktoriseringsbegäran och deras värden måste vara identiska.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Valfritt. Slut punktens URI för omdirigering måste vara en absolut URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Valfritt. Uppdateringstoken som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Valfritt. Uppdateringstoken som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Valfritt. Uppdateringstoken som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Kunna. Värdet måste anges till Code.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Kunna. Värdet måste anges till token.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Valfritt. Omfattningen för åtkomstbegäran.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|VALFRITT om identiskt med det scope som begärs av klienten. Annars krävs.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Valfritt. Omfattningen för åtkomstbegäran.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|VALFRITT, om det är identiskt med det omfång som begärs av klienten. Annars krävs.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Valfritt. Omfattningen för åtkomstbegäran.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|VALFRITT om identiskt med det scope som begärs av klienten. Annars krävs.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Valfritt. Omfattningen för åtkomstbegäran.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VALFRITT, om det är identiskt med det omfång som begärs av klienten. Annars krävs.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|KRÄVS om "State"-parametern fanns i begäran om klient godkännande.  Det exakta värde som tagits emot från klienten.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Rekommenderas. Ett ogenomskinligt värde som används av klienten för att bevara tillstånd mellan begäran och motringning.  Auktoriseringsservern innehåller det här värdet när du omdirigerar användar agenten till klienten igen.  Parametern ska användas för att förhindra förfalskning av begäran mellan webbplatser.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|KRÄVS om "State"-parametern fanns i begäran om klient godkännande.  Det exakta värde som tagits emot från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|KRÄVS om "State"-parametern fanns i begäran om klient godkännande.  Det exakta värde som tagits emot från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Rekommenderas. Ett ogenomskinligt värde som används av klienten för att bevara tillstånd mellan begäran och motringning.  Auktoriseringsservern innehåller det här värdet när du omdirigerar användar agenten till klienten igen.  Parametern ska användas för att förhindra förfalskning av begäran mellan webbplatser.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|KRÄVS om "State"-parametern fanns i begäran om klient godkännande.  Det exakta värde som tagits emot från klienten.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Kunna. Typ av token som utfärdats.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Kunna. Typ av token som utfärdats.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Kunna. Typ av token som utfärdats.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Kunna. Typ av token som utfärdats.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Kunna. Resurs ägarens användar namn.|  
|OAuth2UnsupportedTokenType|Tokentyp {0} stöds inte.|  
|OAuth2InvalidState|Ogiltigt svar från auktoriseringsservern|  
|OAuth2GrantType_AuthorizationCode|Authorization code (Auktoriseringskod)|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Klientautentiseringsuppgifter|  
|OAuth2GrantType_ResourceOwnerPassword|Resurs ägar lösen ord|  
|WebDocumentation302Code|302 hittades|  
|WebDocumentation400Code|400 (felaktig begäran)|  
|OAuth2SendingMethod_AuthHeader|Authorization-huvud|  
|OAuth2SendingMethod_QueryParam|Frågeparameter|  
|OAuth2AuthorizationServerGeneralException|Ett fel uppstod vid auktorisering av åtkomst via {0}|  
|OAuth2AuthorizationServerCommunicationException|Det gick inte att upprätta en HTTP-anslutning till auktoriseringsservern eller så har den stängts av på oväntad sätt.|  
|WebDocumentationOAuth2GeneralErrorMessage|Ett oväntat fel inträffade.|  
|AuthorizationServerCommunicationException|Ett fel inträffade i kommunikations servern. Kontakta administratören.|  
|TextblockSubscriptionKeyHeaderDescription|Prenumerations nyckel som ger åtkomst till detta API. Hittades i din <a href = '/Developer '- \> profil</a \> .|  
|TextblockOAuthHeaderDescription|OAuth 2,0-åtkomsttoken hämtades från <i \> {0}</i \> . Beviljande typer som stöds: <i \> {1}</i \> .|  
|TextblockContentTypeHeaderDescription|Medietyp för texten som skickas till API: et.|  
|ErrorMessageApiNotAccessible|Det API som du försöker anropa är inte tillgängligt just nu. Kontakta API-utgivaren <a href = "/issues" \> här</a \> .|  
|ErrorMessageApiTimedout|Det API som du försöker anropa tar längre tid än normalt för att få svar tillbaka. Kontakta API-utgivaren <a href = "/issues" \> här</a \> .|  
|BadRequestParameterExpected|parametern "" " {0} förväntas|  
|TooltipTextDoubleClickToSelectAll|Dubbelklicka för att markera alla.|  
|TooltipTextHideRevealSecret|Visa/Dölj|  
|ButtonLinkOpenConsole|Prova|  
|SectionHeadingRequestBody|Begärandetext|  
|SectionHeadingRequestParameters|Parametrar för begäran|  
|SectionHeadingRequestUrl|Begärans-URL|  
|SectionHeadingResponse|Svarsåtgärder|  
|SectionHeadingRequestHeaders|Begärandehuvuden|  
|FormLabelSubtextOptional|valfri|  
|SectionHeadingCodeSamples|Kodexempel|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect ID-token hämtades från <i \> {0}</i \> . Beviljande typer som stöds: <i \> {1}</i \> .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelBack|tillbaka|  
|LinkLabelHomePage|Start sida|  
|LinkLabelSendUsEmail|Skicka oss ett e-postmeddelande|  
|PageTitleError|Det gick tyvärr inte att betjäna den begärda sidan|  
|TextblockPotentialCauseIntermittentIssue|Detta kan vara ett tillfälligt problem med data åtkomst som redan är borta.|  
|TextblockPotentialCauseOldLink|Länken du har klickat på kan vara gammal och inte peka på rätt plats längre.|  
|TextblockPotentialCauseTechnicalProblem|Det kan finnas ett tekniskt problem på vår sida.|  
|TextblockPotentialSolutionRefresh|Prova med att uppdatera sidan.|  
|TextblockPotentialSolutionStartOver|Börja om från vår {0} .|  
|TextblockPotentialSolutionTryAgain|Gå {0} till och försök utföra åtgärden igen.|  
|TextReportProblem|{0} att beskriva vad som gick fel och vi kommer att titta på det så snart vi kan.|  
|TitlePotentialCause|Möjlig orsak|  
|TitlePotentialSolution|Det är förmodligen bara ett tillfälligt problem, några saker att prova|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a> IssuesStrings  
  
|Name|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Ärenden|  
|WebIssuesNoActiveSubscriptions|Du har inga aktiva prenumerationer. Du måste prenumerera på en produkt för att rapportera ett problem.|  
|WebIssuesNotSignin|Du är inte inloggad. {0}Rapportera ett problem eller publicera en kommentar.|  
|WebIssuesReportIssueButton|Rapportera problem|  
|WebIssuesSignIn|logga in|  
|WebIssuesStatusReportedBy|Status: {0} &#124; rapporteras av {1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelHomePage|Start sida|  
|LinkLabelSendUsEmail|Skicka oss ett e-postmeddelande|  
|PageTitleNotFound|Vi kan tyvärr inte hitta sidan du letar efter|  
|TextblockPotentialCauseMisspelledUrl|Du kan ha felstavat URL-adressen om du har angett den i.|  
|TextblockPotentialCauseOldLink|Länken du har klickat på kan vara gammal och inte peka på rätt plats längre.|  
|TextblockPotentialSolutionRetype|Försök att skriva om URL: en.|  
|TextblockPotentialSolutionStartOver|Börja om från vår {0} .|  
|TextReportProblem|{0} att beskriva vad som gick fel och vi kommer att titta på det så snart vi kan.|  
|TitlePotentialCause|Möjlig orsak|  
|TitlePotentialSolution|Potentiell lösning|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebProductsAgreement|Genom att prenumerera på {0} produkten godkänner jag `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>` .|  
|WebProductsLegalTermsLink|Användningsvillkor|  
|WebProductsSubscribeButton|Prenumerera|  
|WebProductsUsageLimitsHeader|Användningsgränser|  
|WebProductsYouAreNotSubscribed|Du prenumererar på den här produkten.|  
|WebProductsYouRequestedSubscription|Du begärde prenumerationen på den här produkten.|  
|ErrorYouNeedToAgreeWithLegalTerms|Du måste godkänna användnings villkoren innan du kan fortsätta.|  
|ButtonLabelAddSubscription|Lägg till en prenumeration|  
|LinkLabelChangeSubscriptionName|ändra|  
|ButtonLabelConfirm|Bekräfta|  
|TextblockMultipleSubscriptionsCount|Du har {0} prenumerationer på den här produkten:|  
|TextblockSingleSubscriptionsCount|Du har en {0} prenumeration på den här produkten:|  
|TextblockSingleApisCount|Den här produkten innehåller {0} API:|  
|TextblockMultipleApisCount|Den här produkten innehåller {0} API: er:|  
|TextblockHeaderSubscribe|Prenumerera på produkt|  
|TextblockSubscriptionDescription|En ny prenumeration kommer att skapas på följande sätt:|  
|TextblockSubscriptionLimitReached|Prenumerations gränsen har nåtts.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a> ProductsStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleProducts|Produkter|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Inloggning har inaktiverats av administratörerna för tillfället.|  
|TextboxExternalIdentitiesSigninInvitation|Du kan också logga in med|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Logga in med:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a> SigninResources  
  
|Name|Text|  
|----------|----------|  
|PrincipalNotFound|Det gick inte att hitta huvud kontot eller så är signaturen ogiltig|  
|ErrorSsoAuthenticationFailed|SSO-Autentiseringen misslyckades|  
|ErrorSsoAuthenticationFailedDetailed|Ogiltig token har angetts eller också går det inte att verifiera signaturen.|  
|ErrorSsoTokenInvalid|SSO-token är ogiltig|  
|ValidationErrorSpecificEmailAlreadyExists|E-postmeddelandet har {0} redan registrerats|  
|ValidationErrorSpecificEmailInvalid|E-postmeddelandet {0} är ogiltigt|  
|ValidationErrorPasswordInvalid|Lösen ordet är ogiltigt. Korrigera felen och försök igen.|  
|PropertyTooShort|{0} är för kort|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Ogiltig e-postadress.|  
|ValidationMessageNewPasswordConfirmationRequired|Bekräfta nytt lösen ord|  
|ValidationErrorPasswordConfirmationRequired|Bekräfta lösen ord är tomt|  
|WebAuthenticationEmailChangeNotice|Ändrings Bekräftelsens e-postadress är på väg {0} . Följ anvisningarna i den för att bekräfta din nya e-postadress. Om e-postmeddelandet inte kommer till din inkorg inom några minuter kan du kontrol lera mappen skräp post.|  
|WebAuthenticationEmailChangeNoticeHeader|Din begäran om e-poständring har bearbetats|  
|WebAuthenticationEmailChangeNoticeTitle|E-poständring begärd|  
|WebAuthenticationEmailHasBeenRevertedNotice|E-postmeddelandet finns redan. Begäran har återställts|  
|ValidationErrorEmailAlreadyExists|E-postmeddelandet finns redan|  
|ValidationErrorEmailInvalid|Ogiltig e-postadress|  
|TextboxLabelEmail|E-post|  
|ValidationErrorEmailRequired|E-postadress måste anges.|  
|WebAuthenticationErrorNoticeHeader|Fel|  
|WebAuthenticationFieldLengthErrorMessage|{0} måste vara en maximal längd på {1}|  
|TextboxLabelEmailFirstName|Förnamn|  
|ValidationErrorFirstNameRequired|Förnamn måste anges.|  
|ValidationErrorFirstNameInvalid|Ogiltigt förnamn|  
|NoticeInvalidInvitationToken|Observera att bekräftelse länkar endast är giltiga i 48 timmar. Om du fortfarande befinner dig i den här tids perioden ser du till att länken är korrekt. Om länken har upphört att gälla upprepar du åtgärden som du försöker bekräfta.|  
|NoticeHeaderInvalidInvitationToken|Ogiltig token för inbjudan|  
|NoticeTitleInvalidInvitationToken|Bekräftelse fel|  
|WebAuthenticationLastNameInvalidErrorMessage|Ogiltigt efter namn|  
|TextboxLabelEmailLastName|Efternamn|  
|ValidationErrorLastNameRequired|Efter namn måste anges.|  
|WebAuthenticationLinkExpiredNotice|En bekräftelse länk som skickats till dig har upphört att gälla. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Länken för lösen ords återställning är ogiltig eller har upphört att gälla.|  
|WebAuthenticationLinkExpiredNoticeTitle|Länk skickat|  
|WebAuthenticationNewPasswordLabel|Nytt lösenord|  
|ValidationMessageNewPasswordRequired|Nytt lösen ord krävs.|  
|TextboxLabelNotificationsSenderEmail|E-postmeddelande om avsändare|  
|TextboxLabelOrganizationName|Organisationsnamn|  
|WebAuthenticationOrganizationRequiredErrorMessage|Organisations namnet är tomt|  
|WebAuthenticationPasswordChangedNotice|Lösen ordet har uppdaterats|  
|WebAuthenticationPasswordChangedNoticeTitle|Lösen ordet uppdaterat|  
|WebAuthenticationPasswordCompareErrorMessage|Lösen orden matchar inte|  
|WebAuthenticationPasswordConfirmLabel|Bekräfta lösenordet|  
|ValidationErrorPasswordInvalidDetailed|Lösen ordet är för svagt.|  
|WebAuthenticationPasswordLabel|Lösenord|  
|ValidationErrorPasswordRequired|Lösenord måste anges.|  
|WebAuthenticationPasswordResetSendNotice|Ändra lösen ordet bekräfta att e-post är på väg {0} . Följ anvisningarna i e-postmeddelandet för att fortsätta med processen för lösen ords ändring.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Din begäran om lösen ords återställning har bearbetats|  
|WebAuthenticationPasswordResetSendNoticeTitle|Lösen ords återställning begärdes|  
|WebAuthenticationRequestNotFoundNotice|Förfrågan hittades inte|  
|WebAuthenticationSenderEmailRequiredErrorMessage|E-postmeddelandets avsändare är tomt|  
|WebAuthenticationSigninPasswordLabel|Bekräfta ändringen genom att ange ett lösen ord|  
|WebAuthenticationSignupConfirmNotice|E-postbekräftelsen är på väg {0} . <br/ \> Följ instruktionerna i e-postmeddelandet för att aktivera ditt konto. <br/ \> om e-postmeddelandet inte kommer in i din inkorg inom några minuter kan du kontrol lera mappen skräp post.|  
|WebAuthenticationSignupConfirmNoticeHeader|Ditt konto har skapats|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Ett e-postmeddelande för registrerings bekräftelse skickades igen|  
|WebAuthenticationSignupConfirmNoticeTitle|Kontot har skapats|  
|WebAuthenticationTokenRequiredErrorMessage|Token är tom|  
|WebAuthenticationUserAlreadyRegisteredNotice|Det verkar som om en användare med det här e-postmeddelandet redan är registrerad i systemet. Om du har glömt ditt lösen ord kan du försöka att återställa det eller kontakta vårt support team.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Användaren är redan registrerad|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Redan registrerad|  
|ButtonLabelChangePassword|Ändra lösenord|  
|ButtonLabelChangeAccountInfo|Ändra konto information|  
|ButtonLabelCloseAccount|Stäng konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Texten som anges matchar inte texten på bilden. Försök igen.|  
|ValidationErrorCredentialsInvalid|E-postadressen eller lösen ordet är ogiltigt. Korrigera felen och försök igen.|  
|WebAuthenticationRequestIsNotValid|Begäran är inte giltig|  
|WebAuthenticationUserIsNotConfirm|Bekräfta registreringen innan du försöker logga in.|  
|WebAuthenticationInvalidEmailFormated|E-postmeddelandet är ogiltigt: {0}|  
|WebAuthenticationUserNotFound|Användaren kunde inte hittas|  
|WebAuthenticationTenantNotRegistered|Ditt konto tillhör en Azure Active Directory-klient som inte har behörighet att komma åt den här portalen.|  
|WebAuthenticationAuthenticationFailed|Autentiseringen misslyckades.|  
|WebAuthenticationGooglePlusNotEnabled|Autentiseringen misslyckades. Kontakta administratören för att kontrol lera att Google-autentisering är korrekt konfigurerat om du har auktoriserat programmet.|  
|ValidationErrorAllowedTenantIsRequired|Tillåten klient måste anges|  
|ValidationErrorTenantIsNotValid|Azure Active Directory klient organisationen {0} är inte giltig.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Logga in med ditt {0} konto|  
|WebAuthenticationUserLimitNotice|Den här tjänsten har uppnått det maximala antalet tillåtna användare. `<a href="mailto:{0}"\>contact the administrator</a\>`Uppgradera tjänsten och aktivera användar registrering igen.|  
|WebAuthenticationUserLimitNoticeHeader|Användar registrering inaktiverat|  
|WebAuthenticationUserLimitNoticeTitle|Användar registrering inaktiverat|  
|WebAuthenticationUserRegistrationDisabledNotice|Registrering av användare har inaktiverats av administratören. Logga in med extern identitetsprovider.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Användar registrering inaktiverat|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Användar registrering inaktiverat|  
|WebAuthenticationSignupPendingConfirmationNotice|Innan vi kan slutföra skapandet av ditt konto måste vi verifiera din e-postadress. Vi har skickat ett e-postmeddelande till {0} . Följ anvisningarna inuti e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte tas emot inom några minuter kan du kontrol lera mappen skräp post.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Vi hittade ett obekräftat konto för e-postadressen {0} . För att slutföra skapandet av ditt konto måste vi verifiera din e-postadress. Vi har skickat ett e-postmeddelande till {0} . Följ anvisningarna inuti e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte tas emot inom några minuter kan du kontrol lera mappen skräp post|  
|WebAuthenticationSignupConfirmationAlmostDone|Nästan klart|  
|WebAuthenticationSignupConfirmationEmailSent|Vi har skickat ett e-postmeddelande till {0} . Följ anvisningarna inuti e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte tas emot inom några minuter kan du kontrol lera mappen skräp post.|  
|WebAuthenticationEmailSentNotificationMessage|E-postmeddelandet har skickats till {0}|  
|WebAuthenticationNoAadTenantConfigured|Ingen Azure Active Directory klient har kon figurer ATS för tjänsten.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Jag godkänner `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>` .|  
|TextblockUserRegistrationTermsProvided|Granska `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Användningsvillkor|  
|ValidationMessageConsentNotAccepted|Du måste godkänna användnings villkoren innan du kan fortsätta.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a> SigninStrings  
  
|Name|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Glömt ditt lösenord?|  
|WebAuthenticationIfAdministrator|Om du är administratör måste du logga in `<a href="{0}"\>here</a\>` .|  
|WebAuthenticationNotAMember|Inte medlem ännu? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Kom ihåg mig på den här datorn|  
|WebAuthenticationSigininWithPassword|Logga in med ditt användar namn och lösen ord|  
|WebAuthenticationSigninTitle|Logga in|  
|WebAuthenticationSignUpNow|Registrera dig nu|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a> SignupStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleSignup|Registrera dig|  
|WebAuthenticationAlreadyAMember|Är du redan medlem?|  
|WebAuthenticationCreateNewAccount|Skapa ett nytt API Management konto|  
|WebAuthenticationSigninNow|Logga in nu|  
|ButtonLabelSignup|Registrera dig|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Är du säker på att du vill avbryta den här prenumerationen?|  
|SubscriptionRenewConfirmation|Är du säker på att du vill förnya den här prenumerationen?|  
|WebDevelopersManageSubscriptions|Hantera prenumerationer|  
|WebDevelopersPrimaryKey|Primärnyckel|  
|WebDevelopersRegenerateLink|Återskapa|  
|WebDevelopersSecondaryKey|Sekundär nyckel|  
|ButtonLabelShowKey|Visa|  
|ButtonLabelRenewSubscription|Förnya|  
|WebDevelopersSubscriptionRequested|Begärd {0}|  
|WebDevelopersSubscriptionRequestedState|Begärd|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|Tillstånd|  
|WebDevelopersUsageStatisticsLink|Analys rapporter|  
|WebDevelopersYourSubscriptions|Dina prenumerationer|  
|SubscriptionPropertyLabelRequestedDate|Begärd|  
|SubscriptionPropertyLabelStartedDate|Startades|  
|PageTitleRenameSubscription|Byt namn på prenumeration|  
|SubscriptionPropertyLabelName|Prenumerationens namn|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Vill du stänga ditt konto?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Dölj|  
|ButtonLabelRegenerateKey|Återskapa|  
|InformationMessageKeyWasRegenerated|Är du säker på att du vill återskapa den här nyckeln?|  
|ButtonLabelShowKey|Visa|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Uppdatera profil|  
|PageTitleUpdateProfile|Uppdatera konto information|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a> UserProfile  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Ändra konto information|  
|ButtonLabelChangePassword|Ändra lösenord|  
|ButtonLabelCloseAccount|Stäng konto|  
|TextboxLabelEmail|E-post|  
|TextboxLabelEmailFirstName|Förnamn|  
|TextboxLabelEmailLastName|Efternamn|  
|TextboxLabelNotificationsSenderEmail|E-postmeddelande om avsändare|  
|TextboxLabelOrganizationName|Organisationsnamn|  
|SubscriptionStateActive|Aktiv|  
|SubscriptionStateCancelled|Avbrutet|  
|SubscriptionStateExpired|Upphörd|  
|SubscriptionStateRejected|Avslagen|  
|SubscriptionStateRequested|Begärd|  
|SubscriptionStateSuspended|Inaktiverad|  
|DefaultSubscriptionNameTemplate|{0}  objekt|  
|SubscriptionNameTemplate|Åtkomst till utvecklare #{0}|  
|TextboxLabelSubscriptionName|Prenumerationens namn|  
|ValidationMessageSubscriptionNameRequired|Prenumerations namnet får inte vara tomt.|  
|ApiManagementUserLimitReached|Den här tjänsten har uppnått det maximala antalet tillåtna användare. Uppgradera till en högre pris nivå.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a> Glyph-resurser  
 API Management Developer Portal-mallar kan använda specialtecknen från [Glyphicons från bootstrap](https://getbootstrap.com/components/#glyphicons). Den här uppsättningen med glyfer innehåller över 250 tecken i teckensnitts formatet från [Glyphicon](https://glyphicons.com/) Halflings-uppsättningen. Använd följande syntax om du vill använda ett tecken från den här uppsättningen.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 En fullständig lista över glyfer finns i [Glyphicons från bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).
