---
title: Hantera beräknings nätverk på Azure Stack Edge Pro för att komma åt moduler | Microsoft Docs
description: Beskriver hur du utökar beräknings nätverket på Azure Stack Edge Pro för att komma åt moduler via en extern IP-adress.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90894113"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Aktivera beräknings nätverk på Azure Stack Edge Pro

Den här artikeln beskriver hur modulerna som körs på Azure Stack Edge Pro kan komma åt beräknings nätverket som är aktiverat på enheten.

Utför följande steg för att konfigurera nätverket:

- Aktivera ett nätverks gränssnitt på din Azure Stack Edge Pro-enhet för data bearbetning
- Lägg till en modul för att få åtkomst till Compute Network på Azure Stack Edge Pro
- Kontrol lera att modulen har åtkomst till det aktiverade nätverks gränssnittet

I den här självstudien använder du en modul för webserver-app för att demonstrera scenariot.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du:

- En Azure Stack Edge Pro-enhet med enhets konfigurationen slutförd.
- Du har slutfört **Konfigurera beräknings** steget enligt [självstudien: transformera data med Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) på enheten. Enheten bör ha en tillhör ande IoT Hub resurs, en IoT-enhet och en IoT Edge enhet.

## <a name="enable-network-interface-for-compute"></a>Aktivera nätverks gränssnitt för beräkning

Om du vill komma åt modulerna som körs på enheten via ett externt nätverk måste du tilldela en IP-adress till ett nätverks gränssnitt på enheten. Du kan hantera dessa beräknings inställningar från det lokala webb gränssnittet.

Utför följande steg i det lokala webb gränssnittet för att konfigurera beräknings inställningar.

1. I det lokala webb gränssnittet går du till **konfiguration > beräknings inställningar**.  

2. **Aktivera** det nätverks gränssnitt som du vill använda för att ansluta till en Compute-modul som du kör på enheten.

    - Om du använder statiska IP-adresser anger du en IP-adress för nätverks gränssnittet.
    - Om du använder DHCP tilldelas IP-adresserna automatiskt. I det här exemplet används DHCP.

    ![Aktivera beräknings inställningar 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Tillämpa inställningarna genom att välja **Verkställ** . Anteckna IP-adressen som tilldelats nätverks gränssnittet om du använder DHCP.

    ![Aktivera beräknings inställningar](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Lägg till modul för webserver-app

Utför följande steg för att lägga till en modul för webserver-app på din Azure Stack Edge Pro-enhet.

1. Gå till den IoT Hub resurs som är kopplad till din Azure Stack Edge Pro-enhet och välj sedan **IoT Edge enhet**.
2. Välj den IoT Edge enhet som är kopplad till din Azure Stack Edge Pro-enhet. Välj **Ange moduler** på **enhets informationen**. I **Lägg till moduler** väljer du **+ Lägg till** och väljer sedan **IoT Edge modul**.
3. På bladet **IoT Edge anpassade moduler** :

    1. Ange ett **namn** för den webserver-app som du vill distribuera.
    2. Ange en **bild-URI** för din modul-avbildning. En modul som matchar det tillhandahållna namnet och taggarna hämtas. I det här fallet `nginx:stable` hämtar en stabil nginx-avbildning (Taggad som stabil) från den [offentliga Docker-lagringsplatsen](https://hub.docker.com/_/nginx/).
    3. I **behållaren skapa alternativ** klistrar du in följande exempel kod:  

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

        Med den här konfigurationen kan du komma åt modulen med Compute Network IP över *http* på TCP-port 8080 (med standard porten för webserver som 80).

        ![Ange portinformation i bladet för anpassade moduler för IoT Edge](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Välj **Spara**.

## <a name="verify-module-access"></a>Verifiera åtkomst till modulen

1. Kontrol lera att modulen har distribuerats och att den körs. Körnings status för modulen ska **köras** på fliken **moduler** på sidan med **enhets information** .  
2. Anslut till app-modulen för webb server. Öppna ett webbläsarfönster och skriv:

    `http://<compute-network-IP-address>:8080`

    Du bör se att webserver-appen körs.

    ![Verifiera anslutning till modul över angiven port](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](azure-stack-edge-manage-users.md).
