---
title: Säkerhets filter för trimning av resultat
titleSuffix: Azure Cognitive Search
description: Lär dig hur du implementerar säkerhets privilegier på dokument nivå för Azure Kognitiv sökning Sök resultat, med hjälp av säkerhets filter och användar identiteter.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 8bd162fcf2011d2ccce716564763e7f54f19ff69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97631811"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Säkerhets filter för att trimma resultat i Azure Kognitiv sökning

Du kan använda säkerhets filter för att trimma Sök resultat i Azure Kognitiv sökning baserat på användar identitet. Den här Sök rutinen kräver vanligt vis en jämförelse av identiteten hos vem som begär sökningen mot ett fält som innehåller de principer som har behörighet till dokumentet. När en matchning hittas har användaren eller huvud kontot (till exempel en grupp eller roll) åtkomst till dokumentet.

Ett sätt att uppnå säkerhets filtrering är genom en komplicerad disknutning av likhets uttryck: till exempel `Id eq 'id1' or Id eq 'id2'` , och så vidare. Den här metoden är fel känslig, svår att underhålla och i de fall där listan innehåller hundratals eller tusentals värden, saktar ned svars tiden för frågan med många sekunder. 

En enklare och snabbare metod är genom `search.in` funktionen. Om du använder `search.in(Id, 'id1, id2, ...')` i stället för ett likhets uttryck kan du vänta på under-sekundens svars tider.

Den här artikeln visar hur du utför säkerhets filtrering med hjälp av följande steg:
> [!div class="checklist"]
> * Skapa ett fält som innehåller huvud identifierare 
> * Skicka eller uppdatera befintliga dokument med relevanta huvud identifierare
> * Utfärda en Sök förfrågan med `search.in``filter`

>[!NOTE]
> Processen för att hämta huvud identifierarna omfattas inte av det här dokumentet. Du bör hämta den från din leverantör av identitets tjänster.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), en[Azure kognitiv sökning-tjänst](search-create-service-portal.md)och ett [index](search-what-is-an-index.md).  

## <a name="create-security-field"></a>Skapa säkerhets fält

Dokumenten måste innehålla ett fält som anger vilka grupper som har åtkomst. Den här informationen blir de filter villkor mot vilka dokument väljs eller avvisas från resultat uppsättningen som returneras till utfärdaren.
Vi antar att vi har ett index över säkra filer och att varje fil kan nås av en annan uppsättning användare.

1. Lägg till fält `group_ids` (du kan välja ett namn här) som `Collection(Edm.String)` . Kontrol lera att fältet har ett `filterable` attribut inställt på `true` så att Sök resultatet filtreras baserat på åtkomsten som användaren har. Om du till exempel ställer in `group_ids` fältet till `["group_id1, group_id2"]` för dokumentet med `file_name` "secured_file_b" har endast användare som tillhör grupp-ID: n "group_id1" eller "group_id2" Läs behörighet till filen.
   
   Kontrol lera att fältets `retrievable` attribut är inställt på `false` så att det inte returneras som en del av Sök förfrågan.

2. Lägg även till `file_id` och `file_name` fält för den här exempelens skull.  

    ```JSON
    {
        "name": "securedfiles",  
        "fields": [
            {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "file_name", "type": "Edm.String"},
            {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
        ]
    }
    ```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Skicka data till ditt index med hjälp av REST API
  
Skicka en HTTP POST-begäran till indexets URL-slutpunkt. Bröd texten i HTTP-begäran är ett JSON-objekt som innehåller de dokument som ska läggas till:

```http
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2020-06-30  
Content-Type: application/json
api-key: [admin key]
```

I begär ande texten anger du innehållet i dina dokument:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Om du behöver uppdatera ett befintligt dokument med listan över grupper kan du använda `merge` `mergeOrUpload` åtgärden eller:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

För fullständig information om hur du lägger till eller uppdaterar dokument kan du läsa [Redigera dokument](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="apply-the-security-filter"></a>Tillämpa säkerhets filtret

För att kunna rensa dokument baserat på `group_ids` åtkomst bör du utfärda en Sök fråga med ett `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filter, där group_id1 group_id2,... är de grupper som utfärdaren av Sök begär Anden tillhör.

Det här filtret matchar alla dokument som `group_ids` fältet innehåller ett av de identifierade identifierarna.
Fullständig information om hur du söker efter dokument med hjälp av Azure Kognitiv sökning kan du läsa [Sök dokument](/rest/api/searchservice/search-documents).
Observera att det här exemplet visar hur du söker efter dokument med en POST-begäran.

Utfärda HTTP POST-begäran:

```http
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2020-06-30
Content-Type: application/json  
api-key: [admin or query key]
```

Ange filtret i begär ande texten:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Du bör hämta dokumenten igen där `group_ids` innehåller antingen "group_id1" eller "group_id2". Med andra ord får du de dokument som begär ande utfärdaren har Läs behörighet för.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs ett mönster för att filtrera resultat baserat på användar identitet och `search.in()` funktion. Du kan använda den här funktionen för att skicka princip identifierare för den begär ande användaren att matcha mot huvud identifierare som är associerade med varje mål dokument. När en sökbegäran hanteras `search.in` filtrerar funktionen Sök Resultat för vilka ingen av användarens huvud namn har Läs behörighet. Huvud identifierarna kan representera saker som säkerhets grupper, roller eller till och med användarens egna identitet.

För ett alternativt mönster baserat på Active Directory eller om du vill gå tillbaka till andra säkerhetsfunktioner, se följande länkar.

* [Säkerhets filter för att trimma resultat med Active Directory identiteter](search-security-trimming-for-azure-search-with-aad.md)
* [Säkerhet i Azure Kognitiv sökning](search-security-overview.md)