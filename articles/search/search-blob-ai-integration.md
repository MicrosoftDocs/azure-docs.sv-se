---
title: Använd AI för att utöka BLOB-innehåll
titleSuffix: Azure Cognitive Search
description: Lär dig mer om funktionerna för naturliga språk och bild analys i Azure Kognitiv sökning och hur dessa processer gäller för innehåll som lagras i Azure-blobar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222056"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Använd AI för att bearbeta och analysera BLOB-innehåll i Azure Kognitiv sökning

Innehåll i Azure Blob Storage som består av bilder eller lång avskilda text kan genomgå en djup inlärnings analys för att avslöja och extrahera värdefull information som är användbar för underordnade program. Genom att använda [AI-berikning](cognitive-search-concept-intro.md)kan du:

+ Extrahera text från bilder med hjälp av optisk tecken läsning (OCR)
+ Skapa en scen beskrivning eller taggar från ett foto
+ Identifiera språk och översätta text till olika språk
+ Härled strukturen genom enhets igenkänning genom att söka efter referenser till personer, datum, platser eller organisationer

Även om du kanske behöver bara en av dessa AI-funktioner är det vanligt att kombinera flera av dem till samma pipeline (till exempel Extrahera text från en skannad bild och sedan hitta alla datum och platser som refereras till). Det är också vanligt att inkludera anpassad AI-eller Machine Learning-bearbetning i form av ledande externa paket eller interna modeller som är anpassade efter dina data och dina behov.

Även om du kan använda AI-anrikning till alla data källor som stöds av en Sök indexerare, är blobbar de mest använda strukturerna i en pipeline. Resultaten hämtas till ett sökindex för full texts ökning eller dirigeras tillbaka till Azure Storage för att leda nya program upplevelser som innehåller utforska data för identifierings-eller analys scenarier. 

I den här artikeln visar vi AI-berikning genom en bred lins så att du snabbt kan grepp hela processen, från att omvandla rå data i blobbar, till information som kan sökas i antingen ett sökindex eller i ett kunskaps lager.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Vad det innebär att "utöka" BLOB-data med AI

*AI-anrikning* är en del av indexerings arkitekturen i Azure kognitiv sökning som integrerar maskin inlärnings modeller från Microsoft eller anpassade utbildnings modeller som du anger. Det hjälper dig att implementera slut punkt till slut punkt där du behöver bearbeta blobbar (både befintliga och nya som de kommer in eller uppdateras), öppna alla fil format för att extrahera bilder och text, extrahera den önskade informationen med hjälp av olika AI-funktioner och indexera dem i ett sökindex för snabb sökning, hämtning och utforskning. 

Indata är dina blobbar i en enda behållare i Azure Blob Storage. Blobbar kan vara nästan alla typer av text-eller bilddata. 

Utdata är alltid ett sökindex som används för snabb texts ökning, hämtning och utforskning i klient program. Dessutom kan utdata också vara ett [*kunskaps lager*](knowledge-store-concept-intro.md) som Project innehåller dokument till Azure-blobbar eller Azure-tabeller för underordnad analys i verktyg som Power BI eller i arbets belastningar för data vetenskap.

I between är själva pipeline-arkitekturen. Pipelinen baseras på [*indexerarna*](search-indexer-overview.md), som du kan använda för att tilldela en [*färdigheter*](cognitive-search-working-with-skillsets.md), som består av en eller flera *kunskaper* som tillhandahåller AI. Syftet med pipelinen är att skapa *omfattande dokument* som anger pipelinen som RAW-innehåll, men du kan hämta ytterligare struktur, kontext och information samtidigt som du flyttar genom pipelinen. Omfattande dokument förbrukas under indexeringen för att skapa inverterade index och andra strukturer som används i full texts ökning eller utforskning och analys.

## <a name="required-resources"></a>Nödvändiga resurser

Förutom Azure Blob Storage och Azure Kognitiv sökning behöver du en tredje tjänst eller mekanism som tillhandahåller AI:

+ För inbyggd AI Kognitiv sökning integreras med API: er för Azure Cognitive Services vision och naturliga språk bearbetning. Du kan [bifoga en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md) för att lägga till optisk tecken läsning (OCR), bild analys eller språk bearbetning (språk identifiering, text översättning, entitets igenkänning, nyckel fras extrahering). 

