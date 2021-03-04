---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: 4345810292896cf88de19baf419eee025ba5853f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044787"
---
Nästa steg är att ladda ned Azure Object ankare-paketet för Unity.

# <a name="download-with-web-browser"></a>[Ladda ned med webbläsare](#tab/unity-package-web-ui)

Leta upp paketet Azure Object ankare för Unity [här](https://aka.ms/aoa/unity-sdk/package). Välj den version du vill använda och Hämta paketet med hjälp av knappen **Hämta** .

# <a name="download-with-npm"></a>[Ladda ned med NPM](#tab/unity-package-npm)

Det här steget kräver att <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> är installerat och tillgängligt.

Kör följande kommando för att ersätta `<version_number>` med den version av Azure Object-ankare som du vill hämta:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Om du vill visa en lista över tillgängliga versioner av Azure Object ankare-paketet kör du följande:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Paketet Azure Object ankare kommer att laddas ned till den mapp där du körde kommandot.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Installera med Mixed Reality-funktions verktyg (beta)](#tab/unity-package-mixed-reality-feature-tool)

Fortsätt till nästa steg. Du använder <a a href="https://aka.ms/MRFeatureToolDocs" target="_blank">funktions verktyget för Mixad verklighet</a> i ett senare steg.

---
