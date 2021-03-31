---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102517586"
---
1. Innan du börjar ska du kontrollera att:

    1. Du har konfigurerat och [aktiverat din Azure Stack Edge Pro-enhet](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) med en Azure Stack Edge-resurs i Azure.
    1. Du har [konfigurerat Compute på den här enheten i Azure Portal](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Anslut till PowerShell-gränssnittet](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Använd följande kommando för att aktivera MPS på enheten.

    ```powershell
    Start-HcsGpuMPS
    ```