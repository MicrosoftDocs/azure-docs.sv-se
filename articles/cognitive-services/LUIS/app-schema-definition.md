---
title: App schema definition
description: LUIS-appen representeras antingen i `.json` eller `.lu` och innehåller alla avsikter, entiteter, exempel yttranden, funktioner och inställningar.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327331"
---
# <a name="app-schema-definition"></a>App schema definition

LUIS-appen representeras antingen i `.json` eller `.lu` och innehåller alla avsikter, entiteter, exempel yttranden, funktioner och inställningar.

## <a name="format"></a>Format

När du importerar och exporterar appen väljer du antingen `.json` eller `.lu` .

|Format|Information|
|--|--|
|`.json`| Standard programmerings format|
|`.lu`|Stöds av bot Frameworks [bot Builder verktyg](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md).|

## <a name="version-7x"></a>Version 7. x

* Om du flyttar till version 7. x visas entiteterna som kapslade enheter för maskin inlärning.
* Stöd för redigering av kapslade enheter för maskin inlärning med `enableNestedChildren` egenskap i följande redigerings-API: er:
    * [Lägg till etikett](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Lägg till kommando etikett](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Granska etiketter](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Föreslå slut punkts frågor för entiteter](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Föreslå slut punkts frågor för avsikter](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| brevpost                  | Kommentar                              |
|--------------------------|--------------------------------------|
| "hierarkiskt": [],     | Föråldrad använder du [enheter för maskin inlärning](luis-concept-entity-types.md).   |
| "sammansatta": [],        | Föråldrad använder du [enheter för maskin inlärning](luis-concept-entity-types.md). [Sammansatt entitetsreferens](reference-entity-composite.md) . |
| "closedLists": [],       | [Lista entitets](reference-entity-list.md) -referens, används främst som funktioner till entiteter.    |
| "versionId": "0,1",      | Version av en LUIS-app.|
| "namn": "exempel-App",   | Namnet på LUIS-appen. |
| "DESC": "",              | Valfri beskrivning av LUIS-appen.  |
| "kultur": "en-US",      | Appens [språk](luis-language-support.md) , påverkar underliggande funktioner, till exempel färdiga entiteter, maskin inlärning och tokenizer.  |
| "tokenizerVersion": "1.0.0", | [Tokenizer](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Entiteten Pattern.any](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [Entitet för reguljära uttryck](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [Fras listor (funktion)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  Föråldrad använder du [enheter för maskin inlärning](luis-concept-entity-types.md). |
| "mönster": [],          |  [Mönster förbättrar förutsägelse noggrannhet](luis-concept-patterns.md) med [mönstermatchningssyntax](reference-pattern-syntax.md)   |
| "Inställningar": []           | [Appinställningar](luis-reference-application-settings.md)|

## <a name="version-6x"></a>Version 6. x

* Flyttar till version 6. x, Använd den nya [enheten för maskin inlärning](reference-entity-machine-learned-entity.md) för att representera dina entiteter.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Version 4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Nästa steg

* Migrera till [v3 redigerings-API: er](luis-migration-authoring-entities.md)