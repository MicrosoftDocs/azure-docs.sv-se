---
title: Installera Remote Rendering-paketet för Unity
description: Förklarar hur du installerar klient-DLL-filerna för fjärrstyrning för Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047736"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installera Remote Rendering-paketet för Unity

Azure fjärrrendering använder ett Unity-paket för att kapsla in integrationen i Unity.
Det här paketet innehåller hela C#-API: et och alla binärfiler för plugin-programmet som krävs för att använda Azure-fjärrrendering med Unity.
I följande enhets namngivnings schema för paket kallas paketet **com. Microsoft. Azure. Remote-rendering**.

Du kan välja något av följande alternativ för att installera Unity-paketet.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Installera Remote rendering-paket med hjälp av funktions verktyget för Mixad verklighet

[Funktions verktyget för Mixad verklighet](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) ([hämtning](https://aka.ms/mrfeaturetool)) är ett verktyg som används för att integrera funktioner i Mixad verklighet i Unit-projekt. Paketet ingår inte i [plats för arr-exempel](https://github.com/Azure/azure-remote-rendering)och är inte tillgängligt i enhetens interna paket register.

Om du vill lägga till paketet i ett projekt måste du:
1. [Hämta funktions verktyget för Mixad verklighet](https://aka.ms/mrfeaturetool)
1. Följ [fullständiga instruktioner](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) om hur du använder verktyget.
1. På sidan **identifierings funktioner** avmarkerar du rutan för **Microsoft Azure Remote rendering** -paketet och väljer den version av paketet som du vill lägga till i projektet

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Om du vill uppdatera ditt lokala paket väljer du bara en nyare version från funktions verktyget för Mixad verklighet och installerar det. Uppdatering av paketet kan ibland leda till konsol fel. Om detta inträffar kan du försöka stänga och öppna projektet igen.

## <a name="install-remote-rendering-package-manually"></a>Installera Remote rendering Package manuellt

Om du vill installera Remote rendering-paketet manuellt måste du:

1. Ladda ned paketet från NPM-feed för Mixad verklighet på `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * Du kan antingen använda [NPM](https://www.npmjs.com/get-npm) och köra följande kommando för att ladda ned paketet till den aktuella mappen.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Du kan också använda PowerShell `Scripts/DownloadUnityPackages.ps1` -skriptet från [Azure-fjärrrendering GitHub-lagringsplatsen](https://github.com/Azure/azure-remote-rendering).
        * Redigera innehållet i `Scripts/unity_sample_dependencies.json` till
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Kör följande kommando i PowerShell för att ladda ned paketet till den angivna mål katalogen.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Installera det hämtade paketet](https://docs.unity3d.com/Manual/upm-ui-tarball.html) med enhets paket hanteraren.

Om du vill uppdatera ditt lokala paket kör du bara om respektive kommando som du använde och importerar om paketet. Uppdatering av paketet kan ibland leda till konsol fel. Om detta inträffar kan du försöka stänga och öppna projektet igen.

## <a name="unity-render-pipelines"></a>Unity Render-pipeline

Fjärrrendering fungerar med både **:::no-loc text="Universal render pipeline":::** och **:::no-loc text="Standard render pipeline":::** . Av prestanda skäl rekommenderas den universella åter givnings pipelinen.

Om du vill använda **:::no-loc text="Universal render pipeline":::** paketet måste dess paket installeras i Unity. Detta kan antingen göras i enhetens **paket hanterings** gränssnitt (paket namn **Universal RP**, version 7.3.1 eller senare) eller via `Packages/manifest.json` filen, enligt beskrivningen i [själv studie kursen om installation av Unity-projekt](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Nästa steg

* [Unity Game-objekt och-komponenter](objects-components.md)
* [Självstudie: Visa fjärrmodeller](../../tutorials/unity/view-remote-models/view-remote-models.md)