+ För anpassad AI med Azure-resurser kan du definiera en anpassad färdighet som radbryter den externa funktion eller modell som du vill använda. [Anpassade kunskaper](cognitive-search-custom-skill-interface.md) kan använda kod som tillhandahålls av Azure Functions, Azure Machine Learning, Azure formulär tolken eller en annan resurs som kan kontaktas via https.

+ För anpassade icke-Azure AI måste din modell eller modul vara tillgänglig för en indexerare via HTTP.

Om du inte har alla tjänster som är tillgängliga kan du starta direkt på din Portal sida för lagrings konto. På den vänstra navigerings sidan, under **BLOB service** klickar du på **Lägg till Azure-kognitiv sökning** för att skapa en ny tjänst eller välja en befintlig. 

När du har lagt till Azure-Kognitiv sökning till ditt lagrings konto kan du följa standard processen för att utöka data i alla Azure-Data källor. Vi rekommenderar guiden **Importera data** i Azure kognitiv sökning för en enkel första introduktion till AI-berikning. Du kan koppla en Cognitive Services-resurs under arbets flödet. Den här snabb starten vägleder dig genom stegen: [skapa en pipeline för AI-anrikning i portalen](cognitive-search-quickstart-blob.md). 

Följande avsnitt tar en närmare titt på komponenter och arbets flöden.

## <a name="use-a-blob-indexer"></a>Använda en BLOB-indexerare

AI-anrikning är ett tillägg till en indexerings pipeline, och i Azure Kognitiv sökning skapas dessa pipelines ovanpå en *indexerare*. En indexerare är en data källa medveten under tjänst som är utrustad med intern logik för att sampla data, läsa metadata, hämta data och serialisera data från interna format till JSON-dokument för efterföljande import. Indexerare används ofta av sig själva för import, separat från AI, men om du vill bygga en pipeline för AI-anrikning behöver du en indexerare och en färdigheter för att gå med den. I det här avsnittet beskrivs indexeraren. nästa avsnitt fokuserar på färdighetsuppsättningar.

Blobbar i Azure Storage indexeras med [BLOB-indexeraren](search-howto-indexing-azure-blob-storage.md). Du kan anropa denna indexerare med hjälp av guiden **Importera data** , en REST API eller ett SDK. En BLOB-indexerare anropas när data källan som används av indexeraren är en Azure Blob-behållare. Du kan indexera en delmängd av dina blobbar genom att skapa en virtuell katalog som du sedan kan skicka som en parameter eller genom att filtrera efter ett fil typs tillägg.

