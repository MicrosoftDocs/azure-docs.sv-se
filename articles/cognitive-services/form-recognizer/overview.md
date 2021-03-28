---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Med Azure formulär igenkännings tjänsten kan du identifiera och extrahera nyckel/värde-par och tabell data från dina formulär dokument, samt extrahera större information från försäljnings kvitton och visitkort.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: automatisk data bearbetning, dokument bearbetning, automatisk data inmatning, formulär bearbetning
ms.openlocfilehash: 8d6c2ea760b85e3170b26c63a28d4ac2b3a2ef5a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639460"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure formulär igenkänning är en kognitiv tjänst som gör att du kan skapa automatiserad data bearbetnings program vara med hjälp av Machine Learning-teknik. Identifiera och extrahera text, nyckel/värde-par, markerings märken, tabeller och struktur från dina dokument. &mdash; tjänsten matar ut strukturerade data som innehåller relationerna i original filen, avgränsnings rutor, förtroende och mer. Du får snabbt korrekta resultat som skräddarsys efter ditt eget innehåll utan att du behöver göra en kraftig eller omfattande data vetenskaps expert. Använd formulär igenkänning för att automatisera data inmatning i dina program och förbättra dina dokument Sök funktioner.

Formulär tolken består av anpassade dokument bearbetnings modeller, färdiga modeller för fakturor, kvitton, ID: n, visitkort och layout modellen. Du kan anropa formulär igenkännings modeller genom att använda ett REST API-eller klient biblioteks-SDK: er för att minska komplexiteten och integrera dem i arbets flödet eller programmet.

Den här dokumentationen innehåller följande artikel typer:  

* [**Snabb starter**](quickstarts/client-library.md) hjälper dig att komma igång med instruktioner för att göra förfrågningar till tjänsten.  
* [**Instruktions guider**](build-training-data-set.md) innehåller instruktioner för att använda tjänsten på mer exakta eller anpassade sätt.  
* [**Begreppen**](concept-layout.md) ger djupgående förklaringar av tjänst funktionerna och funktionerna.  
* [**Självstudier**](tutorial-bulk-processing.md) är längre guider som visar hur du använder tjänsten som en komponent i bredare affärs lösningar.  

## <a name="form-recognizer-features"></a>Formulär igenkännings funktioner

Med formulär igenkänning kan du enkelt extrahera och analysera formulär data med dessa funktioner:

