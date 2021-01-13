---
title: Skapa en färdigheter i Azure Portal
titleSuffix: Azure Cognitive Search
description: I den här snabb starten av portalen lär du dig hur du använder guiden Importera data för att lägga till kognitiva kunskaper i en indexerings pipeline i Azure Kognitiv sökning. I kunskaperna finns OCR (optisk tecken läsning) och bearbetning av naturligt språk.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 8151c9ce177d0cd54826603d1a395a8d5828b623
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133828"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Snabb start: skapa en färdigheter för Azure Kognitiv sökning kognitivt i Azure Portal

En färdigheter är en AI-baserad funktion som använder djup inlärnings modeller för att extrahera information och struktur från stora, ej differentierade text-eller bildfiler, vilket gör innehållet både indexerat och sökbart i Azure Kognitiv sökning. 

I den här snabb starten ska du kombinera tjänster och data i Azure-molnet för att skapa färdigheter. När allt är på plats kör du guiden **Importera data** i Azure Portal för att hämta allt tillsammans. Slut resultatet är ett sökbart index som är ifyllt med data som skapats av AI-bearbetning som du kan fråga i portalen ([Sök Utforskaren](search-explorer.md)).

## <a name="prerequisites"></a>Förutsättningar

Skapa följande tjänster innan du börjar:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ Ett Azure Storage konto med [Blob Storage](../storage/blobs/index.yml).

