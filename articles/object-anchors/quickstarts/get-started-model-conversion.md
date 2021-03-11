---
title: 'Snabb start: skapa en objekt ankar modell som ska användas i en app'
description: I den här snabb starten får du lära dig hur du skapar en modell för objekt ankare från en 3D-modell.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607908"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Snabb start: skapa en objekt fäster-modell från en 3D-modell

Objekt ankare i Azure är en hanterad moln tjänst som konverterar 3D-modeller till AI-modeller som möjliggör objekt medveten, mixad verklighets upplevelser för HoloLens. Den här snabb starten beskriver hur du skapar en objekt fäster-modell från en 3D-modell med C#/.NET-distribution. Core SDK.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett konto för objekt ankare
> * Omvandla en 3D-modell för att skapa en modell för objekt ankare

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

* En Windows-dator med <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git för Windows</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.Net Core 3,1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Skapa ett konto för objekt ankare

Först måste du skapa ett konto med tjänsten objekt ankare.

1. Gå till [Azure Portal](https://portal.azure.com/) och välj **skapa en resurs**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Skapa en ny resurs":::

2. Sök efter resursen **objekt ankare** .

   Sök efter "objekt ankare".

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Välj resurs för objekt ankare":::

   På resursen **objekt ankare** i Sök resultaten väljer du **skapa-> objekt ankare**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Skapa en resurs för objekt ankare":::

3. I dialog rutan **objekt fästar konto** :
    * Ange ett unikt resurs namn.
    * Välj den prenumeration som du vill koppla resursen till.
    * Skapa eller Använd en befintlig resurs grupp.
    * Välj den region som du vill att din resurs ska finnas i.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Ange resurs konto information för objekt ankare":::

    Välj **skapa** för att börja skapa resursen.

4. När resursen har skapats väljer du **Gå till resurs**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Gå till resurs":::

5. På sidan Översikt:

   Anteckna **konto domänen**. Du behöver det senare.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Kopiera konto domänen för din resurs för objekt ankare":::

   Anteckna **konto-ID**. Du behöver det senare.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Kopiera konto-ID för din resurs för objekt ankare":::

   Gå till sidan **åtkomst nycklar** och anteckna den **primära nyckeln**. Du behöver det senare.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Kopiera konto nyckeln för din resurs för objekt ankare":::

## <a name="get-the-sample-project"></a>Hämta exempel projektet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Konvertera en 3D-modell

Nu kan du gå vidare och konvertera din 3D-modell.

1. Öppna `quickstarts/conversion/Conversion.sln` i Visual Studio. Den här lösningen innehåller ett C#-konsol projekt.

2. Öppna `Configuration.cs` filen som finns i roten av projektet och Ersätt `set-me` värdena för följande fält:

   | Fält         | Beskrivning                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | **Konto domänen** för objekt ankare som skapats ovan. |
   | AccountId     | **Konto-ID** för det konto för objekt ankare som skapats ovan.     |
   | AccountKey    | **Primär nyckeln** för kontot för objekt ankare som skapats ovan     |

   Det finns fyra ytterligare fält som behöver verifieras:

    | Fält                    | Beskrivning                       |
    | ---                      | ---                               |
    | InputAssetPath           | Den absoluta sökvägen till en 3D-modell på den lokala datorn. Fil format som stöds är,,, `fbx` `ply` `obj` `glb` och `gltf` . |
    | AssetDimensionUnit       | Mått enheten för 3D-modellen. Alla mått enheter som stöds kan nås med `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` uppräkningen. |
    | Vikt                  | Riktningen på 3D-modellens gravitations riktning. Den här 3D-vektorn ger den nedåtriktade riktningen i koordinatsystemet i modellen. Om till exempel negativ `y` representerar den nedåtriktade riktningen i modellens 3D-utrymme blir det här värdet `Vector3(0.0f, -1.0f, 0.0f)` . |

3. Skapa och kör projektet för att ladda upp din 3D-modell, registrera ett nytt konverterings jobb med tjänsten och vänta tills det har slutförts. När jobbet har slutförts hämtas modell för objekt ankare bredvid den fil som anges i `InputAssetPath` . Du bör se något som liknar följande konsol utdata:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Anteckna **jobb-ID** för framtida bruk. Det kan vara användbart vid fel sökning eller fel sökning.

4. När jobbet har slutförts bör du se en fil med formatet `<Model-Filename-Without-Extension>_<JobID>.ou` på den angivna platsen för utdata. Om din 3D-modell till exempel är `chair.ply` och jobb-ID: t är, är `00000000-0000-0000-0000-000000000000` det fil namnet som tjänstens utdata kommer att vara `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett konto ankare och konverterat en 3D-modell för att skapa en modell för objekt ankare. Om du vill lära dig hur du integrerar modellen med objekt Ankarea SDK i appen för Mixad verklighet fortsätter du med någon av följande artiklar:

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Uniting HoloLens med MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
