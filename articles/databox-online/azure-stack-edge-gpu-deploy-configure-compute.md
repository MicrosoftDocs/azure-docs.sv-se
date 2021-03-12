---
title: Självstudie för att filtrera, analysera data med Compute på Azure Stack Edge Pro GPU | Microsoft Docs
description: Lär dig hur du konfigurerar Compute-rollen på Azure Stack Edge Pro GPU och använder den för att transformera data innan du skickar dem till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 750b7a8367d46434f48626268a0eb37c9edddfb1
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633549"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Självstudie: Konfigurera Compute på Azure Stack Edge Pro GPU-enhet

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

I den här självstudien beskrivs hur du konfigurerar en beräknings roll och skapar ett Kubernetes-kluster på din Azure Stack Edge Pro-enhet. 

Den här proceduren kan ta cirka 20 till 30 minuter att slutföra.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Hämta Kubernetes-slutpunkter

 
## <a name="prerequisites"></a>Förutsättningar

Innan du ställer in en beräknings roll på din Azure Stack Edge Pro-enhet ser du till att:

- Du har aktiverat din Azure Stack Edge Pro-enhet enligt beskrivningen i [aktivera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Kontrol lera att du har följt instruktionerna i [Aktivera beräknings nätverk](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) och:
    - Aktiverat ett nätverks gränssnitt för beräkning.
    - Tilldelade Kubernetes Node IP-adresser och Kubernetes external service IP-adresser.

## <a name="configure-compute"></a>Konfigurera beräkning

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Hämta Kubernetes-slutpunkter

Om du vill konfigurera en klient för åtkomst till Kubernetes-kluster behöver du Kubernetes-slutpunkten. Följ de här stegen för att hämta Kubernetes API-slutpunkt från det lokala gränssnittet för din Azure Stack Edge Pro-enhet.

1. I det lokala webb gränssnittet på enheten går du till sidan **enheter** .
2. Under **enhetens slut punkter** kopierar du **Kubernetes API-tjänstens** slut punkt. Den här slut punkten är en sträng i följande format: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Enhets sida i lokalt användar gränssnitt](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Spara slut punkts strängen. Du kommer att använda slut punkt strängen senare när du konfigurerar en klient för åtkomst till Kubernetes-klustret via kubectl.

4. När du är i det lokala webb gränssnittet kan du:

    - Gå till Kubernetes-API, Välj **Avancerade inställningar** och ladda ned en avancerad konfigurations fil för Kubernetes. 

        ![Enhets sida i lokalt användar gränssnitt 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Om du har fått en nyckel från Microsoft (Välj användare kan ha en nyckel) kan du använda den här konfigurations filen.

        ![Enhets sida i lokalt användar gränssnitt 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Du kan också gå till **Kubernetes Dashboard** -slutpunkt och ladda ned en `aseuser` konfigurations fil. 
    
        ![Enhets sida i lokalt användar gränssnitt 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Du kan använda den här konfigurations filen för att logga in på Kubernetes-instrumentpanelen eller felsöka eventuella problem i ditt Kubernetes-kluster. Mer information finns i [Access Kubernetes Dashboard](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Hämta Kubernetes-slutpunkter


Information om hur du administrerar din Azure Stack Edge Pro-enhet finns i:

> [!div class="nextstepaction"]
> [Använd lokalt webb gränssnitt för att administrera Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