* **[Layout-API](#layout-api)** – Extrahera text, markerings märken och tabeller strukturer, tillsammans med deras gränser för avgränsnings rutor från dokument.
* **[Anpassade modeller](#custom-models)** – Extrahera text, nyckel/värde-par, markerings märken och tabell data från formulär. De här modellerna tränas med dina egna data, så de skräddarsys efter dina formulär.

* **[Färdiga modeller](#prebuilt-models)** – extrahera data från unika dokument typer med hjälp av förinställda modeller. För närvarande finns följande färdiga modeller

  * [Fakturor](./concept-invoices.md)
  * [Försäljnings kvitton](./concept-receipts.md)
  * [Visitkort](./concept-business-cards.md)
  * [ID-kort](./concept-identification-cards.md)


## <a name="get-started"></a>Kom igång

Använd exempel verktyget igenkännings verktyg för att testa layout, färdiga modeller och träna en anpassad modell för dina dokument. Du behöver en Azure-prenumeration ([**skapa en kostnads fri**](https://azure.microsoft.com/free/cognitive-services)) och en resurs slut punkt för [**formulär igenkänning**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) och en nyckel för att testa formulär igenkännings tjänsten.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

> [!div class="nextstepaction"]
> [Testa formulär tolken](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Testa formulär tolken](https://fott.azurewebsites.net/)

---
Följ [klient biblioteket/REST API snabb start](./quickstarts/client-library.md) för att komma igång med att extrahera data från dina dokument. Vi rekommenderar att du använder den kostnads fria tjänsten när du lär dig tekniken. Kom ihåg att antalet fria sidor är begränsat till 500 per månad.

Du kan också använda REST-exemplen (GitHub) för att komma igång- 

* Extrahera text, markerings märken och tabell struktur från dokument
  * [Extrahera layoutinformation – python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Träna anpassade modeller och extrahera formulär data
  * [Träna utan etiketter – python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Träna med etiketter – python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Extrahera data från fakturor
  * [Extrahera faktura data – python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Extrahera data från försäljnings kvitton
  * [Extrahera kvitto data – python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Extrahera data från visitkort
  * [Extrahera visitkorts data – python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Granska REST-API: erna

Du använder följande API: er för att träna modeller och extrahera strukturerade data från formulär.

|Name |Beskrivning |
|---|---|
| **Analysera layout** | Analysera ett dokument som skickas som en data ström för att extrahera text, markerings märken, tabeller och struktur från dokumentet |
| **Träna anpassad modell**| Träna en ny modell för att analysera dina formulär genom att använda fem formulär av samma typ. Ange parametern _useLabelFile_ för `true` att träna med manuellt märkta data. |
| **Analysera formulär** |Analysera ett formulär som skickats in som en ström för att extrahera text, nyckel/värde-par och tabeller från formuläret med din anpassade modell.  |
| **Analysera faktura** | Analysera en faktura för att extrahera viktig information, tabeller och annan faktura text.|
| **Analysera inleverans** | Analysera ett kvitto dokument för att extrahera viktig information och annan kvitto text.|
| **Analysera ID** | Analysera ett ID-kort dokument för att extrahera viktig information och annan identifierings kort text.|
| **Visitkort för analys** | Analysera ett visitkort för att extrahera viktig information och text.|

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

Mer information får du genom att utforska [REST API referens dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) . Om du är bekant med en tidigare version av API: n läser du artikeln [Nyheter](./whats-new.md) och lär dig mer om de senaste ändringarna.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Mer information får du genom att utforska [REST API referens dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) . Om du är bekant med en tidigare version av API: n läser du artikeln [Nyheter](./whats-new.md) och lär dig mer om de senaste ändringarna.

---

## <a name="layout-api"></a>Layout-API

Formulär tolken kan extrahera text, markerings märken och tabell struktur (rad-och kolumn nummer som är associerade med texten) med hjälp av optisk tecken läsning (OCR) med hög upplösning och en förbättrad djup inlärnings modell från dokument. Mer information finns i rikt linjer för [layout](./concept-layout.md) .

:::image type="content" source="./media/tables-example.jpg" alt-text="tabell exempel" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Anpassade modeller

Formulär igenkännings anpassade modeller tränar dina egna data och du behöver bara fem exempel inmatnings formulär att starta. En tränad dokument bearbetnings modell kan spara strukturerade data som innehåller relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

Du har följande alternativ när du tränar anpassade modeller: utbildning med märkta data och utan etiketterade data.

### <a name="train-without-labels"></a>Träna utan etiketter

Formulär tolken använder obevakad inlärning för att förstå layouten och relationerna mellan fält och poster i formulären. När du skickar in dina indata-formulär, identifierar algoritmen formulären efter typ, identifierar vilka nycklar och tabeller som finns och associerar värden till nycklar och poster i tabeller. Utbildning utan etiketter kräver inte manuella data etiketter eller intensiv kodning och underhåll, och vi rekommenderar att du provar den här metoden först.

Se [skapa en tränings data uppsättning](./build-training-data-set.md) för tips om hur du samlar in utbildnings dokument.

### <a name="train-with-labels"></a>Träna med etiketter

När du tränar med märkta data använder modellen övervakad inlärning för att extrahera värden av intresse med hjälp av de märkta formulär som du anger. Märkta data resulterar i bättre modeller och kan skapa modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.

Formulär tolken använder [layout-API: t](#layout-api) för att lära dig de förväntade storlekarna och positionerna för de utskrivna och handskrivna text elementen och Sedan använder den användardefinierade etiketter för att lära sig nyckel-och värde associationer och tabeller i dokumenten. Vi rekommenderar att du använder fem manuellt märkta formulär av samma typ (samma struktur) för att komma igång när du tränar en ny modell och lägger till mer märkta data vid behov för att förbättra modellens noggrannhet. Formulär tolken gör det möjligt att träna en modell för att extrahera nyckel värdes par och tabeller med hjälp av övervakade inlärnings funktioner. 

[Kom igång med träna med etiketter](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Fördefinierade modeller

Formulär tolken innehåller också färdiga modeller för automatisk data bearbetning av unika formulär typer.

### <a name="prebuilt-invoice-model"></a>Fördefinierad faktura modell

Den fördefinierade faktura modellen hämtar data från fakturor i olika format och returnerar strukturerade data. Den här modellen extraherar viktig information som faktura-ID, kund information, leverantörs information, leverans till, faktura till, Summa, skatt, Delsumma, rad artiklar med mera. Dessutom tränas den förinställda faktura modellen för att analysera och returnera all text och alla tabeller på fakturan. Mer information finns i konceptuell guide för [fakturor](./concept-invoices.md) .

:::image type="content" source="./media/overview-invoices.jpg" alt-text="exempel faktura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Fördefinierad kvitto modell

Fördefinierad kvitto modell används för att läsa kvitton på engelska försäljning från Australien, Kanada, Storbritannien, Indien och USA typ som &mdash; används av restauranger, gas stationer, åter försäljare och så vidare. Den här modellen hämtar viktig information, till exempel tid och datum för transaktionen, handels information, moms belopp, rad artiklar, total summor och mer. Dessutom tränas den förskapade kvitto modellen att analysera och returnera all text i ett kvitto. Mer information finns i Guide för [kvitton](./concept-receipts.md) .

:::image type="content" source="./media/overview-receipt.jpg" alt-text="exempel kvitto" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Fördefinierad identifiering (ID) kort modell

Med kort modellen identifiering (ID) kan du extrahera viktig information från Världs omfattande Passport och amerikanska driv rutins licenser. Den extraherar data, till exempel dokument-ID, förfallo datum, datum för utgångs datum, namn, land, region, maskinläsbar zon med mera. Mer information finns på [kortet Identification (ID) korts](./concept-identification-cards.md) konceptuell guide.

:::image type="content" source="./media/overview-id.jpg" alt-text="exempel på identifierings kort" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Fördefinierad visitkorts modell

Med visitkorts modellen kan du extrahera information som personens namn, befattning, adress, e-post, företags nummer och telefonnummer från visitkort på engelska. Mer information finns i konceptuell guide för [visitkort](./concept-business-cards.md) .

:::image type="content" source="./media/overview-business-card.jpg" alt-text="exempel på visitkort" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Krav för indatamängd

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-behållare

[Använd formulär igenkännings behållare (för hands version)](form-recognizer-container-howto.md) för att distribuera API-funktioner lokalt. Med den här Docker-behållaren kan du ta tjänsten närmare dina data för efterlevnad, säkerhet eller andra drift orsaker.

## <a name="service-availability-and-redundancy"></a>Tjänst tillgänglighet och redundans

### <a name="is-form-recognizer-service-zone-resilient"></a>Är formulär tolkens service zon – elastisk?

Ja. Formulär igenkännings tjänsten är zon-flexibel som standard.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Hur gör jag för att konfigurerar du att formulär tolks tjänsten ska vara zoner-elastisk?

Ingen kund konfiguration krävs för att aktivera zon återhämtning. Zon-återhämtning för formulär igenkännings resurser är tillgängligt som standard och hanteras av själva tjänsten.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla kognitiva tjänster bör utvecklare som använder formulär tolknings tjänsten vara medvetna om Microsofts principer för kund information. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Prova vårt online verktyg och snabb start för att lära dig mer om formulär igenkännings tjänsten.

* [**Formulär tolks verktyget**](https://fott-preview.azurewebsites.net/)
* [**Klient bibliotek och REST API snabb start**](quickstarts/client-library.md)
