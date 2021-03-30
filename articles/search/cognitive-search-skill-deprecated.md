---
title: Föråldrade kognitiva färdigheter
titleSuffix: Azure Cognitive Search
description: Den här sidan innehåller en lista med kognitiva kunskaper som betraktas som inaktuella och som inte stöds i nära framtid i Azure Kognitiv sökning färdighetsuppsättningar.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936103"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Föråldrade kognitiva kunskaper i Azure Kognitiv sökning

Det här dokumentet beskriver kognitiva kunskaper som betraktas som föråldrade. Använd följande guide för innehållet:

* Kunskaps namn: namnet på den färdighet som ska vara inaktuell, mappas till @odata.type attributet.
* Senaste tillgängliga API-version: den senaste versionen av Azure Kognitiv sökning offentliga API: et som färdighetsuppsättningar som innehåller motsvarande föråldrade färdighet kan skapas/uppdateras.
* Supportens slut: den sista dagen efter vilken motsvarande färdighet betraktas som ej stöds. Tidigare skapade färdighetsuppsättningar bör fortfarande fortsätta att fungera, men användarna rekommenderas att migrera bort från en föråldrad färdighet.
* Rekommendationer: sökväg för migrering framåt för att använda en färdighet som stöds. Användarna uppmanas att följa rekommendationerna för att fortsätta att få support.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft. färdigheter. text. NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Senaste tillgängliga API-version

2017-11-11-Preview

### <a name="end-of-support"></a>Slut på support

15 februari 2019

### <a name="recommendations"></a>Rekommendationer 

Använd [Microsoft. skicklighets. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) i stället. Den innehåller de flesta funktioner i NamedEntityRecognitionSkill med högre kvalitet. Den innehåller också utförlig information i komplexa utdatakolumner.

Om du vill migrera till [kunskap om enhets igenkänning](cognitive-search-skill-entity-recognition.md)måste du utföra en eller flera av följande ändringar i din kunskaps definition. Du kan uppdatera kunskaps definitionen med [Update färdigheter-API: et](/rest/api/searchservice/update-skillset).

> [!NOTE]
> För närvarande stöds inte säkerhets Poäng som ett begrepp. `minimumPrecision`Parametern finns i `EntityRecognitionSkill` för framtida användning och bakåtkompatibilitet.

1. *(Krävs)* Ändra `@odata.type` från `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` till `"#Microsoft.Skills.Text.EntityRecognitionSkill"` .

2. *(Valfritt)* Om du använder utdata använder du i `entities` `namedEntities` stället det komplexa samlings resultatet `EntityRecognitionSkill` . Du kan använda `targetName` i kunskaps definitionen för att mappa den till en anteckning som kallas `entities` .

3. *(Valfritt)* Om du inte uttryckligen anger `categories` , `EntityRecognitionSkill` kan returnera olika typer av kategorier förutom de som stöds av `NamedEntityRecognitionSkill` . Om det här beteendet är oönskat, måste du uttryckligen ange `categories` parametern till `["Person", "Location", "Organization"]` .

    _Exempel på migrations definitioner_

    * Enkel migrering

        _Framför NamedEntityRecognition kompetens definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _När EntityRecognition kompetens definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * En något komplicerad migrering

        _Framför NamedEntityRecognition kompetens definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _När EntityRecognition kompetens definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Kompetens för enhets igenkänning](cognitive-search-skill-entity-recognition.md)