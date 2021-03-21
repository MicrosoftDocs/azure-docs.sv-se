---
title: Utforma skalbara och presterande tabeller i Azure Table Storage. | Microsoft Docs
description: Lär dig att utforma skalbara och presterande tabeller i Azure Table Storage. Granska tabellattribut, enhets grupp transaktioner och kapacitets-och kostnads överväganden.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 03/09/2020
ms.subservice: tables
ms.openlocfilehash: 8f3bd2a998066804bfb589e3262ac5e68db601fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306942"
---
# <a name="design-scalable-and-performant-tables"></a>Utforma skalbara och högpresterande tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

För att utforma skalbara och presterande tabeller måste du tänka på faktorer som prestanda, skalbarhet och kostnad. Om du tidigare har utformat scheman för Relations databaser är dessa saker välkända, men det finns vissa likheter mellan Azure Table service Storage-modellen och Relations modeller, men det finns också viktiga skillnader. Dessa skillnader leder vanligt vis till olika design som kan se till att det är intuitivt eller fel i någon bekant relation mellan Relations databaser, men är avgörande om du designar för en NoSQL nyckel/värde-lagring, till exempel Azure Table service. Många av dina design skillnader återspeglar det faktum att Table service har utformats för att stödja molnbaserade program som kan innehålla miljarder entiteter (eller rader i Relations databas terminologi) eller för data uppsättningar som måste ha stöd för hög transaktions volymer. Därför måste du tänka på ett annat sätt att lagra dina data och förstå hur Table service fungerar. Ett väl utformat NoSQL-datalager kan göra det möjligt för din lösning att skala mycket mer och till en lägre kostnad än en lösning som använder en Relations databas. Den här guiden hjälper dig med dessa ämnen.  

## <a name="about-the-azure-table-service"></a>Om Azure-Table service
I det här avsnittet beskrivs några av de viktigaste funktionerna i Table service som är särskilt relevanta för att utforma för prestanda och skalbarhet. Om du är nybörjare på Azure Storage och Table service läser du först [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md) och [kommer igång med Azure Table Storage med .net](../../cosmos-db/tutorial-develop-table-dotnet.md) innan du läser resten av den här artikeln. Även om den här hand boken är på Table service, innehåller den även en diskussion om Azure-kö-och blob-tjänster och hur du kan använda dem med Table service.  

Vad är Table service? Som du kan förväntar dig från namnet använder Table service ett tabell format för att lagra data. I standard terminologin representerar varje rad i tabellen en entitet och kolumnerna lagrar de olika egenskaperna för entiteten. Varje entitet har ett nyckel par för att unikt identifiera den och en tidsstämpelkolumn som Table service använder för att spåra när entiteten senast uppdaterades. Tidsstämpeln tillämpas automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde. Table service använder den senast ändrade tidsstämpeln (LMT) för att hantera optimistisk samtidighet.  

> [!NOTE]
> Table service REST API-åtgärder returnerar också ett **etag** -värde som den härleds från LMT. Det här dokumentet använder termerna ETag och LMT, vilket är utbytbara eftersom de refererar till samma underliggande data.  
> 
> 

