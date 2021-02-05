---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569526"
---
Du måste ha <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> installerat för att kunna hämta de nödvändiga paketen.

Kör följande kommando för att ersätta `<version_number>` med den version av Azure spatial-ankare som du vill ladda ned till den aktuella mappen:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Om du vill visa en lista över tillgängliga versioner av paketet för Azure spatial-ankare kör du följande:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

Kärn paketet för Azure spatial-ankare kommer att laddas ned till den mapp där du körde kommandot.

Upprepa det här steget för att ladda ned paketet för varje plattform (Android/iOS/HoloLens) som du vill ha stöd för i projektet.

| Plattform | Paketnamn                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. spatial-ankare – sdk.android@ <version_number> |
| iOS      | com. Microsoft. Azure. spatial-ankare – sdk.ios@ <version_number>     |
| HoloLens | com. Microsoft. Azure. spatial-ankare – sdk.windows@ <version_number> |