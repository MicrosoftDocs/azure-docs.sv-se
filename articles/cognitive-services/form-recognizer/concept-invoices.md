---
title: Fakturor-formulär identifierare
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör faktura analys med formatet tolknings-API-användning och begränsningar.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: a47c4c5bdc90e148916900b1e72bc2a392d2e473
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285343"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Fördefinierad faktura modell för formulär tolken

Azure formulär tolken kan analysera och extrahera information från försäljnings fakturor med hjälp av inbyggda faktura modeller. Med faktura-API: et kan kunder ta fakturor i olika format och returnera strukturerade data för att automatisera faktura bearbetningen. Den kombinerar vår kraftfulla [OCR-kapacitet (optisk tecken läsning)](../computer-vision/overview-ocr.md) med faktura om djup inlärnings modeller för att extrahera viktig information från fakturor på engelska. Den extraherar text, tabeller och information, till exempel kund, leverantör, faktura-ID, förfallo datum för faktura, totalt, Fakturerat belopp, moms belopp, leverera till, fakturera till, rad artiklar med mera. Det inbyggda faktura-API: t är offentligt tillgängligt i för hands versionen av formulär igenkänning v 2.1.

## <a name="what-does-the-invoice-service-do"></a>Vad gör faktura tjänsten?

Faktura-API: n extraherar nyckel fält och rad objekt från fakturor och returnerar dem i ett ordnat strukturerat JSON-svar. Fakturor kan vara från en rad olika format och kvalitet, inklusive hämtade bilder, skannade dokument och digitala PDF-filer. Med faktura-API: et extraheras strukturerad utdata från alla dessa fakturor. 

