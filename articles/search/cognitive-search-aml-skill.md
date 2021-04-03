---
title: Anpassad AML-kunskap i färdighetsuppsättningar
titleSuffix: Azure Cognitive Search
description: Utöka funktionerna i Azure Kognitiv sökning färdighetsuppsättningar med Azure Machine Learning-modeller.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630283"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>AML-kunskaper i en pipeline för Azure Kognitiv sökning-anrikning

> [!IMPORTANT] 
> Den här kunskapen är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det finns för närvarande inget stöd för .NET SDK.

Med **AML** -kompetensen kan du utöka AI-anrikningen med en anpassad [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) -modell (AML). När en AML-modell har [tränats och distribuerats](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)integreras en **AML** -skicklighet i AI-anrikning.

Precis som inbyggda kunskaper har en **AML** -färdighet indata och utdata. Indata skickas till den distribuerade AML-tjänsten som ett JSON-objekt, som matar ut en JSON-nyttolast som ett svar tillsammans med status koden lyckades. Svaret förväntas ha de utdata som anges av din **AML** -färdighet. Andra svar betraktas som ett fel och inga berikningar utförs.

> [!NOTE]
> Indexeraren försöker igen två gånger för vissa standard-HTTP-statuskod som returneras från AML-tjänsten. HTTP-status koderna är:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Förutsättningar

* En [AML-arbetsyta](../machine-learning/concept-workspace.md)
* En [Azure Kubernetes-tjänst AML Compute Target](../machine-learning/concept-compute-target.md) på den här arbets ytan med en [distribuerad modell](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * [Beräknings målet ska ha SSL aktiverat](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). Azure Kognitiv sökning tillåter endast åtkomst till **https** -slutpunkter
  * Självsignerade certifikat kan inte användas.

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. Custom. AmlSkill

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga. Vilka parametrar du väljer att använda beror på vilken [autentisering din AML-tjänst kräver, om det finns några](#WhatSkillParametersToUse)

| Parameternamn | Beskrivning |
|--------------------|-------------|
| `uri` | (Krävs endast för [autentisering eller nyckeltoken](#WhatSkillParametersToUse)) [Beräknings-URI: n för den AML-tjänst](../machine-learning/how-to-consume-web-service.md) som _JSON_ -nyttolasten ska skickas till. Endast **https** URI-schemat är tillåtet. |
| `key` | (Krävs för [nyckel autentisering](#WhatSkillParametersToUse)) [Nyckeln för AML-tjänsten](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Krävs för [token-autentisering](#WhatSkillParametersToUse)). Azure Resource Manager resurs-ID för AML-tjänsten. Den ska vara i formatet prenumerationer/{GUID}/resourceGroups/{Resource-Group-name}/Microsoft. MachineLearningServices/arbetsytes/{arbetsyte-Name}/Services/{service_name}. |
| `region` | (Valfritt för [token-autentisering](#WhatSkillParametersToUse)). [Regionen](https://azure.microsoft.com/global-infrastructure/regions/) som AML-tjänsten har distribuerats i. |
| `timeout` | Valfritt Anger tids gränsen för http-klienten som gör API-anropet. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [varaktighets värde på ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Till exempel `PT60S` i 60 sekunder. Om inget värde anges väljs ett standardvärde på 30 sekunder. Tids gränsen kan anges till högst 230 sekunder och minst 1 sekund. |
| `degreeOfParallelism` | Valfritt Anger hur många anrop som indexeraren ska göra parallellt med den slut punkt som du har angett. Du kan minska det här värdet om slut punkten har misslyckats under för hög belastning på begäran, eller öka den om slut punkten kan ta emot fler begär Anden och du vill ha en ökning av prestandan för indexeraren.  Om inget värde anges används standardvärdet 5. DegreeOfParallelism kan anges till högst 10 och minst 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Vilka kunskaps parametrar som ska användas

Vilka AML kvalifikations parametrar som krävs beror på vilken autentisering din AML-tjänst använder, om det finns någon. AML Services tillhandahåller tre autentiseringsalternativ:

* [Nyckelbaserad autentisering](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). En statisk nyckel tillhandahålls för att autentisera Poäng förfrågningar från AML-färdigheter
  * Använd _URI_ och _nyckel_ parametrar
* [Tokenbaserad autentisering](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). AML-tjänsten [distribueras med hjälp av token-baserad autentisering](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). Den [hanterade identiteten](../active-directory/managed-identities-azure-resources/overview.md) för Azure kognitiv sökning-tjänsten beviljas [rollen läsare](../machine-learning/how-to-assign-roles.md) i AML-tjänstens arbets yta. AML-kompetensen använder sedan Azure Kognitiv sökning tjänstens hanterade identitet för att autentisera mot AML-tjänsten, utan att det krävs några statiska nycklar.
  * Använd parametern _resourceId_ .
  * Om Azure Kognitiv sökning-tjänsten finns i en annan region än arbets ytan AML använder du parametern _region_ för att ange den region som AML-tjänsten distribuerades i
* Ingen autentisering. Ingen autentisering krävs för att använda AML-tjänsten
  * Använd _URI_ -parametern

## <a name="skill-inputs"></a>Kompetens inmatningar

Det finns inga fördefinierade indata för den här kunskapen. Du kan välja ett eller flera fält som redan är tillgängliga när du kör den här färdighets körningen som indata och _JSON_ -nyttolasten som skickas till AML-tjänsten kommer att ha olika fält.

## <a name="skill-outputs"></a>Kunskaps utmatningar

Det finns inga fördefinierade utdata för den här kunskapen. Beroende på vilket svar din AML-tjänst kommer att returnera, lägger du till utdatakolumner så att de kan hämtas från _JSON_ -svaret.

## <a name="sample-definition"></a>Exempel definition

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Exempel-JSON-struktur

Den här _JSON_ -strukturen representerar den nytto last som ska skickas till din AML-tjänst. Den översta nivån i strukturen motsvarar de "namn" som anges i `inputs` avsnittet i kunskaps definitionen. Värdet för dessa fält kommer från `source` dessa fält (som kan komma från ett fält i dokumentet eller som kan komma från en annan kunskap)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Exempel-JSON-struktur för utdata

Utdata motsvarar svaret som returnerades från din AML-tjänst. AML-tjänsten bör bara returnera en _JSON_ -nyttolast (verifierad genom att titta i `Content-Type` svars huvudet) och ska vara ett objekt där fälten är berikare som matchar "namn" i `output` och vars värde betraktas som berikning.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Exempel definition för infogad utformning

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Infogad JSON-struktur för infogad utformning

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Inline utformning exempel på utdata JSON-struktur

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Fel fall
Förutom att dina AML är otillgängliga eller skickar ut status koder som inte lyckades, anses följande vara felaktiga fall:

* Om AML-tjänsten returnerar en status kod för lyckad status men svaret visar att det inte är det `application/json` , betraktas svaret som ogiltigt och inga berikningar utförs.
* Om AML-tjänsten returnerar ogiltig JSON

När AML-tjänsten inte är tillgänglig eller returnerar ett HTTP-fel läggs ett fel meddelande med tillgänglig information om HTTP-felet till i körnings historiken för Indexer.

## <a name="see-also"></a>Se även

+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Fel sökning av AML-tjänsten](../machine-learning/how-to-troubleshoot-deployment.md)