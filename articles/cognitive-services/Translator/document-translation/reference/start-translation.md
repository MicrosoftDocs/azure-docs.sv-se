---
title: Starta översättning
titleSuffix: Azure Cognitive Services
description: Starta en begäran om dokumentöversättning med tjänsten För dokumentöversättning.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 1d167962e22953a4a9ca69ece347f8427b9218c9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836316"
---
# <a name="start-translation"></a>Starta översättning

Använd det här API:et för att starta en massöversättningsbegäran (batch) med tjänsten för dokumentöversättning. Varje begäran kan innehålla flera dokument och måste innehålla en käll- och målcontainer för varje dokument.

Prefixet och suffixfiltret (om det anges) används för att filtrera mappar. Prefixet tillämpas på underökvägen efter containernamnet.

Ordlista/översättningsminne kan ingå i begäran och tillämpas av tjänsten när dokumentet översätts.

Om ordlistan är ogiltig eller inte kan nås under översättningen visas ett fel i dokumentets status. Om det redan finns en fil med samma namn vid målet skrivs den över. TargetUrl för varje målspråk måste vara unikt.

## <a name="request-url"></a>Begärans-URL

Skicka en `POST` begäran till:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Lär dig hur du hittar [ditt anpassade domännamn](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Alla API-begäranden till tjänsten För dokumentöversättning krävs en slutpunkt för en anpassad domän.**
> * Du kan inte använda slutpunkten som finns  på sidan Azure Portal nycklar och slutpunkter eller slutpunkten för global översättning– – för att göra `api.cognitive.microsofttranslator.com` HTTP-begäranden till dokumentöversättning.

## <a name="request-headers"></a>Begärandehuvuden

Begärandehuvudena är:

|Sidhuvuden|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Begärandehuvud som krävs|

## <a name="request-body-batch-submission-request"></a>Begärandetext: Batch-överföringsbegäran

|Namn|Typ|Description|
|--- |--- |--- |
|Ingångar|BatchRequest[]|BatchRequest visas nedan. Indatalistan över dokument eller mappar som innehåller dokument. Medietyper: "application/json", "text/json", "application/*+json".|

### <a name="inputs"></a>Indata

Definition för begäran om batchöversättning av indata.

|Namn|Typ|Obligatorisk|Beskrivning|
|--- |--- |--- |--- |
|källa|SourceInput[]|Sant|inputs.source som anges nedan. Källa för indatadokumenten.|
|storageType|StorageInputType[]|Sant|inputs.storageType som anges nedan. Lagringstyp för källsträngen för indatadokument.|
|Mål|TargetInput[]|Sant|inputs.target som anges nedan. Plats för utdatamålet.|

**inputs.source**

Källa för indatadokumenten.

|Namn|Typ|Obligatorisk|Beskrivning|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Falskt|DocumentFilter[] visas nedan.|
|filter.prefix|sträng|Falskt|En case-sensitive prefix-sträng för att filtrera dokument i källsökvägen för översättning. När du till exempel använder en Blob-URI för Azure Storage använder du prefixet för att begränsa undermappar för översättning.|
|filter.suffix|sträng|Falskt|En case-sensitive suffixsträng för att filtrera dokument i källsökvägen för översättning. Detta används oftast för filnamnstillägg.|
|language|sträng|Falskt|Språkkod Om ingen anges utför vi automatisk identifiering av dokumentet.|
|sourceUrl|sträng|True|Plats för mappen/containern eller en enskild fil med dina dokument.|
|storageSource|StorageSource|Falskt|StorageSource visas nedan.|
|storageSource.AzureBlob|sträng|Falskt||

**inputs.storageType**

Lagringstyp för källsträngen för indatadokument.

|Namn|Typ|
|--- |--- |
|file|sträng|
|mapp|sträng|

**inputs.target**

Mål för de färdiga översatta dokumenten.

|Namn|Typ|Obligatorisk|Beskrivning|
|--- |--- |--- |--- |
|category|sträng|Falskt|Kategori/anpassat system för översättningsbegäran.|
|Ordlistor|Ordlista[]|Falskt|Ordlista som listas nedan. Lista över ordlista.|
|glossaries.format|sträng|Falskt|Format.|
|glossaries.glossaryUrl|sträng|True (om du använder ordlistor)|Platsen för ordlistan. Vi använder filnamnstillägget för att extrahera formateringen om formatparametern inte anges. Om översättningsspråkparet inte finns med i ordlistan tillämpas det inte.|
|glossaries.storageSource|StorageSource|Falskt|StorageSource som anges ovan.|
|targetUrl|sträng|True|Plats för mappen/containern med dina dokument.|
|language|sträng|True|Kod för målspråk med två bokstäver. Se [listan över språkkoder](../../language-support.md).|
|storageSource|StorageSource []|Falskt|StorageSource [] som anges ovan.|
|version|sträng|Falskt|Version.|

## <a name="example-request"></a>Exempelbegäran

Följande är exempel på batchbegäranden.

**Översätta alla dokument i en container**

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

**Översätta alla dokument i en container med ordlistor**

Se till att du har skapat en URL & SAS-token för den specifika bloben/dokumentet (inte för containern)

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

**Översätta en specifik mapp i en container**

Se till att du har angett mappnamnet (ärendekänsligt) som prefix i filtret – även om SAS-token fortfarande är för containern.

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

**Översätta ett specifikt dokument i en container**

* Kontrollera att du har angett "storageType": "File"
* Kontrollera att du har skapat käll-URL & SAS-token för den specifika bloben/dokumentet (inte för containern)
* Se till att du har angett målfilnamnet som en del av mål-URL:en – även om SAS-token fortfarande är för containern.
* Exempelbegäran nedan visar ett enskilt dokument som översätts till två målspråk

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

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar.

|Statuskod|Description|
|--- |--- |
|202|Accepterade. En lyckad begäran och batchbegäran skapas av tjänsten. Rubriken som Operation-Location en status-URL med åtgärden ID.HeadersOperation-Location: sträng|
|400|Felaktig begäran. Ogiltig begäran. Kontrollera indataparametrar.|
|401|Obehörig. Kontrollera dina autentiseringsuppgifter.|
|429|Förfrågningsfrekvensen är för hög.|
|500|Internt serverfel.|
|503|Tjänsten är för närvarande inte tillgänglig.  Försök igen senare.|
|Andra statuskoder|<ul><li>För många begäranden</li><li>Tillfälligt otillgänglig server</li></ul>|

## <a name="error-response"></a>Felsvar

|Namn|Typ|Description|
|--- |--- |--- |
|kod|sträng|Uppräkningar som innehåller felkoder på hög nivå. Möjliga värden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Behörighet saknas</li></ul>|
|meddelande|sträng|Hämtar felmeddelande på hög nivå.|
|innerError|InnerErrorV2|Nytt format för inre fel, som överensstämmer Cognitive Services API-riktlinjer. Den innehåller de obligatoriska egenskaperna ErrorCode, message och optional properties target, details(key value pair), inner error (detta kan kapslas).|
|Inre. Felkod|sträng|Hämtar kodfelsträngen.|
|innerError.message|sträng|Hämtar felmeddelande på hög nivå.|

## <a name="examples"></a>Exempel

### <a name="example-successful-response"></a>Exempel på lyckat svar

Följande information returneras i ett lyckat svar.

Du hittar jobb-ID:t i POST-metodens svarshuvud och Operation-Location URL-värde. Den sista parametern i URL:en är åtgärdens jobb-ID (strängen efter "/operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Exempel på felsvar

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

Följ vår snabbstart om du vill veta mer om hur du använder dokumentöversättning och klientbiblioteket.

> [!div class="nextstepaction"]
> [Kom igång med dokumentöversättning](../get-started-with-document-translation.md)