En indexerare gör "dokument sprickor" och öppnar en BLOB för att granska innehåll. När du har anslutit till data källan är det första steget i pipelinen. För BLOB-data är detta var PDF, Office-dokument, bild och andra innehålls typer identifieras. Dokument sprickor med text extrahering är ingen kostnad. Dokument sprickor med avbildnings extrahering debiteras enligt priser som du hittar på [prissättnings sidan](https://azure.microsoft.com/pricing/details/search/).

Även om alla dokument kommer att bli skadade, sker berikningen bara om du anger de kunskaper du behöver. Om din pipeline till exempel består av enbart bild analys, ignoreras texten i din behållare eller dina dokument.

BLOB-indexeraren innehåller konfigurations parametrar och stöder ändrings spårning om underliggande data innehåller tillräckligt med information. Du kan läsa mer i [så här konfigurerar du en BLOB-indexerare](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Lägg till AI-komponenter

AI-anrikning syftar på moduler som söker efter mönster eller egenskaper och utför sedan en åtgärd enligt detta. Ansikts igenkänning i foton, text beskrivningar av foton, identifiering av nyckel fraser i ett dokument och OCR (eller igenkänning av skriven text i binära filer) är exempel på exempel.

I Azure Kognitiv sökning är *färdigheter* de enskilda komponenterna i AI-bearbetning som du kan använda fristående eller i kombination med andra kunskaper. 

+ Inbyggda kunskaper backas upp av Cognitive Services, med bild analys baserat på Visuellt innehåll och naturlig språk bearbetning baserat på Textanalys. En fullständig lista finns i [inbyggda kunskaper för innehålls berikning](cognitive-search-predefined-skills.md).

+ Anpassade kunskaper är anpassad kod, omsluten i en [gränssnitts definition](cognitive-search-custom-skill-interface.md) som möjliggör integrering i pipelinen. I kund lösningar är det vanligt att använda båda, med anpassade kunskaper som tillhandahåller AI-moduler med öppen källkod, tredje part eller första part.

En *färdigheter* är en samling kunskaper som används i en pipeline, och den anropas när dokumentets cracking-fas gör innehåll tillgängligt. En indexerare kan använda exakt en färdigheter, men det färdigheter finns oberoende av en indexerare så att du kan återanvända den i andra scenarier.

Anpassade kunskaper kan vara komplicerade, men kan vara enkla och enkla när det gäller implementering. Om du har befintliga paket som tillhandahåller mönster matchnings-eller klassificerings modeller, kan det innehåll som du extraherar från blobbar skickas till dessa modeller för bearbetning. Eftersom AI-berikning är Azure-baserad bör din modell också vara på Azure. Några vanliga värd metoder är att använda [Azure Functions](cognitive-search-create-custom-skill-example.md) eller [behållare](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Inbyggda kunskaper som backas upp av Cognitive Services kräver en [bifogad Cognitive Services](cognitive-search-attach-cognitive-services.md) allt-i-ett-prenumerations nyckel som ger dig åtkomst till resursen. En allt-i-ett-nyckel ger dig bild analys, språk identifiering, text översättning och text analys. Andra inbyggda kunskaper är funktioner i Azure Kognitiv sökning och kräver ingen ytterligare tjänst eller nyckel. Text formaren, delar och fusion är exempel på hjälp kunskaper som ibland är nödvändiga när du skapar pipelinen.

Om du bara använder anpassade kunskaper och inbyggda verktygs kunskaper, finns det inget beroende eller kostnader som är relaterade till Cognitive Services.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Använd AI – berikade utdata i underordnade lösningar

Utdata från AI-berikning är antingen ett sökindex på Azure Kognitiv sökning eller ett [kunskaps lager](knowledge-store-concept-intro.md) i Azure Storage.

I Azure Kognitiv sökning används ett sökindex för interaktiv utforskning med fri text och filtrerade frågor i en klient app. Omfattande dokument som skapats via AI formateras i JSON och indexeras på samma sätt som alla dokument indexeras i Azure Kognitiv sökning och utnyttjar alla fördelar som en indexerare tillhandahåller. Vid indexering, till exempel, refererar BLOB-indexeraren till konfigurations parametrar och inställningar för att använda alla fält mappningar eller logik för ändrings identifiering. Sådana inställningar är helt tillgängliga för regelbundna indexering och AI-berikade arbets belastningar. Efter indexeringen, när innehåll lagras på Azure Kognitiv sökning, kan du bygga omfattande frågor och filter uttryck för att förstå ditt innehåll.

I Azure Storage har ett kunskaps lager två godsspecifikationer: en BLOB-behållare eller tabeller i Table Storage. 

+ En BLOB-behållare fångar omfattande dokument i sin helhet, vilket är användbart om du vill mata in i andra processer. 

+ Dessutom kan Table Storage hantera fysiska projektioner av förrikade dokument. Du kan skapa segment eller lager av omfattande dokument som inkluderar eller undantar vissa delar. För analys i Power BI blir tabellerna i Azure Table Storage data källan för ytterligare visualisering och utforskning.

Ett berikat dokument i slutet av pipelinen skiljer sig från den ursprungliga indata-versionen genom förekomst av ytterligare fält som innehåller ny information som har extraherats eller genererats under berikning. Därför kan du arbeta med en kombination av ursprungligt och skapat innehåll, oavsett vilken utmatnings struktur du använder.

## <a name="next-steps"></a>Nästa steg

Det finns mycket mer du kan göra med AI-anrikning för att få ut mesta möjliga av dina data i Azure Storage, inklusive att kombinera Cognitive Services på olika sätt och redigera anpassade kunskaper för fall där det inte finns någon befintlig kognitiv tjänst för scenariot. Du kan lära dig mer genom att följa länkarna nedan.

+ [Ladda upp, ladda ned och lista blobar med Azure Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Konfigurera en BLOB-indexerare (Azure Kognitiv sökning)](search-howto-indexing-azure-blob-storage.md) 
+ [Översikt över AI-anrikning (Azure Kognitiv sökning)](cognitive-search-concept-intro.md) 
+ [Skapa en färdigheter (Azure Kognitiv sökning)](cognitive-search-defining-skillset.md)
+ [Mappa noder i ett antecknings träd (Azure Kognitiv sökning)](cognitive-search-output-field-mapping.md)