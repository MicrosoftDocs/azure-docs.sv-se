---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076723"
---
Nästa steg är att ladda ned paket för Azure spatiala ankare för enhet. 

> [!WARNING]
> ASA SDK-2.7.0 är den lägsta version som stöds. Om du använder Unity 2020 är ASA SDK 2.9.0 den lägsta version som stöds.

Om du vill använda Azures spatialdata i enhet måste du ladda ned kärn paketet och ett plattformsspecifik paket för varje plattform (Android/iOS/HoloLens) som du planerar att stödja.

| Plattform | Paketnamn                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com. Microsoft. Azure. spatial-ankare – sdk.core@ <version_number> |
| Android  | com. Microsoft. Azure. spatial-ankare – sdk.android@ <version_number> |
| iOS      | com. Microsoft. Azure. spatial-ankare – sdk.ios@ <version_number>     |
| HoloLens | com. Microsoft. Azure. spatial-ankare – sdk.windows@ <version_number> |

# <a name="download-with-web-browser"></a>[Ladda ned med webbläsare](#tab/unity-package-web-ui)

Leta upp kärn paketet för Azure spatial-ankare [här](https://aka.ms/aoa/unity-sdk/package). Välj den version du vill använda och Hämta paketet med hjälp av knappen **Hämta** . Upprepa det här steget för att ladda ned paketet för varje plattform som du planerar att stödja.

# <a name="download-with-npm"></a>[Ladda ned med NPM](#tab/unity-package-npm)

Det här steget kräver att <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> är installerat och tillgängligt.

Kör följande kommando för att ersätta `<version_number>` med den version av Azure spatial-ankare som du vill ladda ned till den aktuella mappen:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Om du vill visa en lista över tillgängliga versioner av paketet för Azure spatial-ankare kör du följande:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Kärn paketet för Azure spatial-ankare kommer att laddas ned till den mapp där du körde kommandot. Upprepa det här steget för att ladda ned paketet för varje plattform som du planerar att stödja.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Installera med Mixed Reality-funktions verktyg (beta)](#tab/unity-package-mixed-reality-feature-tool)

Fortsätt till nästa steg. Du använder <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">funktions verktyget för Mixad verklighet</a> i ett senare steg.

---