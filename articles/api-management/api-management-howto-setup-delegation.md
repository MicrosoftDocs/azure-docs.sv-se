---
title: Delegera användarregistring och produktprenumeration
description: Lär dig hur du delegerar användar registrering och produkt prenumeration till en tredje part i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 54193c9333c75fd8b973ebe33470fca3617e2f2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341849"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Delegera användarregistring och produktprenumeration

Med delegering kan du använda din befintliga webbplats för att hantera utvecklares inloggning/registrering och prenumeration på produkter, i stället för att använda de inbyggda funktionerna i Developer-portalen. Det gör att din webbplats kan äga användar data och utföra valideringen av dessa steg på ett anpassat sätt.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegera inloggning och registrering av utvecklare

För att delegera utvecklare, logga in och registrera dig på din befintliga webbplats måste du skapa en särskild Delegerings slut punkt på din webbplats. Den måste fungera som ingångs punkt för en sådan begäran som initieras från API Management Developer-portalen.

Det slutliga arbets flödet är följande:

1. Developer klickar på länken Logga in eller registrera på API Management Developer-portalen
2. Webbläsaren omdirigeras till Delegerings slut punkten
3. Delegerings slut punkt i retur omdirigeringar till eller visar ett gränssnitt där användaren uppmanas att logga in eller registrera sig
4. Vid lyckad omdirigeras användaren tillbaka till den API Management Developer Portal-sida som de startade från

Börja med att konfigurera API Management för att dirigera begär Anden via din Delegerings slut punkt. I Azure Portal söker du efter **säkerhet** i API Management resurs och klickar sedan på objektet **delegering** . Klicka på kryss rutan om du vill aktivera "delegera inloggning & registrera dig".

![Sidan delegering][api-management-delegation-signin-up]

* Bestäm vilken URL för din särskilda Delegerings slut punkt som ska användas och ange den i fältet för **Delegerings slut punkt** . 
* I fältet delegera autentiseringsnyckel anger du en hemlighet som ska användas för att beräkna en signatur som du får för att kontrol lera att begäran verkligen kommer från Azure API Management. Du kan klicka på knappen **generera** om du vill att API Management slumpmässigt ska generera en nyckel åt dig.

Nu måste du skapa **Delegerings slut punkten**. Det måste utföra ett antal åtgärder:

1. Ta emot en begäran i följande format:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation?operation=SignIn&ReturnUrl = {URL till käll sidan} &salt = {String} &sig = {String}*
   
    Frågeparametrar för inloggnings-/registrerings fallet:
   
   * **åtgärd**: identifierar vilken typ av delegeringsbegäran det är – det kan bara vara **inloggning** i det här fallet
   * **ReturnUrl**: URL: en till sidan där användaren klickade på inloggnings-eller registrerings länk
   * **salt**: en särskild salt sträng som används för att beräkna en säkerhets-hash
   * **sig**: en beräknad säkerhets-hash som ska användas för jämförelse med din egen beräknade hash
2. Kontrol lera att begäran kommer från Azure API Management (valfritt, men rekommenderas för säkerhet)
   
   * Beräkna en HMAC-SHA512 hash av en sträng baserat på parametrarna **ReturnUrl** och **salt** fråga ([exempel kod anges nedan]):
     
     > HMAC (**salt** + ' \n ' + **ReturnUrl**)

   * Jämför det ovan beräknade hashvärdet med värdet för **sig** -Frågeparametern. Om de två hasharna matchar går du vidare till nästa steg, annars nekar du begäran.
3. Kontrol lera att du får en begäran om att logga in/registrera: **åtgärdens** frågeparameter anges till "**signin**".
4. Presentera användaren användar gränssnitt för att logga in eller registrera dig
5. Om användaren är inloggad måste du skapa ett motsvarande konto för dem i API Management. [Skapa en användare] med API Management REST API. När du gör det, se till att du anger användar-ID: t till samma värde som i ditt användar arkiv eller till ett ID som du kan hålla reda på.
6. När användaren har autentiserats:
   
   * [Begär en delad] åtkomsttoken via API Management REST API
   * Lägg till en returnUrl-frågeparameter till URL: en för SSO som du har fått från API-anropet ovan:
     
     > till exempel `https://<developer portal domain, for example: contoso.developer.azure-api.net>/signin-sso?token=<URL-encoded token>&returnUrl=<URL-encoded URL, for example: %2Freturn%2Furl>` 
     
   * Omdirigera användaren till den ovan producerade URL: en

