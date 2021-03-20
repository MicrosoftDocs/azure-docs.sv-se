---
title: Visa Azure Storage resurser med C++-klient bibliotek
description: 'Lär dig hur du använder API: er för registrering i Microsoft Azure Storage klient bibliotek för C++ för att räkna upp behållare, blobbar, köer, tabeller och entiteter.'
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 8c8e2d2ddf6899e62bc95bc1e52c84eccdc3a91e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92784107"
---
# <a name="list-azure-storage-resources-in-c"></a>Lista över Azure Storage-resurser i C++

List åtgärder är viktiga för många utvecklings scenarier med Azure Storage. Den här artikeln beskriver hur du effektivt räknar upp objekt i Azure Storage att använda de API: er som finns i Microsoft Azure Storage klient biblioteket för C++.

> [!NOTE]
> Den här guiden är avsedd för Azure Storage klient biblioteket för C++ version 2. x, som är tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).

Lagrings klient biblioteket innehåller en mängd metoder för att visa eller fråga objekt i Azure Storage. Den här artikeln handlar om följande scenarier:

* Lista behållare i ett konto
* Visa en lista över blobar i en behållare eller virtuell BLOB-katalog
* Lista köer i ett konto
* Lista tabeller i ett konto
* Fråga entiteter i en tabell

Var och en av dessa metoder visas med olika överbelastningar för olika scenarier.

## <a name="asynchronous-versus-synchronous"></a>Asynkron jämfört med synkron

Eftersom lagrings klient biblioteket för C++ skapas ovanpå [C++ rest-biblioteket](https://github.com/Microsoft/cpprestsdk), stöder vi asynkrona åtgärder med hjälp av [PPLX:: Task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Exempel:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synkrona åtgärder radbryter motsvarande asynkrona åtgärder:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Om du arbetar med flera trådbaserade program eller tjänster rekommenderar vi att du använder asynkrona API: er direkt i stället för att skapa en tråd för att anropa API: erna för synkronisering, vilket märkbart påverkar prestandan.

## <a name="segmented-listing"></a>Segmenterad lista

Skalan för moln lagring kräver segmenterad lista. Du kan till exempel ha över en miljon blobbar i en Azure Blob-behållare eller över en miljard entiteter i en Azure-tabell. Dessa är inte teoretiska tal, men verkliga kund användnings fall.

Det är därför opraktiskt att lista alla objekt i ett enda svar. I stället kan du lista objekt med växling. Varje lista över API: er har en *segmenterad* överlagring.

Svaret på en segmenterad registrerings åtgärd inkluderar:

* *_segment*, som innehåller den uppsättning resultat som returneras för ett enda anrop till List-API: et.
* *continuation_token*, som skickas till nästa anrop för att få nästa resultat sida. Om det inte finns några fler resultat att returnera, är den fortsättnings-token null.

Till exempel kan ett typiskt anrop för att lista alla blobbar i en behållare se ut som i följande kodfragment. Koden är tillgänglig i våra [exempel](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Observera att antalet resultat som returneras på en sida kan kontrol leras av parametern *max_results* i överlagringen för varje API, till exempel:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Om du inte anger parametern *max_results* returneras det maximala standardvärdet på upp till 5000 resultat på en enda sida.

Observera också att en fråga mot Azure Table Storage kan returnera inga poster eller färre poster än värdet för den *max_results* parameter som du har angett, även om tilläggs-token inte är tom. En orsak kan vara att frågan inte kunde slutföras på fem sekunder. Så länge som tilläggs-token inte är tom, ska frågan fortsätta och din kod ska inte anta storleken på segment resultatet.

Rekommenderat kodnings mönster för de flesta scenarier är segmenterade listor, vilket ger uttryckliga förlopps noteringar eller frågor och hur tjänsten svarar på varje begäran. I synnerhet för C++-program eller-tjänster kan kontrollens prestanda på lägre nivå kontrol leras för att kontrol lera minne och prestanda.

## <a name="greedy-listing"></a>Girig lista

Tidigare versioner av lagrings klient biblioteket för C++ (versioner 0.5.0 Preview och tidigare) inkluderade icke-segmenterade registrerings-API: er för tabeller och köer, som i följande exempel:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Dessa metoder implementerades som omslag för segmenterade API: er. För varje svar på segmenterad lista lägger koden till resultatet i en Vector och returnerade alla resultat efter att de fullständiga behållarna genomsöktes.

Den här metoden kan fungera när lagrings kontot eller-tabellen innehåller ett litet antal objekt. Men med en ökning av antalet objekt kan det minne som krävs öka utan begränsning, eftersom alla resultat låg kvar i minnet. En List åtgärd kan ta lång tid, under vilken anroparen inte hade någon information om förloppet.

Dessa girig som visar API: er i SDK finns inte i C#, Java eller Java Script Node.js-miljön. För att undvika potentiella problem med att använda dessa girig-API: er har vi tagit bort dem i version 0.6.0 Preview.

Om din kod anropar dessa girig-API: er:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Sedan bör du ändra koden för att använda API: er för segment lista:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Genom att ange *max_results* parameter för segmentet kan du balansera mellan antalet förfrågningar och minnes användning för att uppfylla prestanda överväganden för ditt program.

Om du använder segmentbaserade API: er för registrering, men lagrar data i en lokal samling i ett "girig"-format, rekommenderar vi också att du återanvänder din kod för att hantera lagring av data i en lokal samling noggrant i stor skala.

## <a name="lazy-listing"></a>Lazy List

Även om girig visar utlösta potentiella problem, är det praktiskt om det inte finns för många objekt i behållaren.

Om du också använder C# eller Oracle Java SDK: er bör du vara bekant med programmerings modellen enumerable, som erbjuder en lista med Lazy-format, där data vid en viss förskjutning bara hämtas om det krävs. I C++ ger den iteratorbaserade mallen också en liknande metod.

Ett typiskt Lazy List-API som använder **list_blobs** som exempel, ser ut så här:

```cpp
list_blob_item_iterator list_blobs() const;
```

Ett typiskt kodfragment som använder det Lazy List mönstret kan se ut så här:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Observera att Lazy List endast är tillgängligt i synkront läge.

Jämfört med girig List hämtar den Lazy listen bara data när det behövs. Under försättsblad hämtar den data från Azure Storage endast när nästa iterator flyttas in i nästa segment. Därför kontrol leras minnes användningen med en storlek som har bundits och åtgärden går snabbt.

API: er för Lazy List ingår i lagrings klient biblioteket för C++ i version 2.2.0.

## <a name="conclusion"></a>Slutsats

I den här artikeln har vi diskuterat olika överbelastningar för att Visa API: er för olika objekt i lagrings klient biblioteket för C++. Sammanfattningsvis:

* Asynkrona API: er rekommenderas starkt under flera tråd scenarier.
* Segmenterad lista rekommenderas för de flesta scenarier.
* En Lazy-lista finns i biblioteket som ett bekvämt gränssnitt i synkrona scenarier.
* Girig-lista rekommenderas inte och har tagits bort från biblioteket.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Storage och klient bibliotek för C++ finns i följande resurser.

* [Använda Blob Storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Table Storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Så använder du Queue Storage från C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage klient bibliotek för C++ API-dokumentation.](https://azure.github.io/azure-storage-cpp/)
* [Azure Storage teamets blogg](/archive/blogs/windowsazurestorage/)
* [Azure Storage dokumentation](https://azure.microsoft.com/documentation/services/storage/)