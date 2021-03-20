---
title: Projektions koncept
titleSuffix: Azure Cognitive Search
description: Spara och forma dina berikade data från AI-förloppet till ett kunskaps lager för användning i andra scenarier än fullständig texts ökning.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85565188"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Kunskaps lager "projektioner" i Azure Kognitiv sökning

Azure Kognitiv sökning möjliggör innehålls berikning genom inbyggda kognitiva kunskaper och anpassade kunskaper som en del av indexeringen. Berikningar skapa ny information där ingen tidigare fanns: extrahera information från bilder, identifiera sentiment, nyckel fraser och entiteter från text, för att ge några. Det kan också vara bättre att lägga till strukturer i en differentierad text. Alla dessa processer resulterar i dokument som gör full texts ökningen mer effektiv. I många fall är utförliga dokument användbara för andra scenarier än Sök, till exempel för kunskaps utvinning.

Projektioner, en komponent i [kunskaps lager](knowledge-store-concept-intro.md), är vyer av omfattande dokument som kan sparas i fysiska lagrings utrymmen för kunskaps utvinning. Med en projektion kan du "projicera" dina data i en form som överensstämmer med dina behov och bevarar relationer så att verktyg som Power BI kan läsa data utan ytterligare arbete.

Projektioner kan vara tabell, med data som lagras i rader och kolumner i Azure Table Storage eller JSON-objekt som lagras i Azure Blob Storage. Du kan definiera flera projektioner av dina data när de är omfattande. Flera projektioner är användbara när du vill att samma data formad annorlunda för enskilda användnings fall.

Kunskaps lagret stöder tre typer av projektioner:

+ **Tabeller**: för data som bäst visas som rader och kolumner kan du med tabell projektioner definiera en schematiserade-form eller projektion i Table Storage. Endast giltiga JSON-objekt kan projiceras som tabeller, det berikade dokumentet kan innehålla noder som inte heter JSON-objekt och när de projiceras, skapar du ett giltigt JSON-objekt med en formaren-kompetens eller infogad form.

+ **Objekt**: när du behöver en JSON-representation av dina data och hur de ska berikas, sparas objekt projektion som blobbar. Endast giltiga JSON-objekt kan projiceras som objekt, det berikade dokumentet kan innehålla noder som inte heter JSON-objekt och när de projiceras, skapar du ett giltigt JSON-objekt med en formaren-kompetens eller infogad form.

+ **Filer**: när du behöver spara de avbildningar som extraheras från dokumenten kan du spara de normaliserade avbildningarna i Blob Storage.