![Exempel på Contoso-faktura](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>Prova

Om du vill testa faktura tjänsten för formulär tolken går du till verktyget online-exempel UI:

> [!div class="nextstepaction"]
> [Prova färdiga modeller](https://fott-preview.azurewebsites.net/)

Du behöver en Azure-prenumeration ([skapa en kostnads fri](https://azure.microsoft.com/free/cognitive-services)) och en resurs slut punkt för [formulär igenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) och en nyckel för att testa formulär tolkens faktura tjänst. 

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Exempel på analyserad faktura" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Krav för indatamängd

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>Faktura åtgärden analysera

[Faktura åtgärden analysera](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291) tar en bild eller PDF av en faktura som indata och extraherar värdena för ränta. Anropet returnerar ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det resultat-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Resultat åtgärden hämta faktura resultat

Det andra steget är att anropa åtgärden för att [analysera resultat för faktura resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83) . Den här åtgärden tar in det resultat-ID som skapades av faktura åtgärden analysera. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. Du anropar den här åtgärden iterativt tills den returnerar värdet **lyckades** . Använd ett intervall på 3 till 5 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | notStarted: analys åtgärden har inte startats.<br /><br />körs: analys åtgärden pågår.<br /><br />misslyckades: det gick inte att utföra analysen.<br /><br />lyckades: analys åtgärden har slutförts.|

När värdet i fältet **status** har värdet **lyckades** inkluderar JSON-svaret faktura förståelsens resultat, tabeller extraherade och valfria text igenkännings resultat om det behövs. Resultatet av faktura förståelsen är ordnat som en ord lista med namngivna fält värden där varje värde innehåller den extraherade texten, normaliserade värdet, avgränsnings rutan, relevansen och motsvarande Word-element. Den innehåller också de rad objekt som extraheras där varje rad objekt innehåller belopp, beskrivning, enhets pris, antal osv. Resultatet av text igenkänningen är ordnat som en hierarki med rader och ord, med text, avgränsnings ram och information om säkerhet.

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Svaret på resultat åtgärden hämta faktura resultat blir den strukturerade åter givningen av fakturan med all information som extraheras. Här visas en [exempel faktura fil](media/sample-invoice.jpg) och dess strukturerade utdata för [exempel faktura](media/invoice-example-new.jpg).

JSON-utdata har 3 delar: 
* `"readResults"` noden innehåller alla tolkade text-och markerings märken. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. 
* `"pageResults"` Node innehåller tabeller och celler som extraheras med deras avgränsnings rutor, relevans och referenser till rader och ord i "readResults".
* `"documentResults"` noden innehåller de specifika värdena och rad objekt som modellen identifierade. Här hittar du alla fält från fakturan, till exempel faktura-ID, leverera till, fakturera till, kund, Summa, rad artiklar och mycket mer.

## <a name="example-output"></a>Exempel på utdata

Faktura tjänsten kommer att extrahera fälten text, tabeller och 26 fakturor. Nedan visas fälten som extraheras från en faktura i JSON-utdata (resultatet nedan använder den här [exempel fakturan](media/sample-invoice.jpg)).

|Namn| Typ | Beskrivning | Text | Värde (standardiserad utdata) |
|:-----|:----|:----|:----| :----|
| CustomerName | sträng | Kunden faktureras | Microsoft Corp |  |
| CustomerId | sträng | Referens-ID för kunden | CID – 12345 |  |
| PurchaseOrder | sträng | Ett referens nummer för inköps order | PO – 3333 | | 
| InvoiceId | sträng | ID för den här aktuella fakturan (ofta "faktura nummer") | INV-100 | | 
| InvoiceDate | date | Datum då fakturan utfärdades | 11/15/2019 | 2019-11-15 | 
| DueDate | date | Datum betalning för den här fakturan är förfallen | 12/15/2019 | 2019-12-15 |
| Namn | sträng | Leverantör som har skapat den här fakturan | CONTOSO LTD. | |
| VendorAddress | sträng | E-postadress för leverantören | 123 456th St New York, Sverige, 10001 | |
| VendorAddressRecipient | sträng | Namn som är associerat med VendorAddress | Contoso Headquarters | |
| CustomerAddress | sträng | Post adress för kunden | 123 annat St, Redmond WA, 98052 | |
| CustomerAddressRecipient | sträng | Namn som är associerat med CustomerAddress | Microsoft Corp | |
| BillingAddress | sträng | Explicit fakturerings adress för kunden | 123 Bill St, Redmond, 98052 | |
| BillingAddressRecipient | sträng | Namn som är associerat med BillingAddress | Microsoft-tjänster | |
| ShippingAddress | sträng | Explicit leverans adress för kunden | 123 leverera St, Redmond, 98052 | |
| ShippingAddressRecipient | sträng | Namn som är associerat med ShippingAddress | Microsoft-leverans | |
| Delsumma | antal | Fält summa som har identifierats på den här fakturan | $100,00 | 100 | 
| TotalTax | antal | Totalt moms fält har identifierats på den här fakturan | $10,00 | 10 |
| InvoiceTotal | antal | Totalt antal nya avgifter som är kopplade till den här fakturan | $110,00 | 110 |
| AmountDue |  antal | Totalt belopp på grund av leverantören | $610,00 | 610 |
| ServiceAddress | sträng | Explicit tjänst adress eller egenskaps adress för kunden | 123 tjänst St, Redmond WA, 98052 | |
| ServiceAddressRecipient | sträng | Namn som är associerat med ServiceAddress | Microsoft-tjänster | |
| RemittanceAddress | sträng | Explicit remittering eller betalnings adress för kunden | 123 remittera St New York, Sverige, 10001 |  |
| RemittanceAddressRecipient | sträng | Namn som är associerat med RemittanceAddress | Contoso-fakturering |  |
| ServiceStartDate | date | Första datumet för service perioden (till exempel en service period för verktyg) | 2019-10-14 | 2019-10-14 |
| ServiceEndDate | date | Slutdatum för service perioden (till exempel en service period för verktyg) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | antal | Explicit tidigare obetald balans | $500,00 | 500 |

Nedan visas de rad objekt som har extraherats från en faktura i JSON-utdata (utdata nedan använder den här [exempel fakturan](./media/sample-invoice.jpg))  

|Namn| Typ | Beskrivning | Text (rad objekt #1) | Värde (standardiserad utdata) |
|:-----|:----|:----|:----| :----|
| Poster | sträng | Fullständig sträng text rad i rad objektet | 3/4/2021 A123 Consulting Services 2 timmar $30,00 10% $60,00 | |
| Amount | antal | Rad objektets belopp | $60,00 | 100 |
| Description | sträng | Text beskrivningen för faktura rads objektet | Konsult tjänst | Konsult tjänst |
| Kvantitet | antal | Kvantitet för den här faktura rads posten | 2 | 2 |
| UnitPrice | antal | Netto priset eller brutto priset (beroende på fakturans brutto faktura inställning) för en enhet av det här objektet | $30,00 | 30 |
| ProductCode | sträng| Produkt kod, produkt nummer eller SKU som är associerad med det specifika rad objektet | A123 | |
| Enhet | sträng| Enhetens enhets objekt t. ex. kg, lb. fl. | timmar | |
| Datum | date| Datum för varje rad objekt. Det här är ofta ett datum då rad artikeln levererades | 3/4/2021| 2021-03-04 |
| Skatt | antal | Skatt som är kopplad till varje rad objekt. Möjliga värden är skatte belopp, momssats% och skatt Y/N | 10 % | |


## <a name="next-steps"></a>Nästa steg

- Testa dina egna fakturor och exempel i [formulär tolkens exempel gränssnitt](https://fott-preview.azurewebsites.net/).
- Slutför snabb starten för [formulär tolken](quickstarts/client-library.md) och kom igång genom att skriva en app för faktura bearbetning med formulär tolken på det utvecklings språk du väljer.

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](./overview.md)
* [REST API referens dokument](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
