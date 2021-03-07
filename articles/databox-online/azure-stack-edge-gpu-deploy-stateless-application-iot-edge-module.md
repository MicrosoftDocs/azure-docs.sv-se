---
title: Distribuera Kubernetes-tillstånds lös app på Azure Stack Edge Pro GPU via modulen IoT Edge | Microsoft Docs
description: Beskriver hur du distribuerar ett Kubernetes-tillstånds löst program på din Azure Stack Edge Pro GPU-enhet med hjälp av en IoT Edge modul som nås via en extern IP-adress.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4eda6eaf69fad6f8600651660ae4ac6223fe8f8c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438071"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>Använd IoT Edge modul för att köra ett Kubernetes tillstånds löst program på din Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs hur du kan använda en IoT Edge-modul för att distribuera ett tillstånds lösa program på din Azure Stack Edge Pro-enhet.

Om du vill distribuera det tillstånds lösa programmet utför du följande steg:

- Se till att kraven har slutförts innan du distribuerar en IoT Edge-modul.
- Lägg till en IoT Edge-modul för att få åtkomst till Compute Network på din Azure Stack Edge Pro.
- Kontrol lera att modulen har åtkomst till det aktiverade nätverks gränssnittet.

I den här instruktions artikeln använder du en modul för webserver-app för att demonstrera scenariot.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du:

- En Azure Stack Edge Pro-enhet. Se till att:

    - Inställningarna för beräknings nätverket konfigureras på enheten.
    - Enheten aktive ras enligt stegen i [Självstudier: Aktivera enheten](azure-stack-edge-gpu-deploy-activate.md).
- Du har slutfört **Konfigurera beräknings** steget enligt [självstudien: Konfigurera compute på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-configure-compute.md) på enheten. Enheten bör ha en tillhör ande IoT Hub resurs, en IoT-enhet och en IoT Edge enhet.


## <a name="add-webserver-app-module"></a>Lägg till modul för webserver-app

Utför följande steg för att lägga till en modul för webserver-app på din Azure Stack Edge Pro-enhet.

1. I den IoT Hub resurs som är kopplad till din enhet går du till **Automatisk enhets hantering > IoT Edge**.
1. Markera och klicka på den IoT Edge enhet som är kopplad till din Azure Stack Edge Pro-enhet. 

    ![Välj IoT Edge enhet](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Välj **Ange moduler**. I **Ange moduler på enhet** väljer du **+ Lägg till** och väljer sedan **IoT Edge modul**.

    ![Välj IoT Edge modul](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. I **modulen Lägg till IoT Edge**:

    1. Ange ett **namn** för den webserver-app som du vill distribuera.
    2. Under fliken **Inställningar för modul** anger du en bild- **URI** för din modul-avbildning. En modul som matchar det tillhandahållna namnet och taggarna hämtas. I det här fallet `nginx:stable` hämtar en stabil nginx-avbildning (Taggad som stabil) från den [offentliga Docker-lagringsplatsen](https://hub.docker.com/_/nginx/).

        ![Lägg till IoT Edge modul](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. Klistra in följande exempel kod på fliken **behållare skapa alternativ** :  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Med den här konfigurationen kan du komma åt modulen med Compute Network IP över *http* på TCP-port 8080 (med standard porten för webserver som 80). Välj **Lägg till**.

        ![Ange portinformation i bladet för anpassade moduler för IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Välj **Granska + skapa**. Granska informationen i modulen och välj **skapa**.

## <a name="verify-module-access"></a>Verifiera åtkomst till modulen

1. Kontrol lera att modulen har distribuerats och att den körs. Körnings status för modulen ska **köras** på fliken **moduler** .  

    ![Verifiera att modulens status körs](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Om du vill hämta den externa slut punkten för webserver-appen [öppnar du Kubernetes-instrumentpanelen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. Filtrera efter **iotedge** -namnområde i det vänstra fönstret på instrument panelen. Gå till **identifiering och belastnings utjämning > tjänster**. Leta upp den externa slut punkten för webserver app-modulen under listan över tjänster i listan. 

    ![Ansluta till webserver-appen vid extern slut punkt](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Välj den externa slut punkten för att öppna ett nytt webbläsarfönster.

    Du bör se att webserver-appen körs.

    ![Verifiera anslutning till modul över angiven port](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du exponerar tillstånds känslig program via en IoT Edge-modul<!--insert link-->.
