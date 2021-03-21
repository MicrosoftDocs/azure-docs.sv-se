---
title: Konfigurera din Anpassad Bing-sökning upplevelse | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Med portalen kan du skapa en Sök instans som anger webb sektorerna. domäner, under sidor och webb sidor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: b2d4eb75002474bbec57753bbf0255cc4c2b4a16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96338647"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurera din Anpassad Bing-sökning upplevelse

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med en anpassad Sök instans kan du skräddarsy Sök upplevelsen så att den bara innehåller innehåll från webbplatser som användarna bryr sig om. I stället för att utföra en sökning på webben söker Bing bara de sektorer på webben som intresserar dig. Du skapar en anpassad vy av webben med hjälp av [portalen](https://www.customsearch.ai) för anpassad sökning i Bing.

Med portalen kan du skapa en Sök instans som anger webb sektorerna: domäner, under sidor och webb sidor, som du vill att Bing ska söka efter och de som du inte vill att de ska genomsöka. Portalen kan också föreslå innehåll som du kanske vill inkludera.

Använd följande när du definierar dina segment på webben:

| Segment namn | Beskrivning                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | En domän sektor inkluderar allt innehåll som finns i en Internet domän. Till exempel `www.microsoft.com`. Om `www.` du utelämnar Bing genomsöks även domänens under domäner. Om du till exempel anger `microsoft.com` , returnerar Bing även resultat från `support.microsoft.com` eller `technet.microsoft.com` . |
| Undersida    | En subpage-sektor innehåller allt innehåll som finns på undersidan och Sök vägarna under det. Du kan ange högst två under sidor i sökvägen. Till exempel `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Historik    | En webb sidas sektor kan bara innehålla den webb sidan i en anpassad sökning. Du kan också ange om du vill inkludera under sidor.                                                                                                                                                                                  |

> [!IMPORTANT]
> Alla domäner, under sidor och webb sidor som du anger måste vara offentliga och indexerade av Bing. Om du äger en offentlig webbplats som du vill inkludera i sökningen, och Bing inte har indexerat den, kan du läsa mer i Bing webb läsar [dokumentationen](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) om hur du hämtar Bing för att indexera det. I webb läsar dokumentationen finns också information om hur du hämtar Bing för att uppdatera din crawlade webbplats om indexet är inaktuellt.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Lägga till webb segment i din anpassade Sök instans

När du skapar en anpassad Sök instans kan du ange webb sektorerna: domäner, under sidor och webb sidor som du vill ha med eller blockera från Sök resultaten. 

Om du känner till de segment som du vill ska ingå i din anpassade Sök instans, lägger du till dem i din instanss **aktiva** lista. 

Om du inte är säker på vilka sektorer du ska inkludera kan du skicka Sök frågor till Bing i **förhands gransknings** fönstret och välja de segment som du vill använda. Gör så här: 

1. Välj "Bing" i list rutan i förhands gransknings fönstret och ange en Sök fråga

2. Klicka på **Lägg till webbplats** bredvid det resultat som du vill inkludera. Klicka sedan på OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Anpassa din Sök upplevelse med aktiva och blockerade listor 

Du kan komma åt listan över aktiva och blockerade segment genom att klicka på flikarna **aktiva** och **blockerade** i din anpassade Sök instans. Segment som läggs till den aktiva listan tas med i din anpassade sökning. Blockerade segment genomsöks inte och visas inte i Sök resultaten.

Klicka på fliken **aktiv** och Lägg till en eller flera URL: er för att ange de sektorer på webben som du vill att Bing ska söka i. Om du vill redigera eller ta bort URL: er använder du alternativen i kolumnen **kontroller** . 

När du lägger till URL: er i den **aktiva** listan kan du lägga till enskilda URL: er eller flera URL: er samtidigt genom att ladda upp en textfil med hjälp av ikonen Ladda upp.

![Fliken Anpassad Bing-sökning aktiv](media/file-upload-icon.png)

Om du vill överföra en fil skapar du en textfil och anger en enda domän, undersida eller webb sida per rad. Filen kommer att avvisas om den inte är korrekt formaterad.

> [!NOTE]
> * Du kan bara ladda upp en fil till den **aktiva** listan. Du kan inte använda den för att lägga till segment **i blockeringslistan.**  
> * Om den **blockerade** listan innehåller en domän, under sida eller webb sida som du har angett i uppladdnings filen, tas den bort **från blockeringslistan och** läggs till i den **aktiva** listan.
> * Dubbla poster i uppladdnings filen ignoreras av Anpassad Bing-sökning. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Hämta webbplats förslag för din Sök upplevelse

När du har lagt till WebSlices i den **aktiva** listan genererar anpassad Bing-sökning-portalen webbplats-och undersidors förslag längst ned på fliken. Detta är segment som Anpassad Bing-sökning tror att du kanske vill inkludera. Klicka på **Uppdatera** för att få uppdaterade förslag efter att du har uppdaterat inställningarna för den anpassade Sök instansen. Det här avsnittet visas bara om förslag är tillgängligt.

## <a name="search-for-images-and-videos"></a>Sök efter bilder och videor

Du kan söka efter bilder och videor på samma sätt som webb innehåll med hjälp av [anpassade bildsökning-API: t för Bing](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) eller [Bing anpassad videosökning API](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Du kan visa dessa resultat med det [värdbaserade användar gränssnittet](hosted-ui.md)eller API: erna. 

Dessa API: er liknar de icke-anpassade [bildsökning i Bing](../Bing-Image-Search/overview.md) -och [videosökning i Bing](../bing-video-search/overview.md) -API: erna, men du kan inte använda Frågeparametern för att söka igenom hela webbplatsen `customConfig` . Se dessa dokumentations uppsättningar för mer information om hur du arbetar med bilder och videor. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testa din Sök instans med förhands gransknings fönstret

Du kan testa din Sök instans genom att använda förhands gransknings fönstret på portalens högra sida för att skicka Sök frågor och visa resultatet. 

1. Under sökrutan väljer du **min instans**. Du kan jämföra resultatet från din Sök upplevelse till Bing genom att välja **Bing**. 
2. Välj ett säkert Sök filter och vilken marknad du vill söka efter (se [frågeparametrar](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Ange en fråga och tryck på RETUR eller klicka på Sök ikonen för att visa resultatet från den aktuella konfigurationen. Du kan ändra Sök typen som du utför genom att klicka på **webb**, **bild** eller **video** för att få motsvarande resultat. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Justera rangordningen för vissa Sök Resultat

På portalen kan du justera Sök rangordningen för innehåll från vissa domäner, under sidor och på webb sidor. När du har skickat en Sök fråga i förhands gransknings fönstret innehåller varje Sök resultat en lista över justeringar som du kan göra för den:  

| Justering | Beskrivning |
|------------|-------------|
| Blockera      | Flyttar domänen, under sidan eller webb sidan till den blockerade listan. Bing kommer att undanta innehåll från den valda webbplatsen från att visas i Sök resultaten.                    |
| Stärka      | Ökar innehållet från domänen eller under sidan som ska vara högre i Sök resultaten.                                                                                        |
| Degradering     | Sänker innehållet från domänen eller under sidan i Sök resultaten. Du väljer om du vill nedgradera innehåll från domänen eller under sidan som webb sidan tillhör. |
| Fäst överst | Flyttar domänen, under sidan eller webb sidan till den **fästa** listan. Detta gör att webb sidan visas som det bästa Sök resultatet för en specifik Sök fråga.                   |

Justering av rang är inte tillgängligt för bild-eller Videos ökningar.

### <a name="boosting-and-demoting-search-results"></a>Förbättra och nedgradera Sök Resultat

Du kan höja förstärkningen, förstärka eller nedgradera alla domäner och under sidor i den **aktiva** listan. Som standard läggs alla segment till utan ranknings justering. Segment av den webbplats som är super Boosted eller Boosted rangordnas högre upp i Sök resultaten (med högre rangordning än Boost). Objekt som har degraderas rangordnas lägre i Sök resultaten.

Du kan höja, förstärka eller nedgradera objekt med hjälp av **rangordnings justerings** kontrollerna i den **aktiva** listan eller genom att använda kontrollerna Boost och degradera i förhands gransknings fönstret. Tjänsten lägger till sektorn i din aktiva lista och justerar rangordningen enligt detta.

> [!NOTE] 
> Att öka och nedgradera domäner och under sidor är en av många metoder som Anpassad Bing-sökning används för att fastställa ordningen på Sök resultaten. På grund av andra faktorer som påverkar rankningen av olika webb innehåll kan effekterna av att justera rang variera. Använd förhands gransknings fönstret för att testa effekterna av att justera rangordningen för dina Sök resultat. 

Super Boost, Boost och nedgradering är inte tillgängliga för bild-och videos ökningar.

## <a name="pin-slices-to-the-top-of-search-results"></a>Fästa segment högst upp i Sök resultaten

På portalen kan du också fästa URL: er överst i Sök resultaten för vissa Sök termer med hjälp av den **fästa** fliken. Ange en URL och fråga för att ange den webb sida som ska visas som det översta resultatet. Observera att du kan fästa maximalt en webb sida per Sök fråga och bara indexerade webb sidor kommer att visas i sökningar. Fästa resultat är inte tillgängligt för bild-eller Videos ökningar.

Du kan fästa en webb sida överst på två sätt:

* På fliken **Fäst** anger du webb adressen till webb sidan som ska fästas överst och dess motsvarande fråga.

* I **förhands gransknings** fönstret anger du en Sök fråga och klickar på Sök. Hitta den webb sida som du vill fästa för din fråga och klicka på **Fäst överst**. webb sidan och frågan kommer att läggas till i den **fästa** listan.

### <a name="specify-the-pins-match-condition"></a>Ange PIN-kodens matchnings villkor

Som standard fästs webb sidor bara högst upp i Sök resultaten när en användares frågesträng exakt matchar en lista i den **fästa** listan. Du kan ändra det här beteendet genom att ange något av följande matchnings villkor:

> [!NOTE]
> Alla jämförelser mellan användarens Sök fråga och PIN-kodens Sök fråga är Skift läges okänslig.

| Värde | Beskrivning                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Börjar med | PIN-koden är en matchning om användarens frågesträng börjar med PIN-kodens frågesträng |
| Slutar med   | PIN-koden är en matchning om användarens frågesträng slutar med PIN-kodens frågesträng.  |
| Innehåller    | PIN-koden är en matchning om användarens frågesträng innehåller PIN-frågesträngen.   |


Om du vill ändra PIN-kodens matchnings villkor klickar du på PIN-kodens redigerings ikon. I kolumnen **fråga matchnings villkor** klickar du på list rutan och väljer det nya villkor som du vill använda. Klicka sedan på ikonen Spara för att spara ändringen.

### <a name="change-the-order-of-your-pinned-sites"></a>Ändra ordningen på dina fästa platser

Om du vill ändra ordningen på dina PIN-kod kan du dra och släppa dem eller redigera deras beställnings nummer genom att klicka på ikonen "redigera" i kolumnen **kontroller** i den **fästa** listan.

Om flera PIN-krav uppfyller ett matchnings villkor kommer Anpassad Bing-sökning att använda den högst i listan.

## <a name="view-statistics"></a>Visa statistik

Om du prenumererar på Anpassad sökning på lämplig nivå (se sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)) läggs fliken **statistik** till i produktions instanserna. Fliken statistik visar information om hur dina anpassade slut punkter används, inklusive anrops volym, topp frågor, geografisk distribution, svars koder och säker sökning. Du kan filtrera informationen med hjälp av de angivna kontrollerna.

## <a name="usage-guidelines"></a>Användnings rikt linjer

- För varje anpassad Sök instans är det maximala antalet ranknings justeringar som du kan göra i **aktiva** och **blockerade** segment begränsade till 400.
- Att lägga till en sektor i aktiva eller blockerade flikar räknas som en ranknings justering.
- Ökning och degraderingen räknas som två justerings justeringar.
- För varje anpassad Sök instans är det högsta antalet stift som du kan göra vara begränsat till 200.

## <a name="next-steps"></a>Nästa steg

- [Anropa din anpassade sökning](./search-your-custom-view.md)
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](../bing-web-search/hit-highlighting.md)
- [Webbsidor för sida](../bing-web-search/paging-search-results.md)