I följande exempel visas en enkel tabell design där du kan lagra personal-och avdelnings enheter. Många av exemplen som visas senare i den här guiden baseras på den här enkla designen.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Allt</td>
<td>Sal</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Avdelning</td>
<td>2014-08-22T00:50:13.45.30 Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Hittills ser dessa data likadant ut som en tabell i en Relations databas med de viktigaste skillnaderna som de obligatoriska kolumnerna och möjligheten att lagra flera entitetstyper i samma tabell. Dessutom har var och en av de användardefinierade egenskaperna, till exempel **FirstName** eller **Age** , en datatyp, till exempel Integer eller string, precis som en kolumn i en Relations databas. Men till skillnad från i en Relations databas innebär att en egenskap som är mindre av Table service innebär att en egenskap inte behöver ha samma datatyp på varje entitet. Om du vill lagra komplexa data typer i en enskild egenskap måste du använda ett serialiserat format, till exempel JSON eller XML. Mer information om tabell tjänsten, till exempel data typer som stöds, datum intervall som stöds, namngivnings regler och storleks begränsningar, finns i [förstå tabell tjänstens data modell](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Ditt val av **PartitionKey** och **RowKey** är grundläggande för en bra tabell design. Varje entitet som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en Relations databas tabell indexeras värdena för **PartitionKey** och **RowKey** för att skapa ett grupperat index för att aktivera snabb uppslags fönster. Table service skapar dock inga sekundära index, så **PartitionKey** och **RowKey** är de enda indexerade egenskaperna. Några av mönstren som beskrivs i [tabell design mönster](table-storage-design-patterns.md) illustrerar hur du kan undvika den här synliga begränsningen.  

En tabell består av en eller flera partitioner, och många av de design beslut du gör är att välja en lämplig **PartitionKey** och **RowKey** för att optimera din lösning. En lösning kan bestå av en enda tabell som innehåller alla entiteter som är ordnade i partitioner, men vanligt vis har en lösning flera tabeller. Tabeller hjälper dig att logiskt organisera dina entiteter, hjälper dig att hantera åtkomst till data med hjälp av åtkomst kontrol listor och du kan släppa en hel tabell med en enda lagrings åtgärd.  

## <a name="table-partitions"></a>Table-partitioner
Konto namnet, tabell namnet och **PartitionKey** tillsammans identifierar partitionen i lagrings tjänsten där tabell tjänsten lagrar entiteten. Som en del av adresserings planen för entiteter definierar partitionerna en omfattning för transaktioner (se [enhets grupp transaktioner](#entity-group-transactions) nedan) och utgör grunden för hur tabell tjänsten skalar. Mer information om partitioner finns i [Check lista för prestanda och skalbarhet för Table Storage](storage-performance-checklist.md).  

I Table service, en enskild nod som utför en eller flera fullständiga partitioner, och tjänsten skalas genom en dynamisk belastnings utjämning av partitioner mellan noder. Om en nod är under inläsning kan tabell tjänsten *dela* det intervall med partitioner som hanteras av noden på olika noder. När trafik under sidor, kan tjänsten *sammanfoga* partitionernas intervall från tysta noder tillbaka till en enda nod.  

Mer information om den interna informationen om Table service och i synnerhet hur tjänsten hanterar partitioner finns i pappers [Microsoft Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

## <a name="entity-group-transactions"></a>Enhets grupp transaktioner
I Table service är enhets grupp transaktioner (EGTs) den enda inbyggda mekanismen för att utföra atomiska uppdateringar över flera entiteter. EGTs kallas ibland även för batch- *transaktioner*. EGTs kan bara användas på entiteter som är lagrade i samma partition (det vill säga samma partitionsnyckel i en specifik tabell). Så varje gång du behöver Atomic-transaktionell beteende över flera entiteter måste du se till att dessa entiteter finns i samma partition. Detta är ofta en orsak till att hålla flera entitetstyper i samma tabell (och partition) och inte använda flera tabeller för olika typer av enheter. En enda avställning kan köras på högst 100 entiteter.  Om du skickar flera samtidiga EGTs för bearbetning är det viktigt att se till att dessa EGTs inte fungerar på entiteter som är gemensamma för EGTs; Annars kan bearbetningen fördröjas.

EGTs introducerar också en potentiell kompromiss som du kan använda för att utvärdera i din design. Det innebär att om du använder fler partitioner ökar programmets skalbarhet eftersom Azure har fler möjligheter för belastnings Utjämnings begär Anden mellan noder. Men att använda fler partitioner kan begränsa möjligheten för ditt program att utföra atomiska transaktioner och upprätthålla stark konsekvens för dina data. Dessutom finns det vissa skalbara mål på nivån för en partition som kan begränsa data flödet för transaktioner som du kan förväntar dig för en enda nod. Mer information om skalbarhets mål för Azure standard Storage-konton finns i [skalbarhets mål för standard lagrings konton](../common/scalability-targets-standard-account.md). Mer information om skalbarhets mål för Table service finns i skalbarhets- [och prestanda mål för Table Storage](scalability-targets.md).

## <a name="capacity-considerations"></a>Överväganden för kapaciteter

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Kostnadsöverväganden
Table Storage är relativt billigt, men du bör inkludera kostnads uppskattningar för både kapacitets användning och antalet transaktioner som en del av din utvärdering av en Table service lösning. I många fall är det dock ett giltigt tillvägagångs sätt att lagra denormaliserade eller duplicerade data för att förbättra prestandan eller skalbarheten för din lösning. Mer information om priser finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Nästa steg

- [Tabell design mönster](table-storage-design-patterns.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabell data](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)