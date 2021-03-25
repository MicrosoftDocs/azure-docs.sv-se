---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: ada83d6263ef033208200d810c53c5f045acc9a7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105103994"
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

Fortsätt till nästa steg. Du använder <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">funktions verktyget för Mixad verklighet</a> i ett senare steg.

---