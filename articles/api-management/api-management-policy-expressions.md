---
title: Princip uttryck för Azure-API Management | Microsoft Docs
description: Lär dig mer om princip uttryck i Azure API Management. Se exempel och Visa fler tillgängliga resurser.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: aec1967f0652e18c4a24ca258c14a103355b22af
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219323"
---
# <a name="api-management-policy-expressions"></a>API Management princip uttryck
I den här artikeln beskrivs syntaxen för princip uttryck i C# 7. Varje uttryck har åtkomst till den implicit tillhandahållna [kontext](api-management-policy-expressions.md#ContextVariables) variabeln och en tillåten [delmängd](api-management-policy-expressions.md#CLRTypes) av .NET Framework typer.

Mer information:

- Se hur du anger Sammanhangs information till Server dels tjänsten. Använd [parametern ange frågesträng](api-management-transformation-policies.md#SetQueryStringParameter) och ange principer för [http-huvud](api-management-transformation-policies.md#SetHTTPheader) för att ange den här informationen.
- Se hur du använder [Verifiera JWT](api-management-access-restriction-policies.md#ValidateJWT) -principen för att förauktorisera åtkomsten till åtgärder baserat på token-anspråk.
- Se hur du använder en [API-kontroll](./api-management-howto-api-inspector.md) spårning för att se hur principer utvärderas och resultatet av dessa utvärderingar.
- Se hur du använder uttryck med [Hämta från cache](api-management-caching-policies.md#GetFromCache) och [lagra i cache](api-management-caching-policies.md#StoreToCache) -principer för att konfigurera API Management cachelagring av svar. Ange en varaktighet som motsvarar cachelagring av svar på backend-tjänsten enligt vad som anges i den säkerhetskopierade tjänstens `Cache-Control` direktiv.
- Se hur du utför innehålls filtrering. Ta bort data element från det svar som tagits emot från Server delen med hjälp av [kontroll flödet](api-management-advanced-policies.md#choose) och [Ange Body](api-management-transformation-policies.md#SetBody) -principer.
- Information om hur du hämtar princip instruktionerna finns i [API-Management-samples/policys](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub lagrings platsen.


## <a name="syntax"></a><a name="Syntax"></a> Uttryck
Uttryck för enstaka uttryck är inneslutna i `@(expression)` , där `expression` är en välformulerad C#-uttrycks instruktion.

Uttryck för flera instruktioner är inneslutna i `@{expression}` . Alla kod Sök vägar i uttryck med flera uttryck måste sluta med en `return` instruktion.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> Fler

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Användning
Uttryck kan användas som attributvärden eller text värden i API Management [principer](api-management-policies.md) (om inte princip referensen anger något annat).

> [!IMPORTANT]
> När du använder princip uttryck finns det bara en begränsad verifiering av princip uttrycken när principen definieras. Uttrycken körs av gatewayen vid körningen. eventuella undantag som genereras av princip uttryck resulterar i ett körnings fel.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> .NET Framework typer som tillåts i princip uttryck
I följande tabell visas de .NET Framework typer och medlemmar som tillåts i princip uttryck.

|Typ|Medlemmar som stöds|
|--------------|-----------------------|
|Newtonsoft.Jspå. Källformatering|Alla|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Jspå. LINQ. Extensions|Alla|
|Newtonsoft.Jspå. LINQ. JArray|Alla|
|Newtonsoft.Jspå. LINQ. JConstructor|Alla|
|Newtonsoft.Jspå. LINQ. JContainer|Alla|
|Newtonsoft.Jspå. LINQ. JObject|Alla|
|Newtonsoft.Jspå. LINQ. JProperty|Alla|
|Newtonsoft.Jspå. LINQ. JRaw|Alla|
|Newtonsoft.Jspå. LINQ. JToken|Alla|
|Newtonsoft.Jspå. LINQ. JTokenType|Alla|
|Newtonsoft.Jspå. LINQ. JValue|Alla|
|System. array|Alla|
|System. BitConverter|Alla|
|System. Boolean|Alla|
|System. byte|Alla|
|System. char|Alla|
|System. Collections. Generic. Dictionary<TKey, TValue>|Alla|
|System. Collections. Generic. HashSet\<T>|Alla|
|System. Collections. Generic. ICollection\<T>|Alla|
|System. Collections. Generic. IDictionary<TKey, TValue>|Alla|
|System. Collections. Generic. IEnumerable\<T>|Alla|
|System. Collections. Generic. IEnumerator\<T>|Alla|
|System. Collections. Generic. IList\<T>|Alla|
|System. Collections. Generic. IReadOnlyCollection\<T>|Alla|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Alla|
|System. Collections. Generic. ISet\<T>|Alla|
|System. Collections. Generic. KeyValuePair<TKey, TValue>|Alla|
|System. Collections. Generic. list\<T>|Alla|
|System. Collections. Generic. Queue\<T>|Alla|
|System. Collections. Generic. stack\<T>|Alla|
|System. convert|Alla|
|System. DateTime|(Konstruktor), Lägg till, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, date, Day, dayofweek, DayOfYear, DaysInMonth, timme, IsDaylightSavingTime, IsLeapYear, MaxValue, millisekund, minut, MinValue, månad, nu, parsa, andra, subtrahera, Tick, TimeOfDay, idag, ToString, UtcNow, år|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Alla|
|System. decimal|Alla|
|System. Double|Alla|
|System. Exception|Alla|
|System. GUID|Alla|
|System. Int16|Alla|
|System. Int32|Alla|
|System. Int64|Alla|
|System. IO. StringReader|Alla|
|System. IO. StringWriter|Alla|
|System. LINQ. enumerable|Alla|
|System. math|Alla|
|System. MidpointRounding|Alla|
|System .net. Web-verktyg|Alla|
|System. Nullable|Alla|
|System. Random|Alla|
|System. SByte|Alla|
|System. Security. Cryptography. AsymmetricAlgorithm|Alla|
|System. Security. Cryptography. CipherMode|Alla|
|System. Security. Cryptography. HashAlgorithm|Alla|
|System. Security. Cryptography. HashAlgorithmName|Alla|
|System. Security. Cryptography. HMAC|Alla|
|System. Security. Cryptography. HMACMD5|Alla|
|System. Security. Cryptography. HMACSHA1|Alla|
|System. Security. Cryptography. HMACSHA256|Alla|
|System. Security. Cryptography. HMACSHA384|Alla|
|System. Security. Cryptography. HMACSHA512|Alla|
|System. Security. Cryptography. KeyedHashAlgorithm|Alla|
|System. Security. Cryptography. MD5|Alla|
|System. Security. Cryptography. OID|Alla|
|System. Security. Cryptography. PaddingMode|Alla|
|System. Security. Cryptography. RNGCryptoServiceProvider|Alla|
|System. Security. Cryptography. RSA|Alla|
|System. Security. Cryptography. RSAEncryptionPadding|Alla|
|System. Security. Cryptography. RSASignaturePadding|Alla|
|System. Security. Cryptography. SHA1|Alla|
|System. Security. Cryptography. SHA1Managed|Alla|
|System. Security. Cryptography. SHA256|Alla|
|System. Security. Cryptography. SHA256Managed|Alla|
|System. Security. Cryptography. SHA384|Alla|
|System. Security. Cryptography. SHA384Managed|Alla|
|System. Security. Cryptography. SHA512|Alla|
|System. Security. Cryptography. SHA512Managed|Alla|
|System. Security. Cryptography. SymmetricAlgorithm|Alla|
|System. Security. Cryptography. X509Certificates. PublicKey|Alla|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Alla|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Name|
|System. Security. Cryptography. X509Certificates. X509Certificate|Alla|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Alla|
|System. Security. Cryptography. X509Certificates. X509ContentType|Alla|
|System. Security. Cryptography. X509Certificates. X509NameType|Alla|
|System. Single|Alla|
|System. String|Alla|
|System. StringComparer|Alla|
|System. StringComparison|Alla|
|System. StringSplitOptions|Alla|
|System. text. Encoding|Alla|
|System. text. RegularExpressions. Capture|Index, längd, värde|
|System. text. RegularExpressions. CaptureCollection|Antal, objekt|
|System. text. RegularExpressions. Group|Avbildningar, lyckade|
|System. text. RegularExpressions. GroupCollection|Antal, objekt|
|System. text. RegularExpressions. match|Tom, grupper, resultat|
|System. text. RegularExpressions. regex|(Konstruktor), IsMatch, match, matchar, Ersätt, avbrott, dela|
|System. text. RegularExpressions. RegexOptions|Alla|
|System. text. StringBuilder|Alla|
|System. TimeSpan|Alla|
|System. TimeZone|Alla|
|System. TimeZoneInfo. AdjustmentRule|Alla|
|System. TimeZoneInfo. TransitionTime|Alla|
|System. TimeZoneInfo|Alla|
|System. tuple|Alla|
|System. UInt16|Alla|
|System. UInt32|Alla|
|System. UInt64|Alla|
|System. URI|Alla|
|System. UriPartial|Alla|
|System.Xml. LINQ. Extensions|Alla|
|System.Xml. LINQ. XAttribute|Alla|
|System.Xml. LINQ. XCData|Alla|
|System.Xml. LINQ. XComment|Alla|
|System.Xml. LINQ. XContainer|Alla|
|System.Xml. LINQ. XDeclaration|Alla|
|System.Xml. LINQ. XDocument|Alla, förutom: Load|
|System.Xml. LINQ. XDocumentType|Alla|
|System.Xml. LINQ. XElement|Alla|
|System.Xml. LINQ. XName|Alla|
|System.Xml. LINQ. XNamespace|Alla|
|System.Xml. LINQ. XNode|Alla|
|System.Xml. LINQ. XNodeDocumentOrderComparer|Alla|
|System.Xml. LINQ. XNodeEqualityComparer|Alla|
|System.Xml. LINQ. XObject|Alla|
|System.Xml. LINQ. XProcessingInstruction|Alla|
|System.Xml. LINQ. XText|Alla|
|System.Xml.XmlNodeType|Alla|

## <a name="context-variable"></a><a name="ContextVariables"></a> Sammanhangs variabel
En variabel med namnet `context` är implicit tillgänglig i varje princip [uttryck](api-management-policy-expressions.md#Syntax). Dess medlemmar tillhandahåller information som är relevant för `\request` . Alla `context` medlemmar är skrivskyddade.

|Sammanhangs variabel|Tillåtna metoder, egenskaper och parameter värden|
|----------------------|-------------------------------------------------------|
|Edit|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Distribution](#ref-context-deployment)<br /><br /> Förfluten: tidsintervall mellan värdet för tidsstämpel och aktuell tid<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Åtgärd](#ref-context-operation)<br /><br /> [Produkt](#ref-context-product)<br /><br /> [Förfrågan](#ref-context-request)<br /><br /> RequestId: GUID – unik begärande-ID<br /><br /> [Response](#ref-context-response)<br /><br /> [Prenumeration](#ref-context-subscription)<br /><br /> Timestamp: DateTime-tidpunkt då begäran togs emot<br /><br /> Spårning: bool-anger om spårning är aktiverat eller inaktiverat <br /><br /> [Användare](#ref-context-user)<br /><br /> [Variabler](#ref-context-variables): IReadOnlyDictionary<sträng, objekt><br /><br /> void-spårning (meddelande: sträng)|
|<a id="ref-context-api"></a>Edit. Application|ID: sträng<br /><br /> IsCurrentRevision: bool<br /><br />  Namn: sträng<br /><br /> Sökväg: sträng<br /><br /> Revision: sträng<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: sträng |
|<a id="ref-context-deployment"></a>Edit. Spridningen|Region: sträng<br /><br /> ServiceName: sträng<br /><br /> Certifikat: IReadOnlyDictionary<sträng, X509Certificate2>|
|<a id="ref-context-lasterror"></a>Edit. LastError|Källa: sträng<br /><br /> Orsak: sträng<br /><br /> Meddelande: sträng<br /><br /> Omfattning: sträng<br /><br /> Avsnitt: sträng<br /><br /> Sökväg: sträng<br /><br /> PolicyId: sträng<br /><br /> För mer information om kontext. LastError, se [fel hantering](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Edit. Reparation|ID: sträng<br /><br /> Metod: sträng<br /><br /> Namn: sträng<br /><br /> UrlTemplate: sträng|
|<a id="ref-context-product"></a>Edit. Momsproduktbokföringsmallar|API: er: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Grupper: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> ID: sträng<br /><br /> Namn: sträng<br /><br /> Tillstånd: Enum ProductState {NotPublished, publicerat}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>Edit. Anmoda|Brödtext: [IMessageBody](#ref-imessagebody) eller `null` om begäran saknar brödtext.<br /><br /> Certifikat: system. Security. Cryptography. X509Certificates. X509Certificate2<br /><br /> [Sidhuvud](#ref-context-request-headers): IReadOnlyDictionary<sträng, string [] ><br /><br /> IP-adress: sträng<br /><br /> MatchedParameters: IReadOnlyDictionary<sträng, String><br /><br /> Metod: sträng<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>sträng kontext. Request. headers. GetValueOrDefault (huvud: String, defaultValue: sträng)|Huvud: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsade begär ande huvud värden eller `defaultValue` om huvudet inte hittas.|
|<a id="ref-context-response"></a>Edit. Svarade|Brödtext: [IMessageBody](#ref-imessagebody)<br /><br /> [Sidhuvud](#ref-context-response-headers): IReadOnlyDictionary<sträng, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: sträng|
|<a id="ref-context-response-headers"></a>sträng kontext. Response. headers. GetValueOrDefault (huvud: String, defaultValue: sträng)|Huvud: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsade värden för svars huvud eller `defaultValue` om huvudet inte hittas.|
|<a id="ref-context-subscription"></a>Edit. Prenumerera|CreatedDate: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: sträng<br /><br /> Nyckel: sträng<br /><br /> Namn: sträng<br /><br /> PrimaryKey: sträng<br /><br /> SecondaryKey: sträng<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>Edit. Användarvänlig|E-post: sträng<br /><br /> FirstName: sträng<br /><br /> Grupper: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> ID: sträng<br /><br /> Identiteter: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: sträng<br /><br /> Obs! sträng<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: sträng<br /><br /> Namn: sträng<br /><br /> Sökväg: sträng<br /><br /> Protokoll: IEnumerable<sträng\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: sträng<br /><br /> Namn: sträng|
|<a id="ref-imessagebody"></a>IMessageBody|Som<T \> (preserveContent: bool = false): där T: String, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> `context.Request.Body.As<T>`Metoderna och `context.Response.Body.As<T>` används för att läsa en begäran och svars meddelande texter i en angiven typ `T` . Som standard använder metoden den ursprungliga meddelande text strömmen och återges den otillgänglig när den har returnerats. För att undvika att metoden fungerar på en kopia av bröd text strömmen anger du `preserveContent` parametern till `true` . Gå [hit](api-management-transformation-policies.md#SetBody) om du vill se ett exempel.|
|<a id="ref-iurl"></a>IUrl|Värd: sträng<br /><br /> Sökväg: sträng<br /><br /> Port: int<br /><br /> [Fråga](#ref-iurl-query): IReadOnlyDictionary<sträng, string [] ><br /><br /> QueryString: sträng<br /><br /> Schema: sträng|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: sträng<br /><br /> Provider: sträng|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Rubrik: sträng<br /><br /> Fråga: sträng|
|<a id="ref-iurl-query"></a>sträng IUrl. Query. GetValueOrDefault (queryParameterName: sträng, defaultValue: sträng)|queryParameterName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsade värden för Frågeparametern eller `defaultValue` om parametern inte hittas.|
|<a id="ref-context-variables"></a>T-kontext. Variabler. GetValueOrDefault<T \> (variableName: String, DefaultValue: T)|variableName: sträng<br /><br /> Standardvärde: T<br /><br /> Returnerar variabeln värde Cast till typ `T` eller `defaultValue` om variabeln inte hittas.<br /><br /> Den här metoden genererar ett undantag om den angivna typen inte matchar den faktiska typen för den returnerade variabeln.|
|BasicAuthCredentials (indatamängd: den här strängen)|inmatade: sträng<br /><br /> Om Indataparametern innehåller ett giltigt värde för HTTP Basic Authentication Request-huvud, returnerar metoden ett objekt av typen `BasicAuthCredentials` , annars returnerar metoden null.|
|bool TryParseBasic (inmatad: den här strängen, resultat: BasicAuthCredentials)|inmatade: sträng<br /><br /> resultat: out-BasicAuthCredentials<br /><br /> Om Indataparametern innehåller ett giltigt auktoriseringsvärde för HTTP Basic-autentisering i begär ande huvudet returnerar metoden `true` och resultat parametern innehåller ett värde av typen `BasicAuthCredentials` , annars returnerar metoden `false` .|
|BasicAuthCredentials|Lösen ord: sträng<br /><br /> UserId: sträng|
|JWT-AsJwt (inmatade: den här strängen)|inmatade: sträng<br /><br /> Om Indataparametern innehåller ett giltigt JWT-token returnerar metoden ett objekt av typen `Jwt` , annars returnerar metoden `null` .|
|bool TryParseJwt (inmatad: den här strängen, resultat: out-of-JWT)|inmatade: sträng<br /><br /> resultat: out-of-JWT<br /><br /> Om Indataparametern innehåller ett giltigt JWT-token returnerar metoden `true` och resultat parametern innehåller ett värde av typen `Jwt` , annars returnerar metoden `false` .|
|JWT|Algoritm: sträng<br /><br /> Mål grupper: IEnumerable<sträng\><br /><br /> Anspråk: IReadOnlyDictionary<sträng, string [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: sträng<br /><br /> Utfärdare: sträng<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Ämne: sträng<br /><br /> Typ: sträng|
|sträng JWT. Claims. GetValueOrDefault (claimName: String, defaultValue: sträng)|claimName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsade anspråks värden eller `defaultValue` om huvudet inte hittas.|
|byte [] kryptera (inmatad: denna byte [], alg: sträng, nyckel: byte [], IV: byte [])|in-klartext som ska krypteras<br /><br />alg – namnet på en symmetrisk krypteringsalgoritm<br /><br />nyckel krypterings nyckel<br /><br />IV – initierings vektor<br /><br />Returnerar krypterad klartext.|
|byte [] kryptera (inmatad: denna byte [], alg: system. Security. Cryptography. SymmetricAlgorithm)|in-klartext som ska krypteras<br /><br />alg-krypteringsalgoritm<br /><br />Returnerar krypterad klartext.|
|byte [] kryptera (inmatad: denna byte [], alg: system. Security. Cryptography. SymmetricAlgorithm, nyckel: byte [], IV: byte [])|in-klartext som ska krypteras<br /><br />alg-krypteringsalgoritm<br /><br />nyckel krypterings nyckel<br /><br />IV – initierings vektor<br /><br />Returnerar krypterad klartext.|
|byte [] dekryptera (ininformation: denna byte [], alg: sträng, nyckel: byte [], IV: byte [])|chiffer text som ska dekrypteras<br /><br />alg – namnet på en symmetrisk krypteringsalgoritm<br /><br />nyckel krypterings nyckel<br /><br />IV – initierings vektor<br /><br />Returnerar oformaterad text.|
|byte [] dekryptera (ininformation: denna byte [], alg: system. Security. Cryptography. SymmetricAlgorithm)|chiffer text som ska dekrypteras<br /><br />alg-krypteringsalgoritm<br /><br />Returnerar oformaterad text.|
|byte [] dekryptera (ininformation: denna byte [], alg: system. Security. Cryptography. SymmetricAlgorithm, nyckel: byte [], IV: byte [])|chiffer text som ska dekrypteras<br /><br />alg-krypteringsalgoritm<br /><br />nyckel krypterings nyckel<br /><br />IV – initierings vektor<br /><br />Returnerar oformaterad text.|
|bool-VerifyNoRevocation (inmatat: system. Security. Cryptography. X509Certificates. X509Certificate2)|Utför en X. 509-kedje verifiering utan att kontrol lera status för återkallning av certifikat.<br /><br />objekt för ingående certifikat<br /><br />Returnerar `true` om verifieringen lyckas `false` . om verifieringen Miss lyckas.|


## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
+ [Principexempel](./policy-reference.md)
