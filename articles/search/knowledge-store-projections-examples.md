---
title: Definiera projektioner i ett kunskaps lager
titleSuffix: Azure Cognitive Search
description: Exempel på vanliga mönster för att projicera dokument i kunskaps lagret för användning med Power BI eller Azure ML.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97509928"
---
# <a name="how-to-shape-and-export-enrichments"></a>Så här gör du för att forma och exportera omfattande

Projektioner är det fysiska uttrycket för berikade dokument i ett kunskaps lager. Effektiv användning av förrikade dokument kräver struktur. I den här artikeln får du utforska både struktur och relationer, lära dig hur du skapar projektions egenskaper, samt hur du relaterar data mellan de projekt typer som skapats. 

För att skapa en projektion, är data formad med antingen en [formaren-färdighet](cognitive-search-skill-shaper.md) för att skapa ett anpassat objekt eller använda den infogade formens syntax i en projektions definition. 

En data-form innehåller alla data som är avsedda för projekt, utformade som en hierarki med noder. Den här artikeln visar flera metoder för att forma data så att de kan projiceras i fysiska strukturer som bidrar till rapportering, analys eller underordnad bearbetning. 

Exemplen som presenteras i den här artikeln finns i det här [REST API exemplet](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), som du kan hämta och köra i en http-klient.

## <a name="introduction-to-projection-examples"></a>Introduktion till projekt exempel

Det finns tre typer av [projektioner](knowledge-store-projection-overview.md):

+ Tables
+ Objekt
+ Filer

Tabell projektioner lagras i Azure Table Storage. Objekt-och fil projektioner skrivs till Blob Storage, där objekt projektioner sparas som JSON-filer och kan innehålla innehåll från käll dokumentet samt eventuella kunskaps utmatningar eller berikningar. Pipelinen kan också extrahera binärfiler som bilder, dessa binärfiler projiceras som fil prognoser. När ett binärt objekt projiceras som en objekt projektion, sparas bara de metadata som är kopplade till den som en JSON-blob. 

För att förstå skärningen mellan data utformning och projektioner använder vi följande färdigheter som grund för att utforska olika konfigurationer. Den här färdigheter bearbetar RAW-avbildning och text innehåll. Projektioner definieras utifrån dokumentets innehåll och de utmatningarna i färdigheterna för de scenarier som önskas.

> [!IMPORTANT] 
> När du experimenterar med projektioner är det praktiskt att [ange egenskapen indexerare cache](search-howto-incremental-index.md) för att se till att kostnads kontrollen är inställd. Genom att redigera projektioner leder du till hela dokumentet som berikas igen om indexeraren cache inte har angetts. När cachen har angetts och endast projektionerna har uppdaterats, resulterar färdigheter-körningar för tidigare berikade dokument inte till några nya Cognitive Services avgifter.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Med hjälp av den här färdigheter, med sitt null `knowledgeStore` som grund, fyller vårt första exempel i `knowledgeStore` objektet, konfigurerat med projektioner som skapar tabell data strukturer som vi kan använda i andra scenarier. 

## <a name="projecting-to-tables"></a>Projicera till tabeller

Att projicera till tabeller i Azure Storage är användbart för rapportering och analys med hjälp av verktyg som Power BI. Power BI kan läsa från tabeller och identifiera relationer baserat på de nycklar som genereras under projektionen. Om du försöker bygga en instrument panel kan du förenkla den aktiviteten genom att ha relaterade data. 

Nu ska vi bygga en instrument panel för att visualisera viktiga fraser som extraherats från dokument som ett Word-moln. Om du vill skapa en rätt data struktur lägger du till en formaren-färdighet till färdigheter för att skapa en anpassad form som innehåller dokument information och viktiga fraser. Den anpassade figuren kommer att anropas `pbiShape` på `document` rotnoden.

> [!NOTE] 
> Tabell projektioner är Azure Storage tabeller som styrs av de lagrings gränser som Azure Storage. Mer information finns i [tabell lagrings gränser](/rest/api/storageservices/understanding-the-table-service-data-model). Det är praktiskt att veta att enhetens storlek inte kan överstiga 1 MB och att en enskild egenskap inte får vara större än 64 KB. Dessa begränsningar gör tabeller till en lämplig lösning för att lagra ett stort antal små entiteter.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Använda en formaren-färdighet för att skapa en anpassad form

Skapa en anpassad form som du kan projicera i Table Storage. Utan en anpassad form kan en projektion bara referera till en enda nod (en projektion per utdata). När du skapar en egen form sammanställs olika element i en ny logisk helhet som kan projiceras som en enskild tabell, eller segmenteras och distribueras i en samling tabeller. 

I det här exemplet kombinerar den anpassade formen metadata och identifierade entiteter och nyckel fraser. Objektet anropas `pbiShape` och är överordnat under `/document` . 

