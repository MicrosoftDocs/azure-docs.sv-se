---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104719974"
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

> [!WARNING]
> ASA SDK-2.7.0 är den lägsta version som stöds. Om du använder Unity 2020 är ASA SDK 2.9.0 den lägsta version som stöds.

Kärn paketet för Azure spatial-ankare kommer att laddas ned till den mapp där du körde kommandot.

Upprepa det här steget för att ladda ned paketet för varje plattform (Android/iOS/HoloLens) som du vill ha stöd för i projektet.

| Plattform | Paketnamn                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. spatial-ankare – sdk.android@ <version_number> |
| iOS      | com. Microsoft. Azure. spatial-ankare – sdk.ios@ <version_number>     |
| HoloLens | com. Microsoft. Azure. spatial-ankare – sdk.windows@ <version_number> |