Om du vill se projektioner som definierats i kontext steg för steg hur du [skapar ett kunskaps lager i rest](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Projektions grupper

I vissa fall måste du projicera dina berikade data i olika former för att möta olika mål. I kunskaps lagret kan du definiera flera grupper av projektioner. Projektions grupper har följande nyckel egenskaper för ömsesidig exklusivitet och relateradhet.

### <a name="mutual-exclusivity"></a>Ömsesidigt exklusivitet

Allt innehåll som projiceras i en enda grupp är oberoende av data som projiceras i andra projektions grupper.
Detta oberoende innebär att du kan ha samma data formad annorlunda, men upprepas i varje projektions grupp.

### <a name="relatedness"></a>Relateradhet

Med projektions grupper kan du nu projicera dokumenten över projektions typer samtidigt som du bevarar relationerna mellan projektions typerna. Allt innehåll som projiceras inom en enskild projektions grupp bevarar relationer i data mellan projektions typer. I tabeller baseras relationer på en genererad nyckel och varje underordnad nod behåller en referens till den överordnade noden. Mellan typer (tabeller, objekt och filer) bevaras relationer när en enskild nod projiceras mellan olika typer. Anta till exempel ett scenario där du har ett dokument som innehåller bilder och text. Du kan projicera texten till tabeller eller objekt och bilderna till filer där tabellerna eller objekten har en kolumn/egenskap som innehåller fil-URL: en.

## <a name="input-shaping"></a>Inmatad form

Att hämta data i rätt form eller struktur är nyckeln till effektiv användning, vara en tabell eller objekt. Möjligheten att forma eller strukturera dina data baserat på hur du planerar åtkomst till och använder det är en viktig funktion som visas som **formaren** -kompetensen i färdigheter.  

Projektioner är enklare att definiera när du har ett objekt i det berikande trädet som matchar projektionens schema. Med den uppdaterade [formaren-kunskapen](cognitive-search-skill-shaper.md) kan du skapa ett objekt från olika noder i ditt anriknings träd och överordnade dem under en ny nod. Med **formaren** -kompetensen kan du definiera komplexa typer med kapslade objekt.

När du har definierat en ny form som innehåller alla element som du behöver för att projicera, kan du nu använda den här formen som källa för dina projektioner eller som inmatade i en annan färdighet.

## <a name="projection-slicing"></a>Segmentering av projektion

När du definierar en projektions grupp kan en enskild nod i ett anriknings träd segmenteras i flera relaterade tabeller eller objekt. Om du lägger till en projektion med en käll Sök väg som är underordnad en befintlig projektion kommer den underordnade noden att bli segmenterad från den överordnade noden och projiceras i den nya relaterade tabellen eller objektet. Med den här metoden kan du definiera en enskild nod i en formaren-färdighet som kan vara källan för alla dina projektioner.

## <a name="table-projections"></a>Tabell projektioner

Eftersom det gör det enklare att importera data, rekommenderar vi tabell prognoser för data utforskning med Power BI. Dessutom tillåter tabell projektioner att ändra kardinalitet mellan tabell relationer. 

Du kan projicera ett enskilt dokument i ditt index i flera tabeller och bevara relationerna. När du projicerar till flera tabeller, kommer hela formen att projiceras i varje tabell, om inte en underordnad nod är källan till en annan tabell inom samma grupp.

### <a name="defining-a-table-projection"></a>Definiera en tabell projektion

När du definierar en tabell projektion i `knowledgeStore` färdigheter-elementet, börjar du med att mappa en nod i berikande träd till tabell källan. Den här noden är vanligt vis resultatet av en **formaren** -färdighet som du har lagt till i listan med kunskaper för att skapa en speciell form som du behöver i projektet i tabeller. Den nod du väljer till projekt kan segmenteras till projekt i flera tabeller. Tabell definitionen är en lista över tabeller som du vill projicera.

Varje tabell kräver tre egenskaper:

+ tableName: namnet på tabellen i Azure Storage.

+ generatedKeyName: kolumn namnet för den nyckel som unikt identifierar den här raden.

+ Källa: noden från det berikande trädet som du kan använda för att kunna använda. Den här noden är vanligt vis resultatet av en formaren, men det kan vara utdata från någon av färdigheterna.

Här är ett exempel på tabell projektioner.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Som det visas i det här exemplet är nyckel fraser och entiteter modellerade i olika tabeller och kommer att innehålla en referens tillbaka till överordnad (MainTable) för varje rad.

## <a name="object-projections"></a>Objekt projektioner

Objekt projektioner är JSON-representationer av det berikande trädet som kan hämtas från vilken nod som helst. I många fall kan samma **formaren** -kompetens som skapar en tabell projektion användas för att generera en objekt projektion. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Att skapa en objekt projektion kräver några objektattribut:

+ storageContainer: BLOB-behållaren där objekten ska sparas
+ Källa: sökvägen till noden i det berikande trädet som är roten för projektionen

## <a name="file-projection"></a>Filprojektion

Filprojektioner liknar objekt projektioner och fungerar bara på `normalized_images` samlingen. På samma sätt som objekt projektioner, sparas fil prognoser i BLOB-behållaren med ett prefix för det Base64-kodade värdet för dokument-ID: t. Filprojektioner kan inte dela samma behållare som objekt projektioner och måste projiceras i en annan behållare.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Projekt livs cykel

Dina projektioner har en livs cykel som är kopplad till data källans källdata. När dina data uppdateras och indexeras om uppdateras dina projektioner med resultaten av de omfattande som säkerställer att dina projektioner är konsekventa med data i data källan. Projektionerna ärver den borttagnings princip som du har konfigurerat för ditt index. Projektioner tas inte bort när indexeraren eller själva Sök tjänsten tas bort.

## <a name="using-projections"></a>Använda projektioner

När indexeraren har körts kan du läsa de planerade data i de behållare eller tabeller som du har angett genom projektioner.

För analys är utforskning i Power BI lika enkelt som att ställa in Azure Table Storage som data källa. Du kan enkelt skapa en uppsättning visualiseringar på dina data med hjälp av relationerna i.

Om du behöver använda de utförliga data i en data vetenskaps pipeline kan du också [läsa in data från blobbar i en Pandas-DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Slutligen, om du behöver exportera data från kunskaps lagret, Azure Data Factory har kopplingar för att exportera data och landa den i valfri databas. 

## <a name="next-steps"></a>Nästa steg

I nästa steg ska du skapa ditt första kunskaps lager med hjälp av exempel data och instruktioner.

> [!div class="nextstepaction"]
> [Skapa ett kunskaps lager i rest](knowledge-store-create-rest.md).

En själv studie kurs som täcker avancerade projektions koncept som segmentering, infogad form och relationer, börjar med [definiera projektioner i ett kunskaps lager](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiera projektioner i ett kunskaps lager](knowledge-store-projections-examples.md)