> [!NOTE]
> I den här snabb starten används även [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. Eftersom arbets belastningen är så liten, överCognitive Servicess i bakgrunden för kostnads fri bearbetning för upp till 20 transaktioner. Det innebär att du kan slutföra den här övningen utan att behöva skapa ytterligare Cognitive Services-resurser.

## <a name="set-up-your-data"></a>Konfigurera dina data

I följande steg konfigurerar du en BLOB-behållare i Azure Storage att lagra heterogena innehållsfiler.

1. [Ladda ned exempeldata](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) som består av en liten filuppsättning med olika typer av data. Zippa upp filerna.

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Välj samma region som Azure Kognitiv sökning för att undvika avgifter för bandbredd. 

   + Välj konto typen StorageV2 (General Purpose v2) om du vill testa kunskaps lagrings funktionen senare i en annan genom gång. Annars väljer du vilken typ som helst.

1. Öppna BLOB Services-sidorna och skapa en behållare. Du kan använda standard nivån för offentlig åtkomst. 

1. I behållare klickar du på **överför** för att ladda upp exempelfilerna som du laddade ned i det första steget. Observera att du har ett brett utbud av innehålls typer, inklusive bilder och programfiler som inte är full text sökbar i deras egna format.

   :::image type="content" source="media/cognitive-search-quickstart-blob/sample-data.png" alt-text="Källfiler i Azure Blob Storage" border="false":::

Nu kan du gå vidare till guiden Importera data.

## <a name="run-the-import-data-wizard"></a>Kör guiden Importera data

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

1. [Leta reda på Sök tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) och klicka på **Importera data** i kommando fältet för att ställa in kognitiv berikning i fyra steg på sidan Översikt.

   :::image type="content" source="media/cognitive-search-quickstart-blob/import-data-cmd2.png" alt-text="Kommandot Importera data" border="false":::

### <a name="step-1---create-a-data-source"></a>Steg 1 – Skapa en data Källa

1. I **Anslut till dina data** väljer du **Azure Blob Storage**, väljer det lagrings konto och den behållare som du skapade. Namnge datakällan och lämna standardvärdena för resten av inställningarna. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/blob-datasource.png" alt-text="Konfiguration av Azure-blob" border="false":::

    Fortsätt till nästa sida.

### <a name="step-2---add-cognitive-skills"></a>Steg 2 – Lägg till kognitiva kunskaper

Konfigurera sedan AI-anrikning för att anropa OCR, bild analys och naturlig språk bearbetning. 

1. I den här snabb starten använder vi den **kostnads fria** Cognitive Services resursen. Exempel data består av 14 filer, så den kostnads fria tilldelningen av 20 transaktioner på Cognitive Services räcker för den här snabb starten. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/cog-search-attach.png" alt-text="Koppla Cognitive Services Base service" border="false":::

1. Expandera **Lägg till anrikninger** och gör fyra val. 

   Aktivera OCR för att lägga till bild analys kunskaper på Guide sidan.

   Ange granularitet för sidor för att dela upp text i mindre segment. Flera text kunskaper är begränsade till 5 KB-indata.

   Välj enhets igenkänning (personer, organisationer, platser) och bild analys kunskaper.

   :::image type="content" source="media/cognitive-search-quickstart-blob/skillset.png" alt-text="Bifoga Cognitive Services Välj tjänster för färdigheter" border="false":::

   Fortsätt till nästa sida.

### <a name="step-3---configure-the-index"></a>Steg 3 – Konfigurera indexet

Ett index innehåller det sökbara innehållet och guiden **Importera data** kan vanligt vis skapa schemat åt dig genom att sampla data källan. I det här steget ska du granska det genererade schemat och eventuellt ändra inställningarna. Nedan visas standardschemat som skapas för demo-BLOB-datauppsättningen.

I den här snabbstarten passar guidens standardinställningar bra:  

+ Standard fält baseras på Egenskaper för befintliga blobbar och nya fält som ska innehålla anriknings utdata (till exempel,, `people` `organizations` `locations` ). Data typer härleds från metadata och data sampling.

+ Standard dokument nyckeln är *metadata_storage_path* (markerad eftersom fältet innehåller unika värden).

+ Standardattribut är **hämtnings** **bara och sökbara**. **Sökbart** tillåter fullständig texts ökning i ett fält. **Hämtnings** bar innebär att fält värden kan returneras i resultat. Guiden förutsätter att du vill att dessa fält ska vara hämtningsbara och sökbara, eftersom du har skapat dem via en kompetensuppsättning.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields.png" alt-text="Indexfält" border="false":::

Lägg märke till det genomstruken och frågetecknet på det **hämtnings** bara attributet med `content` fältet. I textbaserade blobbdokument innehåller fältet `content` den största delen av filen, som skulle kunna köras som tusentals rader. Ett fält som detta är svårhanterligt i Sök Resultat och du bör utesluta det för den här demon. 

Men om du behöver överföra fil innehåll till klient koden kontrollerar du att **hämtningen** fortfarande är markerad. Annars kan du ta bort det här attributet på `content` om de extraherade elementen (till exempel,, `people` `organizations` `locations` och så vidare) är tillräckliga.

Att ett fält markeras som **Hämtningsbart** innebär inte att fältet *måste* finnas i sökresultaten. Du kan detaljstyra sammansättningen av sökresultat med hjälp av frågeparametern **$select** om du vill ange vilka fält som ska inkluderas. I textintensiva fält som `content`, är parametern **$select** din lösning för att dina programanvändare ska få hanterbara sökresultat, samtidigt som du säkerställer att klientkoden har åtkomst till all information som behövs via attributet **Hämtningsbart**.
  
Fortsätt till nästa sida.

### <a name="step-4---configure-the-indexer"></a>Steg 4 – Konfigurera indexeraren

Indexeraren är en övergripande resurs som styr indexeringen. Indexeraren definierar datakällans namn, ett målindex och körningsfrekvensen. Guiden **Importera data** skapar flera objekt, och av dem är det alltid en indexerare som du kan köra upprepade gånger.

1. På sidan **indexerare** kan du acceptera standard namnet och klicka på schema alternativet **när** du vill köra det direkt. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-def.png" alt-text="Definition av indexerare" border="false":::

1. Klicka på **Skicka** för att skapa och köra indexeraren samtidigt.

## <a name="monitor-status"></a>Övervaka status

Indexering av kognitiva kunskaper tar längre tid än vanlig text baserad indexering, särskilt OCR och bild analys. Du övervakar förloppet genom att gå till sidan Översikt och klicka på **indexerare** mitt på sidan.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-notification.png" alt-text="Meddelande om Azure-Kognitiv sökning" border="false":::

Varningar är normala för många olika innehålls typer. Vissa innehålls typer är inte giltiga för vissa kunskaper och på lägre nivåer är det vanligt att du kan stöta på [indexerings gränser](search-limits-quotas-capacity.md#indexer-limits). Trunking Notifications för 32 000-tecken är till exempel en indexerare-gräns på den kostnads fria nivån. Om du körde den här demon på en högre nivå skulle många trunkar-varningar att gå bort.

Om du vill kontrol lera varningar eller fel klickar du på varnings status i listan indexerare för att öppna sidan körnings historik.

På sidan klickar du på varnings status igen för att visa en lista över varningar som liknar den som visas nedan. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-warnings.png" alt-text="Varnings lista för indexerare" border="false":::

Information visas när du klickar på en enskild status rad. Den här varningen anger att sammanfogningen stoppades efter att ha nått Max gränsen (den här specifika PDF-filen är stor).

  :::image type="content" source="media/cognitive-search-quickstart-blob/warning-detail.png" alt-text="Varnings information" border="false":::

## <a name="query-in-search-explorer"></a>Fråga i Sökutforskaren

När ett index har skapats kan du köra frågor för att returnera resultat. Använd **Sök Utforskaren** för den här aktiviteten i portalen. 

1. Klicka på **Sökutforskaren** i kommandofältet på söktjänstens instrumentpanelsida.

1. Välj **Ändra index** längst upp och välj det index som du skapade.

1. Ange en söksträng för att fråga indexet, till exempel `search=Microsoft&$select=people,organizations,locations,imageTags`.

Resultaten returneras som JSON, som kan vara utförliga och svåra att läsa, särskilt i stora dokument som kommer från Azure-blobbar. Några tips för att söka i det här verktyget är följande tekniker:

+ Lägg `$select` till för att ange vilka fält som ska tas med i resultaten. 
+ Använd CTRL-F för att söka i JSON efter vissa egenskaper eller villkor.

Frågesträngar är Skift läges känsliga, så om du får ett "okänt fält"-meddelande, kontrollerar du **fält** eller **index definition (JSON)** för att verifiera namn och Skift läge. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer.png" alt-text="Exempel med Sökutforskaren" border="false":::

## <a name="takeaways"></a>Lärdomar

Nu har du skapat din första färdigheter och lärt dig viktiga koncept som är användbara för prototyp av en omfattande Sök lösning med hjälp av dina egna data.

Bland de viktigaste lärdomarna som vi hoppas att du tar med dig är beroendet av Azure-datakällor. En färdigheter är kopplad till en indexerare och indexerarna är Azure och projektspecifika. I den här snabbstarten används Azure Blob Storage, men det går att använda andra Azure-datakällor. Mer information finns i [indexerare i Azure kognitiv sökning](search-indexer-overview.md). 

Ett annat viktigt begrepp är att kunskaper fungerar över innehålls typer och när du arbetar med heterogent innehåll hoppas vissa indata över. Dessutom kan stora filer eller fält överskrida indexerings gränserna för din tjänst nivå. Det är normalt att se varningar när dessa händelser inträffar. 

Utdata dirigeras till ett sökindex och det finns en mappning mellan namn-värdepar som skapas vid indexering och enskilda fält i indexet. Internt konfigurerar portalen [anteckningar](cognitive-search-concept-annotations-syntax.md) och definierar en [kunskapsuppsättning](cognitive-search-defining-skillset.md), som definierar ordningen på åtgärder och det allmänna flödet. Dessa steg är dolda på portalen, men när du börjar skriva kod blir dessa begrepp viktiga.

Slutligen har du lärt dig att kunna verifiera innehållet genom att fråga indexet. I slutet av den här artikeln är Azure Kognitiv sökning ett sökbart index, som du kan fråga med antingen den [enkla](/rest/api/searchservice/simple-query-syntax-in-azure-search) eller [helt utökade frågesyntaxen](/rest/api/searchservice/lucene-query-syntax-in-azure-search). Ett index som innehåller berikade fält är precis som andra fält. Om du vill lägga till standard-eller [Anpassade analyser](search-analyzers.md), kan du göra det genom att använda [bedömnings profiler](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonymer](search-synonyms.md), [fasettiska filter](search-filters-facets.md), geo-sökning eller någon annan Azure kognitiv sökning-funktion.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Du kan skapa färdighetsuppsättningar med hjälp av portalen, .NET SDK eller REST API. Om du vill veta mer om din kunskap kan du prova REST API med Postman och fler exempel data.

> [!div class="nextstepaction"]
> [Självstudie: Extrahera text och struktur från JSON-blobbar med hjälp av REST API: er ](cognitive-search-tutorial-blob.md)

> [!Tip]
> Ta bort indexeraren i portalen om du vill upprepa den här övningen eller testa en annan AI-anrikning. Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren tillbaka till noll för Cognitive Services bearbetning.