> [!IMPORTANT] 
> Ett avsikt att forma är att se till att alla anriknings noder uttrycks i välformulerad JSON, vilket krävs för att projicera i kunskaps lagret. Detta gäller särskilt när ett anriknings träd innehåller noder som inte är välformulerade JSON (till exempel när en anrikning är överordnad till en primitiv som en sträng).
>
> Lägg märke till de sista två noderna `KeyPhrases` och `Entities` . Dessa omsluts till ett giltigt JSON-objekt med `sourceContext` . Detta krävs som `keyphrases` och `entities` är berikade med primitiver och måste konverteras till giltig JSON innan de kan projiceras.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Lägg till formaren-kompetensen ovan i färdigheter. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Nu när vi har alla data som behövs för att projicera till tabeller, uppdaterar du knowledgeStore-objektet med tabell definitionerna. I det här exemplet har vi tre tabeller som definieras genom att ange `tableName` `source` egenskaperna, och `generatedKeyName` .

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Du kan bearbeta ditt arbete genom att följa dessa steg:

1. Ange ```storageConnectionString``` egenskapen till en giltig anslutnings sträng för lagrings kontot för generell användning i v2.  

1. Uppdatera färdigheter genom att utfärda begäran om placering.

1. När du har uppdaterat färdigheter kör du indexeraren. 

Nu har du en arbets projektion med tre tabeller. Om du importerar dessa tabeller till Power BI bör Power BI identifiera relationerna automatiskt.

Innan du går vidare till nästa exempel kan vi gå tillbaka till aspekter av tabell projektionen för att förstå Mechanics av segmentering och relaterade data.

### <a name="slicing"></a>Slicing 

Segmentering är en teknik som delar upp en helt sammanslagen form i komponent delar. Resultatet består av separata men relaterade tabeller som du kan arbeta med individuellt.

I exemplet `pbiShape` är den konsoliderade figuren (eller beriknings-noden). I definitions definitionen för projektion `pbiShape` är det segmenterat i ytterligare tabeller, vilket gör att du kan hämta delar av formen ```keyPhrases``` och ```Entities``` . I Power BI är detta användbart eftersom flera entiteter och-fraser är associerade med varje dokument och du får fler insikter om du kan se entiteter och diskussions fraser som kategoriserade data.

Segmentering genererar implicit en relation mellan överordnade och underordnade tabeller med hjälp av ```generatedKeyName``` i den överordnade tabellen för att skapa en kolumn med samma namn i den underordnade tabellen. 

### <a name="naming-relationships"></a>Namnge relationer

```generatedKeyName```Egenskaperna och ```referenceKeyName``` används för att relatera data över tabeller eller till och med mellan olika typer av projektioner. Varje rad i den underordnade tabellen/projektionen har en egenskap som pekar tillbaka till överordnad. Namnet på kolumnen eller egenskapen i det underordnade ```referenceKeyName``` objektet är från överordnad. När ```referenceKeyName``` inte har angetts, använder tjänsten den ```generatedKeyName``` från överordnad som standard. 

Power BI använder dessa genererade nycklar för att identifiera relationer i tabellerna. Om du behöver kolumnen i den underordnade tabellen med namnet annorlunda, anger du ```referenceKeyName``` egenskapen för den överordnade tabellen. Ett exempel är att ställa in ```generatedKeyName``` as-ID: t i pbiDocument-tabellen och ```referenceKeyName``` som DocumentID. Detta resulterar i kolumnen i tabellerna pbiEntities och pbiKeyPhrases som innehåller dokument-ID: t som heter DocumentID.

## <a name="projecting-to-objects"></a>Projicera till objekt

Objekt projektioner har inte samma begränsningar som tabell projektioner och passar bättre för att projicera stora dokument. I det här exemplet skickas hela dokumentet som en objekt projektion. Objekt projektioner är begränsade till en enda projektion i en behållare och kan inte segmenteras.

Använd matrisen i projektionerna för att definiera en objekt projektion ```objects``` . Du kan skapa en ny form med formaren-kompetensen eller använda infogad form av objekt projektion. Medan tabell exemplet demonstrerade metoden för att skapa en form och segmentering visar det här exemplet användningen av infogad form. 

Inline Forming är möjligheten att skapa en ny form i definitionen av indata till en projektion. Infogad Forming skapar ett anonymt objekt som är identiskt med det som en formaren-färdighet skulle producera (i vårt fall `pbiShape` ). Infogad utformning är användbart om du definierar en form som du inte planerar att återanvända.

Projektions-egenskapen är en matris. I det här exemplet läggs en ny projektion-instans till i matrisen, där knowledgeStore-definitionen innehåller infogade projektioner. När du använder infogade projektioner kan du utelämna formaren-kompetensen.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Projicera till fil

Filprojektioner är bilder som antingen extraheras från käll dokumentet eller utmatningar av berikning som kan projiceras från beriknings processen. Fil projektioner, som liknar objekt projektioner, implementeras som blobbar i Azure Storage och innehåller avbildningen. 