Förutom **inloggnings** åtgärden kan du också utföra konto hantering genom att följa de föregående stegen och använda någon av följande åtgärder:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **Utloggning**

Du måste skicka följande frågeparametrar för konto hanterings åtgärder.

* **åtgärd**: identifierar vilken typ av delegeringsbegäran den är (ChangePassword, ChangeProfile eller closeaccount)
* **userId**: användar-ID för det konto som ska hanteras
* **salt**: en särskild salt sträng som används för att beräkna en säkerhets-hash
* **sig**: en beräknad säkerhets-hash som ska användas för jämförelse med din egen beräknade hash

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegera produkt prenumeration

Att delegera produkt prenumeration fungerar på samma sätt som att delegera användar inloggning. Det slutliga arbets flödet skulle vara följande:

1. Utvecklare väljer en produkt i API Management Developer-portalen och klickar på knappen Prenumerera.
2. Webbläsaren omdirigeras till Delegerings slut punkten.
3. Delegerings slut punkten utför nödvändiga produkt prenumerations steg. Det är upp till dig att utforma stegen. De kan omfatta omdirigering till en annan sida för att begära fakturerings information, ställa in ytterligare frågor eller bara lagra informationen och inte kräva någon användar åtgärd.

Om du vill aktivera funktionen klickar du på **delegera produkt prenumeration** på sidan **delegering** .

Se sedan till att Delegerings slut punkten utför följande åtgärder:

1. Ta emot en begäran i följande format:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation? åtgärd = {åtgärd} &Produktnr = {produkt att prenumerera på} &userId = {User-förfrågan} &salt = {String} &sig = {sträng}*
   >
   
    Frågeparametrar för ärende om produkt prenumeration:
   
   * **åtgärd**: identifierar vilken typ av Delegerings förfrågan den är. För produkt prenumerations begär Anden är giltiga alternativ:
     * "Prenumerera": en begäran om att prenumerera på användaren till en viss produkt med angivet ID (se nedan)
     * "Avregistrera": en begäran om att avbryta prenumerationen på en användare från en produkt
     * "Förnya": en begäran om att förnya en prenumeration (till exempel som kan upphöra att gälla)
   * **productId**: vid *prenumeration* – ID för produkten som användaren har begärt att prenumerera på
   * **subscriptionId**: vid avregistrering och *förnyelse* -ID: *t för produkt* prenumerationen
   * **userId**: vid *prenumeration* – ID för användaren som begäran görs om
   * **salt**: en särskild salt sträng som används för att beräkna en säkerhets-hash
   * **sig**: en beräknad säkerhets-hash som ska användas för jämförelse med din egen beräknade hash

2. Kontrol lera att begäran kommer från Azure API Management (valfritt, men rekommenderas för säkerhet)
   
   * Beräkna en HMAC-SHA512 av en sträng baserat på parametrarna **productId**, **userId** och **salt** fråga:
     
     > HMAC (**salt** + ' \n ' + **productId** + ' \n ' + **userId**)
     > 
     > 
   * Jämför det ovan beräknade hashvärdet med värdet för **sig** -Frågeparametern. Om de två hasharna matchar går du vidare till nästa steg, annars nekar du begäran.
3. Bearbeta produkt prenumeration baserat på den typ av åtgärd som har begärts i **drift** , till exempel fakturering, ytterligare frågor osv.
4. När du prenumererar användaren på produkten på din sida ska du prenumerera på den API Management produkten genom att [anropa REST API för prenumerationer].

## <a name="example-code"></a><a name="delegate-example-code"></a> Exempel kod

Följande kod exempel visar hur du:

* Ta *Delegerings validerings nyckeln*, som anges på skärmen delegering på utgivar portalen
* Skapa en HMAC som sedan används för att validera signaturen och bevisa giltigheten för den skickade returnUrl.

Samma kod fungerar för productId och userId med mindre modifiering.

**C#-kod för att generera hash för returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS-kod för att generera hash för returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Du måste [Publicera om Developer-portalen](api-management-howto-developer-portal-customize.md#publish) för att Delegerings ändringarna ska börja gälla.

## <a name="next-steps"></a>Nästa steg
Mer information om delegering finns i följande videoklipp:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Begär en token för delad åtkomst]: /rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken
[skapa en användare]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[anropar REST API för prenumerationer]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[exempel kod som anges nedan]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
