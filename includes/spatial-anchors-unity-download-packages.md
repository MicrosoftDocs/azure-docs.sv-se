---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550412"
---
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