Om du vill generera en filprojektion använder du `files` matrisen i objektet projektion. I det här exemplet projekterar vi alla avbildningar som extraherats från dokumentet till en behållare som kallas `samplefile` .

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projicera till flera typer

Ett mer komplext scenario kan kräva att du projicerar innehåll mellan olika typer av projektioner. Om du till exempel behöver projicera vissa data som nyckel fraser och entiteter till tabeller, sparar du OCR-resultatet av text-och layout-text som objekt och sedan projicerar du bilderna som filer. 

I det här exemplet uppdateras färdigheter med följande ändringar:

1. Skapa en tabell med en rad för varje dokument.
1. Skapa en tabell som är relaterad till dokument tabellen med varje nyckel fras som identifieras som en rad i den här tabellen.
1. Skapa en tabell som är relaterad till dokument tabellen med varje entitet som identifieras som en rad i den här tabellen.
1. Skapa en objekt projektion med layout texten för varje bild.
1. Skapa en filprojektion och projicera varje extraherad avbildning.
1. Skapa en kors referens tabell som innehåller referenser till dokument tabellen, projektion av objekt med layout-text och filprojektion.

De här ändringarna återspeglas i knowledgeStore-definitionen. 

### <a name="shape-data-for-cross-projection"></a>Formdata för kors projektion

Börja med att lägga till en ny formaren-färdighet som skapar ett formad objekt som kallas för att få de former som behövs för dessa projektioner `crossProjection` . 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definiera tabell-, objekt-och fil projektioner

Från det konsoliderade crossProjection-objektet, segmentera objektet i flera tabeller, fånga OCR-utdata som blobbar och spara sedan bilden som filer (även i Blob Storage).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                    {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                ]
            }
        ]
    }
```

Objekt projektioner kräver ett behållar namn för varje projektion, objekt projektioner eller filprojektioner kan inte dela en behållare. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relationer mellan tabell-, objekt-och fil projektioner

I det här exemplet markeras även en annan funktion i projektioner. Genom att definiera flera typer av projektioner inom samma projekt objekt, finns det en relation som uttrycks i och över de olika typerna (tabeller, objekt, filer). På så sätt kan du börja med en tabell rad för ett dokument och söka efter all OCR-text för bilderna i dokumentet i objektet projektion. 

Om du inte vill att data ska vara relaterade definierar du projektionerna i olika projektions objekt. Följande fragment resulterar till exempel i tabellerna som är relaterade, men utan relationer mellan tabellerna och objektet (OCR-text). 

Projektions grupper är användbara när du vill projicera samma data i olika former för olika behov. Till exempel en projektions grupp för Power BI instrument panelen och en annan projektions grupp för att samla in data som används för att träna en maskin inlärnings modell som omsluts i en anpassad färdighet.

När du skapar projektioner av olika typer genereras fil-och objekt projektioner först och Sök vägarna läggs till i tabellerna.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Vanliga problem

När du definierar en projektion finns det några vanliga problem som kan orsaka oväntade resultat. Sök efter de här problemen om utdata i kunskaps lagret inte är det du förväntar dig.

+ Det går inte att forma sträng berikade i giltig JSON. När strängar är berikade, till exempel `merged_content` omfattande med viktiga fraser, representeras den här egenskapen som en underordnad i `merged_content` berikande trädet. Standard representationen är inte giltig JSON. Vid projektions tiden ser du till att transformera anrikningen till ett giltigt JSON-objekt med ett namn och ett värde.

+ Utelämna i ```/*``` slutet av en käll Sök väg. Om källan för en projektion är `/document/pbiShape/keyPhrases` , projiceras nyckel fraserna som ett enda objekt/rad. Ange i stället käll Sök vägen till `/document/pbiShape/keyPhrases/*` för att ge en enskild rad eller ett objekt för var och en av de viktigaste fraserna.

+ Syntaxfel för sökväg. Sök vägs väljare är Skift läges känsliga och kan leda till saknade indatatyper om du inte använder det exakta fallet för väljaren.

## <a name="next-steps"></a>Nästa steg

I exemplen i den här artikeln demonstreras vanliga mönster för hur du skapar projektioner. Nu när du har en god förståelse för koncepten är du bättre utrustad med att bygga projektioner för ditt specifika scenario.

När du utforskar nya funktioner bör du överväga stegvisa berikande åtgärder som nästa steg. Stegvis anrikning baseras på cachelagring, som gör att du kan återanvända alla berikande som inte på annat sätt påverkas av en färdigheter modifiering. Detta är särskilt användbart för pipelines som inkluderar OCR-och bild analys.

> [!div class="nextstepaction"]
> [Introduktion till stegvis anrikning och cachelagring](cognitive-search-incremental-indexing-conceptual.md)

För en översikt över projektioner, lär dig mer om funktioner som grupper och segmentering och hur du [definierar dem i en färdigheter](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projektioner i ett kunskaps lager](knowledge-store-projection-overview.md)