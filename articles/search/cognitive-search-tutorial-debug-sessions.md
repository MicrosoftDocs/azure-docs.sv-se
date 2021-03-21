---
title: 'Självstudie: Använd debug-sessioner för att åtgärda färdighetsuppsättningar'
titleSuffix: Azure Cognitive Search
description: Debug-sessioner (för hands version) är ett Azure Portal verktyg som används för att hitta, diagnostisera och reparera problem i en färdigheter.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99509157"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Självstudie: Felsöka en färdigheter med hjälp av debug-sessioner

Färdighetsuppsättningar koordinerar en serie åtgärder som analyserar eller transformerar innehåll, där utdata från en färdighet blir indata för en annan. När indata är beroende av utdata kan misstag i färdigheter-definitioner och fält associationer resultera i uteblivna åtgärder och data.

**Felsök sessioner** i Azure Portal ger en holistisk visualisering av en färdigheter. Med det här verktyget kan du öka detalj nivån till de olika stegen för att enkelt se var en åtgärd kan falla ned.

I den här artikeln använder du **fel söknings-sessioner** för att hitta och korrigera 1) utdata från utdata som saknas och 2). Själv studie kursen är allt. Den innehåller data som du kan använda för att indexera (kliniska test data), en Postman-samling som skapar objekt och instruktioner för att använda **Debug-sessioner** för att hitta och åtgärda problem i färdigheter.

> [!Important]
> Debug-sessioner är en förhands gransknings funktion utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande krav på plats:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ Ett Azure Storage konto med [Blob Storage](../storage/blobs/index.yml)som används som värd för exempel data och för att bevara temporära data som skapas under en felsökningssession.

