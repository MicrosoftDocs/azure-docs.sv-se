---
title: Vad är formigenkänning?
titleSuffix: Azure Cognitive Services
description: Med Azure Formigenkänning-tjänsten kan du identifiera och extrahera nyckel/värde-par och tabelldata från dina formulärdokument, samt extrahera viktig information från kvitton och visitkort.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: automatiserad databehandling, dokumentbearbetning, automatisk datainmatning, formulärbearbetning
ms.openlocfilehash: 680bb612546aaffc167970c1c48a44159ef9af6f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518239"
---
# <a name="what-is-form-recognizer"></a>Vad är formigenkänning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Formigenkänning är en kognitiv tjänst som gör att du kan skapa programvara för automatiserad databehandling med hjälp av maskininlärningsteknik. Identifiera och extrahera text, nyckel/värde-par, markeringsmarkeringar, tabeller och struktur från dina dokument som tjänsten matar ut strukturerade data som innehåller relationerna i den ursprungliga filen, avgränsade &mdash; rutor, konfidens med mera. Du får snabbt korrekta resultat som är skräddarsydda för ditt specifika innehåll utan omfattande manuella åtgärder eller omfattande datavetenskapskunskaper. Använd Formigenkänning för att automatisera datainmatning i dina program och utöka dina dokumentsökningsfunktioner.

Formigenkänning består av anpassade dokumentbearbetningsmodeller, fördefinierade modeller för fakturor, kvitton, ID:er och visitkort samt layoutmodellen. Du kan anropa Formigenkänning modeller med hjälp av en REST API-eller klientbiblioteks-SDK:er för att minska komplexiteten och integrera den i ditt arbetsflöde eller program.

Den här dokumentationen innehåller följande artikeltyper:  

* [**Snabbstarter**](quickstarts/client-library.md) är komma igång-instruktioner som vägleder dig genom att göra begäranden till tjänsten.  
* [**Instruktionsguider**](build-training-data-set.md) innehåller instruktioner för att använda tjänsten på mer specifika eller anpassade sätt.  
* [**Begrepp**](concept-layout.md) ger djupgående förklaringar av tjänstens funktioner och funktioner.  
* [**Självstudier**](tutorial-bulk-processing.md) är längre guider som visar hur du använder tjänsten som en komponent i bredare affärslösningar.  

## <a name="form-recognizer-features"></a>Formigenkänning funktioner

Med Formigenkänning kan du enkelt extrahera och analysera formulärdata med följande funktioner:

* **[Layout-API](#layout-api)** – Extrahera text, markeringsmarkeringar och tabellstrukturer, tillsammans med deras koordinater för en avgränsare, från dokument.
* **[Anpassade modeller](#custom-models)** – Extrahera text, nyckel/värde-par, markeringsmarkeringar och tabelldata från formulär. De här modellerna tränas med dina egna data, så de skräddarsys efter dina formulär.

* **[Fördefinierade modeller – Extrahera](#prebuilt-models)** data från unika dokumenttyper med hjälp av fördefinierade modeller. För närvarande är följande fördefinierade modeller tillgängliga

  * [Fakturor](./concept-invoices.md)
  * [Försäljningskvitton](./concept-receipts.md)
  * [Visitkort](./concept-business-cards.md)
  * [Id-kort](./concept-identification-cards.md)


## <a name="get-started"></a>Kom igång

Använd exempelverktyget Formigenkänning för att testa Layout, Färdiga modeller och träna en anpassad modell för dina dokument. Du behöver en Azure-prenumeration [**(skapa**](https://azure.microsoft.com/free/cognitive-services)en kostnadsfritt) och en Formigenkänning-resursslutpunkt och nyckel för att prova Formigenkänning tjänsten. [](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)

### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/v2-1)

> [!div class="nextstepaction"]
> [Prova Formigenkänning](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Prova Formigenkänning](https://fott.azurewebsites.net/)

---
Följ [snabbstarten Klientbibliotek/REST API för](./quickstarts/client-library.md) att komma igång med att extrahera data från dina dokument. Vi rekommenderar att du använder den kostnadsfria tjänsten när du lär dig tekniken. Kom ihåg att antalet kostnadsfria sidor är begränsat till 500 per månad.

Du kan också använda REST-exemplen (GitHub) för att komma igång – 

* Extrahera text, markeringsmarkeringar och tabellstruktur från dokument
  * [Extrahera layoutdata – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Träna anpassade modeller och extrahera formulärdata
  * [Träna utan etiketter – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Träna med etiketter – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Extrahera data från fakturor
  * [Extrahera fakturadata – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Extrahera data från försäljningskvitton
  * [Extrahera kvittodata – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Extrahera data från visitkort
  * [Extrahera visitkortsdata – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Granska REST-API:erna

Du använder följande API:er för att träna modeller och extrahera strukturerade data från formulär.

|Name |Beskrivning |
|---|---|
| **Analysera layout** | Analysera ett dokument som skickas som en ström för att extrahera text, markeringar, tabeller och struktur från dokumentet |
| **Träna anpassad modell**| Träna en ny modell att analysera dina formulär med hjälp av fem formulär av samma typ. Ange _parametern useLabelFile_ till `true` för att träna med manuellt märkta data. |
| **Analysera formulär** |Analysera ett formulär som skickas som en dataström för att extrahera text, nyckel/värde-par och tabeller från formuläret med din anpassade modell.  |
| **Analysera faktura** | Analysera en faktura för att extrahera viktig information, tabeller och annan fakturatext.|
| **Analysera kvitto** | Analysera ett kvittodokument för att extrahera nyckelinformation och annan kvittotext.|
| **Analysera ID** | Analysera ett ID-kortdokument för att extrahera viktig information och annan id-korttext.|
| **Analysera visitkort** | Analysera ett visitkort för att extrahera viktig information och text.|

### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/v2-1)

Utforska [referensdokumentationen REST API om du](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) vill veta mer. Om du är bekant med en tidigare version av API:et kan du läsa artikeln [Vad är nytt för att](./whats-new.md) lära dig mer om de senaste ändringarna.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Utforska [referensdokumentationen REST API om du](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) vill veta mer. Om du är bekant med en tidigare version av API:et kan du läsa artikeln [Vad är nytt för att](./whats-new.md) lära dig mer om de senaste ändringarna.

---

## <a name="layout-api"></a>Layout-API

Formigenkänning kan extrahera text, markeringsmarkeringar och tabellstruktur (rad- och kolumnnummer som är associerade med texten) med hjälp av optisk teckenläsning (OCR) med hög definition och en förbättrad djupinlärningsmodell från dokument. Mer information [finns](./concept-layout.md) i begreppsguide för layout.

:::image type="content" source="./media/tables-example.jpg" alt-text="tabellexempel" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Anpassade modeller

Formigenkänning anpassade modeller tränas till dina egna data och du behöver bara fem exempel på indataformulär för att starta. En tränad dokumentbearbetningsmodell kan mata ut strukturerade data som innehåller relationerna i det ursprungliga formulärdokumentet. När du har tränat modellen kan du testa och träna om den och så småningom använda den för att extrahera data från flera formulär på ett tillförlitligt sätt utifrån dina behov.

Du har följande alternativ när du tränar anpassade modeller: träning med märkta data och utan märkta data.

### <a name="train-without-labels"></a>Träna utan etiketter

Formigenkänning använder oövervakad inlärning för att förstå layouten och relationerna mellan fält och poster i dina formulär. När du skickar indataformulären klustrar algoritmen formulären efter typ, identifierar vilka nycklar och tabeller som finns och kopplar värden till nycklar och poster till tabeller. Träning utan etiketter kräver inte manuell dataetikettering eller intensiv kodning och underhåll, och vi rekommenderar att du provar den här metoden först.

Se [Skapa en uppsättning träningsdata för](./build-training-data-set.md) tips om hur du samlar in dina träningsdokument.

### <a name="train-with-labels"></a>Träna med etiketter

När du tränar med märkta data använder modellen övervakad inlärning för att extrahera värden av intresse med hjälp av de märkta formulär som du anger. Märkta data resulterar i bättre modeller och kan skapa modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.

Formigenkänning använder [layout-API:et](#layout-api) för att lära sig förväntade storlekar och positioner för utskrivna och handskrivna textelement och extrahera tabeller. Sedan används användar angivna etiketter för att lära sig nyckel-/värdeassociationer och tabeller i dokumenten. Vi rekommenderar att du använder fem manuellt märkta formulär av samma typ (samma struktur) för att komma igång när du tränar en ny modell och lägger till fler märkta data efter behov för att förbättra modellens noggrannhet. Formigenkänning kan träna en modell för att extrahera nyckelvärdepar och tabeller med hjälp av övervakade inlärningsfunktioner. 

[Kom igång med Träna med etiketter](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Fördefinierade modeller

Formigenkänning även fördefinierade modeller för automatiserad databearbetning av unika formulärtyper.

### <a name="prebuilt-invoice-model"></a>Förbyggd fakturamodell

Den fördefinierade fakturamodellen extraherar data från fakturor i olika format och returnerar strukturerade data. Den här modellen extraherar viktig information som faktura-ID, kundinformation, leverantörsinformation, skicka till, fakturera till, summa, skatt, delsumma, radartiklar med mera. Dessutom tränas den fördefinierade fakturamodellen för att analysera och returnera all text och alla tabeller på fakturan. Se [konceptuella](./concept-invoices.md) fakturor för mer information.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="exempelfaktura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Förbyggd kvittomodell

Den fördefinierade kvittomodellen används för att läsa engelska försäljningskvitton från Australien, Kanada, Storbritannien, Indien och USA den typ som används av restauranger, bensinstationer, detaljhandel och &mdash; så vidare. Den här modellen extraherar viktig information, till exempel tid och datum för transaktionen, säljinformation, skattebelopp, radobjekt, summor med mera. Dessutom tränas den fördefinierade kvittomodellen för att analysera och returnera all text på ett kvitto. Mer information [finns i](./concept-receipts.md) konceptuella kvittoguider.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="exempelkvitto" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Modell med fördefinierade ID-kort

Med kortmodellen för identifiering (ID) kan du extrahera viktig information från världsomfattande pass och amerikanska drivrutinslicenser. Den extraherar data som dokument-ID, förfallodatum, förfallodatum, namn, land, region, datorläsbar zon med mera. Mer information [finns i begreppsguide för](./concept-identification-cards.md) id-kort.

:::image type="content" source="./media/overview-id.jpg" alt-text="exempel på id-kort" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Fördefinierade visitkortsmodeller

Med visitkortsmodellen kan du extrahera information som personens namn, befattning, adress, e-postadress, företag och telefonnummer från visitkort på engelska. Mer information [finns i](./concept-business-cards.md) konceptuella guide för visitkort.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="exempel på visitkort" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Indatakrav

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-containrar

[Använd Formigenkänning (förhandsversion) för](form-recognizer-container-howto.md) att distribuera API-funktioner lokalt. Med den här Docker-containern kan du föra tjänsten närmare dina data av efterlevnads-, säkerhets- eller andra driftskäl.

## <a name="service-availability-and-redundancy"></a>Tjänsttillgänglighet och redundans

### <a name="is-form-recognizer-service-zone-resilient"></a>Är Formigenkänning elastisk tjänstzon?

Ja. Tjänsten Formigenkänning är zontålig som standard.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Hur gör jag för att konfigurera Formigenkänning-tjänsten så att den är zontålig?

Ingen kundkonfiguration krävs för att aktivera zonåter återhämtning. Zonåter återhämtning för Formigenkänning resurser är tillgänglig som standard och hanteras av själva tjänsten.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla kognitiva tjänster bör utvecklare som använder Formigenkänning-tjänsten vara medvetna om Microsofts principer för kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Prova vårt onlineverktyg och snabbstart om du vill veta mer om Formigenkänning tjänsten.

* [**Formigenkänning-verktyg**](https://fott-preview.azurewebsites.net/)
* [**Snabbstart för klientbibliotek REST API klientbibliotek**](quickstarts/client-library.md)
