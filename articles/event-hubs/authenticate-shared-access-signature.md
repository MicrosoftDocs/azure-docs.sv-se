---
title: Autentisera åtkomst till Azure Event Hubs med signaturer för delad åtkomst
description: Den här artikeln visar hur du autentiserar åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98986503"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autentisera åtkomst till Event Hubs resurser med signaturer för delad åtkomst (SAS)
Signaturen för delad åtkomst (SAS) ger dig detaljerad kontroll över vilken typ av åtkomst du beviljar till de klienter som har signaturen för delad åtkomst. Här följer några av de kontroller som du kan ange i en SAS: 

- Intervallet då SAS är giltigt, inklusive start tid och förfallo tid.
- De behörigheter som beviljats av SAS. Till exempel kan en SAS för ett Event Hubs namn område ge behörigheten Lyssna, men inte Send-behörigheten.
- Endast klienter som presenterar giltiga autentiseringsuppgifter kan skicka data till en händelsehubben.
- En klient kan inte personifiera en annan klient.
- En falsk klient kan blockeras från att skicka data till en Event Hub.

Den här artikeln beskriver hur du autentiserar åtkomsten till Event Hubs-resurser med hjälp av SAS. Mer information om hur du **auktoriserar** åtkomst till Event Hubs-resurser med hjälp av SAS finns i [den här artikeln](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhets åtgärd, i stället för att använda signaturer för delad åtkomst, vilket kan vara svårare att avslöja. Även om du kan fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerade åtkomst till dina Event Hubs-resurser, erbjuder Azure AD liknande funktioner utan att du behöver hantera SAS-token eller oroa dig för att återkalla en komprometterad SAS.
> 
> Mer information om Azure AD-integrering i Azure Event Hubs finns i [bevilja åtkomst till Event Hubs med hjälp av Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Konfigurera för SAS-autentisering
Du kan konfigurera EventHubs för delad åtkomst i ett Event Hubs-namnområde eller en entitet (Event Hub instance eller Kafka-avsnittet i en Event Hub). Det finns för närvarande inte stöd för att konfigurera en auktoriseringsregel för delad åtkomst på en konsument grupp, men du kan använda regler som kon figurer ATS i ett namn område eller en entitet för att skydda åtkomst till konsument gruppen. 

Följande bild visar hur auktoriseringsregler gäller för exempel entiteter. 

![Konfigurera auktoriseringsregel](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

I det här exemplet har exemplet Event Hubs-namnrymd (ExampleNamespace) två entiteter: EH1 och topic1. Auktoriseringsregler definieras både på enhets nivå och på namn områdes nivå.  

ManageRuleNS-, sendRuleNS-och listenRuleNS-auktoriseringsregler gäller för både Event Hub-instansen EH1 och ämnet T1. Auktoriseringsregler listenRule-händelsehubbnamnområde och sendRule-händelsehubbnamnområde gäller endast Event Hub instance EH1 och sendRuleT-auktoriseringsregeln gäller endast för avsnitt topic1. 

När du använder sendRuleNS kan klient program skicka till både EH1 och topic1. När sendRuleT används, tillämpar den endast detaljerad åtkomst till topic1 och därför kan klient program som använder den här regeln för åtkomst nu inte skicka till EH1, men endast till topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generera en token för signatur för delad åtkomst 
Alla klienter som har åtkomst till namnet på ett namn för auktoriseringsregeln och en av dess signerings nycklar kan generera en SAS-token. Token genereras genom att en sträng skapas i följande format:

- `se`  – Token upphör att gälla omedelbart. Heltal som motsvarar sekunder sedan 1970 1 januari 00:00:00 UTC (UNIX-epok) när token upphör att gälla
- `skn` – Namnet på auktoriseringsregeln, det vill säga SAS-nyckelns namn.
- `sr` – URI för den resurs som används.
- `sig` Signatur.

Signatur-String är SHA-256-hash som beräknats över resurs-URI: n (enligt beskrivningen i föregående avsnitt) och sträng representationen av token som upphör att gälla, avgränsade med CRLF.

Hash-beräkningen ser ut ungefär som följande pseudo-kod och returnerar ett hash-värde på 256 bitar/32 byte. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token innehåller de värden som inte är hash-kodade, så att mottagaren kan beräkna hashen på samma parametrar och kontrol lera att utfärdaren har en giltig signerings nyckel.

Resurs-URI är den fullständiga URI: n för den Service Bus resurs som åtkomst begärs till. Till exempel http:// <namespace> . ServiceBus.Windows.net/ <entityPath> eller `sb://<namespace>.servicebus.windows.net/<entityPath>;` dvs `http://contoso.servicebus.windows.net/eventhubs/eh1` .

URI: n måste vara i procent kodad.

Den auktoriseringsregler för delad åtkomst som används för signering måste konfigureras på den entitet som anges av denna URI, eller av en av dess hierarkiska överordnade. Till exempel `http://contoso.servicebus.windows.net/eventhubs/eh1` eller `http://contoso.servicebus.windows.net` i föregående exempel.

En SAS-token är giltig för alla resurser som har prefixet som <resourceURI> används i Signature-sträng.

> [!NOTE]
> Du genererar en åtkomsttoken för Event Hubs med hjälp av principen för delad åtkomst. Mer information finns i [auktoriseringsprincipen för delad åtkomst](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Skapa en signatur (token) från en princip 
I följande avsnitt visas hur du genererar en SAS-token med signaturer för delad åtkomst

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>PROGRAMMERINGSSPRÅKET

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autentisera Event Hubs utgivare med SAS 
En händelse utgivare definierar en virtuell slut punkt för en Event Hub. Utgivaren kan endast användas för att skicka meddelanden till en Event Hub och inte ta emot meddelanden.

En Event Hub använder vanligt vis en utgivare per klient. Alla meddelanden som skickas till någon av en händelse Hubbs utgivare placeras i kö i den händelsehubben. Utgivare möjliggör detaljerad åtkomst kontroll.

Varje Event Hubs-klient tilldelas en unik token som överförs till klienten. Token skapas så att varje unik token beviljar åtkomst till en annan unik utgivare. En klient som innehåller en token kan bara skicka till en utgivare och ingen annan utgivare. Om flera klienter delar samma token delar var och en av dem utgivaren.

Alla tokens tilldelas med SAS-nycklar. Normalt är alla tokens signerade med samma nyckel. Klienterna är inte medvetna om nyckeln, vilket förhindrar att klienterna tillverkar tokens. Klienterna använder samma token tills de upphör att gälla.

Om du till exempel vill definiera auktoriseringsregler som är begränsade till att endast skicka/publicera till Event Hubs måste du definiera en auktoriseringsregel för att skicka. Detta kan göras på en namn områdes nivå eller ge mer detaljerad räckvidd till en viss entitet (Event Hub-instans eller ett ämne). En klient eller ett program som omfattas av den detaljerade åtkomsten kallas Event Hubs utgivare. Det gör du på följande sätt:

1. Skapa en SAS-nyckel på den entitet som du vill publicera för att tilldela **sändnings** omfånget. Mer information finns i [Auktoriseringsprinciper för delad åtkomst](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Generera en SAS-token med en förfallo tid för en speciell utgivare genom att använda nyckeln som genererats i steg 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Ange token till utgivar klienten, som bara kan skickas till entiteten och utgivaren som token beviljar åtkomst till.

    När token går ut förlorar klienten sin åtkomst till att skicka/publicera till entiteten. 


> [!NOTE]
> Även om det inte rekommenderas, är det möjligt att utrusta enheter med token som beviljar åtkomst till en Event Hub eller ett namn område. Alla enheter som innehåller denna token kan skicka meddelanden direkt till den händelsehubben. Dessutom kan enheten inte blocklisted från att skickas till den händelsehubben.
> 
> Vi rekommenderar alltid att du ger vissa och detaljerade omfång.

> [!IMPORTANT]
> När token har skapats etablerades varje klient med en egen unik token.
>
> När klienten skickar data till en händelsehubben, taggar den begäran med token. För att förhindra att en angripare åsidosätter och stjäla token måste kommunikationen mellan klienten och händelsehubben ske över en krypterad kanal.
> 
> Om ett token stjäls av en angripare kan angriparen imitera klienten vars token har blivit stulen. Blocklisting en utgivare, återger klienten oanvändbar tills den får en ny token som använder en annan utgivare.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autentisera Event Hubs konsumenter med SAS 
För att autentisera backend-program som använder sig av data som genereras av Event Hubs-producenter, kräver Event Hubs token-autentisering att dess klienter antingen har behörigheten **Hantera** eller behörigheten **lyssna** till dess Event Hubs-namnrymd eller Event Hub-instans eller ämne. Data förbrukas från Event Hubs med hjälp av konsument grupper. Medan SAS-principen ger dig detaljerad räckvidd, definieras denna omfattning endast på enhets nivå och inte på konsument nivå. Det innebär att behörigheterna som definierats på namn områdes nivån eller Event Hub-instansen eller ämnes nivån kommer att tillämpas på den entitetens konsument grupper.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Auktorisera med SAS](authenticate-shared-access-signature.md)
- [Auktorisera med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC)](authenticate-shared-access-signature.md)
- [Läs mer om Event Hubs](event-hubs-about.md)

Se följande relaterade artiklar:

- [Autentisera begär anden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md)
- [Ge åtkomst till Event Hubs resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
- [Ge åtkomst till Event Hubs resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)