+ [Postman Desktop-appen](https://www.getpostman.com/) och en [Postman-samling](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) för att skapa objekt med hjälp av REST-API: er.

+ [Exempel data (kliniska prövningar)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> I den här snabb starten används även [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. Eftersom arbets belastningen är så liten, överCognitive Servicess i bakgrunden för kostnads fri bearbetning för upp till 20 transaktioner. Det innebär att du kan slutföra den här övningen utan att behöva skapa ytterligare Cognitive Services-resurser.

## <a name="set-up-your-data"></a>Konfigurera dina data

Det här avsnittet skapar exempel data uppsättningen i Azure Blob Storage så att indexeraren och färdigheter har innehåll att arbeta med.

1. [Hämta exempel data (kliniska-tests-PDF-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), bestående av 19 filer.

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Välj samma region som Azure Kognitiv sökning för att undvika avgifter för bandbredd.

   + Välj konto typen StorageV2 (General Purpose v2).

1. Gå till Azure Storage Services-sidorna i portalen och skapa en BLOB-behållare. Bästa praxis är att ange åtkomst nivån "privat". Namnge din behållare `clinicaltrialdataset` .

1. I behållare klickar du på **överför** för att ladda upp exempelfilerna som du laddade ned och zippa upp i det första steget.

1. I portalen hämtar och sparar du anslutnings strängen för Azure Storage. Du behöver den för REST API anrop som index data. Du kan hämta anslutnings strängen från **Inställningar**  >  **åtkomst nycklar** i portalen.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Hämta en HTTP-slutpunkt och åtkomst nyckel" border="false":::

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="create-data-source-skillset-index-and-indexer"></a>Skapa data källa, färdigheter, index och indexerare

I det här avsnittet används Postman och en angiven samling för att skapa Kognitiv sökning data källa, färdigheter, index och indexerare. Om du inte är bekant med Postman, se [den här snabb](search-get-started-rest.md)starten.

Du kommer att behöva [Postman-samlingen](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) som skapats för den här självstudien för att slutföra den här uppgiften. 

1. Starta Postman och importera samlingen. Under **filer**  >  **nya** väljer du den samling som ska importeras.
1. När samlingen har importer ATS expanderar du åtgärds listan (...).
1. Klicka på **Redigera**.
1. Under aktuellt värde anger du namnet på din `searchService` (till exempel om slut punkten är `https://mydemo.search.windows.net` , så är tjänstens namn " `mydemo` ").
1. Ange `apiKey` antingen den primära eller sekundära nyckeln för Sök tjänsten.
1. Ange på `storageConnectionString` sidan nycklar i ditt Azure Storage-konto.
1. Ange `containerName` för den behållare som du skapade i lagrings kontot och klicka sedan på **Uppdatera**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="redigera variabler i Postman":::

Samlingen innehåller fyra olika REST-anrop som används för att skapa objekt som används i den här självstudien.

Det första anropet skapar data källan. `clinical-trials-ds`. Det andra anropet skapar färdigheter `clinical-trials-ss` . Det tredje anropet skapar indexet `clinical-trials` . Det fjärde och sista anropet skapar indexeraren `clinical-trials-idxr` .

+ Öppna varje begäran i tur och klicka på **Skicka** för att skicka varje begäran till Sök tjänsten. 

När alla anrop i samlingen har slutförts stänger du Postman och återgår till Azure Portal.

## <a name="check-results-in-the-portal"></a>Kontrol lera resultaten i portalen

Exempel koden skapar avsiktligt ett fel söknings index som en följd av problem som uppstått under färdigheter-körningen. Data saknas i problemet. 

1. I Azure Portal går du till sidan Sök tjänst översikt och väljer fliken **index** . 
1. Välj `clinical-trials` indexet.
1. Ange den här frågesträngen: om `$select=metadata_storage_path, organizations, locations&$count=true` du vill returnera fält för specifika dokument (identifieras av det unika `metadata_storage_path` fältet).
1. Klicka på **Sök** för att köra frågan och returnera alla 19 dokument, med tomma värden för "organisationer" och "platser".

De här fälten bör ha fyllts i genom färdigheter för [enhets igenkänning](cognitive-search-skill-entity-recognition.md), som används för att hitta organisationer och platser var som helst inom blobens innehåll. I nästa övning använder du debug-sessionen för att fastställa vad som gått fel.

Ett annat sätt att undersöka fel och varningar är via Azure Portal.

1. Öppna fliken **indexerare** och välj `clinical-trials-idxr` .
1. Observera att när indexerings jobbet lyckades, var 57 varningar.
1. Klicka på **Slutför** om du vill visa varningarna (om det skulle uppstå fel i informations länken skulle länken **Miss** lyckas). Du ser en lång lista över alla varningar som har utsänts av indexeraren.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="Visa varningar":::

## <a name="start-your-debug-session"></a>Starta felsökningssessionen

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Starta en ny felsökningssession":::

1. På sidan Sök Översikt klickar du på fliken **Felsök sessioner** .
1. Välj **+ ny felsökningssession**.
1. Ge sessionen ett namn. 
1. Anslut sessionen till ditt lagrings konto. 
1. Ange namnet på indexeraren i Indexer-mallen. Indexeraren har referenser till data källan, färdigheter och indexet.
1. Godkänn standard dokument valet för det första dokumentet i samlingen. 
1. **Spara** sessionen. Om du sparar sessionen inaktive ras pipelinen för AI-anrikning som definieras av färdigheter.

> [!Important]
> En Felsök-session fungerar bara med ett enda dokument. Du kan välja vilket dokument som ska felsöka eller bara använda det första.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

När felsökningssessionen har initierats, använder sessionen standardvärdet för AI- **förrikaren** och markerar **kunskaps diagrammet**. Kunskaps diagrammet innehåller en visuell hierarki av färdigheter och dess ordning för körningen sekventiellt och parallellt.

## <a name="find-issues-with-the-skillset"></a>Hitta problem med färdigheter

Eventuella problem som har rapporter ATS av indexeraren finns på fliken intilliggande **fel eller varningar** . 

Observera att fliken **fel/varningar** ger en mycket mindre lista än den som visas tidigare, eftersom den här listan endast innehåller fel för ett enskilt dokument. Precis som i listan som visas av indexeraren kan du klicka på ett varnings meddelande och se information om den här varningen.

Välj **fel/varningar** om du vill granska meddelandena. Du bör se tre:

   + "Det gick inte att mappa utmatnings fältets platser" till Sök index. Kontrol lera egenskapen ' outputFieldMappings ' för indexeraren.
Värdet "/Document/merged_content/locations" saknas.

   + "Det gick inte att mappa utmatnings fältet" organisationer "till sökindex. Kontrol lera egenskapen ' outputFieldMappings ' för indexeraren.
Värdet "/Document/merged_content/ORGANIZATIONS" saknas.

   + "Kunskapen kördes men kan ha oväntade resultat eftersom en eller flera färdighets indata var ogiltiga.
Valfria inmatade kunskaper saknas. Namn: ' languageCode ', källa: '/document/languageCode '. Tolknings problem med uttrycks språk: värdet/document/languageCode saknas.

   Många kunskaper har en "languageCode"-parameter. Genom att inspektera åtgärden kan du se att den här språk kods inmatningen saknas i `Enrichment.NerSkillV2.#1` , vilket är samma kunskap om enhets igenkänning som har problem med "platser" och "organisationers" utdata. 

Eftersom alla tre meddelanden är om den här kunskapen är nästa steg att felsöka den här kunskapen. Börja med att lösa inledande problem innan du går vidare till outputFieldMapping-problem.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="En ny felsökningssession har startats":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Korrigera inmatat värde för kompetens saknas

På fliken **fel/varningar** finns det ett fel för en åtgärd med etiketten `Enrichment.NerSkillV2.#1` . Informationen i det här felet förklarar att det är problem med värdet "/document/languageCode" för kompetensen. 

1. Gå tillbaka till fliken **AI-anrikninger** .
1. Klicka på **kunskaps diagrammet**.
1. Klicka på kunskaps etiketten **#1** för att visa information i den högra rutan.
1. Leta upp InInformationen för "languageCode".
1. Välj **</>** symbolen i början av raden och öppna uttrycks utvärderaren.
1. Klicka på knappen **utvärdera** för att bekräfta att uttrycket resulterar i ett fel. Det bekräftar att egenskapen "languageCode" inte är en giltig Indatatyp.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Uttrycks utvärderare":::

Det finns två sätt att undersöka det här felet i sessionen. Det första är att titta på var indatamängden kommer från – vilken kunskap i hierarkin som ska producera det här resultatet? På fliken körningar i fönstret kunskaps information visas källan för indata. Om det inte finns någon källa, indikerar detta ett fel vid fält mappning.

1. Klicka på fliken **körningar** .
1. Titta på indata och hitta "languageCode". Det finns ingen källa för de här inmatade objekten. 
1. Växla till den vänstra rutan för att visa den omfattande data strukturen. Det finns ingen mappad sökväg som motsvarar "languageCode".

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Omfattande data struktur":::

Det finns en mappad sökväg för "språk". Därför finns det ett stavfel i färdighets inställningarna. För att åtgärda det här uttrycket i #1-kunskapen med uttrycket '/Document/Language ' måste uppdateras.

1. Öppna uttrycks utvärderaren **</>** för sökvägen "språk".
1. Kopiera uttrycket. Stäng fönstret.
1. Gå till kunskaps inställningarna för #1s kompetensen och öppna uttrycks utvärderaren **</>** för inmatade "languageCode".
1. Klistra in det nya värdet "/Document/Language" i resultat rutan och klicka på **utvärdera**.
1. Den ska visa rätt Indatatyp "sv". Klicka på Använd för att uppdatera uttrycket.
1. Klicka på **Spara** i rutan till höger, kunskaps information.
1. Klicka på **Kör** på sessionens fönster-meny. Detta startar en annan körning av färdigheter med hjälp av dokumentet. 

När debug-sessionen har körts klart klickar du på fliken fel/varningar så visas att det fel som har etiketten "berikning. NerSkillV2. #1" är borta. Det finns dock fortfarande två varningar om att tjänsten inte kunde mappa utmatnings fält för organisationer och platser till Sök indexet. Det saknas värden: "/Document/merged_content/ORGANIZATIONS" och "/Document/merged_content/locations".

## <a name="fix-missing-skill-output-values"></a>Åtgärda saknade värden för kunskaps utdata

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Fel och varningar":::

Det saknas värden för utdata från en färdighet. Om du vill identifiera kunskapen med felet går du till den fördefinierade data strukturen och letar reda på värde namnet och tittar på den ursprungliga källan. När det gäller de saknade organisationer och plats värden, är de utdata från färdighets #1. Om du öppnar uttrycks utvärderaren </> för varje sökväg visas uttrycken som anges som "/Document/Content/organizations" respektive "/Document/Content/locations".

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Organisations enhet för uttrycks utvärderare":::

Utdata för dessa entiteter är tom och får inte vara tom. Vad är indata som producerar det här resultatet?

1. Gå till **färdighets diagram** och välj kunskaps #1.
1. Välj fliken **körningar** i den högra kunskaps informations rutan.
1. Öppna uttrycks utvärderaren **</>** för INmatad text.

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Inmatade för text kunskaper":::

Det visade resultatet för den här indatamängden ser inte ut som ett text flöde. Det ser ut som en bild som är omgiven av nya rader. Bristen på text innebär att inga entiteter kan identifieras. När du tittar på hierarkin för färdigheter visas innehållet först när du bearbetas av #6 (OCR)-kompetensen och sedan skickas till #5 (slå samman) kunskap. 

1. Välj #5 (slå samman) kompetensen i **färdighets diagrammet**.
1. Välj fliken **körningar** i den högra kunskaps informations rutan och öppna uttrycks utvärderaren **</>** för utmatningarna "mergedText".

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Utdata för kopplings kunskaper":::

Här är texten länkad till bilden. Det går inte att titta på uttrycket "/Document/merged_content" i sökvägen "organisationer" och "platser" för #1-kunskapen. I stället för att använda '/Document/Content ' bör det använda/Document/merged_content för text inmatningar.

1. Kopiera uttrycket för "mergedText"-utdata och Stäng fönstret uttrycks utvärderare.
1. Välj färdighets #1 i **färdighets diagrammet**.
1. Välj fliken **kunskaps inställningar** i rutan höger kunskaps information.
1. Öppna uttrycks utvärderaren **</>** för texten "text".
1. Klistra in det nya uttrycket i rutan. Klicka på **utvärdera**.
1. Rätt inmatare med den tillagda texten ska visas. Klicka på **Använd** för att uppdatera kunskaps inställningarna.
1. Klicka på **Spara** i rutan till höger, kunskaps information.
1. Klicka på **Kör** på menyn sessions-fönster. Detta startar en annan körning av färdigheter med hjälp av dokumentet.

När indexeraren har körts är felen fortfarande där. Gå tillbaka till färdighets #1 och undersök. Indatamängden till färdigheten korrigerades till merged_content från Content. Vilka är utmatningarna för dessa entiteter i kompetensen?

1. Välj fliken **AI-anrikninger** .
1. Välj **färdighets diagram** och klicka på kompetens #1.
1. Navigera bland **kunskaps inställningarna** för att hitta "utdata".
1. Öppna uttrycks utvärderaren **</>** för entiteten organisationer.

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Utdata för organisationer-entitet":::

Utvärdering av resultatet av uttrycket ger rätt resultat. Kunskapen fungerar för att identifiera rätt värde för entiteten, "organisationer". Men utdata-mappningen i entitetens sökväg ger fortfarande ett fel. I jämförelsen av sökvägen för utdata i kunskapen till utdatafilen i fel meddelandet är det den kunskap som översätter utmatningarna, organisationer och platser under/Document/Content-noden. Även om mappningen av utdatakolumner förväntar sig att resultaten ska överordnas under noden/Document/merged_content. I föregående steg ändrades indatatypen från '/Document/Content ' till '/Document/merged_content '. Du måste ändra sammanhanget i kunskaps inställningarna för att se till att utdata genereras med rätt kontext.

1. Välj fliken **AI-anrikninger** .
1. Välj **färdighets diagram** och klicka på kompetens #1.
1. Navigera bland **kunskaps inställningarna** för att hitta "context".
1. Dubbelklicka på inställningen för "context" och redigera den för att läsa "/Document/merged_content".
1. Klicka på **Spara** i rutan till höger, kunskaps information.
1. Klicka på **Kör** på menyn sessions-fönster. Detta startar en annan körning av färdigheter med hjälp av dokumentet.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Kontext korrigering i kompetens inställning":::

Alla fel har åtgärd ATS.

## <a name="commit-changes-to-the-skillset"></a>Genomför ändringar i färdigheter

När felsökningssessionen initierades skapade Sök tjänsten en kopia av färdigheter. Detta gjordes för att skydda den ursprungliga färdigheter på din Sök tjänst. Nu när du har slutfört fel sökningen av färdigheter kan du utföra korrigeringarna (Skriv över den ursprungliga färdigheter). 

Alternativt, om du inte är redo att genomföra ändringar, kan du spara felsökningssessionen och öppna den igen senare.

1. Klicka på **genomför ändringar** i huvud menyn för fel söknings sessioner.
1. Klicka på **OK** för att bekräfta att du vill uppdatera din färdigheter.
1. Stäng felsökningssessionen och välj fliken **indexerare** .
1. Öppna din "kliniska-tests-idxr".
1. Klicka på **Återställ**.
1. Klicka på **Kör**. Bekräfta genom att klicka på **OK** .

När indexeraren har slutförts bör det vara en grön bock och ordet lyckades bredvid tidsstämpeln för den senaste körningen på fliken **körnings historik** . Så här kontrollerar du att ändringarna har tillämpats:

1. På sidan Sök översikt väljer du fliken **index** .
1. Öppna indexet "kliniska-tests" och på fliken Sök Utforskaren anger du den här frågesträngen: `$select=metadata_storage_path, organizations, locations&$count=true` för att returnera fält för specifika dokument (identifieras av det unika `metadata_storage_path` fältet).
1. Klicka på **Sök**.

Resultaten bör visa att organisationer och platser nu fylls med förväntade värden.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Den här självstudien rör olika aspekter av färdigheter-definition och bearbetning. Mer information om begrepp och arbets flöden finns i följande artiklar:

+ [Så här mappar du färdigheter-utmatnings fält till fält i ett Sök index](cognitive-search-output-field-mapping.md)

+ [Färdighetsuppsättningar i Azure Kognitiv sökning](cognitive-search-working-with-skillsets.md)

+ [Konfigurera cachelagring för stegvis berikning](cognitive-search-incremental-indexing-conceptual.md)