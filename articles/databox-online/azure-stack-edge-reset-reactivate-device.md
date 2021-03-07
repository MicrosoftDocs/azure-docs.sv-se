---
title: Återställa och återaktivera din Azure Stack Edge Pro-enhet | Microsoft Docs
description: Lär dig hur du rensar data från och sedan återaktiverar din Azure Stack Edge Pro-enhet.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2020
ms.author: alkohli
ms.openlocfilehash: 95fbe5dc4a934fee10c558f640bc24f261203a33
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102443557"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Återställa och återaktivera din Azure Stack Edge Pro-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du återställer, konfigurerar om och återaktiverar en Azure Stack Edge Pro-enhet om du har problem med enheten eller behöver starta om färskt av någon annan anledning.

När du har återställt enheten för att ta bort data måste du återaktivera enheten som en ny resurs. Om du återställer en enhet tas enhets konfigurationen bort, så du måste konfigurera om enheten via det lokala webb gränssnittet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Rensa data från data diskarna på enheten
> * Återaktivera enheten genom att skapa en ny order, konfigurera om enheten och aktivera den

## <a name="reset-data-from-the-device"></a>Återställa data från enheten

Om du vill rensa bort data från enhetens data diskar måste du återställa enheten.

Innan du återställer skapar du en kopia av lokala data på enheten om det behövs. Du kan kopiera data från enheten till en Azure Storage behållare.

>[!IMPORTANT]
> Om du återställer enheten raderas alla lokala data och arbets belastningar från enheten och det går inte att ångra dem. Återställ endast enheten om du vill starta afresh med enheten.

Du kan återställa enheten i det lokala webb gränssnittet eller i PowerShell. PowerShell-instruktioner finns i [återställa din enhet](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>Återaktivera enhet

När du har återställt enheten måste du återaktivera enheten som en ny resurs. När du har placerat en ny order måste du konfigurera om och sedan återaktivera den nya resursen.

Följ dessa steg om du vill återaktivera din befintliga enhet:

1. Skapa en ny order för den befintliga enheten genom att följa stegen i [skapa en ny resurs](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource). På fliken **leverans adress** väljer **du jag har redan en enhet**.

   ![Ange ingen ny enhet i leverans adressen](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Hämta aktiverings nyckeln](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key).

1. [Anslut till enheten](azure-stack-edge-gpu-deploy-connect.md).

1. [Konfigurera nätverket för enheten](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Konfigurera enhets inställningar](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Konfigurera certifikat](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Aktivera enheten](azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [ansluter till en Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-connect.md).
