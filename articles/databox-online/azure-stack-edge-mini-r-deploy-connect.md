---
title: Självstudie för att ansluta till Azure Stack Edge Mini R i Azure Portal
description: Lär dig hur du kan ansluta till din Azure Stack Edge-Mini R-enhet med hjälp av det lokala webb gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 57d11e1c4109caded4287592a6d71a803e44b68e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061612"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Självstudie: Anslut till Azure Stack Edge Mini R

I den här självstudien beskrivs hur du kan ansluta till din Azure Stack Edge-Mini R-enhet med hjälp av det lokala webb gränssnittet.

Den här anslutningsprocessen kan ta omkring 5 minuter att slutföra.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Ansluta till en fysisk enhet



## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar Azure Stack Edge-enheten måste du kontrol lera att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Anslut till konfiguration av lokalt webb gränssnitt

1. Konfigurera Ethernet-kortet på datorn för att ansluta till den Azure Stack Edge Pro-enheten med en statisk IP-adress för 192.168.100.5 och undernät 255.255.255.0.

2. Anslut datorn till PORT 1 på din enhet. Om du ansluter datorn till enheten direkt (utan någon switch) använder du en korskopplad kabel eller ett USB Ethernet-nätverkskort. Använd följande bild för att identifiera PORT 1 på enheten.

    ![Kablar för Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Ansluta till en fysisk enhet


Information om hur du konfigurerar nätverks inställningar på enheten finns i:

> [!div class="nextstepaction"]
> [Konfigurera nätverk](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
