---
title: Data modell referens för Azure API Management-mall | Microsoft Docs
description: Lär dig mer om entiteter och typ representationer för vanliga objekt som används i data modeller för mallar för utvecklare-portalen i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 868ad3d1c6e7e7ef2cf32dcf675bc471a614f3ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86243162"
---
# <a name="azure-api-management-template-data-model-reference"></a>Data modell referens för Azure API Management-mall
I det här avsnittet beskrivs entitets-och typ representationer för vanliga objekt som används i data modellerna för utvecklares Portal mallar i Azure API Management.  
  
 Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referens

-   [API](#API)  
-   [API-Sammanfattning](#APISummary)  
-   [Program](#Application)  
-   [Bilaga](#Attachment)  
-   [Kodexempel](#Sample)  
-   [Kommentar](#Comment)  
-   [Filtrering](#Filtering)  
-   [Huvud](#Header)  
-   [HTTP-begäran](#HTTPRequest)  
-   [HTTP-svar](#HTTPResponse)  
-   [Ge](#Issue)  
-   [Åtgärd](#Operation)  
-   [Åtgärds meny](#Menu)  
-   [Meny alternativ för åtgärd](#MenuItem)  
-   [Sidindelning](#Paging)  
-   [Parameter](#Parameter)  
-   [Produkt](#Product)  
-   [Leverantör](#Provider)  
-   [Bilden](#Representation)  
-   [Prenumeration](#Subscription)  
-   [Prenumerations Sammanfattning](#SubscriptionSummary)  
-   [Användar konto information](#UserAccountInfo)  
-   [Användar inloggning](#UseSignIn)  
-   [Användar registrering](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a> Application  
 `API`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`id`|sträng|Resurs-ID. Identifierar unikt API i den aktuella API Management tjänst instansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|`name`|sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`description`|sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-taggar. Maximal längd är 1000 tecken.|  
|`serviceUrl`|sträng|Absolut URL för backend-tjänsten som implementerar detta API.|  
|`path`|sträng|En relativ URL som unikt identifierar detta API och alla dess resurs Sök vägar inom API Management tjänst instansen. Den läggs till i API-slutpunktens bas-URL som anges när tjänst instansen skapas för att bilda en offentlig URL för detta API.|  
|`protocols`|matris med tal|Beskriver på vilka protokoll som åtgärderna i detta API kan anropas. Tillåtna värden är `1 - http` och `2 - https` , eller både och.|  
|`authenticationSettings`|[Autentiseringsinställningar för verifiering av Server](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Samling autentiseringsinställningar som ingår i detta API.|  
|`subscriptionKeyParameterNames`|objekt|Valfri egenskap som kan användas för att ange anpassade namn för fråge-och/eller rubrik parametrar som innehåller prenumerations nyckeln. När den här egenskapen finns måste den innehålla minst en av de två följande egenskaperna.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a> API-Sammanfattning  
 `API summary`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`id`|sträng|Resurs-ID. Identifierar unikt API i den aktuella API Management tjänst instansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|`name`|sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`description`|sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-taggar. Maximal längd är 1000 tecken.|  
  
##  <a name="application"></a><a name="Application"></a> Applicering  
 `application`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Id`|sträng|Programmets unika identifierare.|  
|`Title`|sträng|Programmets rubrik.|  
|`Description`|sträng|Programmets beskrivning.|  
|`Url`|URI|URI för programmet.|  
|`Version`|sträng|Versions information för programmet.|  
|`Requirements`|sträng|En beskrivning av kraven för programmet.|  
|`State`|antal|Programmets aktuella tillstånd.<br /><br /> -0-registrerad<br /><br /> -1 – skickad<br /><br /> -2-publicerad<br /><br /> -3-avvisad<br /><br /> -4-opublicerad|  
|`RegistrationDate`|DateTime|Datum och tidpunkt då programmet registrerades.|  
|`CategoryId`|antal|Programmets kategori (finans, underhållning osv.)|  
|`DeveloperId`|sträng|Den unika identifieraren för den utvecklare som skickade programmet.|  
|`Attachments`|Samling av [bilage](#Attachment) enheter.|Bifogade filer för programmet, till exempel skärm bilder eller ikoner.|  
|`Icon`|[Bilaga](#Attachment)|Ikonen för programmet.|  
  
##  <a name="attachment"></a><a name="Attachment"></a> Lage  
 `attachment`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`UniqueId`|sträng|Den unika identifieraren för den bifogade filen.|  
|`Url`|sträng|Resursens URL.|  
|`Type`|sträng|Typ av bifogad fil.|  
|`ContentType`|sträng|Den bifogade filens medietyp.|  
  
##  <a name="code-sample"></a><a name="Sample"></a> Kod exempel  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`title`|sträng|Åtgärdens namn.|  
|`snippet`|sträng|Den här egenskapen är föråldrad och ska inte användas.|  
|`brush`|sträng|Vilken kod syntax som färgmall ska användas när kod exemplet visas. Tillåtna värden är,,,,,, `plain` `php` `java` `xml` `objc` `python` `ruby` och `csharp` .|  
|`template`|sträng|Namnet på den här exempel mal len för kod.|  
|`body`|sträng|En plats hållare för kod exempel delen av kodfragmentet.|  
|`method`|sträng|Åtgärdens HTTP-metod.|  
|`scheme`|sträng|Det protokoll som ska användas för åtgärds förfrågan.|  
|`path`|sträng|Åtgärdens sökväg.|  
|`query`|sträng|Exempel på frågesträng med definierade parametrar.|  
|`host`|sträng|URL-adressen till API Management Gateway för det API som innehåller den här åtgärden.|  
|`headers`|Samling med                                  [huvud](#Header) enheter.|Sidhuvud för den här åtgärden.|  
|`parameters`|Samling av [parameter](#Parameter) enheter.|Parametrar som har definierats för den här åtgärden.|  
  
##  <a name="comment"></a><a name="Comment"></a> Kommentar  
 `API`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|antal|Kommentarens ID.|  
|`CommentText`|sträng|Kommentarens brödtext. Kan innehålla HTML.|  
|`DeveloperCompany`|sträng|Företags namnet för utvecklaren.|  
|`PostedOn`|DateTime|Datum och tid då kommentaren publicerades.|  
  
##  <a name="issue"></a><a name="Issue"></a> Ge  
 `issue`Entiteten har följande egenskaper.  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Id`|sträng|Den unika identifieraren för problemet.|  
|`ApiID`|sträng|ID: t för API: et som det här problemet rapporterades för.|  
|`Title`|sträng|Rubriken på problemet.|  
|`Description`|sträng|Beskrivning av problemet.|  
|`SubscriptionDeveloperName`|sträng|Förnamn för den utvecklare som rapporterade problemet.|  
|`IssueState`|sträng|Problemets aktuella tillstånd. Möjliga värden föreslås, öppnas och stängs.|  
|`ReportedOn`|DateTime|Datum och tid då problemet rapporterades.|  
|`Comments`|Samling med [kommentars](#Comment) enheter.|Kommentarer om det här problemet.|  
|`Attachments`|Samling av [bilage](api-management-template-data-model-reference.md#Attachment) enheter.|Bifogade filer till problemet.|  
|`Services`|Samling av [API](#API) -entiteter.|API: er som prenumererar på av användaren som har arkiverat problemet.|  
  
##  <a name="filtering"></a><a name="Filtering"></a> Statisk  
 `filtering`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Pattern`|sträng|Den aktuella Sök termen; eller `null` om det inte finns någon sökterm.|  
|`Placeholder`|sträng|Texten som ska visas i sökrutan när det inte finns någon angiven sökterm.|  
  
##  <a name="header"></a><a name="Header"></a> Sidfot  
 I det här avsnittet beskrivs `parameter` åter givningen.  
  
|Egenskap|Typ|Description|  
|--------------|-----------------|----------|  
|`name`|sträng|Parameternamn.|  
|`description`|sträng|Parameter beskrivning.|  
|`value`|sträng|Huvud värde.|  
|`typeName`|sträng|Data typen för huvud värde.|  
|`options`|sträng|Alternativ.|  
|`required`|boolean|Om rubriken är obligatorisk.|  
|`readOnly`|boolean|Om rubriken är skrivskyddad.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a> HTTP-begäran  
 I det här avsnittet beskrivs `request` åter givningen.  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`description`|sträng|Beskrivning av åtgärds förfrågan.|  
|`headers`|matris med [huvud](#Header) enheter.|Begärandehuvuden.|  
|`parameters`|[parameter](#Parameter) mat ris|Samling med parametrar för åtgärds förfrågan.|  
|`representations`|Representation av [representation](#Representation)|Samling av representationer av åtgärds begär Anden.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a> HTTP-svar  
 I det här avsnittet beskrivs `response` åter givningen.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`statusCode`|positivt heltal|Status kod för åtgärds svar.|  
|`description`|sträng|Beskrivning av åtgärds svar.|  
|`representations`|Representation av [representation](#Representation)|Samling av representationer av åtgärds svar.|  
  
##  <a name="operation"></a><a name="Operation"></a> Reparation  
 `operation`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`id`|sträng|Resurs-ID. Identifierar en unik åtgärd i den aktuella API Management tjänst instansen. Värdet är en giltig relativ URL i formatet `apis/{aid}/operations/{id}` där `{aid}` är en API-identifierare och `{id}` är ett åtgärds-ID. Den här egenskapen är skrivskyddad.|  
|`name`|sträng|Åtgärdens namn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`description`|sträng|Beskrivning av åtgärden. Får inte vara tomt. Kan innehålla HTML-taggar. Maximal längd är 1000 tecken.|  
|`scheme`|sträng|Beskriver på vilka protokoll som åtgärderna i detta API kan anropas. Tillåtna värden är `http` , `https` , eller både `http` och `https` .|  
|`uriTemplate`|sträng|Relativ URL-mall som identifierar mål resursen för den här åtgärden. Kan innehålla parametrar. Exempel: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|sträng|Den API Management Gateway-URL som är värd för API: et.|  
|`httpMethod`|sträng|HTTP-metod för åtgärd.|  
|`request`|[HTTP-begäran](#HTTPRequest)|En entitet som innehåller information om begäran.|  
|`responses`|matris med [http-svar](#HTTPResponse)|Matris med åtgärder för [http-svar](#HTTPResponse) .|  
  
##  <a name="operation-menu"></a><a name="Menu"></a> Åtgärds meny  
 `operation menu`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`ApiId`|sträng|ID för aktuellt API.|  
|`CurrentOperationId`|sträng|ID för den aktuella åtgärden.|  
|`Action`|sträng|Meny typen.|  
|`MenuItems`|Samling av [Åtgärds meny alternativ](#MenuItem) enheter.|Åtgärder för aktuellt API.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a> Meny alternativ för åtgärd  
 `operation menu item`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Id`|sträng|Åtgärdens ID.|  
|`Title`|sträng|Åtgärdens beskrivning.|  
|`HttpMethod`|sträng|Åtgärdens http-metod.|  
  
##  <a name="paging"></a><a name="Paging"></a> Växlings  
 `paging`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Page`|antal|Det aktuella sid numret.|  
|`PageSize`|antal|Det högsta antalet resultat som ska visas på en enda sida.|  
|`TotalItemCount`|antal|Antalet objekt som ska visas.|  
|`ShowAll`|boolean|Om alla resultat ska visas på en enda sida.|  
|`PageCount`|antal|Antalet resultat sidor.|  
  
##  <a name="parameter"></a><a name="Parameter"></a> ProfileServiceApplicationProxy  
 I det här avsnittet beskrivs `parameter` åter givningen.  
  
|Egenskap|Typ|Description|  
|--------------|-----------------|----------|  
|`name`|sträng|Parameternamn.|  
|`description`|sträng|Parameter beskrivning.|  
|`value`|sträng|Parameter värde.|  
|`options`|strängmatris|Värden som definierats för parameter värden för frågor.|  
|`required`|boolean|Anger om parametern är obligatorisk eller inte.|  
|`kind`|antal|Om den här parametern är en Sök vägs parameter (1) eller en QueryString-parameter (2).|  
|`typeName`|sträng|Parameter typ.|  
  
##  <a name="product"></a><a name="Product"></a> Momsproduktbokföringsmallar  
 `product`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Id`|sträng|Resurs-ID. Identifierar produkten unikt i den aktuella API Management tjänst instansen. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är ett produkt-ID. Den här egenskapen är skrivskyddad.|  
|`Title`|sträng|Namn på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`Description`|sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-taggar. Maximal längd är 1000 tecken.|  
|`Terms`|sträng|Produkt användnings villkor. Utvecklare som försöker prenumerera på produkten visas och krävs för att acceptera dessa villkor innan prenumerations processen kan slutföras.|  
|`ProductState`|antal|Anger om produkten har publicerats eller inte. Publicerade produkter kan upptäckas av utvecklare på Developer-portalen. Icke-publicerade produkter visas bara för administratörer.<br /><br /> Tillåtna värden för produkt tillstånd är:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Anger om en användare kan ha flera prenumerationer på produkten samtidigt.|  
|`MultipleSubscriptionsCount`|antal|Maximalt antal prenumerationer för den här produkten som en användare får ha på samma gång.|  
  
##  <a name="provider"></a><a name="Provider"></a> CSP  
 `provider`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Properties`|sträng ord lista|Egenskaper för denna autentiseringsprovider.|  
|`AuthenticationType`|sträng|Typ av Provider. (Azure Active Directory, Facebook login, Google-konto, Microsoft-konto, Twitter).|  
|`Caption`|sträng|Leverantörens visnings namn.|  
  
##  <a name="representation"></a><a name="Representation"></a> Bilden  
 I det här avsnittet beskrivs en `representation` .  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`contentType`|sträng|Anger en registrerad eller anpassad innehålls typ för den här presentationen, till exempel `application/xml` .|  
|`sample`|sträng|Ett exempel på representationen.|  
  
##  <a name="subscription"></a><a name="Subscription"></a> Prenumerera  
 `subscription`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Id`|sträng|Resurs-ID. Identifierar en unik prenumeration i den aktuella API Management tjänst instansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|`ProductId`|sträng|Produkt resurs identifieraren för den påbörjade produkten. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är ett produkt-ID.|  
|`ProductTitle`|sträng|Namn på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`ProductDescription`|sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-taggar. Maximal längd är 1000 tecken.|  
|`ProductDetailsUrl`|sträng|Relativ URL till produkt information.|  
|`state`|sträng|Prenumerationens tillstånd. Möjliga tillstånd är:<br /><br /> - `0 - suspended` – prenumerationen är blockerad och prenumeranten kan inte anropa några API: er för produkten.<br /><br /> - `1 - active` – prenumerationen är aktiv.<br /><br /> - `2 - expired` – prenumerationen har nått sitt förfallo datum och inaktiverades.<br /><br /> - `3 - submitted` – prenumerations förfrågan har gjorts av utvecklaren, men har ännu inte godkänts eller avvisats.<br /><br /> - `4 - rejected` – prenumerations förfrågan har nekats av en administratör.<br /><br /> - `5 - cancelled` – prenumerationen har avbrutits av utvecklaren eller administratören.|  
|`DisplayName`|sträng|Visnings namn för prenumerationen.|  
|`CreatedDate`|dateTime|Det datum då prenumerationen skapades, i ISO 8601-format: `2014-06-24T16:25:00Z` .|  
|`CanBeCancelled`|boolean|Om prenumerationen kan avbrytas av den aktuella användaren.|  
|`IsAwaitingApproval`|boolean|Om prenumerationen väntar på godkännande.|  
|`StartDate`|dateTime|Prenumerationens start datum, i ISO 8601-format: `2014-06-24T16:25:00Z` .|  
|`ExpirationDate`|dateTime|Prenumerationens förfallo datum i ISO 8601-format: `2014-06-24T16:25:00Z` .|  
|`NotificationDate`|dateTime|Meddelande datumet för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z` .|  
|`primaryKey`|sträng|Primär prenumerations nyckel. Maximal längd är 256 tecken.|  
|`secondaryKey`|sträng|Sekundär prenumerations nyckel. Maximal längd är 256 tecken.|  
|`CanBeRenewed`|boolean|Om prenumerationen kan förnyas av den aktuella användaren.|  
|`HasExpired`|boolean|Om prenumerationen har upphört att gälla.|  
|`IsRejected`|boolean|Om prenumerations förfrågan nekades.|  
|`CancelUrl`|sträng|Den relativa URL: en för att avbryta prenumerationen.|  
|`RenewUrl`|sträng|Den relativa URL: en för att förnya prenumerationen.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a> Prenumerations Sammanfattning  
 `subscription summary`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Id`|sträng|Resurs-ID. Identifierar en unik prenumeration i den aktuella API Management tjänst instansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|`DisplayName`|sträng|Prenumerationens visnings namn|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a> Användar konto information  
 `user account info`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`FirstName`|sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`LastName`|sträng|Efter namn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`Email`|sträng|E-postadress. Får inte vara tom och måste vara unikt inom tjänst instansen. Maximal längd är 254 tecken.|  
|`Password`|sträng|Användar kontots lösen ord.|  
|`NameIdentifier`|sträng|Konto identifierare, samma som användarens e-postadress.|  
|`ProviderName`|sträng|Namn på autentiseringsprovider.|  
|`IsBasicAccount`|boolean|Sant om det här kontot registrerades med e-post och lösen ord; falskt om kontot registrerades med en provider.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a> Användar inloggning  
 `user sign in`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Description|  
|--------------|----------|-----------------|  
|`Email`|sträng|E-postadress. Får inte vara tom och måste vara unikt inom tjänst instansen. Maximal längd är 254 tecken.|  
|`Password`|sträng|Användar kontots lösen ord.|  
|`ReturnUrl`|sträng|URL-adressen till sidan där användaren klickar på Logga in.|  
|`RememberMe`|boolean|Om den aktuella användarens information ska sparas.|  
|`RegistrationEnabled`|boolean|Om registreringen är aktive rad.|  
|`DelegationEnabled`|boolean|Om delegerad inloggning är aktive rad.|  
|`DelegationUrl`|sträng|Den delegerade inloggnings-URL: en, om den är aktive rad.|  
|`SsoSignUpUrl`|sträng|URL: en för enkel inloggning för användaren, om sådan finns.|  
|`AuxServiceUrl`|sträng|Om den aktuella användaren är administratör är det en länk till tjänst instansen i Azure Portal.|  
|`Providers`|Samling av [Provider](#Provider) -entiteter|Providern för den här användaren.|  
|`UserRegistrationTerms`|sträng|Villkor som en användare måste samtycka till innan de kan logga in.|  
|`UserRegistrationTermsEnabled`|boolean|Om villkor är aktiverade.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a> Användar registrering  
 `user sign up`Entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Värde som används av registrerings registreringen för [registrering](api-management-page-controls.md#sign-up).|  
|`Password`|sträng|Användar kontots lösen ord.|  
|`PasswordVerdictLevel`|antal|Värde som används av registrerings registreringen för [registrering](api-management-page-controls.md#sign-up).|  
|`UserRegistrationTerms`|sträng|Villkor som en användare måste samtycka till innan de kan logga in.|  
|`UserRegistrationTermsOptions`|antal|Värde som används av registrerings registreringen för [registrering](api-management-page-controls.md#sign-up).|  
|`ConsentAccepted`|boolean|Värde som används av registrerings registreringen för [registrering](api-management-page-controls.md#sign-up).|  
|`Email`|sträng|E-postadress. Får inte vara tom och måste vara unikt inom tjänst instansen. Maximal längd är 254 tecken.|  
|`FirstName`|sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`LastName`|sträng|Efter namn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`UserData`|sträng|Värde som används av [registrerings](api-management-page-controls.md#sign-up) kontrollen.|  
|`NameIdentifier`|sträng|Värde som används av registrerings registreringen för [registrering](api-management-page-controls.md#sign-up).|  
|`ProviderName`|sträng|Namn på autentiseringsprovider.|

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).
