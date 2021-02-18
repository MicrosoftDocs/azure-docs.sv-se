---
title: Generera miniatyrer med Media Encoder Standard .NET
description: Den här artikeln visar hur du använder .NET för att koda en till gång och skapa miniatyrer samtidigt med hjälp av Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 3efb5aa864f0940b7b5aa6a9131da9ce8bd732ee
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092814"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Så här genererar du miniatyrer med kodare standard med .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Du kan använda Media Encoder Standard för att generera en eller flera miniatyrer från indata-videon i bild fil formaten [JPEG](https://en.wikipedia.org/wiki/JPEG) eller [png](https://en.wikipedia.org/wiki/Portable_Network_Graphics) .

## <a name="recommended-reading-and-practice"></a>Rekommenderad läsning och praxis

Vi rekommenderar att du bekanta dig med anpassade [transformeringar genom att läsa hur du kodar med en anpassad transformering-.net](customize-encoder-presets-how-to.md).

## <a name="transform-code-example"></a>Exempel på Transformations kod

I kod exemplet nedan skapas bara en miniatyr bild.  Du bör ange följande parametrar:

- **Start** – den position i Indataporten där du vill börja skapa miniatyr bilder. Värdet kan vara i ISO 8601-format (t. ex. PT05S för att starta vid 5 sekunder) eller ett antal ramar (till exempel 10 för att starta vid den tionde ramen) eller ett relativt värde till Stream-varaktighet (till exempel 10% för att starta vid 10% av ström varaktigheten). Stöder också ett makro {Best}, som instruerar kodaren att välja den bästa miniatyren från de första sekunderna av videon och kommer bara att producera en miniatyr, oavsett vad andra inställningar är för steg och intervall. Standardvärdet är makro {Best}.
- **steg** – de intervall som miniatyrerna skapas med. Värdet kan vara i formatet ISO 8601 (till exempel PT05S för en bild var femte sekund) eller ett antal ramar (till exempel 30 för en bild var 30: e bild punkt) eller ett relativt värde till Stream-varaktighet (till exempel 10% för en bild var 10: e% av data strömmens varaktighet). Steg värde påverkar den första skapade miniatyr bilden, som kanske inte är exakt den som anges i Start tiden för förinställda transformeringar. Detta beror på Encoder, som försöker välja den bästa miniatyren mellan start tiden och steg positionen från start tiden som det första resultatet. Eftersom standardvärdet är 10% innebär det att om data strömmen har lång varaktighet, kan den första skapade miniatyr bilden vara långt bort från det som anges vid start tiden. Försök att välja ett rimligt värde för steg om den första miniatyren förväntas vara nära start tiden, eller ange värdet 1 om det bara finns en miniatyr som behövs vid start tiden.
- **intervall** – positionen i förhållande till förinställt Transformations start tid i Indataporten där du vill sluta skapa miniatyr bilder. Värdet kan vara i formatet ISO 8601 (till exempel PT5M30S för att stoppas vid 5 minuter och 30 sekunder från start tiden), eller ett antal ramar (till exempel 300 för att stoppa vid 300th-ramen från ramen vid start tillfället. Om det här värdet är 1, innebär det bara att en miniatyr visas vid start tiden, eller ett relativt värde till data Ströms varaktigheten (till exempel 50% för att stoppa vid hälften av ström varaktigheten från start tiden). Standardvärdet är 100%, vilket innebär att stoppa i slutet av data strömmen.
- **lager** – en samling med utgående bild lager som ska skapas av kodaren.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Nästa steg

[Generera miniatyrer med REST](media-services-generate-thumbnails-rest.md)
