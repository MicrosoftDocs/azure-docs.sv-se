---
title: Indexeringsfel och -varningar
titleSuffix: Azure Cognitive Search
description: Den här artikeln innehåller information och lösningar på vanliga fel och varningar som du kan stöta på under AI-anrikning i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3ba0abe8510291351c10ba085ba7e42b8197d886
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553246"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Felsöka vanliga index fel och varningar i Azure Kognitiv sökning

Den här artikeln innehåller information och lösningar på vanliga fel och varningar som du kan stöta på under indexering och AI-anrikning i Azure Kognitiv sökning.

Indexeringen stoppas när antalet fel överstiger ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Om du vill att indexerarna ska ignorera dessa fel (och hoppa över "misslyckade dokument") bör du överväga att uppdatera `maxFailedItems` och `maxFailedItemsPerBatch` enligt beskrivningen [här](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Varje misslyckat dokument tillsammans med dess dokument nyckel (när det är tillgängligt) visas som ett fel i körnings statusen för indexeraren. Du kan använda [index-API: et](/rest/api/searchservice/addupdate-or-delete-documents) för att ladda upp dokumenten manuellt vid ett senare tillfälle om du har ställt in indexeraren för att tolerera felen.

Fel informationen i den här artikeln kan hjälpa dig att lösa fel som gör att indexeringen kan fortsätta.

Varningar slutar inte att indexera, men de anger villkor som kan leda till oväntade resultat. Oavsett om du vidtar åtgärder eller inte beror på data och ditt scenario.

Från och med API `2019-05-06` -versionen struktureras fel och varningar på objekt nivå för att ge ökad klarhet kring orsaker och nästa steg. De innehåller följande egenskaper:

| Egenskap | Beskrivning | Exempel |
| --- | --- | --- |
| key | Dokument-ID: t för dokumentet som påverkas av felet eller varningen. | https: \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Åtgärds namnet som beskriver var felet eller varningen uppstod. Detta genereras av följande struktur: [Category]. [under kategori]. [resourceType]. resourceName | DocumentExtraction. azureblob. myBlobContainerName-anrikning. WebApiSkill. mySkillName projektion. SearchIndex. OutputFieldMapping. myOutputFieldName projektion. SearchIndex. MergeOrUpload. myIndexName projektion. KnowledgeStore. Table. myTableName |
| meddelande | En övergripande beskrivning av felet eller varningen. | Det gick inte att köra kompetensen eftersom webb-API-begäran misslyckades. |
| information | Ytterligare information som kan vara till hjälp för att diagnostisera problemet, till exempel WebApi-svar om det inte gick att köra en anpassad färdighet. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 källa, Func `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ... rest av stack spårning... |
| documentationLink | En länk till relevant dokumentation med detaljerad information för att felsöka och lösa problemet. Den här länken pekar ofta på ett av nedanstående avsnitt på den här sidan. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>Fel: det gick inte att läsa dokumentet

Indexeraren kunde inte läsa dokumentet från data källan. Detta kan inträffa på grund av:

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| Inkonsekventa fält typer i olika dokument | "Typen av värde har en matchnings fel med kolumn typen. Det gick inte att lagra `'{47.6,-122.1}'` i kolumnen författare.  Förväntad typ är JArray. "  "Det gick inte att konvertera data typen nvarchar till float."  "Konverteringen misslyckades vid konvertering av nvarchar-värdet 12 månader till data typen int."  "Aritmetiskt data spills fel vid konvertering av uttryck till data typen int." | Se till att typen för varje fält är samma för olika dokument. Om det första fältet till exempel `'startTime'` är ett datum/tid och i det andra dokumentet är det fel meddelandet. |
| fel från data källans underliggande tjänst | (från Cosmos DB) `{"Errors":["Request rate is large"]}` | Kontrol lera lagrings instansen för att säkerställa att den är felfri. Du kan behöva justera skalning/partitionering. |
| tillfälliga problem | Ett fel på transport nivå har uppstått när resultat togs emot från servern. (provider: TCP-Provider, fel: 0-en befintlig anslutning tvingades stänga av den fjärranslutna värden | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Fel: det gick inte att extrahera innehåll eller metadata från dokumentet
Indexeraren med en BLOB-datakälla kunde inte extrahera innehåll eller metadata från dokumentet (till exempel en PDF-fil). Detta kan inträffa på grund av:

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| blobben överskrider storleks gränsen | Dokumentet är `'150441598'` byte, vilket överskrider max storleken på `'134217728'` byte för dokument extrahering för din aktuella tjänst nivå. | [BLOB-indexerings fel](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| BLOB har en innehålls typ som inte stöds | Dokumentet innehåller en innehålls typ som inte stöds `'image/png'` | [BLOB-indexerings fel](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| bloben är krypterad | Det gick inte att bearbeta dokumentet – det kan vara krypterat eller lösenordsskyddat. | Du kan hoppa över blobben med [BLOB-inställningar](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex). |
| tillfälliga problem | "Fel vid bearbetning av BLOB: begäran avbröts: begäran avbröts." "Tids gränsen nåddes för dokumentet under bearbetningen." | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>Fel: det gick inte att parsa dokumentet
Indexeraren läser dokumentet från data källan, men det uppstod ett problem med att konvertera dokument innehållet till det angivna fält mappnings schemat. Detta kan inträffa på grund av:

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| Dokument nyckeln saknas | Dokument nyckeln kan inte vara tom eller saknas | Se till att alla dokument har giltiga dokument nycklar. Dokument nyckeln bestäms genom att ange Key-egenskapen som en del av [index definitionen](/rest/api/searchservice/create-index#request-body). Indexerare genererar det här felet när egenskapen som flaggats som "nyckel" inte finns i ett visst dokument. |
| Dokument nyckeln är ogiltig | Dokument nyckeln får innehålla högst 1024 tecken | Ändra dokument nyckeln så att den uppfyller verifierings kraven. |
| Det gick inte att använda fält mappning i ett fält | Det gick inte att använda mappnings funktionen `'functionName'` till fältet `'fieldName'` . Matris får inte vara null. Parameter namn: byte | Dubbelt kontrol lera de [fält mappningar](search-indexer-field-mappings.md) som definierats för indexeraren och jämför med informationen i det angivna fältet för det misslyckade dokumentet. Det kan vara nödvändigt att ändra fält mappningarna eller dokument data. |
| Det gick inte att läsa fältvärdet | Det gick inte att läsa värdet för kolumnen `'fieldName'` vid indexet `'fieldIndex'` . Ett fel på transport nivå har uppstått när resultat togs emot från servern. (provider: TCP-Provider, fel: 0-en befintlig anslutning tvingades stänga av den fjärranslutna värden.) | Dessa fel beror vanligt vis på oväntade anslutnings problem med data källans underliggande tjänst. Försök att köra dokumentet via din indexerare igen senare. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>Fel: det gick inte att mappa utmatnings fältet `xyz` till Sök indexet på grund av deserialiserings problem när mappnings funktionen tillämpades `abc`
Utmatnings mappningen kan ha misslyckats på grund av att utdata har fel format för den mappnings funktion som du använder. Om du till exempel använder Base64Encode-mappnings funktionen på binära data genereras det här felet. Du kan lösa problemet genom att antingen köra indexeraren igen utan att ange mappnings funktion eller kontrol lera att mappnings funktionen är kompatibel med data typen output Field. Se [Mappning av utdatakolumner](cognitive-search-output-field-mapping.md) för mer information.

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>Fel: det gick inte att köra kompetensen
Indexeraren kunde inte köra en färdighet i färdigheter.

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| Problem med tillfälliga anslutningar | Ett tillfälligt fel har uppstått. Försök igen senare. | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |
| Potentiell produkt fel | Det uppstod ett oväntat fel. | Detta indikerar en okänd fel klass och det kan betyda att det finns en produkt bugg. Använd ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp. |
| Ett fel uppstod vid körningen av en färdighet | (Från slå samman färdighet) Ett eller flera förskjutnings värden var ogiltiga och kunde inte parsas. Objekt infogades i slutet av texten | Använd informationen i fel meddelandet för att åtgärda problemet. Den här typen av haveri kräver åtgärd för att lösa problemet. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Fel: det gick inte att köra kompetensen eftersom webb-API-begäran misslyckades
Det gick inte att köra kompetensen eftersom anropet till webb-API: et misslyckades. Den här klassen av fel inträffar vanligt vis när anpassade kunskaper används. i så fall måste du Felsöka den anpassade koden för att lösa problemet. Om felet i stället är från en inbyggd färdighet kan du läsa fel meddelandet för att få hjälp med att åtgärda problemet.

När du felsöker det här problemet bör du vara noga med att ta hänsyn till eventuella [ingångs varningar](#warning-skill-input-was-invalid) för kompetensen. Webb-API-slutpunkten kan Miss lyckas eftersom indexeraren skickar den oväntade indatamängden.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Fel: det gick inte att köra kompetensen eftersom kunskaps svaret för webb-API är ogiltigt
Det gick inte att köra kompetensen eftersom anropet till webb-API: et returnerade ett ogiltigt svar. Den här klassen av fel inträffar vanligt vis när anpassade kunskaper används. i så fall måste du Felsöka den anpassade koden för att lösa problemet. Om det i stället är från en inbyggd färdighet kan du skicka in ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp.

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Fel: det gick inte att köra kompetensen inom tids gränsen
Det finns två fall där du kan stöta på det här fel meddelandet, som var och en behandlas på olika sätt. Följ anvisningarna nedan, beroende på vilken kompetens som returnerade felet åt dig.

### <a name="built-in-cognitive-service-skills"></a>Inbyggda kunskaper om kognitiva tjänster
Många av de inbyggda kognitiva färdigheterna, till exempel språk identifiering, entitets igenkänning eller OCR, backas upp av en kognitiv tjänst-API-slutpunkt. Ibland finns det tillfälliga problem med dessa slut punkter och tids gränsen för en begäran upphör. För tillfälliga problem finns det ingen åtgärd förutom att vänta och försöka igen. Som en minskning bör du överväga att ställa in indexeraren att [köras enligt ett schema](search-howto-schedule-indexers.md). Schemalagd indexering hämtar var den slutade. Förutsatt att de tillfälliga problemen är lösta, bör indexerings-och kognitiva kompetens bearbetningen fortsätta vid nästa schemalagda körning.

Om du fortsätter att se det här felet i samma dokument för en inbyggd kognitiv skicklighet, kan du skicka in ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp, eftersom detta inte förväntas.

### <a name="custom-skills"></a>Anpassade färdigheter
Om du stöter på ett tids gräns fel med en anpassad färdighet som du har skapat, finns det några saker du kan prova. Börja med att granska din anpassade färdighet och se till att den inte fastnar i en oändlig slinga och att den returnerar ett resultat konsekvent. När du har bekräftat att är fallet ska du ta reda på hur körnings tiden för din färdighet är. Om du inte uttryckligen angav ett `timeout` värde för din anpassade kunskaps definition är standardvärdet `timeout` 30 sekunder. Om 30 sekunder inte är tillräckligt lång för att din kunskap ska kunna köras, kan du ange ett högre `timeout` värde för din anpassade kunskaps definition. Här är ett exempel på en anpassad kunskaps definition där tids gränsen har angetts till 90 sekunder:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Det maximala värdet som du kan ange för `timeout` parametern är 230 sekunder.  Om din anpassade färdighet inte kan köras konsekvent inom 230 sekunder kan du överväga att minska den `batchSize` anpassade kunskapen så att den kommer att ha färre dokument för bearbetning i en enda körning.  Om du redan har angett `batchSize` till 1 måste du skriva om färdigheten för att kunna köra under 230 sekunder eller dela upp den i flera anpassade kunskaper så att körnings tiden för en enskild anpassad färdighet är högst 230 sekunder. Läs den [anpassade kunskaps dokumentationen](cognitive-search-custom-skill-web-api.md) för mer information.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Fel: det gick inte att `MergeOrUpload` | ' `Delete` '-dokument i Sök indexet

Dokumentet lästes och bearbetades, men indexeraren kunde inte lägga till det i Sök indexet. Detta kan inträffa på grund av:

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| Ett fält innehåller en term som är för stor | En term i dokumentet är större än [32 KB-gränsen](search-limits-quotas-capacity.md#api-request-limits) | Du kan undvika den här begränsningen genom att se till att fältet inte har kon figurer ATS som filtrerat, aspekt Bart eller sorterbart.
| Dokumentet är för stort för att indexeras | Ett dokument är större än [Max storleken för API-begäranden](search-limits-quotas-capacity.md#api-request-limits) | [Så här indexerar du stora data mängder](search-howto-large-index.md)
| Dokumentet innehåller för många objekt i samlingen | En samling i dokumentet överskrider [Max antalet element i alla komplexa samlings gränser](search-limits-quotas-capacity.md#index-limits) : dokumentet med nyckeln `'1000052'` har `'4303'` objekt i samlingar (JSON-matriser). Högst `'3000'` objekt får finnas i samlingar i hela dokumentet. Ta bort objekt från samlingar och försök att indexera dokumentet igen. " | Vi rekommenderar att du minskar storleken på den komplexa samlingen i dokumentet till under gränsen och undviker hög lagrings belastning.
| Det går inte att ansluta till mål indexet (som kvarstår efter återförsök) eftersom tjänsten är under annan belastning, t. ex. frågor eller indexering. | Det gick inte att upprätta en anslutning till uppdaterings index. Sök tjänsten är hårt belastad. | [Skala upp Sök tjänsten](search-capacity-planning.md)
| Sök tjänsten korrigeras för tjänst uppdatering eller är i mitten av en omkonfiguration av topologin. | Det gick inte att upprätta en anslutning till uppdaterings index. Sök tjänsten är för närvarande nere/Search-tjänsten genomgår en över gång. | Konfigurera tjänsten med minst tre repliker för 99,9% tillgänglighet per [SLA-dokumentation](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Haveri i den underliggande beräknings-/nätverks resursen (sällsynt) | Det gick inte att upprätta en anslutning till uppdaterings index. Ett okänt fel uppstod. | Konfigurera indexerare så att de [körs enligt ett schema](search-howto-schedule-indexers.md) för att hämta från ett felaktigt tillstånd.
| En indexerings förfrågan som gjorts till mål indexet bekräftades inte inom en tids gräns på grund av nätverks problem. | Det gick inte att upprätta en anslutning till Sök indexet inom rimlig tid. | Konfigurera indexerare så att de [körs enligt ett schema](search-howto-schedule-indexers.md) för att hämta från ett felaktigt tillstånd. Dessutom kan du försöka med att sänka [batchstorleken](/rest/api/searchservice/create-indexer#parameters) för indexeraren om detta fel tillstånd kvarstår.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Fel: det gick inte att indexera dokumentet eftersom vissa dokument data inte var giltiga

Dokumentet lästes och bearbetades av indexeraren, men på grund av ett matchnings fel i konfigurationen av index fälten och de data som extraherats och behandlats av indexeraren, gick det inte att lägga till i Sök indexet. Detta kan inträffa på grund av:

| Anledning | Information/exempel
| --- | ---
| Data typen för det eller de fält som extraherats av indexeraren är inte kompatibelt med data modellen för motsvarande mål index fält. | Data fältets _data_ i dokumentet med nyckeln 888 har ett ogiltigt värde av typen EDM. String. Den förväntade typen var ' Collection (EDM. String) '. |
| Det gick inte att extrahera någon JSON-entitet från ett sträng värde. | Det gick inte att parsa värdet ' av typen ' EDM. String ' ' för fält _data_ som ett JSON-objekt. Fel: efter parsning av ett värde påträffades ett oväntat Character:. Sökväg '_sökväg_', rad 1, position 3162. ' |
| Det gick inte att extrahera en samling JSON-entiteter från ett sträng värde.  | Det gick inte att parsa värdet ' av typen ' EDM. String ' ' för fält _data_ som en JSON-matris. Fel: efter parsning av ett värde påträffades ett oväntat Character:. Sökväg [0], rad 1, position 27. |
| En okänd typ upptäcktes i käll dokumentet. | Okänd typ '_okänd_' kan inte indexeras |
| En inkompatibel notation för geografi punkter användes i käll dokumentet. | Sträng litteraler för well punkt stöds inte. Använd ingångar av polyjson-punkter i stället |

I alla dessa fall refererar du till [data typer som stöds](/rest/api/searchservice/supported-data-types) och [data typs mappning för indexerare](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) för att se till att du skapar index schemat korrekt och har konfigurerat lämpliga [fält mappningar för indexeraren](search-indexer-field-mappings.md). Fel meddelandet innehåller information som kan hjälpa till att spåra källan till matchnings felet.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Fel: den integrerade principen för ändrings spårning kan inte användas eftersom tabellen har en sammansatt primär nyckel

Detta gäller för SQL-tabeller och inträffar vanligt vis när nyckeln antingen definieras som en sammansatt nyckel eller, när tabellen har definierat ett unikt grupperat index (som i ett SQL-index, inte ett Azure Search index). Huvud orsaken är att nyckelattributet ändras till en sammansatt primär nyckel när ett [unikt grupperat index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)används. I så fall kontrollerar du att SQL-tabellen inte har ett unikt grupperat index eller att du mappar nyckel fältet till ett fält som garanterat inte har dubblettvärden.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Fel: det gick inte att bearbeta dokumentet inom indexeraren maximal kör tid

Det här felet uppstår när indexeraren inte kan slutföra bearbetningen av ett enstaka dokument från data källan inom den tillåtna körnings tiden. [Maximal kör tid](search-limits-quotas-capacity.md#indexer-limits) är kortare när färdighetsuppsättningar används. Om det här felet uppstår, om maxFailedItems har angetts till ett annat värde än 0, hoppar indexeraren över dokumentet vid framtida körningar så att indexeringen kan fortsätta. Om du inte kan välja att hoppa över några dokument, eller om du ser det här felet konsekvent, bör du överväga att dela upp dokument i mindre dokument så att delvis förloppet kan göras inom en enskild indexerare-körning.

<ett namn = "Det gick inte att projicera dokument></a>

## <a name="error-could-not-project-document"></a>Fel: det gick inte att projicera dokumentet

Felet uppstår när indexeraren försöker [projicera data till ett kunskaps lager](knowledge-store-projection-overview.md) och det uppstod ett fel i vårt försök att göra det.  Det här felet kan vara konsekvent och fixablet, eller så kan det vara ett tillfälligt fel med Utkorgen för projektion av utdata som du kan behöva vänta och försöka igen för att kunna lösa problemet.  Här är en uppsättning kända fellägen och möjliga lösningar.

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| Det gick inte att uppdatera projektions-bloben `'blobUri'` i behållaren `'containerName'` |Den angivna behållaren finns inte. | Indexeraren kontrollerar om den angivna behållaren har skapats tidigare och kommer att skapa den om det behövs, men den utför bara den här kontrollen en gång per indexerare körs. Det här felet innebär att något har tagits bort från behållaren efter det här steget.  Försök att lösa det här felet: lämna din lagrings konto information, vänta tills indexeraren har slutförts och kör sedan indexeraren igen. |
| Det gick inte att uppdatera projektions-bloben `'blobUri'` i behållaren `'containerName'` |Det gick inte att skriva data till transport anslutningen: en befintlig anslutning tvingades stänga av den fjärranslutna värden. | Detta förväntas vara ett tillfälligt haveri med Azure Storage och bör därför lösas genom att köra om indexeraren. Om det här felet uppstår konsekvent kan du skicka ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) så att det kan undersökas ytterligare.  |
| Det gick inte att uppdatera raden `'projectionRow'` i tabellen `'tableName'` | Servern är upptagen. | Detta förväntas vara ett tillfälligt haveri med Azure Storage och bör därför lösas genom att köra om indexeraren. Om det här felet uppstår konsekvent kan du skicka ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) så att det kan undersökas ytterligare.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>Varning! kompetens ineffekten var ogiltig
En inström till kompetensen saknades, fel typ eller på annat sätt är ogiltig. Varnings meddelandet visar effekten:
1) Det gick inte att köra kompetensen
2) Kunskapen har utförts men kan ha oväntade resultat

Kognitiva kunskaper har obligatoriska indata och valfria indata. Till exempel är [extraherings kunskaper för nyckel fraser](cognitive-search-skill-keyphrases.md) två obligatoriska indata `text` , `languageCode` och inga valfria indata. Anpassade färdighets indata betraktas som valfria indata.

Om några obligatoriska indata saknas eller om några indata inte är av rätt typ hoppas kunskapen över och genererar en varning. Överhoppade kunskaper genererar inga utdata, så om andra kunskaper använder utdata från den överhoppade kunskapen kan de generera ytterligare varningar.

Om en valfri indata saknas körs kunskapen fortfarande men det kan leda till oväntade utdata på grund av saknade indata.

I båda fallen kan denna varning förväntas på grund av formens data. Om du till exempel har ett dokument som innehåller information om personer med fälten, `firstName` `middleName` och kan `lastName` du ha vissa dokument som inte har någon post för `middleName` . Om du vill skicka `middleName` som inmatade till en färdighet i pipelinen förväntas det att det saknas en del av tiden. Du måste utvärdera dina data och scenario för att avgöra om någon åtgärd krävs till följd av den här varningen.

Om du vill ange ett standardvärde i händelse av saknade indata kan du använda den [villkorliga kompetensen](cognitive-search-skill-conditional.md) för att generera ett standardvärde och sedan använda resultatet av den [villkorliga kompetensen](cognitive-search-skill-conditional.md) som färdighets indata.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| Kompetens ineffekten är av fel typ | "Nödvändig kompetens information var inte av den förväntade typen `String` . Namn: `text` , källa: `/document/merged_content` . "  "Krav på färdighets information var inte av det förväntade formatet. Namn: `text` , källa: `/document/merged_content` . "  "Det går inte att iterera över icke-matriser `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ".  "Det går inte att välja `0` i icke-matrisen `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` " | Vissa kunskaper förväntar sig indata av särskilda typer, till exempel [sentiment-kompetens](cognitive-search-skill-sentiment.md) förväntar sig `text` vara en sträng. Om indatan anger ett värde som inte är ett sträng värde, körs inte kompetensen och genererar inga utdata. Se till att data uppsättningen har inmatnings värden som är enhetliga i typ eller Använd en [anpassad webb-API-färdighet](cognitive-search-custom-skill-web-api.md) för att Förbearbeta indatan. Om du vill iterera över en matris kontrollerar du kunskaps sammanhanget och att inmatade uppgifter har `*` rätt position. Vanligt vis ska både kontexten och Indatakällan sluta med `*` för matriser. |
| Kompetens ineffekt saknas | "Nödvändig kompetens information saknas. Namn: `text` , källa: `/document/merged_content` "" värde saknas `/document/normalized_images/0/imageTags` . "  "Det går inte att välja `0` i matrisen `/document/pages` med längden `0` ." | Om alla dokument får den här varningen, är det förmodligen ett stavfel i inmatnings Sök vägarna och du bör dubbelt kontrol lera egenskaps namnets Skift läge, extra eller saknas `*` i sökvägen och kontrol lera att dokumenten från data källan innehåller de nödvändiga indatana. |
| Inmatade kunskaper om språk kod är ogiltiga | Kompetens ineffekten `languageCode` har följande språk koder `X,Y,Z` , minst en som är ogiltig. | Se mer information [nedan](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Varning! kompetens ingången languageCode har följande språk koder, X, Y, Z, och minst en av dem är ogiltig.
Ett eller flera av värdena som överförs till den valfria `languageCode` indatamängden för en underordnad färdighet stöds inte. Detta kan inträffa om du skickar utdata från [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) till efterföljande kunskaper och utdata består av fler språk än vad som stöds i de efterföljande färdigheterna.

Obs! Du kan också få en varning som liknar detta om en ogiltig `countryHint` inmatare skickas till LanguageDetectionSkill. Om det händer kontrollerar du att fältet som du använder från data källan för indata innehåller giltiga lands koder för ISO 3166-1 alpha-2 2. Om några är giltiga och några är ogiltiga, Fortsätt med följande rikt linjer, men Ersätt `languageCode` med `countryHint` och `defaultLanguageCode` med `defaultCountryHint` för att matcha ditt användnings fall.

Om du vet att din data uppsättning är på ett språk bör du ta bort [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) och `languageCode` färdighets inmatningen och använda `defaultLanguageCode` färdighets parametern för den kunskapen i stället, förutsatt att språket stöds för den aktuella kompetensen.

Om du vet att din data uppsättning innehåller flera språk och därför behöver [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) och `languageCode` indata, kan du överväga att lägga till en [ConditionalSkill](cognitive-search-skill-conditional.md) för att filtrera bort texten med språk som inte stöds innan du skickar texten till den efterföljande färdigheten.  Här är ett exempel på hur det kan se ut för EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Här följer några referenser för de språk som stöds för närvarande för var och en av de kunskaper som kan skapa det här fel meddelandet:
* [Textanalys språk som stöds](../cognitive-services/text-analytics/language-support.md) (för [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)och [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Språk som stöds av Translator](../cognitive-services/translator/language-support.md) (för [texten TranslationSkill](cognitive-search-skill-text-translation.md))
* [Text SplitSkill](cognitive-search-skill-textsplit.md) Språk som stöds: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>Varning! kompetens ineffekten trunkerades
Kognitiva kunskaper har gränser för text längden som kan analyseras samtidigt. Om text ingången för dessa kunskaper överskrider den gränsen kommer vi att trunkera texten för att uppfylla gränsen och sedan utföra anrikningen på den trunkerade texten. Det innebär att kunskapen körs, men inte över alla dina data.

I exemplet nedan `'text'` kan indatamängden utlösa den här varningen om den ligger över tecken gränsen. Du hittar de ingångs begränsningar du har i [kunskaps dokumentationen](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Om du vill se till att all text analyseras bör du överväga att använda [delade kunskaper](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Varning! kunskaps svar för webb-API innehåller varningar
Indexeraren kunde köra en färdighet i färdigheter, men svaret från webb-API-begäran angav att det fanns varningar under körningen. Granska varningarna för att förstå hur dina data påverkas och huruvida en åtgärd krävs eller inte.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Varning! den aktuella indexerare-konfigurationen stöder inte stegvist förlopp

Den här varningen inträffar bara för Cosmos DB data källor.

Stegvis förlopp under indexeringen säkerställer att om körningen av Indexer avbryts vid tillfälliga haverier eller körnings tids gräns, kan indexeraren Hämta var den slutade nästa gång den körs, i stället för att behöva indexera om hela samlingen från grunden. Detta är särskilt viktigt när du indexerar stora samlingar.

Möjligheten att återuppta ett index jobb som inte är klart är predikat på att ha dokument ordnade efter `_ts` kolumnen. Indexeraren använder tidsstämpeln för att avgöra vilket dokument som ska plockas härnäst. Om `_ts` kolumnen saknas eller om indexeraren inte kan avgöra om en anpassad fråga beställs, börjar indexeraren i början och du ser den här varningen.

Det är möjligt att åsidosätta det här beteendet, aktivera stegvisa framsteg och ignorera den här varningen med hjälp av `assumeOrderByHighWatermarkColumn` konfigurations egenskapen.

Mer information finns i [stegvisa framsteg och anpassade frågor](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Varning: vissa data gick förlorade under projektionen. Raden "X" i tabellen "Y" har sträng egenskapen "Z", vilken var för lång.

Den [Table Storage tjänsten](https://azure.microsoft.com/services/storage/tables) har gränser för hur stora [enhets egenskaper](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) kan vara. Strängar kan innehålla högst 32 000 tecken. Om en rad med en sträng egenskap som är längre än 32 000 tecken projiceras, bevaras bara de första 32 000 tecknen. Undvik det här problemet genom att undvika att projicera rader med sträng egenskaper som är längre än 32 000 tecken.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Varning: trunkerad extraherad text till X tecken
Indexerare begränsar hur mycket text som kan extraheras från ett dokument. Den här gränsen är beroende av pris nivån: 32 000 tecken för kostnads fri nivå, 64 000 för Basic, 4 000 000 för standard, 8 000 000 för standard S2 och 16 000 000 för standard S3. Text som trunkerades kommer inte att indexeras. Undvik den här varningen genom att dela upp dokument med stora mängder text till flera mindre dokument. 

Mer information finns i [gränser för indexerare](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Varning! det gick inte att mappa utmatnings fältet "X" till Sök indexet
Mappningar av utdatakolumner som refererar till icke-existerande/null-data genererar varningar för varje dokument och resulterar i ett tomt index fält. Du kan lösa det här problemet genom att dubbelklicka på fältet utdata – mappa käll Sök vägar för möjliga skrivfel eller ange ett standardvärde med hjälp av den [villkorliga kompetensen](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist). Se [Mappning av utdatakolumner](cognitive-search-output-field-mapping.md) för mer information.

| Anledning | Information/exempel | Lösning |
| --- | --- | --- |
| Det går inte att iterera över icke-matriser | "Det går inte att iterera över icke-matriser `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ". | Felet uppstår när utdata inte är en matris. Om du tror att utdata ska vara en matris, kontrollerar du den angivna sökvägen till käll filen för utdata för fel. Du kan till exempel ha en saknad eller extra `*` i käll fält namnet. Det är också möjligt att indatamängden till den här kompetensen är null, vilket resulterar i en tom matris. Det finns ett ogiltigt avsnitt för att hitta liknande information i [kompetens indata](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) .    |
| Det går inte att välja `0` i icke-matriser | "Det går inte att välja `0` i icke-matrisen `/document/pages` ." | Detta kan inträffa om det inte skapas någon matris i kunskaps resultatet och fält namnet för utdatakällan har mat ris index eller `*` sökväg. Se till att du har angett de sökvägar som finns i fält namnen för utdata och värdet i fältet för det angivna fält namnet. Det finns ett ogiltigt avsnitt för att hitta liknande information i [kompetens indata](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) .  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Varning! principen för data ändrings identifiering har kon figurer ATS för att använda nyckel kolumnen "X"
[Principer för data ändrings identifiering](/rest/api/searchservice/create-data-source#data-change-detection-policies) har särskilda krav för de kolumner som de använder för att identifiera ändringar. Ett av dessa krav är att den här kolumnen uppdateras varje gång käll posten ändras. Ett annat krav är att det nya värdet för den här kolumnen är större än det tidigare värdet. Nyckel kolumnerna uppfyller inte det här kravet eftersom de inte ändras vid varje uppdatering. Undvik det här problemet genom att välja en annan kolumn för principen för ändrings identifiering.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Varning! dokument texten verkar vara UTF-16-kodad, men saknar en byte ordnings markering

[Indexerings lägena för indexeraren](/rest/api/searchservice/create-indexer#blob-configuration-parameters) måste veta hur texten kodas innan den kan parsas. De två vanligaste sätten att koda text är UTF-16 och UTF-8. UTF-8 är en kodning med variabel längd där varje tecken är mellan 1 byte och 4 byte långt. UTF-16 är en kodning med fast längd där varje tecken är 2 byte långt. UTF-16 har två olika varianter, "big endian" och "little endian". Text kodningen bestäms av en "byte ordnings markering", en serie byte före texten.

| Kodning | Markering av byte ordning |
| --- | --- |
| UTF-16 big endian | 0xFE 0xFF |
| UTF-16 lite endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Om det inte finns någon byte ordnings markering antas texten vara kodad som UTF-8.

Du kan undvika den här varningen genom att ta reda på vad text kodningen för denna BLOB är och lägga till rätt byte ordnings tecken.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Varning: Cosmos DB samlingen "X" har en Lazy-indexerings princip. Vissa data kan gå förlorade

Samlingar med [Lazy](../cosmos-db/index-policy.md#indexing-mode) indexerings principer kan inte frågas konsekvent, vilket leder till att indexeraren saknar data. Undvik den här varningen genom att ändra indexerings principen till konsekvent.

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>Varning! dokumentet innehåller väldigt långa ord (längre än 64 tecken). Dessa ord kan resultera i trunkerade och/eller otillförlitliga modell förutsägelser.

Den här varningen skickas från Textanalyss tjänsten.  I vissa fall är det säkert att ignorera den här varningen, t. ex. när dokumentet innehåller en lång URL (som troligen inte är en nyckel fras eller en sentiment, osv.).  Tänk på att när ett ord är längre än 64 tecken kommer det att trunkeras till 64 tecken som kan påverka modell förutsägelserna.