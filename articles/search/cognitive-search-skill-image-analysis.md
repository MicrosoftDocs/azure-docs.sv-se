---
title: Inlärnings kunskap för bild analys
titleSuffix: Azure Cognitive Search
description: Extrahera semantisk text via bild analys med hjälp av funktionen för inlärning av bild analys i en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 69b84a3edb606ed99b6aaca7db5ad0e57124f1b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948943"
---
# <a name="image-analysis-cognitive-skill"></a>Inlärnings kunskap för bild analys

I **bild analysens** kunskap extraheras en omfattande uppsättning visuella funktioner baserat på avbildningens innehåll. Du kan till exempel generera en under text från en bild, generera taggar eller identifiera kändisar och landmärken. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [visuellt innehåll](../cognitive-services/computer-vision/overview.md) i Cognitive Services. 

> [!NOTE]
> Små volymer (under 20 transaktioner) kan utföras kostnads fritt i Azure Kognitiv sökning, men större arbets belastningar kräver att du [kopplar en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. vision. ImageAnalysisSkill 

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `defaultLanguageCode` |  En sträng som anger det språk som ska returneras. Tjänsten returnerar igenkännings resultat på ett angivet språk. Om den här parametern inte anges är standardvärdet "en". <br/><br/>Språk som stöds: <br/>*en* – engelska (standard) <br/> *es* – spanska <br/> *Ja* – japanska <br/> *PT* -portugisiska <br/> *zh* – förenklad kinesiska|
| `visualFeatures` |    En sträng mat ris som anger vilka visuella funktions typer som ska returneras. Giltiga typer av visuella funktioner är:  <ul><li>*vuxen* – identifierar om bilden är pornografiskt (visar nakenhet eller en kön Act) eller är fullständig (visar extrema våld eller blod). Sexuellt innehåll (även kallat vågat-innehåll) identifieras också.</li><li>*varumärken* – identifierar olika varumärken inom en avbildning, inklusive den ungefärliga platsen. Den visuella funktionen *varumärken* är endast tillgänglig på engelska.</li><li> *Kategorier* – kategoriserar bild innehåll enligt en taxonomi som definierats i Cognitive Services [visuellt innehåll-dokumentationen](../cognitive-services/computer-vision/category-taxonomy.md). </li><li>*Beskrivning* – beskriver bild innehållet med en hel mening i språk som stöds.</li><li>*ansikten* – identifierar om det finns ansikten. Om det finns genererar koordinater, kön och ålder.</li><li>  *objekt* – identifierar olika objekt i en bild, inklusive den ungefärliga platsen. Den visuella *objekt* funktionen är endast tillgänglig på engelska.</li><li> *taggar* – Taggar avbildningen med en detaljerad lista över ord relaterade till bild innehållet.</li></ul> Namn på visuella funktioner är Skift läges känsliga. Observera att de visuella funktionerna *färg* och *imageType* har föråldrats, men den här funktionen kan fortfarande nås via en [anpassad färdighet](./cognitive-search-custom-skill-interface.md).|
| `details` | En sträng mat ris som visar vilken datorspecifik information som ska returneras. Giltiga typer av visuella funktioner är: <ul><li>*kändisar* – identifierar kändisar om det identifierats i avbildningen.</li><li>*landmärken* – identifierar landmärken om de upptäcks i bilden. </li></ul> |

## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn      | Beskrivning                                          |
|---------------|------------------------------------------------------|
| `image`         | Komplex typ. För närvarande fungerar det bara med fältet "/Document/normalized_images" som skapas av Azure Blob-indexeraren när ```imageAction``` har angetts till ett annat värde än ```none``` . Se [exemplet](#sample-output) för mer information.|



##  <a name="sample-skill-definition"></a>Exempel på kunskaps definition

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Exempel index (endast för fälten kategorier, beskrivning, ansikten och taggar)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Exempel på fält mappning av utdata (för indexet ovan)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Variation av mappningar av utdatakolumner (kapslade egenskaper)

Du kan definiera mappningar för utdatakolumner till lågnivå egenskaper, till exempel bara landmärken eller kändisar. I det här fallet kontrollerar du att index schemat har ett fält som innehåller landmärken specifikt.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Exempelindata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Exempelutdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Fel fall
I följande fel fall extraheras inga element.

| Felkod | Beskrivning |
|------------|-------------|
| `NotSupportedLanguage` | Det tillhandahållna språket stöds inte. |
| `InvalidImageUrl` | Bild-URL: en är felaktigt formaterad eller inte tillgänglig.|
| `InvalidImageFormat` | Indata är inte en giltig bild. |
| `InvalidImageSize` | Indata-bilden är för stor. |
| `NotSupportedVisualFeature`  | Den angivna funktions typen är inte giltig. |
| `NotSupportedImage` | Bild som inte stöds, t. ex. barn pornografi. |
| `InvalidDetails` | En domänbaserad modell som inte stöds. |

Om du får det fel som liknar `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"` kontrollerar du sökvägen. Både kändisar och landmärken är egenskaper under `detail` .

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](/rest/api/searchservice/create-indexer)