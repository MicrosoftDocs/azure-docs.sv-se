---
title: Förstå den digitala modellen med dubbla modeller | Microsoft Docs
description: Som utvecklare får du lära dig hur du använder DTDL-parsern för att validera modeller.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331795"
---
# <a name="understand-the-digital-twins-model-parser"></a>Förstå Digital Twins-modellparsern

Det digitala endefinierade definitions språket (DTDL) beskrivs i [DTDL-specifikationen](https://github.com/Azure/opendigitaltwins-dtdl). Användare kan använda ett _digitalt_ NuGet-paket för att validera och fråga en modell som definierats i flera filer.

## <a name="install-the-dtdl-model-parser"></a>Installera DTDL modell parser

Parsern är tillgänglig i NuGet.org med ID: [Microsoft. Azure. DigitalTwins. parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Om du vill installera parsern använder du en kompatibel NuGet-paket hanterare, till exempel en i Visual Studio eller i `dotnet` cli.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> Vid tidpunkten för skrivning är versionen av parsern `3.12.5` .

## <a name="use-the-parser-to-validate-a-model"></a>Använd parsern för att validera en modell

En modell kan bestå av ett eller flera gränssnitt som beskrivs i JSON-filer. Du kan använda parsern för att läsa in alla filer i en specifik mapp och använda parsern för att validera alla filer som helhet, inklusive alla referenser mellan filerna:

1. Skapa en `IEnumerable<string>` med en lista över alla modell innehåll:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Instansiera `ModelParser` och anropa `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Sök efter verifierings fel. Om parsern hittar eventuella fel, utlöses en `ParsingException` med en lista med fel:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. Granska `Model` . Om verifieringen lyckas kan du använda modellens parser-API för att kontrol lera modellen. Följande kodfragment visar hur du itererar över alla modeller som har parsats och visar de befintliga egenskaperna:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Nästa steg

API: t för modell tolkare som granskas i den här artikeln möjliggör många scenarier för att automatisera eller validera uppgifter som är beroende av DTDL-modeller. Du kan till exempel dynamiskt bygga ett gränssnitt från informationen i modellen.
