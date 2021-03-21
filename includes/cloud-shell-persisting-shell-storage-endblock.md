---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187680"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Överför lokala filer till Cloud Shell
`clouddrive`Katalogen synkroniseras med bladet Azure Portal lagring. Använd det här bladet för att överföra lokala filer till eller från din fil resurs. Uppdatering av filer från Cloud Shell visas i användar gränssnittet för fil lagring när du uppdaterar bladet.

### <a name="download-files"></a>Ladda ned filer

![Lista över lokala filer](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. I Azure Portal går du till den monterade fil resursen.
2. Välj mål filen.
3. Välj knappen **Ladda ned** .

### <a name="upload-files"></a>Ladda upp filer

![Lokala filer som ska överföras](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Gå till den monterade fil resursen.
2. Välj knappen **Ladda upp**.
3. Markera filen eller filerna som du vill överföra.
4. Bekräfta uppladdningen.

Nu bör du se de filer som är tillgängliga i din `clouddrive` katalog i Cloud Shell.