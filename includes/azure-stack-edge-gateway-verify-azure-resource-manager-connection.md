---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305573"
---
Kontrol lera att följande steg kan användas för att komma åt enheten från klienten.

Kontrol lera att klienten kan ansluta till den lokala Azure Resource Manager. 

1. Anropa lokala enhets-API: er för att autentisera:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Ange användar namn `EdgeArmUser` och lösen ord för att ansluta via Azure Resource Manager. Om du inte återkallar lösen ordet [återställer du lösen ordet för Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) och använder lösen ordet för att logga in.