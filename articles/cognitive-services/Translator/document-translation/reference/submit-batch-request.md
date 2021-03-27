---
title: Skicka batch-begäran för dokument Översättning
titleSuffix: Azure Cognitive Services
description: Skicka en begäran om dokument översättning till dokument översättnings tjänsten.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613097"
---
# <a name="document-translation-submit-batch-request"></a>Dokument översättning: skicka batch-begäran

Använd det här API: et för att skicka en Mass översättning av begäran till dokument översättnings tjänsten. Varje begäran kan innehålla flera dokument och måste innehålla en käll-och mål behållare för varje dokument.

Prefix-och suffix-filtret (om det anges) används för att filtrera mappar. Prefixet tillämpas på under Sök vägen efter behållar namnet.

Glossaries/översättnings minne kan tas med i begäran och tillämpas av tjänsten när dokumentet översätts.

Om ord listan är ogiltig eller inte kan komma åt under översättningen anges ett fel i dokumentets status. Om det redan finns en fil med samma namn på målet, kommer den att skrivas över. TargetUrl för varje mål språk måste vara unikt.

## <a name="request-url"></a>Begärans-URL

Skicka en `POST` begäran till:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Lär dig hur du hittar ditt [anpassade domän namn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-förfrågningar till dokument översättnings tjänsten kräver en anpassad domän slut punkt**.
> * Du kan inte använda slut punkten som finns på Azure Portal resurs _nycklar och slut punkts_ sida eller den globala Translator-slutpunkten – `api.cognitive.microsofttranslator.com` för att göra HTTP-förfrågningar till dokument översättning.

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvuden är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Nödvändigt begär ande huvud|

## <a name="request-body-batch-submission-request"></a>Begär ande text: begäran om batch-sändning

|Namn|Typ|Description|
|--- |--- |--- |
|tillför|BatchRequest[]|BatchRequest visas nedan. Indatalistan för dokument eller mappar som innehåller dokument. Medie typer: "Application/JSON", "text/JSON", "Application/* + JSON".|

### <a name="inputs"></a>Indata

Definition av begäran om översättning av indatamängden.

|Namn|Typ|Obligatorisk|Beskrivning|
|--- |--- |--- |--- |
|källa|SourceInput[]|Sant|indata. källa visas nedan. Källa för inmatade dokument.|
|storageType|StorageInputType[]|Sant|Inputs. storageType visas nedan. Lagrings typ för käll strängen för indata-dokument.|
|mål|TargetInput[]|Sant|Inputs. Target anges nedan. Plats för målet för utdata.|

**indata. källa**

Källa för inmatade dokument.

|Namn|Typ|Obligatorisk|Beskrivning|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Falskt|DocumentFilter [] visas nedan.|
|filter. prefix|sträng|Falskt|En Skift läges känslig prefixlängd för att filtrera dokument i käll Sök vägen för översättning. Om du till exempel använder en BLOB-URI för Azure Storage, använder du prefixet för att begränsa undermappar för översättning.|
|Filtrera. suffix|sträng|Falskt|En Skift läges känslig suffix sträng för att filtrera dokument i käll Sök vägen för översättning. Detta används oftast för fil namns tillägg.|
|language|sträng|Falskt|Språkkod om inget anges, kommer vi att utföra automatisk identifiering i dokumentet.|
|sourceUrl|sträng|True|Platsen för mappen/behållaren eller en enskild fil med dina dokument.|
|storageSource|StorageSource|Falskt|StorageSource visas nedan.|
|storageSource. AzureBlob|sträng|Falskt||

**Inputs. storageType**

Lagrings typ för käll strängen för indata-dokument.

|Namn|Typ|
|--- |--- |
|file|sträng|
|mapp|sträng|

**Inputs. Target**

Målet för de färdiga översatta dokumenten.

|Namn|Typ|Obligatorisk|Beskrivning|
|--- |--- |--- |--- |
|category|sträng|Falskt|Kategori/anpassat system för översättnings förfrågan.|
|glossaries|Ord lista []|Falskt|Ord lista nedan. Lista över ord listor.|
|Glossaries. format|sträng|Falskt|Formatering.|
|glossaries.glossaryUrl|sträng|Sant (om du använder Glossaries)|Ord listans plats. Vi kommer att använda fil namns tillägget för att extrahera formateringen om format parametern inte anges. Om översättnings språk paret inte finns i ord listan, används det inte.|
|glossaries.storageSource|StorageSource|Falskt|StorageSource som anges ovan.|
|targetUrl|sträng|True|Platsen för mappen/behållaren med dina dokument.|
|language|sträng|True|Två tecken för mål språk kod. Se [lista över språk koder](../../language-support.md).|
|storageSource|StorageSource []|Falskt|StorageSource [] som anges ovan.|
|version|sträng|Falskt|2.0.1.|

## <a name="example-request"></a>Exempelbegäran

Följande är exempel på batch-begäranden.

**Översättning av alla dokument i en behållare**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Översättning av alla dokument i en behållare som använder Glossaries**

Se till att du har skapat ord listans URL & SAS-token för det angivna blobben/dokumentet (inte för behållaren)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Översätta en speciell mapp i en behållare**

Se till att du har angett mappnamnet (Skift läges känsligt) som prefix i filtret – även om SAS-token fortfarande är för behållaren.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Översättning av ett särskilt dokument i en behållare**

* Kontrol lera att du har angett "storageType": "File"
* Se till att du har skapat käll-URL & SAS-token för angiven BLOB/dokument (inte för containern)
* Se till att du har angett mål fil namnet som en del av mål-URL: en, även om SAS-token fortfarande är för behållaren.
* Exempel förfrågan nedan visar ett enda dokument som översätts till två mål språk

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|202|Accept. Lyckad begäran och batch-begäran skapas av tjänsten. Rubriken Operation-Location anger en status-URL med åtgärds-ID. HeadersOperation-Location: String|
|400|Felaktig begäran. Ogiltig begäran. Kontrol lera indataparametrarna.|
|401|Tillstånd. Kontrol lera dina autentiseringsuppgifter.|
|429|Begär ande frekvensen är för hög.|
|500|Internt Server fel.|
|503|Tjänsten är inte tillgänglig för tillfället.  Försök igen senare.|
|Andra status koder|<ul><li>För många begär Anden</li><li>Servern är tillfälligt otillgänglig</li></ul>|

## <a name="error-response"></a>Fel svar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller fel koder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar fel meddelande på hög nivå.|
|innerError|InnerErrorV2|Nytt inre fel format, som följer Cognitive Services API-riktlinjer. Den innehåller de obligatoriska egenskaperna ErrorCode, meddelande och valfria egenskaper mål, information (nyckel värde par), inre fel (detta kan kapslas).|
|innersta. ErrorCode|sträng|Hämtar kod fel sträng.|
|innerError. Message|sträng|Hämtar fel meddelande på hög nivå.|

## <a name="examples"></a>Exempel

### <a name="example-successful-response"></a>Exempel på lyckat svar

Följande information returneras i ett lyckat svar.

Du hittar jobb-ID: t i POST-metodens svars huvud Operation-Location URL-värde. Den sista parametern i URL: en är åtgärdens jobb-ID (strängen nedan "/operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Exempel fel svar

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

Följ vår snabb start för att lära dig mer om hur du använder dokument översättning och klient biblioteket.

> [!div class="nextstepaction"]
> [Kom igång med dokument Översättning](../get-started-with-document-translation.md)
