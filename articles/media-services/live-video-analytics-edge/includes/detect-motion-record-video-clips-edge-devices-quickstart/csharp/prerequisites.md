---
ms.openlocfilehash: d3a65122772a7cb89de6d3f7f27c2f49d3f74c98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633360"
---
* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.
  > [!NOTE]
  > Du behöver en Azure-prenumeration med behörigheter för att skapa tjänstens huvud namn (**ägar rollen** ger detta). Om du inte har rätt behörighet kan du kontakta konto administratören för att ge dig rätt behörigheter. 
* [Visual Studio Code](https://code.visualstudio.com/)med följande tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
        > [!TIP]
        > När du installerar Azure IoT-verktyg kan du uppmanas att installera Docker. Du kan ignorera prompten.
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Om du inte har slutfört snabb starten [identifiera rörelse-och genererar händelser](../../../detect-motion-emit-events-quickstart.md) följer du dessa steg:
     1. [Ställa in Azure-resurser](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Ställt in din utvecklingsmiljö](../../../detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Skapa och distribuera distributions manifestet för IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Förbereda övervakning av händelser](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Om du stöter på problem med Azure-resurser som skapas, kan du läsa vår **[fel söknings guide](../../../troubleshoot-how-to.md#common-error-resolutions)** för att lösa vanliga problem som uppstår.