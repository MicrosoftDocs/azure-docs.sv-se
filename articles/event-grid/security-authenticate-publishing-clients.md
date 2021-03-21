---
title: Autentisera klienter som publicerar händelser för att Event Grid anpassade ämnen eller domäner
description: I den här artikeln beskrivs olika sätt att autentisera klienter som publicerar händelser för att Event Grid anpassade ämnen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94886268"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Autentisera publicerings klienter (Azure Event Grid)
Den här artikeln innehåller information om hur du autentiserar klienter som publicerar händelser till Azure Event Grid ämnen eller domäner med hjälp av **åtkomst nycklar** eller **SAS-token (signatur för delad åtkomst)** . Vi rekommenderar att du använder SAS-token, men Key Authentication tillhandahåller enkel programmering och är kompatibel med många befintliga webhook-utgivare.  

## <a name="authenticate-using-an-access-key"></a>Autentisera med hjälp av en åtkomst nyckel
Autentisering av åtkomst nycklar är den enklaste formen av autentisering. Du kan skicka åtkomst nyckeln som en HTTP-rubrik eller en URL-frågeparameter. 

### <a name="access-key-in-a-http-header"></a>Åtkomst nyckel i ett HTTP-huvud
Skicka åtkomst nyckeln som ett värde för HTTP-huvudet: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Åtkomst nyckel som frågeparameter
Du kan också ange `aeg-sas-key` som frågeparameter. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Instruktioner för hur du hämtar åtkomst nycklar för ett ämne eller en domän finns i [Hämta åtkomst nycklar](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>Autentisera med en SAS-token
SAS-token för en Event Grid resurs inkluderar resurs, förfallo tid och en signatur. Formatet på SAS-token är: `r={resource}&e={expiration}&s={signature}` .

Resursen är sökvägen till det event Grid-ämne som du skickar händelser till. Till exempel är en giltig resurs Sök väg: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` . Alla API-versioner som stöds finns i [resurs typer för Microsoft. EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

Skapa först en SAS-token och Använd sedan `aeg-sas-token` rubriken eller `Authorization SharedAccessSignature` rubriken för att autentisera med event Grid. 

### <a name="generate-sas-token-programmatically"></a>Generera SAS-token program mässigt
I följande exempel skapas en SAS-token för användning med Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Använda AEG – SAS-token-huvud
Här är ett exempel på hur du skickar SAS-token som ett värde för `aeg-sas-token` rubriken. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Använda Authorization-huvud
Här är ett exempel på hur du skickar SAS-token som ett värde för `Authorization` rubriken. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Nästa steg
Se [Event Delivery Authentication](security-authentication.md) för att lära dig mer om autentisering med händelse hanterare för att leverera händelser. 
