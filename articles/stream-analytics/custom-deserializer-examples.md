---
title: Läs in inläsningar i alla format med anpassade .NET-deserialiserare i Azure Stream Analytics
description: I den här artikeln beskrivs Serialization-formatet och de gränssnitt som definierar anpassade .NET-deserialiserare för Azure Stream Analytics moln-och Edge-jobb.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 140a836882ad3abe048047120e4fe1ebc0a3067c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018164"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Läs in inläsningar i alla format med anpassade .NET-deserialiserare

Med anpassade deserialiserade .NET kan ditt Azure Stream Analytics jobb läsa data från format utanför de tre [inbyggda data formaten](stream-analytics-parsing-json.md). I den här artikeln beskrivs Serialization-formatet och de gränssnitt som definierar anpassade deserialiserare i .NET för Azure Stream Analytics moln-och Edge-jobb. Det finns också exempel på deserialiserare för Protocol buffer och CSV-format.

## <a name="net-custom-deserializer"></a>Anpassad deserialiserare för .NET

Följande kod exempel är de gränssnitt som definierar den anpassade deserialiseraren och implementera `StreamDeserializer<T>` .

`UserDefinedOperator` är Bask Lassen för alla anpassade streaming-operatörer. Den initieras `StreamingContext` , som innehåller en kontext som omfattar mekanismen för att publicera diagnostik för vilken du behöver felsöka eventuella problem med deserialiseraren.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Följande kodfragment är deserialiseringen för strömmande data. 

Överhoppade fel ska genereras med hjälp av `IStreamingDiagnostics` `UserDefinedOperator` initierings metoden. Alla undantag behandlas som fel och deserialiseraren kommer att återskapas. Efter ett visst antal fel, kommer jobbet att hamna i fel tillstånd.

`StreamDeserializer<T>` deserialiserar en data ström till objekt av typen `T` . Följande villkor måste vara uppfyllda:

1. T är en klass eller en struktur.
1. Alla offentliga fält i T är antingen
    1. En av [SByte-, byte-, Short-, ushort-, int-, uint-, Long-, DateTime-, String-, Float-, Double]-eller deras null-motsvarigheter.
    1. En annan struct eller klass som följer samma regler.
    1. Matris av typen `T2` som följer samma regler.
    1. IList `T2` där T2 följer samma regler.
    1. Saknar rekursiva typer.

Parametern `stream` är den data ström som innehåller det serialiserade objektet. `Deserialize` Returnerar en instans samling `T` .

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` innehåller en kontext som innehåller mekanismen för att publicera diagnostik för användar operatörer.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` är diagnostiken för användardefinierade operatorer, inklusive serialiserare, deserialiserare och användardefinierade funktioner.

`WriteError` skriver ett fel meddelande till resurs loggar och skickar felet till diagnostik.

`briefMessage` är ett kort fel meddelande. Det här meddelandet visas i diagnostik och används av produkt teamet för fel sökning. Ta inte med känslig information och se till att meddelandet innehåller färre än 200 tecken

`detailedMessage` är ett detaljerat fel meddelande som bara läggs till i dina resurs loggar i din lagring. Det här meddelandet bör innehålla färre än 2000 tecken.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Exempel på deserialiserare

I det här avsnittet visas hur du skriver anpassade deserialiserare för protobuf och CSV. Fler exempel, till exempel AVRO-format för Event Hub-insamling, finns [på Azure Stream Analytics på GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Protobuf-format (Protocol buffer)

Detta är ett exempel som använder Protocol buffer format.

Antag följande Protocol buffer definition.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

`protoc.exe`Att köra från **Google. protobuf. Tools** -NuGet genererar en. CS-fil med definitionen. Den genererade filen visas inte här. Du måste se till att den version av protobuf-NuGet som du använder i Stream Analytics-projektet matchar den protobuf-version som användes för att generera indatamängden. 

Följande kodfragment är deserialiserings-implementeringen som förutsätter att den genererade filen ingår i projektet. Den här implementeringen är bara en tunn omslutning över den genererade filen.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Följande kodfragment är en enkel CSV-deserialiserare som också visar spridnings fel.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Serialization-format för REST API: er

Alla Stream Analytics-indatatyper har ett **serialiserings-format**. För ytterligare information om indata-alternativ, se REST API dokumentation om [indata](/rest/api/streamanalytics/2016-03-01/inputs) .

Följande JavaScript-kod är ett exempel på formatet .NET deserialiserare när du använder REST API:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` ska vara en klass som implementerar `StreamDeserializer<T>` . Detta beskrivs i följande avsnitt.

## <a name="region-support"></a>Stöd för regioner

Den här funktionen är tillgänglig i följande regioner:

* USA, västra centrala
* Europa, norra
* East US
* USA, västra
* USA, östra 2
* Europa, västra

Du kan [begära stöd](https://aka.ms/ccodereqregion) för ytterligare regioner.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>När kommer den här funktionen vara tillgänglig i alla Azure-regioner?

Den här funktionen är tillgänglig i [6 regioner](#region-support). Om du är intresse rad av att använda den här funktionen i en annan region kan du [skicka en begäran](https://aka.ms/ccodereqregion). Support för alla Azure-regioner finns i översikten.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Kan jag få åtkomst till MetadataPropertyValue från mina indata som liknar funktionen GetMetadataPropertyValue?

Den här funktionen stöds inte. Om du behöver den här funktionen kan du rösta på den här begäran på [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Kan jag dela min avserialiserings implementering med-gruppen så att andra kan dra nytta av dem?

När du har implementerat deserialiseraren kan du hjälpa andra genom att dela den med communityn. Skicka in din kod till [Azure Stream Analytics GitHub-lagrings platsen](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Vilka är de andra begränsningarna med att använda anpassade avserialiseringar i Stream Analytics?

Om du har angett protobuf-format med ett schema som innehåller `MapField` typ kommer du inte att kunna implementera en anpassad deserialiserare. Dessutom stöder inte anpassade deserialiserade exempel data eller för hands versions data. 

## <a name="next-steps"></a>Nästa steg

* [.NET-anpassade deserialiserare för Azure Stream Analytics moln jobb](custom-deserializer.md)
