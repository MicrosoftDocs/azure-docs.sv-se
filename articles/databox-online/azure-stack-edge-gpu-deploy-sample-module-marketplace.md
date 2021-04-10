---
title: Distribuera GPU-modul på din Microsoft Azure Stack Edge Pro-enhet från Azure Marketplace | Microsoft Docs
description: Beskriver hur du distribuerar GPU-aktiverade IoT-moduler på din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e22014380d568b12e1e3bec751a75180d0760ab7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568443"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Distribuera en GPU-aktiverad IoT-modul från Azure Marketplace på Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Den här artikeln beskriver hur du distribuerar en GPU (Graphics Processing Unit) som är aktive rad IoT Edge-modul från Azure Marketplace på din Azure Stack Edge Pro-enhet. 

I den här artikeln kan du se hur du:
  - Förbered Azure Stack Edge Pro för att köra en GPU-modul.
  - Ladda ned och distribuera GPU-aktiverad IoT-modul från Azure Marketplace.
  - Övervaka utdata från modulen.

## <a name="about-sample-module"></a>Om exempel modul

GPU-exemplet i den här artikeln innehåller PyTorch-och TensorFlow-kod för att testa processor mot GPU.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att du har:

- Du har åtkomst till en GPU-aktiverad Azure Stack Edge-enhet med en nod. Enheten aktiveras med en resurs i Azure. 
- Du har konfigurerat Compute på den här enheten. Följ stegen i [Självstudier: Konfigurera Compute på Azure Stack Edge-enheten](azure-stack-edge-gpu-deploy-configure-compute.md).
- Följande utvecklings resurser på en Windows-klient:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).   


## <a name="get-module-from-azure-marketplace"></a>Hämta modul från Azure Marketplace

1. Bläddra bland alla [appar på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Bläddra bland appar på Azure Marketplace](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Sök efter **komma igång med GPU: er**.

    ![Sök i GPU-exempel modul](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Välj **Hämta nu**.

    ![Hämta exempel modul](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Välj **Fortsätt** för att godkänna providerns användnings villkor och sekretess policy. 

    ![Hämta exempel modul 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Välj den prenumeration som du använde för att distribuera din Azure Stack Edge Pro-enhet.

    ![Välj en prenumeration](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Ange namnet på den IoT Hub tjänst som du skapade när du konfigurerade din Azure Stack Edge Pro-enhet. Om du vill hitta IoT Hub tjänst namnet går du till Azure Stack Edge-resurs som är kopplad till enheten i Azure Portal. 

    1. I det vänstra fönstrets meny alternativ går du till **Edge services > IoT Edge**. 

        ![Visa beräknings konfiguration](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Gå till **Egenskaper**. 

        1. Anteckna IoT Hub tjänsten som skapades när du konfigurerade Compute på din Azure Stack Edge Pro-enhet.
        2. Notera namnet på den IoT Edge enhet som skapades när du konfigurerade Compute. Du kommer att använda det här namnet i följande steg.

        ![Konfiguration av Edge-beräkning](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Välj **distribuera till en enhet**.

11. Ange namnet på den IoT Edge enheten eller Välj **hitta enhet** för att bläddra bland de enheter som är registrerade i hubben.

    ![Hitta enhet](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Välj **skapa** för att fortsätta standard processen med att konfigurera ett distributions manifest, inklusive lägga till andra moduler om du vill. Information om den nya modulen, till exempel bild-URI, skapa alternativ och önskade egenskaper är fördefinierade, men kan ändras.

    ![Välj Skapa](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Kontrol lera att modulen har distribuerats i IoT Hub i Azure Portal. Välj din enhet, Välj **Ange moduler** och modulen ska visas i avsnittet **IoT Edge moduler** .

    ![Välj Skapa 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Övervaka modulen  

1. I kommandopaletten i VS Code kör du **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Välj IoT Hub).

2. Välj den prenumeration och IoT-hubb som innehåller den IoT Edge-enhet som du vill konfigurera. I det här fallet väljer du den prenumeration som används för att distribuera Azure Stack Edge Pro-enheten och väljer IoT Edge enheten som skapats för din Azure Stack Edge Pro-enhet. Detta inträffar när du konfigurerar Compute via Azure Portal i föregående steg.

3. I VS Code-Utforskaren expanderar du avsnittet Azure IoT Hub. Under **enheter** bör du se IoT Edge enhet som motsvarar din Azure Stack Edge Pro-enhet. 

    1. Välj den enheten, högerklicka och välj **starta övervakning inbyggd händelse slut punkt**.
  
        ![Starta övervakning](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Gå till **enheter > moduler** och se till att din **GPU-modul** körs.

    3. I VS Code-terminalen bör även IoT Hub händelser visas som övervaknings resultatet för din Azure Stack Edge Pro-enhet.

        ![Övervaka utdata](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Du kan se att den tid det tar att köra samma uppsättning åtgärder (5000 iterationer av form Transformation) av GPU är mycket mindre än för CPU.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [konfigurerar GPU för att använda en modul](./azure-stack-edge-gpu-configure-gpu-modules.md).