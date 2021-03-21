---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78262228"
---
### <a name="set-the-storage-account-connection"></a>Ange lagrings konto anslutningen

Öppna filen local.settings.jsoch kopiera värdet för `AzureWebJobsStorage` , vilket är anslutnings strängen för lagrings kontot. Ställ in `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln på anslutnings strängen genom att använda det här bash-kommandot:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

När du anger anslutnings strängen i `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln, kan du komma åt ditt lagrings konto utan att behöva tillhandahålla autentisering varje gång.